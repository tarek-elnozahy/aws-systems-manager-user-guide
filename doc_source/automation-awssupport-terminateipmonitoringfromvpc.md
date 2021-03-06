# AWSSupport-TerminateIPMonitoringFromVPC

## Description

AWSSupport-TerminateIPMonitoringFromVPC terminates an IP monitoring test previously started by AWSSupport-SetupIPMonitoringFromVPC. Data related to the specified test ID will be deleted.

## Document Type

Automation

## Owner

Amazon

## Parameters

### AutomationExecutionId

- **Type**: String
- **Description**: (Required) AWSSupport-SetupIPMonitoringFromVPC automation execution ID of the test you want to terminate.

### SubnetId

- **Type**: String
- **Description**: (Required) The subnet ID for the monitor instance.

### InstanceId

- **Type**: String
- **Description**: (Required) The instance ID for the monitor instance.

### AutomationAssumeRole

- **Type**: String
- **Description**: (Optional) The IAM role for this execution. If no role is specified, AWS Systems Manager Automation will use the permissions of the user that executes this document.

## Examples

### Start the automation

```json
aws ssm start-automation-execution --document-name AWSSupportTest-TerminateIPMonitoringFromVPC --parameters 'AutomationExecutionId=TESTID,SubnetId=TESTSUBNETID,InstanceId=TESTINSTANCEID,'
```

### Retrieve the execution output

```json
aws ssm get-automation-execution --automation-execution-id EXECUTIONID --output text --query 'AutomationExecution.Output'
```

## Required IAM Permissions

It is recommended the user who executes the automation have the **AmazonSSMAutomationRole** IAM managed policy attached.
In addition to that policy, the user must have:

```json
{
   "Version": "2012-10-17",
   "Statement": [
      {
         "Action": [
            "iam:DetachRolePolicy",
            "iam:RemoveRoleFromInstanceProfile",
            "iam:DeleteRole",
            "iam:DeleteInstanceProfile",
            "iam:DeleteRolePolicy"
         ],
         "Resource": [
            "arn:aws:iam::An-AWS-Account-ID:role/AWSSupport/SetupIPMonitoringFromVPC_*",
            "arn:aws:iam::An-AWS-Account-ID:instance-profile/AWSSupport/SetupIPMonitoringFromVPC_*"
         ],
         "Effect": "Allow"
      },
      {
         "Action": [
            "iam:DetachRolePolicy"
         ],
         "Resource": [
            "arn:aws:iam::aws:policy/service-role/AmazonEC2RoleforSSM"
         ],
         "Effect": "Allow"
      },
      {
         "Action": [
            "cloudwatch:DeleteDashboards"
         ],
         "Resource": [
            "*"
         ],
         "Effect": "Allow"
      }
   ]
}
```

## Document Steps

1. **aws:assertAwsResourceProperty** - check AutomationExecutionId and InstanceId are related to the same test.
1. **aws:assertAwsResourceProperty** - check SubnetId and InstanceId are related to the same test.
1. **aws:executeAwsApi** - retrieve the test security group.
1. **aws:executeAwsApi** - delete the CloudWatch dashboard.
1. **aws:changeInstanceState** - terminate the test instance.
1. **aws:executeAwsApi** - remove the IAM instance profile from the role.
1. **aws:executeAwsApi** - delete the IAM instance profile created by the automation.
1. **aws:executeAwsApi** - delete the CloudWatch inline policy from the role created by the automation.
1. **aws:executeAwsApi** - detach the AmazonEC2RoleForSSM managed policy from the role created by the automation.
1. **aws:executeAwsApi** - delete the IAM role created by the automation.
1. **aws:executeAwsApi** - delete the security group created by the automation, if it exists.

## Outputs

None

