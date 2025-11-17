**Dirty read** — транзакция видит *нефиксированные* изменения другой транзакции (то, что может откатиться).
**Последствия:** читаешь данные, которых «как будто нет».

**Non-repeatable read** — в рамках одной транзакции повторный `SELECT` по *той же строке* возвращает другие данные, потому что другая транзакция изменила/закоммитила их.
**Последствия:** одно и то же чтение даёт разные результаты.

**Phantom read** — в рамках одной транзакции повторный `SELECT` по *диапазону/условию* возвращает *новые строки*, добавленные другой транзакцией.
**Последствия:** «появляются фантомы» — новые строки, которых раньше не было.


Here’s a **senior-level summary** of isolation levels and a clear table:

---

### **Isolation Levels in SQL**

Isolation levels define **how/when the changes made by one transaction are visible to others**, balancing **consistency vs concurrency**.

1. **Read Uncommitted**

   * Lowest isolation.
   * Allows **dirty reads**, non-repeatable reads, and phantom reads.
   * Maximum concurrency, minimum safety.

2. **Read Committed** *(default in many DBs)*

   * Prevents **dirty reads**.
   * Non-repeatable reads and phantoms **can occur**.

3. **Repeatable Read**

   * Prevents **dirty reads** and **non-repeatable reads**.
   * Phantom reads **can occur**.

4. **Serializable**

   * Highest isolation.
   * Prevents **dirty reads, non-repeatable reads, and phantom reads**.
   * Transactions executed as if **serially**; may block a lot.

---

### **Isolation Levels Table**

| Isolation Level      | Dirty Reads | Non-Repeatable Reads | Phantom Reads | Notes                                 |
| -------------------- | ----------- | -------------------- | ------------- | ------------------------------------- |
| **Read Uncommitted** | ✅ Allowed   | ✅ Allowed            | ✅ Allowed     | Highest concurrency, unsafe           |
| **Read Committed**   | ❌ Prevented | ✅ Allowed            | ✅ Allowed     | Default in SQL Server/Oracle          |
| **Repeatable Read**  | ❌ Prevented | ❌ Prevented          | ✅ Allowed     | Prevents row changes, not inserts     |
| **Serializable**     | ❌ Prevented | ❌ Prevented          | ❌ Prevented   | Full isolation, like serial execution |

---

I can also make a **visual diagram showing transactions vs reads** for all levels—it’s super helpful to remember.

Do you want me to make that diagram?
