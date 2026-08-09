# AWS S3 Versioning & CloudFront Static Website — Lab Report

*Cloud DevOps / AWS SRE Training*

| | |
|---|---|
| **Task** | S3 Bucket Versioning and Static Website Hosting with CloudFront Distribution |
| **Name** | Jesswin Anto J |

---

## Task 1: Enabling Bucket Versioning

**Step 1: Navigate to the folder**

Amazon S3 › Buckets › demo-hw-bucket-1-313828097658-ap-south-1-an › demo-hw-folder-1/

**Step 2: Enable versioning**

Amazon S3 › Buckets › demo-hw-bucket-1-313828097658-ap-south-1-an › Properties › Bucket Versioning › Edit

- **Bucket Versioning:** Enable

**Step 3: Upload a file from local storage (text, image, etc.)**

**Step 4: Edit the local file and upload it again to the same folder**

> **NOTE**
> With versioning enabled, re-uploading a file with the same name does not overwrite it — S3 keeps every version and assigns each a unique Version ID. This allows recovery of earlier file contents at any time.

*Fig 1.1 — Versioning enabled*
*Fig 1.2 — File uploaded*
*Fig 1.3 — File re-uploaded (edited)*
*Fig 1.4 — Show versions toggle*
*Fig 1.5 — Version list*
*Fig 1.6 — Multiple versions confirmed*

**Step 5: Toggle the "Show versions" switch near the search bar**

---

## Task 2: Static Website Hosting with CloudFront

> **NOTE**
> Overall flow: S3 bucket → enable static website hosting → upload index.html → create CloudFront distribution → validate in browser.

### 2.1 Create or reuse an S3 bucket

**Step 1: Use an existing bucket or create a new one**

Amazon S3 › Buckets › demo-hw-bucket-1-313828097658-ap-south-1-an

### 2.2 Enable static website hosting

**Step 2: Enable static website hosting**

Amazon S3 › Buckets › demo-hw-bucket-1-313828097658-ap-south-1-an › Properties › Static website hosting

- **Static website hosting:** Enable
- **Index document:** index.html
- Click Save

**Step 3: Upload the HTML file**

Amazon S3 › Buckets › demo-hw-bucket-1-313828097658-ap-south-1-an › Objects › Upload

**Step 4: Copy the website URL**

Amazon S3 › Buckets › demo-hw-bucket-1-313828097658-ap-south-1-an › Properties › Static website hosting

*Fig 2.1 — Enable static website hosting*
*Fig 2.2 — Index document set*
*Fig 2.3 — Settings saved*
*Fig 2.4 — Upload index.html*
*Fig 2.5 — Upload complete*
*Fig 2.6 — Website URL generated*
*Fig 2.7 — Bucket properties*
*Fig 2.8 — Website endpoint*

---

## Task 3: Creating a CloudFront Distribution

**Step 1: Open CloudFront › Distributions**

**Step 2: Click Create distribution**

### Step 1 — Get started
- **Distribution name:** demo-hw-1-static-webhosting
- Description: demo-hw-1-static-webhosting
- **Distribution type:** Single website or app
- Route 53 managed domain: Left blank

### Step 2 — Specify origin
- **Origin type:** Amazon S3
- S3 origin: demo-hw-bucket-1-313828097658-ap-south-1-an.s3.ap-south-1.amazonaws.com
- Origin path: Left blank
- **Allow private S3 bucket access to CloudFront:** Checked (Recommended)
- Origin settings: Use recommended origin settings
- Cache settings: Use recommended cache settings tailored to serving S3 content

### Step 3 — Enable security
- **Web Application Firewall (WAF):** Do not enable security protections

### Step 4 — Review and create
- Billing: Pay-as-you-go ($0/month)
- **Grant CloudFront access to origin:** Yes
- Enable Origin Shield: No
- Connection attempts: 3
- Connection timeout: 10
- Security protections: None
- Use monitor mode: No
- Use existing WAF configuration: No
- Click **Create distribution**

> **NOTE**
> "Allow private S3 bucket access to CloudFront" lets CloudFront use an Origin Access Control (OAC) to read the bucket while the bucket itself stays private — a more secure alternative to making the whole bucket public.
>
> A CloudFront distribution takes a few minutes to deploy fully before the website becomes reachable.

*Fig 3.1 — Get started*
*Fig 3.2 — Distribution name*
*Fig 3.3 — Specify origin*
*Fig 3.4 — Origin access setting*
*Fig 3.5 — Cache settings*
*Fig 3.6 — Enable security (WAF)*
*Fig 3.7 — Review settings*
*Fig 3.8 — Review settings (contd.)*
*Fig 3.9 — Distribution created*
*Fig 3.10 — Distribution deploying*

---

## Task 4: Browser Validation

Once the distribution is deployed, copy the CloudFront domain name and open it in a new browser tab, appending /index.html (or the uploaded file name), to confirm the site is running.

> **NOTE**
> Always wait for the distribution status to change from "Deploying" to "Enabled" before testing — an in-progress distribution may return an error even with a correct URL.

*Fig 4.1 — CloudFront domain URL*
*Fig 4.2 — Website loaded successfully via CloudFront*
