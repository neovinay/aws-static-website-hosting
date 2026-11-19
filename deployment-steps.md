# Deployment steps

This document records the deployment workflow used for the project. AWS console labels may change over time.

## 1. Domain and DNS

1. Register `vinaydevops.xyz` with a domain registrar.
2. Create a **public hosted zone** for `vinaydevops.xyz` in Amazon Route 53.
3. Copy the four Route 53 name servers from the hosted zone's NS record.
4. At the registrar, replace the domain's nameservers with those four Route 53 nameservers.
5. Allow DNS delegation to propagate.

## 2. Request the HTTPS certificate

1. Open AWS Certificate Manager in **US East (N. Virginia), `us-east-1`**.
2. Request a public certificate for:
   - `vinaydevops.xyz`
   - `www.vinaydevops.xyz`
3. Choose DNS validation.
4. Create the requested validation CNAME records in Route 53.
5. Wait until the certificate status is **Issued**.

## 3. Prepare the S3 bucket

1. Create the S3 bucket used for the website.
2. Enable bucket versioning.
3. Upload the website files (`index.html`, `css/`, `img/`, `js/`, and `slick/`) to the bucket root.
4. Configure the lifecycle rule used in this project to permanently delete noncurrent object versions after 30 days.
5. Keep the bucket private when using CloudFront OAC.

## 4. Create the CloudFront distribution

1. Create a CloudFront distribution with the S3 bucket as its origin.
2. Enable private S3 access using Origin Access Control (OAC).
3. Use the recommended origin and cache settings for the project.
4. Enable the WAF protections selected for this project.
5. Create the distribution and wait for deployment.
6. Record the CloudFront distribution domain name.

## 5. Add the custom domains and certificate

1. Edit the CloudFront distribution settings.
2. Add these alternate domain names:
   - `vinaydevops.xyz`
   - `www.vinaydevops.xyz`
3. Select the issued ACM certificate covering both names.
4. Set **Default root object** to `index.html`.
5. Save the changes and wait until the distribution finishes deploying.

## 6. Create Route 53 alias records

In the `vinaydevops.xyz` public hosted zone, create:

| Record name | Type | Alias target |
|---|---|---|
| (blank / apex) | A | Your CloudFront distribution |
| `www` | A | Your CloudFront distribution |

Use Simple routing and leave record protection disabled unless there is a specific reason to enable it.

## 7. Verify the deployment

Test these URLs in a browser:

- https://vinaydevops.xyz
- https://www.vinaydevops.xyz

Confirm that the website loads and the browser shows a secure HTTPS connection. If DNS or CloudFront changes have just been made, allow time for propagation and distribution deployment.

## 8. Evidence screenshots

Save screenshots in `screenshots/`, such as:

- `s3-website-files.png`
- `s3-versioning.png`
- `s3-lifecycle-rule.png`
- `cloudfront-distribution.png`
- `cloudfront-domains-certificate.png`
- `route53-alias-records.png`
- `website-apex-domain.png`
- `website-www-domain.png`

Before committing screenshots, hide AWS account IDs, billing details, email addresses, and any credentials or other private information.

## 9. Cost and cleanup

This project uses AWS services that may incur charges. Review current pricing and your Billing console, especially for AWS WAF, Route 53 hosted zones, CloudFront, and S3. When the assignment is complete, decide which resources to retain and delete any resources you no longer need. Deleting the hosting resources will make the AWS-hosted website unavailable, but it will not delete this GitHub repository.
