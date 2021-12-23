### 线程Thread





#### User Thread

如果OS或硬件不支持multithreading，选择User Thread

#### Kernel Thread

#### Hybrid Thread









#### 课后习题

![image-20211012084045604](C:\Users\DLZ\AppData\Roaming\Typora\typora-user-images\image-20211012084045604.png)

if I pass address of i 

the result only print  one value

![image-20211012084144298](C:\Users\DLZ\AppData\Roaming\Typora\typora-user-images\image-20211012084144298.png)



说明i都是被放在同一地址？



本来的结果：

Hello from thread 1

​								.	(无序的)

​								.

​								.





------

#### Lab

1. A pointer to a pthread_t variable, in which the thread ID of new thread is stored


2. A pointer to a thread attribute object. This object controls details of how the thread
interacts with the rest of the program. If you pass NULL as the thread attribute, a thread
will be created with the default thread attributes.
3. A pointer to the thread function. This is an ordinary function pointer, of this type: void*
(*) (void*)
4. A thread argument value of type void* . Whatever you pass is simply passed as the
argument to the thread function when the thread begins executing.

------

#### Passing Data to Thread

One commonly used technique is to define a structure for each thread function, which contains the “parameters” that the thread function expects.

