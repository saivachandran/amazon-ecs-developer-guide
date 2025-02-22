# Amazon ECS container instance IAM role<a name="instance_IAM_role"></a>

Amazon ECS container instances, including both Amazon EC2 and external instances, run the Amazon ECS container agent and require an IAM role for the service to know that the agent belongs to you\. Before you launch container instances and register them to a cluster, you must create an IAM role for your container instances to use\.

**Important**  
If you are registering external instances to your cluster, the IAM role you use requires Systems Manager permissions as well\. For more information, see [Required IAM permissions for external instances](ecs-anywhere-iam.md#ecs-anywhere-iam-required)\.

Amazon ECS provides the `AmazonEC2ContainerServiceforEC2Role` managed IAM policy which contains the permissions needed to use the full Amazon ECS feature set\. This managed policy can be attached to an IAM role and associated with your container instances\. Alternatively, you can use the managed policy as a guide when creating a custom policy to use\. The container instance role provides permissions needed for the Amazon ECS container agent and Docker daemon to call AWS APIs on your behalf\. For more information on the managed policy, see [AmazonEC2ContainerServiceforEC2Role](security-iam-awsmanpol.md#security-iam-awsmanpol-AmazonEC2ContainerServiceforEC2Role)\.

## Creating the Amazon ECS container instance IAM role<a name="instance-iam-role-create"></a>

**Important**  
If you are registering external instances to your cluster, see [Required IAM permissions for external instances](ecs-anywhere-iam.md#ecs-anywhere-iam-required)\.

The Amazon ECS instance role is automatically created for you when completing the Amazon ECS console first\-run experience\. However, you can manually create the role and attach the managed IAM policy for container instances to allow Amazon ECS to add permissions for future features and enhancements as they are introduced\. Use the following procedure to check and see if your account already has the Amazon ECS container instance IAM role and to attach the managed IAM policy if needed\.<a name="procedure_check_instance_role"></a>

**To check for the `ecsInstanceRole` in the IAM console**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**\. 

1. Search the list of roles for `ecsInstanceRole`\. If the role does not exist, use the procedure in the next section to create the role\. If the role does exist, select the role to view the attached policies\.

1. Choose the **Permissions** tab\.

1. In the **Permissions policies** section, ensure that the **AmazonEC2ContainerServiceforEC2Role** managed policy, or an equivalent custom policy, is attached to the role\. If the policy is attached, your container instance role is properly configured\. If not, follow the substeps below to attach the policy\.
**Important**  
The **AmazonEC2ContainerServiceforEC2Role** managed policy should be attached to the container instance IAM role, otherwise you will receive an error using the AWS Management Console to create clusters\.

   1. Choose **Attach policies**\.

   1. In the **Filter policies** search box, type **AmazonEC2ContainerServiceforEC2Role** to narrow the available policies to attach\.

   1. Check the box to the left of the **AmazonEC2ContainerServiceforEC2Role** policy and choose **Attach policy**\.

1. Choose the **Trust relationships** tab, and **Edit trust relationship**\.

1. Verify that the trust relationship contains the following policy\. If the trust relationship matches the policy below, choose **Cancel**\. If the trust relationship does not match, copy the policy into the **Policy Document** window and choose **Update Trust Policy**\.

   ```
   {
     "Version": "2008-10-17",
     "Statement": [
       {
         "Sid": "",
         "Effect": "Allow",
         "Principal": {
           "Service": "ec2.amazonaws.com"
         },
         "Action": "sts:AssumeRole"
       }
     ]
   }
   ```

**To create the `ecsInstanceRole` IAM role for your container instances**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles** and then choose **Create role**\.

1. Choose the **AWS service** role type, and then choose **Elastic Container Service**\.

1. Choose the **EC2 Role for Elastic Container Service** use case and then **Next: Permissions**\.

1. In the **Attached permissions policies** section, verify the **AmazonEC2ContainerServiceforEC2Role** policy is selected and then choose **Next:Tags**\.
**Important**  
The **AmazonEC2ContainerServiceforEC2Role** managed policy should be attached to the container instance IAM role, otherwise you will receive an error using the AWS Management Console to create clusters\.

1. For **Add tags \(optional\)**, specify any custom tags to associate with the policy and then choose **Next: Review**\.

1. For **Role name**, type `ecsInstanceRole` and optionally you can enter a description\.

1. Review your role information and then choose **Create role** to finish\.

## Adding Amazon S3 read\-only access to your container instance IAM role<a name="container-instance-role-s3"></a>

Storing configuration information in a private bucket in Amazon S3 and granting read\-only access to your container instance IAM role is a secure and convenient way to allow container instance configuration at launch time\. You can store a copy of your `ecs.config` file in a private bucket, use Amazon EC2 user data to install the AWS CLI and then copy your configuration information to `/etc/ecs/ecs.config` when the instance launches\.

For more information about creating an `ecs.config` file, storing it in Amazon S3, and launching instances with this configuration, see [Storing container instance configuration in Amazon S3](ecs-agent-config.md#ecs-config-s3)\.

**To allow Amazon S3 read\-only access for your container instance role**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**\. 

1. Choose the IAM role you use for your container instances \(this role is likely titled `ecsInstanceRole`\)\. For more information, see [Amazon ECS container instance IAM role](#instance_IAM_role)\.

1. Choose the **Permissions** tab, then **Attach policy**\.

1. On the **Attach policy** page, type `S3` into the **Filter: Policy type** field to narrow the policy results\.

1. Check the box to the left of the **AmazonS3ReadOnlyAccess** policy and click **Attach policy**\.
**Note**  
This policy allows read\-only access to all Amazon S3 resources\. For more restrictive bucket policy examples, see [Bucket Policy Examples](https://docs.aws.amazon.com/AmazonS3/latest/dev/example-bucket-policies.html) in the Amazon Simple Storage Service User Guide\.