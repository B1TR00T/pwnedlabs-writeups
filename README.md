# 🔐 PwnedLabs Writeups

> Cloud security attack lab writeups by **B1TR00T**  
> Documenting offensive AWS techniques, attack chains, and defensive lessons.

---

## 👤 About Me

Cybersecurity engineering student specializing in cloud offensive security.  
Currently building skills in AWS security, IAM exploitation, and cloud attack chains.

**Skills:** AWS CLI · Pacu · Burp Suite · Nmap · ffuf · John the Ripper  
**Focus:** Cloud security · Red teaming · AWS offensive techniques

---

## 📚 Completed Labs

| # | Lab | Difficulty | Topics | Status |
|---|---|---|---|---|
| 01 | [Execute and Identify Credential Abuse in AWS](./credential-abuse-aws/) | 🟢 Beginner | S3, IAM, DynamoDB, Password Spraying, Pacu | ✅ Done |
| 02 | [Assume Privileged Role with External ID](./assume-privileged-role-external-id/) | 🟡 Intermediate | nmap, ffuf, IAM, Secrets Manager, Role Assumption, External ID | ✅ Done |
| 03 | Uncover Secrets in CodeCommit and Docker | 🟡 Intermediate | Docker, CodeCommit, Git History, S3 | ✅ Done |
---

## 🔗 Attack Chains Covered
Lab 01 — Credential Abuse
Public S3 Bucket → Exposed Credentials → IAM Enumeration
→ DynamoDB Dump → Password Cracking → Console Access → Flag
Lab 02 — Privilege Escalation via External ID
IP Address → Port Scan → ffuf → config.json → AWS Credentials
→ Secrets Manager → Console Access → IAM Enumeration
→ External ID Discovery → Role Assumption → Flag

---

## 🛠️ Tools Used

| Tool | Purpose |
|---|---|
| AWS CLI | Interact with AWS services |
| Pacu | Cloud exploitation framework |
| ffuf | Web fuzzing and directory discovery |
| nmap | Network scanning and port discovery |
| John the Ripper | Password hash cracking |
| GoAWSConsoleSpray | AWS console password spraying |
| aws-enumerator | AWS permission enumeration |
| Burp Suite | Web application testing |

---

## 🧠 Key Concepts Learned

- **Credential exposure** via publicly accessible S3 buckets
- **IAM permission enumeration** using Pacu and aws-enumerator
- **Privilege escalation** via role assumption
- **External ID abuse** when trust policies are readable
- **Secrets Manager** exploitation for lateral movement
- **Password cracking** and spraying against AWS console
- **CloudShell** as a pivot point inside compromised accounts

---

## 🛡️ Defensive Lessons

| Attack | Defense |
|---|---|
| Public S3 bucket with credentials | Enable Block Public Access, never store secrets in files |
| Weak password hashes | Use bcrypt/argon2, enforce strong passwords |
| No MFA on IAM users | Enforce MFA for all IAM users |
| Discoverable External ID | Restrict iam:GetRole permissions |
| Excessive IAM permissions | Apply least privilege principle |
| Credentials in config files | Use IAM roles for EC2, AWS Secrets Manager |
| No threat detection | Enable GuardDuty, CloudTrail |

---

## 📈 Progress
Labs completed:    2
Techniques learned: 12+
Tools mastered:    7

---

## 📬 Connect

- GitHub: [B1TR00T](https://github.com/B1TR00T)
- Platform: [Intigriti](https://intigriti.com/profile/b1tr00t)

---

> ⚠️ All labs are performed in authorized environments for educational purposes only.
