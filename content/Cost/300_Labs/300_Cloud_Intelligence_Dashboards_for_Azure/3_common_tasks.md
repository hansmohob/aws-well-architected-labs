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
No doubt you'll want to create your own awesome visuals to help you interpret information. This is easy to achieve with Amazon QuickSight. Let's create a new visual based on an Azure tag. In our Example, we want to see environment spend over time.

1. Head over to the **QuickSight** service and open the *AWSCIDforAzure* analysis.
![Images/cidazure-common-visual-quicksight](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-visual-quicksight.png?width=500px)

2. Click on the *details* sheet.
![Images/cidazure-common-visual-details](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-visual-details.png?width=1000px)

3. On the top left, click on **ADD** and **Add visual**.
![Images/cidazure-common-visual-addvisual](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-visual-addvisual.png?width=500px)

4. Select a visual type at the bottom left. Let's go with a *Vertical bar chart*.
![Images/cidazure-common-visual-barchart](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-visual-barchart.png?width=500px)

5. Drag *dateparsed* into the **X axis** and *costinbillingcurrency* or *cost* into **Value**.
![Images/cidazure-common-visual-fieldwell](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-visual-fieldwell.png?width=1000px)

6. Scroll down through your fields list. You'll notice a data field called *tag* this is the original column from the Azure cost export. If you specified Azure tags during deployment you'll see additional data fields prefixed with *tag-*
![Images/cidazure-common-visual-tagfields](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-visual-tagfields.png?width=300px)

7. In our example we're going to select *tag-environment* and drag it into the **Group/Color** field well. 
![Images/cidazure-common-visual-envtag](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-visual-envtag.png?width=1000px)

8. Nice, we have a visual, but you can see a lot of our resources are showing as *null* because they are not tagged. Lets create a filter to exclude these. 

Tagging resources helps us drive accountability. It's an important part of cost optimization in any cloud. Read more [here.](https://docs.aws.amazon.com/whitepapers/latest/cost-optimization-laying-the-foundation/tagging.html?ref=wellarchitected)

9. Select our new visual, click on the **Filter** icon and **Add Filter**.
![Images/cidazure-common-visual-filter](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-visual-filter.png?width=500px)

10. Choose your tag, in our case it's *tag-environment*. Click the 3 dots and select **Edit**.
![Images/cidazure-common-visual-configurefilter](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-visual-configurefilter.png?width=500px)

11. Change the filter to exclude *NULL* and click **Apply**.
![Images/cidazure-common-visual-editfilter](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-visual-editfilter.png?width=500px)

12. The visual will update as below. Looks like our developers have been busy over the past few days!
![cidazure-common-visual-filteredvisual](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-visual-filteredvisual.png?width=500px)

Finally, let's create a filter to change the view on all visuals. We'll stick with *tag-environment*

13. Click on any visual, click on the **Filter** icon and **Add Filter**. Select the tag your interested in. 
![cidazure-common-visual-allfilter](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-visual-allfilter.png?width=1000px)

14. Edit the filter as before, but this time in the *Applied to* box, select **All applicable visuals**. Leave the other settings as default and click **Apply**.
![Images/cidazure-common-visual-allfilter2](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-visual-allfilter2.png?width=500px)

15. Close the filter blade. Click the 3 dots and select **Pin to top**
![Images/cidazure-common-visual-pinfilter](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-visual-pinfilter.png?width=500px)

16. We now have the ability to filter all visuals per tag.
![Images/cidazure-common-visual-filterfinish](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-visual-filterfinish.png?width=1000px)

Have a go at creating your own visuals. If you need help understanding the data fields, Microsoft provide documentation [here.](https://learn.microsoft.com/en-us/azure/cost-management-billing/automate/understand-usage-details-fields#list-of-fields-and-descriptions)

{{% /expand%}}

### Changing scheduled times
{{%expand "Click to expand" %}}

The solution runs automatically at the scheduled times you set during deployment. There are two timers. One for the copy of data (Amazon EventBridge) and another for the processing of data (AWS Glue). It's possible to change the timers on each service:

| | |
|-|-|
|![Images/cidazure-common-time-glue](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-time-glue.png?width=500px)|![Images/cidazure-common-time-eventbridge](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-time-eventbridge.png?width=500px)|

| | |
|-|-|

But we encourage you to update your deployment parameters. This will record your settings, making it easier to apply solution updates.

If you need to change scheduled times follow the instructions below. 

#### CloudFormation deployment

1. Browse to the **CloudFormation** service.
![Images/cidazure-setup-cfn-browse](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-browse.png?width=1000px)

2. Select your deployment stack and Click **Update**.
![Images/cidazure-common-time-updatestack](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-time-updatestack.png?width=1000px)

3. Select **Use current template** and click **Next**
![Images/cidazure-common-time-selecttemplate](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-time-selecttemplate.png?width=1000px)

4. Browse to **Data Copy Settings**, change the scheduled times and click **Next**
![Images/cidazure-common-time-datasettings](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-time-datasettings.png?width=500px)

5. On the *Configure stack options* page, click **Next**

6. Review the CloudFormation stack settings, check the box to acknowledge that AWS CloudFormation might create IAM resources with custom names and click **Submit**
![Images/cidazure-setup-cfn-stackreview](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-stackreview.png?width=1000px)

7. The CloudFormation stack will start to update. Click the refresh button to view progress.

8. The update will run for a few minutes. When it’s finished, you’ll see a UPDATE_COMPLETE message.
![Images/cidazure-common-time-updatedone](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-time-updatedone.png?width=1000px)

You've now updated the scheduled times.

#### Terraform deployment

1. Update the Data Copy Settings section of terraform.tfvars
![Images/cidazure-common-time-tfvars](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-time-tfvars.png?width=500px)

2. Run `terraform apply -auto-approve`

You've now updated the scheduled times.

{{% /expand%}}

### Adding additional tags
{{%expand "Click to expand" %}}
At some point you may wish to add additional tags. 
{{% /expand%}}

### Disabling the data copy process
{{%expand "Click to expand" %}}

There may be a time when you want to disable the Azure blob copy process. Perhaps there is a problem with the Azure cost export or you have an issue with the Cloud Intelligence Dashboard. Disabling the copy process stops the flow of data temporarily, giving you time to resolve issues without incurring Azure egress data charges.

1. Browse to AWS Secrets Manager and click on the Secret created by your deployment. In our example we used *cid* as the customer code and the resource id is *sms*

![Images/cidazure-common-disable-secretsmanager](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-disable-secretsmanager.png?width=1000px)

2. Scroll down and click on the **Retrieve secret value** button.

![Images/cidazure-common-disable-secretsmanager](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-disable-value.png?width=1000px)

3. Click the **Edit** button.

4. Change the *isactive* value to **False**

![Images/cidazure-common-disable-secretsmanager](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-disable-edit.png?width=1000px)

5. Click **Save**

To reenable the copy process, run through the steps above changing the *isactive* value to **True**. The solution will copy all data created after the *begindate* day and time.

{{% /expand%}}

### Manual pull of data after initial setup
{{%expand "Click to expand" %}}

At some point you may want to run a manual pull of data. This process is the same as [Step 2 Initial Manual Run](../2_setup/#step-2-initial-manual-run). You can control what data is copied by changing the *begindate* secret value.

1. Browse to AWS Secrets Manager and click on the Secret created by your deployment. In our example we used *cid* as the customer code and the resource id is *sms*

![Images/cidazure-common-disable-secretsmanager](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-disable-secretsmanager.png?width=1000px)

2. Scroll down and click on the **Retrieve secret value** button.

![Images/cidazure-common-disable-secretsmanager](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-disable-value.png?width=1000px)

3. Click the **Edit** button.

4. Change the *begindate* value to the desired data and time. In the example below we will copy all data created after the moon landing.

![Images/cidazure-common-disable-secretsmanager](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-manual-edit.png?width=1000px)

5. Click **Save**

6. Now follow the [Step 2 Initial Manual Run](../2_setup/#step-2-initial-manual-run) process.

{{% notice note %}}
The *begindate* value is automatically updated by Lambda functions each time data is copied. This ensures we only copy new data.
{{% /notice %}} 

{{% /expand%}}

### Observability and troubleshooting
{{%expand "Click to expand" %}}
TBC

If none of the options above helped please drop us an [email](mailto:cloud-intelligence-dashboards@amazon.com) 
{{% /expand%}}

### Applying an update
{{%expand "Click to expand" %}}
We will periodically release new versions of the deployment code
{{% /expand%}}

{{% notice note %}}
We would love to hear your thoughts! Please drop us an [email](mailto:cloud-intelligence-dashboards@amazon.com) or raise an [issue.](/contributing/02_reportingbugs/) Got a great idea to improve this content? Head over to the [Contribution Guide.](/contributing/)
{{% /notice %}} 


{{< prev_next_button link_prev_url="../2_setup/" link_next_url="../4_clean_up/" />}}

