# Authentication Explained — JWT, Sessions, Cookies & More

Authentication is one of the most important concepts in backend development — and one of the most commonly asked topics in interviews.

In this post, we will cover everything from the basics to the advanced concepts, with real-world analogies, flow diagrams, and interview-ready explanations.

---

## Table of Contents

1. What is Authentication?
2. Authentication vs Authorization
3. Types of Authentication
4. Traditional Authentication Flow
5. What are Sessions?
6. Session-Based Authentication
7. What is JWT?
8. JWT Structure
9. JWT Authentication Flow
10. Cookies and Their Role in Authentication
11. JWT in Cookies vs Local Storage
12. Access Tokens and Refresh Tokens
13. Refresh Token Rotation
14. API Keys vs JWT vs Sessions
15. CORS in Authentication
16. Interview Questions

---

## 1. What is Authentication?

**Authentication** is the process of verifying the identity of a user or system before allowing access.

It uses credentials such as:
- Username and password
- OTP (One Time Password)
- Biometrics (fingerprint, face ID)
- Security tokens

> Simple definition: Authentication answers the question — **"Who are you?"**

---

## 2. Authentication vs Authorization

This is one of the most commonly asked interview questions. They sound similar but are completely different.

| | Authentication | Authorization |
|---|---|---|
| **Question** | Who are you? | What are you allowed to do? |
| **Example** | Logging into a website | Accessing the admin dashboard |
| **Happens** | First | After authentication |

### Real World Analogy — Hotel

> **Authentication** is checking in at the front desk. You show your ID, they confirm you are a real guest, and they hand you a key card. That key card is your JWT — proof that you have been verified.
>
> **Authorization** is what that key card actually opens. Your key card opens room 204 and the gym. It does not open the penthouse suite or the staff office. Even though you are a legitimate guest (authenticated), you only have permission for certain doors (authorized).

---

## 3. Types of Authentication

### 1. Token-Based Authentication (JWT)
- A stateless approach where the server verifies credentials once and generates an encrypted token
- The token is sent back to the client, which attaches it to the header of every subsequent API request
- **Storage:** Memory, HTTP cookies, or localStorage on the client side
- **Best for:** Single Page Applications (SPAs), mobile apps, and microservice architecture

### 2. Session-Based Authentication
- A traditional stateful approach where the server manages the user state in its data store
- **Storage:** Server side — in-memory stores, relational databases, or key-value caches
- **Best for:** Server-rendered applications and monolithic web apps

### 3. Third-Party OAuth and OpenID Connect
- Login with Google, GitHub, Facebook
- The third party handles identity verification
- **Best for:** Apps that want to avoid managing passwords

### 4. Passwordless and Biometric Authentication
- Magic links sent to email, OTP via SMS, fingerprint or face ID
- **Best for:** Mobile apps and high-security applications

---

## 4. Traditional Authentication Flow

```
User enters credentials (username + password)
            ↓
    Request sent to server
            ↓
  Server checks the database
            ↓
    ┌───────────────────┐
    │  Credentials OK?  │
    └───────────────────┘
       ↓           ↓
      YES           NO
       ↓             ↓
 User is        Return error
authenticated   "Invalid credentials"
       ↓
  Allow access
```

---

## 5. What are Sessions?

HTTP is a **stateless protocol** — meaning the server does not remember you between requests.

```
Request 1 → Server knows you
Request 2 → Server forgets you
Request 3 → Server forgets you again
```

A **session** solves this problem. It is a server-side mechanism used to maintain a user's authenticated state across multiple HTTP requests.

- Session **data** is stored on the server
- The browser stores only a **Session ID** in a cookie

### Advantages
- More secure — actual data stays on the server
- Easy to log users out
- Easy to invalidate sessions instantly

### Disadvantages
- Server must store session data for every user
- Harder to scale across multiple servers without shared session storage

### Real World Analogy — Library

> You show your ID at the library.
> The librarian gives you a **membership card** with a unique number.
> Every time you visit, you show that card.
> The librarian checks the number and knows who you are.
>
> - Membership card = Session ID (stored in your browser cookie)
> - Librarian's records = Session data stored on the server

---

## 6. Session-Based Authentication Flow

```
User sends login credentials
            ↓
  Server verifies credentials
            ↓
  Server creates a Session ID
  and stores session data
            ↓
  Server sends Session ID
  to browser as a cookie
            ↓
  Browser stores the cookie
            ↓
  Every future request automatically
  includes the Session ID cookie
            ↓
  Server looks up the Session ID
  and identifies the user
```

---

## 7. What is JWT (JSON Web Token)?

**JWT** is a secure, compact token used to verify and transfer user information between the client and the server.

It is commonly used for authentication and authorization in web applications — allowing users to access protected resources without repeatedly providing credentials.

### Key Features

- **Stateless** — stores user information inside the token itself, so the server does not need to store session data
- **Secure** — uses a digital signature to verify the token has not been tampered with
- **Compact** — three parts separated by dots: `xxxxx.yyyyy.zzzzz`
- **Widely supported** — works across all programming languages

---

## 8. JWT Structure

A JWT has three parts — **Header**, **Payload**, and **Signature** — separated by dots.

```
eyJhbGciOiJIUzI1NiJ9  .  eyJ1c2VySWQiOjEyM30  .  SflKxwRJSMeKKF2QT4fw
        ↑                          ↑                         ↑
     Header                    Payload                   Signature
```

### Part 1 — Header

Contains metadata about the token — the signing algorithm and token type.

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

- `alg` — Algorithm used for signing (e.g. HS256, RS256)
- `typ` — Token type, always "JWT"

### Part 2 — Payload

Contains information about the user (called **claims**) and additional data like timestamps.

```json
{
  "userId": 123,
  "role": "admin",
  "exp": 1716239022
}
```

**Common claim types:**

| Claim | Full Name | Meaning |
|---|---|---|
| `iss` | Issuer | Who issued the token |
| `sub` | Subject | The user the token is about |
| `aud` | Audience | Intended recipient |
| `exp` | Expiration | When the token expires |
| `iat` | Issued At | When the token was created |
| `nbf` | Not Before | When the token becomes valid |

> **Important interview question:** What should you store in a JWT?
> - ✅ Store: User ID, Email, Role
> - ❌ Never store: Passwords, credit card details, sensitive personal information

### Part 3 — Signature

The signature verifies that the token has not been tampered with. It is generated using the header, payload, and a secret key.

```
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload),
  secret
)
```

---

## 9. JWT Authentication Flow

```
User sends login credentials
            ↓
  Server verifies credentials
            ↓
  Server creates a JWT token
  (Header + Payload + Signature)
            ↓
  JWT sent back to the client
            ↓
  Client stores JWT
  (cookie or localStorage)
            ↓
  Client sends JWT in every
  request header:
  Authorization: Bearer <token>
            ↓
  Server verifies the JWT signature
            ↓
    ┌───────────────┐
    │  Token valid? │
    └───────────────┘
       ↓         ↓
      YES         NO
       ↓           ↓
  Allow access   Return 401
                Unauthorized
```

### Creating and Verifying JWT in Code

```js
// Create JWT
jwt.sign({ id: user._id }, SECRET, { expiresIn: "7d" });

// Verify JWT
jwt.verify(token, SECRET);
// If valid → user is authenticated
// If invalid → access denied
```

---

## 10. Cookies and Their Role in Authentication

A **cookie** is a small piece of data stored in the user's browser by the server.

### Why cookies are used in authentication:

After login, we do not want the user to enter their credentials for every request. Cookies store authentication information — like a Session ID or JWT — so the browser automatically sends it with every request to the same server.

```
User logs in
      ↓
Server creates JWT or Session ID
      ↓
Server stores it in a cookie
and sends it to the browser
      ↓
Browser automatically sends
the cookie with every request
      ↓
Server reads the cookie
and identifies the user
```

---

## 11. JWT in Cookies vs Local Storage

Both cookies and localStorage can store a JWT — but they have important differences.

```
Cookies:
Server → Creates JWT → Stores in HttpOnly cookie
→ Browser sends cookie automatically with every request

Local Storage:
Server → Creates JWT → Store in localStorage
→ JavaScript manually sends JWT in request headers
```

### Comparison Table

| Feature | Cookies | Local Storage |
|---|---|---|
| Stored in | Browser cookie | Browser storage |
| Auto sent with requests | ✅ Yes | ❌ No — must be done manually |
| Accessible by JavaScript | ❌ HttpOnly cookies are not | ✅ Yes |
| XSS protection | Better | Weaker |
| Needs CSRF protection | Yes | No |

> **Interview tip:** HttpOnly cookies are safer because JavaScript cannot access them — protecting against XSS attacks. But they need CSRF protection. localStorage is easier to use but more vulnerable to XSS.

---

## 12. Access Tokens and Refresh Tokens

### The Problem

If we make a JWT that never expires — a stolen token gives the attacker permanent access.
If we make it expire too quickly — the user has to log in again and again.

### The Solution — Two Tokens

**Access Token** — A short-lived JWT (usually 15 minutes) used to access protected resources.

**Refresh Token** — A long-lived token (usually 7 days) used to generate a new access token when the old one expires.

### Flow

```
User logs in
      ↓
Server creates:
• Access Token  (expires in 15 min)
• Refresh Token (expires in 7 days)
      ↓
User accesses protected API
using Access Token
      ↓
Access Token expires
      ↓
Client sends Refresh Token
to server
      ↓
Server verifies Refresh Token
      ↓
Server issues a new Access Token
      ↓
User continues without logging in again
```

---

## 13. Refresh Token Rotation

**Refresh Token Rotation** means issuing a **new refresh token** every time the old one is used. The old refresh token immediately becomes invalid.

### Without Rotation — Dangerous

```
Attacker steals Refresh Token
            ↓
Attacker keeps generating
new Access Tokens forever
```

### With Rotation — Safe

```
Refresh Token used
      ↓
New Refresh Token generated
      ↓
Old Refresh Token becomes invalid
      ↓
If attacker tries to use the old token → access denied
```

---

## 14. API Keys vs JWT vs Sessions

| Feature | API Key | JWT | Session |
|---|---|---|---|
| **Purpose** | Identify applications | Authenticate users | Maintain user login |
| **Stored on Server** | Validated against DB | No state stored | Session data stored |
| **Stateful** | Usually no | No | Yes |
| **Contains User Info** | No | Yes | Yes (on server) |
| **Common Use** | APIs, third-party access | Web and mobile apps | Traditional web apps |

---

## 15. CORS in Authentication

**CORS** stands for Cross-Origin Resource Sharing. It is a browser security feature that controls which websites can make requests to your backend server.

### What is an Origin?

An origin consists of three parts:
- Protocol (http or https)
- Domain (example.com)
- Port (3000, 8080)

`https://myapp.com:3000` and `https://myapp.com:8080` are **different origins**.

### How CORS Works

```
Frontend (myapp.com)
      ↓
Sends request to backend (api.myapp.com)
      ↓
Browser checks CORS policy
      ↓
Server responds with allowed origins
in the response header:
Access-Control-Allow-Origin: https://myapp.com
      ↓
Browser allows or blocks the request
```

### Why CORS Matters in Authentication

Authentication cookies or tokens may be sent across different origins. CORS ensures that **only trusted websites** can access your backend resources — preventing malicious websites from making requests on behalf of your users.

---

## Summary

| Concept | One Line Explanation |
|---|---|
| Authentication | Verifying who you are |
| Authorization | Deciding what you can access |
| Session | Server stores user state, browser holds a Session ID |
| JWT | Self-contained token with user info — stateless |
| Access Token | Short-lived token for accessing resources |
| Refresh Token | Long-lived token for generating new access tokens |
| HttpOnly Cookie | Cookie not accessible by JS — safer for storing tokens |
| CORS | Controls which origins can talk to your server |

---

## Interview Questions — Quick Answers

**Q: What is the difference between authentication and authorization?**
Authentication verifies identity (who you are). Authorization determines permissions (what you can do). Authentication always happens first.

**Q: What should you store in a JWT payload?**
Only non-sensitive data like user ID, email, and role. Never store passwords or sensitive information because the payload is base64 encoded — not encrypted.

**Q: Why use refresh tokens instead of long-lived access tokens?**
If an access token is stolen, it expires quickly (15 minutes) and limits damage. A refresh token lets us issue new access tokens without making the user log in again.

**Q: Cookies vs localStorage for storing JWT — which is safer?**
HttpOnly cookies are safer because JavaScript cannot access them, protecting against XSS attacks. But they require CSRF protection. localStorage is simpler but vulnerable to XSS.

**Q: What is CORS and why does it matter?**
CORS is a browser security feature that controls which origins can make requests to your server. It prevents malicious websites from making unauthorized requests using a user's credentials.

---

