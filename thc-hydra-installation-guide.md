# THC Hydra — Complete Installation & Usage Guide

> **Security note:** THC Hydra is a password/login auditing tool. Use it only against systems you own or have explicit permission to test. Unauthorized credential attacks can cause account lockouts, service disruption, or legal consequences.

## What is THC Hydra?

**THC Hydra** is a fast, parallelized network login/password auditing tool maintained by the THC (The Hacker's Choice) project.

It is designed for authorized security assessments where a tester needs to determine whether network services are protected by weak or guessable credentials.

Hydra supports many network services and authentication protocols, including SSH, FTP, HTTP authentication/forms, SMB, RDP, SMTP, IMAP, POP3, MySQL, PostgreSQL, LDAP, VNC, Telnet, SNMP, and others. The exact modules available depend on how Hydra was built.

- **Official project:** https://github.com/vanhauser-thc/thc-hydra
- **License:** AGPL-3.0
- **Current Kali package:** Hydra 9.7 (as listed by Kali Linux)

---

## What can Hydra be used for?

Hydra is useful in legitimate penetration testing, vulnerability assessment, and security research.

### Common authorized use cases

1. **Password policy auditing**
   - Test whether weak passwords can be guessed.
   - Validate password-strength policies.

2. **Network service auditing**
   - Assess authentication controls on services such as SSH, FTP, SMB, RDP, SMTP, and others.

3. **Penetration testing**
   - Test credentials when this activity is explicitly included in the engagement scope.

4. **Security lab training**
   - Practice authentication testing against intentionally vulnerable machines.

5. **Defensive validation**
   - Verify that controls such as account lockout, rate limiting, MFA, and IP-based protections are working.

---

## What Hydra is NOT

Hydra is primarily a **network authentication testing tool**.

It is not:

- A general-purpose password hash cracker.
- A replacement for John the Ripper or Hashcat when working with offline password hashes.
- A vulnerability scanner like Nmap.
- A malware or persistence tool.
- A tool that should be used to test random Internet accounts.

A useful distinction:

| Tool | Main purpose |
|---|---|
| Nmap | Network/service discovery and enumeration |
| THC Hydra | Online authentication/login auditing |
| John the Ripper | Offline password/hash auditing |
| Hashcat | High-performance offline password/hash recovery |
| Wireshark | Network traffic analysis |
| Metasploit | Exploitation and security testing framework |

---

# Installation

## 1. Kali Linux — Recommended

Kali Linux provides Hydra as a packaged tool.

Update the package index:

```bash
sudo apt update
```

Install Hydra:

```bash
sudo apt install hydra
```

Verify:

```bash
hydra -h
```

Check the installed version:

```bash
hydra -V
```

Kali also provides a separate GTK graphical package:

```bash
sudo apt install hydra-gtk
```

Launch the graphical interface:

```bash
xhydra
```

Kali's official tool page documents both `hydra` and `hydra-gtk`.

---

## 2. Debian / Ubuntu

On Debian-based distributions where the package is available:

```bash
sudo apt update
sudo apt install hydra
```

Verify:

```bash
hydra -h
```

If your distribution does not provide the package or you need a source build, use the official source-build method below.

---

# Installing from Source

Building from source is useful when you want the upstream version or need to control which optional modules are compiled.

Clone the official repository:

```bash
git clone https://github.com/vanhauser-thc/thc-hydra.git
```

Enter the directory:

```bash
cd thc-hydra
```

The official project provides an `INSTALL` file with build requirements and instructions.

For Debian/Ubuntu-style systems, install common development dependencies:

```bash
sudo apt update
sudo apt install build-essential libssl-dev libssh-dev libidn11-dev libpcre3-dev libmysqlclient-dev libpq-dev libsvn-dev firebird-dev libncurses-dev
```

Then configure:

```bash
./configure
```

Compile:

```bash
make
```

Install:

```bash
sudo make install
```

Finally verify:

```bash
hydra -h
```

### Important

Optional protocol modules can require additional development libraries. If `./configure` reports that a module cannot be compiled, read the configure output and install the corresponding development package before rebuilding.

---

# Termux / Android

The official Hydra installation documentation also describes building Hydra in Termux.

First install the required repositories/packages:

```bash
pkg install -y x11-repo
pkg install -y clang make openssl openssl-tool wget openssh coreutils gtk3
```

After obtaining the Hydra source:

```bash
./configure --prefix=$PREFIX
make
make install
```

Verify:

```bash
hydra -h
```

The command-line version does not require a graphical environment. The official documentation notes that `xhydra` requires a graphical setup in Termux.

---

# Windows

Hydra is primarily a Unix/Linux-oriented security tool.

For Windows, practical options include:

- Kali Linux in a virtual machine.
- Kali Linux through WSL where your setup supports the required packages.
- A compatible Unix-like build environment such as Cygwin.
- Building from source in a supported environment.

For a beginner, **Kali Linux is generally the simplest option** because Hydra is already packaged and maintained through Kali's repositories.

---

# Verify the Installation

Run:

```bash
hydra -h
```

You should see Hydra's command-line help.

You can also display the version:

```bash
hydra -V
```

To inspect available modules, Hydra provides module-specific help. For example:

```bash
hydra -U ssh
```

The exact module set depends on your installed build.

---

# Understanding Hydra's Syntax

A simplified form of the command is:

```text
hydra [options] service://target[:port]/module-options
```

Hydra can also use the older syntax:

```text
hydra [options] target service [module-options]
```

Common options include:

| Option | Meaning |
|---|---|
| `-l LOGIN` | Use one username |
| `-L FILE` | Read usernames from a file |
| `-p PASS` | Use one password |
| `-P FILE` | Read passwords from a file |
| `-C FILE` | Use combined `login:password` entries |
| `-s PORT` | Specify a non-default port |
| `-S` | Use SSL/TLS where supported |
| `-t TASKS` | Number of parallel tasks |
| `-f` | Stop after finding a valid credential pair |
| `-o FILE` | Write output to a file |
| `-v` | Verbose output |
| `-V` | Show each login/password attempt |
| `-R` | Restore an interrupted session |
| `-I` | Ignore an existing restore file |

Always run:

```bash
hydra -h
```

before using an unfamiliar option, because syntax and module capabilities can vary by version.

---

# Wordlists

Hydra can consume username and password lists.

For example:

```text
users.txt
passwords.txt
```

A username list may look like:

```text
admin
test
student
```

A password list may look like:

```text
Password123
Welcome123
TrainingPassword!
```

For authorized testing, use a **small, purpose-built test list** whenever possible. Large wordlists can create unnecessary traffic and may trigger account lockouts or defensive controls.

Kali may provide additional wordlists through separate packages. Do not assume that every Kali installation contains the same lists.

---

# Safe Practice Lab

The safest way to learn Hydra is to create a deliberately controlled authentication lab.

A simple lab can contain:

```text
Your computer
     |
     +---- Kali Linux
     |
     +---- Local test service / intentionally vulnerable VM
```

Use private IP addresses and accounts created specifically for the lab.

### Example test data

Create:

```bash
mkdir -p ~/hydra-lab
cd ~/hydra-lab
```

Create a username list:

```bash
printf "labuser\n" > users.txt
```

Create a deliberately weak test password list:

```bash
printf "WrongPassword\nTraining123!\n" > passwords.txt
```

These files are safe examples for a lab because they contain test credentials rather than real accounts.

---

# Example: Inspecting SSH Module Help

Before performing any authentication test, inspect the module:

```bash
hydra -U ssh
```

This shows module-specific options supported by your installed version.

For an authorized lab, Hydra's general structure looks like:

```bash
hydra -l labuser -P passwords.txt ssh://LAB_IP
```

Replace `LAB_IP` only with the IP address of a machine you own or are explicitly authorized to test.

**Do not substitute a public server, workplace system, school system, cloud account, or another person's device without written authorization.**

---

# Example: Controlling Parallel Tasks

Hydra can perform multiple authentication attempts concurrently.

The general option is:

```bash
-t TASKS
```

For example, a deliberately conservative lab test could use:

```bash
-t 1
```

Higher concurrency can increase speed but also increases network traffic and the risk of:

- Account lockouts
- IDS/IPS alerts
- Service instability
- Rate-limit triggers
- Unwanted load

In professional assessments, start conservatively and increase concurrency only when the rules of engagement permit it.

---

# Common Services

Hydra includes modules for many authentication services.

Depending on the build, commonly available modules include:

- SSH
- FTP
- HTTP/HTTPS authentication
- SMB
- RDP
- SMTP
- IMAP
- POP3
- MySQL
- PostgreSQL
- LDAP
- VNC
- Telnet
- SNMP
- SOCKS
- Cisco authentication
- VMware authentication
- XMPP

Check your installation instead of assuming a module is available:

```bash
hydra -U MODULE
```

For example:

```bash
hydra -U ssh
```

---

# HTTP Authentication vs Web Forms

This distinction is important.

Hydra can test several HTTP authentication mechanisms, but a normal modern web application login form may require module-specific parameters.

A website login can involve:

- HTTP Basic Authentication
- HTTP Digest Authentication
- POST form authentication
- CSRF tokens
- Cookies
- JavaScript-generated values
- CAPTCHA
- MFA
- Web Application Firewall rules
- API-based authentication

Therefore, simply knowing the username/password fields is often not enough to reproduce a modern login workflow.

Always inspect the relevant Hydra module documentation:

```bash
hydra -U MODULE
```

---

# Hydra and HTTPS

Some modules support SSL/TLS.

The general SSL option is:

```bash
-S
```

A service may also expose TLS-specific module syntax.

Always check:

```bash
hydra -U MODULE
```

Do not assume that adding `-S` is correct for every protocol.

---

# Output Files

Hydra can save results to a file with:

```bash
-o results.txt
```

Example structure:

```bash
hydra [authorized-test-options] -o results.txt SERVICE
```

Keep security-test output protected because successful credential discoveries are sensitive information.

Never commit real credentials, password lists containing real passwords, or assessment secrets to a public GitHub repository.

---

# Restore Interrupted Sessions

Hydra supports restoring interrupted sessions.

Useful options include:

```bash
-R
```

and:

```bash
-I
```

Read the help for your installed version:

```bash
hydra -h
```

---

# Hydra GUI

The GTK front end is commonly called **xhydra**.

On Kali:

```bash
sudo apt install hydra-gtk
```

Start it:

```bash
xhydra
```

The GUI can make it easier for beginners to understand the relationship between:

- Target
- Protocol
- Username
- Password list
- Port
- SSL
- Other module parameters

For repeatable professional work, however, the command-line interface is generally easier to document and automate.

---

# Troubleshooting

## `hydra: command not found`

Install the package:

```bash
sudo apt update
sudo apt install hydra
```

Then:

```bash
hydra -h
```

If you built from source, verify that the installation directory is in your `PATH`.

---

## `./configure` fails

Read the final lines of the configure output.

A missing optional dependency may prevent a particular protocol module from being compiled.

Install the appropriate development package and run:

```bash
./configure
make
```

again.

---

## A module is missing

Run:

```bash
hydra -U MODULE
```

If the module is unavailable, inspect the output of:

```bash
hydra -h
```

and review the configure/build output.

---

## Authentication attempts are being rejected

Possible causes include:

- Incorrect username.
- Incorrect password list.
- Wrong service/port.
- Wrong authentication method.
- TLS/SSL mismatch.
- Account lockout.
- Rate limiting.
- MFA.
- CAPTCHA.
- WAF/IDS/IPS controls.
- The application requires a workflow Hydra's module does not support.

Do not immediately increase the number of threads. First verify the protocol and authentication workflow.

---

# Security Considerations

Hydra performs **online authentication attempts**. That makes it fundamentally different from offline password cracking.

Every online attempt can be visible to the target.

Defensive systems may detect:

- Repeated failed logins.
- Multiple usernames from one source.
- Multiple passwords against one account.
- Abnormally high authentication frequency.
- Distributed login attempts.
- Suspicious source IP addresses.

Organizations can mitigate credential attacks using:

- Strong passwords.
- Password managers.
- MFA/passkeys.
- Rate limiting.
- Account lockout or progressive delays.
- IP reputation controls.
- WAF/IDS/IPS monitoring.
- Login anomaly detection.
- Disablement of unnecessary services.
- SSH key authentication instead of passwords where appropriate.

---

# Hydra vs John the Ripper

These tools are often confused because both can be used for password security auditing.

### THC Hydra

Best suited to:

```text
Online authentication testing
        ↓
Network service
        ↓
Repeated login attempts
        ↓
Authentication controls
```

### John the Ripper

Best suited to:

```text
Password hash
        ↓
Offline password recovery/auditing
        ↓
Compare candidate passwords
        ↓
Identify weak passwords
```

In a professional assessment, both can have a place, but they solve different problems.

---

# Recommended Professional Workflow

A responsible assessment should not begin by blindly attempting passwords.

A better workflow is:

```text
1. Define authorization and scope
          ↓
2. Identify the target service
          ↓
3. Confirm the authentication method
          ↓
4. Create a small approved credential test set
          ↓
5. Perform a conservative test
          ↓
6. Monitor service health and defensive controls
          ↓
7. Stop when the testing objective is satisfied
          ↓
8. Document findings
          ↓
9. Recommend remediation
          ↓
10. Retest after remediation
```

This is safer and produces more useful security evidence than simply maximizing the number of login attempts.

---

# Ethical and Legal Use

Only use Hydra when at least one of the following applies:

- You own the target.
- You have explicit written authorization.
- The system is an intentionally vulnerable training environment.
- The activity is covered by a clearly defined penetration-testing scope.

Never use Hydra to attack:

- Random websites.
- Other people's accounts.
- Public SSH/RDP/FTP servers without authorization.
- Social-media accounts.
- Email accounts.
- Banking systems.
- Workplace systems without authorization.
- Cloud services outside your approved scope.

---

# Official Resources

### THC Hydra — Official GitHub Repository

https://github.com/vanhauser-thc/thc-hydra

### Kali Linux — Hydra Documentation

https://www.kali.org/tools/hydra/

### Hydra Source Installation Instructions

https://github.com/vanhauser-thc/thc-hydra/blob/master/INSTALL

---

# Quick Reference

## Install on Kali

```bash
sudo apt update
sudo apt install hydra
```

## Verify

```bash
hydra -h
hydra -V
```

## Install GUI

```bash
sudo apt install hydra-gtk
```

## Launch GUI

```bash
xhydra
```

## Inspect a module

```bash
hydra -U ssh
```

## Common input options

```text
-l USER       Single username
-L FILE       Username list
-p PASS       Single password
-P FILE       Password list
-C FILE       login:password combinations
-s PORT       Custom port
-S            SSL/TLS
-t TASKS      Parallel tasks
-f            Stop after a successful credential
-o FILE       Save output
-R            Restore session
-I            Ignore existing restore file
```

---

## Final Takeaway

THC Hydra is a specialized **online authentication auditing tool**. Its value is not simply that it can try many passwords quickly; its real security-testing value is that it helps assess whether exposed authentication services can withstand weak-credential attacks.

Used correctly, Hydra can help security professionals identify weak authentication controls and demonstrate the importance of strong passwords, MFA, rate limiting, account protection, and secure service configuration.

Use it deliberately, within scope, and with conservative testing parameters.

---

**Repository suggestion**

Save this document as:

```text
README.md
```

Recommended repository name:

```text
thc-hydra-installation-guide
```

Suggested repository description:

```text
A practical installation, configuration, and authorized security-testing guide for THC Hydra.
```
[Explore More Tools](https://labs.jahidshah.com/Best-Kali-Linux-tools/)
