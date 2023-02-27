---
title: "Cloud Intelligence Dashboards for Azure - Setup"
date: 2020-08-24T11:16:08-04:00
chapter: false
weight: 20
hidden: false
---

We've provided a set of deployment templates to speed up the build. Once you complete the setup, we'll run through some basic operations to get you familiar with the solution and the underlying AWS services. If you want a deeper understanding of how the services work, take a look through the code. We've added comments to signpost whats happening in each section.

{{% notice note %}}
The solution you are about to deploy is an example. Use this lab and the accompanying code to design your own solution that meets your specific cost optimization needs.
{{% /notice %}} 

Deployment is a two step process. First we deploy the AWS services, then we deploy the QuickSight dashboard. There are two methods for deploying AWS Services. Pick the one that suits you.

1. [**AWS CloudFormation**](#CloudFormation-Deployment). We will guide you through the setup step by step. You will need to setup Azure resources prior to deployment.
2. [**Hashicorp Terraform**](#terraform-deployment). You will need to be familiar with Terraform and have an existing deployment pipeline. We include an example template showing you how to deploy Azure resources.

### CloudFormation Deployment (14 Steps)
{{%expand "Click to expand" %}}

1. [Download](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Code/AWSCIDforAzure-CFN.zip) setup files and extract locally. The archive contains files listed below. **You don't need to extract individual files**.

* **azure-arm-identity.zip** Lambda layer for Azure identity
* **azure-arm-storage.zip** Lambda layer for Azure Storage
* **cid-azure-gluejob-cfn.py** Glue python script
* **cid-azure-lambda01.zip - cid-azure-lambda07.zip** Lambda function code. 
* **cid-azure-stack.yaml** CloudFormation template.

2. Place the files in a new or existing Amazon S3 bucket. CloudFormation will access these files during deployment. If you are creating a new S3 bucket don't use the name in the screenshot above, someones probably already grabbed it! S3 bucket names need to be globally unique.
![Images/cidazure-setup-cfn-s3source](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-s3source.png?width=1000px)

3. Browse to the CloudFormation service.
![Images/cidazure-setup-cfn-browse](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-browse.png?width=1000px)

4. Click **Create stack** and choose **With new resources (standard)**.
![Images/cidazure-setup-cfn-createstack](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-createstack.png?width=1000px)

5. Enter `http://s3.amazonaws.com/BUCKETNAME/cid-azure-stack.yaml` into the **Amazon S3 URL** field. Replace *BUCKETNAME* with the name of the bucket you used in step 2. Click **Next**
![Images/cidazure-setup-cfn-template](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-template.png?width=1000px)

6. Give the stack a name so you can identify it later on.
![Images/cidazure-setup-cfn-stackname](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-stackname.png?width=1000px)

7. Let's set values for our parameters. Fill in the **Common Settings** section. Refer to the [Parameters](#parameters) section for guidance.
![Images/cidazure-setup-cfn-commonsettings](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-commonsettings.png?width=1000px)

8. Fill in the **Microsoft Azure Settings** section. Refer to the [Parameters](#parameters) section for guidance.
![Images/cidazure-setup-cfn-azuresettings](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-azuresettings.png?width=1000px)

9. Fill in the **Data Copy Settings** section. Refer to the [Parameters](#parameters) section for guidance.
![Images/cidazure-setup-cfn-datasettings](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-datasettings.png?width=1000px)

10. Leave the **Advanced Settings** as default and click **Next**

11. Configure Stack options. If you require a specific IAM role to deploy the template or need to add additional tags, you can do this here. Otherwise leave the settings as default. When you're ready click **Next**
![Images/cidazure-setup-cfn-stackoptions](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-stackoptions.png?width=1000px)

12. Review the Cloudformation stack settings, check the box to **acknowledge that AWS CloudFormation might create IAM resources with custom names** and click **Submit**
![Images/cidazure-setup-cfn-stackreview](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-stackreview.png?width=1000px)

13. The CloudFormation stack will start to deploy. Click the **refresh** button to view progress. If you see a red status event, then something went wrong. Review the error, correct and try again.
![Images/cidazure-setup-cfn-stackprogress](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-stackprogress.png?width=1000px)

14. The deployment will run for a few minutes. Once deployment is complete, you'll see a *CREATE_COMPLETE* message.
![Images/cidazure-setup-cfn-stackcomplete](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-stackcomplete.png?width=1000px)

Great job, you've completed the setup! Now move onto [Dashboard Deployment.](#Dashboard-Deployment)
{{% /expand%}}

### Terraform Deployment (5 Steps)
{{%expand "Click to expand" %}}


Great job, you've completed the setup now move onto [Dashboard Deployment.](#Dashboard-Deployment)
{{% /expand%}}

### Dashboard Deployment (8 Steps)
{{%expand "Click to expand" %}}

{{% /expand%}}

### Parameters
{{%expand "Click to expand" %}}
Parameters allow us to configure the deployment. Each parameter has a description that tells you what it does and a default to give you an example of the type of value it needs. Where you configure parameters will depend on the deployment option you choose;

|CloudFormation parameters are configured in the AWS console|Terraform parameters are set in the *terraform.tfvars* file|
|-|-|
|![Images/cidazure-setup-cfn-parameters-cfn](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-parameters-cfn.png?width=600px)|![Images/cidazure-setup-cfn-parameters-tf](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-parameters-tf.png?width=600px)|

 There are no constraints set on parameters. Some Parameters only exist for specific deployment types. The following table provides additional information for each parameter.

|Parameter|Section|Deployment|Guidance|
|-|-|-|-|
|SourceBucket|Common Settings|CloudFormation|Used for CloudFormation template deployment only. The deployment will fail if it cannot find source  |
|CustomerCode|Common Settings|CloudFormation, Terraform|This is a prefix attached to each resource|
|EnvironmentCode|Common Settings|CloudFormation, Terraform|desc|
|CustomerTag|Common Settings|CloudFormation, Terraform|desc|
|EnvironmentTag|Common Settings|CloudFormation, Terraform|desc|
|AzureBlobURL|Microsoft Azure Settings|CloudFormation, Terraform|desc|
|AzureApplicationID|Microsoft Azure Settings|CloudFormation, Terraform|desc|
|AzureTenantID|Microsoft Azure Settings|CloudFormation, Terraform|desc|
|AzureSecretKey|Microsoft Azure Settings|CloudFormation, Terraform|desc|
|AzureTags|Microsoft Azure Settings|CloudFormation, Terraform|desc|
|AzureCopySchedule|Data Copy Settings|CloudFormation, Terraform|desc|
|GlueCopySchedule|Data Copy Settings|CloudFormation, Terraform|desc|
|EnableDuplicationRemoval|Data Copy Settings|CloudFormation|desc|
|BlobToS3SyncStartDate|Data Copy Settings|CloudFormation, Terraform|desc|
|PartitionSize|Advanced Settings|CloudFormation, Terraform|desc|
|MaxPartitionsPerFile|Advanced Settings|CloudFormation, Terraform|desc|
|UseFullFilePath|Advanced Settings|CloudFormation, Terraform|desc|
|Region|Regions and Availability Zones|Terraform|desc|

{{% /expand%}}

Ok we're all setup, lets move onto the next section and run a manual pull of data.

{{< prev_next_button link_prev_url="../1_solution_design/" link_next_url="../3_common_tasks/" />}}