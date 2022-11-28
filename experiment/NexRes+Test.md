# RCC in NexRes Test

### 第一步： 开机器

开主机：https://aws.amazon.com，**Sign In to Console**

AccountID: 354775971805

用户名和密码请私聊问我

**Start dakai-dev这个机器，注意不是dakai_dev**

用VSCode SSH Remote。 ssh -i xxx.pem ubuntu@3.229.154.5，打开resilientdb目录

pem文件请私聊问我



实验机器用多少，开多少，4个replica的实验开5台（其中一台是client，从dakai-nexres-1到dakai-nexres-5），16个replica的实验就开17台，以此类推，**用完记得stop。** **用完记得stop。** **用完记得stop。**



### 第二步：跑一次

以4个replica为例：

开4个terminal

第一个在

```
~/resilientdb/oracle_script/pbft/rep_4
# 注意在做不同replica数量的实验时，要改rep后面的值
```

第二个在

```
~/resilientdb/
```

第三个在

```
ssh -i ~/.ssh/dakai_dev.pem ubuntu@172.31.77.218
# 172.31.77.218是dakai-nexres-01的private IP
tail -f server1.log
# 这个实时显示吞吐量，txn后面的数字就是，即一个大概的稳定后的均值就可以了
dstat -n
# 这个实时显示带宽使用情况，记一下send，即一个大概的稳定后的均值就可以了
```

第四个在

```
ssh -i ~/.ssh/dakai_dev.pem ubuntu@172.31.77.103
# 172.31.77.103是dakai-nexres-05（即Client）的private IP
top
# 查看Memory使用情况
```



在第一个Terminal

```
sh ./runsvr.sh
# 生成二进制文件
./killall.sh
# 将文件部署到各个replica
```

第二个

```
./bazel-bin/example/kv_server_tools oracle_script/pbft/rep_4/client.config get key
# 注意是在rep_4目录下，因为我们是4个replica的实验
# 这个命令激活二进制文件，开始运行
```

第三个在

```
分别记录txn和send带宽使用情况，要跑两次，记完了就在第一个killall
```

第四个在

```
top
# 不时看一眼Client的memory usage，别到100%爆掉，那就得等好一会，有点烦
```



### 第三步：实验一，Scalability

测试并记录**PBFT，RCC**在不同replica数量下的吞吐量和输出带宽使用情况

分别**去不同的rep目录**下面做，replica数量取值有:4, 16, 32, 64, 96，（Terminal1，2，3皆有相应改动），batchsize设为100或400（，具体设置方法见实验二）

/home/ubuntu/resilientdb/oracle_script/pbft/script/run_svr.py#L36，设成1就是PBFT，设成x就是x个instance的RCC，**在实验一中我们用replica的数量作为x的取值**

如果RCC在64或96时做不出来，尝试把/home/ubuntu/resilientdb/config/resdb_config.h#L112的值改大，再多跑几遍试试，应该总有那么几遍能成功（实在做不出来我后面做）

BatchSize：100

| Replica                 | 4    | 16   | 32   | 64   | 96   |
| ----------------------- | ---- | ---- | ---- | ---- | ---- |
| RCC Throughput          |605740|538840|405280|147120|83790 |
| RCC Outgoing Bandwidth  |  17  |30   |  40  |  22  |  19  |
| PBFT Throughput         |630220|531000|476160|146220|79700 |
| PBFT Outgoing Bandwidth | 55   |232   |  383 |  312 |  131 |

BatchSize：400

| Replica                 | 4    | 16   | 32   | 64   | 96   |
| ----------------------- | ---- | ---- | ---- | ---- | ---- |
| RCC Throughput          |630080|549453|510453|383560|221680|
| RCC Outgoing Bandwidth  |  14  |19    |  23  |  25  |  23  |
| PBFT Throughput         |651360|588160|542800|247600|239440|
| PBFT Outgoing Bandwidth | 53   |237   |  445 | 596  | 593  |

### 第四步：实验二，BatchSize

测试并记录**PBFT，RCC**在64或96个replica的情况下，采用不同的batch_size时下的吞吐量和输出带宽使用情况

分别**去不同的rep目录**下面做，replica数量取值有:64, 96，（Terminal1，2，3皆有相应改动）

/home/ubuntu/resilientdb/config/resdb_config.h#L114，调整batchsize大小

要是RCC跑不成功，详见实验一中调整方法
| BatchSize                  | 10    | 50    | 100    | 200    | 400    | 800    |
| -------------------------- | ----- | ----- | ------ | ------ | ------ | ------ |
| PBFT 64 Throughput         | 15400 | 33250 | 146520 | 26330  | 372960 | 358720 |
| PBFT 64 Outgoing Bandwidth | 52    | 60    | 252    | 593    | 596    | 596    |
| PBFT 96 Throughput         | 6466  | 31260 | 72540  | 117200 | 224800 | 198560 |
| PBFT 96 Outgoing Bandwidth | 44    | 144   | 241    | 410    | 596    | 596    |
| RCC 64 Throughput          | 14721 | 73218 | 146177 | 261113 | 397324 | 412352 |
| RCC 64 Outgoing Bandwidth  | 20    | 21    | 22     | 26     | 27     | 20     |
| RCC 96 Throughput          | 8834  | 22420 | 46080  | 128320 | 336320 | 374800 |
| RCC 96 Outgoing Bandwidth  | 14    | 15    | 15     | 17     | 21     | 23     |

### 第五步：实验三，Concurrency

测试并记录**RCC**在64或96个replica的情况下，采用不同的instance number时下的吞吐量和输出带宽使用情况

分别**去不同的rep目录**下面做，replica数量取值有:64, 96，（Terminal1，2，3皆有相应改动），batchsize设为100或400（，具体设置方法见第四步）

/home/ubuntu/resilientdb/oracle_script/pbft/script/run_svr.py#L36，调整instance数量

要是RCC跑不成功，详见实验一中调整方法

BatchSize:100

| Instance Number       | 1      | 4      | 16     | 32     | 64     |        |
| --------------------- | ------ | ------ | ------ | ------ | ------ | ------ |
| 64 Throughput         | 140436 | 147410 | 154763 | 144778 | 150105 |        |
| 64 Outgoing Bandwidth | 244    | 81     | 35     | 26     | 24     |        |
| **Instance Number**   | **1**  | **4**  | **16** | **32** | **64** | **96** |
| 96 Throughput         | 34560  | 101180 | 75620  | 67040  | 56920  | 48820  |
| 96 Outgoing Bandwidth | 70     | 48     | 25     | 17     | 15     | 20     |

BatchSize:400

| Instance Number       | 1      | 4      | 16     | 32     | 64     |        |
| --------------------- | ------ | ------ | ------ | ------ | ------ | ------ |
| 64 Throughput         | 30680  | 431120 | 422400 | 431417 | 368793 |        |
| 64 Outgoing Bandwidth | 596    | 188    | 58     | 36     | 25     |        |
| **Instance Number**   | **1**  | **4**  | **16** | **32** | **64** | **96** |
| 96 Throughput         | 229040 | 142000 | 257600 | 85520  | 94960  | 91280  |
| 96 Outgoing Bandwidth | 596    | 176    | 48     | 55     | 27     | 23     |

