### 🔹 **Basic REST API Questions**

1. **What is a REST API?**
   **Answer:** REST (Representational State Transfer) is an architectural style for designing networked applications. A REST API uses HTTP requests to access and manipulate data using standard HTTP methods like GET, POST, PUT, DELETE.

2. **What are the main HTTP methods used in REST?**
   **Answer:**

   * **GET** – Retrieve data
   * **POST** – Create new resource
   * **PUT** – Update existing resource (full)
   * **PATCH** – Update part of a resource (partial)
   * **DELETE** – Remove a resource

3. **What are REST constraints?**
   **Answer:**

   * Statelessness
   * Client-Server architecture
   * Uniform interface
   * Cacheability
   * Layered system
   * Code on demand (optional)

4. **What is a resource in REST?**
   **Answer:** A resource is any object or data entity (like user, product, article) that can be identified using a URI.

5. **What is the difference between PUT and PATCH?**
   **Answer:**

   * **PUT** replaces the entire resource.
   * **PATCH** updates only specific fields.

---

### 🔹 **Intermediate REST API Questions**

6. **What is statelessness in REST?**
   **Answer:** Every request from client to server must contain all the information needed. The server does not store anything about the client's session between requests.

7. **What is the structure of a RESTful URI?**
   **Answer:** URIs should be nouns, not verbs. Example:

   * Good: `/api/users/123`
   * Bad: `/api/getUserById/123`

8. **What are status codes in REST APIs?**
   **Answer:**

   * **200 OK** – Success
   * **201 Created** – Resource created
   * **204 No Content** – Success, no content to return
   * **400 Bad Request** – Invalid input
   * **401 Unauthorized** – Auth required
   * **403 Forbidden** – Access denied
   * **404 Not Found** – Resource not found
   * **500 Internal Server Error** – Server failure

9. **What is idempotency in REST?**
   **Answer:** An operation is idempotent if it produces the same result when called multiple times. `GET`, `PUT`, and `DELETE` are idempotent; `POST` is not.

10. **How do you handle errors in REST API?**
    **Answer:** Use meaningful HTTP status codes and return a structured error response (usually JSON) with message, error code, and details.

---

### 🔹 **Advanced REST API Questions**

11. **How do you handle versioning in REST APIs?**
    **Answer:**

    * URI versioning: `/api/v1/users`
    * Header versioning: `Accept: application/vnd.company.v1+json`
    * Query param versioning: `/users?version=1`

12. **What is HATEOAS?**
    **Answer:** HATEOAS (Hypermedia As The Engine Of Application State) allows clients to navigate the API using links returned in the responses, improving discoverability.

13. **What are REST API best practices?**
    **Answer:**

    * Use proper HTTP methods and status codes
    * Use nouns in endpoints
    * Version your API
    * Secure endpoints
    * Use pagination, filtering, sorting

14. **What is the difference between REST and SOAP?**
    **Answer:**

    * REST is lightweight, uses HTTP/JSON, and is stateless
    * SOAP is a protocol, XML-based, with more strict standards

15. **How do you secure a REST API?**
    **Answer:**

    * HTTPS
    * Authentication (JWT, OAuth2)
    * Authorization
    * Rate limiting
    * Input validation & sanitization

---

### 🔹 **Practical REST API Development Questions**

16. **How do you implement pagination in a REST API?**
    **Answer:** Use query parameters:
    `/api/products?page=2&limit=10`

17. **What is CORS? Why is it important in REST APIs?**
    **Answer:** CORS (Cross-Origin Resource Sharing) allows browsers to make requests to APIs hosted on different domains. It's important for security in frontend-backend interactions.

18. **How do you test REST APIs?**
    **Answer:**

    * Tools like Postman, Insomnia
    * Automated tests with NUnit/xUnit, Swagger, etc.
    * Validate status codes, response body, headers

19. **What is Swagger/OpenAPI?**
    **Answer:** A tool and specification to document, design, and test REST APIs interactively.

20. **How would you handle file uploads in a REST API?**
    **Answer:**

    * Use `multipart/form-data`
    * Accept files via HTTP POST with appropriate headers

---

### 🔹 **Scenario-Based & Conceptual Questions**

21. **What would a REST response to a POST request typically return?**
    **Answer:**

    * `201 Created` status
    * `Location` header with URI of created resource
    * JSON body with resource details

22. **Can you call a REST API from a frontend app directly? How do you handle auth?**
    **Answer:** Yes, using `fetch`/`axios`. For auth, use JWT tokens stored in memory or secure cookies.

23. **How do you ensure your REST API is scalable?**
    **Answer:**

    * Stateless design
    * Caching
    * Rate limiting
    * Horizontal scaling
    * Use of API gateways

24. **What is the role of middleware in REST API development?**
    **Answer:** Middleware handles cross-cutting concerns like logging, authentication, error handling, etc., before the request reaches the controller.

25. **How do you ensure backward compatibility in REST APIs?**
    **Answer:**

    * Never break existing contracts
    * Use versioning
    * Avoid removing or renaming fields in responses

---

**"gotcha"** questions
---

### 🔥 **1. What’s the difference between PUT and PATCH?**

* **Gotcha:** Many developers incorrectly use PUT when PATCH is more appropriate.
* **Correct:**

  * `PUT` replaces the **entire resource**.
  * `PATCH` makes a **partial update**.

---

### 🔥 **2. Can GET requests have a body?**

* **Gotcha:** The HTTP spec does not forbid it, but most servers and clients **ignore** the body in a GET request.
* **Correct:** Technically allowed, but **don’t rely** on the body for GET; use query parameters instead.

---

### 🔥 **3. Is REST stateless? How do you handle authentication then?**

* **Gotcha:** REST is stateless, which **confuses people** about how sessions/auth work.
* **Correct:**

  * Use **tokens (e.g., JWT)** or API keys in headers like `Authorization`.
  * No session state is stored on the server between requests.

---

### 🔥 **4. Should POST be idempotent?**

* **Gotcha:** Many think POST should be safe to retry.
* **Correct:**

  * **POST is NOT idempotent.**
  * Only `GET`, `PUT`, `DELETE`, and `HEAD` are expected to be idempotent.

---

### 🔥 **5. Can DELETE be safely retried?**

* **Gotcha:** Many think DELETE isn’t idempotent.
* **Correct:**

  * **Yes, it is idempotent.**
  * Deleting the same resource multiple times results in the same state: it’s gone.

---

### 🔥 **6. What HTTP status code should you return for a validation error?**

* **Gotcha:** Many use `500` or `200`.
* **Correct:**

  * Use `400 Bad Request` for validation issues.
  * `422 Unprocessable Entity` is also valid and more specific in some cases.

---

### 🔥 **7. What is HATEOAS, and is it required for REST?**

* **Gotcha:** Many think HATEOAS is a core REST requirement.
* **Correct:**

  * It’s part of **REST maturity level 3**, but **rarely implemented** in practice.
  * Most APIs don’t use it even if they call themselves RESTful.

---

### 🔥 **8. Can you use a body in a DELETE request?**

* **Gotcha:** Technically yes, but it’s often ignored.
* **Correct:**

  * RFC 7231 allows a body, but **many servers don't support it**.
  * Avoid it unless absolutely necessary.

---

### 🔥 **9. What status code should you return after a successful POST?**

* **Gotcha:** Many default to `200 OK`.
* **Correct:**

  * **201 Created** is correct, with a `Location` header pointing to the new resource.

---

### 🔥 **10. What’s the correct response code for unauthorized access vs. unauthenticated access?**

* **Gotcha:** People confuse `401` and `403`.
* **Correct:**

  * `401 Unauthorized` = Not **authenticated** (e.g., token missing or invalid).
  * `403 Forbidden` = Authenticated, but **no permission** to access the resource.
