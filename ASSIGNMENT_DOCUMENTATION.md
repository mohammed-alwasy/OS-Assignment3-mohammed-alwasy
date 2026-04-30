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

[Your answer here - 4-6 sentences with code examples]

---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:

[Your answer here - explain your implementation choices]

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:

[Your answer here - reference try-finally blocks, lock ordering, etc.]

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:

[Your answer here - explain coarse-grained vs fine-grained locking, independence of counters, concurrency implications. Show understanding of when to use each approach. 5-8 sentences expected.]

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: 

**Why they need protection**: 

**Synchronization mechanism used**: 

**Code snippet**:
```java
// Paste your implementation here
```

**Justification**: 

---

### Critical Section #2: Execution Log

**What resource**: 

**Why it needs protection**: 

**Synchronization mechanism used**: 

**Code snippet**:
```java
// Paste your implementation here
```

**Justification**: 

---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: 

**Number of permits and why**: 

**Where implemented**: 

**Code snippet**:
```java
// Paste your implementation here
```

**Effect on program behavior**: 

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
