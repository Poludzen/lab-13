# lab-13: Show parallel programming libraries
## Example 1:

### Language: C++; Library: std::thread; Multithreading in C++

#### Some information about this library;

Multithreading support was introduced in C+11. Prior to C++11, we had to use POSIX threads or p threads library in C. While this library did the job the lack of any standard language provided feature-set caused serious portability issues. C++ 11 did away with all that and gave us std::thread. The thread classes and related functions are defined in the thread header file.
#### Some Technical information about this library
std::thread is the thread class that represents a single thread in C++. To start a thread we simply need to create a new thread object and pass the executing code to be called (i.e, a callable object) into the constructor of the object. Once the object is created a new thread is launched which will execute the code specified in callable.
A callable can be either of the three:

A function pointer

A function object

A lambda expression

thread of execution is a sequence of instructions that can be executed concurrently with other such sequences in multithreading environments, while sharing a same address space.

An initialized thread object represents an active thread of execution; Such a thread object is joinable, and has a unique thread id.

A default-constructed (non-initialized) thread object is not joinable, and its thread id is common for all non-joinable threads.

A joinable thread becomes not joinable if moved from, or if either join or detach are called on them.

#### Now let's see that is easy to work with this library
#### Example 1: 
In this example we have 2 functions running in parallel. just showing easy example of work.

```c++
// thread example
#include <iostream> // std::cout
#include <thread> // std::thread 
void foo() { // do stuff... } 
void bar(int x) { // do stuff... }
int main() {
    std::thread first (foo); // spawn new thread that calls foo() 
    std::thread second (bar,0); // spawn new thread that calls bar(0)
    std::cout << "main, foo and bar now execute concurrently...\n"; // synchronize threads: 
    first.join(); // pauses until first finishes 
    second.join(); // pauses until second finishes 
    std::cout << "foo and bar completed.\n"; 
    return 0;
 }
```


##### Output of this example is:

``` 
main, foo and bar now execute concurrently... foo and bar completed
```
#### Example 2:
It launches three thread from the main function . Each thread is called using one of the callable objects specified above, they was mentioned before in readme

```c++
// CPP program to demonstrate multithreading
// using three different callables.
#include <iostream>
#include <thread>
using namespace std;
// A dummy function

void foo(int Z){
    for (int i = 0; i < Z; i++) {
        cout << "Thread using function"
               " pointer as callable\n";
    }
}  
// A callable object
class thread_obj {
public:
    void operator()(int x){
        for (int i = 0; i < x; i++)
            cout << "Thread using function"
                  " object as  callable\n";
    }
};

int main(){
    cout << "Threads 1 and 2 and 3 "
         "operating independently" << endl;
  
    // This thread is launched by using 
    // function pointer as callable
    thread th1(foo, 3);
    // This thread is launched by using
    // function object as callable
    thread th2(thread_obj(), 3);  
    // Define a Lambda Expression
    auto f = [](int x) {
        for (int i = 0; i < x; i++)
            cout << "Thread using lambda"
             " expression as callable\n";
    }; 
    // This thread is launched by using 
    // lamda expression as callable
    thread th3(f, 3);
    // Wait for the threads to finish
    // Wait for thread t1 to finish
    th1.join();
    // Wait for thread t2 to finish
    th2.join();
    // Wait for thread t3 to finish
    th3.join();
    return 0;
}
```
##### Output of this example is:
```
Threads 1 and 2 and 3 operating independently 
Thread using function pointer as callable 
Thread using lambda expression as callable 
Thread using function pointer as callable 
Thread using lambda expression as callable
Thread using function object as callable
Thread using lambda expression as callable
Thread using function pointer as callable 
Thread using function object as callable 
Thread using function object as callable
```
#### Example 3:
What about handling exceptions between threads? To pass exceptions between threads, you need to catch them in a thread function and store them somewhere in order to access them later. In this example, I had to synchronize access to the g_exceptions vector to be sure that only one thread at a time could insert a new element. To do this, I used a mutex and a lock on a mutex. Mutex - the basic element of synchronization
```c++
#include<thread>
#include<mutex>
#include<chrono>
#include<iostream>

std::mutex g_mutex; 
std::vector<std::exception_ptr> g_exceptions;
 void throw_function() {
    throw std::exception("something wrong happened"); 
}
void threadFunction() {
    try {
        throw_function();
    } 
    catch (...){ 
       std::lock_guard<std::mutex> lock(g_mutex); 
       g_exceptions.push_back(std::current_exception()); 
    } 
} 
int main() { 
    g_exceptions.clear();
    std::thread thr(threadFunction);      
    thr.join();
    for(auto &e: g_exceptions) { 
        try { 
            if(e != nullptr)           
              std::rethrow_exception(e); 
        } 
        catch (const std::exception &e) { 
            std::cout << e.what() << std::endl; 
        } 
    } 
    return 0; 
}
```
#### Example 4:
Thread Local Storage : Sometimes you need to create a variable, like a global one, but which only one thread sees. Other threads also see the variable, but for them it has its own local value. To do this, they came up with Thread Local Storage, or TLS. Among other things, TLS can be used to significantly speed up the generation of pseudorandom numbers
```c++
#include <iostream>
#include <mutex>
#include <thread>
#include <vector>

std::mutex io_mtx;
thread_local int counter = 0;
static const int MAX_COUNTER_VAL = 10;

void thread_proc(int tnum) {
    for(;;) {
        counter++;
        if(counter == MAX_COUNTER_VAL)
            break;
        {
            std::lock_guard<std::mutex> lock(io_mtx);
            std::cout << "Thread " << tnum << ": counter = " <<
                      counter << std::endl;
        }
        std::this_thread::sleep_for(std::chrono::milliseconds(10));
    }
}

int main() {
    std::vector<std::thread> threads;
    for(int i = 0; i < 10; i++) {
        std::thread thr(thread_proc, i);
        threads.emplace_back(std::move(thr));
    }

    for(auto& thr : threads) {
        thr.join();
    }

    std::cout << "Done!" << std::endl;
    return 0;
}
```
Output of this code is so big, you can try it on your own!

#### Example 5
Atomic variables: Atomic variables are often used to perform simple operations without using mutexes. For example, you need to increment a counter from multiple threads. Instead of wrapping int in std::mutex, it is more efficient to use std::atomic_int. C++ also offers the types std::atomic_char, std::atomic_bool and many others. Lock-free algorithms and data structures are also implemented on atomic variables. It is worth noting that they are very difficult to develop and debug, and not all systems work faster than similar algorithms and data structures with locks. Note the use of the hardware_concurrency procedure. It returns an estimate of the number of trades that can be executed in parallel in the current system. For example, on a machine with a quad-core processor that supports hyper threading, the procedure returns the number 8. Also, the procedure may return zero if the evaluation failed or the procedure is simply not implemented.
```с++
#include <atomic>
#include <iostream>
#include <mutex>
#include <thread>
#include <vector>

static std::atomic_int atomic_counter(0);
static const int MAX_COUNTER_VAL = 100;

std::mutex io_mtx;

void thread_proc(int tnum) {
    for(;;) {
        {
            int ctr_val = ++atomic_counter;
            if(ctr_val >= MAX_COUNTER_VAL)
                break;

            {
                std::lock_guard<std::mutex> lock(io_mtx);
                std::cout << "Thread " << tnum << ": counter = " <<
                             ctr_val << std::endl;
            }
        }
        std::this_thread::sleep_for(std::chrono::milliseconds(10));
    }
}

int main() {
    std::vector<std::thread> threads;

    int nthreads = std::thread::hardware_concurrency();
    if(nthreads == 0) nthreads = 2;

    for(int i = 0; i < nthreads; i++) {
        std::thread thr(thread_proc, i);
        threads.emplace_back(std::move(thr));
    }

    for(auto& thr : threads) {
        thr.join();
    }

    std::cout << "Done!" << std::endl;
    return 0;
}
```

### Total: the std::thread library in c++ is well combined with other standard libraries, has a user-friendly interface and is easy to use.
