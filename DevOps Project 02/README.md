# AWS DevOps Project
## Project Title: Streamlining Development with AWS DevOps Services

## Getting Started

### Step 1: Create an IAM User
- AWS CodeCommit needs an IAM User to work with.
- Create a user `DevOps-Engineer` with access to AWS Management Console.
- Select Auto-Generate Password and enable password change upon user sign in.
- Attach `AWSCodeCommitPowerUser` policy to this user.
- Upon creating the user, you will get the password.

### Step 2: Generate credentials for CodeCommit
- Now that you have the user created, generate the git credentials for that user.
- Users > Security Credentials > HTTPS Git credentials for AWS CodeCommit > Generate.

### Step 3: Create a repository in AWS CodeCommit
- Services > Developer Tools > CodeCommit
- Create arepository

### Step 4: Clone the repo in local system and push the code.
- In the local development env, clone the repository
``` shell
git clone <https clone>
```
- Give the credentials of the IAM user for that repository when asked
- Commmit and push the code to your repository
  ``` shell
  git add .
  git commit -m "Added index.html"
  git push -u origin main
  ```

### Step 5: Build the code
- Go to CodeBuild > Create a Build Project
  - Source Provider - AWS CodeCommit
  - Repository and Branch - Name of the repo from where the code will be fetched
  - Environment Image - Managed Image (This specifies where the build has to happen)
  - Compute Instance - EC2 (Lamda can also be selected)
  - OS - Ubuntu (This specifies the OS of the env image)
  - Runtime - Standard
  - Image - Select the latest one
  - Service Role - This is needed because we have to create a role for codebuild so that it can access other services like codecommit, artifacts etc
  - Buildspec - Use a buildspec file (This file specifies the steps to be followed while building the code)
  - Artifact - Once the code is built, it wil lbe stored in artifact. You can create a S3 bucket and provide the path here.
- Build the project


  
