# Techniques-to-Bypass-Insecure-Two-Factor-Authentication-2FA-
A curated repository of 2FA bypass methods, misconfigurations, and advanced exploitation techniques | with checklists, examples, and mitigation strategies.


# 🔐 2FA Bypass Techniques & Security Research  

Two-Factor Authentication (2FA) is widely used to secure accounts, but misconfigurations and flawed implementations often make it bypassable.  
This repository serves as a **comprehensive research hub** for identifying, testing, and mitigating 2FA bypass vulnerabilities.  

✅ Ideal for:  
- Security researchers & penetration testers  
- Developers implementing authentication  
- Red teamers performing adversary simulations  
- Students learning authentication security  

⚠️ **Important:**  
This project is for **educational purposes only**. Unauthorized use of these methods on systems you do not own is **illegal**.  

---

## 📑 Table of Contents  

1. [Introduction](#-introduction)  
2. [Common Bypass Techniques](#-common-bypass-techniques)  
3. [Misconfiguration Exploits](#-misconfiguration-exploits)  
4. [Advanced Techniques](#-advanced-techniques)  
5. [Real-World Attack Scenarios](#-real-world-attack-scenarios)  
6. [Comprehensive 2FA Checklist](#-comprehensive-2fa-checklist)  
7. [Visual Attack Flow](#-visual-attack-flow)  
8. [References](#-references)  
9. [Legal & Ethical Notice](#-legal--ethical-notice)  

---

## 🚀 Introduction  

- **What is 2FA?**  
Two-Factor Authentication adds an extra security layer by requiring a second proof of identity (OTP, SMS, App code, U2F key) in addition to a password.  

- **Why Study 2FA Bypass?**  
Even though 2FA is considered secure, **real-world deployments** are often flawed. Attackers can exploit logic errors, weak integrations, and misconfigurations to bypass protections.  

- **Goal of this Repo:**  
    - Help developers understand **how attacks work**.  
    - Provide testers with a **checklist for auditing**.  
    - Document **realistic scenarios** seen in CTFs and bug bounty programs.  

---

## 🚩 Common Bypass Techniques  

### 1. Flawed Verification Logic  
- **Issue:** Server only checks for a valid session, not the OTP itself.  
- **Example:** Directly navigating to `/dashboard` after login without submitting OTP.  

### 2. Clickjacking on 2FA Disable  
- **Method:** Embedding disable-2FA page in an iframe.  
- **Impact:** Victim unknowingly clicks to disable their own protection.  

### 3. Response Tampering  
- **Example:** API returns `{ "success": false }` → attacker changes to `{ "success": true }`.  
- **Impact:** Client-side trust allows bypass.  

### 4. Status Code Manipulation  
- **Example:** Changing HTTP `401 Unauthorized` → `200 OK` through proxy tools.  
- **Impact:** Some clients assume 2FA completed.  

### 5. OTP Reuse  
- **Issue:** Old OTPs remain valid across sessions.  
- **Impact:** Replay attacks allow persistent bypass.  

### 6. CSRF on 2FA Disable  
- **Attack:** Force victim’s browser to send a valid session request to disable 2FA.  

### 7. Referer Header Manipulation  
- **Attack:** Change `Referer` header to make the server assume OTP was verified.  

---

## ⚙️ Misconfiguration Exploits  

- **No Brute-Force Controls** – attacker tries infinite OTPs.  
- **OTP Shared Across Users** – code from user A valid for user B.  
- **Password Reset Disables 2FA** – insecure recovery flows.  
- **Leaked Tokens** – OTPs visible in API response or logs.  
- **Unlimited OTP Requests** – drain SMS/email resources, reset rate limits.  
- **Backup Code Weakness** – short, guessable, or reusable codes.  
- **Client-Side Validation Only** – OTP check done in JavaScript, not server.  

---

## 🛡️ Advanced Techniques  

### 1. Session Hijacking & Persistence  
- Attackers use old sessions that remain valid even after enabling 2FA.  

### 2. Race Condition on OTP Validation  
- Sending multiple OTP submissions in parallel may bypass validation logic.  

### 3. Guessable Tokens or Cookies  
- Weak “remember me” cookies (`user:123|time:1690|secret`) can be brute-forced.  

### 4. Subdomain / Legacy Weaknesses  
- Example: `old.example.com` login does not enforce 2FA but shares same cookies.  

### 5. Social Engineering + MFA Fatigue  
- Attackers repeatedly push 2FA prompts until the victim approves.  

### 6. API Endpoint Bypass  
- Mobile or undocumented APIs often skip OTP enforcement.  

### 7. Time-Based Attacks  
- Exploiting predictable TOTP generation (bad key space, short secrets).  

---

## 🎯 Real-World Attack Scenarios  

- **Password Reset Flow Bypass:** OTP not enforced during reset → full takeover.  
- **OAuth / SSO Integration Flaws:** 2FA skipped on third-party login providers.  
- **Phishing Kits:** Capturing OTP in real time using reverse proxies (e.g., Modlishka).  
- **SIM Swap Attacks:** OTP sent to attacker-controlled phone number.  
- **Stolen Backup Codes:** Often stored in plaintext or shared insecurely.  

---

## ✅ Comprehensive 2FA Checklist  

🔎 **Basic Tests:**  
- [ ] Direct access to endpoints without OTP submission.  
- [ ] OTP replay across sessions or accounts.  
- [ ] Check server rejects expired OTPs.  
- [ ] Verify session expiry after enabling/disabling 2FA.  

🔎 **Brute-Force Protection:**  
- [ ] OTP attempts limited per user/IP/device.  
- [ ] Lockout after N invalid attempts.  
- [ ] CAPTCHA triggered after multiple failed attempts.  

🔎 **Token & Recovery Handling:**  
- [ ] Backup codes unique and one-time use.  
- [ ] Password reset flow enforces OTP.  
- [ ] Tokens never exposed in responses/logs.  

🔎 **Advanced Cases:**  
- [ ] API endpoints also enforce 2FA.  
- [ ] Legacy subdomains tested for bypass.  
- [ ] Mobile apps enforce OTP equally.  
- [ ] OTP algorithm verified for randomness.  
- [ ] Clickjacking protection on 2FA settings page.  
- [ ] 2FA cannot be disabled without re-entering password.  

---

## 🖼️ Visual Attack Flow  

```mermaid
flowchart TD
    A[Login with Username/Password] --> B{2FA Challenge?}
    B -->|Yes| C[Enter OTP]
    B -->|No| D[Bypass Detected]
    C --> E{Valid OTP?}
    E -->|Yes| F[Access Granted]
    E -->|No| G[Access Denied]
    D --> H[Attacker Gains Access]
