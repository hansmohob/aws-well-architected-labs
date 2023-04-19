---
title: "Cloud Intelligence Dashboards for Azure - Common Tasks"
date: 2020-08-24T11:16:08-04:00
chapter: false
weight: 30
hidden: false
---

Now that you've deployed the solution let's perform some common tasks. 

### Creating New Visuals and Filters
{{%expand "Click to expand" %}}
No doubt you'll want to create your own awesome visuals to help you interpret information. This is pretty easy to achieve with Amazon QuickSight. Let's create a new visual based on an Azure tag. In our Example, we want to see environment spend over time.

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

16. We now have the ability to filter all visuals per tag, in this case environment. 
![Images/cidazure-common-visual-filterfinish](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-common-visual-filterfinish.png?width=1000px)

Have a go at creating your own visuals. If you need help understanding the data fields, Microsoft provide documentation [here.](https://learn.microsoft.com/en-us/azure/cost-management-billing/automate/understand-usage-details-fields#list-of-fields-and-descriptions)

{{% /expand%}}

### Changing Scheduled Times
{{%expand "Click to expand" %}}

The solution runs automatically at the scheduled times you set during deployment. There are two timers. One for the copy of data (Amazon EventBridge) and another for the processing of data (AWS Glue). If you need to change these follow the instructions below. 

{{% notice note %}}
You can change the timers on each individual service, but we encourage you to update your deployment parameters. This will ensure these settings are kept when you need to update the solution.
{{% /notice %}} 

#### Cloud Formation Deployment



#### Terraform Deployment



you have a few options:

1. Update the cloud

1. Browse to Amazon Eventbridge. Under

2. In our example we used a customer code of *cid*

cidazure-common-visual-donut.png



{{% /expand%}}


### Adding Additional Tags
{{%expand "Click to expand" %}}

{{% /expand%}}

### Disabling Data Copy Process
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

### Manual Pull of Data After Initial Setup
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

### Observability and Troubleshooting
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

