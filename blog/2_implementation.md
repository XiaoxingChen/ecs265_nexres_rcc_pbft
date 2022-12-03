## Part 2. Implementation

In this section, we will give an overview of implementation. To implement RCC with concurrent instances in Nexres, four modules are required: *Client, Primary, Backup Replica, Total Ordering*. All modules consist of normal-case algorithms and recovery algorithms. The implementation of normal-case algorithms is of higher priority while the implementation of recovery algorithms is optiopnal. Also, the evaluation that helps us assess the performance of RCC is necessary. 

#### Configuration

To implement RCC in NexRes, first we need to make changes in the configutation file. When starting service in NexRes, it will read a configuration file to learn the number of replicas and the original primary. Thus, we need to specify the number of instances and the primaries in the configuration file.

#### Server and Client

Using PBFT, servers in NexRes inform clients of the current primary key by sending *HeartBeat* messages to clients. And clients need to send requests to all primaries evenly rather than a single primary as it does in PBFT.

#### Message

##### Instance

Running concurrent instances, replicas in RCC should be able to indentify the instance of each message. Thus, for proposal messages, we need to use a field called *instance* to identify the instance of a proposal. With such a field, replicas can map messages to correct corresponding instances.

##### Primary

When boardcasting proposals, a primary need to indicate its instance in the proposals.

##### Backup Replica

When receiving a proposal of instance *i*, check if it is exactly from the primary of instance *i*.

#### Total Ordering

Before executing committed client requests, RCC orders the committed requests between different instances in the same round. So we need to implement a mechanism to determine the execution order of committed transactions in one round. The simplest one would be ordering based on instance id, from the lowest instance to the highest one.

