# AWS S3 Bucket — Lab Report

*Cloud DevOps / AWS SRE Training*

| | |
|---|---|
| **Task** | Amazon S3 Bucket — creation, folder management, file upload, permissions, and cross-bucket copy |
| **Name** | Jesswin Anto J |


---

## Task 1: Creating an S3 Bucket

**Step 1: Navigate to the S3 service**

*Fig 1.1 — Amazon S3 console*

**Step 2: Start bucket creation**
- Click General purpose bucket
- Click Create bucket

*Fig 1.2 — Create bucket option*

**Step 3: Configure and create the bucket**

### General configuration
- **Bucket type:** General purpose
- **Bucket namespace:** Account Regional namespace
- **Bucket name prefix:** demo-hw-bucket-1
- Account Regional namespace suffix: -313828097658-ap-south-1-an
- Full bucket name: demo-hw-bucket-1-313828097658-ap-south-1-an
- Copy settings from existing bucket: Not used

### Object Ownership
- **Object Ownership:** ACLs enabled
- **Sub-setting:** Bucket owner preferred

### Block Public Access settings
- **Block all public access:** Not checked
- Block public access to buckets/objects granted through new ACLs: Not checked
- Block public access to buckets/objects granted through any ACLs: Not checked
- Block public access through new public bucket/access point policies: Not checked
- Block public and cross-account access through any public bucket/access point policies: Not checked
- Acknowledgement checked: "I acknowledge that the current settings might result in this bucket and the objects within becoming public."

> **NOTE**
> Disabling Block Public Access is only acceptable for a lab/demo bucket. In production, keep all four Block Public Access settings enabled unless the workload explicitly requires public objects.

### Bucket Versioning
- **Bucket Versioning:** Disabled

### Tags
- **Key:** Name **Value:** demo-hw-bucket-1

### Default encryption
- **Encryption type:** Server-side encryption with Amazon S3 managed keys (SSE-S3)
- **Bucket Key:** Enable

### Advanced settings
- **Object Lock:** Disable

**Action: Click Create**

*Fig 1.3, 1.4, 1.5, 1.6*
*Fig 1.7 — Bucket created successfully*

**Step 4: Create a second bucket by copying settings from the first**

### General configuration
- AWS Region: Asia Pacific (Mumbai) ap-south-1
- **Bucket namespace:** Global namespace
- **Bucket name:** demo-hw-bucket-2

### Copy settings from existing bucket
- Choose bucket dialog: Buckets (1/3)
- Selected: demo-hw-bucket-1-313828097658-ap-south-1-an (Asia Pacific (Mumbai) ap-south-1)
- Other options shown: s3-demo-bucket-1-313828097658-ap-south-1-an, s3-demo-bucket-2-313828097658-ap-south-1-an

> **NOTE**
> Using "Copy settings from existing bucket" saves time by reusing the encryption, versioning, and access configuration of demo-hw-bucket-1, keeping both buckets consistent.

*Fig 1.8 — Copy settings dialog*
*Fig 1.9 — Region and namespace configuration*
*Fig 1.10 — demo-hw-bucket-2 created*

---

## Task 2: Creating a Folder

**Step 1: Navigate to the bucket**

Amazon S3 › Buckets › demo-hw-bucket-1-313828097658-ap-south-1-an › Create folder

### Create folder
- **Folder name:** demo-hw-folder-1

### Server-side encryption
- **Server-side encryption:** Don't specify an encryption key

*Fig 2.1 — Create folder settings*
*Fig 2.2 — Folder configuration*
*Fig 2.3 — Folder created successfully*

---

## Task 3: Uploading a File into the Folder

Amazon S3 › Buckets › demo-hw-bucket-1-313828097658-ap-south-1-an › demo-hw-folder-1/ › Create file

### Upload workflow
- Upload
- Add file / folder
- Set permission (if public access is needed):
  - Access Control List (ACL) → Choose from predefined ACLs
  - Predefined ACLs → Grant public-read access

### Server-side encryption
- **SSE-S3 encryption:** Disabled

### Bucket Versioning
- **Versioning:** Disabled

### Permissions — Access Control List (ACL)
- **ACL:** Choose from predefined ACLs
- **Predefined ACLs:** Grant public-read access

> **NOTE**
> Granting public-read via ACL makes the object accessible to anyone with the URL. Use this only for non-sensitive demo files, and prefer bucket policies with least-privilege access for real workloads.

*Fig 3.1 — Upload configuration*
*Fig 3.2 — ACL settings*
*Fig 3.3 — Upload complete*

### Objects list — folder: demo-hw-folder-1/
- good morning.txt — Type: txt — Last modified: August 1, 2026, 22:56:25 (UTC+05:30) — Size: 12.0 B — Storage class: Standard
- hello world.txt (selected) — Type: txt — Last modified: August 1, 2026, 22:59:45 (UTC+05:30) — Size: 8.0 B — Storage class: Standard

### Actions menu (on hello world.txt)
- **Selected action:** Make public using ACL

### Make public page
- Specified object: hello world.txt — Type: txt — Last modified: August 1, 2026, 22:59:45 (UTC+05:30) — Size: 8.0 B

*Fig 3.4 — Objects list*
*Fig 3.5 — Make public workflow*
*Fig 3.6 — Object made public*

---

## Task 4: Copying / Moving a File Between Buckets

### Transfer details
- **Source bucket:** demo-hw-bucket-1-313828097658-ap-south-1-an
- **Source folder:** demo-hw-folder-1/
- **Object selected:** good morning.txt
- **Action chosen:** Copy / Move
- **Destination bucket:** demo-hw-bucket-2-313828097658-ap-south-1-an
- Destination prefix: Not specified
- Destination type: General purpose bucket
- **Additional copy settings:** Copy source settings
- Region: Asia Pacific (Mumbai) / ap-south-1
- **Copy result:** Successfully copied — 1 object, 12.0 B, 0 failed

*Fig 4.1 – 4.7*
*Fig 4.8 — Copy completed successfully*
