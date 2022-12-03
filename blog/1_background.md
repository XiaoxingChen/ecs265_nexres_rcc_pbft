# Background

This is a background of implementing *RCC* with concurrent *PBFT* instances in NexRes. NexRes is a global-scale sustainable blockchain fabric developed by the developers at the Exploratory Systems Lab at UC Davis. We are a group of graduate students on a mission led by Prof. Mohammad Sadoghi to pioneer implementing RCC in NexRes.

## Practical Byzantine Fault Tolerance Consensus (PBFT)

Consensus protocols enable independent participants to manage a single common database by reliably and continuously replicating a unique sequence of transactions among all participants. It provides more resilience during failures and stronger support for data provenance compared with traditional distributed database systems.Most practical systems use consensus protocols that follow the classical-primary backup design of Practical Byzantine Fault Tolerance Consensus, which is also called *PBFT*[^1].

> **PBFT**
> * Primary broadcasts client requests.
> * Unbalanced overhead of sending messages.
> * Bottleneck: Outgoing bandwidth of Primary

*PBFT* works in asynchronous systems and is optimized to be high-performance. Nevertheless, *PBFT* suffers from the limitation of bandwidth of the primary node since the primary takes much more work compared to backup replicas. In traditional primary-backup protocols like PBFT, a single primary broadcasts client requests. Thus, there is an unbalanced overhead of sending messages between primary and backup replicas. And the bottleneck of the system can be the outgoing bandwidth of the primary.

![img](https://lh4.googleusercontent.com/ftAjZbFNKC2doerf_zaEY4n1DiuLxo7ulk5oIcoK407BJ7FOEalK-vzY4jcL8oCW5bYLgA69ZKaNkOzUdOH7CG8r63qArADW-0wBwSaosdMMa1525kwMtNenxXcEICRYuWEdvTPGO6atWVCaRtaK9cOSXdDfbRhlrJvfB2wU7lstPIzT9IMOJaWoH-3PNVXh)


## Resilient Concurrent Consensus (RCC)

Previous work has proposed the idea of concurrent consensus that promises to achieve higher throughput than primary-backup consensus by effectively utilizing idle resources in other replicas. To relieve the concern above, the Resilient Concurrent Consensus paradigm *RCC* has been presented to turn any primary-backup consensus protocol into concurrent consensus by running multiple instances concurrently. *RCC* is designed with performance in mind and ensures increased resilience against failures[^2]. The design goals of *RCC* are:

> 1. *RCC* provides consensus among replicas on the client transactions to be executed and the order in which they are completed.
> 2. Clients can interact with *RCC* to force the execution of their transactions and learn the outcome of execution.
> 3. *RCC* is a design paradigm that can be applied to any primary-backup consensus protocol, turning it into a concurrent consensus protocol.
> 4. In *RCC*, instances with non-faulty primaries are always able to propose transactions at maximum throughput (concerning the resources available to any replica), this is independent of faulty behavior by any other replica.
> 5. In *RCC*, dealing with faulty primaries does not interfere with the operations of other instances.

Hence, *RCC* is designed for maximizing throughput in all cases, even during malicious activity. Furthermore, *RCC* can make systems more resilient, as it can mitigate the effects of order-based attacks and throttling attacks. As the figure shows, concurrent consensus not only promises greatly improved throughput, but also sharply reduces the costs associated with scaling consensus. By running concurrent instances, *RCC* can distribute the overhead to other replicas and overcome the bottleneck of PBFT.

> **RCC Ascendency**
> * Running concurrent instances
> * Distribute the overhead of broadcasting client requests

![](https://lh4.googleusercontent.com/HoDnmfx_etWbuFOOntjYYWlSsblDb-rDdqzxidyOj2zP11pEHBghDVy5_zyV13u3JkI_RdI2uV6dznuNV5IOgxCxKH9Mik4vN2IpLbPEkxjtikXTJ87rKCHg09tP9D7_ZmWFeCz7oQNHPT86kqkyC97QH-DjuNDqpxIJjUCFqENs_cWTMxyVaEglk6y1v1y1)



## ResilientDB vs NexRes

As a global-scale sustainable blockchain fabri, ResilientDB offers a high-throughput yielding distributed ledger built upon scale-centric design principles to democratize and decentralize computation. And *RCC* has been implemented in the ResilientDB. In ResilientDB, only one thread in assigned to one instance and CPU resources cannot be fully utilized for *PBFT* or *RCC* with a small number instances. So, even if in the ResilientDB, RCC gets better performance than PBFT as theory expectation shows, the real bottleneck is CPU utilization rather than bandwidth.

> **ResilientDB**
> * ResilientDB assigns only one thread to one instance.
> * The actual bottleneck of PBFT in ResDB: CPU utilization.

NexrRes, as the next generation of ResilientDB , which can assign multiple threads to one instance, so CPU utilization is not the bottleneck as in ResilientDB. NexRes can saturate the primary node's network bandwidth in *PBFT*, meets the prerequisites for leveraging *RCC* to optimize its performance and throughput.

> **NexRes**
> Assigns multiple threads to one instance.
> Able to saturate the primary’s network bandwidth in *PBFT*.
> Meets the prerequisite for leveraging *RCC*.


## References
[^1]: **Castro, Miguel, and Barbara Liskov. "*Practical byzantine fault tolerance.*" OsDI. Vol. 99. No. 1999. 1999.** 

[^2]: **Gupta, Suyash, Jelle Hellings, and Mohammad Sadoghi. "*Rcc: Resilient concurrent consensus for high-throughput secure transaction processing.*" 2021 IEEE 37th International Conference on Data Engineering (ICDE). IEEE, 2021.**

