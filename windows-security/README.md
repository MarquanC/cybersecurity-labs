# Active Directory Security Monitoring & Incident Investigation

## Overview

Performed hands-on security monitoring and incident investigation within a Windows Server 2022 Active Directory lab environment.

The project focused on analyzing Windows Security Event Logs to investigate authentication failures, account lockouts, and privileged group membership changes.

The lab used controlled test scenarios to generate security events and applied a SOC-style investigation process to determine the affected account, source system, event details, severity, and appropriate disposition.

---

## Lab Environment

| Component | Configuration |
|---|---|
| Operating System | Windows Server 2022 |
| Directory Service | Active Directory Domain Services |
| Domain | `corp.local` |
| Monitoring Platform | Windows Event Viewer |
| Log Source | Windows Security Event Log |
| Primary Investigation Host | Domain Controller |
| Virtualization | Oracle VirtualBox |

---

## Objectives

- Monitor Windows Security Event Logs
- Investigate failed authentication attempts
- Analyze account lockout events
- Investigate privileged group membership changes
- Identify affected accounts and systems
- Identify the account responsible for security-sensitive changes
- Assess event severity
- Determine whether activity is benign or suspicious
- Practice SOC-style incident triage
- Document investigation findings

---

# Investigation Methodology

The investigation process used throughout the lab was:

```text
Security Event
      ↓
Event Identification
      ↓
Event Detail Analysis
      ↓
Account / Source Identification
      ↓
Contextual Investigation
      ↓
Severity Assessment
      ↓
Disposition
      ↓
Documentation
```
The goal was to avoid treating every security event as malicious and instead evaluate the available evidence before determining an appropriate response.

Investigation 1 — Failed Authentication
Event ID 4625

A controlled failed authentication test was performed within the Active Directory environment.

An incorrect password was entered for the Administrator account to generate a Windows Security Event ID 4625.

Event Details
| Field       | Value             |
| ----------- | ----------------- |
| Event ID    | `4625`            |
| Target User | `Administrator`   |
| Domain      | `CORP`            |
| Logon Type  | `2`               |
| Workstation | `WIN-3AP34VCHQVR` |
| IP Address  | `127.0.0.1`       |
| Status      | `0xC000006D`      |
| SubStatus   | `0xC000006A`      |

Analysis

The event indicates an unsuccessful interactive logon attempt against the Administrator account.

The 0xC000006A substatus indicates that the authentication attempt failed because of an incorrect password.

The 127.0.0.1 address indicates that the event originated locally from the Domain Controller rather than from an external network address.

SOC Assessment

Severity: Low

Disposition: Benign / Controlled Lab Activity

The event was generated intentionally as part of the security monitoring exercise. The available evidence did not indicate an external attack.

Evidence

Investigation 2 — Account Lockout
Event ID 4740

A controlled account lockout was generated using the jsmith lab account.

Multiple incorrect authentication attempts caused the account to become locked according to the configured domain account lockout policy.

Event Details

| Field               | Value                        |
| ------------------- | ---------------------------- |
| Event ID            | `4740`                       |
| Target User         | `jsmith`                     |
| Target Domain Field | `DESKTOP-5FMA4E2`            |
| Caller Computer     | `WIN-3AP34VCHQVR.corp.local` |

Analysis

Event ID 4740 records that the jsmith account was locked out.

The CallerComputerName field identifies WIN-3AP34VCHQVR.corp.local as the computer associated with the lockout event.

The event was correlated with the controlled failed-authentication activity performed immediately before the account lockout.

SOC Assessment

Severity: Low

Disposition: Benign / Controlled Lab Activity

The lockout was intentionally generated for testing purposes and did not represent an actual account compromise.

Security Relevance

Account lockouts can be important security indicators because repeated authentication failures may result from:

Incorrect passwords
Stale credentials
Misconfigured applications
Automated processes
Password spraying
Brute-force activity
Potential credential compromise

A SOC analyst should investigate the surrounding authentication activity before determining whether a lockout represents a security incident.

Evidence

Investigation 3 — Privileged Group Membership Change
Event ID 4728

A controlled security group membership change was performed within Active Directory.

John Smith was temporarily added to the Workstation Admins security group to generate and investigate the corresponding Windows security event.

Event Details
| Field           | Value                |
| --------------- | -------------------- |
| Event ID        | `4728`               |
| Member Added    | `John Smith`         |
| Target Group    | `Workstation Admins` |
| Subject Account | `Administrator`      |
| Subject Domain  | `CORP`               |

Analysis

Event ID 4728 records the addition of a member to a security-enabled global group.

The event shows that the Administrator account performed the change and that John Smith was added to the Workstation Admins group.

Because membership in an administrative group can provide elevated privileges, this type of event should receive additional scrutiny in a production environment.

An analyst would want to determine:

Whether the change was authorized
Who initiated the change
Which account was modified
Which privileged group was affected
Whether the change was expected
Whether additional suspicious activity occurred around the same time
SOC Assessment

Severity: Medium

Disposition: Benign / Authorized Lab Activity

The change was intentionally performed as part of this security-monitoring exercise and was subsequently reverted.

Investigation Workflow
Privileged Group Change
        ↓
Event ID 4728
        ↓
Identify Modified Account
        ↓
Identify Target Group
        ↓
Identify Initiating Account
        ↓
Determine Authorization
        ↓
Document Finding
        ↓
Revert Unauthorized Test Change

Evidence

Security Event Comparison
| Event ID | Security Activity                | Investigation Focus              |
| -------- | -------------------------------- | -------------------------------- |
| `4625`   | Failed Logon                     | Authentication failure           |
| `4740`   | Account Lockout                  | Repeated authentication failures |
| `4728`   | Security Group Membership Change | Privileged access modification   |

These events demonstrate how different Windows Security Event IDs can provide complementary information during a security investigation.

SOC Triage Process

The investigations followed a consistent triage process.

1. Identify

Determine which security event occurred and identify the affected account or resource.

2. Investigate

Review event details including account information, logon type, source system, target group, and initiating account.

3. Correlate

Compare the event with surrounding activity and the known lab scenario.

4. Assess

Determine whether the activity appears benign, suspicious, or potentially malicious.

5. Respond

For controlled lab activity, restore the environment to its original state.

For a production incident, appropriate response actions would depend on the findings and organizational procedures.

6. Document

Record the event, investigation findings, severity, and final disposition.

Security Monitoring Concepts Demonstrated
Authentication Monitoring

Analyzed failed authentication activity using Windows Security Event ID 4625.

Account Lockout Investigation

Investigated Event ID 4740 to determine which account was locked and which computer was associated with the lockout.

Privileged Access Monitoring

Analyzed Event ID 4728 to identify a change to a security-sensitive administrative group.

Event Analysis

Reviewed Windows Security Event Log fields to determine the context and significance of individual events.

Incident Triage

Applied a structured process to determine whether observed activity represented a benign lab action or potentially suspicious behavior.

Evidence-Based Assessment

Used actual event data rather than assumptions to determine the severity and disposition of each investigation.

Lessons Learned
1. Failed Authentication Does Not Automatically Mean an Attack

A failed logon can result from something as simple as an incorrect password. Analysts should investigate the source, account, logon type, and surrounding activity before escalating.

2. Account Lockouts Require Context

An account lockout may be caused by legitimate user behavior, stale credentials, automated processes, or malicious authentication attempts.

3. Privileged Group Changes Are Security-Sensitive

Changes to administrative groups can directly affect an organization's security posture and should be monitored carefully.

4. Event Details Matter

Fields such as source computer, target account, logon type, status, and initiating account provide valuable context during investigations.

5. Security Monitoring Requires Validation

Generating a security event is only the beginning of an investigation. The event must be interpreted within the context of the environment and surrounding activity.

6. Controlled Testing Helps Validate Detection

Controlled security events can be used in a lab environment to confirm that logging and monitoring mechanisms capture the expected activity.

Skills Demonstrated
Active Directory
Active Directory Domain Services
Windows Server 2022
User and account management
Security group management
Administrative group monitoring
Domain security administration
Security Monitoring
Windows Event Viewer
Windows Security Event Logs
Event ID analysis
Authentication monitoring
Account lockout monitoring
Privileged access monitoring
Incident Investigation
SOC-style alert triage
Event investigation
Security event correlation
Severity assessment
Incident disposition
Evidence-based analysis
Security documentation
Security Concepts
Authentication security
Least privilege
Privileged access monitoring
Identity security
Security auditing
Incident response concepts
Tools
Windows Server 2022
Active Directory Users and Computers
Group Policy Management
Windows Event Viewer
Windows Security Event Logs
Command Prompt
gpresult
auditpol
Oracle VirtualBox
Evidence

The following screenshots document the three security investigations performed during the lab.

Failed Authentication — Event ID 4625

Account Lockout — Event ID 4740

Privileged Group Change — Event ID 4728

Conclusion

This project provided hands-on experience monitoring and investigating Windows security events within an Active Directory environment.

The lab demonstrated the relationship between:
Active Directory
        ↓
Windows Security Auditing
        ↓
Security Event Detection
        ↓
Event Investigation
        ↓
SOC Triage
        ↓
Severity Assessment
        ↓
Incident Disposition

The project demonstrates practical experience analyzing authentication failures, account lockouts, and privileged access changes using Windows Security Event Logs.

All investigations were performed using controlled lab activity and the environment was restored after testing.


### After pasting

**Preview it before committing**, just like we did with Entra ID.

You should see the **three actual screenshots** under the Evidence sections.

If they appear correctly, use this commit message:

```text
Add Active Directory security monitoring lab

Then you'll have three completed portfolio projects:

1. Active Directory Home Lab
2. Microsoft Entra ID Identity & Security Lab
3. Active Directory Security Monitoring & Incident Investigation

That's a much stronger portfolio progression because the projects now tell one connected story: identity management → cloud identity → detection & investigation.
