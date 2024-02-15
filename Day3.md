# Deploying a Dockerized Flask Application on AWS Lightsail with Custom DNS

This README provides step-by-step instructions on how to deploy a Dockerized Flask application on AWS Lightsail, including how to configure a custom DNS for your application. AWS Lightsail simplifies the process of launching and managing a virtual server, making it an ideal choice for small to medium-sized projects.

## Prerequisites

Before you begin, make sure you have:

- A Dockerized Flask application with a `Dockerfile` in the application's root directory.
- An AWS account.
- A registered domain name.

## Step 1: Prepare Your Dockerized Flask Application

Your Flask application should be containerized using Docker. Here's an example `Dockerfile` for a basic Flask application:

```Dockerfile
# Use an official Python runtime as a parent image
FROM python:3.8-slim

# Set the working directory in the container
WORKDIR /app

# Copy the current directory contents into the container at /app
COPY . /app

# Install any needed packages specified in requirements.txt
RUN pip install --trusted-host pypi.python.org -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```
## Step 2: Create a Lightsail Container Service

- **Log in** to the AWS Management Console and go to the **Lightsail dashboard**.
- Select **Create instance** and then the **Containers** tab.
- Choose your **instance location** and **instance plan** according to your needs.
- **Name your container service**, then click **Create container service**.

## Step 3: Push Your Container to Lightsail

- Build your Docker image locally and tag it appropriately.
- Push your Docker image to the Lightsail container registry using the **AWS CLI** or the **Lightsail console instructions**.

## Step 4: Deploy Your Container

- In the Lightsail console, navigate to your container service.
- Create a new deployment specifying:
  - The container image to use.
  - Necessary environment variables.
  - The port your container listens on (typically port 80).
- Save and deploy your container configuration.

## Step 5: Configure a Custom Domain

- In Lightsail, create a DNS zone for your domain.
- Add a DNS record:
  - Create an **A record** pointing to the public IP address of your Lightsail container service.
- If your domain is registered outside of AWS, update your domain's nameservers to point to the Lightsail DNS zone's nameservers.

## Step 6: Enable HTTPS (Optional but Recommended)

Consider using **AWS Certificate Manager (ACM)** to issue an SSL/TLS certificate for your domain, enabling HTTPS for your Flask application. You may need to configure a load balancer in Lightsail to use ACM certificates.

## Conclusion

Your Dockerized Flask application is now running on AWS Lightsail and accessible via your custom domain. This setup provides a streamlined, cost-effective solution for deploying containerized applications with the benefits of AWS infrastructure.
