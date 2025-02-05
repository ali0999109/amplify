# OverView 

In this CloudGoat scenario, we’re going to learn how to exploit a Lambda Function that’s **vulnerable to SQL Injection** in order to grant our limited-privileges user named “Bilbo” admin privileges. Once we’ve escalated privileges, we’ll capture the flag by accessing a secret stored in the AWS Secrets Manager service.

![image](https://github.com/user-attachments/assets/fae54973-ac7e-454c-91ef-eb37d7c66c5a)

# Enumerate permissions

First ill start off by enumerating the iam permissions of the user bilbo with PACU **run iam__enum_permissions**, bilbo has the permission to list roles. You can also use whoami to list permissions with PACU.

![image](https://github.com/user-attachments/assets/2d235191-003d-4753-9e7d-d731dd9d4d07)

We can start by listing out all of the roles in this AWS account. We can do that by running ls in pacu to list all of the modules we have access to:


![image](https://github.com/user-attachments/assets/31c2842a-85c4-4425-bb8c-7e924b1d9d68)

Under the category ENUM we will find the following policy iam__enum_users_roles_policies_groups. Lets try and run it.  **run iam__enum_users_roles_policies_groups**

This will enumerate a complete list of users, roles, policies, and groups that are present in the account…but it doesn’t actually show us what the roles are. Instead, we can type in:

**data iam roles**

![image](https://github.com/user-attachments/assets/c22920fd-5b73-45ee-89de-3323c9c7e532)

# Assume role
We can see that this role Has: **An Action of sts:AssumeRole** – which is a way of temporary assuming roles through the AWS Security Token Service
An Effect of **Allow**
And a Principal of **arn:aws:iam::272281913033:user/cg-bilbo-vulnerable_lambda_cgidbdmusq6y4k** – which is our Bilbo user
What this means is that this role grants assumeRole access to our Bilbo user — this is not an uncommon way of granting permissions to users across AWS accounts where you let the user assume a new role that grants them different permissions.

So, let’s assume this role!

Let’s use Pacu’s search to find a module that will let us do this with the following command.

search role  Under the [Category: LATERAL_MOVE], there’s a module named organizations__assume_role which is exactly what we need.

![image](https://github.com/user-attachments/assets/6dd94040-9281-4bf7-8202-51b8beb47e4c)

Lets assume this role now run organizations__assume_role --accounts 272281913033  --roles cg-lambda-invoker-vulnerable_lambda_cgidbdmusq6y4k

Ok so now that i’ve assumed the role, it’s time to list Lambda functions

# Privilege escalation

**run lambda__enum --regions us-east-1**  then run **data lambda** to view what was collected.
![image](https://github.com/user-attachments/assets/06424704-0d10-4f34-9e61-c4e972623fb1)


Using the Location information URL, we can download the source code of the Lambda function from the browser and view it in VScdoe.

![image](https://github.com/user-attachments/assets/71b97a8c-2555-4d39-aaa9-82e12203c0f0)

We want to invoke this Lambda function to grant our bilbo user admin access, which is something we could do via the AWS console or via the AWS CLI. For this let’s use the AWS shell CLI.

**aws --region us-east-1 lambda invoke --function-name vulnerable_lambda_cgidbdmusq6y4k-policy_applier_lambda1 --payload '{"policy_names": ["AdministratorAccess", "--"], "user_name": "cg-bilbo-vulnerable_lambda_cgidbdmusq6y4k"}' --output text out.txt**

**$LATEST 200 return message**, which indicates a success message.

then i use cat on the out.txt file to see the result.

![image](https://github.com/user-attachments/assets/078e9b1a-204e-48e4-ac11-5bb751ba669c)

Unfortunately, the Lambda function prevents us from using the AdministratorAccess policy because the author of that function added a security control…except for the fact that there’s an injection vulnerability.

AWS Lambda is not immune to injections, especially since this one is using a SQLite database, which means we can use SQL injections.

Now for the SQLi injection.. using the same command as above but this time adding a injection in the query.

**aws --region us-east-1 lambda invoke --function-name vulnerable_lambda_cgidbdmusq6y4k-policy_applier_lambda1 --payload '{"policy_names": ["AdministratorAccess'"'"' --"], "user_name": "cg-bilbo-vulnerable_lambda_cgidbdmusq6y4k"}' --output text out.txt**

![image](https://github.com/user-attachments/assets/9250cc9b-91d2-42d3-92c6-e0a5d94bf640)

I've succesfully elevated my privileges through the sqli and have full admin access now 














- 








  










