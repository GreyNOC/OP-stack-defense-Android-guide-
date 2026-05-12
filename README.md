[greynoc_edc_operator_public_guide_v_0_1.md](https://github.com/user-attachments/files/27648108/greynoc_edc_operator_public_guide_v_0_1.md)
# GreyNOC EDC Operator Guide

Public Education Guide · Version 0.1 Public Draft · May 2026  
Publisher: GreyNOC · greynoc.com  
Classification: Public draft · Free to share with attribution after review

## Plain-English Summary

This guide helps operators turn an Android phone into a safe, no-root GreyNOC everyday-carry technical workspace. Android stays installed. The phone is not rooted, unlocked, flashed, or converted into a custom operating system. Instead, the operator installs Termux from F-Droid, creates a Debian Linux environment using PRoot Distro, and adds a small set of defensive tools for administration, notes, SSH, network troubleshooting, scripting, and authorized lab work.

## Best-Practice Recommendation

Do not flash random phone images, unlock bootloaders, or replace Android unless the device vendor and operating system project explicitly support that exact model. For most operators, the safer starting point is a no-root Termux and Debian workspace inside Android.

## 1. Scope and Responsible-Use Notice

This guide is written for public education, field readiness, and legitimate technical operations. It is intended for operators, students, technicians, SOC learners, home-lab builders, and small-business defenders who want a portable command-line environment on Android.

Use all tools only on devices, networks, accounts, and lab environments that you own or have explicit permission to administer or test. Unauthorized scanning, credential attacks, exploitation, interception, bypassing access controls, or testing third-party systems without written permission may violate law, policy, and service terms.

This guide does not provide exploit instructions, credential theft guidance, stealth techniques, malware, unauthorized access workflows, or phone-flashing instructions.

## 2. What This Guide Installs

| Item | Recommendation |
|---|---|
| Installed approach | Android + Termux + PRoot Distro + Debian |
| Root required | No |
| Bootloader unlock required | No |
| Android remains installed | Yes |
| Primary purpose | Portable Linux shell, SSH, scripting, documentation, and defensive troubleshooting |
| Cybersecurity use | Authorized labs, home networks, CTF-style practice, and defensive learning |
| Not included | Custom ROM flashing, packet injection, monitor mode setup, exploit chains, persistence, or bypass procedures |

Termux provides the Android terminal environment. PRoot Distro provides a way to install and manage a Linux userland inside Termux. Debian provides a stable base for Linux tools and scripting.

## 3. Operator Requirements

| Requirement | Recommendation |
|---|---|
| Android device | A phone or tablet you control and are allowed to modify at the app level |
| Storage | 8 GB free minimum; 20 GB or more recommended |
| Network | Stable Wi-Fi for package downloads |
| Power | Keep the device charged or plugged in during installation |
| Keyboard | Optional Bluetooth keyboard for easier field use |
| Account safety | Use trusted app sources and avoid random APK download sites |
| Data safety | Back up important phone data before experimenting |

## 4. Install the Base Tools

### Step 1: Install F-Droid

Install F-Droid from the official F-Droid website. F-Droid is used because the actively maintained Termux package is distributed there.

### Step 2: Install Termux from F-Droid

Open F-Droid, search for Termux, and install it. Avoid outdated Termux builds from random APK sites.

### Step 3: Grant storage access

Open Termux and run:

```bash
termux-setup-storage
```

When Android asks for permission, tap Allow.

### Step 4: Update Termux

```bash
pkg update && pkg upgrade -y
```

### Step 5: Install required Termux packages

```bash
pkg install proot-distro git wget curl nano openssh -y
```

## 5. Install Debian Inside Termux

### Step 6: Install Debian

```bash
proot-distro install debian
```

### Step 7: Log into Debian

```bash
proot-distro login debian
```

### Step 8: Update Debian

Run this from inside the Debian shell:

```bash
apt update && apt upgrade -y
```

### Step 9: Install everyday Linux tools

```bash
apt install sudo git curl wget nano vim python3 python3-pip dnsutils whois netcat-openbsd openssh-client -y
```

### Step 10: Create a normal operator user

Replace `greyuser` with your preferred username.

```bash
adduser greyuser
usermod -aG sudo greyuser
```

Exit Debian:

```bash
exit
```

Log back in as the new user:

```bash
proot-distro login debian --user greyuser
```

## 6. Create the GreyNOC EDC Workspace

Inside Debian as the normal user, create a clean folder structure:

```bash
mkdir -p ~/greynoc-edc/{notes,scripts,logs,reports,clients,lab,tools}
mkdir -p ~/greynoc-edc/reports/{templates,evidence,completed}
touch ~/greynoc-edc/notes/field-notes.md
touch ~/greynoc-edc/logs/session-log.txt
```

Recommended folder use:

| Folder | Purpose |
|---|---|
| `notes` | Operator notes, checklists, reminders |
| `scripts` | Small helper scripts |
| `logs` | Local session notes and command output from authorized work |
| `reports` | Report templates, evidence copies, completed reports |
| `clients` | Public-safe client folders; do not store secrets in plain text |
| `lab` | Home-lab and training artifacts |
| `tools` | Operator utilities and helper files |

## 7. Create a Quick Launch Command

From regular Termux, create a small helper command so the operator can start Debian by typing `linux-phone`.

First exit Debian if you are inside it:

```bash
exit
```

Then run this in Termux. Replace `greyuser` with the username you created:

```bash
echo 'proot-distro login debian --user greyuser' > $PREFIX/bin/linux-phone
chmod +x $PREFIX/bin/linux-phone
```

After this, start the Linux workspace with:

```bash
linux-phone
```

## 8. Operator Baseline Toolset

Inside Debian as the normal user, install a practical defensive baseline:

```bash
sudo apt update
sudo apt install -y \
  git curl wget nano vim tree unzip zip \
  python3 python3-pip python3-venv \
  dnsutils whois netcat-openbsd \
  openssh-client jq htop tmux
```

This baseline supports SSH, scripting, DNS lookups, basic troubleshooting, file handling, and repeatable terminal sessions.

## 9. Create a Status Script

Create a simple operator status script:

```bash
nano ~/greynoc-edc/scripts/status.sh
```

Paste:

```bash
#!/usr/bin/env bash

echo "=== GreyNOC EDC Status ==="
date
echo
echo "User: $(whoami)"
echo "Host: $(hostname)"
echo
echo "Python:"
python3 --version
echo
echo "SSH:"
ssh -V
echo
echo "Network:"
ip addr 2>/dev/null | head -40
```

Save, then make it executable:

```bash
chmod +x ~/greynoc-edc/scripts/status.sh
```

Run it:

```bash
~/greynoc-edc/scripts/status.sh
```

## 10. Optional Authorized Lab Tools

This section is only for authorized labs, home networks, classroom environments, capture-the-flag practice, and defensive learning. Install only what you understand and only use these tools where you have permission.

```bash
sudo apt update
sudo apt install nmap tcpdump tshark nikto gobuster sqlmap -y
```

### Important Limitation

Some tools may install but not work like they do on a laptop. Android, PRoot, and phone hardware can limit raw sockets, monitor mode, packet injection, kernel modules, background services, USB device access, and privileged network operations.

For wireless research, most phone Wi-Fi chipsets do not support monitor mode or packet injection in a standard Android setup. External adapters, root access, kernel support, and legal authorization are usually required. Those steps are outside the scope of this public no-root guide.

## 11. Daily Operator Use

| Action | Command or Step |
|---|---|
| Start Linux | Open Termux and run `linux-phone` |
| Exit Linux | Run `exit` |
| Update Debian | Run `sudo apt update && sudo apt upgrade -y` inside Debian |
| Install a package | Run `sudo apt install package-name -y` inside Debian |
| Check Python | Run `python3 --version` |
| Check SSH | Run `ssh -V` |
| Check IP addresses | Run `ip a` inside Debian or `ip addr` in Termux |
| Run status check | Run `~/greynoc-edc/scripts/status.sh` |

Quick reference session:

```bash
linux-phone
sudo apt update && sudo apt upgrade -y
python3 --version
ssh -V
~/greynoc-edc/scripts/status.sh
exit
```

## 12. Field Notes and Evidence Hygiene

Operators should write down what they do, when they do it, and why. Keep notes professional, factual, and permission-aware.

Recommended note format:

```text
Date:
Operator:
Authorized environment:
Purpose:
Commands or actions performed:
Observed result:
Follow-up needed:
Sensitive data captured? yes/no
Sanitized copy created? yes/no
```

Do not store credentials, private keys, client secrets, API tokens, production logs, or sensitive screenshots in plain text on the phone. If a file is intended for public sharing, make a sanitized copy, remove metadata where appropriate, and review visible clues before publishing.

## 13. What This Setup Is Good For

This setup is good for:

- SSH into authorized systems
- Linux command practice
- Python scripting
- DNS and whois lookups
- Documentation and field notes
- Small report drafts
- Home-lab troubleshooting
- SOC learning and defensive workflows
- Carrying offline checklists and runbooks

## 14. What This Setup Is Not

This setup is not:

- A replacement for a dedicated Linux laptop
- A full custom Android ROM
- A rooted phone environment
- A packet-injection platform
- A stealth or bypass toolkit
- A guarantee that every Linux package will work
- A safe place to store unencrypted client secrets

Full desktop environments may be slow, battery-heavy, and hard to use on a small screen. Systemd-based services may not behave the same way inside PRoot. Low-level network testing can be blocked by Android permissions, kernel limits, or hardware limits.

## 15. Troubleshooting

| Problem | Recommended Action |
|---|---|
| `pkg update` fails | Check Wi-Fi, rerun `pkg update`, then try `pkg upgrade -y` again |
| `proot-distro install debian` fails | Check storage and network stability; remove broken install with `proot-distro remove debian`, then reinstall |
| `sudo` does not work | Make sure you created a normal user and added it to the sudo group |
| `Permission denied` | Confirm whether the command belongs in Termux or Debian; use `sudo` only inside Debian after user setup |
| `Command not found` | Install the package or confirm you are in the right environment |
| Phone gets hot | Pause package installs, plug in the device, close other apps, and avoid long compile jobs |
| Storage fills up | Remove unused packages and old lab artifacts; move non-sensitive archives off-device |
| Tool works on laptop but not phone | Assume Android, PRoot, kernel, or hardware limits may be blocking the feature |

Helpful commands:

```bash
proot-distro list
proot-distro login debian
proot-distro remove debian
sudo apt update
sudo apt autoremove -y
```

## 16. Operator Safety Rules

1. Get permission before touching a network or system.
2. Keep client, employer, and production data off public posts.
3. Use synthetic examples for screenshots and demonstrations.
4. Do not store secrets in plain text.
5. Keep the phone updated.
6. Use screen lock and device encryption.
7. Separate personal, lab, and client materials.
8. Record what you did in clean field notes.
9. Delay or avoid posts from live work, travel, incidents, or sensitive locations.
10. When unsure, stop and ask for authorization.

## 17. Public Posting and Sharing Rules

Before posting screenshots, videos, guides, or operator notes, check for:

- IP addresses
- hostnames
- usernames
- emails
- tickets
- customer names
- credentials
- API keys
- browser tabs
- terminal paths
- Wi-Fi names
- location clues
- badge IDs
- license plates
- faces
- reflections
- document metadata

Always work on a copy. Sanitize the copy. Verify the copy. Publish only the public-safe copy.

## 18. Release Checklist for This Guide

Before publishing this guide externally:

| Check | Status |
|---|---|
| Internal-only references removed | Pending review |
| No exploit instructions included | Pending review |
| No customer environments included | Pending review |
| No credentials, keys, or live targets included | Pending review |
| Commands tested on a clean Android/Termux setup | Pending review |
| Safety and legal language reviewed | Pending review |
| Formatting, links, and accessibility checked | Pending review |
| Final PDF or web version archived | Pending review |

## 19. References for Operators

Use official project documentation when validating installation steps and package behavior:

- Termux project site
- Termux on F-Droid
- Termux PRoot documentation
- PRoot Distro project
- Debian package documentation
- Android device security settings
- GreyNOC public safety and operator guidance

## Legal and Safety Disclaimer

This document is for education, defensive operations, and authorized administration only. GreyNOC does not endorse unauthorized access, scanning, exploitation, credential attacks, interception, evasion, or bypassing access controls. Operators are responsible for following applicable laws, organizational policy, platform terms, and written authorization requirements.

