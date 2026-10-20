# The 20-Point Production Web Security Checklist

A practical, modern security checklist for developers building web applications, APIs, and microservices — especially when utilizing AI coding assistants (Claude, Codex, Cursor, Antigravity, Copilot).

---

## Table of Contents
1. [Pillar 1: Injection & Input Validation](#pillar-1-injection--input-validation)
2. [Pillar 2: Authentication & Access Control](#pillar-2-authentication--access-control)
3. [Pillar 3: Secrets & Token Management](#pillar-3-secrets--token-management)
4. [Pillar 4: Configuration & Security Hygiene](#pillar-4-configuration--security-hygiene)

---

## Pillar 1: Injection & Input Validation

### 1. SQL Injection (SQLi)
- **Vulnerability:** Unsanitized user inputs concatenated into database queries.
- **Vulnerable:**
  ```javascript
  const query = `SELECT * FROM users WHERE email = '${req.body.email}'`;
  ```
- **Secure:**
  ```javascript
  const query = `SELECT * FROM users WHERE email = $1`;
  await db.query(query, [req.body.email]);
  ```

### 2. Cross-Site Scripting (XSS)
- **Vulnerability:** Rendering unsanitized HTML/JavaScript directly into the DOM.
- **Vulnerable:**
  ```jsx
  <div dangerouslySetInnerHTML={{ __html: userBio }} />
  ```
- **Secure:**
  ```jsx
  import DOMPurify from 'dompurify';
  <div dangerouslySetInnerHTML={{ __html: DOMPurify.sanitize(userBio) }} />
  ```

### 3. Cross-Site Request Forgery (CSRF)
- **Vulnerability:** Third-party websites forging unauthorized state-changing requests using stored browser session cookies.
- **Secure Implementation:**
  - Enforce `SameSite=Lax` or `SameSite=Strict` cookie attributes.
  - Implement Anti-CSRF tokens for session-based architectures.

### 4. File Upload Validation
- **Vulnerability:** Attackers uploading executable scripts (.php, .html, .exe) or SVG with malicious script payloads.
- **Secure Implementation:**
  - Validate file MIME types using magic bytes (e.g., using `file-type` package).
  - Enforce file size limits.
  - Generate randomized unique file names (e.g., `crypto.randomUUID()`).
  - Store files outside the web root (e.g., private AWS S3 / Cloud Storage buckets).

### 5. Server-Side Request Forgery (SSRF)
- **Vulnerability:** Server fetches user-supplied URLs, exposing internal APIs or cloud metadata (`169.254.169.254`).
- **Secure Implementation:**
  - Whitelist permitted hostnames and protocols (`https` only).
  - Block internal/private IP address ranges (`10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`, `127.0.0.1`).

---

## Pillar 2: Authentication & Access Control

### 6. Broken Object Level Authorization (BOLA / IDOR)
- **Vulnerability:** Changing resource IDs in requests to access or modify other users' records.
- **Vulnerable:**
  ```javascript
  app.get('/api/orders/:id', async (req, res) => {
    const order = await db.orders.findById(req.params.id);
    res.json(order);
  });
  ```
- **Secure:**
  ```javascript
  app.get('/api/orders/:id', async (req, res) => {
    const order = await db.orders.findOne({
      where: { id: req.params.id, userId: req.user.id }
    });
    if (!order) return res.status(404).json({ error: "Order not found" });
    res.json(order);
  });
  ```

### 7. Rate Limiting
- **Vulnerability:** Brute-force attacks against login, OTP, and password reset endpoints.
- **Secure Implementation:**
  ```javascript
  import rateLimit from 'express-rate-limit';

  const authLimiter = rateLimit({
    windowMs: 15 * 60 * 1000, // 15 minutes
    max: 5, // Limit each IP to 5 requests per window
    message: "Too many login attempts. Please try again later."
  });
  app.use('/api/auth/login', authLimiter);
  ```

### 8. Secure Password Hashing
- **Vulnerability:** Storing passwords in plain text or using outdated hashing (MD5, SHA-1, plain SHA-256).
- **Secure Implementation:**
  - Use `Argon2id` (preferred) or `bcrypt` with work factor >= 12.

### 9. Multi-Factor Authentication (MFA / 2FA)
- **Secure Implementation:** Implement TOTP (authenticator apps like Google Authenticator) or WebAuthn/Passkeys for sensitive operations and admin panels.

### 10. Server-Side Permissions (RBAC/ABAC)
- **Vulnerability:** Hiding UI elements on the frontend without server-side validation.
- **Secure Implementation:** Verify roles and permissions on every backend endpoint using middleware:
  ```javascript
  function requireRole(role) {
    return (req, res, next) => {
      if (req.user.role !== role) {
        return res.status(403).json({ error: "Access Denied" });
      }
      next();
    };
  }
  ```

### 11. Row Level Security (RLS)
- **Vulnerability:** In platforms like Supabase / PostgreSQL where the client queries the database directly, missing RLS exposes entire tables.
- **Secure Implementation:**
  ```sql
  ALTER TABLE profiles ENABLE ROW LEVEL SECURITY;

  CREATE POLICY "Users can only update their own profile"
  ON profiles FOR UPDATE
  USING (auth.uid() = id);
  ```

---

## Pillar 3: Secrets & Token Management

### 12. Strong Cryptographic JWT Secrets
- **Vulnerability:** Weak or short secrets allowing attackers to forge valid tokens offline.
- **Secure Implementation:** Generate at least 256 bits (32 bytes) of random entropy:
  ```bash
  openssl rand -base64 32
  ```

### 13. Server-Side API Secrets
- **Vulnerability:** Accidentally publishing Stripe Secret Key, AWS credentials, or OpenAI keys into frontend bundles (`NEXT_PUBLIC_*`).
- **Secure Implementation:** Call third-party APIs strictly from backend API routes or Server Actions.

### 14. No Auth Tokens in LocalStorage
- **Vulnerability:** `localStorage` is accessible to JavaScript, making tokens vulnerable to any XSS exploit.
- **Secure Implementation:** Set authentication tokens in `HttpOnly`, `Secure`, `SameSite=Lax` cookies:
  ```javascript
  res.cookie('token', jwtToken, {
    httpOnly: true,
    secure: process.env.NODE_ENV === 'production',
    sameSite: 'lax',
    maxAge: 7 * 24 * 60 * 60 * 1000
  });
  ```

### 15. Default Credentials & Placeholders
- **Vulnerability:** Deploying apps with default database credentials (`postgres / postgres`) or admin accounts (`admin / password`).
- **Secure Implementation:** Mandate strong environment variables and validate configuration startup with tools like `zod` or `joi`.

---

## Pillar 4: Configuration & Security Hygiene

### 16. Strict CORS Configuration
- **Vulnerability:** Wildcard `Access-Control-Allow-Origin: *` with credentials.
- **Secure Implementation:**
  ```javascript
  import cors from 'cors';

  app.use(cors({
    origin: ['https://yourdomain.com'],
    credentials: true
  }));
  ```

### 17. Webhook Signature Verification
- **Vulnerability:** Processing spoofed webhook payloads from attackers pretending to be Stripe or GitHub.
- **Secure Implementation:**
  ```javascript
  const event = stripe.webhooks.constructEvent(
    req.rawBody,
    req.headers['stripe-signature'],
    process.env.STRIPE_WEBHOOK_SECRET
  );
  ```

### 18. Disable Production Source Maps
- **Vulnerability:** Exposing original client source code via `.map` files in production.
- **Secure Implementation:**
  ```javascript
  // vite.config.ts / next.config.js
  export default defineConfig({
    build: {
      sourcemap: false
    }
  });
  ```

### 19. Exclude Sensitive Data from Logs
- **Vulnerability:** Storing passwords, credit card numbers, or authorization headers in server logs.
- **Secure Implementation:** Use log sanitization / masking in Winston or Pino loggers.

### 20. Update Vulnerable Dependencies
- **Vulnerability:** Running dependencies with known CVEs.
- **Secure Implementation:**
  ```bash
  npm audit fix
  # or
  pip-audit
  ```
