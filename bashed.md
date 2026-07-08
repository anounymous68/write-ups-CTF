# Bashed

> **Details reconstructed from memory of my own completed walkthrough — exact commands/outputs may be paraphrased.**

**Platform:** Hack The Box  
**Difficulty:** Easy  
**Category:** Web content discovery, command execution, Linux privilege escalation

---

## Overview

Bashed was one of my early Hack The Box retired machines — an accessible Linux box that emphasizes thorough web content discovery over exotic exploit chains. The theme centers on a web server hosting development artifacts and helper tooling that should not be exposed publicly. I completed it as practice for HTB's format: precise enumeration, clean foothold, methodical privesc, and proof capture — all within the platform's stable IP and reset workflow.

---

## Reconnaissance

I ran **nmap** against the assigned HTB target IP with service detection enabled, identifying the open ports and software versions on the host. Bashed-style boxes typically present HTTP as the primary attack surface, with SSH available for interactive access once credentials or keys are recovered.

Web enumeration was the critical phase:

- **gobuster** / **dirb** with multiple wordlists — including lists tuned for directories and common backup/dev path names.
- Manual browsing of discovered paths; HTB web boxes often hide the real entry point one or two levels deeper than the landing page.
- **nikto** for a quick automated pass.
- Source and comment review on any pages that looked like development or staging content.

I paid attention to anything suggesting a **developer workflow** — test pages, script editors, upload utilities, or documentation that referenced internal tooling. Those hints usually point toward command execution or credential material rather than a classic CVE.

---

## Exploitation

My foothold came from **web-accessible functionality that allowed command execution or equivalent code running on the server** — the kind of misconfiguration you find when development tools are left exposed on a production-facing host.

The general approach:

1. Map the web application completely before testing execution primitives — the vulnerable endpoint is often non-obvious.
2. Confirm code/command execution with a minimal proof ( benign callback or simple output ) before attempting a full reverse shell.
3. Stabilize the shell: proper TTY, working directory awareness, and basic environment checks.
4. Identify which user context the web process runs under — that determines the starting point for privesc.

I treated the webshell or execution context as a stepping stone, not the finish line. HTB rewards moving from web context to a more usable interactive session when the box allows it.

---

## Privilege Escalation

From the low-privileged context, I ran structured local enumeration:

- **`sudo -l`** for misconfigured elevated commands.
- Review of **user groups** and shared directories — development boxes sometimes place users in groups with dangerous write access.
- **SUID** and cron checks using standard find one-liners and manual verification.
- Inspection of processes and open ports on localhost that might reveal internal services.
- Search for credentials, keys, or automation scripts in home directories and common deploy paths.

Bashed-class privesc usually involves a **misconfiguration or ownership issue** discoverable through enumeration rather than a kernel exploit. I prioritized findings that explained why a development-themed box would grant one user influence over another user's resources or scheduled tasks.

Once I confirmed a reliable escalation path, I obtained root and captured the user and root proof flags per HTB convention.

---

## Lessons Learned

Bashed reinforced that **content discovery depth matters as much as scanner choice**. A single gobuster run with one wordlist is often insufficient on HTB; retracing with a different list or manual path guessing based on page context frequently unlocks the box.

It also highlighted the risk class of **exposed development tools** — a theme that maps directly to real-world web assessments where staging environments leak into production DNS. That connects to my broader web pentest lab work: understanding execution primitives and misconfigured admin interfaces is not academic when boxes model how developers actually leave tooling online.

Completing Bashed gave me confidence in the HTB workflow itself — stable VPN, disciplined note-taking, and resisting the urge to skip enumeration because the box is rated Easy. Easy on HTB still means complete methodology, not one-shot exploits.
