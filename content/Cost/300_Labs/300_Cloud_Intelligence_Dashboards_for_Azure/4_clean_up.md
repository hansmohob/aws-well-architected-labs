---
title: "Cloud Intelligence Dashboards for Azure - Clean Up"
date: 2020-08-24T11:16:08-04:00
chapter: false
weight: 40
hidden: false
---

{{% notice note %}}
It's best to delete the lab as soon as you have finished or you no longer require the solution. leaving it running will incur usage charges. Deleting resources that are no longer required is an important aspect of Cost Optimization!
{{% /notice %}} 

Once you finish the lab, follow the instructions below to remove all resources.

### CloudFormation Clean Up
{{%expand "Click to expand" %}}

1. Before we delete the CloudFormation stack, let's delete the S3 bucket. Browse to Amazon S3

![Images/cidazure-cleanup-cfn-s3](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-cleanup-cfn-s3.png?width=1000px)

2. Open the S3 bucket created during the deployment and check it's contents. You may wish to retain the processed CSV files for future use. Remember each time they are pulled, Azure data egress charges apply. If you wish to retain the files move the *azurecidprocessed* folder to another S3 bucket.

![Images/cidazure-cleanup-cfn-s3move](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-cleanup-cfn-s3move.png?width=1000px)

3. When you are ready, select the S3 bucket created by your deployment and click **Empty**
![Images/cidazure-cleanup-cfn-s3empty](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-cleanup-cfn-s3empty.png?width=1000px)

4. Follow the on-screen instructions to confirm your action.

5. Select the bucket again and click **Delete**. Following the on-screen instructions to confirm your action.
![Images/cidazure-cleanup-cfn-s3delete](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-cleanup-cfn-s3delete.png?width=500px)

6. Browse to the CloudFormation service.
![Images/cidazure-setup-cfn-browse](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-setup-cfn-browse.png?width=1000px)

7. Select the Stack and click **Delete**. Acknowledge your action and click **Delete Stack**
![Images/cidazure-cleanup-cfn-delete](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-cleanup-cfn-delete.png?width=1000px)

8. Click into the Stack and click the **Events** tab to view progress.
![Images/cidazure-cleanup-cfn-s3deleteprog](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-cleanup-cfn-s3deleteprog.png?width=1000px)

9. The delete progress is complete once you see the stack name and a *DELETE_COMPLETE* status.
![Images/cidazure-cleanup-cfn-s3deletecomp](/Cost/300_Cloud_Intelligence_Dashboard_for_Azure/Images/cidazure-cleanup-cfn-s3deletecomp.png?width=1000px)

Nice work, all resources have been deleted. Please visit us again sometime soon :)
{{% /expand%}}

### Terraform Clean Up
{{%expand "Click to expand" %}}

### CID-CMD Clean Up
{{%expand "Click to expand" %}}


TBC

Nice work, all resources have been deleted. Please visit us again sometime soon :)
{{% /expand%}}

We hope you enjoyed this lab and thankyou for your time. We would love to hear your thoughts! Please drop us an [email](mailto:cloud-intelligence-dashboards@amazon.com) or raise an [issue.](/contributing/02_reportingbugs/) Got a great idea to improve this content? Head over to the [Contribution Guide.](/contributing/)

{{< prev_next_button link_prev_url="../3_common_tasks/"  title="Congratulations!" final_step="true" >}}