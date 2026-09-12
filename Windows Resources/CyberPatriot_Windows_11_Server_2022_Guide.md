
# CyberPatriot Windows 11 / Windows Server 2022 Guide

### Note that anything with a '*' next to it is specifically for Windows Server images, but could be applied to Windows 11 in certain circumstances

# 1. Quick Competition Workflow

## Recommended order

- [ ] Do all **forensics questions** before anything else; chances are, you will remove/change something crucial to the image if you do not do this first
- [ ] Start system updates
- [ ] Make sure everything in Windows Defender is enabled
- [ ] Read the README file on the desktop, noting required software and users/groups that need to be changed
- [ ] **Make sure you note down the administrator account's password that you are logged in as.** If you do not and restart the image, you will not be able to get back in
- [ ] Change password requirements and audit policies (using `secpol.msc`)
- [ ] Compare the user list and permissions in the README to what is actually on the system, changing permissions as needed (you can also create the possible new user/group at this time)
- [ ] Compare installed software to the listed software that is "supposed" to be installed on the system, removing anything that is not supposed to be there (you may have to use some other software for the forensics questions or find the location of some backdoor that was installed)
- [ ] Update all the software (either using Winget (Recommended), or manually installing)
- [ ] Check running services and possible backdoors using `services.msc` and task manager
- [ ] Make sure everything is done installing/updating, and restart **Before you do, make sure you have written down the password for your account**
- [ ] Remove/Disable obsolete 'Guest' account after restart
- [ ] ***Disable unnecessary services:**
  - IIS
  - Telnet
  - Web Services
  - FTP

---
> **Do not blindly disable, delete, or uninstall things.**
>
> In CyberPatriot images, legitimate services may look unusual. Always compare your actions against the README, scenario, user roles, and required functionality.

> **Immediately write down the account's password and the password that you are going to change all the other passwords to.**

---

# 2. Forensics Questions and README

## Forensics questions

### Steps for all questions

- [ ] Read the full question
- [ ] Note down suspicious filenames
- [ ] Record suspicious locations (to delete after you have gotten the points for the question)
- [ ] Record timestamps if relevant

### Types of questions (that I have seen) and how to answer them

- Encryption/Forensics/Decoding
    1. Identify the type (if you don't know, you can go to <https://www.dcode.fr/identification-chiffrement> and paste the code)
    2. Use the same website listed above to decode it, or use CyberChef (recommended if there are multiple steps to decryption)
- Wireshark/Network Logs
    1. Open the `.pcap` network log (usually just on the desktop) in Wireshark
    2. If the question is asking for a specific IP, you can filter by IP by: **Going to the top labels -> IP Section** (you can also do this for filtering by other tags like protocol)
    3. **Go to file -> Export Objects** to get leaked files from the network logs
- Prohibited Media
    1. Since this is usually just finding the file path of some prohibited file, you can use commands to search for specific file types:

    ```bash
        Get-ChildItem -Path "C:\YourDirectory" -Filter "*.txt" -Recurse -File   
    ```

    1. Sometimes, there could be something else you need to do with the file in the second question; for those, look to the other entries for the other most common types of questions
- Backdoor Program
    1. The question will reference an installed (and usually running) backdoor program
    2. To find it, it will usually be running inside of task manager
        - Right click on the program and then "Open File Location"
    3. After finding it, making sure you got the points for the question, you should remove it and stop the process

## README review

- [ ] Read the complete README
- [ ] Identify required software
- [ ] Identify prohibited media (usually any .mp3, mp4, .flac, or any other game executables)
- [ ] Identify required users (and their permissions)
- [ ] Write down the password of the administrator you are logged in as
- [ ] Identify web/database/file services
- [ ] Record special instructions (like new hires/groups)
- [ ] ***Identify required services**

---

# 3. Installed Programs

> Note: I recommend doing this so early because of the time it takes to update/install some programs. While stuff is installing in the background, you can work on other things.

You can automate this entire process with a couple commands

- Run this command to install *winget*:

```powershell
    Invoke-WebRequest
    \https://raw.githubusercontent.com/asheroto/winget-installer/master/winget-install.ps1
    \-UseBasicParsing | iex
```

> Note that it cannot be the usual install command for *winget* because CyberPatriot Windows 11 images just removed PowerShell script execution to prevent automation. This command is just a way to get around this, so you can do this with other `.ps1` scripts without triggering the error.

- Run the following commands to familiarize yourself with the installed programs and get used to the command line:

```powershell
    # See all programs
    winget list

    # See all programs with updates
    winget update

    # Uninstall a specific program (see the second column)
    winget uninstall <program Id>
    #Example: winget uninstall Insecure.Nmap
    
    # Install a specific program
    winget install <program name>
    # Example: winget install wireshark

    # Update a specific program
    winget update <program Id>
    #Example: winget update Notepad++.Notepad++

    # Update all programs with an update
    winget update --all
```

> Note that any standalone `*.exe` files won't be included or recognized just because they are not actually *installed* on the computer

## Audit installed applications

Look for:

- [ ] Unauthorized remote-access tools
- [ ] Suspicious utilities
- [ ] Pirated software
- [ ] Unnecessary games
- [ ] Unknown software
- [ ] Potentially unwanted programs
- [ ] Unexpected browsers or extensions

> There is a decently high chance that there could be a backdoor program or just some prohibited media in some random user's `Downloads` folder or elsewhere. To combat having to go through *EVERY* folder manually, you can just run this command to search for a file type in a folder recursively: `Get-ChildItem -Path "C:\TargetDirectory" -Filter "*<filetype>" -Recurse -File`

Useful locations:

- **Settings → Apps**
- **Control Panel → Programs and Features**

> **Only keep/update software explicitly stated in the README and used in the forensics questions so if Wireshark is used in one of the forensics questions, keep it, even though you could consider it a "hacking tool"**

---

# 4. Windows Security Configuration

> Start a Windows update around now too; the reason why you don't want to do it immediately is because it can cause other programs and just the overall VM to noticeably slow down.

## Windows Security

Review:

- [ ] Virus & threat protection
- [ ] Firewall & network protection
- [ ] App & browser control
- [ ] Device security
- [ ] Account protection

> Don't worry about the account portion

## User Account Control

- [ ] UAC is appropriately enabled
- [ ] Elevation settings are not unnecessarily weakened

---

# 5. Windows Firewall and Network Security

## Firewall

Check all applicable profiles:

- [ ] Domain
- [ ] Private
- [ ] Public

Review:

- [ ] Firewall enabled
- [ ] Inbound rules
- [ ] Outbound rules where required by the scenario
- [ ] Unnecessary exceptions
- [ ] Rules created by suspicious applications

Useful command:

```powershell
Get-NetFirewallProfile
```

---

# 6. Updates and Patch Management

## Windows Update

- [ ] Check update status
- [ ] Install appropriate security updates
- [ ] Restart when necessary
- [ ] Re-check update status afterward

> Updates can take time and may require restarts, so try to do other stuff before restarting so you can optimize the number of required restarts

---

# 7. User and Account Audit

## Review local users

Check:

- [ ] Unauthorized accounts
- [ ] Disabled accounts that should remain disabled
- [ ] Unexpected administrator accounts (or the reverse)
- [ ] Guest account status (it should usually be disabled if not addressed in the README)
- [ ] Default or legacy accounts
- [ ] Accounts with suspicious names
- [ ] Accounts inconsistent with the scenario

Useful locations:

- Control Panel
  - Lets you set passwords for users, but cannot change/create groups
- Computer Management
  - Go to **System Tools -> Local Users and Groups**
  - In the `Users` folder, you can disable the `Guest` account, set better passwords for admins, and disable users instead of deleting them to reduce risk
  - In the `Groups` folder, you can change group permissions, add users to groups, and create new groups

Useful commands:

```powershell
net localgroup Administrators
```

```powershell
Get-LocalGroupMember -Group "Administrators"
```

> - On domain-connected systems, be careful: domain groups may legitimately provide administrative access.
> - If you accidentally delete a user that was supposed to be there, you can get the points back by creating a new user with the same name

---

# 8. Password and Account Security

> - **Windows Key + R** (opens run dialogue)
> - Type in `secpol.msc`
> - Go to **Account Policies** to change password requirements or **Local Policies** to change user rights assignments

## Password policy

1. In `secpol.msc`, go to **Account Policies -> Password Policy**
2. Set the following:

| Setting | Value |
| --- | --- |
| Enforce password history | 5 passwords remembered |
| Maximum password age | 30 days |
| Minimum password age | 7 days |
| Minimum password length | 12 characters |
| Minimum password length audit | 5 characters |
| Password must meet complexity requirements | Enabled |
| Relax minimum password length limits | Disabled |
| Store passwords using reversible encryption | Disabled |

## Account lockout policy

1. In `secpol.msc`, go to **Account Policies -> Account Lockout Policy**
2. Set the **Account lockout threshold** (second row) to at least 5 invalid login attempts
3. Select the default settings for the others (it should show up after setting the second row)

## Set audit policy

1. In `secpol.msc`, go to **Local Policies -> Audit Policy**
2. Set all settings to be `Success, Failure`

## Account hygiene

- [ ] Disable unauthorized accounts instead of immediately deleting when evidence may matter
- [ ] Secure accounts that are authorized
- [ ] Review accounts with blank or weak passwords
- [ ] Check for unnecessary password exemptions

Useful policy tool:

```text
secpol.msc
```

---

# 9. Services

## General service audit

Review services for:

- [ ] Required services accidentally disabled
- [ ] Unnecessary services
- [ ] Suspicious services
- [ ] Services with unusual executable paths
- [ ] Services configured to start automatically without justification

Useful tools:

```text
services.msc
```

```powershell
Get-Service
```

> There are certain services that should always be disabled (WIP):
> - Simple TCP/IP Services
> - Access this computer from the network (Not Everyone)

---

# 10. Startup and Persistence Review

Review potential persistence locations:

- [ ] Startup applications
- [ ] Startup folders
- [ ] Scheduled tasks
- [ ] Services
- [ ] Registry Run locations
- [ ] Browser extensions
- [ ] Unknown login scripts

Useful tools:

```text
Task Manager → Startup apps
taskschd.msc
```

---

# 11. File System Review

Check common locations carefully:

- [ ] Desktop
- [ ] Downloads
- [ ] Documents
- [ ] Temp directories
- [ ] Public folders
- [ ] Startup folders
- [ ] User profiles
- [ ] ProgramData
- [ ] Unusual executable locations

Look for:

- [ ] Unexpected executables
- [ ] Suspicious scripts
- [ ] Unknown archives
- [ ] Files referenced by forensics questions
- [ ] Unusual recently modified files

> Preserve evidence needed for forensics questions before deleting or modifying files.

---

# 12. Sharing and Remote Access

## Shares

Review:

- [ ] Shared folders
- [ ] Administrative shares
- [ ] Permissions
- [ ] Unnecessary anonymous access

Useful command:

```powershell
net share
```

## Remote access

Review scenario requirements before changing:

- [ ] Remote Desktop
- [ ] Remote Assistance
- [ ] Remote management
- [ ] Third-party remote-access software

> A service being "remote" does not automatically mean it should be disabled. Windows Server images may intentionally require remote administration.

---

# 13. Windows Server 2022 Specific Section

> **Server images require additional caution.** A server often intentionally provides services that would be unnecessary on a normal Windows 11 workstation.

## Step 1: Identify server roles

Check:

```text
Server Manager → Manage → Remove Roles and Features
```

Document installed roles:

- [ ] Active Directory Domain Services
- [ ] DNS Server
- [ ] DHCP Server
- [ ] File and Storage Services
- [ ] Web Server (IIS)
- [ ] Remote Desktop Services
- [ ] Print and Document Services
- [ ] Other: __________________________

## Active Directory Domain Services

If AD DS is present:

- [ ] Do not casually remove the role
- [ ] Review privileged domain accounts
- [ ] Review domain administrator membership
- [ ] Check for unauthorized users where appropriate
- [ ] Review suspicious group memberships
- [ ] Preserve required domain functionality

Useful tools may include:

```text
dsa.msc
```

## DNS Server

If DNS is required:

- [ ] Confirm the DNS service is functioning
- [ ] Review suspicious zones or records when relevant
- [ ] Preserve required internal DNS configuration

## DHCP Server

If DHCP is required:

- [ ] Preserve legitimate scopes
- [ ] Review suspicious or unauthorized configuration
- [ ] Avoid breaking network functionality

## IIS / Web Server

If IIS is installed:

- [ ] Identify required websites
- [ ] Review bindings
- [ ] Review unnecessary sites
- [ ] Review authentication configuration
- [ ] Review unnecessary modules/features
- [ ] Preserve the required web application

Useful tool:

```text
inetmgr
```

## File Server

If the server provides file sharing:

- [ ] Review shares
- [ ] Review NTFS permissions
- [ ] Review share permissions
- [ ] Remove unnecessary access only when justified
- [ ] Preserve required users and services

## Disable SMB 1.x

```powershell
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol

# OR

Set-SmbServerConfiguration -EnableSMB1Protocol $false
```
---

# 14. Group Policy

## Local policy

Useful tool:

```text
gpedit.msc
```

Review relevant categories:

- [ ] Account policies
- [ ] Security options
- [ ] User rights assignments
- [ ] Audit policies
- [ ] Windows Defender policies
- [ ] Firewall policies

## Domain Group Policy

For Server 2022 systems participating in Active Directory:

- [ ] Determine whether settings are controlled by domain GPOs
- [ ] Avoid conflicting local changes without understanding their impact
- [ ] Identify whether a setting will be overwritten by Group Policy

Useful command:

```powershell
gpresult /r
```

---

# 15. Event Logs and Auditing

Useful tool:

```text
eventvwr.msc
```

Potential areas to review:

- [ ] Security logs
- [ ] System logs
- [ ] Application logs
- [ ] Suspicious service failures
- [ ] Unexpected login activity
- [ ] Evidence relevant to forensics questions

## Auditing

Review whether important auditing is appropriately configured according to the scenario and training objectives.

---

# 16. Common Useful Commands

## System information

```powershell
systeminfo
hostname
whoami
```

## Users

```powershell
net user
Get-LocalUser
```

## Groups

```powershell
net localgroup
net localgroup Administrators
```

## Network

```powershell
ipconfig /all
netstat -ano
# Or press Windows + R and type sysdm.cpl
```

## Firewall

```powershell
Get-NetFirewallProfile
```

## Services

```powershell
Get-Service
```

## Shares

```powershell
net share
# Or press Windows + R and type in fsmgmt.msc
```

## Group Policy

```powershell
gpresult /r
```

---

# 17. Final Review

## Accounts

- [ ] Authorized users preserved
- [ ] Unauthorized users addressed
- [ ] Administrator membership reviewed
- [ ] Guest and unnecessary accounts reviewed

## Security

- [ ] Firewall reviewed
- [ ] Security software reviewed
- [ ] Password/account policies reviewed
- [ ] Updates checked

## Software

- [ ] Suspicious programs investigated
- [ ] Required applications preserved
- [ ] Startup mechanisms reviewed

## Server-specific

- [ ] Required roles preserved
- [ ] Required services functioning
- [ ] Shares reviewed
- [ ] Domain functionality preserved
- [ ] IIS/DNS/DHCP reviewed if applicable

## Before finishing

- [ ] Review the scoring report
- [ ] Verify required functionality
- [ ] Review unanswered forensics questions
- [ ] Check for accidental changes that reduced security or broke services
- [ ] Read any competition messages
