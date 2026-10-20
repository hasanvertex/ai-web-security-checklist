# 🛡️ 20-Point Web Security Auto-Remediation & 100/100 Hardening Engine

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Target Score](https://img.shields.io/badge/Security%20Score-100%2F100-brightgreen.svg)](#-the-100-point-security-matrix)
[![Antigravity Skill](https://img.shields.io/badge/Antigravity-Auto--Remediation-blue)](https://antigravity.google)
[![Claude Code](https://img.shields.io/badge/Claude-CLAUDE.md-orange)](https://anthropic.com)
[![Cursor Rules](https://img.shields.io/badge/Cursor-.cursorrules-purple)](https://cursor.com)

A battle-tested **20-point Web Security Audit & Autonomous Auto-Remediation Engine** designed for modern web applications and APIs. Compatible with **Google Antigravity**, **Anthropic Claude (Claude Code)**, and **Cursor**.

Instead of just reporting security warnings, this system **actively patches vulnerable code in your project** until your codebase scores a verified **100/100 Production-Ready Security Score**.

---

## ⚡ Key Highlights

- 🔍 **Full-Spectrum Scan:** Audits 20 critical vulnerabilities across 4 pillars.
- 🛠️ **Autonomous Auto-Remediation:** Automatically refactors code, parameterizes SQL, secures cookies, adds rate limiting, and tightens CORS directly in your codebase.
- 💯 **100/100 Scoring System:** Computes initial scores, applies surgical fixes, and verifies a 100/100 scorecard.
- 🤖 **Cross-Platform:** Works seamlessly across Antigravity, Claude Code, and Cursor.

---

## 📊 The 100-Point Security Matrix

| Pillar | Focus Area | Max Score | Vulnerabilities Audited & Auto-Patched |
|---|---|---|---|
| **Pillar 1** | **Injection & Input Validation** | 25 pts | SQL Injection, XSS, CSRF, File Upload Validation, SSRF |
| **Pillar 2** | **Authentication & Access Control** | 25 pts | BOLA/IDOR, Rate Limiting, Password Hashing, MFA, Server-Side RBAC, RLS |
| **Pillar 3** | **Secrets & Token Management** | 25 pts | 256-bit JWT Secrets, Server-Side API Keys, HttpOnly Cookies (No LocalStorage), Default Credentials |
| **Pillar 4** | **Configuration & Hygiene** | 25 pts | Strict CORS, Webhook HMAC Signatures, Source Maps Removal, Log Sanitization, Dependency CVEs |

---

## 🔍 Detailed Breakdown: What The 20-Point Audit Checks & Fixes

### 🏛️ Pillar 1: Injection & Input Validation (25 Points)

#### 1. SQL Injection (SQLi)
* **What it audits:** Checks database queries for direct string concatenation, template literals (`SELECT ... ${input}`), or unsanitized user inputs.
* **The Risk:** Attackers can bypass login, dump database tables, modify data, or execute arbitrary SQL.
* **Auto-Fix:** Rewrites queries using parameterized statements (`$1`, `?`), ORM bindings (Prisma, Drizzle, SQLAlchemy), or prepared statements.

#### 2. Cross-Site Scripting (XSS)
* **What it audits:** Inspects frontend templates and DOM manipulations for `dangerouslySetInnerHTML`, `v-html`, unescaped markdown/HTML rendering, and raw DOM insertions (`element.innerHTML`).
* **The Risk:** Attackers inject malicious JavaScript to steal user session cookies, redirect users, or perform actions on their behalf.
* **Auto-Fix:** Wraps untrusted user content with sanitization libraries (e.g. `DOMPurify.sanitize()`), enforces framework auto-escaping, and configures a strict `Content-Security-Policy` (CSP) header.

#### 3. Cross-Site Request Forgery (CSRF)
* **What it audits:** Scans state-changing HTTP endpoints (POST, PUT, DELETE, PATCH) to ensure mutation requests cannot be triggered by foreign origins.
* **The Risk:** A malicious website can trick an authenticated user's browser into submitting unwanted actions (e.g., changing passwords, making transfers).
* **Auto-Fix:** Enforces `SameSite=Lax` or `SameSite=Strict` cookie policies and implements Anti-CSRF token middleware.

#### 4. Malicious File Uploads
* **What it audits:** Checks file upload handlers for missing size limits, client-side only extension checks, and lack of content inspection.
* **The Risk:** Attackers upload executable scripts (`.php`, `.exe`) or SVG files with embedded JavaScript payloads that execute on the server or in victim browsers.
* **Auto-Fix:** Validates MIME types with magic byte signatures (e.g. `file-type`), enforces file size limits, generates randomized unique filenames (`crypto.randomUUID()`), and ensures storage outside the web root (e.g., private S3/GCS buckets).

#### 5. Server-Side Request Forgery (SSRF)
* **What it audits:** Checks backend code where the server performs outbound HTTP requests (e.g., link unfurling, image proxying, webhooks) to user-supplied URLs.
* **The Risk:** Attackers force the server to scan internal network infrastructure or access cloud metadata services (`http://169.254.169.254`).
* **Auto-Fix:** Implements strict hostname/domain allowlisting and denies access to private and link-local IP ranges (RFC 1918, `127.0.0.1`, `169.254.0.0/16`).

---

### 🔑 Pillar 2: Authentication & Access Control (25 Points)

#### 6. Broken Object Level Authorization (BOLA / IDOR)
* **What it audits:** Audits dynamic API endpoints (`/api/orders/:id`, `/api/users/:id`) to ensure requests verify resource ownership against the currently logged-in user.
* **The Risk:** Users can view, modify, or delete other users' private records simply by altering numeric IDs or UUIDs in URL paths.
* **Auto-Fix:** Injects server-side ownership checks (e.g., `where: { id: req.params.id, userId: req.user.id }`).

#### 7. Rate Limiting & Abuse Prevention
* **What it audits:** Scans public endpoints—especially `/login`, `/register`, `/forgot-password`, `/reset-password`, and OTP routes—for request thresholds.
* **The Risk:** Credential stuffing attacks, password brute-forcing, OTP enumeration, and denial-of-service abuse.
* **Auto-Fix:** Injects rate-limiting middleware (e.g., `express-rate-limit`, Redis-backed sliding window, or token bucket limiters).

#### 8. Secure Password Hashing
* **What it audits:** Examines authentication and registration handlers to ensure passwords are never stored in plaintext or hashed with outdated algorithms (MD5, SHA-1, plain SHA-256).
* **The Risk:** Database leaks allow immediate offline cracking of user credentials.
* **Auto-Fix:** Replaces weak algorithms with memory-hard, salted algorithms: `Argon2id` (preferred) or `bcrypt` with work factor >= 12.

#### 9. Multi-Factor Authentication (MFA / 2FA) Readiness
* **What it audits:** Checks whether critical admin operations, financial transactions, or security settings support step-up validation.
* **The Risk:** Compromised user credentials result in total unauthorized account takeover.
* **Auto-Fix:** Adds TOTP / WebAuthn validation guards or step-up authentication checks on high-risk routes.

#### 10. Server-Side RBAC & Permission Enforcement
* **What it audits:** Verifies that authorization logic is enforced on the server rather than solely on the client UI (e.g., hiding buttons in React/Vue).
* **The Risk:** Any user can bypass frontend guards by sending direct HTTP requests to internal endpoints.
* **Auto-Fix:** Injects server-side Role-Based Access Control (RBAC) middleware verifying roles and permissions on every backend route.

#### 11. Row Level Security (RLS)
* **What it audits:** Inspects database schemas (especially PostgreSQL / Supabase) where frontend clients query tables directly.
* **The Risk:** Missing RLS policies allow any authenticated or anonymous user to query the entire database table.
* **Auto-Fix:** Generates and executes SQL migrations to enable RLS (`ALTER TABLE ... ENABLE ROW LEVEL SECURITY;`) and creates scoped user access policies.

---

### 🔒 Pillar 3: Secrets & Token Management (25 Points)

#### 12. Cryptographic JWT Secrets
* **What it audits:** Checks JWT generation and signing logic for hardcoded, weak, or short secrets (`secret123`).
* **The Risk:** Attackers can brute-force the key offline and forge valid administrative tokens.
* **Auto-Fix:** Moves keys to `.env`, enforces minimum 256-bit (32 bytes) cryptographically random keys, and implements validation guards.

#### 13. Server-Side API Secrets (No Client Exposure)
* **What it audits:** Scans frontend code, client bundles, and build environment variables (`NEXT_PUBLIC_*`, `VITE_*`) for leaked private keys (Stripe, OpenAI, AWS, DB passwords).
* **The Risk:** Third parties extract your private API keys from browser DevTools, running unauthorized billable usage on your accounts.
* **Auto-Fix:** Moves sensitive API integrations to backend routes or Server Actions, removing secret keys from client bundles.

#### 14. No Auth Tokens in LocalStorage
* **What it audits:** Scans frontend authentication workflows to identify JWTs or session tokens stored in `localStorage` or `sessionStorage`.
* **The Risk:** Any minor XSS vulnerability anywhere on your site gives JavaScript full access to steal stored tokens.
* **Auto-Fix:** Refactors token delivery to secure `HttpOnly`, `Secure`, `SameSite=Lax` cookies that JavaScript cannot access.

#### 15. Removal of Default & Placeholder Credentials
* **What it audits:** Searches code for hardcoded fallback passwords, test admin credentials (`admin / admin`), or placeholder database connections.
* **The Risk:** Deploying applications with known default credentials grants immediate unauthorized root/admin access.
* **Auto-Fix:** Replaces hardcoded credentials with strict environment variable loading and adds startup validation (e.g., using `zod` or `dotenv-safe`).

---

### ⚙️ Pillar 4: Configuration & Security Hygiene (25 Points)

#### 16. Strict CORS Configuration
* **What it audits:** Examines Cross-Origin Resource Sharing (CORS) headers for wildcard permissions (`Access-Control-Allow-Origin: *`) paired with credentials.
* **The Risk:** Malicious websites can read private API responses on behalf of authenticated users.
* **Auto-Fix:** Restricts CORS strictly to explicit, trusted frontend domains and rejects wildcard origins when credentials are included.

#### 17. Webhook HMAC Signature Verification
* **What it audits:** Checks third-party webhook receivers (Stripe, PayPal, Paddle, GitHub) to ensure incoming payloads are verified using cryptographic signatures.
* **The Risk:** Attackers can send fake payment-successful or account-updated payloads to trigger unauthorized services.
* **Auto-Fix:** Implements cryptographic HMAC signature validation against the provider's signing secret using raw request buffers.

#### 18. Disable Production Source Maps
* **What it audits:** Checks frontend bundler configs (`vite.config.ts`, `next.config.js`, `webpack.config.js`) to see if source maps (`.map`) are published publicly.
* **The Risk:** Exposes your original, unminified source code, internal comments, and file architecture to anyone in browser DevTools.
* **Auto-Fix:** Configures build tools with `productionSourceMap: false` or `sourcemap: false` for production builds.

#### 19. Exclude Sensitive Data from Application Logs
* **What it audits:** Inspects logging statements (`console.log`, Winston, Morgan, Pino) for printed passwords, credit card numbers, authorization headers, or PII.
* **The Risk:** Leaked server logs or third-party log aggregation services expose sensitive user and payment data.
* **Auto-Fix:** Implements sanitization and masking middleware to scrub passwords, bearer tokens, and PAN data before writing logs.

#### 20. Vulnerable Third-Party Dependencies
* **What it audits:** Scans `package.json`, `requirements.txt`, or dependency locks for packages with known CVE vulnerabilities.
* **The Risk:** Attackers exploit known public vulnerabilities in third-party packages to execute remote code or launch denial of service.
* **Auto-Fix:** Runs package audit commands (`npm audit fix`, `pip-audit`), bumps vulnerable packages to secure versions, and recommends automated dependency monitoring (Dependabot, Snyk).

---

## 🔄 The 3-Step Remediation Cycle

```
[1. Initial Scan] ---> [2. Autonomous Auto-Fix] ---> [3. 100/100 Verification]
Score: 54/100          Patches files directly        Score: 100/100 (Verified)
```

1. **Initial Scan & Score:** Evaluates all 20 security criteria and assigns an initial score.
2. **Auto-Remediation:** Edits code directly—swapping raw SQL with parameterized queries, wrapping DOM nodes with sanitizers, moving tokens to `HttpOnly` cookies, and implementing rate limits.
3. **Verification:** Re-scans all modified files to ensure zero regressions and prints the final **100/100 scorecard**.

---

## 🚀 How to Use

### 1. In Google Antigravity (AGY)
To use across **all projects** globally on your machine:
```bash
# Windows
mkdir "%USERPROFILE%\.gemini\config\skills\web-security-audit"
copy .agents\skills\web-security-audit\SKILL.md "%USERPROFILE%\.gemini\config\skills\web-security-audit\"

# macOS / Linux
mkdir -p ~/.gemini/config/skills/web-security-audit
cp .agents/skills/web-security-audit/SKILL.md ~/.gemini/config/skills/web-security-audit/
```

**Prompt your agent:**
> *"Audit my project's security, fix all issues, and make sure the score reaches 100/100"*

---

### 2. In Anthropic Claude (Claude Code / Projects)
- **Claude Code CLI:** Keep `CLAUDE.md` in your project root or `~/.claude/`.
- **Claude Projects:** Paste `CLAUDE.md` into Project Instructions.

**Prompt Claude:**
> *"Run a security audit, automatically patch all vulnerabilities, and ensure a 100/100 security score"*

---

### 3. In Cursor AI
- Keep `.cursorrules` in your project root. Cursor will proactively enforce the 100/100 security benchmark and auto-remediate security bugs.

---

## 📖 Detailed Reference

For in-depth explanations, code samples, vulnerable vs. secure patterns for each of the 20 points, read [SECURITY-CHECKLIST.md](./SECURITY-CHECKLIST.md).

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](./LICENSE) file for details.
