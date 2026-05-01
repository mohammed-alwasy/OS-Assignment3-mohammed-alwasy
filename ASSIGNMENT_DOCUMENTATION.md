# Assignment 3 - Complete Documentation

**Student Name**: [Mohammed Baleigh Alwasy]  
**Student ID**: [445052803]  
**Date Submitted**: [Submission Date]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: [Paste your personal Gmail Google Drive link here]

**Video filename**: `[YourStudentID]_Assignment3_Synchronization.mp4`

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Entry 1 - [April 29, 2026, 08:00 PM]
**What I implemented**: 
I added a ReentrantLock to the SharedResources class to protect the shared variables (contextSwitchCount, completedProcessCount, totalWaitingTime, and the executionLog list). This ensures mutual exclusion and prevents race conditions when multiple process threads try to update these variables at the same time.

**Challenges encountered**: 
The main challenge was ensuring that the lock is always released properly. If a thread acquires the lock but an exception occurs before it releases it, it could cause a deadlock, freezing the entire simulation. Another realization was that ArrayList is not thread-safe and also needed protection.

**How I solved it**: 
I declared a single shared lock: public static final ReentrantLock lock = new ReentrantLock();. For every method modifying shared data, I used lock.lock() at the start of the critical section and placed lock.unlock() strictly inside a finally block. This guarantees the lock is released no matter what happens.

**Testing approach**: 
I ran the SchedulerSimulationSync program multiple times. I monitored the console output to ensure the simulation finishes completely without freezing (verifying no deadlocks exist) and checked the final "Synchronization Statistics" to confirm the numbers are accurate and consistent (verifying no lost updates).

**Time spent**: 
45 minutes.

---

### Entry 2 - [April 30, 2026, 10:10 AM]
**What I implemented**: 
I added a binary Semaphore with 1 permit to control concurrent CPU access. This simulates a single-core processor environment by ensuring that only one process (thread) can execute its critical section (the CPU burst) at any given time, preventing processes from overlapping during execution.

**Challenges encountered**: 
The main challenge was handling the InterruptedException that the acquire() method throws. I also needed to be absolutely sure that once a process acquires the CPU permit, it doesn't hold onto it forever if something goes wrong, which would starve all other waiting processes.

**How I solved it**: 
I declared a binary semaphore in the SharedResources class: public static final Semaphore cpuSemaphore = new Semaphore(1);. In the Process thread's run() method, I wrapped the cpuSemaphore.acquire() call in a dedicated try-catch block at the very beginning. To ensure the permit is always returned safely, I placed the cpuSemaphore.release() call inside the pre-existing finally block at the end of the execution flow.

**Testing approach**: 
I executed the simulation and carefully monitored the console output. I verified that the process execution logs appeared sequentially without any overlapping "is executing" messages. I also checked that all created processes eventually got their turn and finished successfully, confirming that no deadlocks or starvation occurred.

**Time spent**:
45 minutes. 

---

### Entry 3 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

### Entry 4 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

### Entry 5 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:

Race Condition 1: Shared Counters
The shared counter variables (such as contextSwitchCount) are affected because the increment operation is not atomic at the machine level. When multiple threads access and modify the counter concurrently, their underlying instructions can interleave unpredictably. This concurrent access causes a "lost update" behavior; for example, if two threads read the counter at value 5 simultaneously, they will both increment it locally and write back 6, losing one of the updates instead of reaching 7.

Code Example: contextSwitchCount++; // Threads A & B read 5, both write 6 -> 1 update is lost.

Race Condition 2: The Execution Log (ArrayList)
The executionLog shared list is affected because Java's ArrayList is not a thread-safe data structure. Concurrent access is a problem because the internal steps required to add an element (like checking capacity and updating the array index) can overlap between threads. This can result in incorrect behaviors such as elements overwriting each other, data corruption, or the program crashing entirely with a ConcurrentModificationException.

Code Example: executionLog.add(message); // Threads overwrite the same index or cause index out of bounds.

---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:

Difference between ReentrantLock and Semaphore:
A ReentrantLock is a mutual exclusion mechanism that is "owned" by the thread that acquires it, meaning only the thread that locked it can unlock it. It is designed to strictly protect a critical section so that only one thread can access it at a time. A Semaphore, on the other hand, is a signaling mechanism based on a counter of permits. It does not have thread ownership (one thread can acquire a permit, and a completely different thread can release it) and can allow multiple threads to access a shared resource concurrently if initialized with more than one permit.

Where I used each and why:

ReentrantLock: I used ReentrantLock to protect the shared counter variables (like contextSwitchCount) and the executionLog (ArrayList). This choice was made because modifying these variables are classic critical sections that require strict mutual exclusion to prevent race conditions, lost updates, and memory corruption (like ConcurrentModificationException).

Semaphore: I used a binary Semaphore (initialized with 1 permit) to control access to the CPU execution block inside the process thread. This choice was made to restrict the number of processes running their simulated execution to exactly one at a time. Semaphores are ideal for this type of resource pooling and synchronization, as they can easily be scaled in the future (e.g., changing the permit to 4 to simulate a quad-core CPU).

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:

The Four Necessary Conditions for Deadlock:
 a deadlock can only occur if all four of the following conditions hold simultaneously:

Mutual Exclusion: At least one resource must be held in a non-sharable mode, meaning only one process can use this resource at any given time.

Hold and Wait: A process must be holding at least one resource while simultaneously waiting to acquire additional resources that are currently held by other processes.

No Preemption: Resources cannot be forcibly taken away from a process by the system. They must be released voluntarily by the process holding them only after it finishes its task.

Circular Wait: There must be a circular chain of waiting processes, where the first process is waiting for a resource held by the second, the second is waiting for a resource held by the third, and so on, with the last process waiting for a resource held by the first.

Applying this to my code (Simulation):
In the simulation I programmed, I can clearly demonstrate the "No Preemption" condition through my implementation of the ReentrantLock and Semaphore. Once a process's thread acquires the lock to update the shared counters, or acquires the CPU permit (cpuSemaphore) to begin execution, no other thread or system component can forcibly take that lock or permit away. The resource is only released voluntarily when my process finishes its critical section and explicitly calls lock.unlock() or cpuSemaphore.release() inside the finally block.

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:

In my implementation for Task 1, I used ONE single lock (a ReentrantLock named lock) to protect all three counters, which represents a coarse-grained locking approach. I made this choice primarily for simplicity and safety, as it minimizes the complexity of the code and completely eliminates the risk of deadlocks that can occur when managing multiple locks. The core trade-off between the two approaches is the balance between simplicity and performance: coarse-grained locking is easier to implement and requires less overhead, but it creates a bottleneck because threads block each other even if they are accessing completely different variables. Conversely, fine-grained locking uses separate locks for each resource, which significantly increases code complexity and deadlock risks but maximizes system throughput. Given that the three counters in our simulation (contextSwitchCount, completedProcessCount, and totalWaitingTime) are functionally independent, a fine-grained approach would actually provide much better concurrency. Using a separate lock for each counter would allow multiple threads to update different counters simultaneously (e.g., one thread updating the context switch count while another updates the total waiting time) without unnecessarily waiting for each other, thereby fully utilizing the multithreaded environment.
---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: 
contextSwitchCount, completedProcessCount, and totalWaitingTime.

**Why they need protection**: 
These variables are shared among all concurrent process threads. Operations like incrementing (++) or adding (+=) are not atomic at the machine level; they consist of multiple steps (read the current value, modify it, and write it back). If multiple threads attempt to update these variables at the exact same time, their instructions can interleave. This causes a race condition known as a "lost update," where one thread's modification is overwritten by another, resulting in inaccurate final statistics.

**Synchronization mechanism used**: 
ReentrantLock

**Code snippet**:
```java
// Example for contextSwitchCount (same logic applied to the others)
public static void incrementContextSwitch() {
    lock.lock(); // Acquire the lock before modifying
    try {
        contextSwitchCount++; // Critical Section
    } finally {
        lock.unlock(); // Always release the lock
    }
}
```

**Justification**: 
A ReentrantLock is the perfect mechanism here because updating a shared variable requires strict mutual exclusion. The lock ensures that only one thread can read and modify the counter at any given time, completely preventing race conditions. Furthermore, by placing lock.unlock() inside a finally block, we guarantee that the resource is safely released even if an unexpected error occurs, effectively preventing deadlocks.

---

### Critical Section #2: Execution Log

**What resource**: 
The executionLog list (ArrayList<String>).

**Why it needs protection**: 
In Java, the standard ArrayList class is not thread-safe. When adding an element to an ArrayList, several internal steps occur (such as checking the array's capacity, potentially resizing it, and updating the index pointer). If multiple process threads try to log their execution messages simultaneously, these internal steps can overlap. This can lead to data loss (messages overwriting each other), null elements in the list, or the program crashing entirely by throwing a ConcurrentModificationException.

**Synchronization mechanism used**: 
ReentrantLock (using the same shared lock instance).

**Code snippet**:
```java
public static void logExecution(String message) {
    lock.lock(); // Acquire lock to protect the non-thread-safe ArrayList
    try {
        executionLog.add(message); // Critical Section
    } finally {
        lock.unlock(); // Always release the lock
    }
}
```

**Justification**: 
Since ArrayList lacks built-in synchronization, we must provide external mutual exclusion. The ReentrantLock guarantees that only one thread can access and modify the executionLog at any exact moment. By wrapping the .add() operation inside a lock() and an unlock within a finally block, we serialize access to the list, effectively preventing concurrent modification errors and ensuring that every single log message is safely and accurately recorded.

---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: 
To control concurrent access to the simulated CPU, ensuring that the execution phases (burst times) of the processes do not overlap, effectively simulating a single-core processor environment.

**Number of permits and why**: 
1 permit. Using a single permit creates a Binary Semaphore, which acts as a mutual exclusion mechanism. It ensures that only exactly one process thread can hold the permit and execute its simulated CPU burst at any given time.

**Where implemented**: 
Declared as a static variable in the SharedResources class, and utilized (acquired and released) inside the run() method of the Process class.

**Code snippet**:
```java
public void run() {
        try {
           SharedResources.cpuSemaphore.acquire(); // task 3

            if (startTime == -1) {
                startTime = System.currentTimeMillis();
            }
            
            // Increment context switch counter
            SharedResources.incrementContextSwitch();
            
            int runTime = Math.min(timeQuantum, remainingTime);
            
            String quantumBar = createProgressBar(0, 15);
            String message = "  ▶ " + name + " (Priority: " + priority + ") executing quantum [" + runTime + "ms]";
            System.out.println(Colors.BRIGHT_GREEN + message + Colors.RESET);
            
            // Log execution
            SharedResources.logExecution(name + " started quantum execution");
            
            try {
                int steps = 5;
                int stepTime = runTime / steps;
                
                for (int i = 1; i <= steps; i++) {
                    Thread.sleep(stepTime);
                    int quantumProgress = (i * 100) / steps;
                    quantumBar = createProgressBar(quantumProgress, 15);
                    System.out.print("\r  " + Colors.YELLOW + "⚡" + Colors.RESET + 
                                    " Quantum progress: " + quantumBar);
                }
                System.out.println();
                
            } catch (InterruptedException e) {
                System.out.println(Colors.RED + "\n  ✗ " + name + " was interrupted." + Colors.RESET);
            }
            
            remainingTime -= runTime;
            int overallProgress = (int) (((double)(burstTime - remainingTime) / burstTime) * 100);
            String overallProgressBar = createProgressBar(overallProgress, 20);
            
            System.out.println(Colors.YELLOW + "  ⏸ " + Colors.CYAN + name + Colors.RESET + 
                              " completed quantum " + Colors.BRIGHT_YELLOW + runTime + "ms" + Colors.RESET + 
                              " │ Overall progress: " + overallProgressBar);
            System.out.println(Colors.MAGENTA + "     Remaining time: " + remainingTime + "ms" + Colors.RESET);
            
            if (remainingTime > 0) {
                System.out.println(Colors.BLUE + "  ↻ " + Colors.CYAN + name + Colors.RESET + 
                                  " yields CPU for context switch" + Colors.RESET);
                SharedResources.logExecution(name + " yielded CPU");
            } else {
                completionTime = System.currentTimeMillis();
                long waitingTime = (completionTime - creationTime) - burstTime;
                SharedResources.addWaitingTime(waitingTime);
                SharedResources.incrementCompletedProcess();
                SharedResources.logExecution(name + " completed execution");
                System.out.println(Colors.BRIGHT_GREEN + "  ✓ " + Colors.BOLD + Colors.CYAN + name + 
                                  Colors.RESET + Colors.BRIGHT_GREEN + " finished execution!" + 
                                  Colors.RESET);
            }
            System.out.println();
            
        }catch (InterruptedException e) {   // task 3
        e.printStackTrace(); 
        }
         finally {
            // TODO #4: Release CPU semaphore here
            SharedResources.cpuSemaphore.release(); // task 3
        }
    }
```

**Effect on program behavior**: 
It serializes the execution phase of the concurrent threads. Even though the system starts all process threads at roughly the same time, the semaphore forces them to wait in a queue for the CPU permit. This prevents their execution times from overlapping, ensuring that the "is executing" log messages appear sequentially and the simulation accurately reflects a single CPU serving one process at a time without race conditions.

---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running program multiple times to verify consistent results

**Testing procedure**: 
```bash
# Commands used (run the program at least 5 times)
```

**Results**: 
(Show that running multiple times produces consistent, correct results)

**Why synchronization is necessary**: 
(Explain what race conditions COULD occur without synchronization, even if you didn't observe them. Explain which shared resources need protection and why.)

**Conclusion**: 

---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException

**Testing procedure**: 

**Results**: 

**What this proves**: 

---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)

**Expected values**: 

**Actual values**: 

**Analysis**: 

---

### Test 4: Different Scenarios
**Scenario tested**: [e.g., different time quantum, more processes, etc.]

**Purpose**: 

**Results**: 

**What I learned**: 

---

## Part 5: Reflection and Learning

### What I learned about synchronization:

[6-8 sentences about key concepts, challenges, insights]

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: 

**Example 2**: 

---

### How I would explain synchronization to others:

[Explain to someone who just finished Assignment 1 - use simple terms and analogies]

---

## Part 6: GitHub Repository Information

**Repository URL**: 

**Number of commits**: 

**Commit messages**: 
1. 
2. 
3. 
4. 

---

## Summary

**Total time spent on assignment**: 

**Key takeaways**: 
1. 
2. 
3. 

**Most challenging aspect**: 

**What I'm most proud of**: 

---

**End of Documentation**
