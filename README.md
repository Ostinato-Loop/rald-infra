# rald-infra

AWS infrastructure for the RALD OS ecosystem.

## Stacks

| Stack | Template | Purpose |
|---|---|---|
| `rald-ses-email` | `ses-email.yml` | SES domain identity for @rald.cloud, DKIM, SMTP credentials |
| `rald-assets-cdn` | `s3-cdn.yml` | S3 bucket + CloudFront CDN at cdn.rald.cloud |

## Deploy

Triggered automatically on push to `main`.
Requires org-level secrets: `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, `AWS_REGION`.

## Manual deploy

```bash
aws cloudformation deploy \
  --template-file cloudformation/ses-email.yml \
  --stack-name rald-ses-email \
  --capabilities CAPABILITY_IAM
```

## DNS records required after deploy (add in Cloudflare)

After deploy, get outputs:
```bash
aws cloudformation describe-stacks --stack-name rald-ses-email --query 'Stacks[0].Outputs'
aws cloudformation describe-stacks --stack-name rald-assets-cdn --query 'Stacks[0].Outputs'
```

Add:
- 3x DKIM CNAME records (from SES stack outputs)
- MX record: `mail.rald.cloud → feedback-smtp.<region>.amazonses.com`
- CNAME: `cdn.rald.cloud → <CloudFront domain from CDN stack>`
