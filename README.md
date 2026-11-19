# AWS Static Website Hosting

A static website hosted on **Amazon S3** and delivered through **Amazon CloudFront**, using a custom domain managed by **Amazon Route 53** and HTTPS provided by **AWS Certificate Manager (ACM)**.

## Live website

- https://vinaydevops.xyz        This doesn't work right now because i have deleted the source in AWS after finishing the project.
- https://www.vinaydevops.xyz

## GitHub repository

https://github.com/neovinay/aws-static-website-hosting

## Project overview

This project demonstrates how to publish a static HTML website on AWS. Website files are stored in an Amazon S3 bucket. CloudFront serves the content globally and uses Origin Access Control (OAC) to access the private S3 origin. Route 53 routes the custom domain names to CloudFront. ACM provides the TLS certificate used for HTTPS. AWS WAF protections were enabled on the CloudFront distribution.

## AWS services used

| Service | Purpose |
|---|---|
| Amazon S3 | Stores the static website files |
| Amazon CloudFront | Content delivery network and HTTPS entry point |
| Origin Access Control (OAC) | Allows CloudFront to access the private S3 origin |
| Amazon Route 53 | Public hosted zone and DNS alias records |
| AWS Certificate Manager (ACM) | Public TLS certificate for the apex and `www` domain |
| AWS WAF | Web application security protections on CloudFront |

## Architecture

See [architecture.md](architecture.md) for the architecture diagram and request flow.

## Repository structure

```text
aws-static-website-hosting/
├── website/                 # Website source files (HTML, CSS, JS, images)
├── screenshots/             # Evidence screenshots from the AWS console
├── README.md
├── architecture.md
└── deployment-steps.md
```

The original template's website files should be placed inside `website/`. Keep `index.html` and its asset folders together so relative links continue to work. If you deploy this folder structure to S3, upload the *contents* of `website/` to the bucket root unless you also change the CloudFront origin path.

## Deployment summary

1. Obtain a domain name from a domain registrar.
2. Create a public Route 53 hosted zone and configure the registrar to use its four assigned name servers.
3. Request an ACM public certificate in **US East (N. Virginia / us-east-1)** for `vinaydevops.xyz` and `www.vinaydevops.xyz`, then complete DNS validation.
4. Create an S3 bucket, enable versioning, and upload the website files.
5. Configure an S3 lifecycle rule to remove noncurrent object versions after 30 days.
6. Create a CloudFront distribution with the S3 bucket as its origin and OAC enabled.
7. Attach the ACM certificate and add both domain names as CloudFront alternate domain names.
8. Set CloudFront's default root object to `index.html`.
9. Create Route 53 A-alias records for the apex domain and `www` domain, both pointing to the CloudFront distribution.
10. Test both HTTPS URLs.

See [deployment-steps.md](deployment-steps.md) for more detail.

## Screenshots

Add your own screenshots from the AWS console and browser to the `screenshots/` folder. Suggested evidence:

- S3 bucket showing the uploaded website files
- S3 versioning configuration
- S3 lifecycle rule
- CloudFront distribution and origin configuration
- CloudFront alternate domain names and ACM certificate
- Route 53 hosted zone showing both A-alias records
- Website loaded at `https://vinaydevops.xyz`
- Website loaded at `https://www.vinaydevops.xyz`

Do not include screenshots containing account numbers, billing/payment details, access keys, secret keys, passwords, or other private information. Redact sensitive details before committing screenshots.

## Security and cost notes

- Never commit AWS access keys, secret keys, passwords, private certificates, or `.pem` files.
- This repository contains the website source and project documentation; it does **not** contain your AWS infrastructure or deploy AWS resources by itself.
- AWS resources can continue to incur charges while they exist or are in use. In particular, review WAF, Route 53 hosted-zone, CloudFront, and S3 charges in the AWS Billing console before leaving resources running.
- Deleting the AWS hosting resources will stop this AWS-hosted website from being served, even though the GitHub repository remains available.
- The domain registration is separate from GitHub and AWS hosting.

## Credits

The website design is based on the Tooplate template used for this project. Retain the original template attribution and license/terms as applicable. See `ABOUT THIS TEMPLATE.txt` if included with the original template.
