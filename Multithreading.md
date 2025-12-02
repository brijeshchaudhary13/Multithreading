

# 📘 **Multithreading in C++ — Chapter 1**

---

# **1. Introduction to Multithreading**

---

### **1.1 What is Multithreading?**

Multithreading is the ability of a program to run multiple execution paths (called *threads*) concurrently. A thread represents a lightweight unit of execution within a process.

In a multithreaded environment, multiple tasks progress independently—sometimes in parallel, depending on CPU availability.

🔍 **Analogy:**
A cook boiling rice while chopping vegetables. Both tasks progress independently — this is *multithreading*.

---

#### **Key Characteristics**

* Threads share the same memory space.
* Context switching between threads is faster than between processes.
* Threads improve responsiveness, especially in UI or real-time systems.

---

#### **Real-world Examples**

| Application | How Threads Help                                       |
| ----------- | ------------------------------------------------------ |
| Web browser | Multiple tabs, video rendering, downloads              |
| MS Word     | Typing, spell check, auto-save                         |
| Games       | Graphics, audio, physics engine running simultaneously |

---

### **1.2 Process vs Thread**

| Feature        | Process                                 | Thread                                      |
| -------------- | --------------------------------------- | ------------------------------------------- |
| Memory         | Independent memory                      | Shared memory within same process           |
| Creation Cost  | Heavyweight                             | Lightweight                                 |
| Communication  | Slow (IPC required)                     | Fast (access same address space)            |
| Failure Impact | One process crash doesn't affect others | One thread failure may crash entire process |

📌 **Conclusion:** Threads are efficient but require synchronization because they share memory.

---

### **1.3 Benefits and Challenges of Multithreading**

#### ✔ Benefits

* **Performance Boost:** Can use multiple CPU cores.
* **Responsiveness:** UI doesn't freeze during lengthy operations.
* **Resource Sharing:** Threads access shared memory without duplication.

#### ❌ Challenges

* **Race Conditions**
* **Deadlocks**
* **Debugging Difficulty**
* **Synchronization Overhead**

---

### **1.4 Types of Parallelism**

| Type                 | Meaning                                       | Example                                        |
| -------------------- | --------------------------------------------- | ---------------------------------------------- |
| **Task parallelism** | Different tasks executed by different threads | One thread handling UI, another computing data |
| **Data parallelism** | Same task executed on different data segments | Divide sorting work between threads            |

---

---

# 📘 **Chapter 2 — C++ Thread Support Library**

---

### **2.1 Overview of `<thread>` Header**

C++ introduced official multithreading support in **C++11** via the `<thread>` library.

```cpp
#include <thread>
```

This header allows:

* Thread creation
* Thread lifecycle management
* Thread communication & synchronization

---

### **2.2 Creating and Starting Threads**

A thread starts executing as soon as it is constructed.

Example using a function:

```cpp
#include <iostream>
#include <thread>
using namespace std;

void task() {
    cout << "Hello from thread!" << endl;
}

int main() {
    thread t(task); 
    t.join();
    return 0;
}
```

---

### **2.3 Ways to Create Threads in C++**

| Method                     | Description                  |
| -------------------------- | ---------------------------- |
| Function pointer           | Pass a normal function       |
| Lambda                     | Pass an inline function      |
| Functor                    | Object with `operator()`     |
| Static member function     | Class level function         |
| Non-static member function | Requires an object reference |

---

#### **A. Using Function Pointer**

```cpp
void work(int x){
    while(x-- > 0)
        cout << x << endl;
}

int main(){
    thread t(work, 5);
    t.join();
}
```

---

#### **B. Using Lambda Expression**

```cpp
thread t([](int x){
    while(x--) cout << x << endl;
}, 5);

t.join();
```

---

#### **C. Using Functor (Function Object)**

```cpp
class Task {
public:
    void operator()(int x) {
        while(x--) cout << x << endl;
    }
};

int main(){
    thread t(Task(), 5);
    t.join();
}
```

---

#### **D. Using Non-Static Member Function**

```cpp
class Worker {
public:
    void run(int n) {
        while(n--) cout << n << endl;
    }
};

int main(){
    Worker w;
    thread t(&Worker::run, &w, 5);
    t.join();
}
```

---

#### **E. Using Static Member Function**

```cpp
class Worker {
public:
    static void run(int n) {
        while(n--) cout << n << endl;
    }
};

int main(){
    thread t(&Worker::run, 5);
    t.join();
}
```

---

### **2.4 Thread Lifecycle — `join()` vs `detach()`**

| Method     | Meaning                                         |
| ---------- | ----------------------------------------------- |
| `join()`   | Main thread waits until the new thread finishes |
| `detach()` | Thread runs independently (daemon thread)       |

📌 Once detached, the thread **cannot be joined**.

---

#### Example: `join()`

```cpp
thread t(task);
t.join(); // main waits
```

#### Example: `detach()`

```cpp
thread t(task);
t.detach(); // runs in background
```

---

📌 A thread must be either `joined` or `detached`, otherwise calling `std::thread` destructor will terminate the program.

---

### ✔ End of Chapter 2

---

Great — continuing in the same professional structure.

---

# 📘 **Chapter 3 — Thread Management**

Thread management refers to controlling the behavior and lifecycle of threads—checking their state, coordinating execution, identifying them, and utilizing available system resources effectively.

---

## **3.1 Checking Thread Joinable State — `joinable()`**

Every created thread has a state. Before calling `join()` or `detach()`, it's a good practice to check whether the thread is **joinable**.

🔍 A thread is **joinable** if:

* It has been started.
* It has not yet been joined or detached.

🚫 Calling `join()` twice or joining an already detached thread results in a **runtime error** (`std::system_error`).

---

### **Example: Using `joinable()`**

```cpp
#include <iostream>
#include <thread>
using namespace std;

void work() {
    cout << "Thread Running..." << endl;
}

int main() {
    thread t(work);

    if (t.joinable()) {
        t.join();  // Safe to join
    }

    return 0;
}
```

---

## **3.2 Checking Hardware Parallelism — `hardware_concurrency()`**

This function returns the **number of CPU cores** available for concurrent execution.
It helps decide how many threads should be created for performance-sensitive tasks.

---

### **Example**

```cpp
#include <iostream>
#include <thread>
using namespace std;

int main() {
    cout << "Available CPU Threads: "
         << thread::hardware_concurrency() << endl;
    return 0;
}
```

> Note: The returned value is only a **hint**, and may be zero on some systems.

---

## **3.3 Identifying Threads — `get_id()`**

Every thread has a unique ID assigned by the OS.

---

### **Example**

```cpp
#include <iostream>
#include <thread>
using namespace std;

void task() {
    cout << "Worker Thread ID: " << this_thread::get_id() << endl;
}

int main() {
    thread t(task);
    cout << "Main Thread ID: " << this_thread::get_id() << endl;

    t.join();
    return 0;
}
```

---

## **3.4 Thread Synchronization Challenges**

When multiple threads access shared resources, certain problems can occur if access is not synchronized.

---

### **Common Issues**

| Problem        | Description                                              | Example                               |
| -------------- | -------------------------------------------------------- | ------------------------------------- |
| Race Condition | Two threads modify shared data at the same time          | Updating shared counter               |
| Deadlock       | Two threads wait on each other forever                   | Two locks acquired in opposite order  |
| Starvation     | A thread never gets CPU or access to shared resource     | Priority-based scheduling             |
| Livelock       | Threads keep responding to each other but don’t progress | Two threads repeatedly avoid conflict |

---

### **Example of a Race Condition**

```cpp
#include <iostream>
#include <thread>
using namespace std;

int counter = 0;

void increment() {
    for (int i = 0; i < 100000; i++)
        counter++; // Unsafe access
}

int main() {
    thread t1(increment);
    thread t2(increment);

    t1.join();
    t2.join();

    cout << "Final Counter: " << counter << endl;
    return 0;
}
```

⚠ Result is unpredictable because both threads modify `counter` simultaneously.

---

### **Fixing Race Condition (Preview)**

We fix this using **mutex locks** (covered in Chapter 4):

```cpp
mutex mtx;

void increment() {
    for (int i = 0; i < 100000; i++) {
        lock_guard<mutex> lock(mtx);
        counter++;
    }
}
```

---

### ✔ End of Chapter 3

---
Continuing in the same structured and professional format.

---

# 📘 **Chapter 4 — Synchronization Mechanisms**

Synchronization ensures safe access to shared resources when multiple threads operate concurrently. Without synchronization, threads may interfere with each other, leading to race conditions, corrupted memory, unpredictable results, or crashes.

C++ provides multiple synchronization tools under `<mutex>` and `<shared_mutex>`.

---

## **4.1 `std::mutex`**

A **mutex (Mutual Exclusion)** is the most fundamental synchronization primitive.
It ensures **only one thread at a time** can access a protected (critical) section.

---

### **Example: Protecting Shared Data**

```cpp
#include <iostream>
#include <thread>
#include <mutex>
using namespace std;

int counter = 0;
mutex mtx;

void increment() {
    for(int i = 0; i < 100000; i++) {
        mtx.lock();
        counter++;   // Critical section
        mtx.unlock();
    }
}

int main() {
    thread t1(increment);
    thread t2(increment);

    t1.join();
    t2.join();

    cout << "Final Counter: " << counter << endl;
}
```

🧠 Without the mutex, the result would be non-deterministic.

---

## **4.2 `std::lock_guard`**

`lock_guard` is an RAII wrapper for `mutex`.
It locks the mutex upon creation and automatically unlocks when destroyed—ensuring exception safety.

---

### **Example Using `lock_guard`**

```cpp
void increment() {
    for(int i = 0; i < 100000; i++) {
        lock_guard<mutex> lock(mtx);
        counter++;
    }
}
```

✔ No need to manually call `lock()` or `unlock()`.

---

## **4.3 `std::unique_lock`**

`unique_lock` is more flexible than `lock_guard`:

| Feature            | Available in `unique_lock` | `lock_guard` |
| ------------------ | -------------------------- | ------------ |
| Manual lock/unlock | ✔                          | ❌            |
| Deferred locking   | ✔                          | ❌            |
| Try-lock support   | ✔                          | ❌            |
| Timed locking      | ✔                          | ❌            |

---

### **Example: Deferred Locking**

```cpp
unique_lock<mutex> lock(mtx, defer_lock);
lock.lock();  // lock later
```

---

### **Example: Try-lock**

```cpp
unique_lock<mutex> lock(mtx, try_to_lock);
if(lock.owns_lock()) {
    cout << "Lock acquired\n";
} else {
    cout << "Failed to acquire lock\n";
}
```

---

## **4.4 `std::scoped_lock` (C++17)**

`scoped_lock` locks **multiple mutexes safely** and avoids deadlocks internally.

---

```cpp
mutex m1, m2;

void task() {
    scoped_lock lock(m1, m2); // Deadlock-safe
    cout << "Both mutexes locked safely\n";
}
```

✔ Best for multiple resource locking.

---

## **4.5 `std::shared_mutex` (C++17)**

A `shared_mutex` allows:

* **Multiple readers** at the same time (shared lock)
* **Single writer** exclusively (unique lock)

Useful in **read-heavy** workloads.

---

### **Example**

```cpp
#include <shared_mutex>

shared_mutex smtx;
int data = 0;

void reader(int id) {
    shared_lock<shared_mutex> lock(smtx);
    cout << "Reader " << id << " reads: " << data << endl;
}

void writer(int val) {
    unique_lock<shared_mutex> lock(smtx);
    data = val;
    cout << "Writer updates to " << data << endl;
}
```

---

## **4.6 `std::recursive_mutex`**

A `recursive_mutex` can be locked multiple times **by the same thread**, useful in recursive functions.

### **Example**

```cpp
recursive_mutex rmtx;

void recurse(int count) {
    if(count <= 0) return;
    rmtx.lock();
    cout << "Lock level: " << count << endl;
    recurse(count - 1);
    rmtx.unlock();
}
```

⚠ Should be used carefully because misuse may hide poor design.

---

## **4.7 `std::timed_mutex`**

A timed mutex allows timeout-based locking.

### **Example**

```cpp
timed_mutex tm;

void tryLock() {
    if (tm.try_lock_for(chrono::seconds(1))) {
        cout << "Lock acquired\n";
        tm.unlock();
    } else {
        cout << "Timeout! Couldn't lock\n";
    }
}
```

---

## ⭐ Summary of Mutex Types

| Type              | Reads Allowed | Writes Allowed | Multiple Readers? | Reentrant? |
| ----------------- | ------------- | -------------- | ----------------- | ---------- |
| `mutex`           | ✔             | ✔              | ❌                 | ❌          |
| `recursive_mutex` | ✔             | ✔              | ❌                 | ✔          |
| `shared_mutex`    | ✔             | ✔              | ✔ (shared lock)   | ❌          |
| `timed_mutex`     | ✔             | ✔              | ❌                 | ❌          |

---

## ✔ End of Chapter 4

---

Continuing in the same structured, polished textbook format.

---

# 📘 **Chapter 5 — Condition Variables**

A **Condition Variable** is a synchronization mechanism that allows one or more threads to **wait** until another thread **notifies** them that a certain condition has become true.

Condition variables enable **thread coordination**, especially when a thread must pause execution until specific shared data reaches a valid state.

They are defined in:

```cpp
#include <condition_variable>
```

---

## **5.1 Introduction to Condition Variables**

Threads often need to wait for some event before continuing. For example:

* A consumer thread must wait until the producer creates new data.
* A worker thread must wait until configuration is loaded.

A condition variable allows threads to:

* **Block without wasting CPU**
* **Automatically wake up when notified**

---

### **Key Functions**

| Function                | Purpose                                                 |
| ----------------------- | ------------------------------------------------------- |
| `wait(lock)`            | Put the thread to sleep until notified                  |
| `wait(lock, predicate)` | Same as above but only continues when condition is true |
| `notify_one()`          | Wakes one waiting thread                                |
| `notify_all()`          | Wakes all waiting threads                               |

---

## **5.2 `std::condition_variable`**

A condition variable always works with a **mutex** to ensure proper synchronization.

---

### **Basic Example**

```cpp
#include <iostream>
#include <thread>
#include <mutex>
#include <condition_variable>
using namespace std;

condition_variable cv;
mutex mtx;
bool ready = false;

void waitTask() {
    unique_lock<mutex> lock(mtx);
    cv.wait(lock, []{ return ready; }); // waits until ready == true
    cout << "Condition met! Thread continues.\n";
}

void signalTask() {
    this_thread::sleep_for(chrono::seconds(2));
    {
        lock_guard<mutex> lock(mtx);
        ready = true;
    }
    cv.notify_one(); // wake waiting thread
}

int main() {
    thread t1(waitTask);
    thread t2(signalTask);

    t1.join();
    t2.join();
}
```

---

## **5.3 The `wait()` Mechanism Explained**

When a thread calls:

```cpp
cv.wait(lock);
```

The following occurs:

1. The mutex is temporarily released.
2. The thread is placed into a waiting state.
3. When notified, the mutex is automatically re-acquired.
4. The thread resumes execution.

---

### **Predicate Form (Recommended)**

To avoid spurious wake-ups:

```cpp
cv.wait(lock, []{ return ready; });
```

If a thread wakes unexpectedly, it checks the predicate and sleeps again if needed.

---

## **5.4 Producer–Consumer Problem**

One of the most common use cases of condition variables.

---

### **Producer Adds Data → Consumer Waits and Processes**

---

#### **Full Example**

```cpp
#include <iostream>
#include <thread>
#include <mutex>
#include <condition_variable>
#include <queue>
using namespace std;

queue<int> buffer;
mutex mtx;
condition_variable cv;
const int MAX_SIZE = 5;

void producer() {
    int value = 0;
    while (value < 10) {
        unique_lock<mutex> lock(mtx);
        cv.wait(lock, [] { return buffer.size() < MAX_SIZE; });

        buffer.push(value);
        cout << "Produced: " << value << endl;
        value++;

        cv.notify_all();
    }
}

void consumer() {
    for (int i = 0; i < 10; i++) {
        unique_lock<mutex> lock(mtx);
        cv.wait(lock, [] { return !buffer.empty(); });

        int val = buffer.front();
        buffer.pop();
        cout << "Consumed: " << val << endl;

        cv.notify_all();
    }
}

int main() {
    thread t1(producer);
    thread t2(consumer);

    t1.join();
    t2.join();
}
```

---

### **Output Pattern (May vary)**

```
Produced: 0
Consumed: 0
Produced: 1
Produced: 2
Consumed: 1
Consumed: 2
...
```

---

### **Concepts Demonstrated**

| Concept                           | Used In                 |
| --------------------------------- | ----------------------- |
| Mutual exclusion                  | `mutex`                 |
| Waiting for resource availability | `cv.wait()`             |
| Resource notification             | `notify_all()`          |
| Buffer safety                     | Size checking predicate |

---

## ✔ Key Takeaways from This Chapter

* Condition variables allow threads to wait efficiently.
* They prevent CPU wastage caused by busy waiting.
* Always pair `condition_variable` with a `mutex`.
* Always use the **predicate form** of `wait()` to avoid spurious wake-ups.
* Widely used in producer–consumer systems, event handling, and task scheduling.

---

Continuing in the same structured format.

---

# 📘 **Chapter 6 — Atomics and Memory Model**

Modern processors and compilers apply aggressive optimizations, which may reorder instructions for performance. This reordering can break correctness in multithreaded programs unless carefully controlled.

The C++ memory model and atomic operations allow thread-safe manipulation of shared data **without using mutexes**, ensuring **lock-free synchronization** when possible.

---

## **6.1 What is `std::atomic`?**

`std::atomic` provides operations on shared variables that are:

* **Atomic (indivisible)**
* **Thread-safe without locks**
* **Free from race conditions**

It is defined in:

```cpp
#include <atomic>
```

---

### **Example: Using Atomic Variable**

```cpp
#include <iostream>
#include <thread>
#include <atomic>
using namespace std;

atomic<int> counter(0);

void increment() {
    for(int i = 0; i < 100000; i++) {
        counter++; // atomic operation
    }
}

int main() {
    thread t1(increment);
    thread t2(increment);

    t1.join();
    t2.join();

    cout << "Final Counter: " << counter << endl; 
}
```

Unlike normal integers, incrementing an atomic integer is guaranteed to be safe across threads.

---

## **6.2 Atomic Types and Operations**

Common atomic types:

| Type           | Example         |
| -------------- | --------------- |
| `atomic<int>`  | atomic integers |
| `atomic<bool>` | boolean flags   |
| `atomic<long>` | large numbers   |
| `atomic<T*>`   | atomic pointers |

Atomic operations supported:

* `load()` — Read value atomically
* `store()` — Write atomically
* `exchange()` — Replace and return old value
* `fetch_add()` / `fetch_sub()` — Atomic arithmetic

---

### **Example: fetch_add()**

```cpp
atomic<int> value(0);
value.fetch_add(5);   // value becomes 5
value.fetch_add(3);   // value becomes 8
```

---

## **6.3 Compare-and-Swap (CAS)**

CAS (also called Compare-Exchange) is the foundation of **lock-free algorithms**.

It checks if a value matches an expected value:

* If yes → update it
* If no → fail and retry

---

### **Example: CAS**

```cpp
atomic<int> number(10);
int expected = 10;

if (number.compare_exchange_strong(expected, 20)) {
    cout << "Value updated to 20" << endl;
} else {
    cout << "Update failed" << endl;
}
```

---

## **6.4 Atomic Flags and Spinlocks**

A **spinlock** repeatedly checks a condition instead of sleeping. Useful when lock wait time is expected to be small.

---

### **Example: Simple Spinlock Using `atomic_flag`**

```cpp
#include <atomic>
#include <thread>
#include <iostream>
using namespace std;

atomic_flag lockFlag = ATOMIC_FLAG_INIT;

void work(int id) {
    while(lockFlag.test_and_set()) { 
        // spin until lock is free
    }

    cout << "Thread " << id << " entered critical section.\n";

    lockFlag.clear(); // release lock
}

int main() {
    thread t1(work, 1);
    thread t2(work, 2);

    t1.join();
    t2.join();
}
```

✔ This avoids mutex overhead—but may waste CPU if lock contention is high.

---

## **6.5 Memory Ordering and Visibility**

Memory ordering controls **how and when** memory operations become visible to other threads.

C++ provides the following memory orders:

| Order                  | Description                      | Usage                     |
| ---------------------- | -------------------------------- | ------------------------- |
| `memory_order_relaxed` | No ordering guarantees           | High-performance counters |
| `memory_order_acquire` | Prevents reordering after load   | Reading shared data       |
| `memory_order_release` | Prevents reordering before store | Publishing results        |
| `memory_order_acq_rel` | Combination of acquire & release | Bidirectional sync        |
| `memory_order_seq_cst` | strongest ordering (default)     | Most predictable          |

---

### Example: Relaxed order

```cpp
atomic<int> counter(0);

void increment() {
    for(int i = 0; i < 1000; i++)
        counter.fetch_add(1, memory_order_relaxed);
}
```

Relaxed atomics ensure atomicity but **not ordering** across threads.

---

### Example: seq_cst (Default)

```cpp
counter++;
```

This guarantees both:

* Atomicity
* Global ordering visibility

---

## ✔ Chapter Summary

| Feature            | Atomic                                   | Mutex                  |
| ------------------ | ---------------------------------------- | ---------------------- |
| Thread Safety      | ✔ Yes                                    | ✔ Yes                  |
| Lock-free          | ✔ Yes                                    | ❌ No                   |
| Can block threads? | ❌ No                                     | ✔ Yes                  |
| Best use case      | Small fast shared data (flags, counters) | Larger data structures |

---

### When to Use Atomics?

🔧 Use atomics when:

* Working with simple data types (`bool`, integers, pointers)
* Need lock-free fast communication
* Avoiding overhead of mutexes

⛔ Avoid atomics when:

* Multiple variables must be updated together (use mutex)
* Data structure complexity increases

---

Continuing with the same structured and polished format.

---

# 📘 **Chapter 7 — Advanced Threading Concepts**

As applications scale with multiple threads, the complexity of thread interactions increases. Beyond basic thread creation and synchronization, it becomes crucial to understand deeper concurrency issues such as **deadlocks, livelocks, starvation**, and higher-level architectures like **thread pools** and **thread-safe data structures**.

This chapter focuses on these advanced topics to ensure correctness, scalability, and maintainability in multithreaded systems.

---

## **7.1 Deadlocks: Causes and Solutions**

A **deadlock** occurs when two or more threads are waiting on each other to release resources — resulting in a permanent stall.

---

### **Example of a Deadlock**

```cpp
#include <iostream>
#include <thread>
#include <mutex>
using namespace std;

mutex m1, m2;

void task1() {
    lock_guard<mutex> lock1(m1);
    this_thread::sleep_for(chrono::milliseconds(100));
    lock_guard<mutex> lock2(m2);
}

void task2() {
    lock_guard<mutex> lock1(m2);
    this_thread::sleep_for(chrono::milliseconds(100));
    lock_guard<mutex> lock2(m1);
}

int main() {
    thread t1(task1);
    thread t2(task2);
    t1.join();
    t2.join();
}
```

Here:

* Thread 1 locks `m1` and waits for `m2`
* Thread 2 locks `m2` and waits for `m1`
* Neither can proceed → **deadlock**

---

### **Strategies to Avoid Deadlocks**

| Technique            | Description                                        |
| -------------------- | -------------------------------------------------- |
| Lock ordering        | Ensure all threads acquire locks in the same order |
| Try-lock timeout     | Use `try_lock()` to avoid waiting forever          |
| Scoped locking       | Use `scoped_lock` for safe multi-mutex locking     |
| Reduce lock duration | Minimize time spent holding locks                  |

---

### ✔ Fix using Lock Ordering

```cpp
void task1() {
    lock(m1, m2);
    lock_guard<mutex> lock1(m1, adopt_lock);
    lock_guard<mutex> lock2(m2, adopt_lock);
}
```

---

### ✔ Fix using `scoped_lock` (Best Practice in C++17+)

```cpp
scoped_lock lock(m1, m2); // locks safely without deadlock
```

---

## **7.2 Livelock and Starvation**

### **Livelock**

In a livelock, threads are **not blocked**, but constantly change state in response to each other — yet **no progress is made**.

💡 Example: Two people step aside repeatedly to let each other pass.

---

### **Starvation**

Occurs when a thread is repeatedly denied access to resources because other threads monopolize them.

Common in:

* Priority scheduling
* Readers-writer scenarios where many readers block writers

---

### Preventing Starvation

* Use **fair scheduling**
* Prefer **shared_mutex** with fairness strategy
* Design balanced locking policies

---

## **7.3 Thread-Safe Data Structures**

To avoid constant locking and reduce contention, modern C++ encourages **lock-free or wait-free data structures**.

Examples include:

* Atomic counters
* Lock-free queues
* Concurrent ring buffers
* Thread-safe containers (custom implemented or via libraries)

---

### Example: Atomic Thread-Safe Counter

```cpp
#include <atomic>
atomic<int> counter{0};

void increment() {
    counter.fetch_add(1);
}
```

🔧 Suitable when multiple threads update a shared numeric value frequently.

---

### Lock-Free Queue (Concept Introduction)

A lock-free queue improves performance where high throughput and minimal blocking are required (often implemented using CAS).
Full implementation is beyond introductory level and will be covered later in examples.

---

## **7.4 Thread Pools (Introduction)**

Creating a thread for each task is expensive. A **thread pool** maintains a group of reusable worker threads that execute tasks from a shared task queue.

---

### Benefits of a Thread Pool

✔ Reduces thread creation overhead
✔ Improves throughput
✔ Avoids resource exhaustion
✔ Better CPU utilization

---

### Minimal Thread Pool Example (Conceptual)

```cpp
#include <iostream>
#include <vector>
#include <thread>
#include <queue>
#include <mutex>
#include <condition_variable>
using namespace std;

class ThreadPool {
public:
    ThreadPool(size_t size);
    ~ThreadPool();
    template<typename Func>
    void enqueue(Func task);

private:
    vector<thread> workers;
    queue<function<void()>> tasks;
    mutex mtx;
    condition_variable cv;
    bool stop = false;
};

ThreadPool::ThreadPool(size_t size) {
    for(size_t i = 0; i < size; i++) {
        workers.emplace_back([this] {
            while(true) {
                function<void()> task;
                {
                    unique_lock<mutex> lock(mtx);
                    cv.wait(lock, [this]{ return stop || !tasks.empty(); });

                    if(stop && tasks.empty()) return;
                    task = move(tasks.front());
                    tasks.pop();
                }
                task();
            }
        });
    }
}

ThreadPool::~ThreadPool() {
    {
        lock_guard<mutex> lock(mtx);
        stop = true;
    }
    cv.notify_all();
    for(auto& t : workers) t.join();
}

template<typename Func>
void ThreadPool::enqueue(Func task) {
    {
        lock_guard<mutex> lock(mtx);
        tasks.emplace(task);
    }
    cv.notify_one();
}
```

✔ This is the foundation of modern async execution systems like:

* Web servers
* Game engines
* Background task schedulers

---

---

## ✔ Chapter 7 Summary

| Concept                | Meaning                                              |
| ---------------------- | ---------------------------------------------------- |
| Deadlock               | Threads permanently wait on each other               |
| Livelock               | Threads respond but never progress                   |
| Starvation             | Thread never gets resources                          |
| Thread-safe structures | Avoid data races without locking everywhere          |
| Thread pool            | Efficient task execution model with reusable threads |

---

Continuing in the same structured format.

---

# 📘 **Chapter 8 — Asynchronous Programming**

Asynchronous programming allows tasks to execute independently without blocking the main flow of execution. In C++, asynchronous features help offload work to background threads while enabling the main thread to continue execution.

This chapter focuses on the key asynchronous abstractions introduced in **C++11**:

* `std::async`
* `std::future`
* `std::promise`
* `std::packaged_task`

These provide high-level thread management without manually handling `std::thread`.

---

## **8.1 `std::async`**

`std::async` launches a function **asynchronously** and returns a `future` representing the result.

It automatically manages threads, eliminating the need to explicitly call `join()`.

Defined in:

```cpp
#include <future>
```

---

### **Example: Using `std::async`**

```cpp
#include <iostream>
#include <future>
using namespace std;

int compute(int x) {
    return x * 2;
}

int main() {
    future<int> result = async(launch::async, compute, 10);

    cout << "Result: " << result.get() << endl;
}
```

✔ The call `result.get()` blocks until the result is ready.

---

### Launch Policies

| Policy             | Meaning                                                      |
| ------------------ | ------------------------------------------------------------ |
| `launch::async`    | Forces function to run in a separate thread                  |
| `launch::deferred` | Function is executed only when `get()` or `wait()` is called |
| Default            | Implementation decides based on optimization                 |

---

### Example with Deferred Execution

```cpp
future<int> result = async(launch::deferred, compute, 10);

cout << "Waiting..." << endl;
cout << result.get(); // compute() runs here
```

---

## **8.2 `std::future` and `std::promise`**

### `std::future`

A `future` stores a value that will be available later — usually computed in another thread.

---

### `std::promise`

A `promise` is used to assign a result that will later be retrieved through a `future`.

---

### **Example: `promise + future` for Thread Communication**

```cpp
#include <iostream>
#include <thread>
#include <future>
using namespace std;

void task(promise<int> prom) {
    this_thread::sleep_for(chrono::seconds(1));
    prom.set_value(42);
}

int main() {
    promise<int> prom;
    future<int> fut = prom.get_future();

    thread t(task, move(prom));

    cout << "Waiting for result..." << endl;
    cout << "Result: " << fut.get() << endl;

    t.join();
}
```

✔ `promise` → produces
✔ `future` → consumes

---

### Exception Transfer Example

```cpp
void worker(promise<int> prom) {
    try {
        throw runtime_error("Something went wrong");
    } catch(...) {
        prom.set_exception(current_exception());
    }
}
```

---

## **8.3 `std::packaged_task`**

A packaged task wraps a callable (function, lambda, functor) and allows obtaining its result through a `future`.

Useful for deferred or scheduled execution.

---

### Example

```cpp
#include <iostream>
#include <future>
#include <thread>
using namespace std;

int square(int x) { return x * x; }

int main() {
    packaged_task<int(int)> task(square);

    future<int> result = task.get_future();
    thread t(move(task), 5);

    t.join();
    cout << "Result: " << result.get() << endl;
}
```

---

## **8.4 Exception Handling in Async Operations**

When using `std::future`, exceptions in async tasks automatically propagate and can be handled via `future.get()`.

---

### Example

```cpp
future<int> result = async([](){
    throw runtime_error("error in async task");
});

try {
    cout << result.get();
} catch(const exception& e) {
    cout << "Caught: " << e.what();
}
```

---

---

## ✔ Chapter Summary

| Feature                           | `async` | `promise/future` | `packaged_task` |
| --------------------------------- | ------- | ---------------- | --------------- |
| Thread created automatically      | ✔       | ❌                | ❌               |
| Used for returning values         | ✔       | ✔                | ✔               |
| Supports exception propagation    | ✔       | ✔                | ✔               |
| Requires manual thread management | ❌       | ✔                | ✔               |

---

💡 Key takeaway:

> **Use `std::async` when you want automatic thread management. Use `promise`, `future`, and `packaged_task` when fine-grained control or complex threading models are needed.**

---

Continuing with the same structured format.

---

# 📘 **Chapter 9 — Parallel Algorithms (C++17 and Newer)**

Beginning with **C++17**, the Standard Template Library (STL) introduced **parallel execution policies** that allow many common algorithms to run in parallel without manually managing threads.

This simplifies multithreading by enabling high-level concurrency with minimal code changes.

---

## **9.1 Parallel Execution Policies (`<execution>`)**

To use parallel STL, include:

```cpp
#include <execution>
```

C++ provides three major execution policies:

| Policy                      | Name                  | Behavior                                             |
| --------------------------- | --------------------- | ---------------------------------------------------- |
| `std::execution::seq`       | Sequential            | Default behavior — runs single-threaded              |
| `std::execution::par`       | Parallel              | Runs using multiple threads                          |
| `std::execution::par_unseq` | Parallel + Vectorized | Allows multithreading + SIMD (may reorder execution) |

---

### ⚠ Important Notes

* Not all STL algorithms support execution policies.
* With `par_unseq`, ordering is **not guaranteed**.
* Parallel STL performance depends on:

  * CPU cores
  * Data size
  * Algorithm complexity

---

## **9.2 Parallel STL Examples**

### **Example 1: Parallel Sorting**

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <execution>
using namespace std;

int main() {
    vector<int> v = {9, 3, 5, 1, 8, 2};

    sort(execution::par, v.begin(), v.end());

    for(int x : v)
        cout << x << " ";
}
```

✔ Uses multiple threads internally.

---

### **Example 2: Parallel For-Each**

```cpp
vector<int> v(1'000'000, 1);

for_each(execution::par, v.begin(), v.end(), [](int& x){
    x *= 2;
});
```

✔ Large datasets benefit significantly.

---

### **Example 3: Parallel Reduce (instead of accumulate)**

```cpp
#include <numeric>

int sum = reduce(execution::par, v.begin(), v.end());
```

✔ `reduce()` is associative and optimized for parallel execution.

---

### **Example 4: Parallel Transform**

```cpp
vector<int> result(v.size());

transform(execution::par, v.begin(), v.end(), result.begin(),
          [](int x){ return x * 3; });
```

---

## **9.3 Performance Considerations**

Parallel STL may **not always be faster**.

---

### When Parallel STL is Great

✔ Large datasets (≥ 100k elements)
✔ CPU-bound work
✔ Associative and independent operations

---

### When Not Recommended

❌ Small collections
❌ High synchronization overhead
❌ Non-thread-safe custom functions

---

### Example Comparison

| Dataset Size          | Sequential Time | Parallel Time | Result              |
| --------------------- | --------------- | ------------- | ------------------- |
| Small (100 elements)  | Fast            | Slower        | ❌ Overhead too high |
| Medium (10k elements) | OK              | Comparable    | ⚠ Depends           |
| Large (1M+ elements)  | Slow            | Much faster   | ✔ Best case         |

---

### Thread-Safety Requirements

Functions passed to parallel algorithms **must not:**

* Modify shared global state without synchronization
* Introduce data races
* Depend on element ordering (unless using `seq`)

---

---

## ✔ Chapter Summary

Parallel STL provides a high-level multithreading approach without managing:

* Threads
* Locks
* Futures

It enables performance gains for computationally expensive workloads when used appropriately.

---

| Feature                    | Parallel STL               | Manual Threads                   |
| -------------------------- | -------------------------- | -------------------------------- |
| Ease of Use                | ⭐⭐⭐⭐⭐ (Very easy)          | ⭐⭐ (Requires management)         |
| Performance Tuning Control | ⭐⭐                         | ⭐⭐⭐⭐⭐                            |
| Best Use Case              | Arrays, numeric operations | Custom workflows, I/O operations |

---

Continuing in the same structure and tone.

---

# 📘 **Chapter 10 — Best Practices & Common Pitfalls**

Working with multithreading significantly improves performance and responsiveness—but it also introduces complexity. Poor synchronization, incorrect resource handling, or improper design can lead to subtle bugs that are hard to reproduce.

This chapter outlines best practices and common pitfalls that help build robust, efficient, and maintainable multithreaded applications.

---

## **10.1 When to Use Threads**

Multithreading is not always beneficial. Threads should be used when they provide **real value**.

---

### ✔ Good Use Cases

* Heavy CPU-bound tasks (math, simulation, rendering)
* I/O operations (networking, reading/writing disk)
* Background workers (logging, monitoring, auto-save)
* Highly parallel algorithms (image/video processing)

---

### ❌ Avoid Threads When

* Task is tiny (thread overhead > work)
* Processing is mostly sequential or dependent
* Performance gain is not measurable
* Program may run on low-core hardware (e.g., embedded systems)

---

## **10.2 Avoiding Shared Mutable State**

> The fewer shared resources you have, the fewer locks you need — and the fewer bugs you'll encounter.

---

### Recommended Strategies

| Strategy                                 | Example                                |
| ---------------------------------------- | -------------------------------------- |
| Prefer immutable data                    | Use `const` and copies                 |
| Prefer atomic types over mutex           | `atomic<int> counter`                  |
| Prefer message passing over shared state | Task queues, channels                  |
| Reduce scope of locks                    | use RAII (`lock_guard`, `unique_lock`) |

---

### Example: Safer Design with Message Passing

Instead of:

```cpp
shared_data++;
```

Prefer queue-based communication:

```cpp
taskQueue.push(data);
```

---

## **10.3 Debugging Multithreaded Applications**

Debugging multithreaded issues is challenging because failures are often:

* Intermittent
* Timing-dependent
* Non-reproducible

---

### Common Debugging Techniques

| Method                               | Usage                            |
| ------------------------------------ | -------------------------------- |
| Logging with timestamps              | Best for tracing race conditions |
| Thread naming (`pthread_setname_np`) | Makes logs readable              |
| Deadlock detection tools             | Built into IDEs and sanitizers   |
| Timeouts on locks                    | Avoids infinite waiting          |

---

### Helpful Tools

| Tool / Feature                       | Platform       | Purpose                         |
| ------------------------------------ | -------------- | ------------------------------- |
| Thread Sanitizer (TSAN)              | GCC/Clang      | Detects data races              |
| Valgrind Helgrind                    | Linux          | Detects synchronization issues  |
| Visual Studio Concurrency Visualizer | Windows        | Thread profiling                |
| GDB / LLDB breakpoints               | Cross-platform | Step through threaded execution |

---

## **10.4 Tools and Profilers**

Performance analysis tools help identify:

* Lock contention
* Thread starvation
* CPU usage patterns
* Scheduling bottlenecks

---

### Examples

| Tool               | Platform       | Notes                            |
| ------------------ | -------------- | -------------------------------- |
| Perf               | Linux          | Low-level CPU tracing            |
| Intel VTune        | Cross-platform | Advanced parallel analysis       |
| Chrome Tracing API | Any            | Used with structured log markers |
| DTrace             | macOS / BSD    | Kernel-level insights            |

---

## **10.5 Common Pitfalls and How to Avoid Them**

### ❌ Pitfall 1 — Overusing Threads

Creating too many threads causes:

* Context switching overhead
* Memory exhaustion
* Poor CPU locality

**Fix:** Use a thread pool.

---

### ❌ Pitfall 2 — Forgetting to Join or Detach Threads

This can terminate the program unexpectedly.

✔ Always check:

```cpp
if (t.joinable()) t.join();
```

---

### ❌ Pitfall 3 — Deadlocks

Avoid by:

* Lock ordering
* Using `scoped_lock`
* Minimizing lock scope

---

### ❌ Pitfall 4 — Busy Waiting / Spinlocks

Avoid CPU waste unless lock duration is predictable and short.

---

### ❌ Pitfall 5 — Mixing Atomic and Non-Atomic Access

Example of unsafe mixing:

```cpp
atomic<int> x;
int y = x; // OK
x = y + 1; // ❌ may not be atomic
```

**Fix:** Use only atomic operations when accessing shared atomic variables.

---

---

## ✔ Chapter Summary

| Recommendation              | Description                                  |
| --------------------------- | -------------------------------------------- |
| Use threads wisely          | Only when work is parallelizable             |
| Minimize shared state       | Avoid unnecessary complexity                 |
| Use modern C++ abstractions | `scoped_lock`, `async`, parallel STL         |
| Profile and debug           | Concurrency issues require dedicated tools   |
| Favor deterministic design  | Predictable behavior → fewer race conditions |

---

> 🧠 **Golden Rule of Concurrency:**
> *Simple and predictable code is better than clever and unsafe multithreaded code.*

---

Continuing with the same polished structure.

---

# 📘 **Chapter 11 — Real-World Multithreading Examples**

This chapter applies all previously learned concepts to realistic use cases. Each example demonstrates essential patterns used in production systems such as logging, task execution, data pipelines, and parallel processing.

---

## **11.1 Thread-Safe Logger System**

Logging is one of the most common multithreaded tasks. Multiple threads may write messages to a shared log file or console.

To prevent output corruption, we synchronize access using a **mutex**.

---

### **Example: Thread-Safe Logger**

```cpp
#include <iostream>
#include <fstream>
#include <mutex>
#include <thread>
using namespace std;

class Logger {
private:
    mutex mtx;
    ofstream file;
public:
    Logger() { file.open("log.txt"); }
    ~Logger() { file.close(); }

    void log(const string& msg) {
        lock_guard<mutex> lock(mtx);
        file << msg << endl;
        cout << msg << endl;
    }
};

Logger logger;

void worker(int id) {
    for(int i = 0; i < 5; i++) {
        logger.log("Thread " + to_string(id) + " message " + to_string(i));
    }
}

int main() {
    thread t1(worker, 1);
    thread t2(worker, 2);

    t1.join();
    t2.join();
}
```

✔ Prevents output interleaving
✔ Demonstrates safe shared resource access

---

## **11.2 Thread Pool Implementation (Simplified)**

A **Thread Pool** maintains a fixed set of worker threads that execute tasks from a queue. This avoids expensive thread creation for each task.

---

### **Minimal Thread Pool Example**

```cpp
#include <thread>
#include <mutex>
#include <queue>
#include <condition_variable>
#include <vector>
#include <functional>
using namespace std;

class ThreadPool {
    vector<thread> workers;
    queue<function<void()>> tasks;
    mutex mtx;
    condition_variable cv;
    bool running = true;

public:
    ThreadPool(size_t count = thread::hardware_concurrency()) {
        for(size_t i = 0; i < count; i++) {
            workers.emplace_back([this] {
                while (true) {
                    function<void()> task;
                    {
                        unique_lock<mutex> lock(mtx);
                        cv.wait(lock, [&]{ return !running || !tasks.empty(); });
                        if (!running && tasks.empty()) return;
                        task = move(tasks.front());
                        tasks.pop();
                    }
                    task();
                }
            });
        }
    }

    template<typename F>
    void enqueue(F&& func) {
        {
            lock_guard<mutex> lock(mtx);
            tasks.emplace(func);
        }
        cv.notify_one();
    }

    ~ThreadPool() {
        {
            lock_guard<mutex> lock(mtx);
            running = false;
        }
        cv.notify_all();
        for(auto& t : workers) t.join();
    }
};
```

---

### **Using the Thread Pool**

```cpp
int main() {
    ThreadPool pool(4);
    for(int i = 0; i < 10; i++) {
        pool.enqueue([i] {
            cout << "Task " << i << " running on thread " 
                 << this_thread::get_id() << endl;
        });
    }
}
```

✔ Efficient
✔ Scalable
✔ Common in real frameworks (web servers, scripting engines)

---

## **11.3 Producer–Consumer Queue (Bounded Buffer)**

This is a classic concurrency pattern using:

* `mutex`
* `condition_variable`

---

### **Example: Producer–Consumer**

```cpp
#include <queue>
#include <mutex>
#include <condition_variable>
#include <thread>
#include <iostream>
using namespace std;

queue<int> buffer;
mutex mtx;
condition_variable cv;
const int MAX = 5;

void producer() {
    for(int i = 0; i < 10; i++) {
        unique_lock<mutex> lock(mtx);
        cv.wait(lock, []{ return buffer.size() < MAX; });

        buffer.push(i);
        cout << "Produced: " << i << endl;

        cv.notify_all();
    }
}

void consumer() {
    for(int i = 0; i < 10; i++) {
        unique_lock<mutex> lock(mtx);
        cv.wait(lock, []{ return !buffer.empty(); });

        int value = buffer.front();
        buffer.pop();
        cout << "Consumed: " << value << endl;

        cv.notify_all();
    }
}

int main() {
    thread t1(producer);
    thread t2(consumer);

    t1.join();
    t2.join();
}
```

✔ Prevents buffer overflow and underflow
✔ Real-world use: messaging systems, pipelines, RTOS queues

---

## **11.4 Parallel File Processing**

Files can be processed in parallel to improve performance (e.g., indexing, hashing, compression).

---

### **Example: Parallel Line Counting**

```cpp
#include <iostream>
#include <fstream>
#include <thread>
#include <vector>
using namespace std;

void countLines(const string& filename) {
    ifstream file(filename);
    int count = 0;
    string line;

    while(getline(file, line)) count++;

    cout << filename << ": " << count << " lines" << endl;
}

int main() {
    vector<string> files = {"a.txt", "b.txt", "c.txt"};
    vector<thread> workers;

    for(const auto& f : files)
        workers.emplace_back(countLines, f);

    for(auto& t : workers)
        t.join();
}
```

✔ Demonstrates data parallelism
✔ Ideal for batch workloads

---

---

## ✔ Chapter Summary

| Example                  | Concepts Demonstrated                                 |
| ------------------------ | ----------------------------------------------------- |
| Thread-safe logger       | Mutex, RAII, shared state                             |
| Thread pool              | Task scheduling, condition variable, scalable threads |
| Producer-consumer        | Synchronization, blocking queues                      |
| Parallel file processing | Data parallelism, workload distribution               |

---

Real-world systems often combine several patterns:

> Example: A server may use a **thread pool**, handle incoming requests using a **producer-consumer queue**, and log events using a **thread-safe logger** — all running concurrently.

---

Continuing with the final structured chapter.

---

# 📘 **Chapter 12 — Interview Questions & Coding Problems**

This chapter compiles frequently asked conceptual questions, coding exercises, debugging tasks, and real industry scenarios related to multithreading in C++. It serves as a revision and interview preparation guide.

---

## **12.1 Conceptual Interview Questions**

### **Beginner-Level**

1. **What is a thread?**

   * A thread is the smallest unit of execution within a process. Multiple threads can run concurrently within the same address space.

2. **Difference between process and thread?**

   * Threads share memory and resources, whereas processes have independent memory.

3. **What is a race condition?**

   * A situation where two or more threads access shared data concurrently and the program outcome depends on timing.

4. **What is a mutex?**

   * A synchronization mechanism ensuring only one thread accesses critical code at a time.

---

### **Intermediate-Level**

5. **Difference between `join()` and `detach()`?**

   * `join()` blocks the calling thread until the target thread finishes.
   * `detach()` allows the thread to run independently in the background.

6. **What are condition variables used for?**

   * To make threads wait until certain conditions are met.

7. **Explain thread-safe and thread-unsafe code.**

   * Thread-safe code ensures data integrity when accessed by multiple threads.

8. **What is deadlock and how to prevent it?**

   * Deadlock occurs when threads wait indefinitely for each other.
     Prevention techniques include lock ordering and using `scoped_lock`.

---

### **Advanced-Level**

9. **What is Compare-And-Swap (CAS)?**

   * A lock-free atomic operation used to replace values only if they match an expected value.

10. **Explain memory ordering in atomics.**

* Controls when writes become visible to other threads (`relaxed`, `acquire-release`, `seq_cst`).

11. **Difference between `shared_mutex` and normal `mutex`?**

* `shared_mutex` allows multiple readers and one writer (reader–writer model).

12. **What is a thread pool and why is it useful?**

* A group of reusable threads to avoid thread creation overhead and improve scalability.

---

---

## **12.2 Debug-the-Output Problems**

### **Example 1: Race Condition**

```cpp
int x = 0;

void add() { x++; }

int main() {
    thread t1(add);
    thread t2(add);
    t1.join(); t2.join();
    cout << x << endl;
}
```

❓ Expected Output: `2`
💥 Actual Output: `1` or `2` (non-deterministic).

---

### **Example 2: Deadlock Risk**

```cpp
mutex m1, m2;

void task1() {
    lock_guard<mutex> l1(m1);
    lock_guard<mutex> l2(m2);
}

void task2() {
    lock_guard<mutex> l2(m2);
    lock_guard<mutex> l1(m1);
}
```

❓ What could happen?
✔ Potential **deadlock** due to inconsistent lock ordering.

---

---

## **12.3 Coding Problems**

### **Problem 1: Make the Following Code Thread-Safe**

```cpp
int counter = 0;

void increment() {
    counter++;
}
```

✔ Solution:

```cpp
mutex mtx;

void increment() {
    lock_guard<mutex> lock(mtx);
    counter++;
}
```

---

### **Problem 2: Implement a Spinlock Using `atomic_flag`**

```cpp
class SpinLock {
    atomic_flag flag = ATOMIC_FLAG_INIT;
public:
    void lock() {
        while(flag.test_and_set()) {}
    }
    void unlock() {
        flag.clear();
    }
};
```

---

### **Problem 3: Producer–Consumer Using `condition_variable`**

(Completed previously — core interview exercise.)

---

### **Problem 4: Implement a Thread-Safe Singleton**

```cpp
class Singleton {
private:
    Singleton() {}
    static atomic<Singleton*> instance;
    static mutex mtx;

public:
    static Singleton* getInstance() {
        Singleton* temp = instance.load(memory_order_relaxed);
        if(!temp) {
            lock_guard<mutex> lock(mtx);
            temp = instance.load(memory_order_relaxed);
            if(!temp) {
                temp = new Singleton();
                instance.store(temp, memory_order_release);
            }
        }
        return temp;
    }
};

atomic<Singleton*> Singleton::instance(nullptr);
mutex Singleton::mtx;
```

✔ Uses **Double-Checked Locking Pattern**.

---

---

## **12.4 Real Interview Scenarios**

### Scenario 1: UI Freezing

❓ A GUI freezes during a large computation. What do you do?

✔ Move computation to background thread, keep UI responsive.

---

### Scenario 2: Logging System Slows Application

✔ Use a thread-safe logger with queue + worker thread (async logging).

---

### Scenario 3: Scalability Problem in Server Code

✔ Replace per-request threads with a **thread pool**.

---

### Scenario 4: High Latency in Parallel Algorithm

✔ Profile thread contention and reduce synchronization overhead.

---

---

## ✔ Chapter Summary

| Category      | Skills Tested                                |
| ------------- | -------------------------------------------- |
| Concepts      | Race conditions, deadlocks, thread lifecycle |
| Coding        | Mutex, condition_variable, atomics           |
| Debugging     | Multi-threaded reasoning                     |
| System Design | Thread pools, parallel architectures         |

---

