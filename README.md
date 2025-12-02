## 📚 **C++ Multithreading Index**

| **S. No.** | **Topic**                                                     | **Description**                                                            |
| ---------- | ------------------------------------------------------------- | -------------------------------------------------------------------------- |
| 1          | [Basics](/multithreading/basics.md)                           | How to create and run threads using `std::thread`.                         |
| 2          | [Thread Function Types](/multithreading/thread-types.md)      | Using functions, lambdas, functors, and member functions as thread tasks.  |
| 3          | [join vs detach](/multithreading/join-detach.md)              | Difference between `join()` and `detach()` methods.                        |
| 4          | [Race Condition](/multithreading/race-condition.md)           | What is a race condition and how to prevent it.                            |
| 5          | [Mutex](/multithreading/mutex.md)                             | Use `std::mutex` to prevent concurrent access to shared resources.         |
| 6          | [Lock Guard](/multithreading/lock-guard.md)                   | Scoped locking using `std::lock_guard`.                                    |
| 7          | [Unique Lock](/multithreading/unique-lock.md)                 | More flexible locking mechanism than lock\_guard.                          |
| 8          | [Atomic Variables](/multithreading/atomic.md)                 | Using `std::atomic` for lock-free thread safety.                           |
| 9          | [Condition Variable](/multithreading/condition.md)            | Thread communication using `std::condition_variable`.                      |
| 10         | [Producer-Consumer](/multithreading/producer-consumer.md)     | Solve classic producer-consumer problem using queue, mutex, and condition. |
| 11         | [Deadlock](/multithreading/deadlock.md)                       | Causes and prevention techniques for deadlock.                             |
| 12         | [Thread Pool (Manual)](/multithreading/threadpool.md)         | Manually create a basic thread pool in C++.                                |
| 13         | [std::async and std::future](/multithreading/async-future.md) | Run tasks asynchronously and get results using futures.                    |
| 14         | [Parallel Algorithms (C++17)](/multithreading/parallel.md)    | Using STL algorithms in parallel with execution policies.                  |

---

#### **1. Introduction to Multithreading**

* 1.1 What is Multithreading?
* 1.2 Process vs Thread
* 1.3 Benefits and Challenges of Multithreading
* 1.4 Types of Parallelism (Data vs Task Parallelism)

---

#### **2. C++11 Thread Support Library**

* 2.1 Overview of `<thread>` Header
* 2.2 Creating and Starting Threads
* 2.3 Passing Arguments to Threads
* 2.4 Thread Lifecycle and `join()` vs `detach()`

---

#### **3. Thread Management**

* 3.1 Checking Thread Joinable State (`joinable()`)
* 3.2 Hardware Concurrency (`hardware_concurrency()`)
* 3.3 Thread IDs (`get_id()`)
* 3.4 Thread Synchronization Challenges (Race Conditions, Deadlock)

---

#### **4. Synchronization Mechanisms**

* 4.1 `std::mutex`
* 4.2 `std::lock_guard`
* 4.3 `std::unique_lock`
* 4.4 `std::scoped_lock` (C++17)
* 4.5 `std::shared_mutex` (C++17)
* 4.6 Recursive Mutex (`recursive_mutex`)
* 4.7 `std::timed_mutex`

---

#### **5. Condition Variables**

* 5.1 Introduction to Condition Variables
* 5.2 `std::condition_variable`
* 5.3 `wait()`, `notify_one()`, `notify_all()`
* 5.4 Producer-Consumer Problem

---

#### **6. Atomics and Memory Model**

* 6.1 What is `std::atomic`?
* 6.2 Atomic Types and Operations
* 6.3 Compare-and-Swap (CAS)
* 6.4 Atomic Flags and Spinlocks
* 6.5 Memory Ordering and Visibility (Relaxed, Release-Acquire, Sequential Consistency)

---

#### **7. Advanced Threading Concepts**

* 7.1 Deadlocks: Causes and Solutions
* 7.2 Livelock and Starvation
* 7.3 Thread-Safe Data Structures
* 7.4 Thread Pools (Introduction)

---

#### **8. Asynchronous Programming**

* 8.1 `std::async`
* 8.2 `std::future` and `std::promise`
* 8.3 `packaged_task`
* 8.4 Exception Handling in Async Operations

---

#### **9. Parallel Algorithms (C++17 and newer)**

* 9.1 Parallel Execution Policies (`std::execution`)
* 9.2 Parallel STL Examples (Sort, Reduce, For_each)
* 9.3 Performance Considerations

---

#### **10. Best Practices & Common Pitfalls**

* 10.1 When to Use Threads
* 10.2 Avoiding Shared Mutable State
* 10.3 Debugging Multithreaded Applications
* 10.4 Tools and Profilers

---

#### **11. Real-World Examples**

* 11.1 Logger System with Thread Safety
* 11.2 Thread Pool Implementation
* 11.3 Producer-Consumer Queue
* 11.4 Parallel File Processing


---

# C++ Multithreading Concepts

---

### Threads
In every application there is a default thread, which is main(); inside main() we create other threads.
>"A thread is also known as a lightweight process. The idea is to achieve parallelism by dividing a process into multiple threads."

**Exp.**
1. The browser has multiple tabs that can be different threads.
2. MS Word must be using multiple threads, one thread to format the text and another thread to process inputs (spell checker). 
3. Visual Studio Code editor would be using threading for auto-completing the code (IntelliSense).

---

### A. Multithreading in C++
Multithreading allows concurrent execution of multiple tasks to improve performance and responsiveness.
>Multithreading means running multiple parts of a program at the same time — like doing more than one task together, using threads.

>Note: If we create multiple threads to the same time it doesn't gaurantee which one will start first.

>Like a cook boiling rice while chopping vegetables — doing multiple tasks at once to save time.
That’s multithreading.(जैसे एक रसोइया चावल उबालते समय सब्ज़ी काटता है — एक साथ कई काम करता है।
यही है मल्टीथ्रेडिंग।)

**Ways to Create Threads:**
1. Function Pointers
2. Lambda Expressions
3. Functors (Function Objects)
4. Non-static Member Functions
5. Static Member Functions
---

### 1. Function Pointers
This is very basic form of thread creation
```cpp
#include <iostream>
#include <thread>
using namespace std;

void fun(int x){
    while(x-->0){
    cout<<x<<endl;
    }
}
int main(){
thread t1(fun, 10);
t1.join();
return 0;
}
```
```txt
9
8
7
6
5
4
3
2
1
0
```

```cpp
#include <iostream>
#include <thread>

void printMessage() {
    cout << "Hello from thread!" << endl;
}

int main() {
    thread t(printMessage);
    t.join();
    return 0;
}
```
```
Hello from thread!
```
---

### 2. Lambda Expressions
Lambda expression allows us to define anonymous (एनोनिमस) function objects (functors) which can either be used inline or passed as an argument.
>Lambda expression हमें गुमनाम (anonymous) function objects या functors परिभाषित करने की अनुमति देता है, जिन्हें या तो inline उपयोग किया जा सकता है या फिर एक argument के रूप में पास किया जा सकता है।
```cpp
#include <iostream>
#include <thread>
using namespace std;

int main() {
    // we can directly inject lambda at thread creation time.
    
    auto fun=[](int x){
        while(x-->0){
            cout<<x<<endl;
        }
    };
    thread t(fun, 10);
    t.join();
    return 0;
}
```
```txt
9
8
7
6
5
4
3
2
1
0
```
---

### 3. Functors (Function Objects)
A C++ functor (function object) is a class or struct object that can be called like a function.
>Functor, जिसे function object भी कहा जाता है, एक ऐसा object होता है जिसे ऐसे कॉल किया जा सकता है जैसे वह कोई function हो। यह class का object होता है जिसमें operator() को overload किया गया होता है, जिससे उसे function की तरह व्यवहार करने की क्षमता मिलती है।
```cpp
#include <iostream>
#include <thread>
using namespace std;

class Base{
    public:
    void operator () (int x){
       while(x-->0){
        cout<<x<<endl;
        } 
    }
};

int main(){
thread t1(Base(), 10);
t1.join();
return 0;
}
```
```txt
9
8
7
6
5
4
3
2
1
0
```
---

### 4. Non-static Member Functions
A non-static member function is a function that is declared in a member specification of a class without a static or friend specifier.
>एक non-static member function वह फ़ंक्शन होता है जिसे किसी क्लास की member specification में static या friend specifier के बिना घोषित किया जाता है। यह फ़ंक्शन केवल क्लास के किसी object (instance) के माध्यम से ही access किया जा सकता है, क्योंकि यह उस object की state (data members) पर काम करता है।
```cpp
#include <iostream>
#include <thread>
using namespace std;

class Base {
public:
    void run(int x) {
        while (x-- > 0) {
            cout << x << endl;
        }
    }
};

int main() {
    Base b;
    thread t1(&Base::run, &b, 10); // Correct syntax
    t1.join();
    return 0;
}
```
```txt
9
8
7
6
5
4
3
2
1
0
```

---

### 5. Static Member Functions
A static member function in C++ is a function that belongs to a class rather than any specific object of the class. It can be called using the class name and does not require an instance of the class.
>C++ में एक static member function ऐसा फ़ंक्शन होता है जो किसी क्लास के किसी विशेष object की बजाय पूरी क्लास से संबंधित होता है। इसे class name के द्वारा कॉल किया जा सकता है और इसे कॉल करने के लिए class का कोई object (instance) बनाना आवश्यक नहीं होता।
```cpp
#include <iostream>
#include <thread>
using namespace std;

class Base {
public:
    static void run(int x) { // Made run() static
        while (x-- > 0) {
            cout << x << endl;
        }
    }
};

int main() {
    thread t1(&Base::run, 10); // No need to pass an instance
    t1.join();
    return 0;
}
```
```txt
9
8
7
6
5
4
3
2
1
0
```
---

### B. Thread Management

- **`join()`**: Waits for the thread to finish execution. join() use for join the process with main().
  >Note: Double t.join() is not allow, It showing en error "terminate called after throwing an instance of ' system_error."
```cpp
#include <iostream>
#include <thread>
#include <chrono> // Required for  chrono::seconds
using namespace std;

// Function to be executed by the thread
void run(int count) {
    while (count-- > 0) {
        cout<<"Hello"<<endl;
        this_thread::sleep_for(chrono::seconds(5)); // Pause for 1 second in each iteration
    }
}

int main() {
    thread t1(run, 10);   // Create a thread t1 that runs the 'run' function with an argument of 10
    cout << "main() " << endl;  // Print message from the main thread
    t1.join(); // Wait for t1 to complete execution
    cout << "main() after " << endl;  // Print message after t1 has finished
    return 0;
}
```
```txt
main() 
Hello
Hello
Hello
Hello
Hello
Hello
Hello
Hello
Hello
Hello
main() after 
```

- **`detach()`**: Allows the thread to run independently.
  >Note: Double t.detach() is not allow, It showing en error "terminate called after throwing an instance of ' system_error"
```cpp
#include <iostream>
#include <thread>
#include <chrono> // Required for  chrono::seconds
using namespace std;

// Function to be executed by the thread
void run(int count) {
    while (count--> 0) {
        cout<<"Hello"<<endl;
        this_thread::sleep_for(chrono::seconds(5)); // Pause for 1 second in each iteration
        //cout<<"Thread finished"<<endl;
        
    }
}

int main() {
    thread t1(run, 10);   // Create a thread t1 that runs the 'run' function with an argument of 10
    cout << "main() " << endl;  // Print message from the main thread
    t1.detach(); // Wait for t1 to complete execution
    //t1.detach();
    cout << "main() after " << endl;  // Print message after t1 has finished
    return 0;
}
```
```txt
main() 
Hello
main() after

If we print cout<<"Thread finished"<<endl; then output
main() 
main() after

Beacause we detached our thread t1. we are not saying that wait for complete so we got above result.
```

- **`joinable()`**: Checks if a thread is still joinable.

**Example:**
```cpp
thread t(printMessage);
if (t.joinable()) {
    t.join();
}
```

---
```cpp
#include <iostream>
#include <thread>
#include <chrono> // Required for  chrono::seconds
using namespace std;

// Function to be executed by the thread
void run(int count) {
    while (count--> 0) {
        cout<<"Hello"<<endl;
         // Pause for 1 second in each iteration
        //cout<<"Thread finished"<<endl;
        
    }
}

int main() {
    thread t1(run, 10);   // Create a thread t1 that runs the 'run' function with an argument of 10
    cout << "main() " << endl;  // Print message from the main thread
    t1.detach(); // Wait for t1 to complete execution
    //t1.detach();
    cout << "main() after " << endl;  // Print message after t1 has finished
    this_thread::sleep_for(chrono::seconds(5));
    return 0;
}
```
```txt
main() 
main() after 
Hello
Hello
Hello
Hello
Hello
Hello
Hello
Hello
Hello
Hello
```
---

### **Notes on `this_thread` Functions in C++**  

#### **1. `this_thread::sleep_for()`**  
- **Purpose:** Pauses the current thread for a specified duration.  
- **Usage:**  
  ```cpp
  this_thread::sleep_for(chrono::seconds(2)); // Sleep for 2 seconds
  ```  
- **Use Case:** Useful for implementing delays or throttling execution.  

#### **2. `this_thread::sleep_until()`**  
- **Purpose:** Pauses the current thread until a specified point in time.  
- **Usage:**  
  ```cpp
  auto wake_up_time = chrono::system_clock::now() + chrono::minutes(1);
  this_thread::sleep_until(wake_up_time); // Sleep until the specified time
  ```  
- **Use Case:** Ideal for scheduling tasks at a specific future time.  

#### **3. `this_thread::yield()`**  
- **Purpose:** Suggests to the OS that the current thread should pause and allow another thread to run.  
- **Usage:**  
  ```cpp
  this_thread::yield();
  ```  
- **Use Case:** Helps improve CPU efficiency in multithreading by preventing busy waiting.  

#### **4. `this_thread::get_id()`**  
- **Purpose:** Retrieves the unique ID of the currently executing thread.  
- **Usage:**  
  ```cpp
  thread::id this_id = this_thread::get_id();
  cout << "Thread ID: " << this_id << endl;
  ```  
- **Use Case:** Useful for debugging and tracking thread execution.  

---

### **Mutexes and RAII Wrappers in C++**  

#### **1. Mutex Classes in C++ (`<mutex>` and `<shared_mutex>`)**  
The C++ Standard Library provides multiple types of mutexes for thread synchronization:  

- **`mutex`** – Basic mutex for mutual exclusion. Only one thread can lock it at a time.  
- **`recursive_mutex`** – Allows the same thread to lock the mutex multiple times without causing a deadlock. Useful for recursive functions.
  >यह एक ही thread को एक mutex को कई बार लॉक करने की अनुमति देता है बिना deadlock उत्पन्न किए। यह विशेष रूप से recursive functions (पुनरावृत्तीय फ़ंक्शनों) के लिए उपयोगी होता है।
- **`shared_mutex`** – Supports both **exclusive** (write) and **shared** (read) locks, allowing multiple threads to read concurrently while ensuring exclusive access for writes.
  >यह exclusive (write) और shared (read) दोनों प्रकार के locks को सपोर्ट करता है, जिससे कई threads एक साथ read (पढ़) कर सकते हैं, लेकिन write (लिखने) के लिए केवल एक thread को exclusive access (विशेष पहुंच) मिलती है।
  
#### **2. RAII Wrappers for Mutexes**  
RAII (Resource Acquisition Is Initialization) wrappers help automatically manage the locking and unlocking of mutexes:  

- **`unique_lock`** – A flexible RAII wrapper for exclusive locking of `mutex`.  
  - Allows **deferred locking, ownership transfer, and timed locking**.  
- **`shared_lock`** – A RAII wrapper for **shared (read-only) locking** with `shared_mutex`.  

#### **Key Benefits of Using RAII Wrappers**  
- **Automatic unlocking** when the lock object goes out of scope.  
- **Exception safety** – prevents resource leaks even if an exception occurs.  
- **Better flexibility** compared to manually using `lock()` and `unlock()`. 

---

### **1. `mutex`** 

A **mutex (mutual exclusion)** is used to prevent Race Conditions by allowing only one thread to access a critical section at a time.

**Types of Mutexes:**
- `mutex`
- `timed_mutex`
- `recursive_mutex`

**Example:**
```cpp
mutex mtx;
void sharedFunction() {
    lock_guard< mutex> lock(mtx);
    // Critical section
}
```
### **Race Condition**  

A **race condition** occurs when multiple threads access and modify shared data simultaneously, leading to unpredictable behavior. This happens when proper synchronization mechanisms (like mutexes) are not used. 
>"Race condition is a situation where two or more threads/ process happend to change a common data at the same time."
>Race condition एक ऐसी स्थिति है जहाँ दो या अधिक थ्रेड्स/प्रोसेस एक ही सामान्य डेटा को एक साथ बदलने की कोशिश करते हैं।

```cpp
#include <iostream>
#include <thread>

using namespace std;

int counter = 0; // Shared resource

void increment() {
    for (int i = 0; i < 100000; i++) {
        counter++; // Race condition occurs here
    }
}

int main() {
    thread t1(increment);
    thread t2(increment);

    t1.join();
    t2.join();

    cout << "Final Counter Value: " << counter << endl; // Unexpected output due to race condition
    return 0;
}
```
```txt
Final Counter Value: 149951
```
### **Expected vs. Actual Output**
- **Expected:** `counter` should be `200000` (100000 increments per thread).  
- **Actual:** The result varies each time due to **race conditions**, often producing a number **less than 200000** because both threads modify `counter` without synchronization.

---

### **Fixing Race Condition using ` mutex`**
To prevent race conditions, use a **mutex (`mutex`)** to ensure that only one thread modifies `counter` at a time.

```cpp
#include <iostream>
#include <thread>
#include <mutex>

using namespace std;

int counter = 0; 
mutex mtx; // Mutex to protect shared resource

void increment() {
    for (int i = 0; i < 100000; i++) {
        mtx.lock();   // Lock before modifying shared data
        counter++;    // Critical section
        mtx.unlock(); // Unlock after modification
    }
}

int main() {
    thread t1(increment);
    thread t2(increment);

    t1.join();
    t2.join();

    cout << "Final Counter Value: " << counter << endl; // Always 200000
    return 0;
}
```
```txt
Final Counter Value: 200000
```
---

### **Key Takeaways**
   - **Race Condition Definition:**  Occurs when multiple threads access and modify shared data concurrently, leading to unpredictable results.     
   - **Why it Happens:**  CPU switches between threads at unpredictable times, causing lost updates to shared variables.  
   - **Solution:**  Use **`mutex`** to lock critical sections and prevent concurrent modification., Alternative: **`atomic<int>`** can also be used for simple integer operations.
>**Notes:** _"If there is a race condition then we have protect it, and protected section is called critical section/ region. We use lock, unlock to avoid race condition."_
>यदि race condition होती है, तो हमें उसे सुरक्षित करना होता है, और उस सुरक्षित भाग को critical section/region कहा जाता है। हम lock और unlock का उपयोग करके race condition से बचते हैं।
      
---
### **2. `recursive_mutex`**  
A `recursive_mutex` allows the **same thread** to lock the mutex **multiple times** without causing a deadlock. This is useful in **recursive functions**, where a function calls itself and needs to acquire the same lock multiple times.  

#### **Example: ` recursive_mutex`**
```cpp
#include <iostream>
#include <thread>
#include <mutex>

recursive_mutex rec_mutex;

void recursive_function(int count) {
    if (count <= 0) return;

    rec_mutex.lock(); // Lock the mutex
    cout << "Lock acquired for count: " << count << endl;
    
    recursive_function(count - 1); // Recursive call

    rec_mutex.unlock(); // Unlock the mutex
    cout << "Lock released for count: " << count << endl;
}

int main() {
    thread t1(recursive_function, 3);
    t1.join();
    return 0;
}
```

#### **Output:**
```
Lock acquired for count: 3
Lock acquired for count: 2
Lock acquired for count: 1
Lock released for count: 1
Lock released for count: 2
Lock released for count: 3
```

#### **Why Use ` recursive_mutex`?**
- Without `recursive_mutex`, calling `lock()` multiple times within the same thread **causes a deadlock**.
- `recursive_mutex` allows a thread to **re-enter a locked mutex** multiple times safely.

---

### **3. ` shared_mutex`**
A `shared_mutex` allows **multiple threads to read** the shared resource **simultaneously**, but **only one thread** can **write** at a time.  
- Multiple readers (shared lock) are allowed.
- Writers (exclusive lock) block other threads.

#### **Example: ` shared_mutex`**
```cpp
#include <iostream>
#include <thread>
#include <shared_mutex>

 shared_mutex shared_mtx;
int shared_resource = 0;

void reader(int id) {
     shared_lock< shared_mutex> lock(shared_mtx); // Shared (read) lock
     cout << "Reader " << id << " read value: " << shared_resource <<  endl;
}

void writer(int value) {
     unique_lock< shared_mutex> lock(shared_mtx); // Exclusive (write) lock
    shared_resource = value;
     cout << "Writer updated value to: " << shared_resource <<  endl;
}

int main() {
     thread r1(reader, 1);
     thread r2(reader, 2);
     thread w(writer, 42);

    r1.join();
    r2.join();
    w.join();

    return 0;
}
```

#### **Possible Output:**
```
Reader 1 read value: 0
Reader 2 read value: 0
Writer updated value to: 42
```

#### **Why Use ` shared_mutex`?**
- Allows **multiple readers** at the same time, improving performance.
- Ensures **only one writer** at a time to maintain data integrity.

---
### **Key Differences Between ` mutex`, ` recursive_mutex`, and ` shared_mutex`**

| Feature                | `mutex`                  | `recursive_mutex`          | `shared_mutex`               |
|------------------------|-----------------------------|--------------------------------|------------------------------------|
| **Purpose**            | Basic mutual exclusion      | Allows the same thread to lock multiple times | Supports multiple readers but one writer |
| **Locking Mechanism**  | Single lock per thread      | Multiple locks by the same thread allowed | Shared (read) & Exclusive (write) locks |
| **Reentrancy**         | **Not allowed** (deadlock if locked again by same thread) | **Allowed** (same thread can lock multiple times) | **Not applicable** |
| **Usage Scenario**     | Simple thread synchronization | Recursive functions needing multiple locks | Reader-writer synchronization |
| **Performance**        | Moderate                    | Slightly slower due to multiple locks | Best for read-heavy workloads |
| **Allows Multiple Readers?** | No (only one thread can access) | No (only one thread can access) | Yes (multiple threads can read simultaneously) |
| **Allows Multiple Writers?** | No                    | No                            | No (only one writer at a time) |
| **Header File**        | `<mutex>`                   | `<mutex>`                      | `<shared_mutex>` |
| **Use Case Example**   | Thread-safe resource access | Recursive function locking | Optimizing read-heavy applications |

---

### **Understanding ` unique_lock` and ` shared_lock` in C++**  
C++ provides **RAII (Resource Acquisition Is Initialization)** wrappers to manage mutex locking efficiently.  

---

## **1. ` unique_lock`**
- A **flexible RAII wrapper** for ` mutex`.  
- Provides **exclusive** access to a shared resource.  
- More **feature-rich** than ` lock_guard` (supports deferred locking, try-locking, and timed locking).  

### **Example: Using ` unique_lock` for Exclusive Locking**
```cpp
#include <iostream>
#include <thread>
#include <mutex>

 mutex mtx; // Standard mutex
int counter = 0; // Shared resource

void increment() {
     unique_lock< mutex> lock(mtx); // Exclusive lock
    counter++;  
     cout << "Thread " <<  this_thread::get_id() << " incremented counter to " << counter <<  endl;
    // Lock is automatically released when lock goes out of scope
}

int main() {
     thread t1(increment);
     thread t2(increment);

    t1.join();
    t2.join();

     cout << "Final Counter Value: " << counter <<  endl;
    return 0;
}
```
### **Key Features of ` unique_lock`**
- **Locks automatically** when created.
- **Unlocks automatically** when out of scope.
- Supports:
  - **Deferred locking**: ` unique_lock< mutex> lock(mtx,  defer_lock);`
  - **Try-locking**: `lock.try_lock()`
  - **Timed locking**: `lock.try_lock_for( chrono::milliseconds(100));`

---

## **2. ` shared_lock`**
- Works with ` shared_mutex` (from `<shared_mutex>` header).  
- Allows **multiple threads** to read a shared resource **simultaneously**.  
- Prevents multiple threads from writing at the same time.  

### **Example: Using ` shared_lock` for Shared Read Access**
```cpp
#include <iostream>
#include <thread>
#include <shared_mutex>

 shared_mutex sharedMtx;
int sharedData = 100;

void reader(int id) {
     shared_lock< shared_mutex> lock(sharedMtx); // Shared read lock
     cout << "Reader " << id << " read value: " << sharedData <<  endl;
}

void writer(int value) {
     unique_lock< shared_mutex> lock(sharedMtx); // Exclusive write lock
    sharedData = value;
     cout << "Writer updated value to: " << sharedData <<  endl;
}

int main() {
     thread r1(reader, 1);
     thread r2(reader, 2);
     thread w(writer, 500);

    r1.join();
    r2.join();
    w.join();

    return 0;
}
```

### **Key Features of ` shared_lock`**
- **Multiple threads can acquire a shared lock simultaneously.**
- **Writers get exclusive access** while blocking readers.
- **Optimized for read-heavy operations** like caching or logging.

---

## **Key Differences Between ` unique_lock` and ` shared_lock`**
| Feature          | ` unique_lock`        | ` shared_lock`          |
|-----------------|-------------------------|---------------------------|
| **Lock Type**    | Exclusive (write) lock  | Shared (read) lock       |
| **Allows Multiple Threads?** | No (only one writer) | Yes (multiple readers) |
| **Mutex Type**   | ` mutex` or ` shared_mutex` | Only ` shared_mutex` |
| **Use Case**     | When modifying shared data | When reading shared data |

---

### **Understanding `lock()`, `try_lock()`, and `operator bool()` in C++**  

C++ provides **mutex-related functions** for managing thread synchronization efficiently.  

### **1. `lock()`**
- Blocks the calling thread **until it acquires the lock**.  
- Ensures **exclusive access** to a shared resource.  
- If another thread already holds the lock, `lock()` **waits**.  

#### **Example: Using `lock()`**
```cpp
#include <iostream>
#include <thread>
#include <mutex>

 mutex mtx;

void task(int id) {
    mtx.lock(); // Acquire lock
     cout << "Thread " << id << " is executing...\n";
     this_thread::sleep_for( chrono::seconds(1)); // Simulate work
    mtx.unlock(); // Release lock
}

int main() {
     thread t1(task, 1);
     thread t2(task, 2);

    t1.join();
    t2.join();

    return 0;
}
```
#### **Key Points**
  - `lock()` ensures only **one thread** executes the critical section at a time.
  - **Blocking function**—the thread waits until it gets the lock.  

---

### **2. `try_lock()`**
- Tries to acquire the lock **without blocking**.  
- If the lock is **available**, it **succeeds** and returns `true`.  
- If the lock is **already taken**, it **fails immediately** and returns `false`.  

#### **Example: Using `try_lock()`**
```cpp
#include <iostream>
#include <thread>
#include <mutex>

 mutex mtx;

void task(int id) {
    if (mtx.try_lock()) { // Try acquiring the lock
         cout << "Thread " << id << " acquired the lock!\n";
         this_thread::sleep_for( chrono::seconds(1));
        mtx.unlock(); // Release the lock
    } else {
         cout << "Thread " << id << " could not acquire the lock!\n";
    }
}

int main() {
     thread t1(task, 1);
     thread t2(task, 2);

    t1.join();
    t2.join();

    return 0;
}
```
#### **Possible Output**
```
Thread 1 acquired the lock!
Thread 2 could not acquire the lock!
```
#### **Key Points**
    `try_lock()` **does not block**—it returns immediately.  
    Use for **non-blocking** operations where waiting is not desirable.  

---

### **3. `operator bool()`**
- Checks **whether a ` unique_lock` object holds a lock**.  
- Returns `true` if the lock is acquired, otherwise `false`.  
- Helps **verify if a lock is successfully acquired**.  

#### **Example: Using `operator bool()`**
```cpp
#include <iostream>
#include <thread>
#include <mutex>

 mutex mtx;

void task() {
     unique_lock< mutex> lock(mtx,  try_to_lock); // Try acquiring the lock
    if (lock) { // Checks if the lock is held
         cout << "Lock acquired by thread " <<  this_thread::get_id() << "\n";
    } else {
         cout << "Thread " <<  this_thread::get_id() << " could not acquire lock\n";
    }
}

int main() {
     thread t1(task);
     thread t2(task);

    t1.join();
    t2.join();

    return 0;
}
```
#### **Possible Output**
```
Lock acquired by thread 140235678539520
Thread 140235678539528 could not acquire lock
```
#### **Key Points**
    `operator bool()` is used with ` unique_lock` to check if locking was successful.  
    Helps in **conditional execution** based on lock acquisition.  

---

## **Key Differences Between `lock()`, `try_lock()`, and `operator bool()`**
| Feature         | `lock()` | `try_lock()` | `operator bool()` |
|---------------|---------|------------|----------------|
| **Blocking?** | Yes | No (returns immediately) | No (only checks lock status) |
| **Return Type** | `void` | `bool` (`true` if successful) | `bool` (`true` if lock is held) |
| **Fails If Locked?** | No (waits for lock) | Yes (returns `false`) | Yes (returns `false`) |
| **Use Case** | When waiting for lock is okay | When non-blocking execution is needed | When checking lock ownership |

---

## **When to Use?**
   1. **Use `lock()`** when **you need to wait** for the lock (critical section).  
   2. **Use `try_lock()`** when **you don’t want to block** execution.  
   3. **Use `operator bool()`** to **check if a lock is held** inside ` unique_lock`.  

---

### 4. Condition Variable

A **condition variable** is used to synchronize threads based on certain conditions.

**Example:**
```cpp
 condition_variable cv;
 mutex mtx;
bool ready = false;

void waitFunction() {
     unique_lock< mutex> lock(mtx);
    cv.wait(lock, [] { return ready; });
     cout << "Condition met!" <<  endl;
}

void signalFunction() {
     lock_guard< mutex> lock(mtx);
    ready = true;
    cv.notify_one();
}
```

---

### 5. Deadlock Prevention or Avoiding

Deadlock occurs when two or more threads are waiting for each other to release resources.

**Solutions:**
1. Lock ordering
2. Try-lock mechanisms
3. Using ` lock()`

**Example:**
```cpp
 lock(mtx1, mtx2);
 lock_guard< mutex> lock1(mtx1,  adopt_lock);
 lock_guard< mutex> lock2(mtx2,  adopt_lock);
```

**The following example will lead to deadlocks**
```cpp
 mutex m1, m2, m3;
void threadA() {
// INTENTIONALLY BUGGY
 unique_lock l1{m1}, l2{m2}, l3{m3};
}
void threadB() {
// INTENTIONALLY BUGGY
 unique_lock l3{m3}, l2{m2}, l1{m1};
}
```
**Possible deadlock scenario**
   - threadA() acquires locks on m1 and m2
   - threadB() acquires lock on m3
   - threadA() waits for threadB() to release m3
   - threadB() waits for threadA() to release m2

**Example:**
**Deadlocks can be avoided by always locking mutexes in a globally consistent order**
  - Ensures that one thread always “wins”
  - Maintaining a globally consistent locking order requires considerable developer discipline
  - Maintaining a globally consistent locking order may not be possible at all
    
```cpp
 mutex m1, m2, m3;
void threadA() {
// OK, will not deadlock
 unique_lock l1{m1}, l2{m2}, l3{m3};
}
void threadB() {
// OK, will not deadlock
 unique_lock l1{m1}, l2{m2}, l3{m3};
}
```

**Example:**
**Sometimes it is not possible to guarantee a globally consistent order**
  - The  scoped_lock RAII wrapper can be used to safely lock any number of mutexes
  - Employs a deadlock-avoidance algorithm if required
  - Generally quite inefficient in comparison to  unique_lock
  - Should only be used as a last resort!
    
```cpp
 mutex m1, m2, m3;
void threadA() {
// OK, will not deadlock
 scoped_lock l{m1, m2, m3};
}
void threadB() {
// OK, will not deadlock
 scoped_lock l{m3, m2, m1};
}
```
---

### 6. Thread Synchronization

Ensures that threads work together in a coordinated manner to avoid race conditions.

**Common Synchronization Mechanisms:**
- Mutexes
- Condition Variables
- Barriers

---

### 7. ` async` and Task-Based Parallelism

` async` allows execution of tasks asynchronously and returns a ` future` to retrieve the result later.

**Example:**
```cpp
#include <future>

int computeValue(int x) {
    return x * 2;
}

int main() {
     future<int> result =  async( launch::async, computeValue, 10);
     cout << "Result: " << result.get() <<  endl;
    return 0;
}
```

---

## **Understanding Binary Semaphore in C++**

### **What is a Binary Semaphore?**
A **Binary Semaphore** is a synchronization mechanism that can have **only two states**:
1. **0 (Locked/Unavailable)**
2. **1 (Unlocked/Available)**  

- It is similar to a **mutex** but allows signaling between threads.  
- Used for **resource control** when only **one thread** should access a shared resource at a time.  
- Unlike a mutex, **semaphores can be signaled (released) by a different thread** than the one that acquired them.  
>ये semaphore एक दरवाज़े जैसा है जो शुरुआत में बंद है (count = 0). जब main thread release() करता है, दरवाज़ा खुलता है और worker thread अंदर जा सकता है (यानि आगे बढ़ सकता है).
---

## **Example: Binary Semaphore Using ` counting_semaphore` (C++20)**
  - **` counting_semaphore<1>` works like a binary semaphore** in C++20.
  - **One thread waits until the resource is available, and another thread signals (releases) it.**

```cpp
#include <iostream>
#include <thread>
#include <semaphore> // C++20: Required for  binary_semaphore

 counting_semaphore<1> semaphore(1); // Binary semaphore (1 means available)

void task(int id) {
     cout << "Thread " << id << " waiting for resource...\n";
    semaphore.acquire(); // Wait (P operation)
    
     cout << "Thread " << id << " acquired the resource!\n";
     this_thread::sleep_for( chrono::seconds(2)); // Simulate work

     cout << "Thread " << id << " releasing the resource...\n";
    semaphore.release(); // Signal (V operation)
}

int main() {
     thread t1(task, 1);
     thread t2(task, 2);

    t1.join();
    t2.join();

    return 0;
}
```

---

## **Expected Output**
```
Thread 1 waiting for resource...
Thread 1 acquired the resource!
Thread 2 waiting for resource...
Thread 1 releasing the resource...
Thread 2 acquired the resource!
Thread 2 releasing the resource...
```

---

## **Explanation**
1. **Thread 1 starts and acquires the semaphore** (resource is locked).
2. **Thread 2 waits** since the semaphore is already acquired.
3. **Thread 1 releases the semaphore** after work is done.
4. **Thread 2 acquires the semaphore** and executes.
5. **Thread 2 releases the semaphore** after execution.

---

## **Key Differences Between Binary Semaphore & Mutex**
| Feature         | **Binary Semaphore** | **Mutex** |
|---------------|------------------|--------|
| **Value Range** | 0 or 1 | Only locked/unlocked |
| **Ownership** | Can be released by a different thread | Must be released by the same thread |
| **Blocking?** | Yes, if unavailable | Yes, if locked |
| **Use Case** | Resource synchronization | Mutual exclusion |

---

## **When to Use a Binary Semaphore?**
✔ **Use ` binary_semaphore` when signaling is needed between threads** (e.g., producer-consumer).  
✔ **Use ` mutex` when a thread must exclusively access a resource** and release it itself.  

---

### 8. ` promise` and ` future`

 `std::promise` and `std::future` are part of the **thread communication mechanisms** introduced in **C++11**. They allow **one thread to produce a result** and **another thread to consume it**, making it easy to pass data safely between threads.

**Example:**
```cpp
#include <future>

void setValue( promise<int>& prom) {
    prom.set_value(42);
}

int main() {
     promise<int> prom;
     future<int> fut = prom.get_future();
     thread t(setValue,  ref(prom));
     cout << "Value: " << fut.get() <<  endl;
    t.join();
    return 0;
}
```

---

## **What is `std::promise` and `std::future` in C++ Multithreading?**

| Concept        | Description                                                             |
| -------------- | ----------------------------------------------------------------------- |
| `std::promise` | Used to **set a value** that will be **retrieved by another thread**.   |
| `std::future`  | Used by the **consumer thread** to **wait for and retrieve the value**. |

---

## **How they work together**

1. A `promise` is created in the main (or producer) thread.
2. The associated `future` is obtained using `promise.get_future()`.
3. The `future` is passed to one thread (consumer), and the `promise` is kept in the other (producer).
4. The producer sets the value via `promise.set_value(...)`.
5. The consumer waits and gets the value via `future.get()`.

---

## **Basic Example**

```cpp
#include <iostream>
#include <thread>
#include <future>

using namespace std;

// Worker function
void worker(std::promise<int> prom) {
    cout << "Worker thread is doing some work...\n";
    std::this_thread::sleep_for(std::chrono::seconds(2)); // Simulate work
    prom.set_value(42); // Set result
}

int main() {
    std::promise<int> prom;          // Create promise
    std::future<int> fut = prom.get_future(); // Get associated future

    std::thread t(worker, std::move(prom)); // Pass promise to worker thread

    cout << "Main thread is waiting for result...\n";
    int result = fut.get(); // Wait for result from worker
    cout << "Result received: " << result << endl;

    t.join(); // Join the thread
    return 0;
}
```

---

### Output:

```
Main thread is waiting for result...
Worker thread is doing some work...
Result received: 42
```

---

## When to Use `promise` & `future`

| Use Case                         | Example                                            |
| -------------------------------- | -------------------------------------------------- |
| Thread needs to return a value   | Background computation result                      |
| Need async error reporting       | Use `promise.set_exception(...)`                   |
| Coordination between threads     | One waits until the other signals completion       |
| Replacement for shared variables | Safer than using raw shared variables with mutexes |

---

## Exception Handling Example

```cpp
void throwingWorker(std::promise<int> prom) {
    try {
        throw std::runtime_error("Something went wrong!");
    } catch (...) {
        prom.set_exception(std::current_exception()); // Pass exception to future
    }
}

int main() {
    std::promise<int> prom;
    std::future<int> fut = prom.get_future();

    std::thread t(throwingWorker, std::move(prom));

    try {
        int result = fut.get(); // Will throw here
        cout << "Result: " << result << endl;
    } catch (const std::exception& e) {
        cout << "Caught exception from thread: " << e.what() << endl;
    }

    t.join();
}
```

---

---

## **License**
This project is licensed under the MIT License.

---

Happy Coding!


