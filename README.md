# Getting Started with AWS for Developers (step by step)
In a series of several short articles, you'll learn step by step how to start using AWS securely for your projects. Some of the articles are aimed at Node.js developers, but the insights can be applied to other programming languages as well.

---

## AWS account

### Prerequisites
- [AWS account](https://aws.amazon.com/)

### Set up MFA for the root
1. The first thing you should do after creating a new AWS account is to [set up MFA for the root user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_mfa_enable_virtual.html#enable-virt-mfa-for-root). 

---

## AWS CLI

### Prerequisites
- Basic knowledge of [CLI](https://en.wikipedia.org/wiki/Command-line_interface)
- Installed [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

### What you will learn
- How to securely use the AWS CLI in the [AWS recommended way](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-prereqs.html)
- How to log in to the AWS with AWS CLI using short-term credentials and MFA

What can it be useful for?
- For preventing abuse of your AWS account

### 1. Create a new user in IAM Identity Center
1. Follow this guide on [How to create a user with limited permissions, short-term credentials and MFA in IAM Identity Center](#aws-iam-identity-center).

Next steps
- When you've done all the hard but necessary work, you can securely log in to your AWS account using the AWS CLI.

### 2. Log in to AWS using SSO in AWS CLI
1. Start SSO configuration with `aws configure sso` and follow the [Token provider configuration with automatic authentication refresh for AWS IAM Identity Center](https://docs.aws.amazon.com/cli/latest/userguide/sso-configure-profile-token.html) in AWS documentation.
2. Whenever you will need to refresh access token run: `aws sso login --sso-session {session-name}`
3. To log out before session expires use: `aws sso logout`

Note: For other ways to use the AWS CLI more or less securely, [see the AWS documentation](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-authentication.html).

---

## AWS SDK for Node.js developers

### Prerequisites:
- Installed [AWS SDK for Node.js](https://aws.amazon.com/sdk-for-javascript/)

### What you will learn
- How to securely log in to your AWS account using the AWS SDK in case you run your application outside of AWS
- How to create and use short-term access credentials
- How to create and use long-term access credentials

### Short-term credentials
In case you need short-term credentials, e.g. for your local application, proceed as follows:
1. Follow this guide on [How to create a user with limited permissions, short-term credentials and MFA in IAM Identity Center](#aws-iam-identity-center).
2. [Log in using the AWS CLI and SSO](#aws-cli).
3. The AWS SDK will now automatically retrieve the login credentials you signed in with. [Read more in the AWS documentation](https://docs.aws.amazon.com/sdkref/latest/guide/feature-sso-credentials.html).

### Long-term credentials
In case you need long-term access credentials, e.g. for your production application, proceed as follows:
1. Follow this guide on [How to create a user with limited permissions and long-term credentials in IAM](#aws-iam).
2. Load the login credentials into Node.js using [the Shared Credentials File](https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/loading-node-credentials-shared.html).
   > ‼️ Never store your access key in a code repository, or in code.
3. [Customize your Node.js application so that access keys can be rotated regularly](https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/iam-examples-managing-access-keys.html) and cannot be long-term misused in the event of a leak.

Note: For other ways to log in to your AWS account using the AWS SDK, [see the AWS documentation](https://docs.aws.amazon.com/sdk-for-javascript/v3/developer-guide/setting-credentials.html).

---

## AWS IAM Identity Center

### What you will learn
- How to enable IAM Identity Center
- How to create a user with short-term credentials and limited permissions to access AWS
- How to require MFA(2FA) for login

### What can it be useful for?
- For secure use of AWS CLI
- For secure use of AWS SDK with short-term credentials

### 1. Enable IAM Identity Center
1. __Open [IAM Identity Center console](https://console.aws.amazon.com/singlesignon)__ in your AWS account.
2. __Select the region__ where you want to enable the IAM Identity Center service. IAM Identity Center can only be enabled in one region, so choose wisely.
3. __Click Enable__.
4. IAM Identity Center requires you to create an AWS Organization. If you have not yet created an organization, you can do so by selecting __Create AWS organization__ option. AWS will automatically send an verification email to the address that is associated with your AWS root account. The verification email may be delayed, so you have 24 hours to verify.
5. After enabling IAM Identity Center, you can change the identity source. However, most users will suit the default option __Identity Center directory__. The other options are Active Directory and External identity provider. For more information [see AWS docs](https://docs.aws.amazon.com/singlesignon/latest/userguide/get-started-choose-identity-source.html).

Source
- [https://docs.aws.amazon.com/singlesignon/latest/userguide/get-started-enable-identity-center.html](https://docs.aws.amazon.com/singlesignon/latest/userguide/get-started-enable-identity-center.html)

Next steps
- You've just enabled the IAM Identity Center, now create a new user in it. This user will not have any permissions yet, you will assign them later.

### 2. Create a new user in IAM Identity Center
1. In the IAM Identity Center navigation pane, __click Users__.
2. __Click Add user__.
3. __Check Generate a one-time password that you can share with this user__.
4. Fill required fields and __click Next__.
5. On the Add user to groups - optional page, __click Next__.
6. On the Review and add user page, __click Add user__.
7. In One-time password window, __click Copy__ and store sign in instruction to the safe place.

Next steps
- Before you assign permissions to a user and allow them to access to your AWS account, it would be a good idea to activate MFA(2FA). First, you must configure the IAM Identity Center so that all users who log in through the IAM Identity Center must use MFA.

### 3. Require MFA for all IAM Identity Center users
1. In the navigation pane, __choose Settings__.
2. Under Multi-factor authentication, __click Configure__.
3. Set __Prompt users for MFA__ to __Every time they sign in__.
4. Set __Users can authenticate with these MFA types__ to __Authenticator apps__.
5. Set __If a user does not yet have a registered MFA device__ to __Block their sign-in__.
6. __Uncheck Who can manage MFA devices__.

Next steps
- Now you need to add an MFA device for the user to authenticate with.

### 4. Add an MFA device to the user in IAM Identity Center
1. In the IAM Identity Center navigation pane, __click Users__.
2. __Click {user-name}__.
3. Under MFA devices tab, __click Register MFA device__ and follow instructions.

Next steps
- Finally, it's time to create the permissions you want to assign to the user.

### 5. Create a permission set in IAM Identity Center
1. In the IAM Identity Center navigation pane, under Multi-account permissions, __choose Permission sets__.
2. __Choose Create permission set__.
3. In the Permission set type section, __choose Predefined permission set__ and in the Policy for predefined permission set section, __choose PowerUserAccess__ and then __choose Next__. The predefined PowerUserAccess permission set uses the PowerUserAccess AWS managed policy. 
    > In this tutorial we are using PowerUserAccess, which is a permission that has complete access to AWS, but does not allow you to manage accounts and users. You should only [choose the permissions that you really need](https://docs.aws.amazon.com/singlesignon/latest/userguide/get-started-create-permission-set-to-grant-least-privilege-permissions.html).
4. On the Specify permission set details page, keep the default settings and __click Next__.
5. On the Review and create page, keep the default settings and __click Create__.

Source
- [https://docs.aws.amazon.com/singlesignon/latest/userguide/get-started-create-an-administrative-permission-set.html](https://docs.aws.amazon.com/singlesignon/latest/userguide/get-started-create-an-administrative-permission-set.html)

Next steps
- Now that you've created separate users and permissions, it's time to assign them together to grant the user access to your AWS account.

## 6. Set up AWS account access for an IAM Identity Center user
1. In the navigation pane, under Multi-account permissions, __choose AWS accounts__. 
2. Select the check-box next to the AWS account to which you want to assign administrative access.
3. __Click Assign users or groups__.
4. __Click Users tab__.
5. Check the previously created admin user and __click Next__.
6. Under Permission sets, __select the PowerUserAccess permission set__ and __click Next__.
7. On the Review and submit assignments to..., __click Submit__.

Source
- [https://docs.aws.amazon.com/singlesignon/latest/userguide/get-started-assign-account-access-admin-user.html](https://docs.aws.amazon.com/singlesignon/latest/userguide/get-started-assign-account-access-admin-user.html)

Next steps
- If you have done everything right, you should be able to log in with the new user.

### 7. Test signing in to the AWS access portal with your IAM Identity Center user credentials
1. Visit the URL you obtained in step 2/7.
2. Log in with the credentials you obtained in step 2/7.
3. After you are signed in, an AWS account icon appears in the portal.
4. When you __select the AWS account icon__, the account name, account ID, and email address associated with the account appear.
5. __Choose the name of the account__ to display the PowerUserAccess permission set, and __select the Management Console link__ to the right of PowerUserAccess.
6. If you are redirected to the AWS Management Console, you successfully finished setting up administrative access to the AWS account. The role will appear in the AWS access portal as: PowerUserAccess/username.

Source
- [https://docs.aws.amazon.com/singlesignon/latest/userguide/get-started-sign-in-access-portal.html](https://docs.aws.amazon.com/singlesignon/latest/userguide/get-started-sign-in-access-portal.html)

---

## AWS IAM

### What you will learn
- How to create a user with long-term credentials and limited permissions to access AWS

### What can it be useful for?
- For secure use of AWS SDK out of AWS infrastructure

### 1. Create an admins user group in IAM
1. On the AWS Console Home page, search for the IAM service and open it.
2. In the navigation pane, __select Users groups__ and then __click Create group__.
3. For User group name, __enter {group-name}__.
4. Under Attach permissions policies, __check PowerUserAccess__.
    > In this tutorial we are using PowerUserAccess, which is a permission that has complete access to AWS, but does not allow you to manage accounts and users. You should only [choose the permissions that you really need](https://docs.aws.amazon.com/singlesignon/latest/userguide/get-started-create-permission-set-to-grant-least-privilege-permissions.html).
5. __Click Create group__.

### 2. Create an admin user in IAM
1. In the navigation pane, __select Users__ and then __click Create user__.
2. For User name, __enter {user-name}__.
3. __Click Next__.
4. __Check Add user to group__.
5. Under User groups, __check {group-name}__.
6. __Click Next__.
7. __Click Create user__.

### 3. Create access keys for admin user
1. In the navigation pane, __select Users__.
2. Under Users __click on {user-name}__.
3. __Choose Security credentials tab__.
4. Under Access keys __click Create access key__.
5. Now __choose your Use case__ and listen to the AWS recommendation. In this tutorial expected use case is Application running outside AWS. So choose Application running outside AWS and __click Next__.
6. __Click Create access key__.
7. __Copy keys or click Download .csv__ file and save keys to the safe place. Listen to the Access key best practices:
- Never store your access key in a code repository, or in code.
- Disable or delete access key when no longer needed.
- Enable least-privilege permissions.
- Rotate access keys regularly.
8. __Click Done__.