---
title: "Cloud Intelligence Dashboards for Azure - Setup"
date: 2020-08-24T11:16:08-04:00
chapter: false
weight: 20
hidden: false
---

We've provided a set of deployment templates to speed up the build. Once you complete the setup, we'll run through basic operations to get you familiar with the solution and the underlying AWS services. If you want a deeper understanding of how the services work, take a look through the code. We've added comments to signpost whats happening in each section.

{{% notice note %}}
The solution you are about to deploy is an example. Use this lab and the accompanying code to create your own version that meets your specific cost optimization needs.
{{% /notice %}} 

Deployment is a three step process. 

1. Deploy AWS services
2. Perform a manual run to copy the first batch of data
3. Setup Amazon QuickSight and import an example dashboard

There are 2 **(Options)** to deploy AWS Services. Pick the one that suits you.

1. [**AWS CloudFormation**](#step-1-option-1-cloudformation-deployment). We'll guide you through the setup step by step. You will need to setup Azure resources prior to deployment.
2. [**Hashicorp Terraform**](#step-1-option-2-terraform-deployment). You will need to be familiar with Terraform and have an existing deployment pipeline. We include a sample template for Azure resources.

{{% notice note %}}
If you encounter an error during the lab, head over to the [Observability and Troubleshooting](../3_common_tasks)section.
{{% /notice %}}

### Step 1 (Option-1) CloudFormation Deployment
{{%expand "Click to expand" %}}

1. [Download](https://github.com/aws-samples/aws-data-pipelines-for-azure-storage/archive/refs/heads/main.zip) the source code and extract locally. The *CFN* folder contains files listed below. **You don't need to extract individual files**.

* **azure-arm-identity.zip** Lambda layer for Azure identity
* **azure-arm-storage.zip** Lambda layer for Azure storage
* **cid-azure-gluejob-cfn.py** Glue job python script
* **cid-azure-lambda01.zip - cid-azure-lambda06.zip** Azure blob copy Lambda functions
* **cid-azure-stack.yaml** CloudFormation template.
* **cid-azure-dashboard.yaml** Sample QuickSight dashboard

2. Place the files in a new Amazon S3 bucket. CloudFormation will access these during deployment. Don't use the name in the screenshot below, someone's already used it and Amazon S3 bucket names need to be globally unique.
![Images/cidazure-setup-cfn-s3source](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-s3source.png?width=1000px)

3. Click into **cid-azure-stack-yaml** and copy the Object URL. You'll need this in a moment.
![Images/cidazure-setup-cfn-s3objecturl](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-s3objecturl.png?width=1000px)

4. Browse to the **CloudFormation** service.
![Images/cidazure-setup-cfn-browse](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-browse.png?width=1000px)

5. Click **Create stack** and choose **With new resources (standard)**.
![Images/cidazure-setup-cfn-createstack](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-createstack.png?width=1000px)
 
6. Paste the Object URL you copied earlier into the **Amazon S3 URL** field. Click **Next**.
![Images/cidazure-setup-cfn-template](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-template.png?width=1000px)

7. Give the stack a name. An Amazon S3 bucket will be created starting with the name you enter here.
![Images/cidazure-setup-cfn-stackname](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-stackname.png?width=1000px)

8. Let's set values for our parameters. Fill in the **Common Settings** section. Refer to the [Parameters](#parameters) section for guidance.

9. Fill in the **Microsoft Azure Settings** section. Refer to the [Parameters](#parameters) section for guidance.

10. If you're having trouble identifying what you need for the **AzureFolderPath** parameter, here's an example. In the screenshot below we would enter directory/* **this field is case sensitive**. 
![Images/cidazure-setup-cfn-azurefolder.png](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-azurefolder.png?width=400px)

11. Fill in the **Data Copy Settings** section. Refer to the [Parameters](#parameters) section for guidance.

12. Leave the **Advanced Settings** as default and click **Next**.

13. Configure Stack options. If you require a specific IAM role to deploy the template or need to add additional tags, you can do this here. Otherwise leave these settings as default. When you're ready click **Next**.
![Images/cidazure-setup-cfn-stackoptions](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-stackoptions.png?width=1000px)

14. Review the CloudFormation stack settings, check the box to **acknowledge that AWS CloudFormation might create IAM resources with custom names** and click **Submit**.
![Images/cidazure-setup-cfn-stackreview](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-stackreview.png?width=1000px)

15. The CloudFormation stack will start to deploy. Click the **refresh** button to view progress. If you see a red status event, then something went wrong. Review the error, correct and try again.
![Images/cidazure-setup-cfn-stackprogress](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-stackprogress.png?width=1000px)

16. The deployment will run for a few minutes. When it's finished, you'll see a *CREATE_COMPLETE* message.
![Images/cidazure-setup-cfn-stackcomplete](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-stackcomplete.png?width=1000px)

Great job, you've completed the setup! Now move onto [Manual Run](#step-2-initial-manual-run)
{{% /expand%}}

### Step 1 (Option-2) Terraform Deployment
{{%expand "Click to expand" %}}

1. [Download](https://github.com/aws-samples/aws-data-pipelines-for-azure-storage/archive/refs/heads/main.zip) setup files and extract locally. The *TF* folder contains files listed below. **You don't need to extract individual files**.

* **cid-azure-aws-lambda/azure-arm-identity.zip** Lambda layer for Azure identity
* **cid-azure-aws-lambda/azure-arm-storage.zip** Lambda layer for Azure storage
* **cid-azure-aws-lambda/cid-azure-blobcopy-xxx.py** Azure blob copy Lambda functions
* **cid-azure-aws-glue_schema.json** AWS Glue job python script
* **cid-azure-aws.tf** Terraform template for AWS resources
* **main.tf** AWS provider template
* **variables.tf** Terraform variables file for AWS resources
* **terraform.tfvars** Terraform parameters file
* **cid-azure-dashboard.yaml** Sample QuickSight dashboard
* **cid-azure-azure.tf** Example Terraform template for Azure resources
* **main-azure.tf** Example Azure provider template
* **variables-azure.tf.tf** Example Terraform variables file for Azure resources

2. Edit *terraform.tfvars*. Refer to the Parameters section for guidance.

3. If you're having trouble identifying what you need for the **AzureFolderPath** parameter, here's an example. In the screenshot below we would enter directory/* **this field is case sensitive**.
![Images/cidazure-setup-cfn-azurefolder.png](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-azurefolder.png?width=400px)

4. Run `terraform apply`, review the plan and confirm the action.

Great job, you've completed the setup! Now move onto [Manual Run](#step-2-initial-manual-run).

{{% /expand%}}

### Step 2 Initial Manual Run
{{%expand "Click to expand" %}}
The solution runs automatically at the scheduled times you set in the deployment parameters. If you've had enough for today and want to stop, feel free to come back tomorrow, this part will complete automatically. If you want to visualize data straight away, then let's continue.

As part of the deployment we created a Resource Group to organize our resources into a single view. To view the Resource Group, browse to the **AWS Resource Groups & Tag Editor** service.
![Images/cidazure-setup-manual-resourcegroup](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-manual-resourcegroup.png?width=600px)

Follow the instructions below to start a manual run.

1. Browse to the **AWS Lambda** service.
![Images/cidazure-setup-manual-lambdabrowse](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-manual-lambdabrowse.png?width=1000px)

2. Search for the Lambda functions created by the deployment. In our example we used *cid* as the prefix code and the resource id is *lmd*.
![Images/cidazure-setup-manual-lambdafind](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-manual-lambdafind.png?width=1000px)

3. Click into **Lambda01**. Notice the python code is visible. Take a look around if you have time.
![Images/cidazure-setup-manual-lambda01](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-manual-lambda01.png?width=1000px)

4. Select the **Test tab** and click the **Test** button.
![Images/cidazure-setup-manual-lambda01test](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-manual-lambda01test.png?width=1000px)

5. You should receive an *Execution result: succeeded* message.
![Images/cidazure-setup-manual-lambdasuccess](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-manual-lambdasuccess.png?width=1000px)

6. Head over to the **Amazon S3 bucket** created by the deployment. If you deployed via CloudFormation, the S3 bucket will start with the stack name. If you used Terraform the bucket will start with a prefix code you defined, followed by *sss* as the resource ID. You should see files starting to appear under the *azurecidraw* folder. Wait until all objects have been copied before moving onto the next step.
![Images/cidazure-setup-manual-s3appear](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-manual-s3appear.png?width=1000px)

7. Let's start the AWS Glue job and transform the data. Open the **AWS Glue** service and click into **ETL jobs**.
![Images/cidazure-setup-manual-glue](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-manual-glue.png?width=1000px)

8. Select the AWS Glue job created by the deployment. In our example we used *cid* as the prefix code and the resource id is *glj*.
![Images/cidazure-setup-manual-gluejob](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-manual-gluejob.png?width=1000px)

9. Take a look at the AWS Glue script if you have time. Click on the **Runs tab** and click the **Run** button. The AWS Glue job will execute.
![Images/cidazure-setups-manual-gluestart](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-manual-gluestart.png?width=1000px)

10. When the job completes the run status will change to *success*.
![Images/cidazure-setup-manual-gluesuccess](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-manual-gluesuccess.png?width=1000px)

11. Head back over to the same **Amazon S3 bucket** you accessed earlier. Two new folder have appeared. *azurecidparquet* contains parquet versions of the original CSV files. The original CSV files have moved to the *azurecidprocessed* folder and the *azurecidraw* folder has been deleted.
![Images/cidazure-setup-manual-s3processed](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-manual-s3processed.png?width=1000px)

12. Click into the *azurecidparquet* folder. You'll notice data is partitioned by month.
![Images/cidazure-setup-manual-s3parquet](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-manual-s3parquet.png?width=1000px)

Awesome! You've completed the manual run, let's move onto [Dashboard Deployment.](#dashboard-deployment).
{{% /expand%}}

### Step 3 Dashboard Deployment
{{%expand "Click to expand" %}}

We've created a sample dashboard to get you started. We'll use the [**cid-cmd**](https://github.com/aws-samples/aws-cudos-framework-deployment#demo) utility to set this up. You'll need to run a few commands, but it's much quicker than other methods.

1. In the AWS Console, browse to the **AWS CloudShell** service.
![Images/cidazure-setup-dashboard-cloudshell](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-cloudshell.png?width=1000px)

2. Wait until you see a command prompt. Install the latest pip package by running the following command: `/usr/bin/python3 -m pip install --upgrade pip`
![Images/cidazure-setup-dashboard-installpip](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-installpip.png?width=700px)

3. Install cid-cmd using the following command: `pip3 install -U git+https://github.com/aws-samples/aws-cudos-framework-deployment.git@prefixes`
![Images/cidazure-setup-dashboard-cidcmdinstall](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-cidcmdinstall.png?width=1000px)

4. Run `aws s3 cp s3://SOURCEBUCKET/cid-azure-dashboard.yaml cid-azure-dashboard.yaml`. This will copy the QuickSight Dashboard template to CloudShell. Replace **SOURCEBUCKET**. This is the name of the bucket you uploaded the source files to way back at the start of the lab. In the screenshot below our bucket is called *awscidforazuresourcecode*
![Images/cidazure-setup-dashboard-s3tocloudshell](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-s3tocloudshell.png?width=1000px)

5. We've created the next command for you. Let's retrieve it. Browse to **AWS Systems Manager**
![Images/cidazure-setup-dashboard-ssmbrowse](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-ssmbrowse.png?width=1000px)

6. Click **Parameter Store**
![Images/cidazure-setup-dashboard-ssmparameter](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-ssmparameter.png?width=1000px)

7. Click into the *cidazure-deploy_dashboard_command* parameter and copy the value.
![Images/cidazure-setup-dashboard-ssmparametercopy](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-ssmparametercopy.png?width=1000px)

8. Paste the command into AWS CloudShell and press Enter.
![Images/cidazure-setup-dashboard-cidcmdrunssm](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-cidcmdrunssm.png?width=1000px)

9. Choose if you would like to share the dashboard with everyone in your QuickSight account and press enter. If all goes well you will receive the prestigious **Congratulations!** message.

10. The cid-cmd command created an Athena view, a QuickSight dataset and a QuickSight dashboard.
| | | |
|-|-|-|
|![Images/cidazure-setup-dashboard-athenaview](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-athenaview.png?width=300px)|![Images/cidazure-setup-dashboard-qsdataset](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-qsdataset.png?width=300px)|![Images/cidazure-setup-dashboard-qsdashboard](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-qsdashboard.png?width=300px)|
| | | |
|-|-|-|

11. Browse to Amazon **QuickSight**.
![images/cidazure-setup-dashboard-browseqs](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-browseqs.png?width=1000px)

12. Click on **Dashboards** and click into the new Azure Cost Dashboard. If the Spice dataset is updating, you won't see any data yet. Ignore this for now.
![cidazure-setup-dashboard-appears](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-appears.png?width=600px)

13. Click the **Share** icon on the top right. Then click **Share dashboard**.
![cidazure-setup-dashboard-share](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-share.png?width=1000px)

14. Toggle the **save as** switch to on, confirm the action and click **Go back to Azure Cost Dashboard**.
![cidazure-setup-dashboard-saveas](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-saveas.png?width=1000px)

15. Refresh the web browser and you should see a new *save as* icon appear, It looks like a floppy disk (if you remember those).
![cidazure-setup-dashboard-floppy](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-floppy.png?width=1000px)

16. Click to save the dashboard as a QuickSight analysis. Give it a name and click **SAVE**.
![cidazure-setup-dashboard-saveanalysis](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-saveanalysis.png?width=700px)

Nice work, you've completed the setup :). Check out the [Common Tasks](../3_common_tasks)section to customize the solution.
![cidazure-setup-dashboard-finish](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-finish.png?width=1000px)


{{% /expand%}}

### Parameters
{{%expand "Click to expand" %}}
Parameters allow us to configure the deployment. Each parameter has a description that tells you what it does and a default to give you an example of the type of value it needs. Where you configure parameters will depend on the deployment option you choose;

|CloudFormation parameters are configured in the AWS console|Terraform parameters are set in the *terraform.tfvars* file|
|-|-|
|![Images/cidazure-setup-cfn-parameters-cfn](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-parameters-cfn.png?width=600px)|![Images/cidazure-setup-cfn-parameters-tf](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-parameters-tf.png?width=600px)|

There are no constraints set on parameters. Some Parameters only exist for specific deployment types. The following table provides additional information for each parameter.**Parameters will differ from the screenshot above. Use the table below for the most up-to-date settings**.

|Parameter|Section|Deployment|Guidance|
|-|-|-|-|
|SourceBucket|Common Settings|CloudFormation|Key name of the Amazon S3 bucket containing installation files .e.g. azure-arm-identity.zip, azure-arm-storage.zip, cid-azure-gluejob-cfn.py, cid-azure-lambda0x.zip files, cid-azure-stack.yaml. The deployment will fail if it cannot find source files.  |
|CustomerCode|Common Settings|CloudFormation, Terraform|Used to prefix all resource names. If you set the value to **star**, the  following resources will be created **star**lmdpdcidazurelambda01 for Lambda01 and **star**gljpdcidazure for the AWS Glue job, etc. You can't set this to **aws** :_(
|EnvironmentCode|Common Settings|CloudFormation, Terraform|Used within the name of resources. This is useful when you need to deploy the solution to multiple environments. If you set the value to **dev**,  resources will be named starlmd**dev**cidazurelambda01 for Lambda01 and starglj**dev**cidazure for the AWS Glue job, etc.|
|CustomerTag|Common Settings|CloudFormation, Terraform|All resources that can be tagged are tagged. Tagging adds descriptive metadata to each resource. This tag helps us identify the resources by department, e.g. finops, devops, IT shared services, etc.|
|EnvironmentTag|Common Settings|CloudFormation, Terraform|All resources that can be tagged are tagged. Tagging adds descriptive metadata to each resource. This tag helps us identify resources by environment, e.g. production, development, testing, etc. |
|Account Type|Microsoft Azure Settings|CloudFormation, Terraform|The type of agreement you have with Microsoft. Each agreement has a different data schema. Check [this](https://learn.microsoft.com/en-us/azure/cost-management-billing/automate/understand-usage-details-fields#list-of-fields-and-descriptions)link for details. To quickly determine your account type open an Azure cost export CSV file. If you have a *CostinBillingCurrency* column choose **MCA**. If you have a *Cost* column then go for **EA**, if you find a *PreTaxCost* column then select **PTAX**.|
|AzureBlobURL|Microsoft Azure Settings|CloudFormation, Terraform|Lambda functions query the Azure blob endpoint URL specified here. You can get the URL from the Azure portal Storage Account >  Settings Section > Endpoints > Blob service|
|AzureApplicationID|Microsoft Azure Settings|CloudFormation, Terraform|Used by Lambda functions to request an OAUTH token. Get the Application ID from the Azure portal, refer to instructions [here.](https://learn.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#sign-in-to-the-application)|
|AzureTenantID|Microsoft Azure Settings|CloudFormation, Terraform|Used by Lambda functions to request an OAUTH token. Get the Tenant ID from the Azure portal, refer to instructions [here.](https://learn.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#sign-in-to-the-application)|
|AzureSecretKey|Microsoft Azure Settings|CloudFormation, Terraform|Used by Lambda functions to request an OAUTH token. Get the value of the Application secret from the Azure portal, refer to instructions [here.](https://learn.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#option-2-create-a-new-application-secret)|
|AzureDateFormat|Microsoft Azure Settings|CloudFormation, Terraform|The date format used within the Azure cost export. To quickly determine this, open an Azure cost export CSV file and look for the date format.|
|AzureFolderPath|Microsoft Azure Settings|CloudFormation, Terraform|The path to the Azure cost export folder. We don't need the storage account name or the container name. **This field is case sensitive**. This is super important!|
|AzureTags|Microsoft Azure Settings|CloudFormation, Terraform|Enter Azure tag names to expose them as data fields in the QuickSight dashboard. Each value will add an additional column to the transformed dataset.
|AzureCopySchedule|Data Copy Settings|CloudFormation, Terraform|Used to schedule the Azure data pull. It uses a Cron expression, more information [here.](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html)|
|GlueCopySchedule|Data Copy Settings|CloudFormation, Terraform|Used to schedule the AWS Glue job which transforms data allowing it to be queried by Athena. It uses a Cron expression, more information [here.](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html). The AWS Glue transform should be scheduled to run after the Azure data pull completes.|
|BlobToS3SyncStartDate|Data Copy Settings|CloudFormation, Terraform|Used to define what Azure files are copied across for initial processing. Setting this value to 20200201 Will copy all files modified on and after February 1st 2020. Typically we want to copy all files on the first run, so set this to the date of the moon landing or something similar.|
|PartitionSize|Advanced Settings|CloudFormation, Terraform|Experimental. **Do not use**.|
|MaxPartitionsPerFile|Advanced Settings|CloudFormation, Terraform|Experimental. **Do not use**.|
|UseFullFilePath|Advanced Settings|CloudFormation, Terraform|Experimental. **Do not use**.|
|Region|Regions and Availability Zones|Terraform|Sets the AWS region that AWS resources are deployed to.|
{{% /expand%}}

Once you've completed this section you will have a fully functioning solution.

{{< prev_next_button link_prev_url="../1_solution_design/" link_next_url="../3_common_tasks/" />}}