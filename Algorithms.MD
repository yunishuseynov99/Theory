Отлично! Давай разберём **5 типичных LeetCode задач** с алгоритмами, которые решаются за **O(n) время**, с пояснением **как думать и почему сложность O(n)**. Я дам **короткие, понятные объяснения**.

---

## **1️⃣ Two Sum (Easy)**

**Задача:**
Дан массив `nums` и число `target`. Найти индексы двух чисел, сумма которых равна `target`.

**Алгоритм:** Хеш-таблица (Dictionary)

```csharp
public int[] TwoSum(int[] nums, int target) {
    var dict = new Dictionary<int, int>();
    for (int i = 0; i < nums.Length; i++) {
        int complement = target - nums[i];
        if (dict.ContainsKey(complement))
            return new int[] { dict[complement], i };
        dict[nums[i]] = i;
    }
    return new int[0];
}
```

**Объяснение:**

* Проходим массив один раз → **O(n)**
* Dictionary обеспечивает O(1) поиск
* **Space O(n)** из-за словаря

---

## **2️⃣ Maximum Subarray (Kadane’s Algorithm, Easy)**

**Задача:**
Найти максимальную сумму подмассива в массиве `nums`.

**Алгоритм:** Kadane’s Algorithm

```csharp
public int MaxSubArray(int[] nums) {
    int maxSum = nums[0], currentSum = nums[0];
    for (int i = 1; i < nums.Length; i++) {
        currentSum = Math.Max(nums[i], currentSum + nums[i]);
        maxSum = Math.Max(maxSum, currentSum);
    }
    return maxSum;
}
```

**Объяснение:**

* Проходим массив один раз → **O(n)**
* **Space O(1)**, т.к. храним только два числа

---

## **3️⃣ Move Zeroes (Easy)**

**Задача:**
Переместить все нули в конец массива, сохраняя порядок остальных элементов.

**Алгоритм:** Два указателя

```csharp
public void MoveZeroes(int[] nums) {
    int lastNonZero = 0;
    for (int i = 0; i < nums.Length; i++) {
        if (nums[i] != 0) {
            (nums[lastNonZero], nums[i]) = (nums[i], nums[lastNonZero]);
            lastNonZero++;
        }
    }
}
```

**Объяснение:**

* Один проход по массиву → **O(n)**
* **Space O(1)**, инлайновые перестановки

---

## **4️⃣ Valid Anagram (Easy)**

**Задача:**
Проверить, можно ли переставить буквы строки `s` чтобы получить `t`.

**Алгоритм:** Хеш-таблица для подсчёта букв

```csharp
public bool IsAnagram(string s, string t) {
    if (s.Length != t.Length) return false;
    var count = new int[26];
    for (int i = 0; i < s.Length; i++) {
        count[s[i] - 'a']++;
        count[t[i] - 'a']--;
    }
    return count.All(c => c == 0);
}
```

**Объяснение:**

* Один проход по строкам → **O(n)**
* **Space O(1)** (маска фиксированного размера 26)

---

## **5️⃣ Single Number (Easy)**

**Задача:**
В массиве все числа встречаются дважды, кроме одного. Найти его.

**Алгоритм:** XOR

```csharp
public int SingleNumber(int[] nums) {
    int result = 0;
    foreach (var num in nums)
        result ^= num;
    return result;
}
```

**Объяснение:**

* Один проход → **O(n)**
* **Space O(1)**
* XOR сам убирает все повторения

---

### ✅ Итог:

| Задача        | Алгоритм     | Time | Space |
| ------------- | ------------ | ---- | ----- |
| Two Sum       | Dictionary   | O(n) | O(n)  |
| Max Subarray  | Kadane       | O(n) | O(1)  |
| Move Zeroes   | Two pointers | O(n) | O(1)  |
| Valid Anagram | Array count  | O(n) | O(1)  |
| Single Number | XOR          | O(n) | O(1)  |

---


Отлично! Давай разберём **3 задачи, которые невозможно решить быстрее чем O(n²)** (так называемые “обязательные квадратичные”), с алгоритмами и объяснениями.

---

## **1️⃣ 3Sum (Medium)**

**Задача:**
Дан массив `nums`. Найти все уникальные тройки, сумма которых равна нулю.

**Алгоритм:** Брутфорс (самый прямой)

```csharp
public IList<IList<int>> ThreeSum(int[] nums) {
    var res = new List<IList<int>>();
    int n = nums.Length;
    for (int i = 0; i < n - 2; i++) {
        for (int j = i + 1; j < n - 1; j++) {
            for (int k = j + 1; k < n; k++) {
                if (nums[i] + nums[j] + nums[k] == 0) {
                    res.Add(new List<int>{ nums[i], nums[j], nums[k] });
                }
            }
        }
    }
    return res;
}
```

**Объяснение:**

* Тройные вложенные циклы → **O(n³)**
* На практике используют сортировку + два указателя → O(n²)
* **Space O(n)** для результата

> ⚠️ Суть: прямой брутфорс — O(n³), но оптимальное решение с двумя указателями **O(n²)** — это минимальное.

---

## **2️⃣ Bubble Sort (Easy/Classic)**

**Задача:**
Отсортировать массив.

**Алгоритм:** Классическая пузырьковая сортировка

```csharp
public void BubbleSort(int[] nums) {
    int n = nums.Length;
    for (int i = 0; i < n - 1; i++) {
        for (int j = 0; j < n - i - 1; j++) {
            if (nums[j] > nums[j + 1]) {
                (nums[j], nums[j + 1]) = (nums[j + 1], nums[j]);
            }
        }
    }
}
```

**Объяснение:**

* Два вложенных цикла → **O(n²)**
* **Space O(1)** (сортируем на месте)
* Любая версия пузырька не может быть быстрее O(n²) в худшем случае

> ⚠️ Даже оптимизированные пузырьки → O(n²) в худшем случае, только O(n) если почти отсортировано.

---

## **3️⃣ Check for Duplicate Pairs (All Pairs)**

**Задача:**
Проверить, есть ли пара чисел с одинаковой суммой или найти все уникальные пары `(i, j)`.

**Алгоритм:** Два вложенных цикла

```csharp
public bool HasDuplicatePair(int[] nums) {
    int n = nums.Length;
    for (int i = 0; i < n - 1; i++) {
        for (int j = i + 1; j < n; j++) {
            if (nums[i] == nums[j])
                return true;
        }
    }
    return false;
}
```

**Объяснение:**

* Два вложенных цикла → **O(n²)**
* Space O(1)
* Любая проверка всех пар → квадратичная, если не использовать хеш, но тогда это уже другая постановка задачи.

---

### ✅ Итог:

| Задача                      | Алгоритм                   | Time               | Space |
| --------------------------- | -------------------------- | ------------------ | ----- |
| 3Sum                        | Two pointers / brute-force | O(n²) (оптимально) | O(n)  |
| Bubble Sort                 | Пузырьковая сортировка     | O(n²)              | O(1)  |
| Duplicate Pairs (all pairs) | Два вложенных цикла        | O(n²)              | O(1)  |

> ⚠️ Главное: эти задачи **не позволяют алгоритму быть быстрее O(n²)** в худшем случае без изменения условия задачи.

---

Отлично! Давай разберём **3 типичных задачи, которые решаются только за O(log n)** (то есть быстрее нельзя без изменения структуры данных), с алгоритмами и объяснениями.

---

## **1️⃣ Binary Search (Easy)**

**Задача:**
Найти элемент `target` в **отсортированном массиве** `nums`.

**Алгоритм:** Классический бинарный поиск

```csharp
public int BinarySearch(int[] nums, int target) {
    int left = 0, right = nums.Length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) return mid;
        else if (nums[mid] < target) left = mid + 1;
        else right = mid - 1;
    }
    return -1;
}
```

**Объяснение:**

* Каждый шаг делим массив пополам → **O(log n)**
* **Space O(1)** для итеративной версии
* Невозможно быстрее, потому что нужно проверить `target` в отсортированном массиве

---

## **2️⃣ Search in a Binary Search Tree (BST) (Easy/Medium)**

**Задача:**
Найти узел со значением `target` в BST.

**Алгоритм:** Рекурсивный поиск

```csharp
public TreeNode SearchBST(TreeNode root, int target) {
    if (root == null || root.val == target) return root;
    return target < root.val ? SearchBST(root.left, target) : SearchBST(root.right, target);
}
```

**Объяснение:**

* Каждое ветвление делит пространство поиска пополам → **O(log n)** в сбалансированном дереве
* Space O(log n) для рекурсивного стека
* Невозможно быстрее, чем O(log n) для поиска в BST без дополнительной структуры

---

## **3️⃣ Find Insert Position (Easy)**

**Задача:**
Вставить `target` в отсортированный массив `nums` так, чтобы массив остался отсортированным.

**Алгоритм:** Бинарный поиск позиции

```csharp
public int SearchInsert(int[] nums, int target) {
    int left = 0, right = nums.Length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) return mid;
        else if (nums[mid] < target) left = mid + 1;
        else right = mid - 1;
    }
    return left;
}
```

**Объяснение:**

* Делим диапазон пополам → **O(log n)**
* Space O(1)
* Любая попытка сделать линейный проход → O(n), бинарный поиск минимален

---

### ✅ Итог:

| Задача               | Алгоритм                  | Time     | Space           |
| -------------------- | ------------------------- | -------- | --------------- |
| Binary Search        | Итеративный / рекурсивный | O(log n) | O(1) / O(log n) |
| Search in BST        | Рекурсивный поиск         | O(log n) | O(log n)        |
| Find Insert Position | Binary search             | O(log n) | O(1)            |

> ⚠️ Главное: **O(log n) появляется только там, где можно делить пространство поиска пополам** (отсортированные массивы, бинарные деревья, heap).

---

Если хочешь, я могу сделать **ещё 3 “обязательные O(log n) задачи” с реальными LeetCode паттернами**, которые обычно сеньоры получают на интервью.

Хочешь, чтобы я это сделал?


