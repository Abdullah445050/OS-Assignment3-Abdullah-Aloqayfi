# Assignment 3 - Complete Documentation

**Student Name**: Abdullah Aloqayfi  
**Student ID**: 445050222  
**Date Submitted**: May 2 ,2026

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: https://drive.google.com/file/d/1yyrMDM9OI0W44CI3SqOWdyjJEONFWUyY/view?usp=drivesdk

**Video filename**: `445050222_Assignment3_Synchronization.mp4`

**Verification**:
- [x] Link is accessible (tested in incognito mode)
- [x] Video is 3-5 minutes long
- [x] Video shows code walkthrough and commits
- [x] Video has clear audio
- [x] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Entry 1 - May 2, 2026, 5:00 PM
**What I implemented**:
I started by setting my student ID and reviewing the scheduler simulation code. I focused on understanding how processes and threads interact and identified the shared resources inside the `SharedResources` class. 

**Challenges encountered**: 
The main challenge was understanding how race conditions occur in multithreaded programs.

**How I solved it**: 
I analyzed the TODO comments and related them to synchronization concepts such as mutual exclusion.

**Testing approach**: 
No execution yet, only reading and tracing the code.

**Time spent**: 
15 minutes.

---

### Entry 2 - May 2, 2026, 5:15 PM
**What I implemented**: 
I added the required synchronization imports (`ReentrantLock` and `Semaphore`) at the top of the file.

**Challenges encountered**: 
Making sure I use the correct Java concurrency classes.

**How I solved it**: 
Used proper import statements instead of full class paths for cleaner code.

**Testing approach**:
Compiled the code to ensure no syntax errors. 

**Time spent**: 
15 minutes.

---

### Entry 3 - May 2, 2026, 5:30 PM
**What I implemented**: 
I added a shared `ReentrantLock` and a binary `Semaphore` inside the `SharedResources` class.

**Challenges encountered**: 
Deciding where to place synchronization objects so all threads can access them.

**How I solved it**: 
Placed them inside the shared resources class as static variables.

**Testing approach**: 
Compiled again and verified everything works correctly.

**Time spent**: 
20 minutes.
---

### Entry 4 - May 2, 2026, 5:50 PM
**What I implemented**: 
I protected all shared counters and the execution log using `ReentrantLock`. I updated all methods to include `lock.lock()` and `unlock()` inside `try-finally` blocks.

**Challenges encountered**: 
Ensuring that locks are always released to avoid deadlocks.

**How I solved it**: 
Used `finally` blocks to guarantee releasing the lock.

**Testing approach**: 
Ran the program and verified that it executes without errors.

**Time spent**: 
40 minutes.

---

### Entry 5 - May 2, 2026, 6:10 PM
**What I implemented**: 
I implemented semaphore control inside the `run()` method using `acquire()` and `release()`.

**Challenges encountered**: 
Making sure the semaphore is always released even if an exception occurs.

**How I solved it**: 
Placed the release operation inside a `finally` block.

**Testing approach**: 
Ran the program multiple times and confirmed that all processes complete successfully.

**Time spent**: 
35 minutes.
---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:

The first race condition is related to the shared counter `contextSwitchCount`. Multiple process threads can call `incrementContextSwitch()` and update the same variable at the same time. Since incrementing a variable is not an atomic operation, two threads could read the same old value and write back the same new value, causing a lost update. This would make the final number of context switches incorrect.

The second race condition is related to the `executionLog` ArrayList. The log is shared by all process threads, and multiple threads may add messages to it at the same time. Since `ArrayList` is not thread-safe, concurrent modification could cause inconsistent log entries or a `ConcurrentModificationException`. Protecting it with a lock ensures that only one thread writes to the log at a time.


---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:

`ReentrantLock` is used to provide mutual exclusion for critical sections. It allows only one thread at a time to enter the protected code block. In my code, I used `ReentrantLock` to protect the shared counters and the execution log because these shared resources should not be updated by multiple threads simultaneously.

A `Semaphore` controls access to a limited number of permits. In my code, I used a binary semaphore with one permit to control CPU access. This means only one process thread can execute its CPU quantum at a time. The lock protects data consistency, while the semaphore controls access to the simulated CPU resource.

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:

Deadlock occurs when threads are waiting for each other forever and none of them can continue execution. One prevention technique is to always release locks in a `finally` block so the lock is released even if an exception occurs. I applied this technique in all methods that use `ReentrantLock`.

A second prevention technique is to avoid holding resources longer than necessary. In my code, each lock is held only for a small critical section, such as incrementing a counter or adding one log message. I also released the semaphore inside a `finally` block to ensure that the CPU permit is returned after execution. This prevents a situation where a thread keeps the CPU permit forever.

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:

I used one shared `ReentrantLock` for the three counters and the execution log. This is a coarse-grained locking approach. I made this choice because the assignment is focused on correctness and clear synchronization, and using one lock makes the code simpler and easier to verify.

The advantage of coarse-grained locking is that it is simple and reduces the chance of mistakes such as forgetting to unlock or using the wrong lock. The disadvantage is that it reduces concurrency because only one thread can update any protected shared resource at a time. Fine-grained locking, where each counter has its own lock, would provide better concurrency because the counters are independent. However, it also makes the code more complex. For this assignment, one lock is a reasonable design because it is clear, safe, and easy to explain.

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**:  
`contextSwitchCount`, `completedProcessCount`, and `totalWaitingTime`

**Why they need protection**:  
These variables are shared by multiple process threads. Without synchronization, simultaneous updates could cause lost updates and incorrect final statistics.

**Synchronization mechanism used**:  
I used `ReentrantLock`.

**Code snippet**:
```java
public static void incrementContextSwitch() {
    lock.lock();
    try {
        contextSwitchCount++;
    } finally {
        lock.unlock();
    }
}

public static void incrementCompletedProcess() {
    lock.lock();
    try {
        completedProcessCount++;
    } finally {
        lock.unlock();
    }
}

public static void addWaitingTime(long time) {
    lock.lock();
    try {
        totalWaitingTime += time;
    } finally {
        lock.unlock();
    }
}
```
   

**Justification**: 
The lock ensures mutual exclusion. Only one thread can update the shared counters at a time, which prevents race conditions and keeps the final statistics correct.

---

### Critical Section #2: Execution Log

**What resource**: 
executionLog, which is an ArrayList<String>.

**Why it needs protection**: 
ArrayList is not thread-safe. If multiple threads add log messages at the same time, the list may become inconsistent or cause runtime errors.

**Synchronization mechanism used**: 
I used ReentrantLock.

**Code snippet**:
```java
public static void logExecution(String message) {
    lock.lock();
    try {
        executionLog.add(message);
    } finally {
        lock.unlock();
    }
}


**Justification**: 
This protects the log from concurrent writes. It ensures that each log entry is added safely and completely before another thread can modify the list.

---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: 
The semaphore controls access to the simulated CPU. It ensures that only one process thread executes its CPU quantum at a time.

**Number of permits and why**: 
I used one permit because the simulation represents a single CPU. A binary semaphore with one permit allows only one process to use the CPU at a time.

**Where implemented**: 
Inside the run() method of the Process class.

**Code snippet**:
```java
try {
    SharedResources.cpuSemaphore.acquire();
} catch (InterruptedException e) {
    Thread.currentThread().interrupt();
    return;
}

try {
    // process execution
} finally {
    SharedResources.cpuSemaphore.release();
}

**Effect on program behavior**: 
The semaphore prevents multiple process threads from executing their CPU quantum simultaneously. This makes the CPU scheduling simulation more realistic and controlled.

---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running the program multiple times to verify that the program completes successfully and produces correct final statistics.

**Testing procedure**:
```bash
javac SchedulerSimulationSync.java
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync

# Commands used (run the program at least 5 times)
```

**Results**: 
The program completed successfully. The output showed 20 processes, a time quantum of 3000ms, 41 total context switches, and 20 completed processes.

**Why synchronization is necessary**: 
Synchronization is necessary because the program uses multiple threads and shared resources. Without locks, counters such as contextSwitchCount and completedProcessCount could be updated incorrectly. Without protecting executionLog, multiple threads could modify the ArrayList at the same time. The semaphore is also necessary to control access to the simulated CPU.

**Conclusion**: 

The consistency test shows that the synchronization mechanisms allow the program to run correctly and complete all processes.

---

### Test 2: Exception Testing
**What I tested**:
 I tested whether the program runs without ConcurrentModificationException or synchronization-related runtime errors.

**Testing procedure**: 
I ran the program after protecting executionLog with ReentrantLock. I observed the full execution until the final statistics were printed.

**Results**: 
The program completed successfully and printed the final process summary and synchronization statistics. No ConcurrentModificationException occurred.

**What this proves**: 
This proves that protecting the shared ArrayList prevents unsafe concurrent modifications and improves the reliability of the program.

---

### Test 3: Correctness Verification
**What I tested**:
 I verified the correctness of the final values printed by the program.

**Expected values**: 
Since the student ID is fixed at 445050222, the random generator produces a deterministic simulation setup. The expected number of processes is 20, and the time quantum is 3000ms. The completed process count should equal the number of processes.

**Actual values**: 
The output showed:

Processes: 20
Time Quantum: 3000ms
Total Context Switches: 41
Total Completed Processes: 20

**Analysis**: 
The completed process count matches the number of created processes. This means every process finished execution. The context switch count is also reasonable because several processes required more than one CPU quantum due to their burst times being larger than the time quantum.

---

### Test 4: Different Scenarios
**Scenario tested**: 
I tested the program behavior with the fixed student ID and observed processes with different burst times and priorities.

**Purpose**: 
The purpose was to verify that the scheduler can handle processes that finish in one quantum and processes that require multiple quantums.

**Results**: 
Some processes finished immediately when their burst time was less than or equal to the time quantum. Other processes yielded the CPU and were added back to the ready queue until their remaining time became zero.

**What I learned**: 
I learned that synchronization does not change the scheduling logic itself. Instead, it makes the shared updates safe while the Round Robin behavior continues normally.

---

## Part 5: Reflection and Learning

### What I learned about synchronization:

I learned that synchronization is necessary when multiple threads share and update the same data. Even simple operations like incrementing a counter can cause race conditions because they are not atomic. I also learned that ReentrantLock is useful for protecting critical sections and maintaining mutual exclusion. Semaphores are useful when we want to control access to a limited resource, such as a simulated CPU. The try-finally structure is important because it guarantees that locks and semaphores are released. This assignment also helped me understand the difference between data protection and resource control. Overall, I learned that correct multithreaded programs require careful planning, not just writing threads.

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: 
Banking systems need synchronization when multiple transactions update the same account balance. Without synchronization, two withdrawals or deposits could produce an incorrect final balance. 

**Example 2**: 
Operating systems need synchronization when multiple processes access shared resources such as printers, files, memory tables, or CPU scheduling queues.

---

### How I would explain synchronization to others:

Synchronization is like allowing only one person at a time to write on a shared whiteboard. If many people write at the same time, the information can become mixed or incorrect. In programming, threads can also interfere with each other when they use the same variable or list. A lock works like a key: one thread takes the key, updates the shared data, and then gives the key back. A semaphore is like a limited number of passes that allow access to a resource. In this assignment, I used locks to protect shared data and a semaphore to control CPU access.

---

## Part 6: GitHub Repository Information

**Repository URL**: 
https://github.com/Abdullah445050/OS-Assignment3-Abdullah-Aloqayfi

**Number of commits**: 
5

**Commit messages**: 
1. Set my student ID 
2. Add synchronization imports
3. Add lock and semaphore for shared resources
4. Protect shared counters and execution log
5. Control CPU access using semaphore
---

## Summary

**Total time spent on assignment**: 
About 4 hours

**Key takeaways**: 
1. Race conditions happen when multiple threads access shared data without protection.
2. ReentrantLock provides mutual exclusion for critical sections.
3. Semaphore controls access to limited resources such as the simulated CPU.

**Most challenging aspect**: 
The most challenging aspect was identifying which parts of the code were critical sections and making sure every lock or semaphore permit was released correctly.

**What I'm most proud of**: 
I am most proud that the program runs successfully, completes all processes, and prints correct synchronization statistics after applying locks and semaphores.

---

**End of Documentation**
