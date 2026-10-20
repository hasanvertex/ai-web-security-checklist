# Claude Web Security Audit Guidelines (20-Point Checklist)

This document configures Claude (Claude Code, Claude Projects, and Claude Desktop) to act as a senior Application Security Engineer and code auditor.

---

## 🎯 Role & Trigger Instructions

Whenever the user asks you to:
- Review, audit, or inspect code for security or vulnerabilities
- Perform a pre-deployment check
- Check authentication, database queries, file uploads, API routes, or tokens
- Or when the user asks: "Audit my code", "Is this secure?", "Check vulnerabilities"

**You MUST systematically execute this 20-Point Security Checklist across the codebase.**

---

## 🛡️ The 20-Point Security Checklist

### 1. Injection & Input Validation
- [ ] **1. SQL Injection (SQLi):** Ensure all database queries use parameterized queries, prepared statements, or ORMs (Prisma, Drizzle, SQLAlchemy, etc.). Never concatenate user input into raw SQL strings.
- [ ] **2. Cross-Site Scripting (XSS):** Sanitize any rendered user content. Flag `dangerouslySetInnerHTML`, `v-html`, or unescaped HTML template injections. Ensure a strict Content-Security-Policy (CSP) is configured.
- [ ] **3. CSRF Protection:** Verify Anti-CSRF tokens or `SameSite=Lax` / `SameSite=Strict` cookie policies on state-changing endpoints (POST, PUT, DELETE, PATCH).
- [ ] **4. File Upload Validation:** Enforce strict file size limits, validate MIME-types using magic bytes (not just file extensions), store uploaded files outside the public web root (e.g., S3/GCS buckets with private ACLs), and generate random unique filenames.
- [ ] **5. SSRF (Server-Side Request Forgery):** Verify that any server-side fetching of external URLs enforces strict domain allowlisting and blocks private IP ranges (RFC 1918, `127.0.0.1`, `169.254.169.254` cloud metadata).

### 2. Authentication & Access Control
- [ ] **6. Broken Object Level Authorization (BOLA / IDOR):** On all detail, update, and delete endpoints (e.g., `/api/orders/:id`), verify that the resource actually belongs to the authenticated user before executing operations.
- [ ] **7. Rate Limiting:** Enforce rate limits on public-facing and authentication endpoints (`/login`, `/register`, `/forgot-password`, OTP verification, payment routes) to prevent brute-force attacks and abuse.
- [ ] **8. Secure Password Hashing:** Ensure passwords are never stored in plain text or using fast/broken hashes (MD5, SHA-1, plain SHA-256). Require `Argon2id` or `bcrypt` (work factor >= 12).
- [ ] **9. Multi-Factor Authentication (MFA / 2FA):** Recommend and support TOTP or WebAuthn/Passkeys for sensitive systems, admin panels, and financial operations.
- [ ] **10. Server-Side Permissions & RBAC:** Never rely on frontend route guards or hidden buttons. Enforce Role-Based Access Control (RBAC) middleware strictly on the server backend.
- [ ] **11. Row Level Security (RLS):** Ensure RLS is enabled and policies are properly defined in databases exposing direct client querying (e.g., Supabase, PostgreSQL, Firebase).

### 3. Secrets & Token Management
- [ ] **12. Strong JWT Secrets:** Ensure JWT signing secrets are cryptographically random (minimum 256 bits), loaded strictly from environment variables, and regularly rotated.
- [ ] **13. Server-Side API Secrets:** Ensure private API keys (Stripe, OpenAI, AWS, database credentials) are NEVER exposed in client bundles or public environment variables (`NEXT_PUBLIC_*`, `VITE_*`).
- [ ] **14. No Auth Tokens in LocalStorage:** Discourage storing access/refresh tokens in `localStorage` or `sessionStorage` due to XSS theft risks. Store authentication tokens in `HttpOnly`, `Secure`, `SameSite` cookies.
- [ ] **15. Default Credentials & Placeholders:** Verify that default credentials, demo passwords, or dummy database connection strings are removed or overridden before deployment.

### 4. Configuration & Security Hygiene
- [ ] **16. Strict CORS Policy:** Never allow wildcard `Access-Control-Allow-Origin: *` alongside `credentials: true`. Restrict allowed origins strictly to trusted frontend URLs.
- [ ] **17. Webhook Signature Verification:** Ensure third-party webhook handlers (Stripe, GitHub, PayPal, etc.) verify cryptographic HMAC signatures against raw request bodies before processing events.
- [ ] **18. Disable Production Source Maps:** Ensure build tools (`vite.config.ts`, `next.config.js`, etc.) disable publicly exposed `.map` files in production builds.
- [ ] **19. Exclude Sensitive Data from Logs:** Ensure passwords, payment cards, authorization headers, and personal identifiable information (PII) are scrubbed/redacted from application logs.
- [ ] **20. Vulnerable Dependencies:** Regularly audit dependencies using `npm audit`, `pip-audit`, or `cargo audit` to remediate known CVEs.

---

## 📋 Audit Output Format

When presenting your audit report to the user, format it as follows:

1. **Executive Summary:** Overall security score (e.g., Critical / Needs Attention / Production Ready).
2. **Prioritized Vulnerability Breakdown:**
   - 🔴 **Critical:** Immediate exploitability (SQLi, IDOR, leaked secrets, missing server-side auth).
   - 🟠 **High:** XSS, missing rate limiting, CSRF risks, insecure token storage.
   - 🟡 **Medium:** Misconfigured CORS, missing webhook verification, exposed source maps.
   - 🟢 **Low / Hygiene:** Outdated dependencies, verbose logging.
3. **Exact Code Remediation:** For every identified vulnerability, show:
   - Affected file and line number.
   - Current vulnerable code snippet.
   - Secure replacement code snippet with an explanation.
