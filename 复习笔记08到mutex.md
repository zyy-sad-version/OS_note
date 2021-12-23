## 复习笔记08

lec 14

***Lec quiz***

Which one is not required for critical section solutions?

A) Mutual exclusion	B) Multiprogramming C) Progress D) Bounded waiting

B



### Approaches for mutual exclusion:



* ### Peterson's solution:

  peterson's solution is a software based solution which worked well for **older machines**

  2 share variables :

  * **turn**: Indicate which process is next to enter its critical section
  * **boolead flag[2]**: Indicate that a process is ready to enter the critical section

Two processes execute in strict alternation, can be  generalised to multiple processes

* it satisfies all requirements for mutual exclusion
* 互斥需求：turn变量一次最多只能有一个值
  * 两个进程都准备进入critical section，flag[i]和flag[j]都为真
  * Turn 是一个信号变量所以只能存放一个值
  * Hence， either while (flag[i] && turn == i) or while (flag[j] && turn == j) is true  and at most one process can enter its critical section (satisfied mutual exclusion)

示例代码：![](C:\Users\DLZ\AppData\Roaming\Typora\typora-user-images\image-20211221212619500.png)

-----

![](C:\Users\DLZ\AppData\Roaming\Typora\typora-user-images\image-20211221212858702.png)

##### Peterson's solution 

progress and bounded waiting:

##### progress: if *j* is not waiting to enter its critical section

​					-> flag[j] = false

​					->while(flag[j] && turn == j) will terminate process i

​					-> process i enters critical section

##### Bounded witing: is Process i and Process j both want to enter critical section

​								-> flag[i] == flag[j] == T

​								-> turn is either i or j assuming turn = i 

​								-> while (flag[j] && turn == j)terminates, and then process i enters critical section

​							-> P i finishes critical section -> flag[i] = false

​							-> while (flag[i] && turn == i) terminates and process j enter critical section



* #### Hardware based Approaches

**Disable interrupts** whilst **executing a critical section** and  prevent interruption (i.e., interrupts from timers, I/O devices, etc.)



Disable interrupts may be appropriate on **single CPU machine**

This is inefficient on morden multi-core/multi-processor machine

reason: 1. Disabling interrupts on all cores/CPUs **takes time and cause delay**

		2. **CPU capacity is lost** on other cores
		3. **No guarantee for mutual exclusion**



* test_and_set() & compare_and_swap()

  Implement them as a set of atomic instruction(不会被中断的)

  * Reading and setting the variable(s) is done as one “complete” set of  instructions

    读取和设置变量是作为一个“完整的”指令集来完成的  

  * If test_and_set() or compare_and_swap() are called simultaneously, they  will be executed sequentially

    如果同时调用test_and_set()或compare_and_swap()，它们将依次执行

They are used in in combination with **global lock variables**,  assumed to be true if the lock is **in use**

sample code：![image-20211221215941910](C:\Users\DLZ\AppData\Roaming\Typora\typora-user-images\image-20211221215941910.png)

sample code:![image-20211221220011770](C:\Users\DLZ\AppData\Roaming\Typora\typora-user-images\image-20211221220011770.png)

Disadvantage:

1. test_and_set() and compare_and_swap() are hardware instructions and usually **not directly accessible to the user**

2. Busy waiting is used

3. Deadlock is possible: When 2 locks are requested in opposite order in different threads

   ![image-20211221220248971](C:\Users\DLZ\AppData\Roaming\Typora\typora-user-images\image-20211221220248971.png)

操作系统使用的硬件指令实现高级机制/指令： mutexes， semaphore



* #### Mutexes

mutexes are an approach for mutual exclusion provided by the OS containing a Boolean lock variable to indicate availability可用性

* The lock variable is set to **true** if the lock is **available** (process can enter  critical section), **false** if **not**

2 **atomic** functions are used to manipulate the mutex:

* **acquire**(): called **before entering** a critical section, boolean set to false
* **release**(): called **after exiting** the critical section, boolean set to true again

得到锁的进程必须解锁

Advantage of mutex lock

1. Simple implementation
2. Simple implementation
3. **Efficient** **on multi-core/multi-processor systems** when **locks are  held for a short time** only



***lec quiz***

Different outputs of parallel programs is  the result of …? 造成并行程序结果不同的原因是：

A. Mutual exclusion	 B. Race condition	  C. Process progress	 D. hardware locks

B