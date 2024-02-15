# Hosting a Flask Application on AWS EC2 with Custom DNS

This guide walks you through deploying a Flask application on an Amazon Web Services (AWS) EC2 instance and setting up a custom domain name for your application. We'll cover setting up the EC2 instance, deploying the Flask app, and configuring a custom DNS.

## Prerequisites

- An AWS account
- A Flask application ready for deployment
- A registered domain name (you can register a domain through AWS Route 53 or another domain registrar)

## Step 1: Set Up an EC2 Instance

1. **Log in** to the AWS Management Console and navigate to the EC2 Dashboard.
2. Click **Launch Instance** to create a new instance.
3. **Select an Amazon Machine Image (AMI)**: Choose an AMI with your preferred OS. For a Flask application, an Ubuntu Server is a popular choice.
4. **Choose an Instance Type**: Select an instance type that fits your application's needs (e.g., `t2.micro` for a small, free-tier eligible instance).
5. **Configure Instance Details** as needed. The defaults are often sufficient for a basic setup.
6. **Add Storage** if the default storage size isn't enough for your application.
7. **Configure Security Group**: Create a new security group with rules allowing HTTP (port 80) and HTTPS (port 443) access, and SSH (port 22) for remote access.
8. **Review and Launch** the instance. You'll be prompted to select a key pair for SSH access. Create a new key pair or use an existing one, and download it if it's new.
9. **Launch** the instance.

## Step 2: Deploy Your Flask Application

1. **SSH into Your EC2 Instance**:
    - Use the key pair downloaded during setup and the public DNS or IP of the EC2 instance.
    - Example SSH command:
        ```bash
        ssh -i /path/to/your-key.pem ubuntu@your-ec2-public-dns.amazonaws.com
        ```
2. **Install Dependencies**:
    - Update package lists and install Python, pip, and other necessary packages.
        ```bash
        sudo apt-get update
        sudo apt-get install python3 python3-pip python3-venv -y
        ```
3. **Upload Your Flask App**:
    - Use SCP or a Git repository to transfer your Flask application to the EC2 instance.
4. **Set Up a Virtual Environment and Install Dependencies**:
    - Navigate to your Flask app directory.
    - Create and activate a virtual environment.
        ```bash
        python3 -m venv venv
        source venv/bin/activate
        ```
    - Install your Flask app's dependencies.
        ```bash
        pip install -r requirements.txt
        ```
5. **Run Your Flask Application**:
    - You can start your application with Gunicorn or another WSGI server. For example:
        ```bash
        gunicorn --workers 3 --bind 0.0.0.0:80 wsgi:app
        ```
    - Replace `wsgi:app` with your application's entry point.

## Step 3: Configure a Custom Domain

1. **Set Up an Elastic IP (EIP)** for your EC2 instance:
    - In the EC2 Dashboard, navigate to **Elastic IPs** and allocate a new EIP.
    - Associate the EIP with your EC2 instance.
2. **Configure DNS Settings**:
    - Go to your domain registrar's DNS management page.
    - Create an `A` record pointing to your EC2 instance's Elastic IP.
    - If you're using AWS Route 53, you can create a hosted zone for your domain and add the `A` record there.

## Step 4: Secure Your Application with HTTPS (Optional but Recommended)

- Use Let's Encrypt/Certbot or AWS Certificate Manager to obtain an SSL/TLS certificate for your domain.
- Configure your web server (e.g., Nginx or Apache) to serve your Flask application over HTTPS.

## Conclusion

Your Flask application should now be accessible via your custom domain. This guide covers a basic deployment and domain setup. Depending on your application's needs, consider additional configurations like database setup, environment variable management, and security enhancements.

