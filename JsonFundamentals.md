# JSON Guide for QA Automation Engineers

A practical reference for everything you need to know about JSON and the operations you will perform on it while testing.

---

## 1. What is JSON?

**JSON** (JavaScript Object Notation) is a lightweight, text-based format for exchanging data. It is the standard format for REST APIs, config files, and test data.

Why QA engineers care:
- API requests and responses are almost always JSON
- Test data and expected results are stored as JSON
- Config files (e.g., `package.json`, `appsettings.json`, Postman collections) use JSON

---

## 2. JSON Syntax Rules

1. Data is in **key/value pairs**: `"name": "Alice"`
2. Keys **must** be strings in **double quotes**
3. Data is separated by **commas** (no trailing comma after the last item)
4. Objects use `{ }`, arrays use `[ ]`
5. **No comments** allowed
6. Strings must use **double quotes** (not single)

### Valid Example

```json
{
  "id": 101,
  "name": "Alice",
  "isActive": true,
  "balance": 2500.75,
  "address": null,
  "roles": ["admin", "tester"],
  "profile": {
    "email": "alice@example.com",
    "age": 30
  }
}
```

---

## 3. JSON Data Types

| Type    | Example                  | Notes                                  |
|---------|--------------------------|----------------------------------------|
| String  | `"hello"`                | Always double quotes                   |
| Number  | `42`, `3.14`, `-7`, `1e3`| No quotes, no leading zeros            |
| Boolean | `true`, `false`          | Lowercase only                         |
| Null    | `null`                   | Lowercase; means "no value"            |
| Object  | `{ "a": 1 }`             | Unordered key/value pairs              |
| Array   | `[1, 2, 3]`              | Ordered list, can mix types            |

> **Not supported:** `undefined`, `NaN`, `Infinity`, functions, dates (dates are stored as strings, e.g. `"2026-11-13T10:30:00Z"`).

---

## 4. Common Syntax Mistakes (Top Cause of Bugs)

| Wrong                          | Correct                          |
|--------------------------------|----------------------------------|
| `{ 'name': 'Bob' }`            | `{ "name": "Bob" }`              |
| `{ name: "Bob" }`              | `{ "name": "Bob" }`              |
| `{ "a": 1, }` (trailing comma) | `{ "a": 1 }`                     |
| `{ "active": True }`           | `{ "active": true }`             |
| `{ "a": 1 // comment }`        | Remove comments                  |
| `{ "age": 007 }`               | `{ "age": 7 }`                   |

---

## 5. Nested Structures

```json
{
  "orderId": "ORD-1001",
  "customer": {
    "id": 5,
    "name": "Alice"
  },
  "items": [
    { "sku": "A1", "qty": 2, "price": 10.5 },
    { "sku": "B2", "qty": 1, "price": 25.0 }
  ],
  "total": 46.0
}
```

Accessing values:
- `customer.name` -> `Alice`
- `items[0].sku` -> `A1`
- `items[1].price` -> `25.0`

---

## 6. Core JSON Operations in Testing

### 6.1 Parse (string -> object)
### 6.2 Serialize (object -> string)
### 6.3 Read / Access values
### 6.4 Update / Modify values
### 6.5 Add / Delete keys
### 6.6 Validate (syntax and schema)
### 6.7 Compare (expected vs actual)
### 6.8 Query (JSONPath)
### 6.9 Read from / Write to files

Examples below for the most common languages.

---

## 7. Operations by Language

### 7.1 JavaScript / TypeScript (Cypress, Playwright, Jest, WebdriverIO)

```javascript
// Parse
const obj = JSON.parse('{"name":"Alice","age":30}');

// Serialize
const str = JSON.stringify(obj);
const pretty = JSON.stringify(obj, null, 2); // pretty print

// Read
console.log(obj.name);
console.log(obj["age"]);
console.log(obj.items?.[0]?.sku); // optional chaining (safe access)

// Update / Add / Delete
obj.age = 31;
obj.city = "Pune";
delete obj.city;

// Check key exists
"name" in obj;                       // true
obj.hasOwnProperty("name");          // true

// Loop
Object.keys(obj).forEach(k => console.log(k, obj[k]));
Object.entries(obj).forEach(([k, v]) => console.log(k, v));

// Array operations
const names = users.map(u => u.name);
const admins = users.filter(u => u.role === "admin");
const found = users.find(u => u.id === 5);

// Deep clone (avoid mutating test data)
const copy = structuredClone(obj);
// or: JSON.parse(JSON.stringify(obj))

// Read / write file (Node.js)
const fs = require("fs");
const data = JSON.parse(fs.readFileSync("data.json", "utf-8"));
fs.writeFileSync("out.json", JSON.stringify(data, null, 2));

// Import directly
import data from "./data.json";
```

### 7.2 Python (pytest, requests, Robot Framework)

```python
import json

# Parse
obj = json.loads('{"name": "Alice", "age": 30}')

# Serialize
text = json.dumps(obj)
pretty = json.dumps(obj, indent=2, sort_keys=True)

# Read
print(obj["name"])
print(obj.get("city", "N/A"))  # safe access with default

# Update / Add / Delete
obj["age"] = 31
obj["city"] = "Pune"
del obj["city"]
obj.pop("age", None)

# Loop
for key, value in obj.items():
    print(key, value)

# Read / write file
with open("data.json") as f:
    data = json.load(f)

with open("out.json", "w") as f:
    json.dump(data, f, indent=2)

# With requests library
import requests
resp = requests.get("https://api.example.com/users/1")
body = resp.json()
assert resp.status_code == 200
assert body["id"] == 1

# POST with JSON body
requests.post(url, json={"name": "Alice"})
```

### 7.3 Java (REST Assured, Jackson, Gson)

```java
// REST Assured
given()
    .contentType("application/json")
    .body("{\"name\":\"Alice\"}")
.when()
    .post("/users")
.then()
    .statusCode(201)
    .body("name", equalTo("Alice"))
    .body("roles", hasItem("admin"))
    .body("items.size()", greaterThan(0));

// Extract
String name = response.jsonPath().getString("customer.name");
List<String> skus = response.jsonPath().getList("items.sku");

// Jackson - Parse
ObjectMapper mapper = new ObjectMapper();
JsonNode root = mapper.readTree(jsonString);
String name = root.get("name").asText();
int age = root.path("age").asInt();

// POJO <-> JSON
User user = mapper.readValue(jsonString, User.class);
String json = mapper.writeValueAsString(user);
```

### 7.4 C# (.NET - RestSharp, Newtonsoft, System.Text.Json)

```csharp
// Newtonsoft.Json
var obj = JObject.Parse(json);
string name = (string)obj["name"];
var token = obj.SelectToken("items[0].sku");

var user = JsonConvert.DeserializeObject<User>(json);
string text = JsonConvert.SerializeObject(user, Formatting.Indented);

// System.Text.Json
var user = JsonSerializer.Deserialize<User>(json);
string text = JsonSerializer.Serialize(user);
```

---

## 8. JSONPath - Querying JSON

JSONPath is like XPath for JSON. Used in Postman, REST Assured, JMeter, Karate, etc.

| Expression                      | Meaning                                  |
|---------------------------------|------------------------------------------|
| `$`                             | Root                                     |
| `$.name`                        | Property `name` of root                  |
| `$.customer.name`               | Nested property                          |
| `$.items[0]`                    | First array element                      |
| `$.items[-1]`                   | Last element                             |
| `$.items[*].sku`                | All `sku` values                         |
| `$..sku`                        | Recursive search for `sku` anywhere      |
| `$.items[?(@.price > 20)]`      | Filter: items with price over 20         |
| `$.items[?(@.sku=='A1')].qty`   | Filter by value, get `qty`               |
| `$.items[0:2]`                  | Slice: first two elements                |
| `$.items.length()`              | Array length (implementation dependent)  |

Sample data:

```json
{
  "items": [
    { "sku": "A1", "price": 10.5 },
    { "sku": "B2", "price": 25.0 }
  ]
}
```

- `$.items[*].sku` -> `["A1", "B2"]`
- `$.items[?(@.price > 20)].sku` -> `["B2"]`

---

## 9. JSON Schema Validation

A **JSON Schema** describes the expected structure of a JSON document. Use it to validate API responses automatically.

### Example Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "required": ["id", "name", "email"],
  "properties": {
    "id":    { "type": "integer", "minimum": 1 },
    "name":  { "type": "string", "minLength": 1, "maxLength": 50 },
    "email": { "type": "string", "format": "email" },
    "age":   { "type": "integer", "minimum": 0, "maximum": 120 },
    "role":  { "type": "string", "enum": ["admin", "user", "guest"] },
    "tags":  {
      "type": "array",
      "items": { "type": "string" },
      "uniqueItems": true
    }
  },
  "additionalProperties": false
}
```

### Useful Keywords

| Keyword                | Purpose                               |
|------------------------|---------------------------------------|
| `type`                 | Data type check                       |
| `required`             | Mandatory fields                      |
| `properties`           | Define fields                         |
| `enum`                 | Allowed values list                   |
| `pattern`              | Regex match for strings               |
| `minimum` / `maximum`  | Number range                          |
| `minLength`/`maxLength`| String length                         |
| `minItems`/`maxItems`  | Array size                            |
| `uniqueItems`          | No duplicates in array                |
| `additionalProperties` | Allow/deny unknown fields             |
| `format`               | email, date-time, uri, uuid, etc.     |

### Validate in Code

```javascript
// JavaScript (Ajv)
const Ajv = require("ajv");
const ajv = new Ajv();
const validate = ajv.compile(schema);
const valid = validate(response);
if (!valid) console.log(validate.errors);
```

```python
# Python (jsonschema)
from jsonschema import validate, ValidationError
try:
    validate(instance=response_json, schema=schema)
except ValidationError as e:
    print(e.message)
```

```java
// REST Assured
.then().assertThat().body(matchesJsonSchemaInClasspath("user-schema.json"));
```

---

## 10. API Testing with JSON

### 10.1 Typical Request

```http
POST /api/users HTTP/1.1
Host: api.example.com
Content-Type: application/json
Authorization: Bearer <token>

{
  "name": "Alice",
  "email": "alice@example.com"
}
```

### 10.2 Typical Response

```json
{
  "id": 101,
  "name": "Alice",
  "email": "alice@example.com",
  "createdAt": "2026-11-13T07:30:00Z"
}
```

### 10.3 What to Verify

- **Status code** (200, 201, 400, 401, 404, 500...)
- **Headers** (`Content-Type: application/json`)
- **Response time**
- **Body structure** (schema validation)
- **Field values** (correct data)
- **Data types** (number vs string)
- **Required fields present**
- **Null / empty handling**
- **Array size and order**
- **Error message format**

### 10.4 Postman Test Example

```javascript
pm.test("Status is 200", () => pm.response.to.have.status(200));

pm.test("Body is correct", () => {
  const json = pm.response.json();
  pm.expect(json.name).to.eql("Alice");
  pm.expect(json.roles).to.include("admin");
  pm.expect(json.items).to.be.an("array").that.is.not.empty;
});

// Save value for next request
pm.environment.set("userId", pm.response.json().id);
```

### 10.5 Cypress Example

```javascript
cy.request("POST", "/api/users", { name: "Alice" }).then((res) => {
  expect(res.status).to.eq(201);
  expect(res.body).to.have.property("id");
  expect(res.body.name).to.eq("Alice");
});
```

---

## 11. Comparing JSON (Expected vs Actual)

Key points:
- **Key order does not matter** in objects
- **Order matters** in arrays (unless you sort first)
- Ignore dynamic fields (`id`, `createdAt`, `token`, `timestamp`)

```javascript
// Deep equal (Chai / Jest)
expect(actual).to.deep.equal(expected);   // Chai
expect(actual).toEqual(expected);         // Jest

// Ignore dynamic fields
const { id, createdAt, ...rest } = actual;
expect(rest).toEqual(expectedWithoutDynamic);
```

```python
# Python
assert actual == expected

# Ignore fields
for f in ("id", "createdAt"):
    actual.pop(f, None)
```

Handy tools: **JSONCompare**, **jsondiffpatch**, **DeepDiff** (Python), **JSONAssert** (Java), VS Code "Compare" feature.

---

## 12. Test Scenarios Checklist for JSON Testing

### Positive Tests
- [ ] Valid request returns expected response
- [ ] All required fields present
- [ ] Correct data types
- [ ] Optional fields omitted
- [ ] Maximum allowed length / values

### Negative Tests
- [ ] Missing required field
- [ ] Wrong data type (`"age": "abc"`)
- [ ] `null` value in a non-nullable field
- [ ] Empty string `""`, empty array `[]`, empty object `{}`
- [ ] Invalid JSON syntax (missing brace, trailing comma)
- [ ] Extra / unknown fields
- [ ] Value out of range (negative, zero, huge)
- [ ] Invalid enum value
- [ ] Invalid format (email, date, UUID)
- [ ] Wrong `Content-Type` header

### Edge / Boundary Tests
- [ ] Min / max length strings
- [ ] Special characters: `" \ / \n \t`
- [ ] Unicode and emojis: `"नमस्ते"`, `"😀"`
- [ ] SQL injection / XSS strings in values
- [ ] Very large payload
- [ ] Deeply nested objects
- [ ] Large arrays (performance)
- [ ] Duplicate keys
- [ ] Number precision (`0.1 + 0.2`, large integers over 2^53)
- [ ] Date/time zones and formats

### Security
- [ ] Missing / invalid auth token
- [ ] Sensitive data not exposed in response (passwords, tokens)
- [ ] Mass assignment (sending extra fields like `"isAdmin": true`)

---

## 13. Handling Special Cases

### Null vs Missing vs Empty

| Case          | JSON                     | Meaning                    |
|---------------|--------------------------|----------------------------|
| Null          | `{ "a": null }`          | Key present, no value      |
| Missing       | `{ }`                    | Key absent                 |
| Empty string  | `{ "a": "" }`            | Key present, blank text    |
| Empty array   | `{ "a": [] }`            | Key present, no items      |
| Empty object  | `{ "a": {} }`            | Key present, no properties |

Always test how the API treats each of them.

### Dates
JSON has no date type. Standard format is **ISO 8601**: `"2026-11-13T10:30:00Z"`. Verify format and time zone.

### Numbers
- Integers vs decimals: `10` vs `10.0`
- Large IDs may lose precision in JavaScript, so consider strings
- Money: prefer integers (cents) or strings to avoid floating-point errors

### Escaping Special Characters

| Character   | Escape  |
|-------------|---------|
| Double quote| `\"`    |
| Backslash   | `\\`    |
| New line    | `\n`    |
| Tab         | `\t`    |
| Unicode     | `\u00E9`|

### Related Format: JSON Lines (NDJSON)
One JSON object per line, used for logs and streaming:
```
{"id":1,"event":"login"}
{"id":2,"event":"logout"}
```

---

## 14. Test Data Management with JSON

### Data-Driven Test Example

`testdata.json`
```json
[
  { "username": "alice", "password": "Pass@123", "expected": "success" },
  { "username": "alice", "password": "wrong",    "expected": "failure" },
  { "username": "",      "password": "Pass@123", "expected": "failure" }
]
```

```javascript
// Cypress / Jest style loop
testData.forEach(({ username, password, expected }) => {
  it(`login with ${username || "empty"} -> ${expected}`, () => {
    // run test using data
  });
});
```

```python
# pytest parametrize
import json, pytest
with open("testdata.json") as f:
    data = json.load(f)

@pytest.mark.parametrize("case", data)
def test_login(case):
    ...
```

### Best Practices
- Keep test data in separate files, not hard-coded
- Use one file per feature or per environment
- Never store real passwords or secrets (use env variables)
- Clone data before mutating
- Use meaningful names for files and keys

---

## 15. Config & Other JSON Files You Will See

| File                       | Purpose                              |
|----------------------------|--------------------------------------|
| `package.json`             | Node dependencies and scripts        |
| `cypress.config.json` / `playwright.config` | Test framework settings |
| `tsconfig.json`            | TypeScript config                    |
| `*.postman_collection.json`| Postman collections                  |
| `*.postman_environment.json`| Postman environments                |
| `appsettings.json`         | .NET app config                      |
| `.eslintrc.json`           | Linting rules                        |
| `swagger.json` / `openapi.json` | API specification               |
| `allure-results/*.json`    | Test reports                         |

---

## 16. Useful Tools

| Tool                     | Use                                             |
|--------------------------|-------------------------------------------------|
| **Postman / Insomnia**   | Manual and automated API testing                |
| **jq** (CLI)             | Filter and transform JSON in terminal           |
| **JSONLint**             | Validate JSON syntax                            |
| **jsonpath.com**         | Test JSONPath expressions online                |
| **jsonschema.net**       | Generate a schema from sample JSON              |
| **quicktype.io**         | Generate classes/types from JSON                |
| **VS Code**              | Format (`Shift+Alt+F`), fold, compare           |
| **json-server**          | Quick mock REST API from a JSON file            |
| **WireMock / MockServer**| Mock APIs with JSON responses                   |

### jq Quick Cheatsheet

```bash
cat data.json | jq .                      # pretty print
jq '.name' data.json                      # get field
jq '.items[].sku' data.json               # all skus
jq '.items | length' data.json            # count
jq '.items[] | select(.price > 20)' data.json   # filter
jq -r '.items[].sku' data.json            # raw output (no quotes)
jq 'keys' data.json                       # list keys
jq '.age = 31' data.json                  # update
jq 'del(.password)' data.json             # delete
```

---

## 17. Quick Cheat Sheet

| Task                    | JavaScript                 | Python                     |
|-------------------------|----------------------------|----------------------------|
| String to object        | `JSON.parse(s)`            | `json.loads(s)`            |
| Object to string        | `JSON.stringify(o)`        | `json.dumps(o)`            |
| Pretty print            | `JSON.stringify(o,null,2)` | `json.dumps(o, indent=2)`  |
| Read file               | `JSON.parse(fs.readFileSync(f))` | `json.load(open(f))` |
| Write file              | `fs.writeFileSync(f, str)` | `json.dump(o, open(f,"w"))`|
| Key exists              | `"k" in o`                 | `"k" in o`                 |
| Safe get                | `o?.a?.b`                  | `o.get("a", {}).get("b")`  |
| Delete key              | `delete o.k`               | `del o["k"]`               |
| Keys list               | `Object.keys(o)`           | `list(o.keys())`           |
| Deep clone              | `structuredClone(o)`       | `copy.deepcopy(o)`         |

---

## 18. Common Pitfalls to Avoid

1. Forgetting to parse the response (working with a string instead of an object)
2. Comparing arrays without considering order
3. Not handling `null` or missing keys, which causes `TypeError` / `KeyError`
4. Hard-coding dynamic values (ids, timestamps) in assertions
5. Mutating shared test data between tests
6. Trusting only the status code and skipping body validation
7. Ignoring `Content-Type` header
8. Number vs string mismatch (`1` vs `"1"`)
9. Not testing invalid or malformed JSON
10. Committing secrets/tokens inside JSON files

---

## 19. Learning Path

1. Learn syntax and data types (sections 2-5)
2. Practice parse / read / update in your test language (section 7)
3. Learn JSONPath (section 8)
4. Learn JSON Schema validation (section 9)
5. Build API tests in Postman, then automate in code (section 10)
6. Apply the test scenario checklist (section 12)
7. Master `jq` and tooling (section 16)

---

*Happy Testing!*
