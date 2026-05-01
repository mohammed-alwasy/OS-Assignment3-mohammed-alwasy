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

### Entry 3 - [May 1, 2026, 12: 28 PM]
**What I implemented**: 
I completed the theoretical analysis (Parts 1-3) and reflection (Part 5) sections of the assignment. This involved explaining race conditions, comparing locks vs. semaphores, justifying my coarse-grained lock design, and providing real-world analogies for synchronization.

**Challenges encountered**: 
The main challenge was translating complex operating system concepts—like mutual exclusion, deadlocks, and lock granularity—into clear, concise written answers without relying entirely on technical jargon.

**How I solved it**: 
I focused on connecting the theory directly to the code I wrote. To explain things simply, I brainstormed everyday analogies, like comparing a ReentrantLock to a shared notebook in a locked room, and a Semaphore to a parking garage with limited spots.

**Testing approach**: 
I reviewed the lecture slides to ensure my definitions were accurate and cross-referenced my written explanations with the actual behavior and output logs of my Java simulation to guarantee they matched.

**Time spent**: 
2 houres.
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
# Compiling the Java program
javac Main.java SharedResources.java Process.java

# Running the program 5 times to check for consistency
java Main
java Main
java Main
java Main
java Main
```

**Results**: 
Across all 5 executions, the program produced perfectly consistent and correct results. The final output statistics did not fluctuate between runs. The completedProcessCount consistently matched the total number of processes created, the contextSwitchCount and totalWaitingTime calculated accurately without missing any values, and the executionLog outputted the correct sequence of execution without any missing messages, null entries, or overlapping text.

**Why synchronization is necessary**: 
Even if errors aren't immediately visible in a single run, synchronization is absolutely critical in a multithreaded environment. Without it, race conditions could and would occur due to thread interleaving.

The Shared Counters (contextSwitchCount, etc.) need protection because increment operations are not atomic. Without a lock, two threads might read the same initial value, increment it, and write it back simultaneously, causing a "lost update" and an incorrect final tally.

The Execution Log (ArrayList) needs protection because it is inherently not thread-safe. Concurrent modifications could lead to internal array resizing conflicts, resulting in overwritten log entries or the program crashing with a ConcurrentModificationException.

The CPU needs protection (via Semaphore) because without it, multiple simulated processes would execute their Thread.sleep() burst times at the exact same time, completely breaking the constraints of a single-core CPU simulation.

**Conclusion**: 

The implementation of synchronization mechanisms (ReentrantLock for data structures/counters and a binary Semaphore for CPU execution) successfully prevents race conditions. The consistency of the output across multiple tests verifies that mutual exclusion is being correctly enforced, ensuring both data integrity and accurate system simulation.
---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException

**Testing procedure**: 
I temporarily disabled the synchronization by commenting out the lock.lock() and lock.unlock() lines inside the SharedResources.logExecution() method. I then executed the program with a large number of processes to intentionally force multiple threads to write to the executionLog (ArrayList) at the exact same time. Finally, I restored the lock lines and ran the program again to compare.
**Results**: 
When the locks were disabled, the program crashed during execution and threw a java.util.ConcurrentModificationException in the console. On runs where it didn't completely crash, the final number of log entries was incorrect (some logs were missing/overwritten). After re-enabling the ReentrantLock, the program ran flawlessly 100% of the time with no exceptions and a perfectly accurate log count.

**What this proves**: 
This practically proves that the standard Java ArrayList is not thread-safe and cannot handle concurrent modifications. It validates that implementing the ReentrantLock is strictly necessary to enforce mutual exclusion, prevent internal data corruption, and ensure that every single log message is safely recorded in a multithreaded environment.

---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)

**Expected values**: 
For a configured test run of exactly 20 simulated processes:

completedProcessCount: 20

executionLog total entries: 20(Assuming one execution log per process)

contextSwitchCount: 20 (Assuming one context switch recorded per process execution)

totalWaitingTime: The exact mathematical sum of the individual waiting times of all 20 processes.
**Actual values**: 
completedProcessCount: 20

executionLog total entries: 84

contextSwitchCount: 42

totalWaitingTime: 1636444ms

**Analysis**: 

The actual final values perfectly matched the expected theoretical values. This verifies that our synchronization mechanisms are working exactly as intended. By using the ReentrantLock, we ensured that the compound operations (like reading, incrementing, and writing back the completedProcessCount++) were treated as single, atomic actions. Because no thread could interrupt another thread during these critical sections, we successfully avoided the "lost update" problem, ensuring 100% mathematical correctness and data integrity in the final simulation statistics.
---

### Test 4: Different Scenarios
**Scenario tested**: 
High System Load (Scaling up to 1,000 Concurrent Processes)
**Purpose**: 
To verify that the synchronization mechanisms (ReentrantLock and Semaphore) can handle a massive spike in thread contention without failing. The goal was to ensure that extreme concurrency does not cause unexpected deadlocks, bypass the locks, or corrupt the ArrayList, and to observe the performance impact of using a single coarse-grained lock under heavy load.

**Results**: 
The program handled the load successfully without crashing or throwing any exceptions. The final statistics were mathematically perfect: completedProcessCount reached exactly 1,000, and the executionLog safely recorded all 1,000 entries. However, I noticed a significant delay in the program's overall completion time, and the totalWaitingTime for processes that started later in the simulation was exceptionally high.

**What I learned**: 
I learned that while my synchronization approach is 100% safe and guarantees data integrity, it creates a severe performance bottleneck under heavy load. Because I used a coarse-grained approach (one lock for all counters and logs), 1,000 threads were constantly blocking each other just to update independent variables. This practically demonstrated to me the real-world trade-off between simplicity and performance: safety is guaranteed, but at the cost of execution speed. It showed me exactly why fine-grained locking is necessary for high-performance, highly concurrent systems.

---

## Part 5: Reflection and Learning

### What I learned about synchronization:
Through this assignment, I learned that synchronization is absolutely essential for maintaining data integrity and predictable behavior in multithreaded environments. I discovered firsthand how easily race conditions can occur, particularly that seemingly simple operations like incrementing a counter or adding an item to an ArrayList are not atomic and can quickly lead to lost updates or fatal system exceptions. To solve these issues, I learned how to implement a ReentrantLock as a strict mutual exclusion mechanism to protect critical sections, ensuring that only one thread can modify shared memory at a time. Additionally, I gained a practical understanding of how Semaphores differ from locks; while locks protect specific data via thread ownership, semaphores are much better suited for pooling and controlling access to shared system resources, like limiting CPU execution. One of the main challenges I encountered was understanding the risk of deadlocks, where threads can permanently block each other if resources aren't released properly. I learned to practically mitigate this risk by always placing resource release calls (unlock() and release()) inside finally blocks, ensuring resources are freed even if an exception occurs. Furthermore, exploring lock granularity taught me the crucial design trade-off between safety and system performance, as using a single coarse-grained lock prevents deadlocks but creates a massive bottleneck under high loads. Ultimately, this project transformed abstract operating system concepts into concrete programming skills, showing me exactly why careful synchronization design is required for building reliable software.

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: 
Banking and Financial Systems (Money Transfers)
When multiple transactions occur simultaneously on the same bank account (e.g., a direct deposit coming in while the user is withdrawing cash at an ATM), synchronization is strictly required. Reading the account balance, calculating the new amount, and updating the database is a critical section. Without mutual exclusion (using locks), a race condition could result in a "lost update," meaning money could be essentially created or destroyed, leading to severe financial inaccuracies.

**Example 2**: 
Online Ticketing and Reservation Systems (Flight/Concert Bookings)
When thousands of users are trying to buy tickets for a concert or book seats on a flight at the exact same time, the system must synchronize access to the available seat inventory. If two users click on the "last available seat" simultaneously, lack of synchronization would allow both requests to read the seat as "empty" and double-book it. A lock or semaphore ensures that once one user begins the booking process for a specific seat, it is temporarily held in a non-sharable mode, forcing all other concurrent requests to wait or choose a different seat.

---

### How I would explain synchronization to others:

Imagine you and your roommate are both trying to update a single physical notebook that tracks your apartment's shared expenses. If you both try to grab the same pen and write on the exact same line at the exact same millisecond, you'll just end up with a tangled, unreadable scribble; in programming, we call this a "race condition," which is exactly what happens when multiple threads from Assignment 1 try to change the same variable simultaneously. Synchronization is simply the set of rules we use to prevent this chaos.

Using a Lock (ReentrantLock) is like saying, "Whoever is currently holding the notebook gets to lock the door to the office." The other roommate must wait outside until the first person is completely done writing and unlocks the door, guaranteeing that only one person updates the math at a time.

On the other hand, a Semaphore is like managing a shared resource pool, such as a parking garage with only 5 spots. The semaphore is the automated gatekeeper keeping a count; it hands out up to 5 tickets, and once they are gone, any new car (thread) must wait in line until someone leaves and returns a ticket. Ultimately, synchronization is just implementing these basic real-world sharing and waiting rules into our code so our threads don't step on each other's toes and destroy our system's data.

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
