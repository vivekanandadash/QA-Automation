# API Testing Fundamentals

> **Purpose:** Build a strong foundation in APIs before learning Postman and Java-based API automation.
>
> **Learning path:** API Basics → HTTP Fundamentals → REST/SOAP → JSON/XML → Authentication → API Testing → Postman → Swagger → REST Assured → JMeter

---

## Table of Contents

1. [Introduction to APIs](#1-introduction-to-apis)
2. [API Architecture and Communication](#2-api-architecture-and-communication)
3. [Types of APIs](#3-types-of-apis)
4. [HTTP Fundamentals](#4-http-fundamentals)
5. [HTTP Status Codes](#5-http-status-codes)
6. [JSON and XML](#6-json-and-xml)
7. [Authentication and Authorization](#7-authentication-and-authorization)
8. [API Testing Fundamentals](#8-api-testing-fundamentals)
9. [API Testing Tools](#9-api-testing-tools)
10. [E-commerce API Example](#10-e-commerce-api-example)
11. [Recommended Learning Sequence](#11-recommended-learning-sequence)
12. [Learning Checklist](#12-learning-checklist)

---

## 1. Introduction to APIs

### 1.1 What is an API?

**API** stands for **Application Programming Interface**. It is a set of rules that lets one software application talk to another, request data, or ask it to perform an action, without needing to know how the other application is built internally.

**Restaurant analogy**

| Restaurant | API world |
|---|---|
| You (the customer) | Client application |
| The menu | API documentation |
| The waiter | The API |
| The kitchen | Server / backend / database |
| The dish delivered | Response |

You never walk into the kitchen. You tell the waiter what you want, and the waiter brings it back. An API works the same way.

### 1.2 Why do we need APIs?

- **Communication:** Different applications (mobile app, website, payment system) need to exchange data.
- **Reusability:** One backend can serve a website, an Android app and an iOS app.
- **Separation of concerns:** Frontend and backend teams can work independently.
- **Integration:** Third-party services (payments, maps, SMS, login) can be plugged in easily.
- **Security:** The API exposes only what is allowed and hides the internal database and logic.
- **Automation and scalability:** Systems can interact without human involvement.

### 1.3 Purpose of an API

- Expose specific data or functionality in a controlled way.
- Define a contract: what to send, what you will get back.
- Allow systems built in different languages and technologies to work together.

### 1.4 How does an API work?

1. The client sends a **request** to an API **endpoint**.
2. The API receives the request and validates it (format, authentication, permissions).
3. The server processes the request, possibly reading or writing a database or calling other services.
4. The server sends back a **response** (data plus a status code).
5. The client uses or displays the response.

### 1.5 Real-world examples

| Scenario | What the API does |
|---|---|
| Weather app | Calls a weather service API to get the forecast |
| "Login with Google" | Your app calls Google's authentication API |
| Online payment | Shopping site calls a payment gateway API |
| Google Maps on a food delivery app | Delivery app calls a maps API for routes |
| Flight booking site | Calls multiple airline APIs and shows combined results |
| UPI / banking apps | Apps call bank APIs to check balance or transfer money |

### 1.6 Client and Server concepts

- **Client:** The one that *sends* the request (browser, mobile app, Postman, another server).
- **Server:** The one that *receives* the request, processes it and sends the response.
- A single system can be both: a backend server can act as a client when it calls a payment API.

### 1.7 How two different applications communicate

Applications agree on a **protocol** (usually HTTP/HTTPS), a **data format** (usually JSON or XML), and a **contract** (endpoints, methods, required fields). As long as both sides follow the contract, it does not matter what language each is written in (Java ↔ Python ↔ JavaScript).

### 1.8 API vs. Web Application

| Aspect | Web Application | API |
|---|---|---|
| Audience | Human users | Other software |
| Interface | Visual UI (pages, buttons) | No UI; requests and responses |
| Output | HTML pages | Data (JSON/XML) |
| Example | Opening amazon.com in a browser | Mobile app fetching product data from Amazon's backend |

### 1.9 API vs. Web Services

- A **web service** is an API that is accessed **over a network** (typically HTTP).
- **Every web service is an API, but not every API is a web service.** For example, a Java library's methods are an API but not a web service, since they do not run over a network.
- Web services commonly use SOAP or REST.

### 1.10 Types of APIs

Covered in detail in [Section 3](#3-types-of-apis).

### Learning outcome

You should be able to explain an API in simple words and describe how one application requests data or a service from another.

---

## 2. API Architecture and Communication

### 2.1 Client–Server Architecture

The system is split into two roles:

```
 ┌──────────┐   Request    ┌──────────┐      ┌──────────┐
 │  Client  │ ───────────► │  Server  │ ───► │ Database │
 │          │ ◄─────────── │          │ ◄─── │          │
 └──────────┘   Response   └──────────┘      └──────────┘
```

- The server holds data and business logic.
- The client is responsible for presenting or consuming the data.
- Multiple clients can use one server.

### 2.2 Request and Response

**Request contains:** method, URL/endpoint, headers, optional parameters, optional body.
**Response contains:** status code, headers, optional body.

### 2.3 API Communication Flow

```
Client → (DNS lookup) → Server address
Client → HTTP(S) request → API endpoint
Server → validate, authenticate, process
Server → HTTP(S) response → Client
Client → parse response → show/use data
```

### 2.4 HTTP Protocol

**HTTP (HyperText Transfer Protocol)** is the rule set for transferring data on the web. Key properties:

- **Request–response based:** the client asks, the server answers.
- **Stateless:** each request is independent; the server does not remember previous requests unless information (token, session ID) is sent again.
- **Text-based:** human-readable messages.

### 2.5 HTTPS Protocol

**HTTPS = HTTP + TLS/SSL encryption.**

- Encrypts data in transit so it cannot be read by attackers.
- Verifies the server's identity using certificates.
- Protects against tampering.
- Default port: **443** (HTTP uses **80**).
- Always use HTTPS for APIs that carry credentials or personal data.

### 2.6 REST Architecture

**REST (Representational State Transfer)** is an architectural style for designing networked APIs. It is not a protocol.

**Key principles**

1. **Client–Server** separation.
2. **Stateless:** each request carries all the information needed.
3. **Cacheable:** responses can declare whether they may be cached.
4. **Uniform interface:** consistent use of URLs and HTTP methods.
5. **Layered system:** client may not know whether it talks to the real server or an intermediary (gateway, load balancer).
6. **Resource-based:** everything is a *resource* identified by a URL (e.g., `/users/5`).

**Typical characteristics:** uses HTTP methods, commonly exchanges JSON, lightweight and fast.

### 2.7 SOAP Architecture

**SOAP (Simple Object Access Protocol)** is a strict, XML-based messaging protocol.

A SOAP message contains:

```xml
<soap:Envelope>
  <soap:Header> ... </soap:Header>   <!-- optional metadata, e.g. security -->
  <soap:Body>   ... </soap:Body>     <!-- the actual request/response -->
</soap:Envelope>
```

- Described by a **WSDL** (Web Services Description Language) file that defines operations and data types.
- Supports standards like WS-Security.
- Common in banking, telecom and enterprise systems.

### 2.8 REST API vs. SOAP API

| Feature | REST | SOAP |
|---|---|---|
| Type | Architectural style | Protocol |
| Data format | JSON, XML, text, etc. | XML only |
| Contract | OpenAPI/Swagger (optional) | WSDL (mandatory) |
| Transport | Mostly HTTP/HTTPS | HTTP, SMTP and others |
| Weight | Lightweight | Heavier (XML envelope) |
| Speed | Generally faster | Generally slower |
| Caching | Supported | Not typically |
| Standards/Security | Relies on HTTPS, OAuth, tokens | Built-in WS-Security |
| Typical use | Web/mobile apps, microservices | Enterprise, finance, legacy |

### 2.9 Synchronous vs. Asynchronous communication

| | Synchronous | Asynchronous |
|---|---|---|
| Behaviour | Client waits for the response | Client continues; result arrives later |
| Example | Fetching product details | Generating a large report, sending an email, processing payments |
| Typical response | `200 OK` with data | `202 Accepted`, then poll or receive a callback/webhook |

### 2.10 API Gateway: basic introduction

An **API Gateway** is a single entry point that sits in front of backend services.

```
Clients ──► API Gateway ──► Service A
                       ├──► Service B
                       └──► Service C
```

Typical responsibilities: routing, authentication, rate limiting, logging/monitoring, caching, load balancing. Examples: AWS API Gateway, Kong, Apigee, Azure API Management.

### Basic request–response flow (summary)

1. A client (browser or mobile app) sends a request.
2. The request reaches the server through an API endpoint.
3. The server processes it and may talk to a database or another service.
4. The server sends a response to the client.
5. The client displays or uses the returned information.

---

## 3. Types of APIs

### 3.1 Common API styles

| API type | Meaning | Notes |
|---|---|---|
| **REST** | Uses HTTP methods on resources, usually JSON | Most popular for web and mobile |
| **SOAP** | Strict XML-based messaging | Enterprise, high-security needs |
| **GraphQL** | Client asks for exactly the fields it needs, usually via a single endpoint | Avoids over-fetching and under-fetching |
| **RPC** | Client calls a procedure/function on a remote system (e.g., gRPC, JSON-RPC) | Action-oriented; gRPC is fast |
| **WebSocket** | Two-way, persistent connection | Chat apps, live scores, trading dashboards |

**GraphQL example**

```graphql
query {
  product(id: 101) {
    name
    price
  }
}
```

Returns only `name` and `price`, nothing extra.

### 3.2 API access categories

| Category | Description | Example |
|---|---|---|
| **Public (Open)** | Available to external developers, subject to terms and often an API key | Weather API, public maps API |
| **Private (Internal)** | Used only within an organization | Internal HR system API |
| **Partner** | Shared with approved business partners | Airline API shared with travel agencies |
| **Composite** | Combines multiple operations or data sources in one call | One call that fetches user, orders and payment info together |

---

## 4. HTTP Fundamentals

### 4.1 What is HTTP / HTTPS?

See [2.4](#24-http-protocol) and [2.5](#25-https-protocol).

### 4.2 URL structure

```
https://api.shop.com:443/v1/products/101?category=books&sort=price#reviews
└─┬──┘   └────┬─────┘└┬─┘└──────┬──────┘ └──────────┬──────────┘ └──┬───┘
scheme      host    port      path              query string     fragment
```

| Part | Meaning |
|---|---|
| Scheme/Protocol | `http` or `https` |
| Host/Domain | Server address |
| Port | Optional; defaults 80 (HTTP) / 443 (HTTPS) |
| Path | Location of the resource |
| Query string | Optional key-value filters after `?`, joined by `&` |
| Fragment | Client-side anchor (not sent to the server) |

### 4.3 URI vs. URL

- **URI (Uniform Resource Identifier):** identifies a resource (by name, location, or both).
- **URL (Uniform Resource Locator):** a URI that also tells you *where/how* to reach it.
- Every URL is a URI; not every URI is a URL.

### 4.4 HTTP request methods

| Method | Purpose | E-commerce example | Idempotent* | Safe** |
|---|---|---|---|---|
| **GET** | Retrieve data | Get product details | Yes | Yes |
| **POST** | Create data or trigger an operation | Create a new user | No | No |
| **PUT** | Replace an existing resource | Replace user details | Yes | No |
| **PATCH** | Partially update a resource | Update a user's email | Usually not guaranteed | No |
| **DELETE** | Delete a resource | Delete a product | Yes | No |
| HEAD | Like GET but returns headers only | Check if a product exists | Yes | Yes |
| OPTIONS | Ask which methods are supported | CORS pre-flight | Yes | Yes |

\* **Idempotent:** making the same request multiple times has the same effect as making it once.
\*\* **Safe:** does not modify data on the server.

**PUT vs. PATCH**

- `PUT /users/5` with a full user object → replaces the whole record.
- `PATCH /users/5` with `{"email": "new@x.com"}` → changes only that field.

### 4.5 HTTP request structure

```http
POST /api/users HTTP/1.1
Host: api.shop.com
Content-Type: application/json
Authorization: Bearer eyJhbGciOi...

{
  "name": "Vivek",
  "email": "vivek@example.com"
}
```

### 4.6 HTTP response structure

```http
HTTP/1.1 201 Created
Content-Type: application/json
Location: /api/users/101

{
  "id": 101,
  "name": "Vivek",
  "email": "vivek@example.com"
}
```

### 4.7 HTTP headers

Headers are key-value metadata sent with requests and responses.

| Header | Type | Purpose |
|---|---|---|
| `Content-Type` | Request/Response | Format of the body (`application/json`, `application/xml`) |
| `Accept` | Request | Format the client wants back |
| `Authorization` | Request | Credentials (Basic, Bearer token) |
| `User-Agent` | Request | Identifies the client |
| `Cache-Control` | Both | Caching rules |
| `Set-Cookie` / `Cookie` | Response / Request | Session handling |
| `Location` | Response | URL of a newly created resource |
| `Content-Length` | Both | Size of the body |

### 4.8 Important request components

| Component | Description | Example |
|---|---|---|
| **Endpoint / URL** | Address the request is sent to | `https://api.shop.com/products` |
| **Path parameter** | Value inside the URL path, identifies a specific resource | `/products/101` |
| **Query parameter** | Optional filter/sort/paging after `?` | `/products?category=books&page=2` |
| **Header** | Metadata | `Content-Type: application/json` |
| **Request body** | Data sent to the server (POST, PUT, PATCH) | JSON payload |
| **Response body** | Data returned by the server | JSON product object |

**Path vs. query parameter rule of thumb:** path parameters identify *which resource*; query parameters *filter, sort or paginate* a collection.

---

## 5. HTTP Status Codes

Status codes are grouped by their first digit:

| Range | Category | Meaning |
|---|---|---|
| 1xx | Informational | Request received, continuing |
| 2xx | Success | Request succeeded |
| 3xx | Redirection | Further action needed |
| 4xx | Client error | Problem with the request |
| 5xx | Server error | Problem on the server |

### Commonly used codes

| Code | Name | Meaning |
|---|---|---|
| **200** | OK | Request succeeded |
| **201** | Created | Resource created successfully |
| **202** | Accepted | Request accepted for processing (not yet complete) |
| **204** | No Content | Succeeded, no response body (common for DELETE) |
| **400** | Bad Request | Request is invalid (malformed JSON, missing field) |
| **401** | Unauthorized | Valid authentication is required or failed |
| **403** | Forbidden | Authenticated, but not allowed |
| **404** | Not Found | Resource does not exist |
| **405** | Method Not Allowed | HTTP method not supported for this resource |
| **409** | Conflict | Conflicts with current state (e.g., duplicate email) |
| **415** | Unsupported Media Type | Request format not supported |
| **422** | Unprocessable Content | Well-formed but fails validation rules |
| **500** | Internal Server Error | Unexpected server-side error |
| **502** | Bad Gateway | Invalid response from an upstream server |
| **503** | Service Unavailable | Server temporarily unable to handle requests |

### Comparisons to understand

| Pair | Difference |
|---|---|
| **200 vs. 201** | 200 = request worked (read/update). 201 = a *new resource was created* (typically after POST). |
| **400 vs. 401** | 400 = the request itself is wrong. 401 = missing or invalid credentials. |
| **401 vs. 403** | 401 = "I don't know who you are." 403 = "I know who you are, but you can't do this." |
| **404 vs. 500** | 404 = client asked for something that doesn't exist. 500 = server failed unexpectedly. |
| **502 vs. 503** | 502 = a gateway got a bad response from an upstream server. 503 = the server is overloaded or down for maintenance. |

---

## 6. JSON and XML

### 6.1 JSON

**JSON (JavaScript Object Notation)** is a lightweight, human-readable data format, and the most common format for REST APIs.

**Building blocks**

- **Object:** `{ ... }`, a set of key-value pairs.
- **Array:** `[ ... ]`, an ordered list of values.
- **Key-value pair:** `"name": "Vivek"`. Keys are always strings in double quotes.

**JSON data types**

| Type | Example |
|---|---|
| String | `"Vivek"` |
| Number | `101`, `99.5` |
| Boolean | `true`, `false` |
| Null | `null` |
| Object | `{ "city": "Bengaluru" }` |
| Array | `["Java", "SQL"]` |

**Example**

```json
{
  "id": 101,
  "name": "Vivek",
  "email": "vivek@example.com",
  "active": true,
  "skills": ["Java", "SQL", "API Testing"]
}
```

**Nested JSON**

```json
{
  "orderId": 5001,
  "customer": {
    "id": 101,
    "name": "Vivek",
    "address": { "city": "Bengaluru", "pin": "560001" }
  },
  "items": [
    { "productId": 1, "name": "Book", "price": 250.0, "qty": 2 },
    { "productId": 2, "name": "Pen",  "price": 20.5,  "qty": 10 }
  ],
  "paid": false,
  "discount": null
}
```

Reading values (path notation):

| What you want | Path |
|---|---|
| Customer name | `customer.name` |
| City | `customer.address.city` |
| First item name | `items[0].name` |
| Second item price | `items[1].price` |

**JSON validation**

- **Syntax validation:** is it valid JSON? (matching braces, quotes, commas, no trailing comma)
- **Schema validation:** does it follow the expected structure (required fields, data types, allowed values)? Done with **JSON Schema**.
- Common syntax errors: single quotes instead of double, missing comma, trailing comma, unquoted keys.

### 6.2 XML

**XML (eXtensible Markup Language)** uses tags to describe data.

- **Element:** `<name>Vivek</name>`
- **Attribute:** `<user id="101">`
- Must have exactly one **root element**; tags must be properly closed and nested; it is case-sensitive.

```xml
<user id="101">
  <name>Vivek</name>
  <email>vivek@example.com</email>
  <active>true</active>
  <skills>
    <skill>Java</skill>
    <skill>SQL</skill>
    <skill>API Testing</skill>
  </skills>
</user>
```

### 6.3 JSON vs. XML

| Feature | JSON | XML |
|---|---|---|
| Readability | Compact, easy | Verbose |
| Size | Smaller | Larger |
| Parsing speed | Faster | Slower |
| Data types | Built-in (string, number, boolean, null, array) | Everything is text unless a schema says otherwise |
| Attributes/Namespaces | No | Yes |
| Comments | No | Yes |
| Typical use | REST, modern web/mobile | SOAP, legacy and enterprise |

### Learning outcome

You should be able to read a JSON response, identify objects and arrays, understand nested data, and validate expected values.

---

## 7. Authentication and Authorization

### 7.1 Authentication vs. Authorization

| | Authentication (AuthN) | Authorization (AuthZ) |
|---|---|---|
| Question | **Who are you?** | **What are you allowed to do?** |
| Happens | First | After authentication |
| Example | Logging in with username/password | A normal user cannot delete other users; an admin can |
| Failure code | 401 | 403 |

### 7.2 Basic Authentication

- Client sends `Authorization: Basic <base64(username:password)>`.
- Base64 is **encoding, not encryption**, so it must only be used over HTTPS.
- Simple, but credentials are sent with every request.

### 7.3 API Keys

- A unique string identifying the calling application.
- Sent in a header (`x-api-key: abc123`) or as a query parameter.
- Good for identifying and rate-limiting clients; weak for identifying individual users.

### 7.4 Bearer Token Authentication

- Client first logs in and receives a token.
- Then sends `Authorization: Bearer <token>` on later requests.
- Whoever holds ("bears") the token gets access, so keep it secret.

### 7.5 JWT (JSON Web Token)

A JWT is a compact, signed token with three Base64URL-encoded parts separated by dots:

```
header.payload.signature
```

| Part | Contains |
|---|---|
| Header | Algorithm and token type |
| Payload | Claims: user id, role, expiry (`exp`), issued-at (`iat`) |
| Signature | Proof the token was not altered |

- The payload is **readable** (encoded, not encrypted), so never put secrets in it.
- The server verifies the signature without needing a session lookup.

### 7.6 OAuth 2.0: basic introduction

OAuth 2.0 is an **authorization framework** that lets an application access a user's resources on another service **without sharing the user's password**. Example: "Allow this app to read your Google contacts."

**Roles**

| Role | Meaning |
|---|---|
| Resource Owner | The user |
| Client | The application requesting access |
| Authorization Server | Issues tokens |
| Resource Server | The API holding the protected data |

**Simplified flow:** user grants permission → authorization server issues an access token → client calls the API with that token.

### 7.7 Access Token and Refresh Token

| | Access Token | Refresh Token |
|---|---|---|
| Purpose | Used to call the API | Used to get a new access token |
| Lifetime | Short (minutes to an hour) | Longer (days or more) |
| Sent to | Resource server (API) | Authorization server only |

### 7.8 Role-Based Access Control (RBAC)

Permissions are assigned to **roles**, and users are assigned roles.

| Role | View products | Add product | Delete product |
|---|---|---|---|
| Customer | ✅ | ❌ | ❌ |
| Seller | ✅ | ✅ | ❌ |
| Admin | ✅ | ✅ | ✅ |

**Testing tip:** call the same endpoint with different roles and verify allowed roles succeed and others get `403`.

---

## 8. API Testing Fundamentals

### 8.1 What is API Testing?

API testing verifies that an API **works correctly, reliably, securely and performs well**, by sending requests directly to endpoints and checking responses, **without using the UI**.

### 8.2 Why is API Testing required?

- **Earlier bug detection:** APIs are ready before the UI.
- **Faster and more stable** than UI tests (no browser dependency).
- **Wider coverage:** reaches business logic and error paths the UI may not expose.
- **Cost-effective:** issues are cheaper to fix early.
- **Easy to automate** and run in CI/CD.
- **Core layer:** many apps (web, mobile, partners) depend on the same API.

### 8.3 How API testing works

1. Understand the API requirements and documentation.
2. Prepare the request (URL, method, headers, auth, body).
3. Send the request.
4. Receive the response.
5. Compare actual vs. expected result (status, body, headers, time).
6. Report defects and re-test.

### 8.4 API Testing Life Cycle

1. **Requirement analysis:** understand endpoints, inputs, outputs, rules.
2. **Test planning:** scope, tools, environments, test data.
3. **Test case design:** positive, negative, boundary, security cases.
4. **Test environment setup:** base URLs, credentials, data.
5. **Execution:** run tests manually or via automation.
6. **Result analysis and defect reporting.**
7. **Regression and maintenance:** re-run after changes; update tests.

### 8.5 Types of API testing

| Type | What it checks | Example |
|---|---|---|
| **Positive testing** | Valid input gives the expected result | Valid login returns 200 and a token |
| **Negative testing** | Invalid input is handled properly | Wrong password returns 401; missing field returns 400 |
| **Functional testing** | The API does what the requirements say | Adding a product creates it with the right data |
| **Integration testing** | APIs/services work together | Order API correctly calls Payment API and Inventory API |
| **Regression testing** | New changes haven't broken existing behaviour | Re-run the suite after a release |
| **Boundary value testing** | Behaviour at min/max edges | Quantity allowed 1–100: test 0, 1, 100, 101 |
| **Data validation** | Types, formats, mandatory fields, values | `price` is a number; `email` is valid |
| **Error handling** | Clear, correct errors and codes | Meaningful message with the right 4xx/5xx |
| **Performance testing (intro)** | Speed and behaviour under load | 1,000 users calling `/products` at once |
| **Security testing (intro)** | Protection against misuse | No data without a token; no access to other users' data |

### 8.6 What should a tester validate?

- ✅ Correct **HTTP status code**
- ✅ Correct **response body and data**
- ✅ Correct **response headers** (e.g., `Content-Type`)
- ✅ **Data types** and **required fields**
- ✅ **Error messages** for invalid requests
- ✅ **Authentication and authorization** behaviour
- ✅ **Response time** against agreed expectations
- ✅ Correct behaviour for **valid and invalid inputs**
- ✅ Side effects: was the data actually created/updated/deleted? (verify with a follow-up GET)

### 8.7 Sample test case template

| Field | Example |
|---|---|
| Test ID | TC_API_001 |
| Endpoint | `POST /api/auth/login` |
| Scenario | Login with valid credentials |
| Precondition | User exists |
| Request body | `{"username":"vivek","password":"Pass@123"}` |
| Expected status | 200 |
| Expected body | Contains non-empty `token` |
| Expected time | < 2 seconds |

### 8.8 Negative and boundary ideas

- Missing required field → `400`/`422`
- Wrong data type (`"price": "abc"`) → `400`/`422`
- Empty body, null values, very long strings
- Invalid or expired token → `401`
- Valid token, insufficient role → `403`
- Non-existent ID → `404`
- Wrong method on an endpoint → `405`
- Wrong `Content-Type` → `415`
- Duplicate record → `409`
- Special characters / SQL-injection-like input (security intro)

---

## 9. API Testing Tools

Learn the tools in this practical order.

### 9.1 Postman: learn first

Postman is a GUI tool to send requests and inspect responses.

**Topics**

- Send GET, POST, PUT, PATCH, DELETE requests.
- Work with headers and parameters (Params tab).
- Send JSON bodies (Body → raw → JSON).
- Configure authorization (Basic, Bearer, API key, OAuth 2.0).
- Validate status codes and bodies.
- Write basic JavaScript tests (Tests tab).
- Use **environments** and **variables** (`{{baseUrl}}`).
- Organize requests into **collections**; run them with the Collection Runner.

**Sample Postman test script**

```javascript
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

pm.test("Response time is under 2000 ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(2000);
});

pm.test("Product has expected fields", function () {
    const json = pm.response.json();
    pm.expect(json.id).to.eql(101);
    pm.expect(json).to.have.property("name");
    pm.expect(json.price).to.be.a("number");
});

// Save a token for later requests
// pm.environment.set("token", pm.response.json().token);
```

### 9.2 Swagger / OpenAPI

- **OpenAPI** is a standard format for describing REST APIs (YAML or JSON).
- **Swagger UI** renders that description as interactive documentation.

**Topics**

- Read API documentation.
- Explore endpoints and schemas.
- Review request and response models.
- Execute requests directly from Swagger UI ("Try it out").
- Import an OpenAPI file into Postman to generate a collection.

### 9.3 REST Assured

REST Assured is a Java library for automating REST API tests.

**Topics**

- Send HTTP requests from Java.
- Validate status codes and response data.
- Work with JSON responses (JsonPath).
- Handle authentication.
- Integrate with **TestNG** or **JUnit**.
- Build a maintainable framework (base specs, utilities, test data, reporting).

**Sample (Given–When–Then style)**

```java
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class ProductApiTest {

    @Test
    public void getProductById() {
        given()
            .baseUri("https://api.shop.com")
            .header("Authorization", "Bearer " + token)
        .when()
            .get("/api/products/101")
        .then()
            .statusCode(200)
            .body("id", equalTo(101))
            .body("name", notNullValue())
            .time(lessThan(2000L));
    }
}
```

### 9.4 JMeter

Apache JMeter is used for an introduction to API performance testing.

**Topics**

- What performance testing is.
- **Load testing:** behaviour under expected user load.
- **Stress testing:** behaviour beyond normal limits.
- Reading metrics: response time, throughput, error rate.

### Tool summary

| Tool | Main purpose | Stage |
|---|---|---|
| Postman | Manual testing and basic automation | First |
| Swagger/OpenAPI | Documentation and exploration | Alongside Postman |
| REST Assured | Java-based API automation | After manual comfort |
| JMeter | Performance testing | Later / intro |

---

## 10. E-commerce API Example

Imagine testing an e-commerce application with product and login APIs.

| Functionality | HTTP method | Example endpoint |
|---|---|---|
| View all products | GET | `/api/products` |
| View product details | GET | `/api/products/101` |
| Add a product | POST | `/api/products` |
| Replace product details | PUT | `/api/products/101` |
| Delete a product | DELETE | `/api/products/101` |
| Login | POST | `/api/auth/login` |

> These are illustrative endpoints. Actual endpoints depend on the application's API design.

### Example: test the Product Details API

**Request**

```http
GET /api/products/101
Authorization: Bearer <token>
```

**Sample response**

```json
{
  "id": 101,
  "name": "Wireless Mouse",
  "price": 799.00,
  "inStock": true,
  "category": "Electronics"
}
```

**Possible checks**

| # | Check | Expected |
|---|---|---|
| 1 | Success status | `200 OK` |
| 2 | Product ID correct | `id == 101` |
| 3 | Product name correct | Matches the database/spec |
| 4 | Price correct | Matches the database/spec |
| 5 | Required fields present | `id`, `name`, `price`, `inStock` exist |
| 6 | Data types correct | `id` number, `name` string, `price` number, `inStock` boolean |
| 7 | Response time | Within the agreed limit (e.g., < 2 s) |
| 8 | Product doesn't exist (`/products/99999`) | `404 Not Found` with a clear message |
| 9 | Invalid ID format (`/products/abc`) | `400 Bad Request` |
| 10 | Missing/invalid token | `401 Unauthorized` (where authentication is required) |

### More scenarios

| Scenario | Expected |
|---|---|
| `POST /api/products` with valid body | `201 Created`; follow-up GET returns the new product |
| `POST /api/products` missing `name` | `400` or `422` with validation message |
| `PUT /api/products/101` with full valid body | `200` (or `204`); GET shows updated data |
| `DELETE /api/products/101` | `200` or `204`; GET afterwards returns `404` |
| `POST /api/auth/login` valid credentials | `200` with a token |
| `POST /api/auth/login` wrong password | `401` |
| Customer role calls `DELETE /api/products/101` | `403` |

**Core approach:** send a request → inspect the response → compare the **actual** result with the **expected** result.

---

## 11. Recommended Learning Sequence

Follow this order instead of trying to learn every topic at once.

| Phase | Topics |
|---|---|
| 1 | API Introduction and Fundamentals |
| 2 | Client–Server Architecture and API Communication |
| 3 | REST API and SOAP API |
| 4 | HTTP Methods, Headers, Parameters, and Status Codes |
| 5 | JSON and XML |
| 6 | Authentication and Authorization |
| 7 | API Testing Fundamentals |
| 8 | Postman Practical Testing |
| 9 | Swagger / OpenAPI |
| 10 | REST Assured with Java |

### Suggested priority

For a **Manual Testing → Automation Testing** path, give extra attention to:

1. API Fundamentals
2. HTTP Methods and Status Codes
3. REST API Architecture
4. JSON and Authentication
5. Postman Practical Testing
6. REST Assured with Java

> Do not rush into Postman before understanding how a client sends a request and receives a response.

### Practice APIs (free, no setup)

- **ReqRes** (`reqres.in`): users, login, CRUD sample
- **JSONPlaceholder** (`jsonplaceholder.typicode.com`): posts, comments, todos
- **Restful-Booker** (`restful-booker.herokuapp.com`): booking API with auth, built for testers
- **Petstore Swagger** (`petstore.swagger.io`): classic OpenAPI demo

---

## 12. Learning Checklist

### API basics

- [ ] What is an API?
- [ ] Why do applications use APIs?
- [ ] Client and server
- [ ] Request and response
- [ ] API communication flow
- [ ] API vs. Web Services

### API architecture and types

- [ ] HTTP and HTTPS
- [ ] REST architecture
- [ ] SOAP architecture
- [ ] REST vs. SOAP
- [ ] Synchronous vs. asynchronous
- [ ] API Gateway basics
- [ ] GraphQL, RPC, and WebSocket basics
- [ ] Public, Private, Partner, and Composite APIs

### HTTP fundamentals

- [ ] URL and URI
- [ ] HTTP methods
- [ ] Headers
- [ ] Path parameters
- [ ] Query parameters
- [ ] Request body
- [ ] Response body
- [ ] HTTP status codes

### Data formats and security

- [ ] JSON objects and arrays
- [ ] Nested JSON
- [ ] XML basics
- [ ] Authentication vs. Authorization
- [ ] API keys and Bearer tokens
- [ ] JWT basics
- [ ] OAuth 2.0 basics
- [ ] Access and refresh tokens
- [ ] RBAC

### Testing and tools

- [ ] API testing fundamentals
- [ ] Positive and negative testing
- [ ] Functional and integration testing
- [ ] Boundary value and regression testing
- [ ] Data validation and error handling
- [ ] Postman basics and practical requests
- [ ] Postman test scripts, variables, collections
- [ ] Swagger / OpenAPI
- [ ] REST Assured with Java
- [ ] JMeter basics

---

## Quick Revision Cheat Sheet

| Concept | One-line summary |
|---|---|
| API | Contract that lets applications talk to each other |
| Client / Server | Requester / responder |
| HTTP | Stateless request–response protocol |
| HTTPS | HTTP encrypted with TLS |
| REST | Resource-based style using HTTP methods, usually JSON |
| SOAP | Strict XML protocol with WSDL |
| GET / POST / PUT / PATCH / DELETE | Read / Create / Replace / Partially update / Remove |
| 2xx / 4xx / 5xx | Success / client error / server error |
| 401 vs. 403 | Not authenticated vs. not permitted |
| JSON | Objects `{}`, arrays `[]`, key-value pairs |
| Authentication / Authorization | Who you are / what you can do |
| JWT | Signed token: header.payload.signature |
| API Testing | Send request → inspect response → compare with expected |
| Postman → REST Assured | Manual first, then Java automation |

---

## Final Learning Advice

1. First understand **what an API is and why it is needed**.
2. Then understand **how requests and responses work**.
3. Learn **HTTP methods, headers, parameters, and status codes**.
4. Understand **JSON and authentication**.
5. Start practical API testing with **Postman**.
6. After becoming comfortable with manual API testing, move to **REST Assured with Java**.

**Goal:** Be able to explain API concepts, test APIs manually using Postman, and later automate those API tests using Java.
