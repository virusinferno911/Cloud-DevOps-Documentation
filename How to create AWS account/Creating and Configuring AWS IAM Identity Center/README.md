# Creating and Configuring AWS IAM Identity Center

**SUMMARY**

AWS IAM Identity Center (formerly AWS SSO) provides centralized access management for AWS accounts and applications within an AWS Organization. It allows administrators to create users and groups in one place, assign them permission sets, and grant secure access to multiple AWS accounts without creating separate IAM users in each account. By using IAM Identity Center, organizations simplify user management, improve security through centralized authentication, and enable users to sign in once to access all authorized AWS accounts and services.

**Step-by-Step in Creating and Configuring AWS IAM Identity Center**

**STEP ONE**

Sign in to the Management Account:

- Log in to the **AWS Management Console**
- Use the **management account** of your AWS Organization
- Ensure you have admin or Organizations permissions
- In the AWS Console search bar, type **IAM Identity Center**
- Click **IAM Identity Center (Successor to AWS SSO)**

![image.png](Creating%20and%20Configuring%20AWS%20IAM%20Identity%20Center/image.png)

**STEP TWO**

Enable IAM Identity Center:

- On the IAM Identity Center page, click **Enable**
- AWS will automatically:
    - Create the Identity Center instance
    - Link it to your AWS Organization
    - Set up the default Identity Center directory

![image.png](Creating%20and%20Configuring%20AWS%20IAM%20Identity%20Center/image%201.png)

![image.png](Creating%20and%20Configuring%20AWS%20IAM%20Identity%20Center/image%202.png)

**STEP THREE**

Choose Identity Source:

- Under **Settings → Identity source**
- Select **Identity Center directory** (default and recommended for beginners)
- This allows you to create users directly in AWS

![image.png](Creating%20and%20Configuring%20AWS%20IAM%20Identity%20Center/image%203.png)

![image.png](Creating%20and%20Configuring%20AWS%20IAM%20Identity%20Center/image%204.png)

**STEP FOUR**

Locate the AWS Access Portal URL:

- In IAM Identity Center, go to **Settings**
- Copy the **AWS access portal URL**
- This URL is used by users to sign in to AWS accounts

![image.png](Creating%20and%20Configuring%20AWS%20IAM%20Identity%20Center/image%205.png)

**STEP FIVE**

Create User:

- Click **Users**
- Click **Add user**
- Enter user details (username, email, name)
- Save the user

The user will receive an email to set their password.

![image.png](Creating%20and%20Configuring%20AWS%20IAM%20Identity%20Center/image%206.png)

![image.png](Creating%20and%20Configuring%20AWS%20IAM%20Identity%20Center/image%207.png)

**STEP SIX**

Create Groups (Recommended):

- Click **Groups**
- Click **Create group**
- Add users to the group

![image.png](Creating%20and%20Configuring%20AWS%20IAM%20Identity%20Center/image%208.png)

![image.png](Creating%20and%20Configuring%20AWS%20IAM%20Identity%20Center/image%209.png)

**STEP SEVEN**

Create Permission Sets:

- Click **Permission sets**
- Click **Create permission set**
- Choose permission type (AWS managed policies)
- Create the permission set

![image.png](Creating%20and%20Configuring%20AWS%20IAM%20Identity%20Center/image%2010.png)

![image.png](Creating%20and%20Configuring%20AWS%20IAM%20Identity%20Center/image%2011.png)

![image.png](Creating%20and%20Configuring%20AWS%20IAM%20Identity%20Center/image%2012.png)

**STEP EIGHT**

Assign Access to AWS Accounts:

- Click **AWS accounts**
- Select an account (e.g. `development`)
- Assign users or groups with permission sets

![image.png](Creating%20and%20Configuring%20AWS%20IAM%20Identity%20Center/image%2013.png)

![image.png](Creating%20and%20Configuring%20AWS%20IAM%20Identity%20Center/image%2014.png)

**STEP NINE**

Sign in and Test Access:

- Open the **AWS access portal URL**
- Sign in as the user
- Select the assigned account and role
- Confirm access

![image.png](Creating%20and%20Configuring%20AWS%20IAM%20Identity%20Center/image%2015.png)

![image.png](Creating%20and%20Configuring%20AWS%20IAM%20Identity%20Center/image%2016.png)