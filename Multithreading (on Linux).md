## Why?
Performance.

Pi4's ARM Corectex-A72 has four cores, so 4x speedup?

**Slowdown factors:**
- Amdahl's Law: $Speedup = \frac{1}{(1-p)+p/N}$ (not everything can run in parallel)
- Cost of synchronization
- Non-uniform work (different tasks can be longer)
- Shared resources (memory cannot be read in parallel)

**Note:** using NEON and using the 64-bit OS will get you a ~4x and ~2x speedup.

## POSIX threads example
```cpp
#include <pthread.h>

void* perform_work(void* userdata)
{
	// do shit
}

void main()
{
	pthread_t thread;
	pthread_create(&thread, NULL, perform_work, &whatever);
	
	// thread is doing work
	//...

	// wait for thread to complete
	pthread_join(thread, NULL);
}
```

Caveats:
- Linux only
- The thread does one job and takes a while
- The thread does not depend on other threads
- Spawning threads is very expensive
- Inter-thread communication is hard

## Synchronization
Naïve shit example:
```cpp
while (workRemaining > 0)
{
	int id = workRemaining;
	DoJob(id); // expensive function
	workRemaining--;
}
```
This loop cannot run in parallel because multiple jobs with the same ID might run before the decrement is reached.

Even operations like `--` can go wrong in threads!

### Atomics
Atomics guarantee to execute without being interrupted:
```cpp
int __sync_add_and_fetch(int* ptr, int value);
int __sync_fetch_and_add(int* ptr, int value);
...
```

The previous example would become something like this:
```cpp
while (workRemaining > 0)
{
	// fetch the value before subtracting from it
	int id = __sync_fetch_and_sub(&workRemaining, 1);
	DoJob(id); // expensive function
}
```

## Mutexes
A mutex is a variable that you can claim by locking it:
```cpp
pthread_mutex_t mutex;
int myVariable;

// in the thread:
pthread_mutex_lock(&mutex);
do_stuff_with(&myVariable);
pthread_mutex_unlock(&mutex);
```

# Worker threads
Most big games use worker threads, and the amount of worker threads matches the amount of cores.
This architecture works well with many **independent**, small tasks.

The worker threads just perform (non-uniform) jobs and are never killed:
1. Wait for 'go' signal (main thread sets up jobs to be performed)
2. Claim one job from the job pile
3. If more jobs are left, repeat step 2
4. Raise a 'done' signal
5. Repeat

Threads are not bound to a specific core by default, so you should do that yourself:
```cpp
// inside the thread
cpu_set_t cpuSet;
CPU_ZERO(&cpuSet);
CPU_SET(threadId, &cpuSet);
pthread_setaffinity_np(pthread_self(), sizeof(cpuSet), &cpuSet);
```

See https://github.com/bmartini/worker-threads for a full example.

**Note:** OpenGL doesn't like threads, so keep rendering on the main thread.

