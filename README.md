Building a CI/CD Pipeline for Hosting Resume Websites on S3 Using GitHub and AWS CodePipeline
=============================================================================================

[![Joyson Fernandes](https://miro.medium.com/v2/resize:fill:88:88/1*2qYR-0r7vQTej6kjWjexpQ.png)
[Joyson Fernandes](https://joysonfernandes.medium.com/building-a-ci-cd-pipeline-for-hosting-resume-websites-on-s3-using-github-and-aws-codepipeline-eae80d50b3a6)


What is Continuous Integration (CI) and Continuous Delivery (CD)?
-----------------------------------------------------------------

CI/CD stands for Continuous Integration and Continuous Delivery (or Deployment). It is a set of practices aimed at improving software development by automating code integration, testing, and deployment.

Continuous Integration (CI)
---------------------------

*   Definition: Automatically integrates code changes from multiple contributors into a shared repository.

Key Elements:

*   Automated testing to catch errors early.
*   Build automation for seamless integration.

**Benefits:** Higher software quality, reduced integration issues.

Continuous Delivery (CD)
------------------------

*   Definition: Prepares code changes for release to production reliably.

Key Elements:

*   Automated deployment processes.
*   Comprehensive testing before releases.

**Benefits:** Faster release cycles, increased confidence in deployment.

Continuous Deployment
---------------------

*   Definition: Extends Continuous Delivery by automatically deploying every change that passes tests to production without manual intervention.
*   Benefits: Eliminates human error in deployments, provides rapid user feedback.

What is AWS CodePipeline?
-------------------------

AWS CodePipeline is a fully managed continuous integration and continuous delivery (CI/CD) AWS service that automates the release process of applications. It enables you to model and visualize the workflow of code changes, streamlining development and deployment.

Key Features:
=============

*   Automated Workflow: Automatically builds, tests, and deploys code in response to changes.
*   Integration: Works seamlessly with AWS services like CodeCommit, CodeBuild, CodeDeploy, and S3.
*   Multi-Stage Pipelines: Supports complex workflows with multiple stages (source, build, test, deploy).
*   Approval Process: Allows for manual approval steps before changes are deployed.
*   Notifications: Integrates with AWS CloudWatch and SNS for real-time updates on pipeline status.

Benefits:
=========

*   Faster Time to Market: Reduces deployment cycle time.
*   Improved Quality: Catches issues early through automated testing.
*   Collaboration: Enhances team collaboration and visibility into workflows.
*   Cost-Effective: Pay only for usage, with no upfront costs.

Overall, CodePipeline helps teams automate and optimize their software release processes efficiently.

Prerequisites:
==============

*   GitHub for Version Control repository creation.
*   AWS Access

Overview
========

In this topic will cover how to setup CI/CD pipeline using GitHub, AWS CodePipeline, S3, CouldFront and SNS for email notification. will start from foundational, advanced, and complex tasks as outlined below.

> _Get ready for some fun :)_

**FOUNDATIONAL TASK:**
----------------------

1.  Create a new repository in GitHub or CodeCommit and load the attached HTML.
2.  Create and configure a S3 bucket to host your static website.
3.  Create a CI/CD pipeline using the **AWS Codepipeline service**.
4.  Set your repo as the Source Stage of the Codepipeline that is triggered when an update is made to the repo.
5.  For the deploy stage select your S3 bucket.
6.  Deploy the pipeline and verify that you can reach the static website.
7.  Make an update to the code in your code in the repo to verify that the codepipeline is triggered. This can be as simple as changing the wording in the file because any change to the files should trigger the workflow.

> **Note: you can skip the Build stage for this project.**

ADVANCED TASK:
--------------

1.  Add a manual approval step so that a senior developer is notified via email to approve the changes prior to the updated code being pushed to the website bucket.
2.  Implement a CloudFront distribution pointing to the website.

**COMPLEX TASK: (Create Your Own Cloud Resume)**
------------------------------------------------

1.  Create your own Cloud Resume.
2.  Verify that the new website is viewable through the CloudFront link.

FOUNDATIONAL Task
=================

**Step 1: Create a New Repository**
-----------------------------------

1.  Go to GitHub (or AWS CodeCommit).
2.  Click ÔÇÿNew RepositoryÔÇÖ.
3.  Name the repository (e.g., `my-static-resume-website`).
4.  Set the repository to public or private based on your need.
5.  Click ÔÇÿCreate RepositoryÔÇÖ.
6.  Create and upload the HTML files with below code to this repository.

```
<h1>Hello Level Up In Tech!</h1>  
<h3>Anyone can transition to DevOps. The steps are: </h3>  
<ol>  
    <li>Trust the process</li>  
    <li>Work hard </li>  
    <li>Be consistent</li>  
    <li>Stay Hungry</li>  
</ol>
```

Step 2: Create and Configure an S3 Bucket
-----------------------------------------

1.  Log in to the AWS Management Console.
2.  Navigate to the S3 service.
3.  Click ÔÇÿCreate bucketÔÇÖ. Select ÔÇÿGeneral purposeÔÇÖ as Bucket type.
4.  Enter a unique bucket name (e.g., `my-static-resume-website-bucket`).
5.  Uncheck ÔÇÿ**Block _all_ public accessÔÇÖ.** Leave rest to default or make changes as per required.
6.  Click ÔÇÿCreate bucketÔÇÖ.
7.  In the bucketÔÇÖs properties, enable ÔÇÿStatic website hostingÔÇÖ.

*   Select ÔÇÿUse this bucket to host a websiteÔÇÖ.
*   Set the Index Document (e.g., `index.html`).

8\. Set bucket policy to allow public access:

*   Go to Permissions > Bucket Policy.
*   Use the following JSON to make your bucket public:

```
{  
  "Version": "2012-10-17",  
  "Statement": \[  
    {  
      "Effect": "Allow",  
      "Principal": "\*",  
      "Action": "s3:GetObject",  
      "Resource": "arn:aws:s3:::my-static-resume-website-bucket/\*"  
    }  
  \]  
}
```

9\. Go into the bucket and Upload ÔÇÿindex.html fileÔÇÖ

Step 3: Create a CI/CD Pipeline Using AWS CodePipeline
------------------------------------------------------

1.  Go to the AWS Management Console and navigate to CodePipeline.
2.  Click ÔÇÿCreate pipelineÔÇÖ and Select ÔÇÿ**Build custom pipeline**ÔÇÖ as creation option.
3.  Provide a name for the pipeline (e.g., `MyResumeWebsitePipeline`).
4.  Choose ÔÇÿNew service roleÔÇÖ and let AWS create a role for you or select existing.
5.  Click ÔÇÿNextÔÇÖ.

Step 4: Set Source Stage
------------------------

1.  Select ÔÇÿGitHubÔÇÖ (or CodeCommit) as the source.

*   Authenticate your account if prompted.
*   Choose your repository and branch (typically `main`).
*   Under Trigger type ÔÇÿNo filterÔÇÖ

2\. Click ÔÇÿNextÔÇÖ.

Step 5: Skip Build Stage
------------------------

1.  On the build stage step, click ÔÇÿSkipÔÇÖ to skip the build stage.
2.  Click ÔÇÿNextÔÇÖ to proceed.

Step 6: Add Deploy Stage
------------------------

1.  Select ÔÇÿAmazon S3ÔÇÖ as the deployment provider.
2.  Choose your S3 bucket created in Step 2 for deployment.
3.  Select ÔÇÿExtract file before deployÔÇÖ.
4.  Click ÔÇÿNextÔÇÖ, and then ÔÇÿCreate pipelineÔÇÖ.

Step 7: Verify Deployment
-------------------------

1.  Wait for the pipeline to initialize and complete the deployment.
2.  Access the static website using the S3 bucket endpoint (found in S3 > Properties for the bucket).

Step 8: Test CodePipeline Trigger
---------------------------------

1.  Make a simple change in your HTML file (e.g., change a word).
2.  Commit and push the changes to the repository.
3.  Check CodePipeline to ensure the pipeline was triggered and completed successfully.

ADVANCED TASK:
==============

Step 9:Create an SNS Topic for notification
-------------------------------------------

**1\. Create an SNS Topic and Subscription.**

*   In the Amazon SNS dashboard, click on Topics in the left sidebar.
*   Click on the Create topic button.

**2\. Configure the Topic**

**_Choose a topic type:_**

*   Select Standard for most use cases.

**_Configure the topic:_**

*   Name: Enter a name for your topic (e.g., `ManualApprovalNotifications`). Topic names must be unique within an AWS account per region.
*   You can optionally add a display name, which is the human-readable name that will be shown in notifications.
*   Click on Create topic at the bottom.

**3\. Add Subscription(s)**

*   After creating the topic, you will be redirected to the topic details page. Click on the Create subscription button.

_Configure the subscription:_

*   Protocol: Choose `Email` if you want to notify a senior developer via email.
*   Endpoint: Enter the email address of the user who should receive the notifications.

_Click on Create subscription._

> Important: The user will need to confirm the subscription by clicking on the link in the email they receive to start receiving notifications.

Step 10: Add a Manual Approval Stage
------------------------------------

1.  Go to AWS CodePipeline, select your pipeline, and click ÔÇÿEditÔÇÖ.
2.  Add a new stage called ÔÇÿApprovalÔÇÖ before the Deploy stage.
3.  In the new Approval stage:

*   Choose ÔÇÿManual approvalÔÇÖ as the action type.
*   Configure the settings to notify a senior developer via email.
*   Select SNS topic, and choose the SNS topic you created (`ManualApprovalNotifications`).
*   Configure the notifications as needed and save the changes.

4\. Save the changes.

Step 11: Implement CloudFront Distribution
------------------------------------------

1.  Click on the **Create Distribution** button.

**2\. Origin Domain Name:** Select the S3 bucket you created earlier from the dropdown list.

**3\. Origin access:** Select Origin access control settings (recommended) and ÔÇ£create new OACÔÇØ which allows bucket access to only CloudFront.

**4\. Viewer Protocol Policy:** Choose **Redirect HTTP to HTTPS** to ensure secure connections.

**5\. Cache Policy:** Under **Cache key and origin requests** Use the default **CachingOptimized** policy, or choose a custom one if you need specific caching rules.

**6\. Web Application Firewall(WAF):** Under Web Application Firewall(WAF) select Do not enable security protection.

**7\. Default Root Object:** Enter `index.html`.

Leave other settings as default unless you have specific requirements.

*   Click **Create Distribution**.
*   **Wait for the Distribution to Deploy:**
*   The distribution will take a few minutes to deploy. You can monitor the progress in the CloudFront dashboard.

8\. Update Bucket policy to allows bucket access to only CloudFront.

*   Go under you bucket permission tab and then click on edit on bucket policy and past the below code.

```
{  
        "Version": "2008-10-17",  
        "Id": "PolicyForCloudFrontPrivateContent",  
        "Statement": \[  
            {  
                "Sid": "AllowCloudFrontServicePrincipal",  
                "Effect": "Allow",  
                "Principal": {  
                    "Service": "cloudfront.amazonaws.com"  
                },  
                "Action": "s3:GetObject",  
                "Resource": "arn:aws:s3:::my-static-resume-website-bucket/\*",  
                "Condition": {  
                    "StringEquals": {  
                      "AWS:SourceArn": "arn:aws:cloudfront::061051257340:distribution/E27670VUCB2P7P"  
                    }  
                }  
            }  
        \]  
      }
```

COMPLEX TASK:
=============

Step 12: Update HTML Template
-----------------------------

1.  I have created my custom resume index.html file
2.  So I will Replace the content of existing HTML files with my custom resume index.html file
3.  And upload these files to my GitHub repository and S3 bucket.

Step 13: Verify CloudFront Distribution
---------------------------------------

1.  Access your new website via the CloudFront distribution URL.
2.  Ensure the new resume template displays correctly.

*   You now have a fully automated deployment system with a manual approval step and a CloudFront distribution for optimization.

> Fell free to check out my resume website and GitHub Repo files links below.
> 
> My resume website link : [JoysonÔÇÖs Resume](https://cv.joysonfernandes.com/)
> 
> GitHub Repo Link: [joyson-fernandes/my-static-resume-website](https://github.com/joyson-fernandes/my-static-resume-website)

If this post was helpful, please click the clap ­ƒæÅ button below a few times to show your support!
