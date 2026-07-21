# 🔗 REST API using AJAX in Core PHP — Interview Notes (Zero to Advance)

> Complete guide — REST API concepts, AJAX (jQuery + Fetch), request/response flow, Core PHP API implementation, security, aur ek **Student REST API + AJAX CRUD** mini project (no page reload) feature-wise concept ke saath. Technical round + coding round dono ke liye ready.

---

## 📑 Table of Contents

1. [What is a REST API?](#1-what-is-a-rest-api)
2. [What is AJAX?](#2-what-is-ajax)
3. [Why REST API + AJAX Together?](#3-why-rest-api--ajax-together)
4. [HTTP Methods Deep Dive](#4-http-methods-deep-dive)
5. [HTTP Status Codes](#5-http-status-codes)
6. [Request-Response Flow (Architecture)](#6-request-response-flow-architecture)
7. [Setting Up a REST API in Core PHP](#7-setting-up-a-rest-api-in-core-php)
8. [Handling Different HTTP Methods in PHP](#8-handling-different-http-methods-in-php)
9. [AJAX Techniques — jQuery vs Fetch API](#9-ajax-techniques--jquery-vs-fetch-api)
10. [JSON — The Data Format](#10-json--the-data-format)
11. [CORS (Cross-Origin Resource Sharing)](#11-cors-cross-origin-resource-sharing)
12. [API Authentication](#12-api-authentication)
13. [Error Handling & Validation](#13-error-handling--validation)
14. [Security Best Practices](#14-security-best-practices)
15. [Advanced Concepts](#15-advanced-concepts)
16. [Mini Project: Student REST API + AJAX CRUD](#16-mini-project-student-rest-api--ajax-crud)
17. [Common Interview Questions](#17-common-interview-questions)
18. [Coding Round — Common Problems](#18-coding-round--common-problems)
19. [Quick Revision Cheatsheet](#19-quick-revision-cheatsheet)

---

## 1. What is a REST API?

**REST (Representational State Transfer)** — ek **architectural style** hai (protocol nahi) web services banane ke liye, jo HTTP methods use karta hai resources ko manage karne ke liye.

**API (Application Programming Interface)** — do systems ke beech communicate karne ka contract/interface — jaise frontend (browser/app) backend (server/database) se data mangta hai.

### REST Principles (6 Constraints)
1. **Client-Server** — frontend aur backend alag-alag independently develop ho sakte hain
2. **Stateless** — har request **apne aap mein complete** honi chahiye; server client ka pichla state yaad nahi rakhta (session state client manage karta hai, e.g. token bhejna har request mein)
3. **Cacheable** — responses cacheable ho sakte hain performance ke liye
4. **Uniform Interface** — consistent URL structure aur HTTP methods (`GET /students`, `POST /students`)
5. **Layered System** — client ko pata nahi hota ki wo directly server se baat kar raha hai ya kisi proxy/load-balancer se
6. **Code on Demand** (optional) — server client ko executable code bhi bhej sakta hai

### REST API Example Structure
```
GET    /api/students        → sab students list karo
GET    /api/students/5      → id=5 wale student ko lao
POST   /api/students        → naya student add karo
PUT    /api/students/5      → id=5 wale student ko fully update karo
DELETE /api/students/5      → id=5 wale student ko delete karo
```

**Interview Q: REST API "stateless" kyu hoti hai?**
Kyunki server kisi bhi request ke beech client ka data/state store nahi karta. Har request mein zaroori info (auth token, params) khud hi included honi chahiye. Isse server **scalable** banta hai — koi bhi server instance kisi bhi request ko handle kar sakta hai (load balancing aasan hota hai).

---

## 2. What is AJAX?

**AJAX (Asynchronous JavaScript And XML)** — browser mein **bina page reload kiye** server se data fetch/send karne ki technique hai. Naam mein XML hai but aaj-kal mostly **JSON** use hota hai.

### Core Idea
```
User action → JS request server ko bhejta hai (background mein) → Server response deta hai
→ JS DOM update karta hai (bina page reload ke)
```

### Kyu Zaroori Hai?
- Traditional form submit → **poora page reload** hota hai (slow, bad UX)
- AJAX → sirf **zaroori data** fetch hota hai, page reload nahi hota (fast, smooth UX — jaise Gmail, Facebook feed)

**Interview Q: AJAX ka full form kya hai, aur kya isme XML zaroori hai?**
Asynchronous JavaScript And XML. Naam historical hai — original implementations XML use karte the, but modern AJAX mein **JSON** standard hai (lighter, JS-native parsing).

---

## 3. Why REST API + AJAX Together?

| Without AJAX | With AJAX |
|---|---|
| Form submit → poora page reload | Sirf data update hota hai, page same rehta hai |
| Server HTML render karta hai | Server sirf **data (JSON)** deta hai, JS usse render karta hai |
| Tight coupling frontend-backend | Frontend/backend **decoupled** — same API mobile app bhi use kar sakti hai |

**Real-world flow**: AJAX **request bhejta hai** REST API endpoint ko → PHP **JSON response** return karta hai → JS us JSON se **DOM update** karta hai.

---

## 4. HTTP Methods Deep Dive

| Method | Purpose | Idempotent? | Body? |
|---|---|---|---|
| `GET` | Data fetch karna | ✅ Yes | ❌ No |
| `POST` | Naya resource create karna | ❌ No | ✅ Yes |
| `PUT` | Poora resource replace/update karna | ✅ Yes | ✅ Yes |
| `PATCH` | Resource ka **partial** update | ❌ No (technically) | ✅ Yes |
| `DELETE` | Resource delete karna | ✅ Yes | ❌ Usually no |

**Idempotent ka matlab**: Same request baar-baar bhejne se result same rehta hai. `GET` idempotent hai (baar baar fetch karne se data change nahi hota). `POST` idempotent nahi hai (baar baar call karne se **naye-naye records** create ho jayenge).

**Interview Q: `PUT` aur `PATCH` mein kya farak hai?**
`PUT` **poora object** replace karta hai (missing fields null ho sakte hain). `PATCH` sirf **diye gaye fields** update karta hai, baaki as-is rehte hain.

---

## 5. HTTP Status Codes

| Code | Meaning | Use Case |
|---|---|---|
| `200 OK` | Success | GET/PUT/DELETE success |
| `201 Created` | Resource created | POST success |
| `204 No Content` | Success, body empty | DELETE success (kabhi kabhi) |
| `400 Bad Request` | Client ki galti | Invalid input, missing fields |
| `401 Unauthorized` | Authentication missing/failed | Login required |
| `403 Forbidden` | Authenticated but permission nahi | Access denied |
| `404 Not Found` | Resource exist nahi karta | Wrong ID/URL |
| `405 Method Not Allowed` | Wrong HTTP method use hua | GET route par POST bheja |
| `409 Conflict` | Duplicate/conflict | Email already exists |
| `422 Unprocessable Entity` | Validation failed | Form validation errors |
| `500 Internal Server Error` | Server-side bug | Uncaught exception, DB error |

**Interview Q: `401` aur `403` mein kya farak hai?**
`401` — **"Tum ho kaun, pehchana nahi"** — authentication hi missing/invalid hai (login karo). `403` — **"Pehchana, but permission nahi"** — authenticated ho but us action ka access nahi hai.

---

## 6. Request-Response Flow (Architecture)

```
┌─────────────┐        AJAX Request         ┌──────────────┐        SQL Query        ┌──────────┐
│   Browser    │ ───────────────────────────► │  PHP (API)    │ ────────────────────────► │  MySQL    │
│  (JS/AJAX)   │                              │  Endpoint     │                          │  Database │
│              │ ◄─────────────────────────── │              │ ◄──────────────────────── │           │
└─────────────┘      JSON Response            └──────────────┘         Result Set          └──────────┘
       │
       ▼
  JS parses JSON
  → DOM update
  (no page reload)
```

### Step-by-Step Flow
1. **User action** — button click, form submit (JS `preventDefault()` se page reload roka jata hai)
2. **AJAX call** — JS `fetch()`/`$.ajax()` se HTTP request banata hai (method, URL, headers, body)
3. **PHP endpoint receive karta hai** — `$_SERVER['REQUEST_METHOD']` check karta hai, input parse karta hai (`php://input` for JSON body)
4. **Business logic + DB query** — validation, prepared statements se DB operation
5. **PHP JSON response bhejta hai** — `header('Content-Type: application/json')` + `json_encode()`
6. **JS response handle karta hai** — `.then()`/`success` callback mein JSON parse hota hai
7. **DOM update** — naya data page mein inject hota hai bina reload ke

---

## 7. Setting Up a REST API in Core PHP

### Simple Approach — Separate File per Endpoint
```
api/
├── students/
│   ├── index.php     → GET (list), POST (create)
│   └── single.php     → GET/PUT/DELETE by id
└── config/
    └── db.php
```

### Basic API Endpoint Structure
```php
<?php
// api/students/index.php
header('Content-Type: application/json');
require '../config/db.php';

$method = $_SERVER['REQUEST_METHOD'];

switch ($method) {
  case 'GET':
    $result = mysqli_query($conn, "SELECT * FROM students");
    $students = mysqli_fetch_all($result, MYSQLI_ASSOC);
    http_response_code(200);
    echo json_encode(["success" => true, "data" => $students]);
    break;

  case 'POST':
    $input = json_decode(file_get_contents("php://input"), true);
    // ... validation + insert logic
    http_response_code(201);
    echo json_encode(["success" => true, "message" => "Student created"]);
    break;

  default:
    http_response_code(405);
    echo json_encode(["success" => false, "message" => "Method not allowed"]);
}
```

**Interview Q: `file_get_contents("php://input")` kya karta hai aur kyu zaroori hai?**
`$_POST` sirf **form-encoded data** (`application/x-www-form-urlencoded` ya `multipart/form-data`) parse karta hai. Jab AJAX/JS **raw JSON body** (`Content-Type: application/json`) bhejta hai, `$_POST` khali rehta hai — isliye `php://input` se raw request body read karke `json_decode()` karna padta hai.

### Simple Routing (URL-based, without framework)
```php
<?php
// index.php — single entry point (front controller pattern)
header('Content-Type: application/json');

$uri = parse_url($_SERVER['REQUEST_URI'], PHP_URL_PATH);
$segments = explode('/', trim($uri, '/'));
// e.g. /api/students/5 → ['api', 'students', '5']

$resource = $segments[1] ?? '';
$id = $segments[2] ?? null;
$method = $_SERVER['REQUEST_METHOD'];

if ($resource === 'students') {
  if ($method === 'GET' && $id) {
    // single student fetch
  } elseif ($method === 'GET') {
    // list all
  } elseif ($method === 'POST') {
    // create
  } elseif ($method === 'PUT' && $id) {
    // update
  } elseif ($method === 'DELETE' && $id) {
    // delete
  }
}
```
`.htaccess` (Apache) se sab requests `index.php` par route karte hain:
```apache
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-f
RewriteRule ^ index.php [QSA,L]
```

---

## 8. Handling Different HTTP Methods in PHP

```php
// GET — query params
$page = $_GET['page'] ?? 1;

// POST — form data OR JSON
$formData = $_POST;                                    // form-encoded
$jsonData = json_decode(file_get_contents("php://input"), true);  // raw JSON

// PUT / DELETE — PHP mein $_PUT/$_DELETE superglobal EXIST NAHI karta!
if ($_SERVER['REQUEST_METHOD'] === 'PUT') {
  parse_str(file_get_contents("php://input"), $putData);   // agar form-encoded
  // ya JSON ho toh:
  $putData = json_decode(file_get_contents("php://input"), true);
}
```

**Interview Q (bahut common): PHP mein `PUT`/`DELETE` ka data kaise access karte ho, `$_POST` se kyu nahi milta?**
PHP sirf `POST` requests ke liye `$_POST` superglobal auto-populate karta hai. `PUT`/`DELETE`/`PATCH` requests ka body **manually** `php://input` stream se read karna padta hai, phir `json_decode()` ya `parse_str()` se parse karna padta hai.

---

## 9. AJAX Techniques — jQuery vs Fetch API

### jQuery AJAX
```js
$.ajax({
  url: '/api/students',
  method: 'GET',
  dataType: 'json',
  success: function(response) {
    console.log(response.data);
  },
  error: function(xhr) {
    console.error(xhr.responseJSON.message);
  }
});

// POST with JSON body
$.ajax({
  url: '/api/students',
  method: 'POST',
  contentType: 'application/json',
  data: JSON.stringify({ name: 'Nehal', email: 'a@b.com' }),
  success: function(res) { console.log(res); }
});
```

### Fetch API (Modern, native JS)
```js
// GET
fetch('/api/students')
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error(error));

// POST
fetch('/api/students', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ name: 'Nehal', email: 'a@b.com' })
})
  .then(res => res.json())
  .then(data => console.log(data));

// async/await syntax (cleaner)
async function getStudents() {
  try {
    const response = await fetch('/api/students');
    if (!response.ok) throw new Error('Request failed');
    const data = await response.json();
    console.log(data);
  } catch (err) {
    console.error(err);
  }
}
```

### jQuery AJAX vs Fetch API (common interview Q)
| jQuery `$.ajax()` | Fetch API |
|---|---|
| jQuery library dependency chahiye | Native browser API, no dependency |
| Callback-based (`success`/`error`) | Promise-based (`.then()`/`.catch()`) |
| `error` callback network errors + HTTP errors dono catch karta hai | `.catch()` sirf **network errors** catch karta hai — HTTP error (404, 500) khud "success" resolve hota hai, `response.ok` manually check karna padta hai |
| Automatic JSON stringify/parse (kuch cases mein) | Manual `JSON.stringify()`/`response.json()` |
| Older projects mein common | Modern projects mein preferred |

**Interview Q: `fetch()` mein 404 error `.catch()` mein kyu nahi jata?**
`fetch()` sirf **network failure** (no internet, DNS fail) par reject hota hai. HTTP error status codes (404, 500) ko wo "successful fetch" hi maanta hai (response mila, bas status bad hai) — isliye `response.ok` ya `response.status` manually check karna padta hai.

---

## 10. JSON — The Data Format

```php
// PHP → JSON
$data = ["id" => 1, "name" => "Nehal", "active" => true];
echo json_encode($data);
// {"id":1,"name":"Nehal","active":true}

// JSON → PHP
$json = '{"id":1,"name":"Nehal"}';
$arr = json_decode($json, true);   // associative array
$obj = json_decode($json);         // stdClass object

// Pretty print (debugging)
echo json_encode($data, JSON_PRETTY_PRINT);

// Error handling
$data = json_decode($invalidJson);
if (json_last_error() !== JSON_ERROR_NONE) {
  echo json_last_error_msg();
}
```

### Standard API Response Format (best practice)
```php
// Success
echo json_encode([
  "success" => true,
  "data" => $students,
  "message" => "Students fetched successfully"
]);

// Error
http_response_code(422);
echo json_encode([
  "success" => false,
  "message" => "Validation failed",
  "errors" => ["email" => "Invalid email format"]
]);
```
Consistent structure rakhna zaroori hai taaki frontend hamesha predictable format handle kar sake.

---

## 11. CORS (Cross-Origin Resource Sharing)

Jab AJAX request **different domain/port** par jaati hai (e.g. `localhost:3000` se `localhost:8000/api`), browser **security reason** se block kar deta hai — jab tak server explicitly allow na kare.

```php
header("Access-Control-Allow-Origin: *");   // sabko allow (development only)
header("Access-Control-Allow-Origin: https://myapp.com");  // specific domain (production)
header("Access-Control-Allow-Methods: GET, POST, PUT, DELETE, OPTIONS");
header("Access-Control-Allow-Headers: Content-Type, Authorization");

// Preflight request handle karna
if ($_SERVER['REQUEST_METHOD'] === 'OPTIONS') {
  http_response_code(200);
  exit;
}
```

**Interview Q: CORS kya hai aur "preflight request" kya hoti hai?**
CORS browser ka **Same-Origin Policy** enforce karne ka mechanism hai — cross-domain requests ko default block karta hai security ke liye. **Preflight** — jab request "simple" na ho (e.g. `PUT`/`DELETE`, ya custom headers ho), browser pehle ek `OPTIONS` request bhejta hai "permission maangne" — server agar allow kare (proper CORS headers) tab hi actual request jaati hai.

---

## 12. API Authentication

### API Key (simple approach)
```php
$apiKey = $_SERVER['HTTP_X_API_KEY'] ?? '';
if ($apiKey !== 'your-secret-key') {
  http_response_code(401);
  echo json_encode(["success" => false, "message" => "Invalid API key"]);
  exit;
}
```

### Token-based (Bearer Token / Session Token)
```php
$headers = getallheaders();
$authHeader = $headers['Authorization'] ?? '';
$token = str_replace('Bearer ', '', $authHeader);

// Token ko DB/cache se validate karo
$stmt = mysqli_prepare($conn, "SELECT * FROM users WHERE api_token = ?");
mysqli_stmt_bind_param($stmt, "s", $token);
mysqli_stmt_execute($stmt);
$user = mysqli_fetch_assoc(mysqli_stmt_get_result($stmt));

if (!$user) {
  http_response_code(401);
  echo json_encode(["success" => false, "message" => "Unauthorized"]);
  exit;
}
```

### JWT (JSON Web Token) — Concept Level
```
Header.Payload.Signature
```
- **Header** — algorithm info (base64 encoded)
- **Payload** — user data/claims (base64 encoded, NOT encrypted — sensitive data mat rakho)
- **Signature** — server secret key se sign hota hai, tamper-proof verification ke liye

**Interview Q: JWT stateless authentication kaise enable karta hai?**
Server ko session store nahi karni padti — sara zaroori data (user id, role, expiry) token ke andar hi encoded hota hai. Server sirf signature verify karta hai (secret key se) — isse horizontal scaling aasan hoti hai (koi bhi server instance verify kar sakta hai, session-sharing ki zaroorat nahi).

AJAX se token bhejna:
```js
fetch('/api/students', {
  headers: { 'Authorization': 'Bearer ' + localStorage.getItem('token') }
});
```

---

## 13. Error Handling & Validation

```php
<?php
header('Content-Type: application/json');

try {
  $input = json_decode(file_get_contents("php://input"), true);

  if (empty($input['name'])) {
    http_response_code(422);
    echo json_encode(["success" => false, "message" => "Name is required"]);
    exit;
  }

  if (!filter_var($input['email'], FILTER_VALIDATE_EMAIL)) {
    http_response_code(422);
    echo json_encode(["success" => false, "message" => "Invalid email"]);
    exit;
  }

  // ... DB operation
  $stmt = mysqli_prepare($conn, "INSERT INTO students (name, email) VALUES (?, ?)");
  mysqli_stmt_bind_param($stmt, "ss", $input['name'], $input['email']);

  if (!mysqli_stmt_execute($stmt)) {
    throw new Exception(mysqli_error($conn));
  }

  http_response_code(201);
  echo json_encode(["success" => true, "message" => "Created"]);

} catch (Exception $e) {
  http_response_code(500);
  echo json_encode(["success" => false, "message" => "Server error: " . $e->getMessage()]);
}
```

### Frontend Error Handling
```js
fetch('/api/students', { method: 'POST', body: JSON.stringify(data) })
  .then(async response => {
    const result = await response.json();
    if (!response.ok) {
      throw new Error(result.message || 'Something went wrong');
    }
    return result;
  })
  .then(data => console.log('Success:', data))
  .catch(err => alert(err.message));
```

---

## 14. Security Best Practices

1. **Prepared statements** — SQL injection prevention, har DB query mein
2. **Input validation on server** — client-side validation kabhi trust mat karo (Postman se koi bhi bypass kar sakta hai)
3. **`json_decode()` output ko sanitize karo** before use — `htmlspecialchars()` output ke waqt
4. **Rate limiting** — brute-force/abuse prevent karne ke liye (per IP/token request count limit karo — theory level)
5. **HTTPS mandatory** — production APIs mein, especially agar tokens/passwords transmit ho rahe hon
6. **CORS specific origins** — production mein `*` (sab allow) use mat karo, specific domains whitelist karo
7. **Token expiry** — API tokens/JWT ki expiry rakho, refresh mechanism implement karo
8. **Never expose stack traces** — production mein raw error messages (`mysqli_error()`) client ko mat bhejo, generic message do, actual error server logs mein rakho

**Interview Q: Production API mein detailed DB error client ko kyu nahi dikhana chahiye?**
Attacker ko internal structure (table names, column names, query logic) reveal ho sakta hai — ye **information disclosure** vulnerability hai. Generic message client ko do ("Something went wrong"), actual error server-side log file mein likho.

---

## 15. Advanced Concepts

### Debouncing AJAX Calls (Search-as-you-type)
```js
let timer;
$('#search').on('keyup', function() {
  clearTimeout(timer);
  const query = $(this).val();
  timer = setTimeout(() => {
    fetch(`/api/students?search=${query}`)
      .then(res => res.json())
      .then(data => renderResults(data));
  }, 500);   // 500ms wait — har keystroke par request nahi jati
});
```
**Interview Q: Debouncing kyu zaroori hai search feature mein?**
Bina debounce ke, har keystroke par ek API call fire hoti hai — server par unnecessary load aur network waste hota hai. Debounce ensure karta hai ki request tabhi jaye jab user typing **rok** de (kuch milliseconds ke liye).

### Loading States & UX
```js
$('#loader').show();
fetch('/api/students')
  .then(res => res.json())
  .then(data => {
    $('#loader').hide();
    renderData(data);
  })
  .catch(() => $('#loader').hide());
```

### Optimistic UI Update
```js
// UI turant update karo, API call background mein confirm kare
function deleteStudent(id) {
  $(`#row-${id}`).fadeOut();   // pehle hi hata do UI se
  fetch(`/api/students/${id}`, { method: 'DELETE' })
    .catch(() => {
      $(`#row-${id}`).fadeIn();  // fail ho toh wapas dikhado
      alert('Delete failed');
    });
}
```

### Pagination via AJAX
```js
function loadPage(page) {
  fetch(`/api/students?page=${page}&limit=10`)
    .then(res => res.json())
    .then(data => {
      renderTable(data.students);
      renderPagination(data.totalPages, page);
    });
}
```

### AbortController — Cancel Previous Requests
```js
let controller;
function search(query) {
  if (controller) controller.abort();   // purani request cancel karo
  controller = new AbortController();

  fetch(`/api/students?search=${query}`, { signal: controller.signal })
    .then(res => res.json())
    .then(data => renderResults(data));
}
```
Fast typing mein purani (stale) requests cancel karna zaroori hai, warna race condition ho sakti hai (purani response baad mein aake naye result ko overwrite kar de).

---

## 16. Mini Project: Student REST API + AJAX CRUD

> **Tech stack**: Core PHP (Procedural, REST endpoints) + MySQLi + jQuery AJAX — poora CRUD **bina page reload** ke.

### Folder Structure
```
student-api/
├── api/
│   ├── db.php
│   ├── students.php     # GET (list), POST (create)
│   └── student.php      # GET/PUT/DELETE by id
├── index.html            # Frontend — table + form
└── js/
    └── app.js             # AJAX logic
```

### `api/db.php`
```php
<?php
header('Content-Type: application/json');
header('Access-Control-Allow-Origin: *');
$conn = mysqli_connect('localhost', 'root', '', 'school_db');
if (!$conn) {
  http_response_code(500);
  echo json_encode(["success" => false, "message" => "DB connection failed"]);
  exit;
}
```

### `api/students.php` — List (GET) + Create (POST)
```php
<?php
require 'db.php';
$method = $_SERVER['REQUEST_METHOD'];

if ($method === 'GET') {
  $result = mysqli_query($conn, "SELECT * FROM students ORDER BY id DESC");
  $students = mysqli_fetch_all($result, MYSQLI_ASSOC);
  echo json_encode(["success" => true, "data" => $students]);
}

elseif ($method === 'POST') {
  $input = json_decode(file_get_contents("php://input"), true);

  $errors = [];
  if (empty($input['name'])) $errors[] = "Name required";
  if (!filter_var($input['email'] ?? '', FILTER_VALIDATE_EMAIL)) $errors[] = "Valid email required";

  if (!empty($errors)) {
    http_response_code(422);
    echo json_encode(["success" => false, "errors" => $errors]);
    exit;
  }

  $stmt = mysqli_prepare($conn, "INSERT INTO students (name, email, course) VALUES (?, ?, ?)");
  mysqli_stmt_bind_param($stmt, "sss", $input['name'], $input['email'], $input['course']);

  if (mysqli_stmt_execute($stmt)) {
    http_response_code(201);
    echo json_encode(["success" => true, "message" => "Student created", "id" => mysqli_insert_id($conn)]);
  } else {
    http_response_code(500);
    echo json_encode(["success" => false, "message" => "Insert failed"]);
  }
}
```

### `api/student.php` — Update (PUT) + Delete (DELETE) by ID
```php
<?php
require 'db.php';
$method = $_SERVER['REQUEST_METHOD'];
$id = (int)($_GET['id'] ?? 0);

if ($method === 'PUT') {
  $input = json_decode(file_get_contents("php://input"), true);

  $stmt = mysqli_prepare($conn, "UPDATE students SET name=?, email=?, course=? WHERE id=?");
  mysqli_stmt_bind_param($stmt, "sssi", $input['name'], $input['email'], $input['course'], $id);

  if (mysqli_stmt_execute($stmt)) {
    echo json_encode(["success" => true, "message" => "Student updated"]);
  } else {
    http_response_code(500);
    echo json_encode(["success" => false, "message" => "Update failed"]);
  }
}

elseif ($method === 'DELETE') {
  $stmt = mysqli_prepare($conn, "DELETE FROM students WHERE id = ?");
  mysqli_stmt_bind_param($stmt, "i", $id);

  if (mysqli_stmt_execute($stmt)) {
    echo json_encode(["success" => true, "message" => "Student deleted"]);
  } else {
    http_response_code(500);
    echo json_encode(["success" => false, "message" => "Delete failed"]);
  }
}
```

### `index.html` — Frontend
```html
<table id="studentTable">
  <thead><tr><th>Name</th><th>Email</th><th>Course</th><th>Actions</th></tr></thead>
  <tbody></tbody>
</table>

<form id="studentForm">
  <input type="hidden" id="studentId">
  <input type="text" id="name" placeholder="Name" required>
  <input type="email" id="email" placeholder="Email" required>
  <select id="course">
    <option value="MCA">MCA</option>
    <option value="BCA">BCA</option>
  </select>
  <button type="submit">Save</button>
</form>

<script src="https://code.jquery.com/jquery-3.7.1.min.js"></script>
<script src="js/app.js"></script>
```

### `js/app.js` — Full AJAX CRUD Logic
```js
const API = '/api';

function loadStudents() {
  $.ajax({
    url: `${API}/students.php`,
    method: 'GET',
    success: function(res) {
      let rows = '';
      res.data.forEach(s => {
        rows += `<tr>
          <td>${s.name}</td><td>${s.email}</td><td>${s.course}</td>
          <td>
            <button onclick="editStudent(${s.id}, '${s.name}', '${s.email}', '${s.course}')">Edit</button>
            <button onclick="deleteStudent(${s.id})">Delete</button>
          </td>
        </tr>`;
      });
      $('#studentTable tbody').html(rows);
    }
  });
}

$('#studentForm').on('submit', function(e) {
  e.preventDefault();   // page reload rokna — AJAX ka core requirement

  const id = $('#studentId').val();
  const payload = {
    name: $('#name').val(),
    email: $('#email').val(),
    course: $('#course').val()
  };

  const isEdit = id !== '';
  $.ajax({
    url: isEdit ? `${API}/student.php?id=${id}` : `${API}/students.php`,
    method: isEdit ? 'PUT' : 'POST',
    contentType: 'application/json',
    data: JSON.stringify(payload),
    success: function(res) {
      if (res.success) {
        $('#studentForm')[0].reset();
        $('#studentId').val('');
        loadStudents();   // list refresh — bina page reload ke
      } else {
        alert(res.message || (res.errors ? res.errors.join(', ') : 'Error'));
      }
    },
    error: function(xhr) {
      alert('Something went wrong: ' + xhr.responseJSON?.message);
    }
  });
});

function editStudent(id, name, email, course) {
  $('#studentId').val(id);
  $('#name').val(name);
  $('#email').val(email);
  $('#course').val(course);
}

function deleteStudent(id) {
  if (!confirm('Are you sure?')) return;
  $.ajax({
    url: `${API}/student.php?id=${id}`,
    method: 'DELETE',
    success: function(res) {
      if (res.success) loadStudents();
    }
  });
}

$(document).ready(loadStudents);
```

### Concept Breakdown (interview mein pucha jata hai)
1. **Single Page, No Reload** — `e.preventDefault()` traditional form submission rokta hai; sara data flow AJAX se hota hai
2. **Same URL, different `method`** decide karta hai kaunsa CRUD operation hoga (`students.php` GET=list, POST=create; `student.php?id=` PUT=update, DELETE=delete) — ye RESTful design ka core idea hai
3. **`contentType: 'application/json'`** — jQuery ko batata hai ki data JSON format mein bheja ja raha hai, PHP ko `php://input` se parse karna padega (`$_POST` nahi milega)
4. **Response `success` flag pattern** — frontend hamesha ek consistent structure expect karta hai, isliye error/success handling predictable rehti hai
5. **`loadStudents()` re-call after every mutation** — Create/Update/Delete ke baad list ko refresh karna simplest tareeka hai UI ko sync rakhne ka (advanced apps mein sirf changed row update karte hain, pura list re-fetch nahi)
6. **Inline `onclick` with data** — chhote projects mein simple hai, but bade projects mein event delegation (`$(document).on('click', '.edit-btn', ...)`) better practice hai (especially dynamic content ke liye)

---

## 17. Common Interview Questions

**Q1. REST API kya hai?**
Ek architectural style jo HTTP methods (GET, POST, PUT, DELETE) use karke resources manage karta hai — stateless, client-server based communication.

**Q2. AJAX kya hai aur ye kaise kaam karta hai?**
Browser se bina page reload kiye server ko background request bhejne ki technique. JS `fetch()`/`XMLHttpRequest`/jQuery `$.ajax()` se request bhejta hai, response (usually JSON) receive karke DOM update karta hai.

**Q3. PHP mein REST API request ka data kaise receive karte ho?**
`GET` params — `$_GET`. Form-encoded `POST` — `$_POST`. Raw JSON body (kisi bhi method mein) — `file_get_contents("php://input")` phir `json_decode()`.

**Q4. `$_SERVER['REQUEST_METHOD']` ka use kya hai API mein?**
Yahi decide karta hai ki incoming request GET/POST/PUT/DELETE mein se kya hai, jisse endpoint appropriate logic (list/create/update/delete) execute kar sake.

**Q5. API response mein proper HTTP status codes kyu zaroori hain?**
Client (frontend/mobile app/Postman) status code dekh ke hi decide karta hai request successful thi ya nahi, bina response body parse kiye bhi basic decision le sakta hai. `200` vs `404` vs `500` alag-alag handling trigger karte hain frontend mein.

**Q6. CORS error kab aata hai aur kaise fix karte ho?**
Jab frontend aur API alag origin (domain/port) par hon, browser Same-Origin Policy ke tehat block kar deta hai. Fix: server response mein `Access-Control-Allow-Origin` header add karo.

**Q7. API authentication kaise implement karoge?**
API key header check karo (simple), ya Bearer token/JWT verify karo Authorization header se — dono cases mein request process karne se pehle validate karna zaroori hai.

**Q8. jQuery AJAX aur Fetch API mein kya farak hai?**
jQuery callback-based hai aur dependency chahiye, Fetch native promise-based API hai. Fetch mein HTTP errors (404/500) manually `response.ok` check karke handle karne padte hain, jQuery ka `error` callback automatically handle kar leta hai.

**Q9. REST API "stateless" kyu hoti hai, iska fayda kya hai?**
Server request ke beech koi state store nahi karta — har request self-contained hoti hai. Isse horizontal scaling aasan hoti hai (koi bhi server instance kisi bhi request handle kar sakta hai).

**Q10. AJAX request fail ho jaye toh kaise handle karoge?**
`error`/`.catch()` callback mein user-friendly message dikhao, retry option do agar applicable ho, aur console/logs mein actual error track karo debugging ke liye.

**Q11. API mein pagination kyu zaroori hai?**
Agar sara data ek saath bhej diya (jaise 10,000 records), response bahut bada aur slow ho jayega. Pagination (`LIMIT`/`OFFSET`) se sirf zaroori chunk bhejte hain, performance aur UX dono better hoti hai.

**Q12. `json_encode()`/`json_decode()` mein common gotchas kya hain?**
`json_decode($json, true)` associative array deta hai, bina `true` ke stdClass object. Invalid JSON par `json_decode()` `null` return karta hai — `json_last_error()` se check karna chahiye.

---

## 18. Coding Round — Common Problems

### 1. Build a Simple REST Endpoint (GET all + GET by ID)
```php
<?php
header('Content-Type: application/json');
require 'db.php';

$id = $_GET['id'] ?? null;

if ($id) {
  $stmt = mysqli_prepare($conn, "SELECT * FROM students WHERE id = ?");
  mysqli_stmt_bind_param($stmt, "i", $id);
  mysqli_stmt_execute($stmt);
  $student = mysqli_fetch_assoc(mysqli_stmt_get_result($stmt));

  if ($student) {
    echo json_encode(["success" => true, "data" => $student]);
  } else {
    http_response_code(404);
    echo json_encode(["success" => false, "message" => "Not found"]);
  }
} else {
  $result = mysqli_query($conn, "SELECT * FROM students");
  echo json_encode(["success" => true, "data" => mysqli_fetch_all($result, MYSQLI_ASSOC)]);
}
```

### 2. Validate JSON Input and Return Proper Errors
```php
$input = json_decode(file_get_contents("php://input"), true);

if (json_last_error() !== JSON_ERROR_NONE) {
  http_response_code(400);
  echo json_encode(["success" => false, "message" => "Invalid JSON"]);
  exit;
}

$required = ['name', 'email'];
$missing = array_filter($required, fn($field) => empty($input[$field]));

if (!empty($missing)) {
  http_response_code(422);
  echo json_encode(["success" => false, "message" => "Missing: " . implode(', ', $missing)]);
  exit;
}
```

### 3. Write a Debounce Function (Vanilla JS — common asked)
```js
function debounce(func, delay) {
  let timer;
  return function(...args) {
    clearTimeout(timer);
    timer = setTimeout(() => func.apply(this, args), delay);
  };
}

const searchAPI = debounce((query) => {
  fetch(`/api/students?search=${query}`);
}, 500);
```

### 4. Implement Simple Rate Limiting (theory + basic code)
```php
session_start();
$now = time();
$window = 60;    // 60 seconds
$maxRequests = 10;

if (!isset($_SESSION['requests'])) $_SESSION['requests'] = [];
$_SESSION['requests'] = array_filter($_SESSION['requests'], fn($t) => $t > $now - $window);

if (count($_SESSION['requests']) >= $maxRequests) {
  http_response_code(429);
  echo json_encode(["success" => false, "message" => "Too many requests"]);
  exit;
}
$_SESSION['requests'][] = $now;
```

### 5. Write a Function to Build Query String from an Object (JS)
```js
function buildQueryString(params) {
  return Object.keys(params)
    .filter(key => params[key] !== '' && params[key] != null)
    .map(key => `${encodeURIComponent(key)}=${encodeURIComponent(params[key])}`)
    .join('&');
}
buildQueryString({ search: 'nehal', page: 1 });  // "search=nehal&page=1"
```

**Tip**: Interviewers aksar pucheta hai — "AJAX request se pehle same field validate kaise karoge, aur duplicate submission kaise roken?" — Answer: submit button disable karo request ke duration ke liye, aur server-side bhi duplicate check (e.g. email unique constraint) rakho.

---

## 19. Quick Revision Cheatsheet

- ✅ REST = architectural style, stateless, HTTP methods se resources manage karta hai
- ✅ AJAX = bina page reload ke background request-response
- ✅ `$_POST` sirf form-encoded data ke liye, JSON body → `php://input` + `json_decode()`
- ✅ `PUT`/`DELETE` ka data bhi `php://input` se hi milta hai, koi `$_PUT` superglobal nahi hota
- ✅ Status codes: `200` OK, `201` Created, `401` Unauthorized, `404` Not Found, `422` Validation, `500` Server Error
- ✅ CORS error = cross-origin block → `Access-Control-Allow-Origin` header se fix
- ✅ Fetch API — HTTP errors ke liye `response.ok` manually check karo (`.catch()` sirf network fail ke liye)
- ✅ `e.preventDefault()` — form ka default reload rokta hai, AJAX flow ke liye mandatory
- ✅ Debounce — search-as-you-type mein excessive API calls rokta hai
- ✅ Auth — API key (simple) ya Bearer token/JWT (scalable, stateless)
- ✅ Consistent JSON response format (`success`, `data`, `message`) — frontend-backend contract

---

*Notes prepared for interview + coding round revision — REST API using AJAX in Core PHP, part of Full Stack PHP Laravel Developer prep series.*
