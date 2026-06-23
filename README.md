```
        )    (
       (    )  )
        )  (  (
       _____
      /     \    NOTAFISH
     ( o   o )   phishing URL checker
      \  ^  /
       \___/
```

<div align="center">

# 🐟 NotAFish

### A heuristic phishing URL checker that lives in your terminal

[![License: MIT](https://img.shields.io/badge/License-MIT-39d353?style=for-the-badge)](#-license)
[![Python](https://img.shields.io/badge/Python-3.7%2B-ffd33d?style=for-the-badge&logo=python&logoColor=black)](#-requirements)
[![Platform](https://img.shields.io/badge/Platform-macOS%20%7C%20Linux%20%7C%20WSL-00bcd4?style=for-the-badge)](#-installation)
[![Made for](https://img.shields.io/badge/Made%20for-the%20terminal-ff5e5e?style=for-the-badge)](#-usage)

*"It's probably not a fish. But let's check anyway."* 🎣

</div>

---

## 📖 Table of Contents

- [What is NotAFish?](#-what-is-notafish)
- [Features](#-features)
- [Installation](#-installation)
- [Usage](#-usage)
- [How Scoring Works](#-how-scoring-works)
- [Detection Heuristics](#-detection-heuristics)
- [Example Output](#-example-output)
- [CLI Reference](#-cli-reference)
- [Exit Codes & Automation](#-exit-codes--automation)
- [Roadmap](#-roadmap)
- [Contributing](#-contributing)
- [License](#-license)

---

## 🎯 What is NotAFish?

**NotAFish** is a lightweight, zero-dependency, MIT-licensed command-line tool that inspects a URL and tells you — in glorious ANSI color — how suspicious it looks. It's not a magic phishing oracle, it's a **heuristic scoring engine**: it checks for the kinds of red flags that show up over and over again in real phishing campaigns (typosquatted brand names, punycode homographs, sketchy TLDs, shady redirectors, malformed hosts, and more), tallies up a risk score, and gives you a clear verdict.

Think of it as a fast, offline-friendly second opinion before you click that link your "bank" texted you.

```
┌──────────────────────────────────────────────┐
│  paypa1-secure-login.tk/account/verify.php    │
│                                                │
│  Risk Score:  ████████████████░░░░  62/100    │
│  Verdict:     [ DANGEROUS ]                   │
└──────────────────────────────────────────────┘
```

---

## ✨ Features

| 🚩 | Capability |
|---|---|
| 🧬 | **Typosquat detection** — Levenshtein-distance brand lookalike matching (`paypa1.com`, `mlcrosoft.com`, etc.) |
| 🕵️ | **Homograph/punycode detection** — flags `xn--` encoded hosts used in IDN spoofing attacks |
| 🌐 | **Raw IP detection** — flags URLs hiding behind a bare IP instead of a domain |
| 🔗 | **URL shortener detection** — flags known link shorteners (`bit.ly`, `tinyurl.com`, `t.co`, …) |
| 🏴 | **Suspicious TLD detection** — flags abuse-prone TLDs (`.tk`, `.xyz`, `.top`, `.zip`, …) |
| 🔐 | **HTTPS check** — flags plaintext HTTP connections |
| 🧩 | **Keyword scanning** — flags phishing-flavored keywords (`verify`, `secure`, `billing`, `suspend`, …) |
| 🧵 | **Structural anomalies** — flags `@` tricks, excessive subdomains, malformed hosts, non-standard ports, oversized URLs |
| 📡 | **Live DNS resolution check** — flags domains that don't resolve at all |
| 📂 | **Batch mode** — scan an entire file of URLs in one pass |
| 🎨 | **Colorful terminal UI** — ANSI colors, progress-bar risk meter, optional `--no-color` for scripts/CI |
| 🪶 | **Zero external dependencies** — pure Python standard library |

---

## 📦 Installation

### Option 1 — Homebrew (recommended, macOS/Linux)

```bash
brew tap realnishil/notafish
brew install notafish
```

### Option 2 — Clone & run directly

```bash
git clone https://github.com/realnishil/notafish.git
cd notafish
chmod 755 notafish.py
./notafish.py https://example.com
```

### Option 3 — Drop it on your `$PATH`

```bash
curl -o /usr/local/bin/notafish https://raw.githubusercontent.com/realnishil/notafish/main/notafish.py
chmod 755 /usr/local/bin/notafish
notafish --help
```

> 💡 **Requirements:** Python 3.7+. No `pip install` needed — it's built entirely on the standard library (`re`, `socket`, `argparse`, `ipaddress`, `urllib.parse`).

---

## 🚀 Usage

```bash
# Check a single URL
notafish https://paypa1-secure.tk/login/verify

# Check a list of URLs from a file (one per line)
notafish -f urls.txt

# Skip DNS resolution (faster, useful when offline)
notafish https://example.com --no-resolve

# Suppress the ASCII banner/logo
notafish https://example.com --no-banner

# Disable colors (great for piping into logs/CI)
notafish https://example.com --no-color
```

---

## 🧮 How Scoring Works

Every URL starts at **0** and accumulates points as red flags are found. The total is capped at **100**, then mapped to a verdict:

```
  0 ───────────── 24      LIKELY SAFE      🟢
 25 ───────────── 49      SUSPICIOUS       🟡
 50 ───────────── 100     DANGEROUS        🔴
```

```
   Risk Meter
   ┌────────────────────────────────────────┐
   │ 🟢 LIKELY SAFE      │ 0  – 24          │
   │ 🟡 SUSPICIOUS       │ 25 – 49          │
   │ 🔴 DANGEROUS        │ 50 – 100         │
   └────────────────────────────────────────┘
```

Each finding is also tagged with a **severity**:

- 🔴 `HIGH` — strong phishing signal (typosquat, punycode, raw IP, `@` masking, DNS failure)
- 🟡 `MED` — moderate signal (no HTTPS, suspicious TLD, shortener, excessive subdomains, odd port)
- 🔵 `LOW` — weak/contextual signal (suspicious keywords, long URL, malformed host)

---

## 🔬 Detection Heuristics

| Check | Severity | Points | Trigger |
|---|:---:|:---:|---|
| Not HTTPS | MED | 10 | Scheme isn't `https` |
| Raw IP host | HIGH | 25 | Hostname is a literal IP address |
| Punycode host | HIGH | 25 | Host contains `xn--` |
| Known shortener | MED | 15 | Host matches `bit.ly`, `tinyurl.com`, etc. |
| Suspicious TLD | MED | 12 | TLD in `.zip`, `.xyz`, `.top`, `.tk`, `.gq`, etc. |
| Excessive subdomains | MED | 10 | 3+ subdomain levels |
| `@` in URL | HIGH | 20 | Classic "real URL hidden after @" trick |
| Hyphenated brand mimicry | MED | 12 | Host contains a hyphen + a known brand name |
| Brand typosquat | HIGH | 22 | Levenshtein distance ≤ 2 from a known brand |
| Suspicious keywords | LOW | 5 × (up to 3 hits) | `login`, `verify`, `secure`, `billing`, `appleid`, etc. found in URL |
| Long URL | LOW | 5 | URL exceeds 100 characters |
| Malformed host | LOW | 8 | No TLD / dot in hostname |
| Non-standard port | MED | 10 | Port isn't 80 or 443 |
| DNS failure | HIGH | 20 | Hostname fails to resolve (skipped with `--no-resolve` or for raw IPs) |

> 🏷️ **Brand list includes:** PayPal, Apple, Microsoft, Google, Amazon, Facebook, Netflix, Instagram, Bank of America, Wells Fargo, Chase, LinkedIn, eBay, Dropbox, Adobe.

---

## 🖥️ Example Output

```
$ notafish http://paypa1-account-verify.tk/secure/login.php

--------------------------------------------------
URL: http://paypa1-account-verify.tk/secure/login.php
Host: paypa1-account-verify.tk

Risk Score: ████████████████████░░░░░░░░░░ 78/100
Verdict:    [DANGEROUS]

Findings:
  [HIGH] hostname looks similar to brand 'paypal' (typosquat?)
  [MED ] connection is not HTTPS
  [MED ] suspicious top-level domain (.tk)
  [MED ] hyphenated host resembles a known brand
  [LOW ] suspicious keyword(s): login, secure, verify
```

```
$ notafish https://github.com --no-banner

--------------------------------------------------
URL: https://github.com
Host: github.com

Risk Score: ░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░ 0/100
Verdict:    [LIKELY SAFE]

No suspicious indicators found.
```

---

## ⚙️ CLI Reference

```
usage: notafish [-h] [-f FILE] [--no-resolve] [--no-banner] [--no-color] [url]

NotAFish - heuristic phishing URL checker (MIT License)

positional arguments:
  url                   URL to check

options:
  -h, --help            show this help message and exit
  -f FILE, --file FILE  file with one URL per line
  --no-resolve          skip DNS resolution check
  --no-banner           suppress logo/banner
  --no-color            disable colored output
```

---

## 🤖 Exit Codes & Automation

NotAFish prints a human-friendly report, which makes it easy to drop into scripts:

```bash
notafish "$SUSPECT_URL" --no-color --no-banner | tee -a phishing-log.txt
```

Combine with `-f` to triage a batch of URLs pulled from email headers, browser history, or a SIEM export:

```bash
notafish -f suspicious_links.txt --no-color > report.txt
```

> ⚠️ NotAFish is a **heuristic** tool, not ground truth. Pair its output with threat-intel feeds, sandboxing, or a second human glance before making security decisions.

---

## 🛣️ Roadmap

- [ ] JSON output mode for pipeline integration
- [ ] Configurable brand/keyword/TLD lists via config file
- [ ] Optional reputation API lookups (VirusTotal, urlscan.io)
- [ ] WHOIS age/registrar checks
- [ ] SSL certificate inspection (issuer, age, validity)

---

## 🤝 Contributing

Pull requests, issue reports, and heuristic ideas are welcome! If you spot a phishing pattern NotAFish misses, open an issue with an example (defanged, please 🙏) and a short description of the pattern.

```bash
git clone https://github.com/realnishil/notafish.git
cd notafish
# hack away
```

---

## 📜 License

MIT License — see [`LICENSE`](LICENSE) for details. Use it, fork it, ship it.

<div align="center">

---

Made with 🎣 by [@realnishil](https://github.com/realnishil)

*Stay safe out there. Not every link is what it claims to be.*

</div>
