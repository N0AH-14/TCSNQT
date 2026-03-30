# 17 — REST API, HTTP & Web Fundamentals

---

> **TCS Digital frequently asks "What happens when you type a URL into the browser?" and REST API concepts. These are NOT in your original 16 files. Know them cold.**

---

## 17.1 What Happens When You Type a URL in the Browser?

> **This is one of the MOST frequently asked questions in TCS Digital interviews. Memorize the flow.**

```
Step-by-Step:

1. URL PARSING
   Browser breaks the URL into: protocol (https), domain (www.google.com), path (/search), query (?q=TCS)

2. DNS LOOKUP (Domain → IP Address)
   Browser cache → OS cache → Router cache → ISP Recursive Resolver
   → Root Server (.com?) → TLD Server (google.com?) → Authoritative NS (142.250.190.78)

3. TCP CONNECTION (3-Way Handshake)
   Client → SYN → Server
   Server → SYN-ACK → Client
   Client → ACK → Server
   Connection established on port 443 (HTTPS) or 80 (HTTP)

4. TLS/SSL HANDSHAKE (if HTTPS)
   Browser and server negotiate encryption algorithm
   Server sends SSL certificate (with public key)
   Browser verifies certificate with Certificate Authority
   Session keys exchanged → encrypted channel established

5. HTTP REQUEST
   Browser sends: GET /search?q=TCS HTTP/1.1
   Headers: Host, User-Agent, Accept, Cookie, etc.

6. SERVER PROCESSING
   Web server (Nginx/Apache) receives request
   → Routes to application server (Flask, Node.js, etc.)
   → May query database (MySQL, MongoDB)
   → Generates HTTP response

7. HTTP RESPONSE
   Server sends: HTTP/1.1 200 OK
   Headers: Content-Type, Set-Cookie, Cache-Control
   Body: HTML content

8. BROWSER RENDERING
   Parse HTML → Build DOM tree
   Parse CSS → Build CSSOM tree
   Combine → Render tree
   Layout → Paint → Display
   If JS/CSS/images found → repeat fetch for each resource
```

**Your Project Context:**
> "My Power BI dashboard connects to MySQL on `localhost:3306` using TCP. When the dashboard requests data, it's essentially the same flow — TCP handshake, then MySQL's wire protocol sends the SQL query, the server processes it, and returns result rows. The key difference is MySQL uses a binary protocol instead of HTTP text."

---

## 17.2 REST API Fundamentals

### What is REST?

**REST** = **RE**presentational **S**tate **T**ransfer — an architectural style for designing networked applications using HTTP.

### REST Principles

| Principle | Meaning |
|-----------|---------|
| **Stateless** | Each request contains ALL information needed. Server doesn't store client state between requests |
| **Client-Server** | Client (frontend) and server (backend) are independent — can evolve separately |
| **Uniform Interface** | Consistent URL patterns: `/api/users/123` always means "user with ID 123" |
| **Resource-Based** | Everything is a "resource" identified by a URL (URI) |
| **Cacheable** | Responses should indicate if they can be cached to improve performance |

### HTTP Methods — CRUD Mapping

| Method | CRUD Action | Example | Idempotent? |
|--------|------------|---------|-------------|
| **GET** | Read | `GET /api/crimes?district=Central` | ✅ Yes |
| **POST** | Create | `POST /api/crimes` (body: new crime data) | ❌ No |
| **PUT** | Update (full) | `PUT /api/crimes/12345` (body: full record) | ✅ Yes |
| **PATCH** | Update (partial) | `PATCH /api/crimes/12345` (body: `{arrest: true}`) | ✅ Yes |
| **DELETE** | Delete | `DELETE /api/crimes/12345` | ✅ Yes |

**Q: "What does idempotent mean?"**
> "An idempotent operation produces the same result whether you execute it once or multiple times. GET is idempotent — reading the same data twice gives the same result. POST is NOT — submitting the same form twice creates two records. This matters for retry logic — if a network error occurs, it's safe to retry a GET or PUT, but retrying a POST could create duplicates."

### HTTP Status Codes — Organized by Category

```
1xx — Informational (rarely encountered)
2xx — Success
3xx — Redirection
4xx — Client Error (YOUR fault)
5xx — Server Error (SERVER's fault)
```

| Code | Meaning | When You'd See It |
|------|---------|-------------------|
| **200** | OK — Request successful | Normal GET response |
| **201** | Created — Resource created | After successful POST |
| **204** | No Content — Success but no body | After successful DELETE |
| **301** | Moved Permanently | URL changed permanently |
| **302** | Found — Temporary redirect | Login page redirect |
| **400** | Bad Request — Malformed request | Invalid JSON in POST body |
| **401** | Unauthorized — Not authenticated | Missing or expired token |
| **403** | Forbidden — Authenticated but no permission | User can't access admin route |
| **404** | Not Found — Resource doesn't exist | Wrong URL or deleted resource |
| **405** | Method Not Allowed | POST to a GET-only endpoint |
| **429** | Too Many Requests — Rate limited | API rate limit exceeded |
| **500** | Internal Server Error | Unhandled exception on server |
| **502** | Bad Gateway | Upstream server unavailable |
| **503** | Service Unavailable | Server overloaded or in maintenance |

---

## 17.3 REST vs SOAP

| Feature | REST | SOAP |
|---------|------|------|
| **Protocol** | Uses HTTP | Can use HTTP, SMTP, TCP |
| **Data Format** | JSON (lightweight), XML | XML only (heavier) |
| **Complexity** | Simple, lightweight | Complex, enterprise-grade |
| **State** | Stateless | Can be stateful |
| **Speed** | Faster (lighter payloads) | Slower (XML overhead) |
| **Use Case** | Web/mobile APIs, microservices | Banking, enterprise integrations |
| **Example** | Adzuna job API, weather APIs | Payment gateway SOAP services |

**Q: "Which would you use for your project?"**
> "REST with JSON — for a data analytics pipeline, I need lightweight, fast data transfer. My Flask API (if I built one) would expose crime data as JSON endpoints. SOAP's XML overhead is unnecessary unless I'm integrating with legacy enterprise systems that mandate it."

---

## 17.4 API Authentication Methods

| Method | How It Works | Security Level | Use Case |
|--------|-------------|---------------|----------|
| **API Key** | Pass a key in header or query param | Low (key can be stolen) | Public APIs with rate limiting (Adzuna, OpenWeather) |
| **Basic Auth** | Base64-encoded username:password in header | Low (easily decoded) | Internal/development APIs |
| **Bearer Token (JWT)** | JSON Web Token sent in `Authorization: Bearer <token>` header | High | Modern web applications, SPAs |
| **OAuth 2.0** | Token-based flow with authorization server | Highest | Google login, GitHub OAuth, social logins |

### JWT (JSON Web Token) — Know This

```
JWT Structure:
┌────────────┬─────────────┬──────────────┐
│   Header   │   Payload   │  Signature   │
│  (algo,    │  (user_id,  │  (HMAC of    │
│   type)    │   role,     │   header +   │
│            │   expiry)   │   payload)   │
└────────────┴─────────────┴──────────────┘
       ↓              ↓              ↓
  Base64Url      Base64Url      Base64Url
       ↓              ↓              ↓
  xxxxx      .      yyyyy     .     zzzzz

The three parts are separated by dots → sent in header:
Authorization: Bearer xxxxx.yyyyy.zzzzz
```

**Q: "How does JWT authentication work?"**
> "User logs in with credentials → server verifies → server generates a JWT containing the user's ID, role, and an expiry time → signs it with a secret key → sends it to the client. On subsequent requests, the client sends the JWT in the Authorization header. The server verifies the signature without needing to query a database — the token itself contains the user identity. This makes it stateless and scalable."

---

## 17.5 JSON — Know the Basics

```json
{
    "crime_id": 12345,
    "primary_type": "THEFT",
    "arrest": false,
    "location": {
        "district": "Central",
        "latitude": 34.0522,
        "longitude": -118.2437
    },
    "tags": ["property", "non-violent"]
}
```

| JSON Type | Python Equivalent |
|-----------|------------------|
| `string` | `str` |
| `number` | `int` / `float` |
| `boolean` | `bool` (`true/false` → `True/False`) |
| `null` | `None` |
| `array` | `list` |
| `object` | `dict` |

```python
import json

# Parse JSON string → Python dict
data = json.loads('{"name": "Krishna", "age": 23}')

# Python dict → JSON string
json_str = json.dumps(data, indent=2)

# Read JSON file
with open('data.json') as f:
    data = json.load(f)

# Write JSON file
with open('output.json', 'w') as f:
    json.dump(data, f, indent=2)
```

---

## 17.6 Flask API — Your Project Context

```python
from flask import Flask, jsonify, request
import pymysql

app = Flask(__name__)

@app.route('/api/crimes', methods=['GET'])
def get_crimes():
    district = request.args.get('district')  # Query parameter
    # Connect to MySQL and fetch data
    # ...
    return jsonify({"status": "success", "data": crimes}), 200

@app.route('/api/crimes/<int:crime_id>', methods=['GET'])
def get_crime(crime_id):
    # Fetch specific crime by ID
    return jsonify(crime_data), 200

@app.route('/api/crimes', methods=['POST'])
def add_crime():
    data = request.get_json()
    # Validate and insert into MySQL
    return jsonify({"message": "Crime record created"}), 201

if __name__ == '__main__':
    app.run(debug=True, port=5000)
```

**Q: "If you had to expose your crime data as an API, how would you do it?"**
> "I'd use Flask to create REST endpoints. `GET /api/crimes` would return paginated crime records with optional filters (district, crime type, date range). `GET /api/crimes/stats` would return aggregated statistics. I'd add pagination using `?page=1&limit=100` to avoid returning all 8 million records at once. For security, I'd add API key authentication and rate limiting."

---

*This file fills a gap in the original 16-document set. REST/HTTP concepts are increasingly common in TCS Digital 2026 interviews.*
