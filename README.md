# OverView 
- In this CloudGoat scenario, we’re going to learn how to exploit a Lambda Function that’s vulnerable to SQL Injection in order to grant our limited-privileges user named “Bilbo” admin privileges. Once we’ve escalated privileges, we’ll capture the flag by accessing a secret stored in the AWS Secrets Manager service.![image](https://github.com/user-attachments/assets/fae54973-ac7e-454c-91ef-eb37d7c66c5a)

# Enumerate permissions

First ill start off by enumerating the iam permissions of the user bilbo with PACU run iam__enum_permissions, bilbo has the permission to list roles. You can also use whoami to list permissions with PACU.

![image](https://github.com/user-attachments/assets/2d235191-003d-4753-9e7d-d731dd9d4d07)

We can start by listing out all of the roles in this AWS account. We can do that by running ls in pacu to list all of the modules we have access to:


![image](https://github.com/user-attachments/assets/31c2842a-85c4-4425-bb8c-7e924b1d9d68)

Under the category ENUM we will find the following policy iam__enum_users_roles_policies_groups. Lets try and run it.  run iam__enum_users_roles_policies_groups

This will enumerate a complete list of users, roles, policies, and groups that are present in the account…but it doesn’t actually show us what the roles are. Instead, we can type in:

data iam roles

![image](https://github.com/user-attachments/assets/c22920fd-5b73-45ee-89de-3323c9c7e532)


We can see that this role Has: An Action of sts:AssumeRole – which is a way of temporary assuming roles through the AWS Security Token Service
An Effect of Allow
And a Principal of arn:aws:iam::272281913033:user/cg-bilbo-vulnerable_lambda_cgidbdmusq6y4k – which is our Bilbo user
What this means is that this role grants assumeRole access to our Bilbo user — this is not an uncommon way of granting permissions to users across AWS accounts where you let the user assume a new role that grants them different permissions.

So, let’s assume this role!

Let’s use Pacu’s search to find a module that will let us do this with the following command.

search role  Under the [Category: LATERAL_MOVE], there’s a module named organizations__assume_role which is exactly what we need.

![image](https://github.com/user-attachments/assets/6dd94040-9281-4bf7-8202-51b8beb47e4c)






















- 








  










