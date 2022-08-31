## Basics
### Reference - Book Operating System Concepts by Abraham Silberschatz, Peter Baer Galvin, Greg Gagne
### Note : There might be several typo and spelling mistakes. Made just for my quick reference.
### system calls
* They can be grouped roughly into 6 major categories :
process control, file manipulation, device manipualtion, information maintenance, communications protection
### DTrace 
* debugging OS

### Process
* each process may be one of teh following states -  new, ready, running, waiting or terminated
* each process is represented in the operating syste by its own process control block (PCB)
* zombie process and approach of unix to fix it via init process that issues wait() periodically
* Google chrome tidentifies 3 different types of processes , - browser, renderers, plug-ins

### Interprocess communication (IPC)
* process executing concurrently may be either indepenedent or cooperating processes. 
* Process is cooperating if it affects or get affected by other processes in the system.
* Cooperating process need interprocess cmmunication
* It has two models - shared memory and message passing
* Shared memory can be faster than message passing as message passing are typically implemented using system calls, so more time. in shared memory , system call is called only to establish shared memory regions. 
* on systems with several processing, message passing perform better than shared memory as the laer suffers from cache coherency issue because of migration of shared data among the sevral caches. 
* typically shared memory resides in the address space of the process creating it, others attach it to their address space if they want to establish the communication. 
* Message passing allows processes to communicate without sharing the same address space. 
* Message passing communication can be - direct/indirect; synchronous/asynchronous; automatic/explicit buffering
* In direct communciation, each process that want to communicate must explicitly name the recipient or the sender, send(P, meg); receive(Q, msg)
* Symmentry where both processes are named, in asymmentry only sender names the recipient. 
* In indirect messages are send to and from ports.
* In synchronization, communication occurs through call to send(), receive() primitives. different design - blocking/non-blocking; also called synchronous/asynchronous. when both send() and receive() are blocking, that's rendezvous.


### Sockets
* Socket is defined as an endpoint for communication.
* Pair of process communicating over network has a pair of sockets , one for each process. 
* All ports below 1024 are well known.
* It is low level form of communication as socket allow only unstructured stream of bytes to be exchanged between the communicating threads. It is responsibility of client or server application to impose structure on the data.

### Remote Procedure Call (RPC)
* RPC has message-based communication, it is similar in many aspect to IPC
* But the message exchanged in RPC are well structured, not just the packet of data.
* Each message is addresses to an RPC daemon listening to a port on the remote system, and each contains an identifier specifying the function to execute, and the parameters to pass to that function. The function is then executed as requested, and any output is sent back to requester in a separate message.
* RPC hides the detail that allows communication to take place by providing a stub on the client side.
* To let the client know the port number of server , there is rendezvous (also called matchmaker) daemon that is on fixed RPC port.  

### Pipes
* Pipe acts as a conduit allowing two processes to communicate.
* ordinary pipes are unidirectional. They rqeuire a parent-child relationship between communicating processes. They exist only when the processes are communicating to one another. Hence tehn can only be used for communciation between the processes on the same machine.
* Named pipe provide a much powerful communication tool. It can be bidirectional and no parent-child relationship is required. 
* Once a named pipe is established , several processes can use it for communication. They continue to exist after communication processes have finished.
* Named pipes are referred as FIFOs in Unix system. 
* Once created , they appear as a typical file in  the file system.  It will continue to exist until explicitly deleted from the file system.
* Although FIFO allow bidirectional communication , but only half-duplex transmission is permitted. 
* Moreover, communicating processes must reside on the same mahine, esle socket is required for intermediate communication. 
* Named pipes on Windows allows full duplex communication, an dcommunicating process may reside either on same or different machines.
* Also, only byte oriented data is allowed in UNIX FIFO , but windows system allow either byte or message oriented. 

### Threads
* Thread is a basic unit of CPU utilization; it comprises of thread ID, a program counter, a register set , and a stack.
* It shares its code section, data section, and other os resources like open files, signals with other threads belonging to the same process. 
* An application typically is implemented as single processes and multiple threads. 
* Linux uses kernel thread for managing the amount of free memory in the system.
* On system with multiple core, concurency means that threads can run in parallel, because system can assign a separate thread to each core. 

### Difference between parallelism and concurrency
* A system is parallel if it can perfrom multiple tasks simultaneously. In contrast, a concurrent system supports more than one task by allowing all the tasks to make progress. Hence, it is possible to have concurrency without parallelism.

### Amdahl's Law
* Amdahl's Law - > sppedup <= 1/(S+((1-S)/N)), where S is portion of application that must be performed serially on the system with N processing cores.
* As N reaches to infinity, the speedup converges to 1/S. 
* This is fundamental priciple behind this Law: the serial portion of application could have disproportionate effect on the performance taht is gained by aading additional compute cores.
* It is argued that this law does not take into account the hardwrae performance enhancement used in design of multicore system, hence this may cease to be applicable with increase in processing cores in morder computer systems.

### Types of Parallelism
* Data parallelism - it focuses on distributing subset of same data across multiple computing cores, and perfroming the same operation on each core.
* Task parallelism - it distributes tasks (threads) across multiple cores, not data. Each thread performing unique operation. May be operating either on same or different data.

### Multithreading model
* Thread can be at user level , called user thread or at kernel level called kernel thread.
* User thread are supported above teh kernel and managed without kernel support.
* Kernel threads are managed and supported directly by operating system. 
* Three common ways of establishing relation between user thread and kernel thread - many-to-one model , one-to-one model, and many-to-many model.
* many-to-one maps many user thread to one kernel thread. Thread management is done by thread library in user space. In this case, only one thread can access the kernel at the time, multiple threads cannot run on parallel on multicore system , hence not suitable for multicore systems. 
* one-to-one maps each user thread to a kernel thread. Provides more concurrency by allowing other threads to run even when thread amkes a blocking system call, but posses the overhead of creating kernel thread for each user thread that can buden application perfromance. Linux, along with famility of windows implements this.
* many-to-many models multiplexes many user thread to smaller  or equal number of kernel threads. The number of kernel threads maybe specific to either the particular application or particlar machine. But it does not result in true concurrency as kernel can schedule only one thread at a time. One-to-one model allows greater concurrency but one has to be careful in not to create too many threads within the application. 
*  The many-to-many model suffers neither of these shortcomings : developers can create as many threads as required, and corresponding kernel threads ac=can run in parallel on multi processor. Also, when thread perfroms blocking system call, the kernel can schedule another system call.
* When user-level thread is allowed to bound to  a kernel thread, this variation is refered as two-level model.  

### Threading library
* Posix Pthread used in Unix system.
