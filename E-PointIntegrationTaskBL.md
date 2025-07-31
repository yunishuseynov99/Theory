Вот краткое содержание (бизнес-логика + шаги интеграции) подключения платежной системы **Epoint** на сайт или в приложение:

---
###  **Page 1-9**
### 📌 **Цель**

Организовать прием онлайн-платежей через платежную страницу **Epoint**.

---

### 🔧 **Что нужно предоставить Epoint для подключения:**

1. Адрес Вашего сайта
2. URL успешной оплаты — `success_url`
3. URL ошибки оплаты — `error_url`
4. URL для приёма результатов платежей — `result_url`

После проверки вы получите:

* `public_key` — публичный ключ (ID торговца)
* `private_key` — секретный ключ для формирования сигнатуры

---

### 🚀 **Шаги работы платежной страницы Epoint**

1. **Сформировать API-запрос**:

   * Создать `json` с параметрами платежа.
   * Закодировать его в `base64` → это `data`.
   * Сформировать `signature`:

     ```plaintext
     base64_encode( sha1( private_key + data + private_key, true ) )
     ```

2. **Отправить POST-запрос** на:

   * `https://epoint.az/api/1/request` — для получения URL страницы оплаты
     **или**
   * `https://epoint.az/api/1/checkout` — для прямого редиректа клиента

3. **Клиент вводит данные карты** на странице оплаты банка.

4. **После оплаты:**

   * Удача → редирект на `success_url`
   * Ошибка → редирект на `error_url`

5. **Результат транзакции** дополнительно отправляется серверу на `result_url` (POST-запрос с деталями платежа).

---

### 🧾 **Основные параметры `json` (внутри `data`)**

| Параметр               | Обязательный | Описание                          |
| ---------------------- | ------------ | --------------------------------- |
| `public_key`           | ✅            | Ваш ID в системе                  |
| `amount`               | ✅            | Сумма оплаты                      |
| `currency`             | ✅            | Валюта (например, `AZN`)          |
| `language`             | ✅            | Язык страницы (`az`, `en`, `ru`)  |
| `order_id`             | ✅            | Уникальный ID заказа              |
| `description`          | ⭕            | Описание (до 1000 символов)       |
| `is_installment`       | ⭕            | 1 = рассрочка, 0 = обычная оплата |
| `success_redirect_url` | ⭕            | Ссылка при успехе                 |
| `error_redirect_url`   | ⭕            | Ссылка при ошибке                 |
| `other_attr`           | ⭕            | Доп. параметры                    |

---

### 📤 **Итог:**

Вы отправляете `data` и `signature` на сервер Epoint. В ответ — `redirect_url`, на который отправляется клиент. По завершении оплаты вы получаете уведомление на `result_url`.

Хочешь пример кода для генерации `data` и `signature`?



-----------------------------------------------------------------------------------------------------------
###  **Page 9-14**
Вот краткое **пошаговое описание бизнес-логики** обработки callback-запроса от платёжного сервиса **Epoint** с деталями:

---

## **1. Получение callback от Epoint**

После завершения транзакции, **Epoint** отправляет на ваш `result_url` HTTP **POST-запрос** с параметрами:

* `data` — закодированные параметры транзакции
* `signature` — подпись для проверки подлинности

---

## **2. Проверка подлинности запроса**

### Шаг 1: Сформировать свою подпись (signature)

На вашем сервере:

1. Получите `data` из POST-запроса.
2. Сформируйте строку:
   `private_key + data + private_key`
3. Вычислите SHA1 от этой строки (в бинарном виде).
4. Закодируйте результат в base64.

Вот C# код, который формирует `signature` для проверки подлинности запроса от Epoint:

```csharp
using System;
using System.Security.Cryptography;
using System.Text;

public class EpointSignatureHelper
{
    public static string GenerateSignature(string privateKey, string data)
    {
        // Формируем строку: private_key + data + private_key
        string sgnString = privateKey + data + privateKey;

        // SHA1-хеш в бинарном виде
        using (SHA1 sha1 = SHA1.Create())
        {
            byte[] hashBytes = sha1.ComputeHash(Encoding.UTF8.GetBytes(sgnString));

            // Закодировать хеш в base64
            return Convert.ToBase64String(hashBytes);
        }
    }
}
```

### Пример использования:

```csharp
string privateKey = "your_private_key";
string data = "полученное_значение_data";

string signature = EpointSignatureHelper.GenerateSignature(privateKey, data);

// Сравниваешь эту подпись с пришедшей от Epoint
bool isValid = (signature == signatureFromEpoint);
```

Если нужно, могу также помочь с декодированием `data` из base64 и парсингом JSON.

Отлично, вот как в C#:

---

## ✅ Раскодировать `data` из base64 и распарсить JSON

```csharp
using System;
using System.Text;
using System.Text.Json;

public class EpointDataHelper
{
    public static JsonDocument DecodeData(string base64Data)
    {
        // Декодируем из base64
        byte[] dataBytes = Convert.FromBase64String(base64Data);
        
        // Преобразуем в строку (UTF-8)
        string jsonString = Encoding.UTF8.GetString(dataBytes);
        
        // Разбираем JSON
        return JsonDocument.Parse(jsonString);
    }
}
```

---

## ✅ Пример использования:

```csharp
string base64Data = "полученное_значение_data_из_callback";

var jsonDoc = EpointDataHelper.DecodeData(base64Data);
var root = jsonDoc.RootElement;

// Примеры доступа к значениям:
string orderId = root.GetProperty("order_id").GetString();
string status = root.GetProperty("status").GetString();
string message = root.GetProperty("message").GetString();
decimal amount = root.GetProperty("amount").GetDecimal();

Console.WriteLine($"Order ID: {orderId}, Status: {status}, Amount: {amount}, Message: {message}");
```

---

## ⚠️ Убедись:

* Библиотека `System.Text.Json` доступна с .NET Core 3.0+ или .NET 5+
* Если используешь старый .NET Framework, нужно использовать `Newtonsoft.Json`

Хочешь пример на `Newtonsoft.Json` тоже?


### Шаг 2: Сравните подписи

* Сравните вашу `signature` с той, что пришла от **Epoint**
* Если совпадают → запрос подлинный

---

## **3. Расшифровка параметра `data`**

* Раскодируйте `data` из base64 → получится JSON-строка с параметрами транзакции

---

## **4. Обработка статуса транзакции**

Пример параметров после декодирования `data`:

```json
{
  "order_id": "12345",
  "status": "success",
  "code": "00",
  "message": "Оплата прошла успешно",
  "transaction": "...",
  "bank_transaction": "...",
  "operation_code": "100",
  "rrn": "123456789012",
  "card_name": "Ivan Ivanov",
  "card_mask": "123456******1234",
  "amount": 100.00,
  "other_attr": { ... }
}
```

* Используйте `status`, `code`, `message` для определения результата операции
* Пример статусов:

  * `new` — платёж зарегистрирован
  * `success` — платёж успешен
  * `error`, `server_error` — ошибка
  * `returned` — возврат средств

---

## **5. При необходимости — запрос статуса вручную**

Если вы хотите **самостоятельно проверить статус транзакции**, вызовите API Epoint:

* URL: `https://epoint.az/api/1/get-status`
* Метод: POST
* Параметры:

  * `data` — base64(json)
  * `signature` — как и ранее

---

## **Итог**

Сценарий интеграции:

1. Получили `POST` от Epoint → проверили `signature`
2. Расшифровали `data` → получили статус и детали платежа
3. Сохранили/обработали результат в системе
4. При необходимости сделали запрос на `get-status` для уточнения


---------------------------------------------------------------------------------------------------------
###  **Page 14-19**

Вот краткое **пошаговое описание бизнес-логики** интеграции с платежной системой **Epoint** для **сохранения карты** и **выполнения платежей без повторного ввода карточных данных**, ориентированное на реализацию на **бэкенде C#**:

---

## ✅ **1. Сохранение карты (регистрация карты)**

### **Цель:**

Получить `card_id` — уникальный идентификатор карты, чтобы использовать её для последующих платежей.

### **Шаги:**

1. **Сформировать JSON-объект** с параметрами:

   * `public_key` — публичный ключ торговца.
   * `language` — язык страницы: az, en, ru.
   * `refund` — (опционально) 0 для списания, 1 для выплаты.
   * `description` — (опционально) описание.
   * `success_redirect_url` и `error_redirect_url` — (опционально) для перенаправления клиента.

2. **Закодировать JSON как строку (`data`)**, затем сгенерировать `signature`:

   ```csharp
   string signature = HmacSHA256(data, privateKey);
   ```

3. **POST-запрос** на `https://epoint.az/api/1/card-registration` с `data` и `signature`.

4. **Ответ Epoint:**

   * `status` = "success" или "error".
   * `redirect_url` — адрес, куда нужно перенаправить клиента для ввода карты.
   * `card_id` — сохраняется в базе (ключ для оплаты в будущем).

---

## ✅ **2. Обработка результата регистрации карты (callback на ваш сервер)**

### **Сценарий:**

После регистрации карты Epoint отправляет POST на ваш `result_url`.

### **Тело запроса:**

* `data` (закодированная строка JSON)
* `signature`

### **На вашей стороне:**

1. Проверить `signature`, пересчитав HMAC с `data` и `private_key`.

2. Если подпись совпадает:

   * Раскодировать `data`, извлечь:

     * `status`, `code`, `message`
     * `card_id`, `card_mask`, `card_name`, `operation_code`
     * `bank_transaction`, `bank_response`, `rrn`

3. Сохранить `card_id` — он нужен для списания.

---

## ✅ **3. Выполнение платежа сохранённой картой**

### **Цель:**

Произвести оплату без ввода карты, используя `card_id`.

### **Шаги:**

1. **Сформировать JSON-объект** с параметрами:

   * `public_key`
   * `language`
   * `card_id` — получен ранее.
   * `order_id` — уникальный ID операции в вашей системе.
   * `amount`, `currency` (например, 100, "AZN")
   * `description` (опционально)

2. **Сгенерировать `data` и `signature`**, аналогично шагу 1.

3. **POST-запрос** на `https://epoint.az/api/1/execute-pay`.

4. **Ответ от Epoint:**

   * `status` = "success" или "failed"
   * `transaction`, `bank_transaction`, `rrn`, `bank_response`
   * `card_name`, `card_mask`, `amount`
   * `message`

---

## ⚙️ **Технические детали реализации:**

* `signature = HMAC_SHA256(data, private_key)` — строка должна быть подписана строго этим алгоритмом.
* `data` — сериализованный JSON в виде строки.
* Используй `System.Text.Json` или `Newtonsoft.Json` для сериализации.
* Используй `System.Security.Cryptography.HMACSHA256` для генерации подписи.

---

---------------------------------------------------------------------------------------------------------------------------
