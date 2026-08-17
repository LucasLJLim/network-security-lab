# Day 15 – Group Policy, Password Enforcement and Security Auditing

## Objective

Day 14 built the structure of the domain: organisational units, users and security groups. Nothing in it was enforced or watched. This session adds both.

The goals were to:

- Enforce a domain-wide password policy through Group Policy
- Configure account lockout thresholds and durations
- Create a separate GPO for advanced audit logging on the domain controller
- Verify that both policies applied using command line tools
- Deliberately trigger an account lockout and trace it through the Windows Security log
- Identify the specific event IDs a SOC analyst would use to investigate a brute-force attempt

---

## Environment

**Server VM**
- Windows Server 2025 Datacenter: Azure Edition
- VM name: `DC01`
- Administrator username: `<redacted>`
- Roles: Active Directory Domain Services, DNS

**Domain**
- Forest root domain: `lucaslab.test`
- NetBIOS name: `LUCASLAB`
- Functional level: Windows Server 2025

**Existing objects used from Day 14**
- User: `jsmith` (John Smith, IT OU)
- Group: `GG_IT_Staff`

**Client**
- MacBook (Apple Silicon)
- Microsoft Remote Desktop

**Tools used**
- Group Policy Management Console (GPMC)
- Group Policy Management Editor
- Active Directory Users and Computers (ADUC)
- Event Viewer
- PowerShell (`gpupdate`, `gpresult`, `net accounts`, `auditpol`, `Get-ADUser`, `Get-WinEvent`)

Public IP address and administrator username are redacted in this write-up. Publishing the address and account name of an internet-facing domain controller narrows what an attacker needs to guess.

---

## Part 1 – Enforce a domain password policy

Password and account lockout settings for domain accounts only take effect when the GPO is linked at the **domain root**. A GPO linked to a child OU will apply its other settings normally but will silently ignore password policy for domain users. This is a common source of confusion and is the reason the built-in Default Domain Policy is edited here rather than a new GPO.

Opened Server Manager → Tools → Group Policy Management, confirming the forest, domain, the built-in Domain Controllers OU and the `LucasLab` OU structure created on Day 14 *(Day15_01)*.

Edited the Default Domain Policy at:

```
Computer Configuration → Policies → Windows Settings →
Security Settings → Account Policies → Password Policy
```

| Setting | Value |
|---------|-------|
| Enforce password history | 24 passwords remembered |
| Maximum password age | 90 days |
| Minimum password age | 1 day |
| Minimum password length | 14 characters |
| Password must meet complexity requirements | Enabled |
| Store passwords using reversible encryption | Disabled |

**Why 14 characters:** current ACSC and NIST guidance both favour length over enforced complexity and frequent rotation. Long passphrases resist offline cracking far better than short strings padded with symbols, and they are less likely to be written down.

*Day15_02*

---

## Part 2 – Configure account lockout

Same GPO, under Account Policies → Account Lockout Policy.

| Setting | Value |
|---------|-------|
| Account lockout threshold | 5 invalid logon attempts |
| Account lockout duration | 15 minutes |
| Reset account lockout counter after | 15 minutes |
| Allow Administrator account lockout | Disabled |

The final setting exists on Windows Server 2022 and later. It is left disabled here so that a failed test cannot lock the only administrative account out of the domain controller, which in an Azure-hosted lab would mean losing RDP access entirely.

**The trade-off:** lockout defends against online password guessing but creates a denial-of-service risk. An attacker who knows valid usernames can lock out staff deliberately. A 15 minute automatic duration is the usual compromise, since it slows guessing to a crawl without requiring a help desk call.

*Day15_03*

---

## Part 3 – Create an audit policy GPO

Rather than adding audit settings to the Default Domain Policy, a separate GPO was created and linked to the **Domain Controllers** OU. Keeping custom settings out of the built-in policies makes them easier to identify, disable and troubleshoot later.

GPO name: `LucasLab - DC Audit Policy`

The Scope tab confirms the link: location Domain Controllers, link enabled Yes, enforced No, path `lucaslab.test/Domain Controllers`, with security filtering set to Authenticated Users *(Day15_04)*.

Configured at:

```
Computer Configuration → Policies → Windows Settings →
Security Settings → Advanced Audit Policy Configuration → Audit Policies
```

Four of the ten categories were configured, leaving the rest untouched to keep log volume manageable *(Day15_05)*.

| Category | Subcategory | Setting | Screenshot |
|----------|-------------|---------|------------|
| Account Logon | Audit Credential Validation | Success and Failure | Day15_06 |
| Account Logon | Audit Kerberos Authentication Service | Success and Failure | Day15_06 |
| Account Management | Audit User Account Management | Success and Failure | Day15_07 |
| Account Management | Audit Security Group Management | Success | Day15_07 |
| Logon/Logoff | Audit Logon | Success and Failure | Day15_08 |
| Logon/Logoff | Audit Account Lockout | Success and Failure | Day15_08 |
| Policy Change | Audit Audit Policy Change | Success | Day15_09 |

**Advanced versus basic audit policy:** the older Audit Policy node offers nine broad categories. Advanced Audit Policy Configuration splits those into around sixty subcategories, so specific activity can be logged without generating unusable volumes of noise. The two should not be mixed, as conflicting settings produce unpredictable results.

The editor also warns that Advanced Audit Policy is ignored unless **Audit: Force audit policy subcategory settings (Windows Vista or later) to override audit policy category settings** is enabled under Local Policies → Security Options. Without it, every subcategory above would configure cleanly and then produce no events.

---

## Part 4 – Apply and verify

```powershell
gpupdate /force
```

Both computer and user policy reported success *(Day15_10)*.

```powershell
gpresult /r /scope:computer
```

The RSOP header confirms DC01 as a Primary Domain Controller running OS version 10.0.26100 *(Day15_11)*. Under Computer Settings, the distinguished name `CN=DC01,OU=Domain Controllers,DC=lucaslab,DC=test` confirms the DC sits in the OU the audit GPO is linked to, and Applied Group Policy Objects lists all three policies *(Day15_12)*:

- Default Domain Controllers Policy
- LucasLab - DC Audit Policy
- Default Domain Policy

The security group membership section confirms DC01 is a member of Domain Controllers and Enterprise Domain Controllers, which is what makes the OU link effective *(Day15_13)*.

Confirmed the effective password and lockout policy:

```powershell
net accounts
```

Minimum password length 14, password history 24, maximum age 90 days, lockout threshold 5, lockout duration 15 minutes, observation window 15 minutes *(Day15_14)*. These match what was configured in Parts 1 and 2.

Confirmed audit subcategories were active:

```powershell
auditpol /get /category:"Account Logon","Logon/Logoff","Account Management"
```

Logon, Account Lockout, Credential Validation, Kerberos Authentication Service and User Account Management all returned Success and Failure; Security Group Management returned Success; everything else returned No Auditing *(Day15_15)*.

Verifying through three separate tools matters here. The GPO editor shows what was *configured*. `gpresult`, `net accounts` and `auditpol` show what is actually *in effect*, which is not always the same thing once inheritance, link order and precedence are involved.

**Observations:**

Everything applied on the first `gpupdate /force`, with no reboot required and no settings needing to be reopened. That is partly because DC01 is both the domain controller and the only machine in scope, so there is no replication delay between where the GPO is written and where it takes effect. In a multi-DC environment the same change would need to replicate first, and `gpresult` could legitimately show the old policy for a while.

---

## Part 5 – Simulate a brute-force lockout

With enforcement and logging both in place, the policy was tested against the `jsmith` account created on Day 14.

```powershell
1..6 | ForEach-Object {
    net use \\DC01\IPC$ /user:lucaslab\jsmith "WrongPassword$_" 2>$null
    Start-Sleep -Seconds 1
}
```

The lockout threshold is 5, so the sixth attempt confirms the account is already locked rather than triggering the lock itself *(Day15_16)*.

Confirmed the lockout state:

```powershell
Get-ADUser jsmith -Properties LockedOut,BadLogonCount |
    Select-Object Name,LockedOut,BadLogonCount
```

Returned `LockedOut: True` with `BadLogonCount: 5` *(Day15_17)*.

---

## Part 6 – Trace the attack through the Security log

```powershell
Get-WinEvent -FilterHashtable @{LogName='Security'; ID=4625,4740,4771,4776} -MaxEvents 20 |
    Select-Object TimeCreated, Id, @{n='Account';e={$_.Properties[0].Value}} |
    Format-Table -AutoSize
```

The output shows six `4771` events for `jsmith` between 12:21:24 and 12:21:30, with a `4740` at 12:21:29 *(Day15_18)*.

| Event ID | Meaning | Why it matters |
|----------|---------|----------------|
| 4625 | An account failed to log on | The core failed-logon event; volume and pattern distinguish a typo from an attack |
| 4771 | Kerberos pre-authentication failed | Wrong password presented to AD over Kerberos |
| 4776 | NTLM credential validation failed | Same failure over the older NTLM protocol |
| 4740 | A user account was locked out | Confirms the threshold was reached |
| 4767 | A user account was unlocked | Should be rare; frequent unlocks can indicate an attacker maintaining access |

### Event 4740 – the lockout

Filtering the Security log to event ID 4740 returned a single result, task category User Account Management, logged 12:21:29 *(Day15_19)*. The detail pane shows:

- **Subject** Security ID `SYSTEM`, Account Name `DC01$` — the lockout was performed by the machine itself, not a person
- **Account That Was Locked Out** `LUCASLAB\jsmith`
- **Caller Computer Name** `DC01`

### Events 4771 – the failed attempts

Filtering to 4771 returned six results, all keyworded Audit Failure, task category Kerberos Authentication Service *(Day15_20)*. The detail pane shows account `LUCASLAB\jsmith`, service name `krbtgt/lucaslab`, client address `::1`, and failure code `0x12`. Scrolling further shows the certificate information fields are empty, since no certificate was used for pre-authentication, and a note that pre-authentication types, ticket options and failure codes are defined in RFC 4120 *(Day15_21)*.

**Detection logic this supports:** a practical rule is multiple 4625 or 4771 events for the same account within a short window, followed by a 4740. A single 4625 is almost always a typo. Six in seven seconds from one source is not. This is the exact pattern the SIEM stage of this lab will alert on.

---

## Part 7 – Unlock the account and confirm the audit trail

The account was unlocked through ADUC → John Smith → Properties → Account tab → **Unlock account**, which is only offered when the account is genuinely locked *(Day15_22)*. The IT OU then showed John Smith restored to a normal user object *(Day15_23)*.

Filtering the Security log to event ID 4767 returned one result, logged 12:37:43 *(Day15_24)*. The detail pane shows:

- **Subject** Account Name `<redacted>`, domain `LUCASLAB` — the administrator who performed the unlock
- **Target Account** `LUCASLAB\jsmith`

This is the contrast worth noting. In the 4740, the Subject was `SYSTEM`, because Windows locked the account automatically when the threshold was reached. In the 4767, the Subject is a named administrator, because a person chose to unlock it. Windows records not just what happened but who caused it, and distinguishing machine-initiated from human-initiated events is central to incident investigation.

Final verification:

```powershell
Get-ADUser jsmith -Properties LockedOut | Select-Object Name,LockedOut
```

Returned `LockedOut: False` *(Day15_25)*.

---

## Key Concepts Learned

- Group Policy Objects apply settings based on where they are linked in the domain hierarchy
- Password and lockout policies for domain accounts must be linked at the domain root to take effect
- Advanced Audit Policy Configuration provides subcategory-level control and depends on the Force audit policy subcategory setting to work at all
- Configured settings and effective settings are different things, and effective settings must be verified separately
- Account lockout is both a defence against password guessing and a potential denial-of-service vector
- Windows Security event IDs 4625, 4740, 4767, 4771 and 4776 form the core of failed-authentication monitoring
- The Subject field distinguishes machine-initiated events from human-initiated ones

---

## Skills Practised

- Creating, linking and editing Group Policy Objects
- Configuring domain password and account lockout policy
- Configuring advanced audit policy on a domain controller
- Verifying policy application with `gpresult`, `net accounts` and `auditpol`
- Simulating a brute-force authentication attempt in a controlled environment
- Querying and filtering the Windows Security log with `Get-WinEvent` and Event Viewer filters
- Interpreting Windows authentication event IDs and their Subject and Target fields
- Unlocking a locked domain account through ADUC

---

## Reflection

Two things stood out.

The failed attempts logged as `4771` rather than `4625`. Running `net use` against the domain controller authenticated over Kerberos, so the failure was recorded by the Kerberos Authentication Service rather than as a generic logon failure. A detection rule watching only for 4625 would have missed this entirely, which is a good argument for covering both.

The client address in every `4771` was `::1`, the IPv6 loopback, because the attempts originated on DC01 itself. In a real brute-force that field would hold an external source address, and it is exactly what an analyst would pivot on to identify the attacking host. The simulation reproduces the authentication pattern faithfully but not the network path.

The Group Policy hierarchy took the longest to get comfortable with. The distinction between the Group Policy Management Console and the Group Policy Management Editor is not obvious from the interface, and I lost time in the editor looking for a way to create a new GPO when creating and linking only happens in the console. The link position also matters more than I expected: password policy has to sit at the domain root, while the audit policy belongs on the Domain Controllers OU, and getting that wrong produces settings that save correctly and then do nothing.

The PowerShell was the other slow part. Commands like the `Get-WinEvent` filter hashtable and the calculated property that pulls the account name out of `$_.Properties[0].Value` were not something I could have written from scratch at the start of this session. Working through what each segment does, rather than pasting and moving on, was worth the extra time.

Active Directory itself is still the largest thing I am learning in this lab. Each session surfaces mechanisms I did not know existed, this time Kerberos pre-authentication, the Force audit policy subcategory override, and fine-grained password policies. The pattern I have settled on is to configure something, verify it through a second tool, then deliberately break it and watch what the logs say.

---

## Outcome

DC01 now enforces a password and lockout policy across the domain and writes detailed authentication events to the Security log. A simulated brute-force attempt was executed against a domain user, the account locked as configured, and the full sequence was traced through Windows Security events from first failure to lockout to unlock, with the administrator who performed the unlock recorded by name.

This closes the gap left at the end of Day 14, where the domain had structure but no enforcement and no visibility. It also produces the log data that the SIEM stage of this lab depends on: a SIEM is only as useful as the events its sources are configured to generate.

One figure worth recording: the Security log passed 14,600 events on a single-purpose domain controller with no interactive users, within days of auditing being enabled. Manual review does not scale past this point, which is the practical case for centralised log aggregation.

**Next:** install Sysmon on DC01 for detailed process and network telemetry, then forward Windows Security and Sysmon logs to a central SIEM for dashboarding and alerting.

---

## Screenshots

| File | Description |
|------|-------------|
| `Day15_01_Group_Policy_Management_Console.png` | GPMC showing the forest, domain, Domain Controllers OU and LucasLab OU |
| `Day15_02_Password_Policy_Configured.png` | Password Policy settings applied in the Default Domain Policy |
| `Day15_03_Account_Lockout_Policy.png` | Account Lockout Policy settings, including Administrator lockout disabled |
| `Day15_04_Audit_GPO_Linked_To_DC_OU.png` | LucasLab - DC Audit Policy Scope tab confirming the link to Domain Controllers |
| `Day15_05_Audit_Categories_Summary.png` | Advanced Audit Policy summary showing four of ten categories configured |
| `Day15_06_Audit_Account_Logon.png` | Account Logon subcategories: Credential Validation and Kerberos Authentication Service |
| `Day15_07_Audit_Account_Management.png` | Account Management subcategories: User Account and Security Group Management |
| `Day15_08_Audit_Logon_Logoff.png` | Logon/Logoff subcategories: Logon and Account Lockout |
| `Day15_09_Audit_Policy_Change.png` | Policy Change subcategory: Audit Policy Change |
| `Day15_10_gpupdate_Force.png` | `gpupdate /force` completing for both computer and user policy |
| `Day15_11_gpresult_RSOP_Header.png` | `gpresult /r` RSOP header identifying DC01 as Primary Domain Controller |
| `Day15_12_gpresult_Applied_GPOs.png` | Applied Group Policy Objects listing all three policies |
| `Day15_13_gpresult_Security_Groups.png` | DC01 security group membership including Domain Controllers |
| `Day15_14_net_accounts_Output.png` | `net accounts` confirming effective password and lockout policy |
| `Day15_15_auditpol_Output.png` | `auditpol /get` confirming which subcategories are auditing |
| `Day15_16_Failed_Logon_Attempts.png` | PowerShell loop generating six failed authentication attempts |
| `Day15_17_Account_Locked_Out.png` | `Get-ADUser` showing LockedOut True and BadLogonCount 5 |
| `Day15_18_Security_Events_PowerShell.png` | `Get-WinEvent` output showing the 4771 sequence and the 4740 |
| `Day15_19_Event_4740_Lockout.png` | Event Viewer 4740 with Subject SYSTEM and locked account jsmith |
| `Day15_20_Event_4771_Kerberos_Failures.png` | Event Viewer showing all six 4771 Audit Failure events |
| `Day15_21_Event_4771_Detail_Continued.png` | 4771 detail continued: empty certificate fields and RFC 4120 reference |
| `Day15_22_ADUC_Unlock_Account.png` | ADUC Account tab with Unlock account selected for John Smith |
| `Day15_23_ADUC_Account_Restored.png` | IT OU showing John Smith restored after unlock |
| `Day15_24_Event_4767_Unlocked.png` | Event Viewer 4767 showing the administrator who unlocked jsmith |
| `Day15_25_LockedOut_False_Verified.png` | `Get-ADUser` confirming LockedOut has returned to False |