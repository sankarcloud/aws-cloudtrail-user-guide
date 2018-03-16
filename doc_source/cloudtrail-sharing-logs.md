# Sharing CloudTrail Log Files Between AWS Accounts<a name="cloudtrail-sharing-logs"></a>

This section explains how to share CloudTrail log files between multiple AWS accounts\. We will assume that the log files have all been received in a single Amazon S3 bucket, which is the default setting for a trail created in the CloudTrail console\. In the first scenario, you will learn how to grant read\-only access to the accounts that generated the log files that have been placed into your Amazon S3 bucket\. In the second scenario, you will learn how to grant access to all of the log files to a third\-party account that can analyze the files for you\. 

To share log files between multiple AWS accounts, you must perform the following general steps\. These steps are explained in detail later in this section\.

+ Create an IAM role for each account that you want to share log files with\.

+ For each of these IAM roles, create an access policy that grants read\-only access to the account you want to share the log files with\. 

+ Have an IAM user in each account programmatically assume the appropriate role and retrieve the log files\. 

This section walks you through the preceding steps in the context of two different sharing scenarios: granting access to the log files to each account that generated those files, and sharing log files with a third party\. Most of the steps you take for the two scenarios are the same; the important difference is in what kind of permissions the IAM role grants to each account\. That is, you can grant permission for an account to read only its own log files, or you can grant an account permission to read all log files\. For details about permissions management for IAM roles, see [ Roles \(Delegation and Federation\) ](http://docs.aws.amazon.com/IAM/latest/UserGuide//WorkingWithRoles.html) in *IAM User Guide*\. 

## Scenario 1: Granting Access to the Account that Generated the Log Files<a name="cloudtrail-sharing-logs-read-only-access-to-generating-account"></a>

In this scenario, we'll assume that your enterprise is made up of two business units and that it maintains three AWS accounts\. The first account, Account A, is the top\-level account\. For example, it might be managed by your enterprise's IT department and therefore be responsible for collecting log files from all other departments and business units into a single bucket\. The other two accounts, B and C, correspond to your enterprise's business units\. 

This scenario assumes that you have already configured the log files from all three accounts to be delivered to a single Amazon S3 bucket, and that account A has full control over that bucket, as shown in the following illustration\. 

![\[All the log files are placed into a single bucket\]](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/images/Sharing1.png)

Although the Amazon S3 bucket contains log files that were generated by Accounts A, B and C, accounts B and C do not initially have access to the log files that accounts B and C generated\. You will give each business unit read\-only access to the log files that it generated, as shown in the following illustration\. 

![\[Giving access to the log files\]](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/images/Sharing2.png)

To grant read\-only access to the log files generated by accounts B and C, you must do the following in the account Account A\. Remember that Account A has full control of the Amazon S3 bucket\.

+ Create an IAM role for account B and another IAM role for account C\. How: [Creating a Role](cloudtrail-sharing-logs-create-role.md) 

+ For the IAM role created for account B, create an access policy that grants read\-only access to the log files generated by account B\. For the IAM role created for account C, create an access policy that grants read\-only access to the log files generated by account C\. How: [Creating an Access Policy to Grant Access to Accounts You Own](cloudtrail-sharing-logs-your-accounts.md) 

+ Have an IAM user in account B programmatically assume the role created for account B\. Have an IAM user in account C programmatically assume the role created for account C\. Each IAM user must be given permission to assume the role by the respective account owner\. How: [Creating permissions policies for IAM users](cloudtrail-sharing-logs-assume-role.md#cloudtrail-sharing-logs-assume-role-create-policy)\.

+  Finally, the account owner who grants the permission must be an administrator, and must know the ARN of the role in account A that is being assumed\. How: [Calling AssumeRole](cloudtrail-sharing-logs-assume-role.md#cloudtrail-sharing-logs-assume-role-call)\. 

The IAM users in accounts B and C can then programmatically retrieve their own log files, but not the log files of any other account\.

## Scenario 2: Granting Access to All Logs<a name="cloudtrail-sharing-logs-read-only-access-to-all-logs"></a>

In this scenario, we'll assume that your enterprise is structured as it was in the previous scenario, that is, it is made up of two business units and it maintains three AWS accounts\. The first account, Account A, is the top\-level account\. For example, it might be managed by your enterprise's IT department and therefore be responsible for placing all other log files into a single bucket\. The other two accounts, B and C, correspond to each of your enterprise’s business units\. 

Like the previous scenario, this scenario assumes that you have already placed the log files from all three accounts into a single Amazon S3 bucket, and that account A has full control over that bucket\. 

Finally, we'll also assume that your enterprise wants to share all the log files from all accounts \(A, B, and C\) with a third party\. We'll say that the third party has an AWS account called Account Z, as shown in the following illustration\.

![\[Account Z reading log files from accounts A, B, and C\]](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/images/Sharing3.png)

To share all of the log files from your enterprise with Account Z, you must do the following in the Account A, the account that has full control over the Amazon S3 bucket\.

+ Create an IAM role for Account Z\. How: [Creating a Role](cloudtrail-sharing-logs-create-role.md)

+ For the IAM role created for Account Z, create an access policy that grants read\-only access to the log files generated by accounts A, B, and C\. How: [Creating an Access Policy to Grant Access to a Third Party ](cloudtrail-sharing-logs-third-party.md) 

+ Have an IAM user in Account Z programmatically assume the role and then retrieve the appropriate log files\. The IAM user must be given permission to assume the role by the owner of Account Z\. How: [Creating permissions policies for IAM users](cloudtrail-sharing-logs-assume-role.md#cloudtrail-sharing-logs-assume-role-create-policy)\. Further, the account owner who grants the permission must be an administrator and know the ARN of the role in Account A that is being assumed\. How: [Calling AssumeRole](cloudtrail-sharing-logs-assume-role.md#cloudtrail-sharing-logs-assume-role-call)\. 