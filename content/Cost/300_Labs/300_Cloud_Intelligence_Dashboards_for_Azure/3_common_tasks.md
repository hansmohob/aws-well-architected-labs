---
title: "Cloud Intelligence Dashboards for Azure - Common Tasks"
date: 2020-08-24T11:16:08-04:00
chapter: false
weight: 30
hidden: false
---

Now that you've deployed the solution let's perform some common tasks. 

### Creating new visuals and filters
{{%expand "Click to expand" %}}
No doubt you'll want to create your own awesome visuals. This is easy to achieve with Amazon QuickSight. Let's create a new visual based on an Azure tag. In our Example, we want to see environment spend over time.

1. Head over to the **QuickSight** service and open your Cloud Intelligence Dashboards for Azure analysis.
![Images/cidazure-common-visual-quicksight](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-visual-quicksight.png?width=500px)

2. Click on the *Detail* sheet.
![Images/cidazure-common-visual-details](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-visual-details.png?width=1000px)

3. On the top left, click on **ADD** and **Add visual**.
![Images/cidazure-common-visual-addvisual](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-visual-addvisual.png?width=500px)

4. Select a visual type at the bottom left. Let's go with a *Vertical bar chart*.
![Images/cidazure-common-visual-barchart](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-visual-barchart.png?width=500px)

5. Drag *dateparsed* into the **X axis** and *costinbillingcurrency* into **Value**.
![Images/cidazure-common-visual-fieldwell](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-visual-fieldwell.png?width=1000px)

6. Scroll down through the fields list on the left hand side. You'll notice a data field called *tags* this is the original column, in JSON format, from the Azure cost export. If you specified Azure tags during deployment you'll see additional data fields that start with *tag-*
![Images/cidazure-common-visual-tagfields](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-visual-tagfields.png?width=300px)

7. In our example we're going to select *tag-environment* and drag it into the **Group/Color** field well. 
![Images/cidazure-common-visual-envtag](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-visual-envtag.png?width=1000px)

8. Nice, we have a visual, but you can see a lot of our resources are showing as *null* because they are not tagged. Let's create a filter to exclude these. 

Tagging resources helps us drive accountability. It's an important part of cloud cost optimization. Read more [here.](https://docs.aws.amazon.com/whitepapers/latest/cost-optimization-laying-the-foundation/tagging.html?ref=wellarchitected)

9. Select the new visual and right click on *null* in the legend.
![Images/cidazure-common-visual-filter](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-visual-filter.png?width=500px)

10. Click **Exclude null**. This will create a filter for the field tag-environment which excludes any null values and is applied to only this visual. 
![Images/cidazure-common-visual-configurefilter](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-visual-configurefilter.png?width=500px)

11. The visual will update as below. Looks like our developers have been busy over the past few days!
![cidazure-common-visual-filteredvisual](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-visual-filteredvisual.png?width=500px)

Finally, let's create a filter to change the view on all visuals. We'll stick with *tag-environment*

12. Click on any visual, click on the **Filter** icon and **Add Filter**. Select the *tag-environment field* field. 
![cidazure-common-visual-allfilter](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-visual-allfilter.png?width=1000px)

13. Edit the filter by just clicking on it or by choosing **Edit** from the 3 dots menu. In the *Applied to* box, select **All applicable visuals**. Leave the other settings as default and click **Apply**.
![Images/cidazure-common-visual-allfilter2](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-visual-allfilter2.png?width=500px)

14. Close the filter blade by clicking the back arrow next to the Edit filter heading. Click the 3 dots and select **Pin to top**
![Images/cidazure-common-visual-pinfilter](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-visual-pinfilter.png?width=500px)

15. Now we can filter all visuals on this sheet by environment tag.
![Images/cidazure-common-visual-filterfinish](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-visual-filterfinish.png?width=1000px)

Have a go at creating your own visuals. If you need help understanding the data fields, Microsoft provide documentation [here.](https://learn.microsoft.com/en-us/azure/cost-management-billing/automate/understand-usage-details-fields#list-of-fields-and-descriptions)

{{% /expand%}}

### Changing scheduled times
{{%expand "Click to expand" %}}

The solution runs automatically at the scheduled times you set during deployment. There are two timers. One for the copy of data (Amazon EventBridge) and another for the processing of data (AWS Glue). While it's possible to change the timers for each service, we do not advise you do this.

| | |
|-|-|
|![Images/cidazure-common-time-glue](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-time-glue.png?width=500px)|![Images/cidazure-common-time-eventbridge](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-time-eventbridge.png?width=500px)|

| | |
|-|-|

Instead, we encourage you to update the deployment parameters. This will record settings, making it easier to apply solution updates.

If you need to change scheduled times follow the instructions below. 

#### CloudFormation deployment

1. Browse to the **CloudFormation** service.
![Images/cidazure-setup-cfn-browse](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-browse.png?width=1000px)

2. Select the deployment stack and Click **Update**.
![Images/cidazure-common-time-updatestack](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-time-updatestack.png?width=1000px)

3. Select **Use current template** and click **Next**
![Images/cidazure-common-time-selecttemplate](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-time-selecttemplate.png?width=1000px)

4. Browse to **Data Copy Settings**, change *AzureCopySchedule* and *GlueCopySchedule* fields, then click **Next**
![Images/cidazure-common-time-datasettings](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-time-datasettings.png?width=1000px)

5. On the *Configure stack options* page, click **Next**

6. Review the CloudFormation stack settings, check the box to acknowledge that AWS CloudFormation might create IAM resources with custom names and click **Submit**
![Images/cidazure-setup-cfn-stackreview](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-stackreview.png?width=1000px)

7. The CloudFormation stack will start to update. Click the refresh button to view progress.

8. The update will run for a few minutes. When it’s finished, you’ll see an UPDATE_COMPLETE message.
![Images/cidazure-common-time-updatedone](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-time-updatedone.png?width=1000px)

Scheduled times have now been updated.

#### Terraform deployment

1. Update the *Data Copy Settings* section of terraform.tfvars
![Images/cidazure-common-time-tfvars](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-time-tfvars.png?width=1000px)

2. Run `terraform apply` review the plan and confirm the action.

Scheduled times have now been updated.

{{% /expand%}}

### Adding additional tags
{{%expand "Click to expand" %}}
Use the instructions below to surface new Azure tags to the Amazon QuickSight dashboard. While it's possible to change tags directly in the console, we do not advise you do this.

| | |
|-|-|
|**CloudFormation**|**Terraform**|
|![Images/cidazure-common-tags-ssm](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-tags-ssm.png?width=500px)|![Images/cidazure-common-tags-gluejob](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-tags-gluejob.png?width=500px)|

| | |
|-|-|

Instead, we encourage you to update the deployment parameters. This will record your settings, making it easier to apply solution updates.

#### CloudFormation deployment

1. Browse to the **CloudFormation** service.
![Images/cidazure-setup-cfn-browse](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-browse.png?width=1000px)

2. Select the deployment stack and Click **Update**.
![Images/cidazure-common-time-updatestack](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-time-updatestack.png?width=1000px)

3. Select **Use current template** and click **Next**
![Images/cidazure-common-time-selecttemplate](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-time-selecttemplate.png?width=1000px)

4. Browse to **Microsoft Azure Settings**, change the *AzureTags* field and click **Next**
![Images/cidazure-common-tags-AzureTags](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-tags-AzureTags.png?width=1000px)

5. On the *Configure stack options* page, click **Next**

6. Review the CloudFormation stack settings, check the box to acknowledge that AWS CloudFormation might create IAM resources with custom names and click **Submit**
![Images/cidazure-setup-cfn-stackreview](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-stackreview.png?width=1000px)

7. The CloudFormation stack will start to update. Click the refresh button to view progress.

8. The update will run for a few minutes. When it’s finished, you’ll see an UPDATE_COMPLETE message.
![Images/cidazure-common-time-updatedone](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-time-updatedone.png?width=1000px)

Newly processed data can now be filtered using the specified tags. **New tags are not appended to data that has already been processed**. To apply tags to existing data, all data needs to be reprocessed refer to the [Re-processing data](#re-processing-data) Section.

#### Terraform deployment

1. Update the *Microsoft Azure Settings* section of terraform.tfvars
![Images/cidazure-common-tags-tfvars](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-tags-tfvars.png?width=1000px)

2. Run `terraform apply` review the plan and confirm the action.

Newly processed data can now be filtered using the specified tags. **New tags are not appended to data that has already been processed**. To apply tags to existing data, all data needs to be reprocessed refer to the [Re-processing data](#re-processing-data) Section.

{{% /expand%}}

### Disabling the data copy process
{{%expand "Click to expand" %}}

There may be a time when you want to disable the Azure blob copy process. Perhaps there is a problem with the Azure cost export or you have an issue with the Cloud Intelligence Dashboard. Disabling the copy process temporarily stops the flow of data, giving you time to resolve issues without incurring Azure egress data charges.

1. Browse to AWS Secrets Manager and click on the Secret created by the deployment. In our example we used *cid* as the prefix code and the resource id is *sms*
![Images/cidazure-common-disable-secretsmanager](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-disable-secretsmanager.png?width=1000px)

2. Scroll down and click on the **Retrieve secret value** button.
![Images/cidazure-common-disable-secretsmanager](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-disable-value.png?width=1000px)

3. Click the **Edit** button.

4. Change the *isactive* value to **False**
![Images/cidazure-common-disable-secretsmanager](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-disable-edit.png?width=1000px)

5. Click **Save**

To re-enable the copy process, run through the steps above changing the *isactive* value to **True**. The solution will copy all data created after the *begindate* day and time.

{{% /expand%}}

### Re-processing data
{{%expand "Click to expand" %}}
If you've added new Azure tags or you're redeploying the solution, you may need to re-process data. To reprocess data we delete existing parquet files, move processed CSV files to the *azurecidraw* folder, set the bulk run parameter to true and run the AWS Glue job. See instructions below.

1. Browse to the **Amazon S3 bucket** created by the deployment. If you deployed using CloudFormation then the bucket will start with the stack name. If you used Terraform the bucket will start with the prefix code you specified, followed by *sss* as the resource ID.
![Images/cidazure-common-reprocess-s3browse](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-reprocess-s3browse.png?width=1000px)

2. Select the *azurecidparquet*  folder and click **Delete**

3. Confirm deletion and click **Delete objects**
![Images/cidazure-common-reprocess-s3delete](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-reprocess-s3delete.png?width=500px)

4. Locate the processed data. If you're modifying an existing deployment, the processed data will be in a folder called *azurecidprocessed*. If you're redeploying the solution you should have taken a copy of the processed data and moved it to another Amazon S3 bucket. Once you locate the files, **move** the folder structure to a new folder called *azurecidraw* within the same Amazon S3 Bucket.

| | |
|-|-|
|**Source**|**Target**|
|![Images/cidazure-common-reprocess-s3movesource](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-reprocess-s3movesource.png?width=800px)|![Images/cidazure-common-reprocess-s3movetarget](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-reprocess-s3movetarget.png?width=500px)|

| | |
|-|-|

5. When the move completes, head back to the Amazon S3 Bucket created by your deployment. You should have an *azurecidraw* folder. This folder will contain a folder structure and CSV files.
![Images/cidazure-common-reprocess-s3rawfolder](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-reprocess-s3rawfolder.png?width=1000px)

6. Browse to **Systems Manager**.
![Images/cidazure-setup-dashboard-ssmbrowse](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-ssmbrowse.png?width=1000px)

7. Click **Parameter Store**
![Images/cidazure-setup-dashboard-ssmparameter](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-ssmparameter.png?width=1000px)

8. Click into the *cidazure-var_bulk_run* parameter and click the **Edit** button.
![Images/cidazure-setup-dashboard-ssmparametercopy](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-reprocess-ssmedit.png?width=1000px)

9. Change the value to *true* and click **Save Changes**
![Images/cidazure-setup-dashboard-ssmparametercopy](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-reprocess-ssmsave.png?width=1000px)


10. You're ready to re-process the data by running the AWS Glue job. Open the **AWS Glue** service and click into **ETL jobs**
![Images/cidazure-setup-manual-glue](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-manual-glue.png?width=1000px)

Select the AWS Glue job created by the deployment. In our example we used *cid* as the prefix code and the resource id is *glj*
![Images/cidazure-setup-manual-gluejob](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-manual-gluejob.png?width=1000px)

11. Click on the **Runs tab** and click the **Run** button. The AWS Glue job will execute.
![Images/cidazure-setups-manual-gluestart](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-manual-gluestart.png?width=1000px)

12. When the job completes the run status will change to *success*.
![Images/cidazure-setup-manual-gluesuccess](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-manual-gluesuccess.png?width=1000px)

13. Now let's head back over to the same **Amazon S3 bucket** you accessed earlier. A *azurecidparquet* folder should appear containing parquet versions of the original CSV files. The original CSV files have moved to the *azurecidprocessed* folder and the *azurecidraw* folder has been deleted.
![Images/cidazure-setup-manual-s3processed](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-manual-s3processed.png?width=1000px)

That's it, you've reprocessed the data, [refresh your Spice DataSet](#refresh-spice-dataset) to view results.
{{% /expand%}}

### Refresh Spice dataset
{{%expand "Click to expand" %}}
To view any changes made to the dataset, simply refresh the Spice Dataset to update the dashboard.

1. Browse to Amazon **QuickSight**.
![images/cidazure-setup-dashboard-browseqs](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-browseqs.png?width=1000px)

2. Select **DataSets** from the menu. 
![cidazure-setup-dashboard-saveanalysis](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-datasettab.png?width=1000px)

3. Click your Cloud Intelligence Dashboard for Azure dataset and click on the **Refresh** tab.
![cidazure-setup-dashboard-saveanalysis](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-dashboard-spicerefreshbutton.png?width=1000px)

4. Click on the **Refresh Now** button to initiate the task. Once the task is executed, the status will change to *Completed* to indicate its successful completion.
{{% /expand%}}

### Manual pull of data after initial setup
{{%expand "Click to expand" %}}

At some point you may want to run a manual pull of data. This process is the same as [Step 2 Initial Manual Run](../2_setup/#step-2-initial-manual-run). You can control what data is copied by changing the *begindate* secret value.

1. Browse to AWS Secrets Manager and click on the Secret created by the deployment. In our example we used *cid* as the prefix code and the resource id is *sms*
![Images/cidazure-common-disable-secretsmanager](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-disable-secretsmanager.png?width=1000px)

2. Scroll down and click on the **Retrieve secret value** button.
![Images/cidazure-common-disable-secretsmanager](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-disable-value.png?width=1000px)

3. Click the **Edit** button.

4. Change the *begindate* value to the desired data and time. In the example below we will copy all data created after the moon landing.
![Images/cidazure-common-disable-secretsmanager](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-manual-edit.png?width=1000px)

5. Click **Save**

6. Now follow the [Step 2 Initial Manual Run](../2_setup/#step-2-initial-manual-run) process. Don't forget to [refresh your Spice DataSet](#refresh-spice-dataset) to view results in the dashboard.

{{% notice note %}}
The *begindate* value is automatically updated by Lambda functions each time data is copied. This ensures we only copy new data.
{{% /notice %}} 

{{% /expand%}}

### Observability and troubleshooting

{{%expand "Click to expand" %}}
#### CloudWatch Dashboard
As part of the deployment we built a basic Amazon CloudWatch dashboard. You can use this to gain insights into the solution and troubleshoot issues. To access the dashboard follow the instructions below.

1. Browse to the Amazon CloudWatch service.
![Images/cidazure-common-observe-browsecw](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-observe-browsecw.png?width=1000px)

2. Select **Dashboards** on the left and click on the dashboard created by the deployment. In our example we used *cid* as the prefix code and the resource id is *cwd*
![Images/cidazure-common-observe-selectcw](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-observe-selectcw.png?width=1000px)

3. Take a look at the widgets to identify errors.
![Images/cidazure-common-observe-cwdash](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-observe-cwdash.png?width=1000px)

The error in the screenshot below indicates an incorrect Azure App Secret usage.

![Images/cidazure-common-observe-autherror](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-observe-autherror.png?width=1000px)

This basic dashboard serves as a consolidated view, aggregating logs and metrics to aid in troubleshooting various issues.

#### AWS Glue Job Errors

When executing the AWS Glue job, it's possible to encounter errors. This section aims to assist in troubleshooting such errors.
![Images/cidazure-common-disable-secretsmanager](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-observe-glueerror.png?width=1000px)

1. Troubleshoot AWS Glue errors by reviewing Cloudwatch **Output logs**. For the error above the log contains the following entries:
![Images/cidazure-common-disable-secretsmanager](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-observe-gluelog.png?width=1000px)

2. The last entry indicates a problem with the CSV file. If a CSV file cannot be processed it's moved to the *azureciderrors* folder. This process ensures that the next days CSV file of month-to-date costs are not blocked and can still be processed. 
![Images/cidazure-common-disable-secretsmanager](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-observe-errorsfolder.png?width=1000px)

3. Upon inspections we see that the CSV file has a malformed date:
![Images/cidazure-common-disable-secretsmanager](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-observe-csverror.png?width=1000px)

4. To resolve you could wait until tomorrow, when a new file with month-to-date costs are processed, or you can manually fix the file, move to the *azurecidraw* folder and reprocess.

5. Sometimes the AWS Glue job errors will be more descriptive. The error below indicates that there are no files in the *azurecidraw* folder or the folder structure is incorrect.
![Images/cidazure-common-disable-secretsmanager](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-observe-nofileerror.png?width=1000px)

If you notice dashboard data is not updating, check the *azureciderrors* folder and Output logs for the AWS Glue job.

{{% /expand%}}

### Applying an update
{{%expand "Click to expand" %}}
This section will be updated when we release a new version of the solution. 
{{% /expand%}}

{{% notice note %}}
We would love to hear your thoughts! Please drop us an [email](mailto:cloud-intelligence-dashboards@amazon.com) or raise an [issue.](/contributing/02_reportingbugs/) Got a great idea to improve this content? Head over to the [Contribution Guide.](/contributing/)
{{% /notice %}} 


{{< prev_next_button link_prev_url="../2_setup/" link_next_url="../4_clean_up/" />}}

