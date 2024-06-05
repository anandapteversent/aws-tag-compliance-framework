# Create a shared AWS System Manager Automation runbook from the Management Account.
# Share with member accounts.
# Combine AWSS Config required-tags rule and the remediation actoun using an automation runbook into an AWS config conformance pack.
# Deploy it through AWS organiations to selected AWS accounts.

#Pre-Requisite
    #Enable AWS Config in all accounts in AWS organization. Using thq cuik setup below - 
    #Open the AWS Systems Manager console at https://console.aws.amazon.com/systems-manager/.
    #In the navigation pane, choose Quick Setup.
    #On the Config Recording card, choose Create.
    #In the Configuration options section, choose the AWS resource types you want to record and whether you want to include global resources.
    #Choose the Region you want AWS Config to use when recording changes made to global resources. The value you specify determines where API calls originate from when AWS Config gathers information about global resources in your configuration. The Region you choose must be a Region you specify later in Targets.
    #Create a new Amazon Simple Storage Service (Amazon S3) bucket, or choose an existing bucket you want to send configuration snapshots to.
    #Choose the notification option you prefer. AWS Config uses Amazon Simple Notification Service (Amazon SNS) to notify you about important AWS Config events related to your resources. If you choose the Use existing SNS topics option, you must provide the AWS account ID and name of the existing Amazon SNS topic in that account you want to use. If you target multiple AWS Regions, the topic names must be identical in each Region.
    #In the Schedule section, choose how frequently you want Quick Setup to remediate changes made to resources that differ from your configuration. The Default option runs once. If you don't want Quick Setup to remediate changes made to resources that differ from your configuration, choose Disable remediation under Custom.
    #In the Targets section, choose whether to allow AWS Config recording for your entire organization, some of your organizational units (OUs), or the account you're logged in to.
    #If you choose Entire organization, continue to step 12.
    #If you choose Custom, continue to step 11.
    #In the Target OUs section, select the check boxes of the OUs and Regions where you want to use AWS Config recording.
    #Choose Create.

#Pre-Requisites to go in the code
  # AWS Orgniztions Management Account ID
  # A list of member account IDs that are needed to be excluded from the solution.
  # S3 Bucket in Management account which will be used to hold the conformance pack template.


Step 1 - Deploy the IAM role through CloudFormation
    - Deploy CloudFormation StackSet from the management account - cf-iam.yml (This is for members account).
    - Do it again for the members account using Stacks.

Step 2 - Create the Automation runbook for the remediation action and share the runbook with the member accounts.
 -  Deploy CloudFormation Stacks - cf-automation-runbook.yaml

Step 3 - After the automation runbook is created in Step 2, share it with member accounts.
- In the management account open the AWS Systems Manager Console, and choose documents.
- Choose Owned by me and select SsmDocumentTagRemediation created in the previous step.
- Select Details tab and add accounts where you need to share this runbook.

Step 4 - Deploy the conformance pack in the management account 
 - Create Conformance Pack template - cf-conformancepack-doc-map.yaml
 - Change conformance pack name - Line 19-22.
 - Change require tag / value pair - line 23-27 (add all 3 tags).
 - replace management ID in the template.
 - Upload to an S3 bucket in the management account.

Step 5 - Deploy the conformance pack as an organization conformance pack through Cloud Formation. 
- Modify Excluded Accounts - (modify this based on which accounts you want to do in which change window)
- Modify TemplateS3Uri on line 31.


