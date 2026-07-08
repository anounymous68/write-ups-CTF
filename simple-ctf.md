# Simple CTF

> **Details reconstructed from memory of my own completed walkthrough — exact commands/outputs may be paraphrased.**

**Platform:** TryHackMe  
**Difficulty:** Easy  
**Category:** Web exploitation, Linux privilege escalation  
**Path context:** Jr. Penetration Tester

---

## Overview

Simple CTF was one of my first full-box rooms on TryHackMe and a solid bridge between isolated web challenges and end-to-end Linux penetration testing. The box exposed a small web application with a login form vulnerable to blind SQL injection, which I used to recover credentials indirectly rather than through a direct auth bypass. From there, the path involved cracking a password hash to obtain a valid shell user, then abusing a misconfigured `sudo` rule to escalate to root. It reinforced the full chain: web vuln → credential recovery → foothold → privesc.

---

## Reconnaissance

I started with a targeted **nmap** scan against the room's target IP — typically a `-sC -sV` service scan on common ports to identify HTTP and SSH quickly without burning time on a full 65535-port sweep in a lab environment.

The scan revealed a web server on port 80 and **SSH** on port 22, which told me the likely path was web exploitation first, then credential-based remote access.

On the web side, I browsed the application manually and noted a login form as the primary attack surface. I ran **gobuster** (or dirb) against the web root to map directories, and used **nikto** for a quick misconfiguration pass. The login page stood out as the main injection point — no obvious admin panels or upload forms, so SQL injection on the authentication flow became my focus.

For blind SQLi specifically, I looked for behavioral differences: login success vs failure, subtle response length changes, or timing delays when injecting conditional payloads. I also checked whether error-based injection was possible before committing to slower blind extraction.

---

## Exploitation

### Blind SQL injection

The login form did not return database errors or query output directly, so I treated it as **blind SQL injection**. I used boolean-based techniques — injecting conditions that evaluated to true or false and observing whether the application treated the login as valid or invalid — to infer information one piece at a time.

My approach:

1. Confirm injectability with simple boolean probes (`' AND '1'='1` vs `' AND '1'='2`).
2. Determine query structure and column count through systematic probing.
3. Extract data character-by-character from relevant tables — usernames, password hashes, or other fields stored in the backend database.

This was slower than union-based SQLi but matched what the room taught: when you cannot see query results, response differences become your exfiltration channel.

### Hash cracking

The blind SQLi path yielded a **password hash** rather than plaintext credentials. I copied the hash into **John the Ripper** (with appropriate wordlist rules) and/or **hashcat**, identified the hash type, and ran a dictionary attack until I recovered the cleartext password.

With valid credentials in hand, I used **SSH** to obtain an interactive shell as the low-privileged user — my initial foothold on the box.

---

## Privilege Escalation

On the compromised host, I ran standard local enumeration: `sudo -l` to list permitted commands, checked for SUID binaries, reviewed cron jobs, and looked for readable sensitive files in the user's home directory.

The escalation vector was **`sudo` misconfiguration**: the low-priv user could run **vim** as root without a password. This is a well-documented GTFOBins pattern — `vim` can spawn a shell with elevated privileges when invoked through `sudo`.

My approach:

1. Confirm with `sudo -l` that `vim` (or `(ALL) NOPASSWD: /usr/bin/vim`) appears in the allowed list.
2. Run `sudo vim` and escape to a root shell using GTFOBins techniques (e.g., `:!/bin/bash` or equivalent vim command-mode shell spawn).
3. Verify root access and capture the proof flags.

This was my first hands-on experience connecting GTFOBins theory to a real room completion, and it stuck because the privesc was entirely configuration-driven — no kernel exploit required.

---

## Lessons Learned

Simple CTF taught me that **not every SQLi ends at auth bypass**. Blind extraction and hash cracking are legitimate, realistic paths on production-adjacent targets where error messages are suppressed and passwords are stored hashed.

It also cemented a habit I still use: after any web foothold, immediately run **`sudo -l`** and cross-reference results against GTFOBins before diving into kernel exploits or noisy automated privesc scanners.

The room connects directly to my broader SQLi practice — I later built a local vulnerable login lab to reproduce and demo the blind injection workflow I first practiced here.
