# AWS Hands-On Series – Complete IAM Demo (Single Account)

🎯 **Objective:**
In this single video and guide, we’ll cover complete IAM hands-on including:

* IAM users
* IAM groups
* IAM policies (managed and inline)
* IAM roles (attached to EC2)
* How permissions behave in each case
* End-to-end verification by logging in as IAM users

> 🔒 This demo is entirely within **a single AWS account** (no cross-account concepts).

---

## 🛠️ Step-by-Step Guide

### ✅ Step 1: Create IAM Group with Admin Access

1. Go to **IAM → User Groups → Create group**
2. Name the group: `AdminGroup`
3. Attach policy: `AdministratorAccess`
4. Click **Create group**

---

### ✅ Step 2: Create IAM User and Add to Group

1. Go to **IAM → Users → Create user**
2. Username: `devuser`
3. Select **Console access**

   * Set a password
4. Add user to **AdminGroup**
5. Click **Create user**

🔍 Now `devuser` inherits full admin permissions from the group.

🧪 **Test:**

* Login using `devuser` URL from the IAM dashboard.
* Create an S3 bucket, delete it, try launching EC2 → should work.

---

### ✅ Step 3: Test Behavior Without Permissions

1. Go to **IAM → Users → Create user**
2. Username: `readonlyuser`
3. Console access: enable
4. No group, no policy (user has no permissions)

🧪 **Test:**

* Login as `readonlyuser`
* Try listing S3 buckets or EC2 → will get **Access Denied** errors.

---

### ✅ Step 4: Attach Managed Policy to Individual User

1. Go to **IAM → Users → readonlyuser → Add permissions**
2. Attach AWS managed policy: `AmazonS3ReadOnlyAccess`

🧪 **Test:**

* Login again as `readonlyuser`
* Run `aws s3 ls` (or list buckets in UI) → should work
* Try deleting a bucket → should be denied

---

### ✅ Step 5: Create Custom Inline Policy (Least Privilege)

1. Go to **readonlyuser → Add inline policy**
2. Visual editor → S3 → Select **ListBucket** only
3. Choose resources: All or specific bucket
4. Save policy

🧪 **Test:**

* Try listing and accessing bucket content
* Confirm that only `List` is allowed

---

### ✅ Step 6: Create and Attach IAM Role to EC2

1. Go to **IAM → Roles → Create Role**
2. Trusted entity: AWS service → EC2
3. Attach policy: `AmazonS3ReadOnlyAccess`
4. Name: `EC2S3ReadRole`

➡️ Role is now ready to be assumed by EC2

---

### ✅ Step 7: Launch EC2 Instance with IAM Role

1. Go to **EC2 → Launch Instance**
2. AMI: Amazon Linux 2
3. Instance role: Select `EC2S3ReadRole`
4. Launch & connect via SSH

Run inside instance:

```bash
aws s3 ls
```

✅ Should be able to list S3 buckets

---

## 🧪 Permission Behavior Matrix

| Identity       | Policy Attached         | What Can They Do?          |
| -------------- | ----------------------- | -------------------------- |
| `devuser`      | Group: Admin            | All actions (Admin access) |
| `readonlyuser` | No policy               | Access denied everywhere   |
| `readonlyuser` | Managed: S3 ReadOnly    | Can list/view S3 only      |
| `readonlyuser` | Inline: ListBucket only | Can only list, not read    |
| EC2 Instance   | IAM Role: S3 ReadOnly   | Instance can read S3       |

---

## 🧼 Cleanup (Post-Demo)

* Terminate EC2 instance
* Delete IAM users: `devuser`, `readonlyuser`
* Delete IAM group: `AdminGroup`
* Delete IAM role: `EC2S3ReadRole`

---

## 🎯 Summary

In this demo, we covered:

* Creation of users, groups, and policies
* Different ways to assign permissions
* Real-time testing by logging in
* How IAM roles enable EC2 access to AWS services

📌 Ideal for learners who want to **see and understand IAM visually and practically** within a single AWS account.

---

👉 [Back to AWS Hands-On Series Home](../README.md)
