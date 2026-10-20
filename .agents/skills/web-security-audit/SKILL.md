---
name: web-security-audit
description: >-
  Audits web applications, APIs, and backend/frontend code against a 20-point production
  security checklist covering: 1) Injection & Input Validation (SQLi, XSS, CSRF, File Uploads, SSRF),
  2) Authentication & Access Control (BOLA/IDOR, Rate Limiting, Password Hashing, MFA, Server-Side Permissions, RLS),
  3) Secrets & Token Management (JWT secrets, Server-Side API Secrets, No tokens in LocalStorage, Default credentials),
  4) Configuration & Hygiene (Strict CORS, Webhook Signatures, Source Maps removal, Sensitive Data Logs, Dependency Audit).
  Activate whenever the user requests a security review, vulnerability audit, pre-deployment inspection, or code hardening.
---

# Web Security Audit & Hardening Guide (20-Point Checklist)

This skill guides the inspection, auditing, and remediation of security vulnerabilities in web applications, APIs, and full-stack projects before or after deployment.

---

## When to Activate This Skill
- The user requests a security audit, vulnerability scan, or code review.
- The user asks: "Is my app secure?", "Audit my code for security", "Pre-deploy security check", or "Check security vulnerabilities".
- Preparing an application for production deployment.
- Reviewing authentication, payment webhooks, database queries, file uploads, or API endpoints.

---

## The 20-Point Security Checklist

### Pillar 1: Injection & Input Validation
1. **SQL Injection (SQLi) Prevention:**
   - **Risk:** Raw SQL string concatenation allows attackers to bypass auth or dump databases.
   - **Check:** Search for string interpolations in database queries (e.g., `SELECT * FROM users WHERE id = '${id}'`).
   - **Fix:** Use parameterized queries, prepared statements, or modern ORMs/ODMs (Prisma, Drizzle, SQLAlchemy, TypeORM, Hibernate) with bound parameters.

2. **Cross-Site Scripting (XSS) Prevention:**
   - **Risk:** Injected client-side scripts steal cookies or hijack user sessions.
   - **Check:** Look for `dangerouslySetInnerHTML`, `v-html`, unescaped template tags, or direct DOM manipulation like `element.innerHTML = userInput`.
   - **Fix:** Sanitize user input using libraries like DOMPurify, use framework escaping, and implement a strict `Content-Security-Policy` (CSP) header.

3. **CSRF (Cross-Site Request Forgery) Protection:**
   - **Risk:** Malicious sites induce logged-in users to perform unwanted actions.
   - **Check:** Check state-changing HTTP endpoints (POST/PUT/DELETE/PATCH) for CSRF validation.
   - **Fix:** Use Anti-CSRF tokens for session-based auth, or set cookie attributes to `SameSite=Lax` or `SameSite=Strict`.

4. **File Upload Validation:**
   - **Risk:** Malicious file uploads (.php, .exe, SVG with embedded scripts) executing on the server or client.
   - **Check:** Check upload handlers for file size limits, extension validation, and MIME-type verification.
   - **Fix:** Validate MIME types with magic numbers (file headers), store files outside the public web root (e.g., in private S3/GCS buckets), and generate randomized filenames.

5. **Server-Side Request Forgery (SSRF) Prevention:**
   - **Risk:** Server fetches arbitrary URLs provided by users, exposing internal network resources (e.g., `169.254.169.254` cloud metadata).
   - **Check:** Any endpoint where the server performs an HTTP request to a user-supplied URL (e.g., webhooks, unfurling previews, proxies).
   - **Fix:** Validate and allowlist permitted hostnames/protocols, disallow private IP ranges (RFC 1918, loopback `127.0.0.1`, link-local `169.254.x.x`).

---

### Pillar 2: Authentication & Access Control
6. **Broken Object Level Authorization (BOLA / IDOR):**
   - **Risk:** Users modifying resource IDs in URLs/payloads (e.g., `/api/orders/456`) to access or modify data belonging to other users.
   - **Check:** Route handlers that fetch records by ID without checking `WHERE user_id = current_user.id`.
   - **Fix:** Always verify ownership or explicit permissions on the server before returning or mutating data.

7. **Rate Limiting & Brute-Force Protection:**
   - **Risk:** Denial of Service (DoS), credential stuffing, and spam attacks.
   - **Check:** Public endpoints (especially `/login`, `/register`, `/forgot-password`, `/reset-password`, and OTP endpoints).
   - **Fix:** Implement rate limiting (e.g., `express-rate-limit`, Redis-backed sliding window, Cloudflare rate limiting).

8. **Secure Password Hashing:**
   - **Risk:** Weak or fast algorithms (MD5, SHA-1, plain SHA-256) lead to fast offline cracking if database leaks.
   - **Check:** User registration and password reset logic.
   - **Fix:** Use adaptive, salted hashing algorithms: `Argon2id` (preferred) or `bcrypt` (cost factor >= 12). Never store plain passwords.

9. **Multi-Factor Authentication (MFA / 2FA):**
   - **Risk:** Credential compromises grant full access.
   - **Check:** Critical systems, admin dashboards, and high-value user portals.
   - **Fix:** Support TOTP (Time-based One-Time Password via authenticator apps) or WebAuthn/Passkeys.

10. **Server-Side Authorization & Permissions:**
    - **Risk:** Client-side route guards (e.g., hiding a button in React) can easily be bypassed with direct API requests.
    - **Check:** Backend endpoints corresponding to protected frontend views.
    - **Fix:** Enforce Role-Based Access Control (RBAC) or Attribute-Based Access Control (ABAC) middleware on every backend route.

11. **Row Level Security (RLS):**
    - **Risk:** Direct database exposure or leaked query logic allows table-wide reads.
    - **Check:** Supabase, PostgreSQL, or Firebase databases where clients interact directly with DB queries.
    - **Fix:** Enable RLS (`ALTER TABLE table_name ENABLE ROW LEVEL SECURITY;`) and write explicit read/write policies based on authenticated user IDs.

---

### Pillar 3: Secrets & Token Management
12. **JWT Secret & Cryptographic Key Security:**
    - **Risk:** Weak JWT secret keys (`secret123`) can be brute-forced to forge valid admin tokens.
    - **Check:** Secret strings passed to `jwt.sign()` / `jwt.verify()`.
    - **Fix:** Use cryptographically secure keys (minimum 256 bits / 32 bytes random string), rotate keys, and ensure the secret is loaded strictly from environment variables.

13. **Server-Side API Secrets (No Leakage to Client):**
    - **Risk:** Embedding private API keys (Stripe secret key, database credentials, OpenAI keys) into frontend code.
    - **Check:** Frontend bundles, `NEXT_PUBLIC_*`, `VITE_*`, `REACT_APP_*` environment variables.
    - **Fix:** Move third-party API calls requiring private secrets to backend server routes / Server Actions.

14. **No Sensitive Auth Tokens in LocalStorage:**
    - **Risk:** Any XSS vulnerability can read `localStorage.getItem('access_token')` and steal user sessions.
    - **Check:** Frontend auth flow storing refresh/access tokens in LocalStorage or SessionStorage.
    - **Fix:** Store session tokens in `HttpOnly`, `Secure`, `SameSite=Lax` cookies.

15. **Change Default Credentials & Placeholders:**
    - **Risk:** Default passwords (e.g., `admin / admin`, `postgres / postgres`) left on staging or production instances.
    - **Check:** Default admin seed scripts, sample environment configuration files (`.env.example`).
    - **Fix:** Mandate strong random credentials on deployment and enforce password changes upon initial setup.

---

### Pillar 4: Configuration & Security Hygiene
16. **Strict CORS (Cross-Origin Resource Sharing):**
    - **Risk:** Wildcard CORS (`Access-Control-Allow-Origin: *`) combined with credentials allows unauthorized sites to read private API responses.
    - **Check:** Server CORS configuration.
    - **Fix:** Restrict origins strictly to trusted frontend domains. Never combine `Origin: *` with `Credentials: true`.

17. **Webhook Signature Verification:**
    - **Risk:** Attackers forge fake payment success or delivery webhooks to trigger unauthorized fulfillment.
    - **Check:** Incoming webhook handlers (Stripe, PayPal, Paddle, GitHub, etc.).
    - **Fix:** Verify raw payload HMAC signatures using the provider's official signing secret before processing events.

18. **Disable Exposed Source Maps in Production:**
    - **Risk:** Uploading `.js.map` files to public servers reveals original source code, internal comments, and file architecture.
    - **Check:** Build tool configuration (`vite.config.ts`, `next.config.js`, `webpack.config.js`).
    - **Fix:** Set `productionSourceMap: false` or `sourcemap: false`, or keep source maps private (uploaded only to Sentry/monitoring).

19. **Exclude Sensitive Data from Application Logs:**
    - **Risk:** Logging passwords, bearer tokens, credit card numbers, or PII into stdout or log aggregators.
    - **Check:** `console.log(req.body)`, logger middleware (Winston, Morgan, Pino).
    - **Fix:** Redact or mask sensitive fields (passwords, tokens, cards) in logging middleware before recording.

20. **Audit & Update Vulnerable Dependencies:**
    - **Risk:** Known vulnerabilities (CVEs) in third-party packages allow remote code execution or denial of service.
    - **Check:** Package manifest files (`package.json`, `requirements.txt`, `go.mod`, `pom.xml`).
    - **Fix:** Run audit commands regularly (`npm audit`, `pip-audit`, `cargo audit`) and configure automated tools (Dependabot, Snyk).

---

## Standard Audit Workflow

When asked to audit a project:
1. **Reconnaissance:** Identify language, framework, database, and authentication mechanism (e.g., Next.js, Express, Supabase, Django, FastAPI).
2. **Scan Codebase:**
   - Search for sensitive patterns (raw SQL, unescaped HTML, exposed tokens in localStorage, `.env` committed to git).
   - Inspect API endpoints for BOLA / IDOR and missing auth middlewares.
   - Check CORS and webhook handlers.
3. **Structured Audit Report:**
   Group findings by severity:
   - 🔴 **Critical:** SQL Injection, BOLA/IDOR, hardcoded secrets, missing auth checks.
   - 🟠 **High:** XSS, missing rate limiting, CSRF on state changes, insecure cookies.
   - 🟡 **Medium:** Exposed source maps, weak CORS, missing webhook verification.
   - 🟢 **Low / Hygiene:** Dependency updates, logging sanitization.
4. **Remediation:** Provide exact, production-ready code replacements for every discovered issue.
