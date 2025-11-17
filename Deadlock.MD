A **deadlock** occurs when **two or more threads are permanently blocked**, waiting for each other’s resources, and none of them can continue.
In simpler terms:

> **Thread A is waiting for something Thread B holds, and Thread B is waiting for something Thread A holds → both freeze forever.**

---

# ❗ **When Deadlocks Can Appear**

## **1. Nested Locks (Most Common Case)**

When two locks are taken in *different order* by different threads.

### Example:

```
Thread 1:
 lock(A)
   lock(B)

Thread 2:
 lock(B)
   lock(A)
```

➡️ **Thread 1** holds `A`, waits for `B`
➡️ **Thread 2** holds `B`, waits for `A`
➡️ **Deadlock forever**

---

## **2. Forgetting to Release a Lock**

If a lock is taken but never released:

```csharp
Monitor.Enter(lockObj);
// no Exit -> all future threads wait forever
```

This can happen because of:

* Exception thrown before `Monitor.Exit`
* Logic error — no release path
* Manual lock code instead of `lock { }`

---

## **3. Waiting for Each Other (Semaphore, Mutex, Events)**

Deadlock is not only about `lock`.

Example with `AutoResetEvent`:

```
Thread A waits for signal from Thread B
Thread B waits for signal from Thread A
```

➡️ **Both wait forever**.

Same with:

* `Semaphore.Wait()` without `Release()`
* `Mutex.WaitOne()` without `ReleaseMutex()`
* `Task.Wait()` waiting on a task that waits back on calling thread

---

## **4. Blocking Calls Inside `lock`**

If you do something that **blocks** (I/O, wait, database call) while holding a lock:

```csharp
lock(obj)
{
    var result = httpClient.GetAsync(...).Result;
}
```

Another thread might need this lock to continue the execution → **deadlock risk**.

---

## **5. UI Thread Deadlock (`.Result`, `Wait()`)**

Very common in **ASP.NET and UI apps**:

```csharp
public async Task Foo()  
{
   await Task.Delay(1000);
}

// Somewhere else:
Foo().Wait();   // ❌ can deadlock in UI
```

Because:

* UI thread waits synchronously
* Async continuation tries to resume on the UI thread
* **Both stuck**

---

## **6. Deadlock between Services / Databases**

Deadlocks occur not only in code:

### SQL example:

Two transactions update tables in different order ⇒ DB deadlock.

---

# 🧠 **4 Conditions Required for Deadlock (Theory)**

A deadlock is only possible when ALL FOUR conditions exist:

| Condition        | Meaning                                            |
| ---------------- | -------------------------------------------------- |
| Mutual exclusion | Only one thread can use resource                   |
| Hold and wait    | Thread holds one resource and waits for another    |
| No preemption    | Cannot force thread to release resource            |
| Circular wait    | Thread A waits for B, B waits for C, C waits for A |

Remove ANY ONE — no deadlock possible.

---

# ✔ How to Prevent Deadlocks

### **1. Always lock in the same order**

```csharp
lock(A)
   lock(B)
```

Never reverse order.

---

### **2. Use `lock` instead of `Monitor.Enter` manually**

Automatic exit protects from exceptions.

---

### **3. Avoid `.Result`, `.Wait()` — use `await`**

---

### **4. Keep lock scope small**

Never block inside a lock.

---

### **5. Timeouts**

```csharp
if (Monitor.TryEnter(obj, 1000))
{
   ...
}
else
{
   // lock not acquired → avoid blocking forever
}
```

---

# ⚡ Real-World Example

### ❌ BAD

```csharp
lock(orderLock)
{
    UpdateOrder();
    lock(paymentLock)
    {
        UpdatePayment();
    }
}
```

Another part of code:

```csharp
lock(paymentLock)
{
    UpdatePayment();
    lock(orderLock)
    {
        UpdateOrder();
    }
}
```

⚠ → **Classic circular deadlock**

