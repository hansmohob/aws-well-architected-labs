---
title: "Level 300: Cloud Intelligence Dashboards for Azure"
date: 2020-08-24T11:16:08-04:00
chapter: false
weight: 8
hidden: false
---

### Last Updated

28 February 2023

### Authors

**Mo Mohoboob** Solution Architect [AWS], **Chris Schoerning** Solution Architect [AWS], **Anton Kovalenko** Solution Architect [AWS]

### Contributors
**Iakov Gan** Technical Account Manager [AWS], **Yuriy Prykhodko** Technical Account Manager [AWS]

### Feedback

We would love to hear your thoughts! Please drop us an [email](mailto:cloud-intelligence-dashboards@amazon.com) or raise an [issue.](/contributing/02_reportingbugs/) Got a great idea to improve this content? Head over to the [Contribution Guide.](/contributing/)

### Introduction
Monitoring cloud usage is an important factor for all customers. Identifying areas for cost optimization starts with understanding cloud spend. Many customers are familiar with Amazon QuickSight and the [Cloud Intelligence Dashboards](/cost/200_labs/200_cloud_intelligence/) and want to use familiar tools to monitor cloud usage across multiple cloud providers. In this lab we show you how you can build a solution to view your Microsoft Azure usage data in Amazon QuickSight. You will build a data pipeline which securely pulls Azure cost management data to AWS. Your QuickSight dashboard will automatically refresh each day to display trends. You will need to be familiar with the AWS console, but we'll walk you through the design and setup step by step. This solution does not provide an aggregated view of usage across AWS and Azure (yet).

![Images/cidazure-highlevel.png](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-highlevel.png)

### Goals

* Understand how AWS services are used as part of the solution.
* Deploy an ETL pipeline to pull Azure cost management data to an Amazon S3 bucket on a daily recurring schedule.
* Deploy a sample QuickSight dashboard to view your Azure usage data.
* Run a manual pull of data.
* Apply a basic set of dashboard customizations.

### Prerequisites

1. An existing AWS Account or you must complete the [AWS account setup lab.](/cost/100_labs/100_1_aws_account_setup/)
2. An Amazon QuickSight Enterprise Edition Account
3. A dedicated Azure Storage Account and storage container to house Azure cost management exports. The Storage Account should be configured for blob storage. Follow instructions [here.](https://learn.microsoft.com/en-us/azure/storage/common/storage-account-create?tabs=azure-portal)
4. An Azure Active Directory application and service principal. Follow instructions [here.](https://learn.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal)**Note down the *Application secret* which is only displayed during setup.**
5. The service principle must be assigned *Storage Blob Data Contributor* and *Storage Queue Data Contributor* roles scoped to the Storage Account used in step 3. Follow instructions [here.](https://learn.microsoft.com/en-us/azure/storage/blobs/assign-azure-role-data-access?tabs=portal)
6. The Azure Active Directory application's *Application ID* and *Tenant ID*. Follow instructions [here.](https://learn.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#sign-in-to-the-application)
7. Microsoft Azure must be configured to export cost management data to the dedicated Azure storage container, used in step 3, on a daily recurring schedule. Follow instructions [here.](https://learn.microsoft.com/en-us/azure/cost-management-billing/costs/tutorial-export-acm-data?tabs=azure-portal)

Once you've setup Azure, you will need the following information to start the lab:

- [x] Azure Application ID
- [x] Azure Tenant ID
- [x] Azure Application Secret


{{% notice note %}}
If you use Hashicorp Terraform within your organization, we have provided a sample Terraform template that may remove steps 3 to 6.
{{% /notice %}} 

### Permissions Required

<to complete>

### Costs

Costs for this lab are broken down into the following components;

* **AWS resources**. The resources created within this lab typically cost less than **$100** a month. 
* **QuickSight Enterprise**. License costs start at **$18** a month.
* **Microsoft Azure egress data charges**. Please refer to [this](https://azure.microsoft.com/en-us/pricing/details/bandwidth/) link.

### Duration

**1-2 hours** depending on your level of experience. We'll speed up deployment by providing CloudFormation and Terraform templates.

When you are ready to begin, click the button below and hold on to your hat!

{{< prev_next_button link_next_url="./1_solution_design/" button_next_text="Start Lab" first_step="true" />}}

{{% notice note %}}
 This lab: (a) is for informational purposes only, (b) represents current AWS product offerings and practices, which are subject to change without notice, and (c) does not create any commitments or assurances from AWS and its affiliates, suppliers or licensors. AWS content, products or services are provided “as is” without warranties, representations, or conditions of any kind, whether express or implied. The responsibilities and liabilities of AWS to its customers are controlled by AWS agreements, and this document is not part of, nor does it modify, any agreement between AWS and its customers. Customers are responsible for making their own independent assessment of this solution.
{{% /notice %}} 