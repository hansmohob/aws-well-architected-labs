---
title: "Level 300: Cloud Intelligence Dashboards for Azure"
date: 2020-08-24T11:16:08-04:00
chapter: false
weight: 8
hidden: false
---

### Last Updated

30th May 2023

### Authors

**Mo Mohoboob** Solution Architect [AWS]

### Contributors
**Chris Schoerning** Solution Architect [AWS], **Anton Kovalenko** Solution Architect [AWS], **Iakov Gan** Technical Account Manager [AWS], **Yuriy Prykhodko** Technical Account Manager [AWS]

### Feedback

We would love to hear your thoughts! Please drop us an [email](mailto:cloud-intelligence-dashboards@amazon.com) or raise an [issue.](/contributing/02_reportingbugs/) Got a great idea to improve this content? Head over to the [Contribution Guide.](/contributing/)

### Introduction
Monitoring cloud usage is an important factor for all customers. Identifying areas for cost optimization starts with understanding cloud spend. Many customers take advantage of the [Cloud Intelligence Dashboards](/cost/200_labs/200_cloud_intelligence/) with Amazon QuickSight and want to use familiar tools to monitor cloud usage across multiple cloud providers. In this lab we show you how to build a solution to view Microsoft Azure usage data in Amazon QuickSight. You'll build a data pipeline to securely pull Azure cost management data to AWS. A QuickSight dashboard will automatically refresh, each day, to display useful insights. You will need to be familiar with the AWS console, but we'll walk you through the setup. This solution does not provide an aggregated view of usage across AWS and Azure (yet).

| | | | |
|-|-|-|-|
|![Images/cidazure-highlevel](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-dashboard-1.png?width=500px)|![Images/cidazure-highlevel](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-dashboard-2.png?width=500px)|![Images/cidazure-highlevel](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-dashboard-3.png?width=500px)|![Images/cidazure-highlevel](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-dashboard-4.png?width=500px)|

| | | | |
|-|-|-|-|

{{% notice note %}}
Click [here](https://d1s0yx3p3y3rah.cloudfront.net/anonymous-embed?dashboard=cid-for-azure) to explore a sample Cloud Intelligence Dashboard for Azure.
{{% /notice %}}

### Goals

* Deploy an ETL pipeline to pull Azure cost management data to Amazon Simple Storage Service (Amazon S3) on a daily recurring schedule.
* Run an initial manual pull of data.
* Deploy a sample QuickSight dashboard.
* Apply a basic set of dashboard customizations.
* Learn how to manage the operation of the solution.

### Prerequisites

1. An existing AWS Account or you must complete the [AWS account setup lab.](/cost/100_labs/100_1_aws_account_setup/)
2. An Amazon QuickSight Enterprise Edition Account. If you do not already have one follow instructions [here.](https://aws.amazon.com/premiumsupport/knowledge-center/quicksight-enterprise-account/)
3. Enough QuickSight SPICE capacity to accommodate your data set. To purchase SPICE, follow instructions [here.](https://docs.aws.amazon.com/quicksight/latest/user/managing-spice-capacity.html#spice-capacity-purchasing)
4. A dedicated Azure Storage Account and blob storage container to house Azure cost management exports. Follow instructions [here.](https://learn.microsoft.com/en-us/azure/storage/common/storage-account-create?tabs=azure-portal) **All data within the blob storage container will be sent to Amazon S3**. It's best to use a dedicated blob container for this project.
5. Make a note of the Blob endpoint URL found under storage accounts > [storage account name] > Settings > Endpoints > Blob service.
6. Azure Active Directory application and service principal. Follow instructions [here.](https://learn.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal)**Note down the *Application secret* which is only displayed during setup.**
7. The service principal must be assigned *Storage Blob Data Contributor* and *Storage Queue Data Contributor* roles scoped to the Storage Account used in step 3. Follow instructions [here.](https://learn.microsoft.com/en-us/azure/storage/blobs/assign-azure-role-data-access?tabs=portal)
8. The Azure Active Directory application's *Application ID* and *Tenant ID*. Follow instructions [here.](https://learn.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#sign-in-to-the-application)
9. Microsoft Azure must be configured to export cost management data to the dedicated Azure storage container used in step 3 on a daily recurring schedule. Follow instructions [here.](https://learn.microsoft.com/en-us/azure/cost-management-billing/costs/tutorial-export-acm-data?tabs=azure-portal)

{{% notice warning %}}
Do not export data for Actual cost (Usage and Purchases) and Amortized cost (Usage and Purchases) to the same Azure storage container. Pick one to avoid unnecessary Azure egress charges.
{{% /notice %}} 

Once you've setup Azure, you'll have the following information and are ready to start the lab:

- [x] Azure Application ID
- [x] Azure Tenant ID
- [x] Azure Application Secret
- [x] Azure Blob endpoint URL


{{% notice note %}}
If you use Hashicorp Terraform, we've provided a sample Terraform template that may remove steps 3 to 6.
{{% /notice %}} 

### Permissions Required

You'll need permissions to create all **AWS resources** shown below.
![Images/cidazure-highlevel.png](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-highlevel.png)

### Costs

Costs for this lab include the following components;

* **AWS resources**. The resources created within this lab typically cost less than **$100** a month. 
* **QuickSight Enterprise**. Author licenses are $18/month with an annual commitment or $24/month without a commitment.
* **Microsoft Azure egress data charges**. Please refer to [this](https://azure.microsoft.com/en-us/pricing/details/bandwidth/) link.

### Duration

**1-2 hours** depending on your level of experience. We'll speed up the process by providing deployment templates to automate your build.

When you're ready to begin, click the button below and hold on to your hat!

{{< prev_next_button link_next_url="./1_solution_design/" button_next_text="Start Lab" first_step="true" />}}

{{% notice note %}}
 This lab: (a) is for informational purposes only, (b) represents current AWS product offerings and practices, which are subject to change without notice, and (c) does not create any commitments or assurances from AWS and its affiliates, suppliers or licensors. AWS content, products or services are provided “as is” without warranties, representations, or conditions of any kind, whether express or implied. The responsibilities and liabilities of AWS to its customers are controlled by AWS agreements, and this document is not part of, nor does it modify, any agreement between AWS and its customers. Customers are responsible for making their own independent assessment of this solution.
{{% /notice %}}