# Running a Flask Application on AWS Lambda with API Gateway and Custom DNS

Deploying a Flask application on AWS Lambda allows you to run your application without managing servers. AWS API Gateway acts as the front door for your application, handling HTTP requests and routing them to your Lambda function. This guide will also cover setting up a custom DNS using Route 53.

## Prerequisites

- AWS account
- Domain name registered (through AWS Route 53 or another domain registrar)
- Flask application ready to be deployed
- [Serverless Framework](https://www.serverless.com/) or [AWS SAM](https://aws.amazon.com/serverless/sam/) installed

## Step 1: Prepare Your Flask Application

Ensure your Flask application is ready for deployment. You may need to make slight adjustments for it to work well with AWS Lambda, such as using the `serverless-wsgi` plugin if you're using the Serverless Framework.

## Step 2: Configure Serverless Framework or AWS SAM

### Using Serverless Framework

1. **Create `serverless.yml`**: Define your service, provider, functions, events, and plugins. Here's a basic example:

    ```yaml
    service: flask-app-service

    provider:
      name: aws
      runtime: python3.8
      stage: dev
      region: us-east-1

    functions:
      app:
        handler: wsgi_handler.handler
        events:
          - http:
              path: /
              method: ANY
          - http:
              path: /{proxy+}
              method: ANY

    plugins:
      - serverless-wsgi
      - serverless-python-requirements

    custom:
      wsgi:
        app: yourapplication:app
      pythonRequirements:
        dockerizePip: non-linux
    ```

2. **Deploy**: Run `serverless deploy` from your project directory.

### Using AWS SAM

1. **Create `template.yaml`**: Define your AWS resources, including Lambda function and API Gateway. Example:

    ```yaml
    AWSTemplateFormatVersion: '2010-09-09'
    Transform: AWS::Serverless-2016-10-31
    Resources:
      FlaskApp:
        Type: AWS::Serverless::Function
        Properties:
          Handler: app.lambda_handler
          Runtime: python3.8
          CodeUri: .
          Events:
            ProxyApiRoot:
              Type: Api
              Properties:
                Path: /
                Method: ANY
            ProxyApiGreedy:
              Type: Api
              Properties:
                Path: /{proxy+}
                Method: ANY
          Policies:
            - AWSLambdaBasicExecutionRole
    ```

2. **Deploy**: Use the AWS SAM CLI to deploy your application with `sam build` followed by `sam deploy`.

## Step 3: Configure Custom Domain in API Gateway

1. **Purchase or Configure Domain**: Ensure your domain is managed in Route 53.
2. **Create a Custom Domain Name in API Gateway**: Go to the API Gateway console, choose "Custom Domain Names", and create a new domain name.
3. **Map the Custom Domain Name to Your API**: Set up a base path mapping that points to your deployed API.
4. **Update DNS Records**: In Route 53, create an `A` or `CNAME` record (as appropriate) to point your domain to the API Gateway custom domain name.

## Step 4: Enable HTTPS (Optional but Recommended)

- Use AWS Certificate Manager (ACM) to provision a certificate for your custom domain.
- Associate the ACM certificate with your custom domain in API Gateway.

## Conclusion

Your Flask application is now deployed on AWS Lambda with API Gateway handling the HTTP requests. The application is accessible via your custom domain, providing a scalable and serverless solution for your Flask app.
