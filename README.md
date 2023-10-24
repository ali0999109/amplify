# OverView 
- Amplify Console hosts static web resources including HTML, CSS, JavaScript, and image files which are loaded in the user’s browser via S3. JavaScript executed in the browser sends and receives data from a public backend API built using Lambda and API Gateway. Amazon Cognito provides user management and authentication functions to secure the backend API. Finally, DynamoDB provides a persistence layer where data can be stored by the API’s Lambda function.

![image](https://github.com/ali0999109/amplify/assets/145396907/39f94bb8-d3b5-444b-81e7-7b58fe3010a9)


# Setup cloud 9
- Go to cloud 9 click create environment and use the same region for everything
- Enter wildrydes-webapp-development into Name and optionally provide a Description.
- select next step
- in environment settings select t.3 small leave all other  defaults unchanged
- select next step
- Review and create
- Verify that your user is logged in by running the command aws sts get-caller-identity. Copy and paste the command into the Cloud9 terminal window.
- You’ll see output indicating your account and user information
  ![image](https://github.com/ali0999109/amplify/assets/145396907/2a4ab4ad-5cb5-4016-9259-83ec7bd5a307)


  # Static web hosting repository 
  - First, create a new CodeCommit repository from within your Cloud9 terminal window
  - Type in the following commands in cloud9 aws codecommit create-repository \ --repository-name wild-rydes
  - Clone the existing (not new) workshop repository from GitHub: git clone https://github.com/aws-samples/aws-serverless-webapp-workshop.git  
  - Change into the workshop repository directory: cd aws-serverless-webapp-workshop
  - Split out the WildRydesVue code into its own branch: sudo yum install git-subtree -y git subtree split -P resources/code/WildRydesVue -b WildRydesVue
  - Create a New directory for code-commit repo mkdir ../wild-rydes && cd ../wild-rydes
  - initialize a new git repo:  git init
  - Pull the WildRydesVue branch into your new repo: git pull ../aws-serverless-webapp-workshop WildRydesVue
  - Add your CodeCommit repository as a remote: git remote add origin codecommit://wild-rydes
  -  Push the code to your new CodeCommit repository: git push -u origin master
  - Remove the temporary local repository you created in step 2: rm -rf ../aws-serverless-webapp-workshop

  # Static web hosting deploy
  - Launch the amplify console
  - Scroll down to host your web app click Get started
  - Select AWS code commit
  - Select the repository created
  - Select Create a new Environment and name it prod
    ![image](https://github.com/ali0999109/amplify/assets/145396907/f807b04a-7fd1-4796-83ba-800f3e2a7e6d)

  - Click on Create New role make sure amplify is selected click next permissions, click next: Tags, click Next: Review.
  - Give the Role a new name: wildrydes-backend-role and click Create role.
  - Click Attach policies under the ** Add Permissions** tab, search for AWSCodeCommitReadOnly policy, click on the checkbox next to the policy name, and click **Attach Policy**.
    ![image](https://github.com/ali0999109/amplify/assets/145396907/804ae21c-27d4-40cb-bbc1-ffd78a581f0c)
  - click next, save and deploy
  - This initial build and deploy process may take up to five minutes for Amplify Console to create the necessary resources and to deploy your code.
    
    ![image](https://github.com/ali0999109/amplify/assets/145396907/b061efa8-59f4-4832-a330-6b4a03e521b0)
    
  - Once completed, click the site URL to launch your Wild Rydes site.
    ![image](https://github.com/ali0999109/amplify/assets/145396907/f42d56d5-738c-41db-8f19-051df78c181a)

# Modify the website
- From your Cloud9 environment open the index.html file in the /wild-rydes/public/ directory of the repository.
- Modify the title line so it says:   <title>Wild Rydes - Rydes of the Future!</title>
- save the file
- type in the following commands to commit changes to your repository 
- git add .
- git commit -m "updated title"
- git push
- Once completed, re-open the Wild Rydes site and notice the title change.
  ![image](https://github.com/ali0999109/amplify/assets/145396907/7a959caf-f3b3-4c29-a9f1-2a04a86222a9)

# Initialize AWS Amplify CLI
- Install the Amplify CLI by running the following command from within your Cloud9 terminal window: npm install -g @aws-amplify/cli
- Configure your default AWS profile. echo '[profile default]' > ~/.aws/config
- Make sure you are in the root wild-rydes directory of the repository
- Initialize amplify CLI by executing the following command: amplify init
- you should see the following
  ![image](https://github.com/ali0999109/amplify/assets/145396907/54a3feb9-7e23-40c9-976f-1aeaf25af3f7)
- Verify that the initialization has finished by entering the following command. Version 4.29.3 or greater should be installed.
- Command: amplify version
- Next you will add an Amazon Cognito category to your AWS Amplify configuration, via the AWS Amplify CLI.

# Create an Amazon Cognito User Pool using AWS Amplify CLI
- Execute the following commands to add the Amazon Cognito User Pool from within the Cloud9 terminal window:
- amplify add auth
- The AWS Amplify CLI will now run through the set up for Amazon Cognito, select the following'
  
  ![image](https://github.com/ali0999109/amplify/assets/145396907/a243031b-32cd-4388-9237-389233019599)
  
- Once configuration completes you see the following confirmation
  
  ![image](https://github.com/ali0999109/amplify/assets/145396907/2c317d78-0399-4153-bfd6-1e053f161d1c)

- Commit the changes to your git repository:

- git add .

- git commit -m "Configure Cognito"

- git push

- go to Cognito
- choose manage user pools
- Here you will see a new userpool generated by the AWS Amplify CLI that looks something similar to the example below:
  
  ![image](https://github.com/ali0999109/amplify/assets/145396907/c39547d9-2042-4b87-b89e-5debe3895080)


# Create an Amazon DynamoDB Table
- Go to dynamoDB
- Choose create table
- Enter Rides for the Table name. This field is case sensitive.
- Enter RideId  for the Partition key and select String for the key type. This field is case sensitive.
- Choose the Default settings button for Table settings and choose Create table.
  ![image](https://github.com/ali0999109/amplify/assets/145396907/4921bc1b-d40d-48ec-a740-9845b292644b)

# Create an IAM Role for Your Lambda Function
- Go to aws iam console
- Select roles and choose create role
- Under Use case, Select Lambda from the AWS services, then click Next
- Begin typing AWSLambdaBasicExecutionRole in the Filter text box and check the box next to that managed role, then Click Next
- Enter WildRydesLambda for the Role name. Add any tags that you wish.
- Choose Create role.
- Next you need to add permissions to the role so that it can access your DynamoDB table.
- While in the IAM Console on the roles page type WildRydesLambda into the filter box and click the role name.
- Under the Add Permissions tab, choose the create inline policy
  ![image](https://github.com/ali0999109/amplify/assets/145396907/0a604b99-ff22-4e5f-985f-b4f584787792)
- Select choose a service
- Begin typing DynamoDB into the search box labeled Find a service and select DynamoDB when it appears.
- Select actions
- Begin typing PutItem into the search box labeled Filter actions and check the box next to PutItem when it appears.
- Select the Resources section.
- With the Specific option selected, choose the Add ARN link in the table section.
- Paste the ARN of the table you created in the previous section in the Specify ARN for table field, and choose Add.
- Choose Review Policy.
- Enter DynamoDBWriteAccess for the policy name and choose Create policy.


# Create a Lambda Function for Handling Requests

-












  






























    
    









 
    
  
  -
  -





