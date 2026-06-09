# REST API Explained — HTTP Methods, Status Codes, Routing & More

If you are learning backend development, REST APIs are the first thing you need to master. Almost every web application uses REST APIs to communicate between the frontend and backend.

In this post, we will cover everything — from what an API is to routing in Express.js — with clear examples and diagrams.

---

## Table of Contents

1. What is an API?
2. What is a REST API?
3. REST Principles
4. HTTP Methods — GET, POST, PUT, PATCH, DELETE
5. CRUD Operations
6. HTTP Request and Response
7. HTTP Status Codes
8. Path Parameters vs Query Parameters
9. Headers and Request Body
10. REST API Routing in Express.js
11. REST vs GraphQL
12. Interview Questions

---

## 1. What is an API?

**API** stands for **Application Programming Interface**. It is a way for two applications to communicate and exchange data with each other.

Think of an API as a waiter in a restaurant:
- You (the client) place an order
- The waiter (API) takes your order to the kitchen
- The kitchen (server) prepares it and sends it back through the waiter

```
Client  →  Request  →  API  →  Server
Client  ←  Response ←  API  ←  Server
```

**Why do we use APIs?**
- To share data and services between applications
- To hide the internal complexity of a system
- To allow different applications to communicate with each other

---

## 2. What is a REST API?

**REST API** stands for **Representational State Transfer API**. It is a type of web API that follows REST principles and uses HTTP methods like GET, POST, PUT, PATCH, and DELETE to perform operations on resources.

### Examples of REST API endpoints:

```
GET    /users         → Get all users
GET    /users/1       → Get user with ID 1
POST   /users         → Create a new user
PUT    /users/1       → Update user with ID 1 completely
PATCH  /users/1       → Update only specific fields of user with ID 1
DELETE /users/1       → Delete user with ID 1
```

---

## 3. REST Principles

### Statelessness
Every request from the client must contain all the information needed to process it. The server does not store client session data between requests.

```
Request 1: POST /login  → includes username + password
Request 2: GET /profile → includes token (server does NOT remember Request 1)
```

### Client-Server Separation
The client and server are completely separate. The client handles the user interface, while the server handles data and business logic.

```
React App    → Client  (handles UI)
Node.js      → Server  (handles logic)
MongoDB      → Database (stores data)
```

### Resources
In REST, everything is treated as a **resource**, and each resource is identified by a unique URL.

```
/users
/products
/orders
/posts
```

> **Easy way to remember:** REST = Resource + HTTP Methods + Stateless Communication

---

## 4. HTTP Methods — GET, POST, PUT, PATCH, DELETE

HTTP methods are standard actions used by clients to communicate with a server. They define what operation should be performed on a resource.

---

### GET — Fetch data from the server

```js
app.get("/users", (req, res) => {
  res.send("Here are all the users");
});
```

```
Client                    Server
  |                          |
  |  GET /users              |
  |------------------------->|
  |                          |
  |  200 OK + user data      |
  |<-------------------------|
```

> GET is **idempotent** — calling it multiple times always returns the same result and never changes data.

---

### POST — Send data to the server (create something new)

```js
app.post("/orders", (req, res) => {
  res.status(201).send("Order placed successfully");
});
```

```
Client                    Server
  |                          |
  |  POST /orders            |
  |  Body: { item: "shoes" } |
  |------------------------->|
  |                          |
  |  201 Created             |
  |<-------------------------|
```

---

### PUT — Update an existing resource completely

```js
app.put("/users/123", (req, res) => {
  res.send("User updated completely");
});
```

```
Client                         Server
  |                               |
  |  PUT /users/123               |
  |  Body: { name, email, age }   |
  |------------------------------>|
  |                               |
  |  200 OK                       |
  |<------------------------------|
```

> PUT replaces the **entire resource**. If you only send `name`, the rest of the fields get wiped.

---

### PATCH — Update only specific fields

```js
app.patch("/users/123", (req, res) => {
  res.send("User email updated");
});
```

```
Client                       Server
  |                             |
  |  PATCH /users/123           |
  |  Body: { email: "new@x.com"}|
  |---------------------------->|
  |                             |
  |  200 OK                     |
  |<----------------------------|
```

> PATCH updates only the fields you send — other fields remain unchanged.

---

### DELETE — Remove a resource from the server

```js
app.delete("/orders/123", (req, res) => {
  res.send("Order deleted successfully");
});
```

```
Client                    Server
  |                          |
  |  DELETE /orders/123      |
  |------------------------->|
  |                          |
  |  200 OK                  |
  |<-------------------------|
```

---

### PUT vs PATCH — Quick Comparison

| | PUT | PATCH |
|---|---|---|
| **Updates** | Entire resource | Only specified fields |
| **If field missing** | Field gets deleted | Field stays unchanged |
| **Use when** | Replacing full object | Updating one or two fields |

---

### Can GET have a request body?

According to HTTP standards, GET requests should **not** have a request body. Data is sent through **query parameters** instead.

```
✅ GET /users?age=21&city=Agra
❌ GET /users  Body: { age: 21 }
```

---

## 5. CRUD Operations

Every REST API is built around **CRUD** — the four basic operations you can perform on data.

| Operation | HTTP Method | Example |
|---|---|---|
| **C**reate | POST | `POST /users` |
| **R**ead | GET | `GET /users/1` |
| **U**pdate | PUT / PATCH | `PUT /users/1` |
| **D**elete | DELETE | `DELETE /users/1` |

---

## 6. HTTP Request and Response

Every interaction between a client and server involves a **request** and a **response**.

### HTTP Request

Sent by the client to the server. It contains:

```
POST /users HTTP/1.1
Host: example.com
Content-Type: application/json
Authorization: Bearer token123

{
  "name": "Abhijeet",
  "email": "abhi@example.com"
}
```

| Part | Description | Example |
|---|---|---|
| **Method** | What action to perform | GET, POST, PUT |
| **URL** | Which resource | `/users`, `/orders` |
| **Headers** | Additional information | `Content-Type`, `Authorization` |
| **Body** | Data being sent (optional) | `{ "name": "Abhijeet" }` |

### HTTP Response

Sent by the server after processing the request. It contains:

```
HTTP/1.1 201 Created
Content-Type: application/json

{
  "message": "User created successfully",
  "userId": 42
}
```

| Part | Description |
|---|---|
| **Status Code** | Whether it succeeded or failed |
| **Headers** | Metadata about the response |
| **Body** | The actual data returned |

---

## 7. HTTP Status Codes

HTTP status codes are three-digit numbers returned by the server to indicate the result of a request.

### Categories

```
1xx → Informational  (request received, processing)
2xx → Success        (request completed successfully)
3xx → Redirection    (resource moved somewhere else)
4xx → Client Error   (something wrong with the request)
5xx → Server Error   (something went wrong on the server)
```

### Most Important Status Codes for Interviews

| Code | Meaning | When to use |
|---|---|---|
| **200** | OK | Successful GET, PUT, PATCH |
| **201** | Created | Successful POST (new resource created) |
| **204** | No Content | Successful DELETE (nothing to return) |
| **400** | Bad Request | Invalid input from client |
| **401** | Unauthorized | Not logged in / no token |
| **403** | Forbidden | Logged in but no permission |
| **404** | Not Found | Resource does not exist |
| **500** | Internal Server Error | Something crashed on the server |
| **503** | Service Unavailable | Server is down or overloaded |

### 401 vs 403 — Most Asked Interview Question

```
401 Unauthorized → You are not logged in
                   "Who are you? Show me your ID."

403 Forbidden    → You are logged in but not allowed
                   "I know who you are, but you cannot enter."
```

### 200 vs 201 — When to use which?

```
200 OK      → Use for successful GET, PUT, PATCH operations
201 Created → Use when a new resource is successfully created (POST)
```

---

## 8. Path Parameters vs Query Parameters

### Path Parameters

Used to **identify a specific resource**. Part of the URL path itself.

```
GET /users/101
         ^^^
         Path parameter — identifies user 101
```

```js
app.get("/users/:id", (req, res) => {
  const userId = req.params.id; // "101"
  res.send(`Fetching user ${userId}`);
});
```

### Query Parameters

Used to **filter, sort, or provide optional data**. Come after the `?` in the URL.

```
GET /users?age=21&city=Agra
          ^^^^^^^^^^^^^^^^^
          Query parameters — filter users
```

```js
app.get("/users", (req, res) => {
  const age = req.query.age;   // "21"
  const city = req.query.city; // "Agra"
  res.send(`Users aged ${age} from ${city}`);
});
```

### Path vs Query — Quick Comparison

| | Path Parameter | Query Parameter |
|---|---|---|
| **Example** | `/users/101` | `/users?age=21` |
| **Used for** | Identifying a specific resource | Filtering or sorting |
| **Required?** | Usually yes | Usually optional |
| **Access in Express** | `req.params` | `req.query` |

---

## 9. Headers and Request Body

### Headers

Headers provide **metadata** about the request or response — they do not contain the main data.

```
Content-Type: application/json     → tells server the body is JSON
Authorization: Bearer token123     → sends authentication token
Accept: application/json           → tells server what format client expects
```

### Request Body

The request body contains the **actual data** being sent to the server. Only POST, PUT, and PATCH requests typically have a body.

```js
// Reading the request body in Express
app.post("/users", (req, res) => {
  const { name, email } = req.body;
  res.status(201).send(`User ${name} created`);
});
```

---

## 10. REST API Routing in Express.js

Routing in Express.js determines how an application responds to different HTTP requests based on the URL and HTTP method.

```js
const express = require("express");
const app = express();

app.use(express.json()); // middleware to parse JSON body

// GET all users
app.get("/users", (req, res) => {
  res.status(200).json({ message: "All users" });
});

// GET user by ID
app.get("/users/:id", (req, res) => {
  const id = req.params.id;
  res.status(200).json({ message: `User ${id}` });
});

// POST create user
app.post("/users", (req, res) => {
  const { name, email } = req.body;
  res.status(201).json({ message: `User ${name} created` });
});

// PUT update user completely
app.put("/users/:id", (req, res) => {
  res.status(200).json({ message: "User updated completely" });
});

// PATCH update user partially
app.patch("/users/:id", (req, res) => {
  res.status(200).json({ message: "User partially updated" });
});

// DELETE user
app.delete("/users/:id", (req, res) => {
  res.status(200).json({ message: "User deleted" });
});

app.listen(3000, () => console.log("Server running on port 3000"));
```

### app.use() vs app.get()

| | `app.use()` | `app.get()` |
|---|---|---|
| **Type** | Middleware | Route handler |
| **HTTP method** | All methods | Only GET |
| **Use when** | Applying middleware globally | Handling GET requests |

### req.params vs req.query vs req.body

```js
// URL: GET /users/5?sort=asc
// Body: { "name": "Abhijeet" }

req.params.id    // "5"         ← from path parameter
req.query.sort   // "asc"       ← from query parameter
req.body.name    // "Abhijeet"  ← from request body
```

---

## 11. REST vs GraphQL

REST and GraphQL are two approaches for building APIs.

| Feature | REST | GraphQL |
|---|---|---|
| **Endpoints** | Multiple (`/users`, `/posts`) | Single (`/graphql`) |
| **Data fetching** | Fixed — server decides what to return | Flexible — client asks for exact fields |
| **Over-fetching** | Possible | Avoided |
| **Under-fetching** | Possible | Avoided |
| **Learning curve** | Easy | Moderate |
| **Caching** | Simple | More complex |
| **Best for** | Simple CRUD APIs | Complex data requirements |

> **For most interviews and projects — REST is what you need to know first.**

---

## Summary

| Concept | One Line Explanation |
|---|---|
| API | Bridge between two applications |
| REST API | API using HTTP methods on resources |
| GET | Fetch data — never changes data |
| POST | Create new resource |
| PUT | Replace entire resource |
| PATCH | Update specific fields only |
| DELETE | Remove a resource |
| 200 | Success |
| 201 | Created |
| 401 | Not authenticated |
| 403 | Authenticated but not authorized |
| 404 | Not found |
| 500 | Server error |
| req.params | Access path parameters |
| req.query | Access query parameters |
| req.body | Access request body data |

---

Happy coding! 🚀

#wemakedevs #javascript #backend #webdev #100DaysOfCode
