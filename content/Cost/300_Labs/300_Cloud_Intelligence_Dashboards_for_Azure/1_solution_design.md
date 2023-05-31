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

In this section, we dive into the solution's mechanics, enabling you to customize the design to suit your needs.

### AWS Services
Let's examine the services and their roles in the design.
![Images/cidazure-lambda.png](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-services.png)

[**Amazon EventBridge**](https://aws.amazon.com/eventbridge/)
: -- Serverless service that uses events to connect application components together, making it easier for you to build scalable event-driven applications.
: -- Amazon EventBridge triggers a Lambda function on a schedule. The Lambda function starts the data copy process.

[**AWS Lambda**](https://aws.amazon.com/lambda/)
: -- Compute service that lets you run code without provisioning or managing servers.
: -- Lambda functions run the code that pulls CSV files from Azure to AWS. They use the [Azure libraries (SDK) for Python](https://learn.microsoft.com/en-us/azure/developer/python/sdk/azure-sdk-overview).

[**Amazon Simple Notification Service (Amazon SNS)**](https://aws.amazon.com/sns/)
: -- Managed service that provides message delivery from publishers to subscribers (also known as producers and consumers).
: -- Lambda Functions communicate via Amazon SNS using a publish/subscribe pattern.

[**Amazon Simple Storage Service (Amazon S3)**](https://aws.amazon.com/s3/)
: -- Object storage service offering industry-leading scalability, data availability, security, and performance.
: -- Azure cost management data is stored in an encrypted Amazon S3 bucket. The bucket stores data in raw and transformed states.

[**AWS Glue**](https://aws.amazon.com/glue/)
: -- Serverless data integration service that makes it easier to discover, prepare, move, and integrate data from multiple sources for analytics, machine learning (ML), and application development.
: -- AWS Glue does the heavy lifting, transforming incoming data, making it easier and more efficient to query.

[**Amazon Athena**](https://aws.amazon.com/athena/)
: -- Serverless, interactive analytics service built on open-source frameworks, supporting open-table and file formats.
: -- Amazon Athena queries transformed data, providing a data source for Amazon QuickSight.

[**Amazon QuickSight**](https://aws.amazon.com/quicksight/)
: -- Powers data-driven organizations with unified business intelligence (BI) at hyperscale.
: -- Amazon QuickSight helps us gain insights from data through captivating visualizations.

[**AWS Secrets Manager**](https://aws.amazon.com/secrets-manager/)
: -- Helps you manage, retrieve, and rotate database credentials, API keys, and other secrets throughout their lifecycles.
: -- AWS Secrets Manager stores secrets such as the Azure Application Secret. Secrets Manager also stores parameters used by Lambda functions.

[**AWS Key Management Service (AWS KMS)**](https://aws.amazon.com/kms/)
: -- Lets you create, manage, and control cryptographic keys across your applications and more than 100 AWS services.
: -- AWS KMS generates a cryptographic key used to encrypt data in AWS Lambda, Amazon S3, AWS Glue and Amazon Athena.

[**AWS Identity and Access Management (IAM)**](https://aws.amazon.com/iam/)
: -- Lets you create, manage, and control cryptographic keys across your applications and more than 100 AWS services.
: -- IAM helps us adhere to the principle of least privilege. Services should only have access to the specific data, resources and applications needed to complete a required task.

[**AWS Systems Manager**](https://aws.amazon.com/systems-manager/)
: -- Acts as the operations hub for your AWS applications and resources.
: -- Systems Manager Parameter Store holds parameters used by the AWS Glue job.

[**Amazon CloudWatch**](https://aws.amazon.com/cloudwatch/)
: -- Collects and visualizes real-time logs, metrics, and event data in automated dashboards to streamline your infrastructure and application maintenance.
: -- All logs and metrics are sent to CloudWatch. Cloudwatch dashboards help us troubleshoot issues by providing a single pane of glass.

[**AWS Resource Groups**](https://docs.aws.amazon.com/ARG/latest/userguide/resource-groups.html)
: -- AWS Resource Groups lets you organize AWS resources into groups using criteria that you define as tags.
: -- All resources are tagged and grouped in a Resource Group for easy identification of solution components.

While the main focus of this lab is on cost optimization, it's important to consider all pillars of the Well-Architected Framework when designing a solution.

### Naming Convention

All resources adhere to a consistent naming convention.
![Images/cidazure-naming.png](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-naming.png?width=1000px)

As with most rules, there are a few exceptions.

1. If you deploy the solution using CloudFormation, the Amazon S3 bucket will be named **stack name**-s3bucket-**unique id**
2. Systems Manager parameters start with **cidazure-var**.

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
|iap|IAM policy|
|iar|IAM role|
|kms|AWS KMS key|
|lmd|AWS Lambda function|
|lml|AWS Lambda layer|
|rgg|AWS Resource Group group|
|smp|AWS Systems Manager Parameter Store parameter|
|sms|AWS Secrets Manager secret |
|sns|Amazon SNS topic|
|sss|Amazon S3 bucket (Terraform only)|

### High Level Diagram

The diagram below shows you how AWS services interact and how AWS communicates with Azure. Arrows indicate the direction of invocation. Notice that we haven't included Amazon CloudWatch or AWS KMS. These are used throughout the design.
![Images/cidazure-midlevel.png](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-midlevel.png?width=1000px)

|Step|Description|
|-|-|
|1|An EventBridge scheduled task triggers the first AWS Lambda function to start the copy of Azure cost management CSV files to AWS.|
|2|Lambda functions query parameters and secrets stored in Secrets Manager. Refer to [Lambda blob copy stack section.](#lambda-blob-copy-stack)|
|3|Lambda functions communicate with each other using Amazon SNS topics. Refer to [Lambda blob copy stack section.](#lambda-blob-copy-stack)|
|4|Lambda functions requests an OAUTH token from Azure Active Directory over HTTPS.|
|5|Lambda functions interact with Azure blob storage over HTTPS.|
|6|Lambda functions download CSV files to the Amazon S3 *azurecidraw* folder. It's not really a folder, but let's pretend it is!|
|7|An AWS Glue schedule triggers the AWS Glue job.|
|8|AWS Glue job queries System Manager Parameter store to retrieve values used by the job.|
|9|AWS Glue job loads data from the Amazon S3 *azurecidraw* folder.|
|10|AWS Glue job transformations data and converts CSV files to Parquet format. The resulting Parquet files are stored in the Amazon S3 *azurecidparquet* folder|
|11|AWS Glue job updates the AWS Glue table schema. The schema helps other services, like Athena, understand the structure of the data stored in Amazon S3.|
|12|AWS Glue job moves the CSV files from the Amazon S3 *azurecidraw* folder to the *azurecidprocessed* folder and subsequently deletes the contents of the *azurecidraw* folder. This process ensures that the original files are preserved, allowing for potential reprocessing if needed.|
|13|If an error occurs while processing the CSV file, it is automatically moved to the *azureciderror* folder.|
|14 and 15|An Athena query uses the AWS Glue Table and Parqeut files to build an Athena view.|
|16|Amazon QuickSight uses the Athena view as its datasource.|

### Lambda blob copy stack 

Let's zoom in on the Lambda functions and Amazon SNS topics. Each function is a discrete piece of code that performs a specific function. The functions use Amazon SNS to communicate with each other. All functions are written in Python.
![Images/cidazure-lambda.png](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-lambda.png?width=1000px)

Lambda functions 04, 05 and 06 are used to upload large files (100 MB by default). They take advantage of Amazon S3's multipart upload option to accelerate file transfer.
![Images/cidazure-lambda-filesize.png](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-lambda-filesize.png?width=1000px)

### Other Design Components

* The Amazon S3 bucket folder hierarchy is really important. Things won't work if they are not where they are supposed to be. 
* The AWS Glue job uses a script written in PySpark. The script is stored in the *azurecidscripts* folder in a Terraform deployment and in the Amazon S3 artifacts bucket for a CloudFormation deployment.

Now that we have an understanding of the solution design, let’s go build it!

{{< prev_next_button link_prev_url="../" link_next_url="../2_setup/" />}}