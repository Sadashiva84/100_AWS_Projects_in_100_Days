# Automating Docker Builds and Pushing to Docker Hub with AWS CodePipeline

This guide describes how to set up AWS CodePipeline to automatically create Docker images from changes in a GitHub repository and push them to Docker Hub.

## Prerequisites

- AWS account.
- GitHub repository with your Docker project.
- Docker Hub account and repository.

## Step 1: Prepare Your Docker Hub Credentials

1. **Create Docker Hub Access Token**:
   - Log in to Docker Hub, go to **Account Settings** > **Security**, and create a new access token. Note this token as you'll use it as your password in AWS Secrets Manager.

## Step 2: Store Credentials in AWS Secrets Manager

1. **Create a New Secret**:
   - Navigate to AWS Secrets Manager in the AWS Console.
   - Click **Store a new secret**.
   - Select **Other type of secrets**.
   - Input your Docker Hub username and access token as key/value pairs.
   - Choose a name for your secret, e.g., `dockerHubCredentials`.

## Step 3: Set Up Your Build Environment with AWS CodeBuild

1. **Create a Build Project in AWS CodeBuild**:
   - Go to the AWS CodeBuild console and create a new build project.
   - **Source**: Choose GitHub and connect your repository. Select the option to rebuild every time a code change is pushed to this repository.
   - **Environment**: Choose a managed image, such as Ubuntu, and specify the runtime(s) you need, along with the Docker runtime.
   - **Service Role**: Ensure CodeBuild has permissions to access your Secrets Manager secret.
   - In the **Buildspec** section, specify the build commands and environment variables. You can reference the secret values by using the `secrets-manager` namespace.

   Example `buildspec.yml`:

   ```yaml
   version: 0.2

   phases:
     pre_build:
       commands:
         - echo Logging in to Docker Hub...
         - $(aws secretsmanager get-secret-value --secret-id dockerHubCredentials --query SecretString --output text | python -c "import sys, json; print('docker login --username ' + json.load(sys.stdin)['username'] + ' --password ' + json.load(sys.stdin)['password'])")
     build:
       commands:
         - echo Build started on `date`
         - echo Building the Docker image...
         - docker build -t yourusername/yourrepository:tag .
     post_build:
       commands:
         - echo Pushing the Docker image...
         - docker push yourusername/yourrepository:tag
         - echo Build completed on `date` ```
   
Replace `yourusername/yourrepository:tag` with your Docker Hub repository and desired tag.

## Step 4: Create a Pipeline in AWS CodePipeline

### Create a New Pipeline:
- **Navigate** to the AWS CodePipeline console and create a new pipeline.
- **Source Stage**: Select GitHub as the source provider and connect to your repository. Configure it to trigger on changes to your specified branch.
- **Build Stage**: Select the build project you created in AWS CodeBuild.
- **Deploy Stage**: Since we're pushing to Docker Hub, you can skip this stage or add custom actions based on your needs.

## Step 5: Test Your Setup

- Make a change to your GitHub repository to trigger the pipeline.
- Monitor the pipeline execution in the AWS CodePipeline console.
- Check Docker Hub for the updated image once the pipeline completes.

## Conclusion

You've successfully set up an automated workflow using AWS CodePipeline to build Docker images from GitHub changes and push them to Docker Hub. This setup streamlines the development process, ensuring your Docker images are always up-to-date with your latest code changes.
