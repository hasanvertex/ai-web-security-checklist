# 🛡️ 20-Point Web Security Checklist for AI Coding

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Antigravity Skill](https://img.shields.io/badge/Antigravity-Skill%20Ready-blue)](https://antigravity.google)
[![Claude Code](https://img.shields.io/badge/Claude-CLAUDE.md-orange)](https://anthropic.com)
[![Cursor Rules](https://img.shields.io/badge/Cursor-.cursorrules-purple)](https://cursor.com)

A battle-tested **20-point Web Security Audit Checklist** designed for modern web applications and APIs, formatted as ready-to-use skills and rules for **Google Antigravity**, **Anthropic Claude (Claude Code)**, and **Cursor**.

---

## 💡 Why This Exists

AI coding assistants (Claude, Cursor, Codex, Copilot, Antigravity) allow developers to build software at unprecedented speed. However, AI models do not inherently write production-secure code by default.

The difference between a vulnerable app and a secure one isn't the AI model—**it's the security checklist you enforce**.

This repository gives your AI assistant the exact security runbook needed to audit, detect, and remediate vulnerabilities across your codebase.

---

## 📋 The 20 Security Pillars at a Glance

| # | Pillar | Security Check | Vulnerability Prevented |
|---|---|---|---|
| **1** | **Injection & Input** | Parameterized Queries / ORMs | SQL Injection (SQLi) |
| **2** | **Injection & Input** | Input Sanitization & Strict CSP | Cross-Site Scripting (XSS) |
| **3** | **Injection & Input** | Anti-CSRF Tokens & SameSite Cookies | Cross-Site Request Forgery (CSRF) |
| **4** | **Injection & Input** | Magic Bytes & Size Limits on Uploads | Arbitrary File Execution |
| **5** | **Injection & Input** | URL Whitelisting & RFC 1918 Blocking | Server-Side Request Forgery (SSRF) |
| **6** | **Auth & Access** | Object Ownership Validation | BOLA / IDOR (Broken Object Authorization) |
| **7** | **Auth & Access** | Sliding Window Rate Limiting | Brute-force & Credential Stuffing |
| **8** | **Auth & Access** | Argon2id / bcrypt (Work Factor >= 12) | Plaintext / Weak Password Cracking |
| **9** | **Auth & Access** | TOTP / WebAuthn / Passkeys | Unauthorized Account Takeover |
| **10** | **Auth & Access** | Server-Side Authorization Middleware | Client-Side Permission Bypass |
| **11** | **Auth & Access** | Row Level Security (RLS) Policies | Direct Database Data Leakage |
| **12** | **Secrets & Tokens** | Min 256-bit Cryptographic Keys | JWT Forgery & Offline Brute-forcing |
| **13** | **Secrets & Tokens** | Server-Side-Only API Secrets | Private Key Leakage in Client Bundles |
| **14** | **Secrets & Tokens** | HttpOnly, Secure, SameSite Cookies | Token Theft via XSS (No LocalStorage) |
| **15** | **Secrets & Tokens** | Removal of Default Credentials | Unauthorized Admin / DB Access |
| **16** | **Config & Hygiene**| Specific Origins (No `*` with credentials) | Unauthorized Cross-Origin Data Access |
| **17** | **Config & Hygiene**| HMAC Payload Signature Verification | Spoofed Payment / Event Webhooks |
| **18** | **Config & Hygiene**| Disabled Production Source Maps | Client Source Code & Logic Exposure |
| **19** | **Config & Hygiene**| Log Sanitization (Masking Passwords/PII) | Sensitive Data Exposure in Logs |
| **20** | **Config & Hygiene**| Automated Vulnerability Scanning | Known CVEs in Dependencies |

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

To use for **a specific repository**:
Simply keep `.agents/skills/web-security-audit/SKILL.md` in the project root.

**Prompt your agent:**
> *"Audit this project's security using the 20-point checklist"*

---

### 2. In Anthropic Claude (Claude Code / Projects)
- **Claude Code CLI:** Copy `CLAUDE.md` to your repository root or your global `~/.claude/` directory.
- **Claude Projects:** Paste the contents of `CLAUDE.md` into your Project Instructions.

**Prompt Claude:**
> *"Perform a pre-deployment security review on this codebase"*

---

### 3. In Cursor AI
- Copy `.cursorrules` to the root of your project directory.
- Cursor will automatically enforce these 20 checks whenever generating or refactoring code.

---

## 📖 Detailed Reference

For in-depth explanations, code samples, vulnerable vs. secure patterns for each of the 20 points, read [SECURITY-CHECKLIST.md](./SECURITY-CHECKLIST.md).

---

## 🤝 Contributing

Contributions, additional security vectors, and framework-specific patterns are welcome! Feel free to open an Issue or submit a Pull Request.

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](./LICENSE) file for details.
