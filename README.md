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
