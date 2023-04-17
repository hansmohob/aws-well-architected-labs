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

Deployment is a three step process. 

1. Deploy AWS services
2. Perform a manual run to copy the first batch of data
3. Setup QuickSight and import an example dashboard. 

There are 2 **(Options)** to deploy AWS Services. Pick the one that suits you.

1. [**AWS CloudFormation**](#step-1-option-1-cloudformation-deployment). We'll guide you through the setup step by step. You will need to setup Azure resources prior to deployment.
2. [**Hashicorp Terraform**](#step-1-option-2-terraform-deployment). You will need to be familiar with Terraform and have an existing deployment pipeline. We include an example template showing you how to deploy Azure resources.

{{% notice note %}}
If you encounter any errors with the tasks below, head over to the [Observability and Troubleshooting](../3_common_tasks)section.
{{% /notice %}}

### Step 1 (Option-1) CloudFormation Deployment
{{%expand "Click to expand" %}}

1. [Download](https://github.com/aws-samples/aws-data-pipelines-for-azure-storage/archive/refs/heads/main.zip) setup files and extract locally. The archive contains files listed below. **You don't need to extract individual files**.

* **azure-arm-identity.zip** Lambda layer for Azure identity
* **azure-arm-storage.zip** Lambda layer for Azure storage
* **cid-azure-gluejob-cfn.py** Glue python script
* **cid-azure-lambda01.zip - cid-azure-lambda06.zip** Azure blob copy Lambda functions
* **cid-azure-lambda07.zip** Impact tracking Lambda function
* **cid-azure-stack.yaml** CloudFormation template.
* **cid-azure-dashboard.yaml** Sample QuickSight dashboard

2. Place the files in a new Amazon S3 bucket. CloudFormation will access these files during deployment. Don't use the name in the screenshot below, someone's probably already grabbed it and S3 bucket names need to be globally unique.
![Images/cidazure-setup-cfn-s3source](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-s3source.png?width=1000px)

3. Click into **cid-azure-stack-yaml** and grab the Object URL. You'll need this a moment.
![Images/cidazure-setup-cfn-s3objecturl](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-s3objecturl.png?width=1000px)

4. Browse to the **CloudFormation** service.
![Images/cidazure-setup-cfn-browse](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-browse.png?width=1000px)

5. Click **Create stack** and choose **With new resources (standard)**.
![Images/cidazure-setup-cfn-createstack](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-createstack.png?width=1000px)
 
6. Paste in the Object URL you grabbed earlier into the **Amazon S3 URL** field. Click **Next**
![Images/cidazure-setup-cfn-template](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-template.png?width=1000px)

7. Give the stack a name. An S3 bucket will be created by the stack and start with the name you enter here.
![Images/cidazure-setup-cfn-stackname](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-stackname.png?width=1000px)

8. Let's set values for our parameters. Fill in the **Common Settings** section. Refer to the [Parameters](#parameters) section for guidance.

9. Fill in the **Microsoft Azure Settings** section. Refer to the [Parameters](#parameters) section for guidance.

10. If you're having trouble identifying what you need for the **AzureFolderPath** parameter, here's an example. In the screenshot below we would enter directory/* Also note **this field is case sensitive** ![Images/cidazure-setup-cfn-azurefolder.png](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-azurefolder.png?width=400px)

11. Fill in the **Data Copy Settings** section. Refer to the [Parameters](#parameters) section for guidance.

12. Leave the **Advanced Settings** as default and click **Next**

13. Configure Stack options. If you require a specific IAM role to deploy the template or need to add additional tags, you can do this here. Otherwise leave the settings as default. When you're ready click **Next**
![Images/cidazure-setup-cfn-stackoptions](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-stackoptions.png?width=1000px)

14. Review the Cloudformation stack settings, check the box to **acknowledge that AWS CloudFormation might create IAM resources with custom names** and click **Submit**
![Images/cidazure-setup-cfn-stackreview](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-stackreview.png?width=1000px)

15. The CloudFormation stack will start to deploy. Click the **refresh** button to view progress. If you see a red status event, then something went wrong. Review the error, correct and try again.
![Images/cidazure-setup-cfn-stackprogress](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-stackprogress.png?width=1000px)

16. The deployment will run for a few minutes. Once deployment is complete, you'll see a *CREATE_COMPLETE* message.
![Images/cidazure-setup-cfn-stackcomplete](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-stackcomplete.png?width=1000px)

Great job, you've completed the setup! Now move onto [Manual Run](#step-2-initial-manual-run)
{{% /expand%}}

### Step 1 (Option-2) Terraform Deployment
{{%expand "Click to expand" %}}

1. [Download](https://github.com/aws-samples/aws-data-pipelines-for-azure-storage/archive/refs/heads/main.zip) setup files and extract locally. The archive contains files listed below. **You don't need to extract individual files**.

* **azure-arm-identity.zip** Lambda layer for Azure identity
* **azure-arm-storage.zip** Lambda layer for Azure Storage
* **cid-azure-gluejob-cfn.py** Glue python script
* **cid-azure-lambda01.zip - cid-azure-lambda06.zip** Lambda function code. 
* **cid-azure-stack.yaml** CloudFormation template.
* **cid-azure-dashboard.yaml** Sample QuickSight dashboard

Great job, you've completed the setup! Now move onto [Manual Run](#step-2-initial-manual-run)

{{% /expand%}}

### Step 2 Initial Manual Run
{{%expand "Click to expand" %}}
The solution runs automatically at the scheduled times you set in your deployment parameters. When you first deploy you may want to visualize your data straight away. If you've had enough for today and want to stop, feel free to come back tomorrow, this part should complete automatically.

As part of the deployment we created a Resource Group to organize our resources into a single view. This will help you identify resources that were created should you get stuck. To view the Resource Group, browse to the **AWS Resource Groups & Tag Editor** service.
![Images/cidazure-setup-manual-resourcegroup](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-manual-resourcegroup.png?width=600px)

Follow the instructions below to start a manual run.

1. Browse to the **AWS Lambda** service.
![Images/cidazure-setup-manual-lambdabrowse](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-manual-lambdabrowse.png?width=1000px)

2. Search for the Lambda Functions created by your deployment. In our example we used *cid* as the customer code and the resource id is *lmd*
![Images/cidazure-setup-manual-lambdafind](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-manual-lambdafind.png?width=1000px)

3. Click into **Lambda01**. Notice the python code is viewable here. Take a look around if you have time.
![Images/cidazure-setup-manual-lambda01](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-manual-lambda01.png?width=1000px)

4. Select the **Test tab** and click the **Test** button.
![Images/cidazure-setup-manual-lambda01test](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-manual-lambda01test.png?width=1000px)

5. You should receive an *Execution result: succeeded* message. 
![Images/cidazure-setup-manual-lambdasuccess](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-manual-lambdasuccess.png?width=1000px)

6. Quickly head over to the **Amazon S3 bucket** created by your deployment. If you deployed via CloudFormation then your bucket will start with the stack name. If you deployed with Terraform the bucket will start with customer code, followed by *sss* as the resource ID. You should see files are already starting to appear under the *azurecidraw* folder.
![Images/cidazure-setup-manual-s3appear](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-manual-s3appear.png?width=1000px)

7. Now let's start the Glue job to transform the data. Head over to **AWS Glue** and click into **ETL jobs**
![Images/cidazure-setup-manual-glue](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-manual-glue.png?width=1000px)

8. Select the Glue job created by your deployment. In our example we used *cid* as the customer code and the resource id is *glj*
![Images/cidazure-setup-manual-gluejob](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-manual-gluejob.png?width=1000px)

9. Take a look at the Glue script if you have time, you can make changes here to suit your needs. Click on the **Runs tab** and click the **Run** button. Your Glue job will execute.
![Images/cidazure-setups-manual-gluestart](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-manual-gluestart.png?width=1000px)

10. When the job completes the run status will change to *success*.
![Images/cidazure-setup-manual-gluesuccess](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-manual-gluesuccess.png?width=1000px)

11. Now let's head back over to the same **Amazon S3 bucket** you accessed earlier. Two new folder should have appeared. *azurecidparquet* contains parquet versions of the original CSV files. The orginal CSV files have moved to the *azurecidprocessed* folder and the *azurecidraw* folder has been deleted.
![Images/cidazure-setup-manual-s3processed](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-manual-s3processed.png?width=1000px)

12. If you click into the *azurecidprocessed* folder you'll see data is partitioned by month.
![Images/cidazure-setup-manual-s3parquet](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-manual-s3parquet.png?width=1000px)

Awesome! You've completed the manual run, let's move onto [Dashboard Deployment.](#dashboard-deployment)
{{% /expand%}}

### Step 3 Dashboard Deployment
{{%expand "Click to expand" %}}

First up we need to create our Amazon Athena view. 

1. Browse to the **Amazon Athena** service.
![Images/cidazure-setup-dashboard-athenabrowse](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-athenabrowse.png?width=1000px)

2. Select the **Athena Workgroup** and **Database** created by your deployment. In our example we used *cid* as the customer code.
![Images/cidazure-setup-dashboard-athenaworkgroup](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-athenaworkgroup.png?width=1000px)

3. Click the **Saved queries** tab and click the ID for the saved query created by your deployment.
![Images/cidazure-setup-dashboard-athenaquery](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-athenaquery.png?width=1000px)

4. Click the **Run** button to run the query. Once complete a new Athena view will be created.
![Images/cidazure-setup-dashboard-athenarun](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-athenarun.png?width=1000px)

Next, let's create our QuickSight dataset.

5. Head over to the **QuickSight** service and select **Datasets**. Click the **New dataset** button
![Images/cidazure-setup-dashboard-qsnewdataset](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-qsnewdataset.png?width=1000px)

6. Select **Athena** as the Data Source
![Images/cidazure-setup-dashboard-qsselectathena](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-qsselectathena.png?width=1000px)

7. Give the Data source a name, select the Athena workgroup created by your deployment and click **Create data source**.
![Images/cidazure-setup-dashboard-qscreatedata](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-qscreatedata.png?width=500px)

8. Select the Glue database created by your deployment and relevant table which will end in *athena_view*. Then click **Edit/Preview data**
![Images/cidazure-setup-dashboard-qschoosetable](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-qschoosetable.png?width=500px)

9. Check your data appears along with any tag columns you specified. Once done, set the **Query mode to SPICE** and click **save & publish button**. We use Spice to minimize the loading times for visuals.

![Images/cidazure-setup-dashboard-qssave](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-qssave.png?width=1000px)

Let's set a daily refresh for Spice to make sure you have the most up-to-date visualization.

10. Click the QuickSight icon in the top left to get back to the main menu. Select **Datasets** 
![Images/cidazure-setup-dashboard-qsstartspice](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-qsstartspice.png?width=1000px)

11. Click the dataset you just created, then click the **Refresh** tab. 
![Images/cidazure-setup-dashboard-qsspicerefresh](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-qsspicerefresh.png?width=1000px)

12. Click the **ADD NEW SCHEDULE** button, Select **Full refresh** and click **SAVE**
![Images/cidazure-setup-dashboard-qsspiceschedule](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-qsspiceschedule.png?width=500px)

OK, nice, we've got data, but need visuals! We've created a sample dashboard to get you started. We'll use the [**cid-cmd**](https://github.com/aws-samples/aws-cudos-framework-deployment#demo) utility to import a template into your account. You'll need to run a few commands, but it's much quicker than other methods.

13. Head back to the AWS Console, browse to the **AWS CloudShell** service.
![Images/cidazure-setup-dashboard-cloudshell](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-cloudshell.png?width=1000px)

14. Wait until you see a command prompt. Install the latest pip package by running the following command: `python3 -m ensurepip --upgrade`

15. Install cid-cmd using the following command: `pip3 install -U git+https://github.com/aws-samples/aws-cudos-framework-deployment.git@export-views`
![Images/cidazure-setup-dashboard-cidcmdinstall](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-cidcmdinstall.png?width=1000px)

16. Run `aws s3 cp s3://SOURCEBUCKET/CloudIntelligenceDashboardforAzure.yaml  /tmp/CloudIntelligenceDashboardforAzure.yaml`. This will copy the QuickSight Dashboard template to CloudShell. Replace **SOURCEBUCKET** with the name of your Source Bucket. This is the name of the bucket you uploaded the source files to way back at the start of the lab. In the screenshot below our bucket is called *awscidforazuresourcecode*
![Images/cidazure-setup-dashboard-s3tocloudshell](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-s3tocloudshell.png?width=1000px)

17. Start the import process by running `cid-cmd deploy --resources /tmp/CloudIntelligenceDashboardforAzure.yaml`. Use the arrow keys on your keyboard to select *cloudintelligencedashboardforazure* from the dashboard selection list.
![Images/cidazure-setup-dashboard-cidcmd2](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-cidcmd2.png?width=1000px)

18. Select the **Athena WorkGroup** created by your deployment. In our example we used *cid* as the customer code and the resource id is *atw*
![Images/cidazure-setup-dashboard-cidcmd3](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-cidcmd3.png?width=600px)

19. Select the **Glue database** created by your deployment. In our example we used *cid* as the customer code and the resource id is *gld*
![Images/cidazure-setup-dashboard-cidcmd4](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-cidcmd4.png?width=600px)

20. If all goes well you will receive the prestigious **Congratulations!** message. You can choose to share the dashboard with everyone in your QuickSight account or not.
![Images/cidazure-setup-dashboard-cidcmd5](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-cidcmd5.png?width=600px)

Great we have a Dashboard, but we cant edit this and it's got no data. Lets save it as a QuickSight Analysis and change the dataset. 

21.  Headback to QuickSight and click on Dashboards. You should see the Cloud Intelligence Dashboard for Azure. 
![cidazure-setup-dashboard-appears](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-appears.png?width=600px)

22. Click on it to load. There is no data at present because it is connected to the wrong QuickSight dataset. Click the **Share** icon on the top right. Then click **Share dashboard**
![cidazure-setup-dashboard-share](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-share.png?width=1000px)

23. Toggle the **save as** switch to om and click **Go back to CloudIntelligenceDashboardforAzure**
![cidazure-setup-dashboard-saveas](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-saveas.png?width=1000px)

24. Refresh your web browser and you should see a new *save as* icon appear, It looks like a floppy disk (if you remember those). 
![cidazure-setup-dashboard-floppy](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-floppy.png?width=1000px)

25. Click to save the dashboard as a QuickSight analysis. Give it a name and click **SAVE**
![cidazure-setup-dashboard-saveanalysis](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-saveanalysis.png?width=600px)

26. Click the **pencil icon** next to Dataset
![cidazure-setup-dashboard-pencil](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-pencil.png?width=600px)

27. Click the resulting dialogu box, click the 3 dots and **Replace**
![cidazure-setup-dashboard-pencil](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-pencil.png?width=600px)

28. Choose your Athena view and click **Select**
![cidazure-setup-dashboard-replace](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-replace.png?width=600px)

29. At this point you may need to do some field matching. We built the analysis using the MCA data schema. If you have an Enterprise Agreement map **CostinBillingCurrency** to **Cost**.

30. Once you're done click the Update field mapping button, close the dataset dialogue box and marvel at your new analysis!

Just a few things to tidy up before we finish...

31. Head back to Datasets and delete the Template dataset.

32. Open Athena and delete the Template athena view.

Nice work, you've completed the setup :). Check out the [Common Tasks](../3_common_tasks)section to customize your solution.

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
|SourceBucket|Common Settings|CloudFormation|Key name of the S3 bucket containing installation files .e.g. azure-arm-identity.zip, azure-arm-storage.zip, cid-azure-gluejob-cfn.py, cid-azure-lambda0x.zip files, cid-azure-stack.yaml. The deployment will fail if it cannot find source files.  |
|CustomerCode|Common Settings|CloudFormation, Terraform|Used to prefix all resource names. If you set the value to **star**, the  following resources will be created **star**lmdpdcidazurelambda01 for Lambda01 and **star**gljpdcidazure for the Glue job, etc. You can't set this to **aws** :_(
|EnvironmentCode|Common Settings|CloudFormation, Terraform|Used within the name of resources. This is useful when you need to deploy the solution to multiple environments. If you set the value to **dev**,  resources will be named starlmd**dev**cidazurelambda01 for Lambda01 and starglj**dev**cidazure for the Glue job, etc.|
|CustomerTag|Common Settings|CloudFormation, Terraform|All resources that can be tagged are tagged. Tagging adds descriptive metadata to each resource. This tag helps us identify the resources by department, e.g. finops, devops, IT shared services, etc.|
|EnvironmentTag|Common Settings|CloudFormation, Terraform|All resources that can be tagged are tagged. Tagging adds descriptive metadata to each resource. This tag helps us identify resources by environment, e.g. production, development, testing, etc. |
|Account Type|Microsoft Azure Settings|CloudFormation, Terraform|The type of agreement you have with Microsoft. Each agreement has a different data schema. Check [this](https://learn.microsoft.com/en-us/azure/cost-management-billing/automate/understand-usage-details-fields#list-of-fields-and-descriptions)link for details. To quickly determine your account type open an Azure cost export CSV file. If you have a *CostinBillingCurrency* column choose **MCA**. If you have a *Cost* column then go for **EA**, if you find a *PreTaxCost* column then select **PTAX**.|
|AzureBlobURL|Microsoft Azure Settings|CloudFormation, Terraform|Lambda functions query the Azure blob endpoint URL specified here. You can get the URL from the Azure portal Storage Account >  Settings Section > Endpoints > Blob service|
|AzureApplicationID|Microsoft Azure Settings|CloudFormation, Terraform|Used by Lambda functions to request an OAUTH token. Get the Application ID from the Azure portal, refer to instructions [here.](https://learn.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#sign-in-to-the-application)|
|AzureTenantID|Microsoft Azure Settings|CloudFormation, Terraform|Used by Lambda functions to request an OAUTH token. Get the Tenant ID from the Azure portal, refer to instructions [here.](https://learn.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#sign-in-to-the-application)|
|AzureSecretKey|Microsoft Azure Settings|CloudFormation, Terraform|Used by Lambda functions to request an OAUTH token. Get the value of the Application secret from the Azure portal, refer to instructions [here.](https://learn.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#option-2-create-a-new-application-secret)|
|AzureDateFormat|Microsoft Azure Settings|CloudFormation, Terraform|The date format used within your Azure cost export. To quickly determine this, open an Azure cost export CSV file and look for the date format.|
|AzureFolderPath|Microsoft Azure Settings|CloudFormation, Terraform|The path to your Azure cost export folder. We don't need the storage account name or the container name. **This field is case sensitive**. This is super important!|
|AzureTags|Microsoft Azure Settings|CloudFormation, Terraform|Enter Azure tag names to expose them as data fields in the QuickSight dashboard. Each value will add an additional column to the transformed dataset.
|AzureCopySchedule|Data Copy Settings|CloudFormation, Terraform|Used to schedule the Azure data pull. It uses a Cron expression, more information [here.](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html)|
|GlueCopySchedule|Data Copy Settings|CloudFormation, Terraform|Used to schedule the Glue job which transforms data allowing it to be queried by Athena. It uses a Cron expression, more information [here.](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html). The Glue transform should be scheduled to run after the Azure data pull completes.|
|BlobToS3SyncStartDate|Data Copy Settings|CloudFormation, Terraform|Used to define what Azure files are copied across for initial processing. Setting this value to 20200201 Will copy all files modified on and after February 1st 2020. Typically we want to copy all files on the first run, so set this to the date of the moon landing or something similar.|
|PartitionSize|Advanced Settings|CloudFormation, Terraform|Experimental. **Do not use**.|
|MaxPartitionsPerFile|Advanced Settings|CloudFormation, Terraform|Experimental. **Do not use**.|
|UseFullFilePath|Advanced Settings|CloudFormation, Terraform|Experimental. **Do not use**.|
|Region|Regions and Availability Zones|Terraform|Sets the AWS region that AWS resources are deployed to.|

{{% /expand%}}

{{< prev_next_button link_prev_url="../1_solution_design/" link_next_url="../3_common_tasks/" />}}