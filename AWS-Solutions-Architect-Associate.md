# aws-solutions-architect-associate

This will be a home to all important notes pertaining to the AWS SAA-C03 Exam Prep

## Intro

The AWS global infrastructure can be found [here](https://aws.amazon.com/about-aws/global-infrastructure/) where you can find resources in each region, POPs etc. The Exam Blueprint is [here](AWS-Certified-Solutions-Architect-Associate_Exam-Guide.pdf)

## IAM: Users & Groups
- IAM => Identity and Access Management, **Global** service
- **Root account** created by default created the first IAM user, shouldn't be used or shared
- **Users** are people within your organization, and can be grouped
- **Groups** only contain users, not other groups
- Users don't have to belong to a group, and user can belong to multiple groups. At the same time a user may not belong to any group
  
**Exam tip:** Recommended/best practice is using groups

## IAM: Permissions
- **Users or Groups** can be assigned JSON documents called policies 
- These policies governs access **permissions** of users
- In AWS you apply the **least privilege principle:** don't give more permissions that the user needs
- IAM is a global service
  - To create a user you go to IAM > Users > Create user 
    - User name > mbadwa
      - Check the Provide user access to the AWS Management Console - *optional*
        - User type
          - Select I want to create an IAM user option
        - Console password
          - Select Autogenerated password
          - Check Users must create a new password at next sign-in-recommended box
      - Hit Next
      - Set Permissions > Permissions options > Create group
        - User group name
          - admin
          - Permissions policies 
            - AdministratorAccess
          - Hit Create user group
      - Check the admin group box and hit Next
      - Review and Create
        - User details
        - Permissions summary
        - Tags - *optional*
          - Key: Department
          - Value - optional: Engineering
      - Hit Create user
    - Retrieve password
      - Console sign-in details > Email sign-in instructions
    - Hit Return to users list
    - Go to IAM > User groups > Admin > Permissions
    - Now you can log in as a new user
  
## IAM Policies

  ### 1. IAM Policies inheritance

  ![Alt](IAM%20Policy%20Inheritance.png)

  - Imagine you have a Developers group and attach a policy at a group level and their members are Alice, Bob and Charles. They will inherit the policy permissions attached to the Developer group. 
  
  - Likewise for David and Edward that belong to Operations, they'll have a different policy inherited from their group. 
  
  - A singularly attached policy attached to Fred for example is called inline policy. 
  
  - Finally, if Charles and David also belong to Audit Team, they will have two separate policies attached to them, Charles from Developers and Audit team while David will have Audit Team policy and Operations Team.

 ### 2. IAM Policy structure

![IAM Policy Structure](/IAM%20Policy%20Structure.png)
 - Consists of
   - **Version:** policy language version, always include"2012-10-17"
   - **Id:** an identifier for the policy (optional)
   - **Statement:** one or more individual statements (required)
 - Statements consists of
   - **sid:** an identifier for the statement (optional)
   - **Effect:** whether the statement allows or denies access (Allow, Deny)
   - **Principal:** account/user/role to which this policy is applied to, ie. root account of AWS
   - **Action:** a list of actions this policy allows or denies
   - **Resource:** a list of resources to which the actions will be applied to
   - **Condition**: conditions for when this policy is in effect (optional)
  
    **Exam tip:** You need to decipher an IAM policy
 
 ## IAM & AWS CLI

  - Strong passwords = higher security for your account
  - AWS allows you to setup a password policy
    - Set a minimum password length
    - Specific character types:
      - including uppercase letters
      - lowercase letters
      - numbers
      - non-alphanumeric characters
    - Allow or disallow IAM users to change their own passwords
    - Require users to change their password after some time (password expiration)
    - Prevent password re-use
  - Multi Factor Authentication - MFA
    - Users with access to your account can possibly change configurations or delete resources in your AWS account
    - For this you need to protect the Root Account and IAM users using MFA
    - MFA = password *you know* + security device *you own*
    - Main benefit is that if password is compromised then the compromiser will need to get the MFA to log in, this will maintain the security of the account.
    - MFA device Options in AWS
      - Virtual MFA device, e.g. Google Authenticator, Authy, Microsoft Authenticator etc.
      - Universal 2nd Factor (U2F) Security Key. YubiKey by Yubico(3rd Party). This is a USB physical device. It supports for multiple root and IAM users using a single security key
      - Hardware Key Fob MFA Device, e.g. by Gemalto (3rd Party)
      - Hardware Key Fob MFA Device for AWS GovCloud(US) e.g. by SurePassID (3rd Party)
  - To define a password policy 
    - Go to IAM > Access management > Account settings > Edit > Either select IAM Default or Custom
    - Do the changes and hit Save changes
  - To setup MFA
    - Click on your account name on top right corner > Security credentials > Assign MFA Device > MFA device name > MFA device > Device Options > Authenticator App 
    - Do follow the prompts to set it up
  - Options to access AWS
    - There are three options:
      - Via AWS Management Console (protected by password + MFA)
      - AWS Command Line Interface (CLI): protected by access keys
      - Software Development Kit (SDK) - for apis to be called within a code: protected by access keys
    - Access keys are generated through the AWS Management Console
    - Users are responsible for their own access keys. Don't share them with other people
 - Installing AWS CLI, use this [guide](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
 - To create an access key 
   - Go to IAM > Users > Your username > Create access key > Use case > Select Command Line Interface (CLI) option
   - Check the Confirmation button and hit the Next button
   - Hit the Create access key
   - Download

- Logging into AWS using CLI
  - Go to your terminal and type

        $ aws configure
        AWS Access Key ID [None]: Enter your key ID here
        AWS Secret access Key [None]: Enter your Secret access Key here
        Default region name [None]: Enter your region here e.g. us-east-1
        Default output format [None]: Hit enter key
  - You are good to go
- Testing a few commands
  
        $ aws iam list-users

  Output similar to this

        {
        "Users": [
            {
                "Path": "/",
                "UserName": "ansibleadmin",
                "UserId": "AIDA2ITMZ3WJKHKZKK5K4",
                "Arn": "arn:aws:iam::705676565906:user/ansibleadmin",
                "CreateDate": "2024-08-06T15:14:00+00:00"
            }
        ]
        }
 - AWS CloudShell: Region Availability
   - Cloud shell is not readily available to all regions, here is the [reference](https://docs.aws.amazon.com/cloudshell/latest/userguide/supported-aws-regions.html)
   - Alternatively you can use cloud shell 
     - Go to the top of your screen and click on shell icon next to the bell icon
     - It'll indicate the region you are in and can run commands from it as well
  
            $ aws iam list-users
     - Cloud shell retains your files, no worries after a restart of your session. You can change fonts under Preferences as well by clicking on the cogwheel
     - You can also download and upload files, you can add tabs etc.
  
 - IAM Roles for Services
   - Some AWS services will need to perform actions on your behalf
   - To do so, we will need to assign **permissions** to AWS services with **IAM Roles**. For example, take an EC2 server trying to perform some action on other AWS service like connecting to another AWS service such as an EC2 Database instance, this will require creating an IAM Role that can perform such actions attached to the EC2.
   - Common role:
     - EC2 Instance Roles
     - Lambda Function Roles
     - Roles for CloudFormation
   - Creating a role
     - Go to IAM > Roles > Create role
     - Trusted entity type
       - select AWS service 
     - Use case > Service or use case
       - Select EC2
     - Hit Next 
     - Permissions policies > select `IAMReadOnlyAccess`
     - Hit Next
     - Hit Create role
 ## IAM Security Tools
   - IAM Credentials Report (Account-level)
     - A report that lists all your account's users and the status of their various credentials
     - To retrieve a Credential Report; go to IAM > Access Reports > Credential Report > Download credentials report
   - IAM Last Accessed (user-level)
     - Last Accessed shows the service permissions granted to a user and when those services were last accessed.
     - This helps to assess which access permissions are frequently used and helps us trim those that are hardly used to maintain the least access privilege security practice.
     - To retrieve information on Last Accessed; go to IAM > Users > your user > Last Accessed
     - You can page through to see the services accessed with what permissions
     - This helps to drill down on granular access permissions on AWS
## IAM Guidelines & Best Practices

   - Don't use root account except for AWS account setup
   - One physical user = One AWS user
   - Assign users to groups and assign permissions to groups, so that access permissions are managed at the group level
   - Create a strong password policy
   - Use and enforce the use of Multi Factor Authentication (MFA)
   - Create and use **Roles** for granting permissions to AWS services
   - Use Access Keys for Programmatic Access (CLI/SDK)
   - Audit permissions of your account using IAM Credentials Report and IAM Last Accessed
   - **Never ever share your IAM users and Access Keys**
  
## IAM Section summary

   - **Users**: mapped to a physical user, has a password for AWS Console
   - **Groups**: contains users only
   - **Policies**: JSON document that outlines permissions for users or groups
   - **Roles**: for EC2 instances or AWS services
   - **Security**: MFA + Password Policy
   - **AWS CLI**: manage your AWS services using the command-Line
   - **AWS SDK**: manage your AWS services using a programming language
   - **Access Keys**: access AWS using the CLI or SDK
   - **Audit**: IAM Credential Reports & IAM Last Accessed

## EC2 Fundamentals
