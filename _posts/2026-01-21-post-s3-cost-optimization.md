---
layout: post
title: "Amazon S3 Cost Optimization: Key Takeaways from AWS Summit Japan 2025 (AWS-05)"
date: 2026-01-21
categories: [aws, s3, cost-optimization]
tags: [AWS, Amazon S3, FinOps, Cloud Architecture]
---

# Amazon S3 Cost Optimization  
## Key Takeaways from AWS Summit Japan 2025 (AWS-05)

---

## Why Cloud Cost Optimization Is No Longer Optional

Many organizations adopt cloud platforms such as AWS or Azure smoothly and quickly. However, as usage grows, it is common to hear concerns that **cloud costs have become harder to control than expected**.

Going forward, simply stating “I can use the cloud” is no longer sufficient. For cloud engineers and architects, **cost optimization must be treated as a core, baseline skill**, alongside availability, security, and scalability.

---

## The Hidden Cost After Compute: Storage

When discussing cloud costs, most attention naturally goes to compute services such as **Amazon EC2** or **Amazon RDS**. These services are often the most visible cost drivers, and AWS provides well-known optimization mechanisms such as **Savings Plans** and **Reserved Instances**, making them relatively familiar targets for optimization.

However, storage services—especially **Amazon S3** and **Amazon EBS**—are often the next largest contributors to monthly cloud bills.

In one environment I previously designed and operated, improper file retention resulted in **Amazon S3 costs exceeding 300% of the planned monthly budget**. The immediate response was to add application-level logic to aggressively delete files—an example of reactive cost control that could have been avoided with better upfront storage design.

This experience reinforced why understanding storage cost optimization before problems occur is critical. With that perspective, I paid close attention to the following session.

---

## Session Overview

This post summarizes key lessons from the AWS Summit Japan 2025 session:

> 「クラウドストレージのコスト最適化戦略 – AWS ストレージの賢い活用法（AWS-05）」

The focus here is **Amazon S3 cost optimization**.  
*(Amazon EBS optimization will be covered in a separate post.)*

---

## The Common Problem: “Store Everything in S3 Standard”

In many environments, teams default to storing all data in **S3 Standard**, often because:

- Access frequency is unclear  
- Analyzing usage patterns feels time-consuming  
- “We’ll optimize later” seems acceptable at the beginning  

Over time, data volume grows steadily, costs increase uncontrollably, and teams are left wondering which data can be deleted or transitioned—and how.

---

## The Core Principle: Understand Data Characteristics First

Effective cost optimization starts with **understanding the nature of your data**, not with blindly changing storage classes.

From a practical perspective, S3 data can be divided into **two access patterns**, each requiring a different optimization approach.

---

## Two Key S3 Access Patterns

### 1) Predictable or Known Access Patterns  
### 2) Unpredictable or Unknown Access Patterns  

**Examples**

**Predictable access data**
- Application logs  
- Backup data with defined retention periods  
- Compliance or audit records  

**Unpredictable access data**
- User-generated content  
- Ad-hoc analytics data  
- Legacy data with unclear usage history  

---

## Optimizing Predictable Access Data with S3 Lifecycle Rules

For data with known or predictable access patterns, **Amazon S3 Lifecycle rules** are the most effective optimization mechanism.

Lifecycle rules allow you to **automatically transition objects between S3 storage classes or expire them after a defined period**, based on object age.

- AWS Documentation:  
  https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lifecycle-mgmt.html

---

## S3 Storage Classes and Cost Characteristics

Amazon S3 offers multiple storage classes, each optimized for different access requirements:

- **S3 Standard** – Low latency, high availability, higher cost  
- **S3 Standard-IA (Infrequent Access)** – Lower storage cost, retrieval fee applies  
- **S3 Glacier Instant Retrieval (GIR)** – Very low storage cost, *millisecond-level access*  
- **S3 Glacier Flexible Retrieval (GFR)** – Minutes to hours retrieval  
- **S3 Glacier Deep Archive (GDA)** – Lowest cost, hours retrieval  

- AWS Storage Classes Overview:  
  https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html

---

## Common Misconception: “Glacier Means Slow”

A frequent misunderstanding is assuming that all Glacier classes have slow access.

In reality:
- **S3 Glacier Instant Retrieval** provides **millisecond-level access latency**, comparable to S3 Standard  
- Only **Flexible Retrieval** and **Deep Archive** involve minutes-to-hours restore times  

This makes **GIR** a powerful option for rarely accessed data that still requires immediate availability.

---

## Cost Comparison Example (1 TB / Month)

Approximate storage costs for **1 TB per month** (US regions, storage only):

- **S3 Standard**: ~$25  
- **S3 Standard-IA**: ~45% cheaper than Standard  
- **S3 Glacier Instant Retrieval**: ~80% cheaper than Standard  

- Official pricing reference:  
  https://aws.amazon.com/s3/pricing/

---

## A Critical Lifecycle Rule Detail: Transition Costs

When objects are transitioned between storage classes, a **transition request cost** is incurred.

A key point from AWS documentation:

> Lifecycle transition costs are charged **per object**, not per object size.

This means transitioning a large number of very small objects may reduce the expected cost benefits.

For this reason, **Amazon S3 does not transition objects smaller than 128 KB by default** when a new lifecycle rule is created.

- Lifecycle transition considerations:  
  https://docs.aws.amazon.com/AmazonS3/latest/userguide/lifecycle-transition-general-considerations.html

---

## Practical Design Insight

Blindly transitioning all objects after a certain number of days does not always lead to optimal results.

In some cases:
- Leaving small objects in S3 Standard  
- Transitioning only large, cold objects  

can be more cost-effective than applying uniform lifecycle rules.

This highlights an important architectural principle:

> Storage optimization is not just a technical configuration—it is a data design decision.

---

## Final Thoughts

Amazon S3 cost optimization is not about memorizing features.  
It is about being able to explain:

- Why a specific storage class is chosen  
- Why another option is intentionally avoided  
- How access patterns, cost, and recovery requirements trade off  

The AWS Summit Japan 2025 session provided valuable clarity on these decision points, reinforcing the importance of treating storage design as a first-class architectural concern.
