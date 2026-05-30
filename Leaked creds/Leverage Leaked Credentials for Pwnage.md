# Leverage Leaked Credentials for Pwnage

## Difficulty
🟡 Intermediate

## Objective
Find leaked AWS credentials and database passwords in a
public GitHub repository, exploit password reuse to gain
AWS console access, and pivot to an RDS database via
Secrets Manager to retrieve the flag.

## Tools Used
- git
- trufflehog
- git-secrets
- AWS CLI
- MySQL client

---

## Step 1 — Clone the GitHub repository

```bash
git clone https://github.com/huge-logistics/aws-react-app
ls
```

![](./screenshots/02-git-clone.png)

Found a Laravel/React application with standard structure.

---

## Step 2 — Scan for secrets with trufflehog

```bash
trufflehog git https://github.com/huge-logistics/aws-react-app.git
```

![](./screenshots/03-trufflehog.png)

Trufflehog found nothing — missed the credentials.

---

## Step 3 — Scan with git-secrets

```bash
git secrets --install
git secrets --register-aws
git secrets --scan
```

![](./screenshots/04-git-secrets.png)

Found: `REACT_APP_AWS_ACCESS_KEY_ID=AKIAWHEOTHRFVXYV44WP`
in `.env` file at line 40.

---

## Step 4 — Inspect the .env file

![](./screenshots/05-env-file.png)

Found multiple secrets:
- AWS Access Key ID
- DB credentials: `jose:DevOps0001!`
- DB name: `aws_react_app`
- S3 bucket: `react-dev-aws`
- Region: `us-east-1`

---

## Step 5 — Verify AWS access key

```bash
aws sts get-access-key-info --access-key AKIAWHEOTHRFVXYV44WP
```

![](./screenshots/06-access-key-info.png)

Confirmed account ID: `427648302155`

---

## Step 6 — Password reuse attack

Attempted to login to AWS console as `jose` using
the DB password `DevOps0001!` — it worked.

![](./screenshots/07-console-access.png)

User `jose` reused their database password as their
AWS console password — a critical security mistake.

---

## Step 7 — Explore Secrets Manager

![](./screenshots/08-secrets-manager.png)

Found 4 secrets:
- `billing/hl-default-payment`
- `ext/cost-optimization`
- `employee-database` ← accessible
- `employee-database-admin`

---

## Step 8 — Extract database credentials

![](./screenshots/09-secret-value.png)

Retrieved from `employee-database` secret:
- Username: `reports_clone`
- Host: `employees.cwqkzlyzmm5z.us-east-1.rds.amazonaws.com`
- Port: `3306`
- Engine: `mariadb`

---

## Step 9 — Connect to RDS database

```bash
mysql -u reports_clone -p \
  -h employees.cwqkzlyzmm5z.us-east-1.rds.amazonaws.com \
  -P 3306
```

![](./screenshots/10-mysql-connect.png)

Successfully connected to the MariaDB RDS instance.

---

## Step 10 — Enumerate databases and tables

```sql
show databases;
use employees;
show tables;
```

![](./screenshots/11-show-databases.png)
![](./screenshots/12-show-tables.png)

Found 8 tables including a `flag` table.

---

## Step 11 — Retrieve the flag

```sql
select * from flag;
```

![](./screenshots/13-flag.png)

**Flag:** `d0e4b22365ad230c53c4ffc269dc0202`

---

## Attack Chain Summary

![[Pasted image 20260530135606.png]]
