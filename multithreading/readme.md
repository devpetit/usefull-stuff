# Multithreading

## Concepts

### Process

Process is something that the operating system uses to execute a program by providing the resources required. Each process has a unique process id associated with it. We can view the process within which a program is running using the windows task manager.

### Thread

A Thread is a lightweight process that is responsible for executing application code. A process has at least one thread which is commonly called the main thread which actually executes the application code. A single process can have multiple threads.

### What is the difference between Process and Thread?

This is one of the most frequently asked Multithreading Interview Questions in C#. A process is started when you start an Application. The process is a collection of resources like virtual address space, code, security contexts, etc. A process can start multiple threads. Every process starts with a single thread called the primary thread. You can create n number of threads in a process. Threads share the resources allocated to the process. A process is the parent and threads are his children.

### Task

A Task is an abstraction that allows us to easily use the thread pool or native threads without the complexity we saw previously. It is like a promise a unit of work that may or may not execute asynchronously.

Task vs Thread:

- Task can return a result
- Tasks can be chained
- Tasks do propagate exceptions, which are wrapped in an AggregateException. To retrieve the wrapped exceptions we can inspect the InnerExceptions property.

> We have to use a Thread when we need a foreground execution of some code. Tasks always run on background threads, which do not block the application from exiting.

## Synchronization

## Lock

The lock statement acquires the mutual-exclusion lock for a given object, executes a statement block, and then releases the lock.

```csharp
lock (x)
{
    // Your code...
}
```

is equivalent to:

```csharp
object __lockObj = x;
bool __lockWasTaken = false;
try
{
    Monitor.Enter(__lockObj, ref __lockWasTaken);
    // Your code...
}
finally
{
    if (__lockWasTaken) Monitor.Exit(__lockObj);
}
```

> When you synchronize thread access to a shared resource, lock on a dedicated object instance (for example, private readonly object balanceLock = new object();) or another instance that is unlikely to be used as a lock object by unrelated parts of the code. 

## Monitor

Monitor provides a mechanism that synchronizes access to objects. It can be done by acquiring a significant lock so that only one thread can enter in a given piece of code at one time. Monitor is no different from lock but the monitor class provides more control over the synchronization of various threads trying to access the same lock of code.

Methods:

- Monitor.Enter() : Acquires an exclusive lock on the specified object. This action also marks the beginning of a critical section.
- Monitor.Exit() : Releases an exclusive lock on the specified object. This action also marks the end of a critical section protected by the locked                          object.
- Monitor.Pules() : Notifies a thread in the waiting queue of a change in the locked object's state.
- Monitor.Wait() : Releases the lock on an object and blocks the current thread until it reacquires the lock.
- Monitor.PulesAll() : Notifies all waiting threads of a change in the object's state.
- Monitor.TryEnter() : Attempts to acquire an exclusive lock on the specified object.

The following example demonstrates the usage of the `Wait` and `PulseAll` signalling. 

```csharp
public void Enqueue(T item)
    {
        lock (queue)
        {
            while (queue.Count >= maxSize)
            {
                Monitor.Wait(queue);
            }
            queue.Enqueue(item);
            if (queue.Count == 1)
            {
                // wake up any blocked dequeue
                Monitor.PulseAll(queue);
            }
        }
    }
    public T Dequeue()
    {
        lock (queue)
        {
            while (queue.Count == 0)
            {
                Monitor.Wait(queue);
            }
            T item = queue.Dequeue();
            if (queue.Count == maxSize - 1)
            {
                // wake up any blocked enqueue
                Monitor.PulseAll(queue);
            }
            return item;
        }
    }
```

## Semaphore

Semaphores are Int32 variables stored in a operating system resources. When we initialize the semaphore object we initialize with number. This number limits the threads that can enter into the critical section.

When a thread enters into a critical section, it decreases the Int32 variable with 1 and when a thread exits from a critical section, it increases the Int32 variable with 1.

- `semaphoreObject.WaitOne();` - Threads can enter into the critical section by using WaitOne method. They called the WaitOne method on semaphore object. If the Int32 variable maintained by semaphore is greater than 0 then it allows calling thread to enter. There is an overload for it with wait interval.
- `semaphoreObject.Release();` - When a thread exits from the critical section, it must call the Release method to increment the counter maintained by semaphore object. It allows waiting threads to enter into a critical section.

## Mutex

A Mutex is like a C# lock, but it can work across multiple processes. In other words, Mutex can be computer-wide as well as application-wide.

> Mutex is a binary Semaphore so to say.

A Mutex can be either named or unnamed. If a Mutex is named, it is eligible to be a system-wide Mutex that can be accessed from multiple processes.

The `initialOwnership` flag decides whether the current thread that creates the Mutex is the owner of the Mutex, after release the thread is no longer the owner.

## EventWaitHandle (Signaling)

This is a class, which allows the threads to interact with each other by notifying through the signals.

Two variants:

- ManualResetEvent
- AutoResetEvent (releases only one thread that is blocked in WaitOne)

Methods:

- Set() - releases all blocked threads
- WaitOne() - waits for the signal
- Reset() - resets the initial state