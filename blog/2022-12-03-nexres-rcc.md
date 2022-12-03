# Experiment and Setup

In this chapter, we will use three experiments to benchmark the performance of RCC and PBFT.

The experiments are done in AWS EC2. The hardware configuration used is shown as follow:

- t3.2xlarge
- 8-vCPUs
- 32-GiB Memory

The experiments use up to 96 replicas and 4 clients.

## Experiment 1 - Scalability

The first experiment is designed to compare the best-case throughput
and outgoing bandwidth of RCC and PBFT of different replicas.

During the test, the throughput and outgoing bandwidth of RCC and PBFT with different number of replicas
are recorded. 
The replica number is selected in 4, 16, 32, 64 and 96. 
The batch size is set to be 400 and 800. 

![ecs265_exp1_fig1](https://user-images.githubusercontent.com/16934019/205419564-5408ed29-37f5-46fb-a19d-78573e439de1.png)
![ecs265_exp1_fig2](https://user-images.githubusercontent.com/16934019/205419565-e7a982b0-e317-4b30-b0ce-600b8ff5bfbe.png)

The figures above show the results of the experiments.

As the system scales, throughput decreases.
And RCC 800 outperforms all others when there are 96 replicas. 
From the right figure we can see that PBFT-400 and PBFT-800 are bottlenecked by outgoing bandwidth. 
So why does RCC-400 has lower performance? 
Compared to RCC-800, RCC-400 processes double number of consensus messages. 
And we found that RCC-400 was bottlenecked by computing capability by monitoring its CPU utilization while running.
