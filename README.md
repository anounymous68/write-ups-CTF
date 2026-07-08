# Machine Writeups

> **Details reconstructed from memory of my own completed walkthroughs — exact commands/outputs may be paraphrased.**

**Author:** [Mostafa Tamime](mailto:mostafatamime8@gmail.com)

Personal writeups for machines and rooms I completed while building toward **PJPT**-level skills. These sit alongside my technique-based lab folders ([web-pentest-lab](../web-pentest-lab/README.md), Linux privesc labs, etc.) and document full-box methodology rather than isolated vulnerability classes.

---

## 🔗 TryHackMe Profile

[View my TryHackMe profile](https://tryhackme.com/p/mostafatamime8)

---

## TryHackMe

| Machine | Difficulty | Category | Summary |
|---------|------------|----------|---------|
| [Simple CTF](simple-ctf.md) | Easy | Web / Linux | Blind SQLi foothold, hash cracking for credentials, sudo `vim` privesc via GTFOBins |
| [Copy Fail (CVE-2026-31431)](copy-fail-cve-2026-31431.md) | Intermediate | Linux / Kernel | `authencesn` scatterlist bug, AF_ALG + `splice()` page-cache write, setuid binary patch for root |

---

## VulnHub

| Machine | Difficulty | Category | Summary |
|---------|------------|----------|---------|
| [Mr. Robot](mr-robot.md) | Intermediate | Web / WordPress | WordPress enumeration, web exploitation for initial access, Linux privesc to root |
| [Basic Pentesting 1](basic-pentesting-1.md) | Easy | Web / Linux | Directory and service enumeration, web foothold, credential reuse and local privesc |
| [Basic Pentesting 2](basic-pentesting-2.md) | Easy–Medium | Web / Linux | Multi-service recon, web app abuse, pivoting from low-priv shell to root |

---

## Hack The Box

| Machine | Difficulty | Category | Summary |
|---------|------------|----------|---------|
| [Bashed](bashed.md) | Easy | Web / Linux | Web content discovery, command execution foothold, misconfiguration-based privesc |

---

## Legal Notice

These writeups document **authorized lab environments** I completed for training. Do not use the techniques described here against systems you do not own or lack explicit permission to test.
