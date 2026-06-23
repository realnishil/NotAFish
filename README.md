<div align="center">

# 🐟 NOTAFISH

███╗   ██╗ ██████╗ ████████╗ █████╗ ███████╗██╗███████╗██╗  ██╗
████╗  ██║██╔═══██╗╚══██╔══╝██╔══██╗██╔════╝██║██╔════╝██║  ██║
██╔██╗ ██║██║   ██║   ██║   ███████║█████╗  ██║███████╗███████║
██║╚██╗██║██║   ██║   ██║   ██╔══██║██╔══╝  ██║╚════██║██╔══██║
██║ ╚████║╚██████╔╝   ██║   ██║  ██║██║     ██║███████║██║  ██║
╚═╝  ╚═══╝ ╚═════╝    ╚═╝   ╚═╝  ╚═╝╚═╝     ╚═╝╚══════╝╚═╝  ╚═╝

🐠 Heuristic Phishing URL Checker
⚡ Fast • Lightweight • Terminal Friendly • MIT Licensed

</div>

<p align="center">
<img src="https://img.shields.io/badge/Python-3.8+-blue?style=for-the-badge">
<img src="https://img.shields.io/badge/License-MIT-green?style=for-the-badge">
<img src="https://img.shields.io/badge/Security-Phishing-red?style=for-the-badge">
<img src="https://img.shields.io/badge/CLI-Terminal-black?style=for-the-badge">
</p>

---

# 🐟 What is NotAFish?

**NotAFish** is a lightweight command-line phishing URL detector that analyzes URLs using multiple heuristic techniques.

It detects suspicious domains, typosquatting attempts, malicious TLDs, fake brands, URL shorteners, suspicious keywords, DNS failures, and many other indicators.

The goal is simple:

> "If it smells phishy, it probably is."

---

# ✨ Features

✅ HTTPS detection  
✅ Raw IP address detection  
✅ Punycode attack detection  
✅ URL shortener detection  
✅ Suspicious TLD detection  
✅ Excessive subdomain detection  
✅ @ symbol masking detection  
✅ Brand typosquatting detection  
✅ Lookalike domains  
✅ Suspicious keywords  
✅ Long URL detection  
✅ Non-standard ports  
✅ DNS resolution checks  
✅ Colored terminal output  
✅ Risk scoring system  

---

# 📸 Preview

```bash
$ notafish paypal-login-security.xyz

--------------------------------------------------

URL: paypal-login-security.xyz
Host: paypal-login-security.xyz

Risk Score:
██████████████████████████░░░░ 78/100

Verdict:
[DANGEROUS]

Findings:

[HIGH] hostname looks similar to brand 'paypal'
[MED ] suspicious top-level domain (.xyz)
[MED ] hyphenated host resembles a known brand
[LOW ] suspicious keyword: login
