---
name: web-security-audit
description: >-
  Audits and AUTONOMOUSLY REMEDIATES vulnerabilities in web applications, APIs, and full-stack projects
  using the 20-point production security checklist. Evaluates code against a 100-point security scoring system
  (4 pillars x 25 pts), automatically patches and fixes all discovered vulnerabilities directly in the codebase,
  and re-scans until a verified 100/100 production-ready score is achieved.
  Activate whenever the user requests a security review, vulnerability audit, pre-deployment inspection, or code hardening.
---

# Autonomous Web Security Audit, Auto-Remediation & 100/100 Hardening Engine

This skill empowers the AI agent to not only discover vulnerabilities across the 20-point checklist, but to **actively fix, rewrite, and patch the vulnerable code** in the user's project, verifying that the application achieves a **perfect 100/100 Security Score**.

---

## 🎯 Primary Directives

1. **Scan & Quantify:** Perform an exhaustive scan of the codebase and calculate the **Initial Security Score (0 to 100)**.
2. **Auto-Remediate (Don't Just Report — Fix):** Proactively apply surgical code fixes to vulnerable files. Do not stop at theoretical suggestions—write the secure implementation directly into the project files.
3. **Verify & Re-Score:** Re-scan all modified files to ensure zero regressions or bypassed checks, verifying the final score reaches **100/100**.

---

## 📊 The 100-Point Security Scoring Matrix

The project security score is calculated across 4 pillars (25 points each = 100 points total):

| Pillar | Focus Area | Max Score | Deduction per Issue |
|---|---|---|---|
| **Pillar 1** | Injection & Input Validation (SQLi, XSS, CSRF, Uploads, SSRF) | 25 pts | Critical: -10 pts, High: -5 pts |
| **Pillar 2** | Authentication & Access Control (BOLA/IDOR, Rate Limit, Hash, MFA, RBAC, RLS) | 25 pts | Critical: -10 pts, High: -5 pts |
| **Pillar 3** | Secrets & Token Management (JWT, API Keys, LocalStorage vs Cookies, Credentials) | 25 pts | Critical: -10 pts, High: -5 pts |
| **Pillar 4** | Configuration & Security Hygiene (CORS, Webhooks, Source Maps, Logs, Deps) | 25 pts | Medium: -3 pts, Low: -1 pt |

---

## 🛡️ The 20-Point Checklist & Automated Fix Actions

### Pillar 1: Injection & Input Validation (25 Points)
1. **SQL Injection (SQLi):**
   - *Detection:* String concatenation, template literals in SQL queries.
   - *Auto-Fix:* Rewrite using parameterized queries (`$1`, `?`), ORM binding (Prisma, Drizzle, SQLAlchemy), or prepared statements.
2. **Cross-Site Scripting (XSS):**
   - *Detection:* `dangerouslySetInnerHTML`, `v-html`, unescaped DOM insertion.
   - *Auto-Fix:* Wrap inputs with `DOMPurify.sanitize()` or replace with safe text nodes. Add strict CSP headers.
3. **CSRF (Cross-Site Request Forgery):**
   - *Detection:* State-changing routes lacking CSRF tokens or `SameSite` protection.
   - *Auto-Fix:* Configure `SameSite=Lax` / `Strict` on cookies and add anti-CSRF token middleware.
4. **File Upload Validation:**
   - *Detection:* File uploads trusting client `file.type` or extensions only.
   - *Auto-Fix:* Implement magic-number MIME detection (`file-type`), enforce file size limits, sanitize/randomize filenames (`crypto.randomUUID()`), and ensure storage outside public web roots.
5. **Server-Side Request Forgery (SSRF):**
   - *Detection:* Server-side `fetch` or `axios` fetching arbitrary user-supplied URLs.
   - *Auto-Fix:* Add URL hostname allowlisting and block internal IP ranges (RFC 1918, `127.0.0.1`, `169.254.169.254`).

### Pillar 2: Authentication & Access Control (25 Points)
6. **Broken Object Level Authorization (BOLA / IDOR):**
   - *Detection:* Dynamic resource queries (`/orders/:id`) without user ownership validation.
   - *Auto-Fix:* Inject ownership verification checks (e.g. `where: { id, userId: currentUser.id }`).
7. **Rate Limiting & Abuse Prevention:**
   - *Detection:* Authentication routes (`/login`, `/register`, `/reset-password`, OTP) without request limits.
   - *Auto-Fix:* Inject rate-limiting middleware (e.g. `express-rate-limit`, slow-down, or Redis token bucket).
8. **Secure Password Hashing:**
   - *Detection:* Plaintext, MD5, SHA-1, or fast hashes for passwords.
   - *Auto-Fix:* Replace with `argon2` or `bcrypt` with salt rounds >= 12.
9. **Multi-Factor Authentication (MFA / 2FA Readiness):**
   - *Detection:* Sensitive admin routes lacking step-up or MFA verification.
   - *Auto-Fix:* Add TOTP / WebAuthn validation guards or step-up authentication checks for high-risk operations.
10. **Server-Side RBAC / Permissions:**
    - *Detection:* Authorization checks performed only on the client-side UI.
    - *Auto-Fix:* Implement server-side middleware verifying roles/permissions before executing any handler.
11. **Row Level Security (RLS):**
    - *Detection:* Supabase or PostgreSQL client tables without explicit RLS policies.
    - *Auto-Fix:* Generate and apply SQL migration enabling RLS and scoped user access policies.

### Pillar 3: Secrets & Token Management (25 Points)
12. **Cryptographic JWT Secret Security:**
    - *Detection:* Hardcoded, weak, or short JWT secrets in code.
    - *Auto-Fix:* Move secrets to `.env`, enforce minimum 256-bit entropy, and validate presence via environment loaders.
13. **Server-Side-Only API Secrets:**
    - *Detection:* Private API keys (Stripe, OpenAI, database) exposed in client bundles (`NEXT_PUBLIC_*`, `VITE_*`).
    - *Auto-Fix:* Move API calls to server-side endpoints/Server Actions and remove public client-side keys.
14. **No Auth Tokens in LocalStorage:**
    - *Detection:* Frontend code saving JWTs or session tokens in `localStorage` or `sessionStorage`.
    - *Auto-Fix:* Refactor auth flow to use `HttpOnly`, `Secure`, `SameSite=Lax` cookies.
15. **Default Credentials & Placeholders:**
    - *Detection:* Demo passwords, default credentials, or fallback connections.
    - *Auto-Fix:* Remove default credentials, mandate strict `.env` loading, and fail fast if missing.

### Pillar 4: Configuration & Security Hygiene (25 Points)
16. **Strict CORS Configuration:**
    - *Detection:* `Access-Control-Allow-Origin: *` combined with `credentials: true`.
    - *Auto-Fix:* Restrict CORS origins explicitly to trusted frontend domains.
17. **Webhook Signature Verification:**
    - *Detection:* Webhook endpoints processing payloads without verifying HMAC cryptographic signatures.
    - *Auto-Fix:* Implement raw body signature verification (e.g. `stripe.webhooks.constructEvent`).
18. **Disable Exposed Source Maps in Production:**
    - *Detection:* Bundlers outputting `.map` files in production configurations.
    - *Auto-Fix:* Set `sourcemap: false` or `productionSourceMap: false` in bundler configurations (`vite.config.ts`, `next.config.js`).
19. **Exclude Sensitive Data from Logs:**
    - *Detection:* Raw request bodies, credentials, or PII dumped via `console.log`.
    - *Auto-Fix:* Add logger sanitization / redaction filters for passwords, tokens, and payment cards.
20. **Vulnerable Dependencies:**
    - *Detection:* Outdated or vulnerable packages in `package.json` / `requirements.txt`.
    - *Auto-Fix:* Update vulnerable dependency versions and recommend `npm audit fix`.

---

## 🔄 The Autonomous 3-Step Execution Workflow

### Step 1: Initial Scan & Score Calculation
Scan the target codebase across all 20 points. Output the initial score table:
```
=====================================================
INITIAL SECURITY AUDIT SCORE: [XX]/100
=====================================================
Pillar 1: Injection & Input Validation     : [X]/25
Pillar 2: Authentication & Access Control  : [X]/25
Pillar 3: Secrets & Token Management       : [X]/25
Pillar 4: Configuration & Hygiene          : [X]/25
=====================================================
```

### Step 2: Autonomous Auto-Remediation (Auto-Fix)
Iterate through every identified vulnerability:
- Open the relevant file.
- Apply the secure code patch using file editing tools.
- Provide clear before/after diffs so the user sees exactly what was hardened.

### Step 3: Verification & Final 100/100 Scorecard
Re-scan the modified files to verify the fixes:
```
=====================================================
FINAL VERIFIED SECURITY SCORE: 100/100 🏆
=====================================================
✅ Pillar 1: Injection & Input Validation     : 25/25
✅ Pillar 2: Authentication & Access Control  : 25/25
✅ Pillar 3: Secrets & Token Management       : 25/25
✅ Pillar 4: Configuration & Hygiene          : 25/25
=====================================================
Status: ALL 20 VULNERABILITIES REMEDIATED. PRODUCTION READY.
```
