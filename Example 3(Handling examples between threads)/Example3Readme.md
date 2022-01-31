What about handling exceptions between threads?
To pass exceptions between threads, you need to catch them in a thread function and store them somewhere in order to access them later.

In this example, I had to synchronize access to the g_exceptions vector to be sure that only one thread at a time could insert a new element. To do this, I used a mutex and a lock on a mutex. Mutex - the basic element of synchronization
