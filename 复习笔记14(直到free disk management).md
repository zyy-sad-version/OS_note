### 复习笔记14

important point:

* user view:
* implementation view

---

### User view of file system



* #### File system

user view:  in terms of abstractions that OS provide

1. How files are named, what operations allowed on them.
2. What directories looks like
3. **Interface** issue



implementation view: in terms of its low level implementation

1. How files and directories are stored
2. How disk space is managed
3. How to make everything work **efficiently**



#### Important aspects of user view

1. **File abstraction**: Hides away implementation details to the user(**File name policies**[2part file name, abstracts storage detail], **user file attribute**[metadata like：size, owner, proctection, dates])
2. **Directory structure and organization**
3. **System calls** to interact with the file system



#### File

.Type

**Regular files**: Contain user dat in **ASCII** or **binary** format

​	A file is a **named** collection of data

​	**ASCII files** consist of line of text

​	**Binary files** have internal structure	

**Directories**: group files together--->但在实现时是以file来实现

* directories provide a **mapping** of the logical file onto the physical location

* ##### System Calls

Sys call enable a **user application** to **ask the OS** to carry out an action on its behalf, in kernel mode

.Types

**File manipulation**: open(), close(), write(), delete()...





#### Directories

directories are **special** **files** that group files together and of which the  structure is defined by the **file system**[A **bit** is set to indicate that they are directories! ]

Directory structures:

1. Single level: All files in the same directory

   ![image-20211223161130405](C:\Users\DLZ\AppData\Roaming\Typora\typora-user-images\image-20211223161130405.png)

2. 2 or multiple level directories(hierarchical): Tree structure.

   1. 绝对路径absolute path name: from the root of the file system
   2. 相对路径relative path name: The current working directory is used as the starting point

3. Directed acyclic非循环的 graph(DAG): Allows files to be shared but **cycle are forbidden** (**links** to files to subdirectories)

![image-20211223161140528](C:\Users\DLZ\AppData\Roaming\Typora\typora-user-images\image-20211223161140528.png)

* The use of DAGs results in significant complications in the  implementation实现中会产生严重的并发症

  * Files have multiple absolute file names
  * Deleting files becomes a lot more complicated( meads links may no longer point to a file)
  *  A **garbage collection scheme** may be required to remove files  that are no longer accessible from the file system tree

* ##### System call

similar to files, directories are manipulated using system calls

**Directory manipulation**： open(), close(), read(), readdir(), rename(), link(), unlink(), list(), update(). 

---

### Implementation view of file system

Additional considerations: 

Disk partition, **partition tables,** **boot sectors**

Free **space management**

System wide and per process file tables

How file an directories **store** 文件储存管理

**Low level formatting**: writes sectors to the disk

**High level formatting**:  imposes a file system on top of this 

* File system manage **blocks** that can cover multiple **sectors**
* The file is split up into a number of (not necessarily) contiguous blocks. 



* ##### Partitions

Disks are usually divided into multiple partitions

![image-20211223170642017](C:\Users\DLZ\AppData\Roaming\Typora\typora-user-images\image-20211223170642017.png)



![image-20211223171319613](C:\Users\DLZ\AppData\Roaming\Typora\typora-user-images\image-20211223171319613.png)

*而不同的文件系统可能存在每一个分区

**MBR** master boot record --located at start of entire drive位于驱动器最前

* Used to boot the computer开机(BIOS reads and executes MBR)
* Contains partition table **at its(MBR) end**--即partition table是在MBR内

* One partition is listed as **active** containing a **boot block** to load the  operating system



* ##### Unix partition

![image-20211223171432968](C:\Users\DLZ\AppData\Roaming\Typora\typora-user-images\image-20211223171432968.png)

The layout of a partition differs depending on the file system.分区的布局根据文件系统的不同而不同

**Boot block**: 1. contain code to boot the OS	2.Every partition has boot block-even if it does not contain OS

**Super block**: contains the partition's key parameters, partition size, number of blocks, I-node table size.  Read into main memory when computer is booted

**Free space management**: contains a bit map or linked list that indicate the free blocks

**I-node** contains information on files, commonly maintained in I-nodes

**Root directory**: contains the top of the file-system tree

**Data**: file and directories

---

### Disk Space management

2 method are commonly used to keep track of free disk space. And disk space management approaches are very similar to keep track of free memory

**bitmap**![image-20211223190358811](C:\Users\DLZ\AppData\Roaming\Typora\typora-user-images\image-20211223190358811.png)

**linked list**![image-20211223190428006](C:\Users\DLZ\AppData\Roaming\Typora\typora-user-images\image-20211223190428006.png)



* ##### Bitmap

Represent each block by a single bit in a map

1. The **size** of the bitmap grows **with the size of the disk** but is **constant** for a  given disk

2. Bitmaps take comparably **less space than linked lists**

   1 bit per block  < ---- > 1 pointer(32bit/64bit) per block

   

   * ***Require extra space,例：block块的大小：2<sup>12</sup>byte, 磁盘大小：2<sup>30</sup> byte = 1GB=====>bitmap size: 2<sub>30</sub>/2<sup>12</sup> = 2<sup>18</sup>byte=32kb***
   * Proportional(成比例) to number of blocks
   * Keeping it in main memory is possible **only for small disks.** 

* **Linked list**

 Free blocks are used to hold the numbers of the free blocks (hence, they are no longer free). Because the free linked list **shrinks缩小** when the disk become full, this is **not wasted space**

1. Blocks are linked together----> multiple blocks list the free blocks

2. The size of the list grows with the **size** of the disk and shrinks with the size of the blocks(**not constant**)

3. Linked lists can be modified by keeping track of the number of  consecutive free blocks for each entry 

   

   * No **waste** of disk space
   * We only need to keep in memory one block of pointers (load a new block when need).
   * if block size = 2<sup>12</sup>(4K) bytes, 32-bit disk block number and disk size = 2<sup>30</sup>bytes
     * Block number 2<sup>30</sup> / 2<sup>12</sup> = 2<sup>18</sup>
     * linked-list block number = 2<sup>18</sup> / 2<sup>10</sup> = 2<sup>8</sup>
     * linked-list size = 2<sup>8</sup> * 4 kb = 1024 kb



---

### File management by OS

Apart from the free disk space tables, there is a number of key data  structures stored in memory:

除了free disk space tables，内存中还存有以下重要数据结构

* An in-memory **mount table**



* An in-memory **directory cache**

  ​	Of recently accessed directory information

  

* An **system-wide open file table**

  ​	Containing a copy of the **FCB** for every currently  open file in the system, including location on disk, file size, and “open count’

  **FCB**(File control blocks)-- kernel data structures, fcbs are protected and only accessible **in kernel mode**

  ![image-20211223194836758](C:\Users\DLZ\AppData\Roaming\Typora\typora-user-images\image-20211223194836758.png)



* A **pre-process open file table**

  ​	Containing a pointer to the system open file table

<img src="C:\Users\DLZ\AppData\Roaming\Typora\typora-user-images\image-20211223194948948.png" alt="image-20211223194948948" style="zoom:50%;" /><img src="C:\Users\DLZ\AppData\Roaming\Typora\typora-user-images\image-20211223195009543.png" alt="image-20211223195009543" style="zoom:50%;" />

(a) opening a file																	(b) reading a file