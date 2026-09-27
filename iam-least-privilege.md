 IAM Least Privilege Design

The DEvelopment team from Kijanikiosk want staff can only access the files relevant to them. Backend storage stays unreachable from the internet, even with valid credentials.

1. Staff role 

Staff assume `staff-file-access-role` via SSO (no shared passwords). Access is scoped automatically using the staff member's `department` session tag — one policy covers everyone, no per-person rules to maintain.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ReadOnlyOwnDepartment",
      "Effect": "Allow",
      "Action": ["s3:GetObject", "s3:ListBucket"],
      "Resource": "arn:aws:s3:::kijaniosk-reports/${aws:PrincipalTag/department}/*",
      "Condition": {
        "Bool": { "aws:MultiFactorAuthPresent": "true" }
      }
    },
    {
      "Sid": "DenyWriteDelete",
      "Effect": "Deny",
      "Action": ["s3:PutObject", "s3:DeleteObject"],
      "Resource": "arn:aws:s3:::kijaniosk-reports/*"
    }
  ]
}
```

- `${aws:PrincipalTag/department}` auto-scopes each staff session to their own folder only.
- Read-only — staff view reports, they don't write or delete them.
- MFA required — a password alone isn't enough.

2. Bucket policy — deny access outside the private network

Even a valid staff credential is refused if the request doesn't come through the internal VPC endpoint. This backs up the NACL/Security Group rules in `network-topology.png` with an independent check on the storage resource itself.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyOutsidePrivateEndpoint",
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:*",
      "Resource": ["arn:aws:s3:::kijaniosk-reports", "arn:aws:s3:::kijaniosk-reports/*"],
      "Condition": {
        "StringNotEquals": { "aws:sourceVpce": "vpce-0123456789abcdef0" }
      }
    }
  ]
}
```


Staff never reach storage directly — they go through the web portal, which calls storage through the private VPC endpoint. This IAM design still holds even if the portal's own authorization logic ever had a bug.
