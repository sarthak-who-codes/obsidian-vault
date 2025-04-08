## Process

Its a instance of a program.

It has its own **memory space, system resources, and execution context**.

#### **Characteristics of Processes:**

1. **Independent Execution:** Each process runs independently of others.
2. **Separate Memory Space:** Processes do not share memory directly(heap, stack, data section).
3. **Resource Allocation:** Each process gets its own resources like CPU time, memory, and file handles.
4. **Inter-Process Communication (IPC):** Processes communicate using mechanisms like pipes, message queues, shared memory, or sockets.
5. **Context Switching:** Switching between processes requires saving and restoring process states, making it resource-intensive.

### Process Control Block(PCB)

Stores metadata about the process for Kernel.

It contains:
- PID, Process State, Program Counter and Registers.
- Process control info (running/stopped, priority).
- Page Table (Virtual memory to physical mapping).
- Accounting (CPU / Memory usage).
- Memory management info(pointer to code / stack etc.)
- IO info (File descriptors)
- IPC info, semaphores, mutexes, shared memory, messages.

## Threads

A **thread** is the smallest unit of execution within a process. A process can have multiple threads, which share the same memory and resources but execute independently.

#### **Characteristics of Threads:**

1. **Shared Memory:** Threads of the same process share the same memory space.
2. **Lightweight:** Threads require less overhead compared to processes.
3. **Faster Context Switching:** Switching between threads is faster than switching between processes.
4. **Communication:** Threads communicate easily since they share memory.
5. **Concurrency and Parallelism:** Threads can execute concurrently on a single-core CPU or in parallel on multi-core processors.

### Thread Control Block

Stores metadata about kernel for Thread.

It contains:
- TID, Thread State, Program counter, register
- Process control info(running / stopped, priority).
- Accounting (CPU/memory).
- Memory management info (pointer to the stack etc.).
- Pointer to the parent PCB.

## Process vs Threads

|Feature|Process|Thread|
|---|---|---|
|**Definition**|A process is an independent program in execution.|A thread is a lightweight subunit of a process.|
|**Memory**|Each process has its own separate memory space.|Threads share the same memory within a process.|
|**Communication**|Requires IPC mechanisms like pipes, shared memory, or message queues.|Can communicate easily since they share memory.|
|**Context Switching**|Expensive, as it involves saving/restoring process state.|Faster, as threads share memory.|
|**Resource Allocation**|Each process gets separate resources.|Threads share resources of the process.|
|**Concurrency**|Processes execute independently.|Threads execute concurrently within a process.|
|**Creation Time**|Slower due to memory allocation and resource management.|Faster since it shares process resources.|
|**Crash Handling**|A crash in one process does not affect others.|A crash in one thread can crash the whole process.|

---
## Node Worker Threads

- Same process but multiple threads
- Shared parent process memory and file descriptors
- Implemented with `worker_threads`.
- Each thread gets its own mail loop
- Useful for large CPU intensive workloads.
- If parent process dies, worker threads also dies.

### Worker thread messages

- Needs to transferable(ex. can be a socket).
- Parent process coordinates the process
- Each child process gets a port and they can communicate among themselves using that port.

---
## Child processes and cluster module

**Why do it need child processes when we have worker threads?**
- Thread use shared memory and kernel has limit on how much a process can consume.
- Isolation
- Works with other program not just node.

### Node fork

- Forks runs on initialization phase
- Its a asynchronous process(even though it runs in initialization phase).
- Does not clone like unix fork
- User should manually set up child process (that's why cluster is created)


### Cluster module

- Helps to manage child processes
- Gives you indication who is parent
- Manages communication between parent and child
- Great for socket handling
- Scheduling algorithms
