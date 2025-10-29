
---

## Project: AWS Cross-Account Static Website Migration (SilvanX.com)

This project demonstrates proficiency in essential cloud architecture, security, and automation disciplines crucial for an AWS Cloud Architect.

**Goal:** Migrate the live, static website (**silvanx.com**) from a development/demo AWS account to the dedicated production SilvanX AWS Account with near-zero downtime and adhering to security best practices.

---

### I. Architecture

* **AWS Services Used:** **S3**, **CloudFront**, **Route 53**, **ACM**, **IAM**.
* **Tools:** AWS CLI and Powershell.

---

### II. Cross-Account Migration Strategy

#### A. Data Migration & Security (AWS CLI)

1.  **Initial Content Sync:** AWS CLI (`aws s3 sync`) was used to transfer all static assets from the Demo Account's S3 bucket (`www.silvan.com`) to my local PC running on Windows 11, and then to the new AWS acct's SilvanX S3 bucket (`silvanx.com`)
2.  **ACL Conflict Resolution:** Encountered the AWS security constraint **"AccessControlListNotSupported"** because the new S3 bucket enforced **Bucket Owner Enforced** settings.
    * **Solution:** Removed the `--acl public-read` flag and implemented a **Bucket Policy** on the new S3 bucket to grant public `s3:GetObject` permission via **OAC/CloudFront**.

#### B. Certificate & DNS Handover (Zero Downtime Preparation)

1.  **ACM Certificate Validation:** A new certificate was requested in the SilvanX Account and validated by temporarily adding the ACM-provided **CNAME records** to the Demo Account's live **Route 53 Hosted Zone**.
    * This allowed the certificate to be **Issued** without impacting live traffic.
2.  **CloudFront CNAME Deconfliction:** We encountered the **CNAMEAlreadyExists** error when trying to attach the domain to the new CloudFront distribution, as the names were still claimed by the old distribution.
    * **Solution:** The CNAMEs (`silvanx.com`, `www.silvanx.com`) were removed from the Demo Account's CloudFront distribution. We waited for this configuration to deploy, which immediately freed the domain name for use in the new CloudFront distribution.

#### C. The Final Switch (Changing Domain Authority)

1.  **Route 53 Pre-config:** The new SilvanX Hosted Zone was configured with **A-Alias Records** pointing to the live, newly deployed SilvanX CloudFront endpoint.
2.  **Registrar Switch:** The final switch involved logging into the Domain Registrar and replacing the four old **Name Server (NS) entries** (pointing to the Demo Account) with the four new NS entries (pointing to the SilvanX Account). This change redirected all DNS lookups to the new SilvanX Hosted Zone.

---

### Key Skills Used

* **Networking & DNS:** DNS Management (Route 53), **NS Record Cutover (Minimal downtime migration)**, Apex Domain Routing (using Alias records), and Global DNS Propagation monitoring.
* **Cloud Architecture:** Designing and implementing a secure, scalable Static Site Hosting solution leveraging a **Content Delivery Network (CDN)** for low latency and high availability.
* **Security Best Practices:** Cross-Account Access Management, HTTPS/SSL Implementation (**ACM**), enforcing Object Ownership via S3 Bucket Policies (solving the ACL conflict), and utilizing **IAM OAC (Origin Access Control)**.
* **Automation & CLI:** **AWS CLI mastery** for file synchronization (`aws s3 sync`), troubleshooting, and environment control, demonstrating practical non-IaC automation.
* **Troubleshooting:** Diagnosing and resolving critical, real-world AWS errors, including `AccessControlListNotSupported` and `CNAMEAlreadyExists` across multiple AWS accounts.

---

### III. Outcome

The SilvanX Static Website Migration successfully moved production workloads across AWS accounts, achieving HTTPS enforcement and minimal user downtime by leveraging Caching and DNS sequencing best practices.

* **Security:** Eliminated direct S3 access by implementing **IAM OAC** and a strict S3 Bucket Policy.
* **Availability:** Established a high-availability architecture using the **Global CloudFront CDN**.
* **Downtime:** Total website downtime during the cutover phase was **under 5 minutes** (limited only by global DNS propagation time after the registrar switch).

In the future, I plan on cutting down errors and time by implementing an **IaC (Infrastructure as Code)** CloudFormation stack using **Terraform**.

---

=======
## Project Title: AWS Cross-Account Static Website Migration (SilvanX.com)

This project demonstrates proficiency in essential cloud architecture, security, and automation disciplines crucial for an AWS Cloud Architect.

Goal: Migrate the live, static website (silvanx.com) from a development/demo AWS account to the dedicated production SilvanX AWS Account with near-zero downtime and adhering to security best practices.

I. Architecture:
  AWS Services Used: S3, CloudFront, Route 53, ACM, IAM.
  Tools: AWS CLI and Powershell.

II. Cross-Account Migration Strategy:
  A. Data Migration & Security (AWS CLI)
    1. Initial Content Sync: AWS CLI (aws s3 sync) was used to transfer all static assets from the Demo Account's S3 bucket (www.silvan.com) to my local PC running on Windows 11, and then to the new AWS acct's SilvanX S3 bucket (silvanx.com).
    2. ACL Conflict Resolution: Encountered the AWS security constraint "AccessControlListNotSupported" because the new S3 bucket enforced Bucket Owner Enforced settings.
      - Solution: removed the --acl public-read flag and implemented a Bucket Policy on the new S3 bucket to grant public s3:GetObject permission via OAC/CloudFront.
  B. Certificate & DNS Handover (Zero Downtime Preparation):
    1. ACM Certificate Validation: A new certificate was requested in the SilvanX Account and validated by temporarily adding the ACM-provided CNAME records to the Demo Account's live Route 53 Hosted Zone.
      - This allowed the certificate to be Issued without impacting live traffic.
    2. CloudFront CNAME Deconfliction: We encountered the CNAMEAlreadyExists error when trying to attach the domain to the new CloudFront distribution, as the names were still claimed by the old distribution.
      - Solution: The CNAMEs (silvanx.com, www.silvanx.com) were removed from the Demo Account's CloudFront distribution. We waited for this configuration to deploy, which immediately freed the domain name for use in the new CloudFront distribution.
  C. The Final Switch (Changing Domain Authority):
    1. Route 53 Pre-config: The new SilvanX Hosted Zone was configured with A-Alias Records pointing to the live, newly deployed SilvanX CloudFront endpoint.
    2. Registrar Switch: The final switch involved logging into the Domain Registrar and replacing the four old Name Server entries (pointing to the Demo Account) with the four new NS entries (pointing to the SilvanX Account). This change redirected all DNS lookups to the new SilvanX Hosted Zone.

Key Skills Used:
  Networking & DNS: DNS Management (Route 53), NS Record Cutover (Minimal downtime migration), Apex Domain Routing (using Alias records), and Global DNS Propagation monitoring.
  Cloud Architecture: Designing and implementing a secure, scalable Static Site Hosting solution leveraging a Content Delivery Network (CDN) for low latency and high availability.
  Security Best Practices: Cross-Account Access Management, HTTPS/SSL Implementation (ACM), enforcing Object Ownership via S3 Bucket Policies (solving the ACL conflict), and utilizing IAM OAC (Origin Access Control).
  Automation & CLI: AWS CLI mastery for file synchronization (aws s3 sync), troubleshooting, and environment control, demonstrating practical non-IaC automation.
  Troubleshooting: Diagnosing and resolving critical, real-world AWS errors, including AccessControlListNotSupported and CNAMEAlreadyExists across multiple AWS accounts.

III. Outcome:
  The SilvanX Static Website Migration successfully moved production workloads across AWS accounts, achieving HTTPS enforcement and minimal user downtime by leveraging Caching and DNS sequencing best practices.
    - Security: Eliminated direct S3 access by implementing IAM OAC and a strict S3 Bucket Policy.
    - Availability: Established a high-availability architecture using the Global CloudFront CDN.
    - Downtime: Total website downtime during the cutover phase was under 5 minutes (limited only by global DNS propagation time after the registrar switch).

In the future I plan on cutting down errors and time by implementing an IaC cloudformation stack using terraform.

