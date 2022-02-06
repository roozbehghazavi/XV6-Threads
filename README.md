# XV6-Threads
Fall 2021 - Operating systems final project - Adding kernel threads to xv6

Overview
In this project, you'll be adding real kernel threads to xv6. Sound like fun? Well, it should. Because you are on your way to becoming a real kernel hacker. And what could be more fun than that?

Specifically, you'll add four new system calls:

int clone(void *stack)
int lock(int *l)
int unlock(int *l)
int join()
You'll also add one user-space function that wraps clone() for convenience:

int thread_create(void (*fn) (void *), void *arg)
You'll also have to modify some existing system calls, such as wait().

Note: Start with a clean kernel; no need for your new fancy address space with the stack at the bottom, for example.

Details
Each new function returns -1 on failure. The locking functions (lock and unlock) return 0 on success, and the other three (clone, join, and thread_create) return a thread ID.

Getting clone working is the first step, and will probably take the most time. This call should behave very much like fork, except that instead of copying the address space to a new page directory, clone initializes the new process so that the new process and cloned process use the same page directory. Thus, memory will be shared, and the two "processes" are really actually threads.

Although the two threads will share the same address space, they each need their own stack; the cloned process allocates space for this, probably with malloc. Suppose thread T1 is making a clone of itself, T2. T1 will first allocate a 1-page piece of page-aligned memory and pass a pointer to that memory to the clone system call. The clone system call will then copy T1's stack to that location, and update T2's stack and base registers to use the new stack. It's admittedly a bit strange to allocate T2's stack in a heap object allocated by T1 in this way, but the alternative would be to integrate with p3b (relocated stacks), and that would require you to do much more work.

The thread_create function wraps clone, and takes a function pointer and thread argument as arguments. This call should do the following:

allocate page-aligned space for the new stack
call clone, returning the thread ID to the parent
in the child, thread_create should call the function pointer, passing it the provided argument
when the provided function returns, thread_create should free the stack and call exit
Note that functions passed to thread_create should not quit by exiting; they should quit by returning. Otherwise, thread_create will not have an opportunity to free the space used for the stack.

Another new system call is int join(void), which is very similar to the already existing int wait(void) call; join waits for a thread child to finish, and wait waits for a process child to finish.

The lock and unlock calls should have the normal behavior. Any code using these calls should initialize the lock variable to 0. Internally, a thread waiting on lock should go to sleep rather than spin.

To test your code, use the TAs tests, as usual! But of course you should write your own little code snippets to test pieces as you go.

Have fun!
