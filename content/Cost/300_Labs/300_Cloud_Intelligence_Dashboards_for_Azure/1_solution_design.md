---
title: "Cloud Intelligence Dashboards for Azure - Solution Design"
date: 2020-08-24T11:16:08-04:00
chapter: false
weight: 10
hidden: false
---

{{% notice note %}}
If you just want to deploy the dashboard, feel free to jump to the [setup](../2_setup/)section.
{{% /notice %}}

In this section we take a look under the hood to show you how the solution works. This will help you customize the design to meet your needs.

### AWS Services
Let's take a look at the services we'll use and their roles within the design.
![Images/cidazure-lambda.png](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-services.png)

**Amazon EventBridge**
: -- Serverless service that uses events to connect application components together, making it easier for you to build scalable event-driven applications.
: -- Amazon EventBridge triggers an AWS Lambda function on a schedule. The Lambda Function starts the data copy process.

**AWS Lambda** 
: -- Compute service that lets you run code without provisioning or managing servers.
: -- AWS Lambda Functions run the code that pulls CSV files from Azure to AWS. [Azure libraries (SDK) for Python](https://learn.microsoft.com/en-us/azure/developer/python/sdk/azure-sdk-overview) are used.

**Amazon Simple Notification Service (Amazon SNS)**
: -- Managed service that provides message delivery from publishers to subscribers (also known as producers and consumers).
: -- AWS Lambda Functions communicate with each other using a publish/subscribe pattern hosted on Amazon SNS.

**Amazon Simple Storage Service (Amazon S3)**
: -- Object storage service offering industry-leading scalability, data availability, security, and performance.
: -- Azure cost management data is stored in an encrypted Amazon S3 bucket. The bucket stores data in raw and transformed states.

**AWS Glue**
: -- Serverless data integration service that makes it easier to discover, prepare, move, and integrate data from multiple sources for analytics, machine learning (ML), and application development.
: -- AWS Glue does the heavy lifting, transforming incoming data, making it easier and more efficient to query.

**Amazon Athena**
: -- Serverless, interactive analytics service built on open-source frameworks, supporting open-table and file formats.
: -- Amazon Athena queries transformed data, providing a data source for Amazon QuickSight.

**Amazon QuickSight**
: -- Powers data-driven organizations with unified business intelligence (BI) at hyperscale.
: -- Amazon QuickSight helps us understand the data through awesome visuals.

**AWS Secrets Manager**
: -- Helps you manage, retrieve, and rotate database credentials, API keys, and other secrets throughout their lifecycles.
: -- AWS Secrets Manager stores secrets such as the Azure Application Secret. Secrets Manager also stores parameters used by the Lambda Functions.

**AWS Key Management Service (AWS KMS)**
: -- Lets you create, manage, and control cryptographic keys across your applications and more than 100 AWS services.
: -- AWS KMS generates a cryptographic key used to encrypt data in Lambda, S3, Glue and Athena.

**AWS Identity and Access Management (IAM)**
: -- Lets you create, manage, and control cryptographic keys across your applications and more than 100 AWS services.
: -- AWS IAM helps us adhere to the principle of least privilege. Services should only have access to the specific data, resources and applications needed to complete a required task.

**AWS Systems Manager**
: -- Acts as the operations hub for your AWS applications and resources.
: -- Systems Manager Parameter Store holds parameters used to setup the Glue job when deploying through CloudFormation.

**Amazon CloudWatch**
: -- Collects and visualizes real-time logs, metrics, and event data in automated dashboards to streamline your infrastructure and application maintenance.
: -- All logs and metrics are sent to CloudWatch. A Cloudwatch dashboard helps us troubleshoot issues.

**AWS Resource Groups**
: -- AWS Resource Groups lets you organize AWS resources into groups using criteria that you define as tags.
: -- All resources are tagged and grouped together in a Resource Group. This helps us identify our solutions components.

While the main focus of this lab is on cost optimization, it's important to consider all pillars of the Well-Architected Framework when designing a solution. 
continue - align to WAR.

### Naming Convention

All resources created by the automated deployment follow a standard naming pattern. 
![Images/cidazure-naming.png](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-naming.png?width=1000px)

As with most rules, there are a few exceptions.

1. If you deploy the solution with CloudFormation your S3 bucket will be named **stack name**-s3bucket-**unique id**
2. System Center parameters start with **cidazure-var**.

|AWS Resource ID|Description|
|-|-|
|atq|Amazon Athena named query|
|atw|Amazon Athena workgroup|
|cwd|Amazon Cloudwatch dashboard|
|evr|Amazon EventBridge rule |
|gld|AWS Glue database|
|glj|AWS Glue job|
|glt|AWS Glue table|
|glx|AWS Glue security configuration|
|iap|AWS IAM policy|
|iar|AWS IAM role|
|kms|AWS KMS key|
|lmd|AWS Lambda function|
|lml|AWS Lambda layer|
|rgg|AWS Resource Group group|
|smp|AWS Systems Manager Parameter Store parameter|
|sms|AWS Secrets Manager secret |
|sns|Amazon SNS topic|
|sss|Amazon S3 bucket (Terraform only)|

### High Level Diagram

The diagram below shows you how AWS service interact and how AWS communicates with Azure. Arrows indicate the direction of invocation. Notice that we haven't included Cloudwatch and KMS, which are used throughout the solution.
![Images/cidazure-midlevel.png](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-midlevel.png)

|Step|Description|
|-|-|
|1|An EventBridge scheduled task triggers the first AWS Lambda function to start the copy of Azure cost management CSV files to AWS.|
|2|Lambda functions query parameters and secrets stored in Secrets Manager. Refer to [Lambda blob copy stack section.](#lambda-blob-copy-stack)|
|3|Lambda functions communicate with each other using SNS topics. Refer to [Lambda blob copy stack section.](#lambda-blob-copy-stack)|
|4|Lambda functions requests an OAUTH token from Azure Active Directory over HTTPS.|
|5|Lambda functions interact with Azure blob storage over HTTPS.|
|6|Lambda functions download CSV files to the S3 *azurecidraw* folder. It's not really a folder, but let's pretend it is!|
|7|A Glue schedule triggers the Glue job|
|8|The Glue job queries System Manager Parameter store to get values used by the job. E.g. S3 bucket name. This is part of CloudFormation deployments only.|
|9|The Glue job loads data from the S3 *azurecidraw* folder.|
|10|The Glue job adds new columns, changes data field types and transforms the data from CSV to Parquet format. The Parquet files are stored in the S3 *azurecidparquet* folder.|
|11|The Glue job updates the Glue table schema. The schema helps other services, like Athena, understand the structure of the data stored in S3.|
|12|The Glue job copies the CSV files from the S3 *azurecidraw* folder to the *azurecidprocessed* folder, then deletes the contents of the *azurecidraw* folder. We do this to retain the original files, in case we need to reprocess.|
|13 and 14|An Athena query uses the Glue Table and parqeut files to build an Athena view.|
|15|QuickSight uses the Athena view as its datasource.|

### Lambda blob copy stack 

Let's zoom in on the Lambda functions and SNS topics. Each function is a discrete piece of code that performs a specific function. The functions use SNS to talk to each other. All functions are written in Python.
![Images/cidazure-lambda.png](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-lambda.png)

Lambda functions 04, 05 and 06 are used to download large files. They take advantage of Amazon S3's multipart download option to accelerate file transfer.
![Images/cidazure-lambda-filesize.png](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-lambda-filesize.png)

### Other Design Components

* The S3 bucket folder hierarchy is really important. Things wont work if they are not where they are supposed to be. 
* The Glue job uses a script written in pyspark. The script is stored in the *azurecidscripts* folder in a Terraform deployment and in the S3 artifacts bucket for a CloudFormation deployment.

Now we have an understanding of the solution design, let’s build!

{{< prev_next_button link_prev_url="../" link_next_url="../2_setup/" />}}