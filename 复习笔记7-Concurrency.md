### 复习笔记7-Concurrency

Lec 13

### Important definition

1. Critical section: A set of instructions in which **shared  variables** (between processes/threads) **are changed**
2. Mutual exclusion[**enforced** for critical section]: 
   1. Only **one process at a time** should be in the critical section
   2. Processes have to **get "permission"** before entering their critical section



Requirement and approaches for mutual exclusion:

requirement: 1. **Mutual exclusion**: only one process can be in its critical section **at any  one point in time;** 

​						2.**Progress**: any process must be able to enter its critical section **at  some point in time**;

​					3. Processes/threads in the “**remaining code**[在critical section中的code]” **do not influence** access to critical  sections

​				4.**Fairness/bounded waiting**: processes cannot be made to wait indefinitely进程不能无限等待

approaches:1.Software based: Peterson’s solution

​					2.Hardware based: test_and_set(), compare_and_swap()

​					Based on: • Mutexes • Semaphores • Monitors (software construct within the programming languages





#### Concurrency Definition

**Thread and process execute concurrently or in parallel and can share resources**

A process/thread can be interrupted at any point of time(I/O, timer)

* PCB save the state of process
* Sharing data can lead to inconsistent.
* Outcome of execution depends on order with which instruction are carried out

Outcome of program may become **unpredictable**

----

#### Race Condition

A race condition occurs when multiple thread/processes access shares data and the result is dependent on the order in which the instructions are interleaved交错.

example:

sharing resource： counter = 5

两个不同进程：	

| Process1             | Process2             |
| -------------------- | -------------------- |
| Register1 = counter; | Register2 = counter; |
| Register1 +=1;       | Register2 -=1;       |
| counter = Register1; | counter = Register2; |

发生race condition：

<img src="C:\Users\DLZ\AppData\Roaming\Typora\typora-user-images\image-20211221170529912.png" alt="image-20211221170529912" style="zoom: 50%;" />



---

#### Concurrency with OS-

####  data structure

Multiple processes are running in the kernel, kernel processes can be interrupted at any point

Kernel maintain data structures like **process table, memory structures, open file lists **

* These data structures are accessed concurrently/ in parallel
* These can be subject to **concurrency issues**

​					

#### Resources

* Processes share resources, memory, files, processor time, printer, I/O devices...

* OS must:
  * Allocate and deallocate these resources safely (avoid interference干扰, deadlock, starvation)
  * Make sure these interference **do not result in race condition**
* OS provide locking mechanisms锁机制 to implement/support **mutual exclusion**互斥锁 (prevent starvation and deadlock)

***Lecture Quiz***

1.Which one is not a benefit of concurrency?

A. CPU utilization 	B. Unpredictable result 	C. Reason sharing 	D. Parallel access to critical section

B

2.Concurrency would be challenging if  processes try to ….?

A. Access private data	B. Access their local registers	C. Access public data	D. Execute their codes

C



---

### Critical section, Mutual Exclusion

##### Definition

A **critical section** is a set of instructions in which shared  variables (between processes/threads) are changed

**Mutual exclusion** must be enforced for critical sections 

​	• Only one process at a time should be in the critical section 

​	• Processes have to get “permission” before entering their critical  section



解决critical section问题必须满足以下要求：

Any solution to the critical section problem must **satisfy the  following requirements:*** 重点

* **Mutual exclusion**: only one process can be in its critical section at any  one point in time
* **Progress**: any process must be able to enter its critical section **at  some point in time**
  *  Processes/threads in the “**remaining code**[在critical section中的code]” **do not influence** access to critical  sections

* **Fairness/bounded waiting**: processes cannot be made to wait indefinitely进程不能无限等待

这些条件必须被满足，与执行顺序无关

critical section sequence diagram

![image-20211221204731342](C:\Users\DLZ\AppData\Roaming\Typora\typora-user-images\image-20211221204731342.png)

#### Approaches for mutual exclusion:

* software based: Peterson's solution
* Hardware based: test_and_set(), compare_and_swap()
* Based on: 
  * Mutexes
  * Semaphore
  * Monitors(software construct within programming languages)
* 互斥现象中必须避免死锁！



***lec quiz***

1.Does single-processor systems need to  consider mutual exclusion?

A. Yes, always	B. No, never	C. Yes, if it is non-preemptive	D. No, if it is non-preemptive

D

2.What are the final results of A and B, according to  following concurrent execution (A=1, B=1)?

| Process1   | Process2   |
| ---------- | ---------- |
| A = A + 1; | B = 2 * B; |
| B = B + 1; | A = 2 * A; |

1. P1,P2,P1,P3 ====> A = 4; B = 3;
2. P1, P1, P2, P2====> A = 4; B = 4;
3.  P1, P2, P2, P1 ===> A = 4; B = 3;