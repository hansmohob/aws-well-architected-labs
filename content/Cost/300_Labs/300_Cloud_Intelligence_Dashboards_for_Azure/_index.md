---
title: "Level 300: Cloud Intelligence Dashboards for Azure"
date: 2020-08-24T11:16:08-04:00
chapter: false
weight: 8
hidden: false
---

### Last Updated

17th April 2023

### Authors

**Mo Mohoboob** Solution Architect [AWS], **Chris Schoerning** Solution Architect [AWS], **Anton Kovalenko** Solution Architect [AWS]

### Contributors
**Iakov Gan** Technical Account Manager [AWS], **Yuriy Prykhodko** Technical Account Manager [AWS]

### Feedback

We would love to hear your thoughts! Please drop us an [email](mailto:cloud-intelligence-dashboards@amazon.com) or raise an [issue.](/contributing/02_reportingbugs/) Got a great idea to improve this content? Head over to the [Contribution Guide.](/contributing/)

### Introduction
Monitoring cloud usage is an important factor for all customers. Identifying areas for cost optimization starts with understanding cloud spend. Many customers use Amazon QuickSight and the [Cloud Intelligence Dashboards](/cost/200_labs/200_cloud_intelligence/) and want to use familiar tools to monitor cloud usage across multiple cloud providers. In this lab we show you how you can build a solution to view Microsoft Azure usage data in Amazon QuickSight. You will build a data pipeline which securely pulls Azure cost management data to AWS. QuickSight dashboard will automatically refresh, each day, to show useful insights. You will need to be familiar with the AWS console, but we'll walk you through the setup. This solution does not provide an aggregated view of usage across AWS and Azure (yet).

| | | | |
|-|-|-|-|
|![Images/cidazure-highlevel.png](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-dashboard-1.png?width=500px)|![Images/cidazure-highlevel.png](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-dashboard-2.png?width=500px)|![Images/cidazure-highlevel.png](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-dashboard-3.png?width=500px)|![Images/cidazure-highlevel.png](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-dashboard-4.png?width=500px)|

| | | | |
|-|-|-|-|

Explore an example dashboard **TOCOMPLETE**

### Goals

* Understand the AWS services and their roles in the solution.
* Deploy an ETL pipeline to pull Azure cost management data to an Amazon S3 bucket on a daily recurring schedule.
* Run an initial manual pull of data.
* Deploy a sample QuickSight dashboard to view Azure usage data.
* Apply a basic set of dashboard customizations.

### Prerequisites

1. An existing AWS Account or you must complete the [AWS account setup lab.](/cost/100_labs/100_1_aws_account_setup/)
2. An Amazon QuickSight Enterprise Edition Account. If you do not already have one follow instructions [here.](https://aws.amazon.com/premiumsupport/knowledge-center/quicksight-enterprise-account/)
3. A dedicated Azure Storage Account and blob storage container to house Azure cost management exports. Follow instructions [here.](https://learn.microsoft.com/en-us/azure/storage/common/storage-account-create?tabs=azure-portal) **All data within the blob storage container will be sent to Amazon S3**. It's best to use a dedicated blob container for this project.
4. An Azure Active Directory application and service principal. Follow instructions [here.](https://learn.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal)**Note down the *Application secret* which is only displayed during setup.**
5. The service principle must be assigned *Storage Blob Data Contributor* and *Storage Queue Data Contributor* roles scoped to the Storage Account used in step 3. Follow instructions [here.](https://learn.microsoft.com/en-us/azure/storage/blobs/assign-azure-role-data-access?tabs=portal)
6. The Azure Active Directory application's *Application ID* and *Tenant ID*. Follow instructions [here.](https://learn.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#sign-in-to-the-application)
7. Microsoft Azure must be configured to export cost management data to the dedicated Azure storage container used in step 3 on a daily recurring schedule. Follow instructions [here.](https://learn.microsoft.com/en-us/azure/cost-management-billing/costs/tutorial-export-acm-data?tabs=azure-portal)

{{% notice warning %}}
Do not export data for Actual cost (Usage and Purchases) and Amortized cost (Usage and Purchases) to the same Azure storage container. Only one metric will work with this solution. 
{{% /notice %}} 

Once you've setup Azure, you'll have the following information and are ready to start the lab:

- [x] Azure Application ID
- [x] Azure Tenant ID
- [x] Azure Application Secret


{{% notice note %}}
If you use Hashicorp Terraform within your organization, we have provided a sample Terraform template that may remove steps 3 to 6.
{{% /notice %}} 

### Permissions Required

You'll need permissions to create all **AWS resources** shown below.

![Images/cidazure-highlevel.png](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-highlevel.png)

You'll also need permissions to deploy through **AWS CloudFormation** or **Hashicorp Terraform**. 

### Costs

Costs for this lab are broken down into the following components;

* **AWS resources**. The resources created within this lab typically cost less than **$100** a month. 
* **QuickSight Enterprise**. License costs start at **$18** a month.
* **Microsoft Azure egress data charges**. Please refer to [this](https://azure.microsoft.com/en-us/pricing/details/bandwidth/) link.

### Duration

**1-2 hours** depending on your level of experience. We'll speed this up by providing deployment templates to automate the build.

When you're ready to begin, click the button below and hold on to your hat!

{{< prev_next_button link_next_url="./1_solution_design/" button_next_text="Start Lab" first_step="true" />}}

{{% notice note %}}
 This lab: (a) is for informational purposes only, (b) represents current AWS product offerings and practices, which are subject to change without notice, and (c) does not create any commitments or assurances from AWS and its affiliates, suppliers or licensors. AWS content, products or services are provided “as is” without warranties, representations, or conditions of any kind, whether express or implied. The responsibilities and liabilities of AWS to its customers are controlled by AWS agreements, and this document is not part of, nor does it modify, any agreement between AWS and its customers. Customers are responsible for making their own independent assessment of this solution.
{{% /notice %}}