# Static Website Host in S3 and cloudfront

A responsive static website for **CloudTech – AWS Cloud Solutions**, hosted on **Amazon S3** and delivered securely over HTTPS through **Amazon CloudFront**.

---

## About the Project

This project is a simple company landing page built with HTML and CSS. The website files are stored in a private S3 bucket. CloudFront serves them to visitors worldwide, and Origin Access Control (OAC) ensures the bucket can only be read through CloudFront.

## Website Sections

- **Home**: hero section with "Build. Deploy. Scale." and AWS cloud highlights
- **Services**: AWS Cloud, Deployment, Cloud Security, Monitoring
- **About**: company introduction
- **Contact**: contact form

## Features

- Dark theme with a blue accent
- Sticky navigation bar with smooth scrolling
- Fully responsive for desktop, tablet, and mobile
- Private S3 bucket (no public access)
- HTTPS through CloudFront

## Tech Stack

| Technology | Purpose |
|------------|---------|
| HTML5 | Page structure |
| CSS3 | Styling and responsive layout |
| Amazon S3 | File storage |
| Amazon CloudFront | Global content delivery and HTTPS |

## Project Structure

```
static-website-host-in-s3/
├── index.html    # Website page
├── styles.css    # Styling
└── README.md     # Documentation
```

## Architecture

```
User ──HTTPS──► CloudFront ──OAC──► S3 Bucket (private)
```

## Deployment Steps

### 1. Create the S3 bucket
- Open **Amazon S3** → **Create bucket**
- Enter a unique bucket name and choose a region
- Keep **Block all public access** turned **on**

### 2. Upload the files
Upload `index.html` and `styles.css` to the bucket, or run:

```bash
aws s3 sync . s3://YOUR_BUCKET_NAME --exclude ".git/*" --exclude "README.md"
```

### 3. Create the CloudFront distribution
- Open **CloudFront** → **Create distribution**
- **Origin domain**: select your S3 bucket
- **Origin access**: Origin access control settings (recommended) → create a new OAC
- **Viewer protocol policy**: Redirect HTTP to HTTPS
- **Default root object**: `index.html`

### 4. Add the bucket policy
In the bucket go to **Permissions** → **Bucket policy** and paste the following, replacing the placeholders:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowCloudFrontServicePrincipal",
      "Effect": "Allow",
      "Principal": {
        "Service": "cloudfront.amazonaws.com"
      },
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::YOUR_BUCKET_NAME/*",
      "Condition": {
        "StringEquals": {
          "AWS:SourceArn": "arn:aws:cloudfront::YOUR_ACCOUNT_ID:distribution/YOUR_DISTRIBUTION_ID"
        }
      }
    }
  ]
}
```

### 5. Open the website
Once the distribution is **Enabled**, open the CloudFront domain name:

```
https://xxxxxxxxxxxx.cloudfront.net
```

## Updating the Website

After changing a file, upload it and clear the CloudFront cache:

```bash
aws s3 sync . s3://YOUR_BUCKET_NAME --exclude ".git/*" --exclude "README.md"

aws cloudfront create-invalidation --distribution-id YOUR_DISTRIBUTION_ID --paths "/*"
```

## Cleanup

1. CloudFront: **Disable** the distribution, then **Delete** it
2. S3: **Empty** the bucket, then **Delete** it

## Notes

- S3 bucket names must be globally unique.
- The contact form is front-end only and does not send messages.
- Replace `YOUR_BUCKET_NAME`, `YOUR_ACCOUNT_ID`, and `YOUR_DISTRIBUTION_ID` with your own values.

## Author

**Subhajit Dey**
GitHub: [@subhajitajodhya-dotcom](https://github.com/subhajitajodhya-dotcom)
