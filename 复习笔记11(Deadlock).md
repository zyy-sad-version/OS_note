### Note for lec 19 and 复习笔记11



#### important point

1.What is deadlock and how occurs?

What： A set of processes is deadlocked if each process in the set is waiting for an event that only the other process in the set can cause

how: Process A and B request the resources in opposite order and end up in deadlock

2.Graph approach to detect single-source resources

对graph进行深度搜索，如果出现了之前数过的节点，则就是一个循环cycle即死锁deadlock

3.Matrix approach to detect multi-source resources

4.Deadlock recovery: preemption, rollback and/or kill





#### Deadlock

Definition: A set of processes is deadlocked if each process in the set is  waiting for an event that only the other process in the set -如果一组进程中的每个进程都在等待一个事件，而该事件只有另一组进程在等待，则该组进程被死锁

* 每一个被死锁的进程都在 等待另一个死锁进程使用的资源（不能运行也不能释放资源）

* 任何数量的进程和任何数量资源之间都有可能发生

  

------

#### 如何发生

On occasions, **multiple processes** will require access to **multiple mutually exclusive resources**

在某些情况下，多个进程需要访问多个互斥的资源

Process A and B request the resources in opposite orders and end up in deadlock

* 死锁可能在任何数量的资源和同一个机器或多个机器之间发生（网络）



##### A resource (e.g. a device, a data record, file, semaphore) can be  acquired获得, used使用, and released释放

* 资源可以是可抢占的**preemptable**，也就是说，它可以被强制从进程中移除，而不会产生永久性的负面影响(例如，内存=写入磁盘，叉子用于用餐哲学家)
* 资源是不可抢占的**non-preemptable**，如果把它从一个过程中带走，就会产生永久性的不利影响

##### 死锁只会在不可抢占资源中发生

死锁只发生在**不可抢占的资源**上，因为可抢占的资源可以被临时拿走以从死锁中恢复

若不可抢占资源为不可用，进程则会被变为等待



------

### 四个死锁发生条件

* **Mutual exclusion**互斥: a resource can be assigned to at most one process  at a time

* **Hold and wait condition**: a resource can be held whilst requesting new  resources
* **No preemption:** resources cannot be forcefully taken away from a  process
* **Circular wait**循环等待: there is a circular chain of two or more processes, waiting  for a resource held by the other processes

若有一个情况不符合，死锁不会发生



------

#### Directed Graph定向图

资源： 正方形；进程：圆形

⬜--> ⚪ ：the resource was requested and granted, i.e. is allocated to the  process

⚪ --> ⬜:  the process has requested the resource and is waiting to obtain it

A cycle in the graph means that a deadlock occurs for the  respective resources and processe

![image-20211122141236524](C:\Users\DLZ\AppData\Roaming\Typora\typora-user-images\image-20211122141236524.png)



------

### 处理死锁

1. just ignore: Ostrich algorithm 鸵鸟算法
2. Detection and recovery
3. Avoid deadlocks from happening by detecting them early
4. Avoid deadlocks from happening by “breaking” Coffman’s conditions



#### 1. Ostrich algorithm 鸵鸟算法

在[计算机科学](https://baike.baidu.com/item/计算机科学)中，**鸵鸟算法**是一个忽略潜在[问题](https://baike.baidu.com/item/问题)的一种[算法策略](https://baike.baidu.com/item/算法策略)，这种策略对[计算机程序](https://baike.baidu.com/item/计算机程序)可能出现的问题采取无视态度（类似于[鸵鸟](https://baike.baidu.com/item/鸵鸟)在遇到危险时将头埋在地里，装作看不见）。鸵鸟算法的使用前提是，问题出现的[概率](https://baike.baidu.com/item/概率)很低。

------

#### 2. detection and recovery检测和恢复

**Detect** when the system is **deadlocked** and recover from them

In case that only a **single resource** exists per type, a graph approach can be used

If the graph contains a cycle the current system state is deadlocked

* Basic graph algorithms can be used to search the graph and detect cycles



#### Basic graph algorithms

**For** every node in the graph{

​		carry out a depth first search following the outgoing arcs from the node

​		**if** a previously encountered node occurs => **cycle**

​		**if** all arcs of the node are exhausted => **backtrack**

​		**if** the root node is searched again, **no cycle is found** in the current subgraph

}



#### Matrix approach (detection)

对应银行家算法

E-existing resource 

A **matrix based algorithm** is used when **multiple “copies”** of  the same resource exist

当同一资源存在多个“副本”时，使用基于矩阵的算法

processes  => P1 - Pn

exit m resources classes(1,2,3..i..m)

* Let Ei denote the “existing” resources of type I
* Let Ai denote the number of available resources of type I
* The total number of allocated and available resources (Ai ) of type i is  equal to Ei

The algorithm uses **two vectors** describing the existing and  available resources (E and A), and **two matrices** to describe  the **current** and **requested** resources per process (C and R)

该算法使用两个向量描述现有的和可用的资源(E和A)，两个矩阵描述每个进程当前的和请求的资源(C和R)



#### Steps:

1. Select a process for which the requested resource allocation can be satisfied

- R_ij <= A_i for all j in [1..m]
- Run the process and ‘mark’ it as completed

2. Reclaim the process’s resources by adding row C_i to A_i 回收进程的资源,加入c_i行到A_i行
3. Repeat the above and terminate the algorithm if no  ‘unmarked’ or ‘completable process can be found重复上述步骤，如果没有“unmarked”或“complete”过程，则终止算法



例题：

E = (4	2	3	1)		A = (2	1	0	0)

![image-20211222152811299](C:\Users\DLZ\AppData\Roaming\Typora\typora-user-images\image-20211222152811299.png)![image-20211222152820843](C:\Users\DLZ\AppData\Roaming\Typora\typora-user-images\image-20211222152820843.png)

Is the system deadlock?

首先因为A只能满足Process3，先运行Process3

​	P3运行完成后释放资源， A = (2,2,2,0)

满足Process2，运行Process2

P2运行完成后释放资源，A = (4,2,2,3)

最后满足P1

P1运行完成释放资源，A = (4,2,3,1)
