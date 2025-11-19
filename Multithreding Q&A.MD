Here are **senior-level “gotcha” questions** on **multithreading, locks, mutexes, semaphores, monitors, RW locks, and memory model** in C#.
These are the type of questions that expose whether someone *really* understands what's happening under the hood.

---

# ✅ **Senior-Level Gotcha Questions (with traps)**

## 🔥 **1. What is the difference between `lock(obj)` and `Monitor.Enter(obj)`—and when does `lock` NOT behave like you expect?**

**Hidden traps:**

* `lock` silently wraps `Monitor.Enter(obj, ref lockTaken)` + `Monitor.Exit`.
* `lock` does **NOT** guarantee fairness or prevent starvation.
* If `obj` is **public** or **mutable**, any external code can block your lock → deadlock.

---

## 🔥 **2. Why is locking on `this` or on a string literal dangerous?**

**Expected senior answer:**

* You expose your synchronization primitive publicly.
* Anybody can lock the same reference → deadlock.
* Interned strings are shared across AppDomain → cross-code lock collisions.

---

## 🔥 **3. Is a `SemaphoreSlim` a kernel object? Why is it faster than a `Semaphore`?**

**Traps:**

* `SemaphoreSlim` is **NOT** a kernel primitive; it uses **pure user-mode spinning + async compatibility**.
* `Semaphore` is a **kernel object** → context switch → expensive.
* `SemaphoreSlim` supports `WaitAsync`, `Semaphore` does NOT.

---

## 🔥 **4. When will a `Mutex` allow cross-process synchronization and why is this dangerous?**

**Expected answer:**

* A **named Mutex** is system-wide.
* Other processes (even malware on Windows) can open it.
* Causes:

  * unpredictable deadlocks
  * hangs from external processes
  * privilege escalation scenarios

---

## 🔥 **5. Why can `lock` cause deadlocks even with "simple" code?**

**You want to hear:**

* Thread enters a `lock` → throws exception → **lock is still released**, BUT…
* If two locks are nested and one thread takes them in different order → **classic deadlock**.

Also check if they know:

* Lock ordering
* Hierarchical locking
* Avoid locking on async code
* NEVER `await` inside lock

---

## 🔥 **6. What happens if you `await` inside a `lock`?**

**Correct answer:**

* You **cannot**: compiler error.
* BUT with `Monitor.Enter/Exit` you *can*, and it will BREAK:

  * Thread releases context
  * Not guaranteed to return to same thread
  * The lock is held while your async continuation is parked → deadlock.

---

## 🔥 **7. What is the difference between `volatile` and `Interlocked`?**

**You want:**

* `volatile` ensures *visibility* (prevent reordering)
* `Interlocked` ensures **atomicity**
* `volatile` does NOT guarantee atomic updates
* `Interlocked.Increment(ref x)` is atomic
* `x++` on a volatile field is NOT

---

## 🔥 **8. Why is `ThreadLocal<T>` considered dangerous in ASP.NET?**

Because:

* ASP.NET uses **thread pool threads**
* They get reused for other requests
* ThreadLocal sticks around → memory leaks
* Request-specific data SHOULD NOT be stored per-thread

---

## 🔥 **9. `ReaderWriterLockSlim`—why can writers starve readers and vice versa?**

Traps:

* RWLockSlim is NOT fair
* If reads are constant, writes may starve (never get lock)
* If writes are constant, read concurrency collapses
* Lock escalation logic is not automatic

---

## 🔥 **10. What is the difference between deadlock and livelock?**

**Deadlock:**

* Threads are BLOCKED forever, waiting on each other.

**Livelock:**

* Threads are **not blocked**, but keep *responding to each other* and never progress.
  Example: two threads repeatedly releasing/reacquiring locks to avoid deadlock.

---

## 🔥 **11. Why does spinlock exist and why shouldn't you use it?**

**Correct answer:**

* SpinLock spins in user mode → no context switch
* GREAT for ultra-short critical sections (<100ns)
* TERRIBLE when threads are preempted
* Consumes CPU
* Not re-entrant → deadlocks if you enter twice on the same thread

---

## 🔥 **12. Why would you use `ManualResetEventSlim` instead of `ManualResetEvent`?**

**Expected:**

* Slim uses **mostly user-mode spinning**
* Much faster when waits are short
* But:

  * Not cross-process
  * Consumes CPU while spinning
  * Should fallback to kernel wait for long waits

---

## 🔥 **13. What is the .NET Memory Model barrier around `lock`?**

Senior engineer should know:

* Enter/Exit creates full memory fences
* Prevents reordering
* Guarantees visibility to other threads
* `lock` = Acquire + Release semantics

---

## 🔥 **14. Will `Interlocked.Exchange(ref obj, newObj)` fully protect object state?**

Trick question:

* It protects only the **reference**, not the internal state of the object.
* Mutating `obj.Field` still requires separate synchronization.

---

## 🔥 **15. Why can `Parallel.ForEach` degrade to single-thread execution?**

Because:

* If CPU count = 1
* ThreadPool is saturated
* Heavy blocking inside loop
* Very small workload (overhead > work)
* Current synchronization context forces single-thread execution

---

# Want more?

I can give you:
✅ extremely tricky real interview questions
✅ code snippets with hidden deadlocks
✅ real-world scenarios (DB locks, file locks, async pipelines)
✅ diagrams of which primitives are user-mode vs kernel-mode

Just say **“give me more tricky questions”** or **“give me real code gotchas”**.
