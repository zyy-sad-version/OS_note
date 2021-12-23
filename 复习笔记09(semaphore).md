## 复习笔记09

#### important definition:

Semaphore: Semaphore is an **approach** for mutual exclusion (and process  synchronisation) provided by the operating system

Starvation: All the programs continue to run **indefinitely** but **fail to make any progress.**

Deadlock: two or more processes are **waiting**  **indefinitely** for an event that can be caused only by one  of the waiting processes

producer and consumer problem: P and C share *n* buffers that are capable of holding one item each

difficult in synchronising code: race condition



***lec quiz***

Which one is NOT recommended for  multi-processor systems?

A. Mutext locks 	B. Peterson solution 	C. Disabled interrupts 	D. test_and_set()

C



* #### Semaphore

Semaphore is an approach for mutual exclusion and process synchronisation provided by the operating system:

		1. They contain integer variable
		2. Distinguish between binary (0/1) and counting/general semaphores(0-N)

Two atomic functions are uses to manipulate semaphores(like count++)

1. *wait()* is called when a resource is acquired, the counter is **decremented**
2. *signal()/post()* is called when a resource is released, the counter is **incremented**

##### Implementation:![image-20211222093730413](C:\Users\DLZ\AppData\Roaming\Typora\typora-user-images\image-20211222093730413.png)

##### Semaphore example：![image-20211222093947388](C:\Users\DLZ\AppData\Roaming\Typora\typora-user-images\image-20211222093947388.png)

* Calling *wait()* will **block** process when the internal counter is negative(**not busy waiting**)

  1. The process joins the "blocked" queue

  2. The process state is changed from **running** to **blocked**
  3. Control is transferred to the process scheduler

* Calling *post()* **removes a process** from the **blocked queue** if the counter is less or equal to 0

  1. The process state is changed from **blocked** to **ready**
  2. Different queueing strategies can be employed to remove processes(FIFO...)

If the internal counter is negative, the **negative** value is the **number** of processes **waiting for the resource**

* *block()* and *wakeup()* are system calls provided by the OS
* *post()* and *wait()* must be atomic
  * They can be achieve through **mutexes** or **disabling interrupts** in single CPU systems, **hardware instruction**
  * Busy waiting is **moved** from **critical section** to **wait()** and **post()**



---

#### Semaphore in Linux

Semaphore within the same process can be declared as global variables of type *sem_t*

sem_init()初始化信号量

sem_wait(), 减少信号量，当信号量为0时，等待， 当信号量大于0时被唤醒

sem_post(), 增加信号量

**Example:**![image-20211222101651179](C:\Users\DLZ\AppData\Roaming\Typora\typora-user-images\image-20211222101651179.png)



----

#### Some potential difficult about semaphore

**Starvation**: Poorly designed queueing approaches may result in fairness violations可能违反bounded waiting -进程不能无限等待

**Deadlock**：2 or more processes are waiting indefinitely for an event that can be **caused only by one of the waiting processes**



***lec quiz***

Which one is NOT included by semaphore  instructions?

A. Decrement value 	B. Increment value 	C. Initiate value 	D. Sort value

D

---

#### Producer and Consumer problem

P and C share n buffers that are capable of holding one item each

1. buffer can be of bounded or unbounded size
2. There can be one or multiple consumers and/or producers

**Producer** add items and goes to sleep if the buffer is **full**

The **consumer** remove item and goes to sleep if the buffer is **empty**.



---

#### one consumer, one producer

The simplest version of the problem, and the buffer is unbounded size

* A counter(index) variable keeps track of the number of items in the buffer
* And it use 2 binary semaphore:
  * *sync* synchronises access to the buffer(counter), initialised to 1
  * *delay_consumer* ensures that the consumer goes to sleep when there are no items available, initialised to 0

code exapmle：![](C:\Users\DLZ\AppData\Roaming\Typora\typora-user-images\image-20211222124125573.png)

从以上代码看出，对item的操作，需要synchronization。

且race condition 依然存在

When the consumer has exhausted the buffer, should have gone  to sleep, but the producer increments items before the  consumer checks it



***lec quiz***

Which one is the critical section in  bounded-buffer producer/consumer  problem?

A. Consumer  	B. Producer  	C. Sem(&sync) 	D. Counter

D