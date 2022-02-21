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


### Total: the std::thread library in c++ is well combined with other standard libraries, has a user-friendly interface and is easy to use.
