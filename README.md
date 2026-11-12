# phishing-investigation-report
Malicious Resume Attachment

**Incident ID:** A-2703  
**Severity:** Medium  
**Status:** Closed — True Positive (malicious attachment confirmed)  
**Analyst:** Anastasiia Titkova  
**Date of incident:** 20 July 2022, 09:30  

## 1. Executive Summary

A phishing email, disguised as a job application, arrived in the HR department mailbox at hr@inergy.com. This email claimed that there was a password protected resume and that the person receiving the email should view the resume by entering a password. The file attached to this email was identified to be bfsvc.exe and this file pretended to be a windows service file. When analyzed on VirusTotal, there were 54 detections out of 71, suggesting a trojan infection.

## 2. Alert Details

| Field | Value |
|---|---|
| Ticket ID | A-2703 |
| Alert message | SERVER-MAIL Phishing attempt possible download of malware |
| Severity | Medium |
| Recipient | `hr@inergy.com` (`176.157.125.93`) |
| Sender (display) | Def Communications `<76tguyhh6tgftrt7tg.su>` |
| Sender IP | `114.114.114.114` |
| Subject | `Re: Infrastructure Egnieer role` |
| Attachment | `bfsvc.exe` (password-protected, password: `paradise10789`) |

## 3. Initial Triage. Red Flags

Before even opening the attachment, there were several red flags regarding the phishing attack:
- Mismatched sender: display name "Def Communications" with a .su (former Soviet Union) TLD – highly unusual for a real recruiting agency.
- Subject "Re:" without any previous thread: faked reply to create an impression of an ongoing dialogue.
- Spelling and grammatical mistakes: "Egnieer" (Engineer), "Ingergy" (Inergy), "for to express my interest" (typical of non-native mass phishing).
- An executable file disguised as a document: bfsvc.exe is pretending to be the Windows font cache service (bfsvc.exe).
- Password-protected archive: intended to evade email scanning and sandboxing.
- Urgency and social engineering: the password is provided in the email body to encourage immediate action.

## 4. Analysis

### 4.1 Attachment hash

Computed SHA-256 of `bfsvc.exe`:
54e6ea47eb04634d3e87fd7787e2136ccfbcc80ade34f246a12cf93bab527f6b


Submitted to VirusTotal: **54/71 engines flagged it as malicious** — a very high detection ratio that confirms the file is malicious beyond reasonable doubt.

**Classification:** Trojan — a program that disguises itself as legitimate software to trick the user into executing it.

### 4.2 Header and infrastructure indicators

- Sender domain `.su` (commonly abused TLD in phishing campaigns, cheap and weakly regulated.)
- Sender IP `114.114.114.114` (public DNS resolver, likely used as a relay or spoofed header.)
- Recipient IP `176.157.125.93` (corporate mail gateway.)

### 4.3 Attack chain (what would have happened if executed)

1. User opens the password-protected archive with the provided password.
2. `bfsvc.exe` is extracted and executed (masquerading as a system service).
3. Trojan establishes persistence and likely downloads additional payloads (stage-2).
4. Potential credential theft, lateral movement, and data exfiltration.

**MITRE ATT&CK mapping:**
- **T1566.001** — Phishing: Spearphishing Attachment
- **T1204.002** — User Execution: Malicious File
- **T1036.005** — Masquerading: Match Legitimate Name or Location
- **T1027** — Obfuscated Files or Information (password-protected archive)

---

## 5. Verdict

**True positive. Malicious phishing email with trojan delivery.**  
The attachment is proven to be malicious (54/71 VT detections), the sending infrastructure is suspicious, and the social engineering story (false job application) is an active phishing topic.

**Impact if executed:** high — trojan with potential for full host compromise.

---

## 6. Response and Recommendations

**Immediate actions:**
- Block the sender domain `76tguyhh6tgftrt7tg.su` and IP `114.114.114.114` at the mail gateway.
- Search mail logs for other recipients of the same campaign; quarantine matching messages.
- Block the file hash `54e6ea47...` across EDR and antivirus.
- Notify the HR team and confirm no one opened the attachment.

**Preventive controls:**
- Block executable and password-protected attachments at the email gateway.
- Enforce **SPF, DKIM, and DMARC** validation and quarantine on failure.
- Security awareness training, specifically for HR and recruiting teams targeted by fake-resume campaigns.
- Patch management and least-privilege access control.
- Enable **MFA** on all corporate accounts.
- Deploy EDR + antivirus with behavioral detection.
- Use sandboxing for all attachments before delivery.
- Implement application allowlisting on endpoints.

---

## 7. Lessons Learned

- Password Protected Archives are among the best ways to bypass the gateway – the gateway policy must identify them as a rule, not as an exception.
- Executables masquerading as system binary files (`bfsvc.exe`) still pose threats to untrained personnel; naming is no longer a sign of authenticity.
- The HR Department is a high-value target for attacks – training should be role specific.

---

## 8. Indicators of Compromise (IOC)

| Type | Value |
|---|---|
| SHA-256 | `54e6ea47eb04634d3e87fd7787e2136ccfbcc80ade34f246a12cf93bab527f6b` |
| Filename | `bfsvc.exe` |
| Sender domain | `76tguyhh6tgftrt7tg.su` |
| Sender IP | `114.114.114.114` |
| Recipient | `hr@inergy.com` |
| Archive password | `paradise10789` |
| Subject pattern | `Re: Infrastructure Egnieer role` |

---

## 9. Evidence

| VirusTotal — attachment hash |
|---|---|
<img width="2560" height="1438" alt="virus total" src="https://github.com/user-attachments/assets/c6951019-73fd-4f41-8ea2-13ee7ab2d15a" />
