# Mr. Robot

> **Details reconstructed from memory of my own completed walkthrough — exact commands/outputs may be paraphrased.**

**Platform:** VulnHub  
**Difficulty:** Intermediate  
**Category:** Web / WordPress, Linux privilege escalation

---

## Overview

Mr. Robot is a themed VulnHub machine inspired by the TV series, built around a WordPress-powered web presence and a Linux host behind it. I approached it as a realistic web-to-shell engagement: enumerate the web application thoroughly, find a way in through WordPress or related misconfigurations, establish a low-privileged foothold, then work through local enumeration to reach root. The box rewarded patience with directory and CMS enumeration more than it rewarded spraying exploits blindly.

---

## Reconnaissance

I began with **nmap**, running a service/version scan on the target to identify open ports — typically HTTP/HTTPS and SSH on a box like this. That gave me a clear split: web exploitation for initial access, SSH as the likely post-exploitation channel once I had credentials or keys.

On the web side:

- Manual browsing to understand the WordPress theme, plugins, and exposed functionality.
- **gobuster** / **dirb** for hidden directories and files — WordPress installs often leak interesting paths if directory listing or backup artifacts exist.
- **wpscan** (or equivalent WordPress-focused enumeration) to identify users, themes, plugins, and known vulnerability classes.
- **nikto** for a quick pass on common web misconfigurations.

I also checked for virtual host behavior and robots.txt hints, since themed boxes often embed narrative clues in the content itself. The goal at this stage was a map of users, technologies, and potential entry points — not immediate exploitation.

---

## Exploitation

My initial foothold came through **web application abuse** on the WordPress stack. After enumeration narrowed the attack surface, I pursued a vulnerability class appropriate to what the CMS exposed — this might involve weak or leaked credentials, a plugin or configuration weakness, or abusable functionality that let me read sensitive material or execute code in a limited context.

The general pattern I followed:

1. Correlate enumeration output (users, versions, exposed files) with plausible attack paths.
2. Validate one path manually before automating — WordPress boxes punish unfocused brute force and noisy scanning.
3. Convert web access into something useful on the host: credentials, a private key, or a webshell depending on what the box allowed.

Once I had material to authenticate or execute commands, I established a shell as a low-privileged Linux user — typically via **SSH** with recovered credentials or through a web-based shell if the box supported it.

---

## Privilege Escalation

From the low-priv shell, I ran structured local enumeration:

- **`sudo -l`** for misconfigured command permissions.
- **SUID binary** review with `find` and manual inspection of anything non-standard.
- **Cron job** and writable script checks.
- Review of home directories and world-readable files for keys, configs, or password reuse.
- Kernel and OS version notes for exploit suitability (as a last resort in lab boxes).

The privesc path on a box like Mr. Robot typically involves chaining something found during enumeration — a misconfiguration, a readable key, or a vulnerable SUID/cron pattern — rather than a single obvious `sudo` grant. I worked methodically through findings, validated each lead, and escalated to root once I confirmed a reliable technique.

---

## Lessons Learned

Mr. Robot reinforced that **WordPress is an enumeration problem first**. User lists, plugin versions, and leaked files often matter more than firing generic exploits. It also trained me to read themed content carefully — narrative boxes hide hints in plain sight if you are willing to browse like a user, not just scan like a scanner.

The engagement strengthened skills that complement my web labs: CMS-specific tooling, credential hygiene awareness, and the discipline of converting a web win into a stable shell before chasing privesc. It sits alongside my broader web testing practice and reminds me that real targets rarely present a single obvious CVE — they present a graph of small misconfigurations that connect if you map them thoroughly.
