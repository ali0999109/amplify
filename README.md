# OverView 
- In this CloudGoat scenario, we’re going to learn how to exploit a Lambda Function that’s vulnerable to SQL Injection in order to grant our limited-privileges user named “Bilbo” admin privileges. Once we’ve escalated privileges, we’ll capture the flag by accessing a secret stored in the AWS Secrets Manager service.![image](https://github.com/user-attachments/assets/fae54973-ac7e-454c-91ef-eb37d7c66c5a)

# Enumerate permissions

First ill start off by enumerating the iam permissions of the user bilbo with PACU run iam__enum_permissions, bilbo has the permission to list roles. You can also use whoami to list permissions with PACU.

![image](https://github.com/user-attachments/assets/2d235191-003d-4753-9e7d-d731dd9d4d07)

We can start by listing out all of the roles in this AWS account. We can do that by running ls in pacu to list all of the modules we have access to:











- 








  










