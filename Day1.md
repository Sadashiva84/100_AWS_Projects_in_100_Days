# Hosting a Static Portfolio Website on Amazon S3

This guide provides a straightforward process for hosting a static portfolio website on Amazon S3, a cost-effective and scalable option for static content like HTML, CSS, JavaScript, and images.

## Step 1: Prepare Your Website Files

Make sure your website's HTML, CSS, JavaScript, images, and other files are ready. Your website should have an `index.html` file as the entry point.

## Step 2: Create an S3 Bucket

1. **Log in** to the AWS Management Console and navigate to the **S3 service**.
2. **Create a new bucket**:
    - Click **Create bucket**.
    - **Bucket name**: Choose a unique, DNS-compliant name for your bucket. This name will be part of your website URL.
    - **Region**: Select the AWS Region where you want to host your website.
    - **Uncheck** the **Block all public access** settings, acknowledging that the bucket will be publicly accessible.
    - **Acknowledge** that the bucket will be publicly accessible.
    - Click **Create bucket**.

## Step 3: Enable Static Website Hosting

1. After creating your bucket, click on its name to open its settings.
2. Navigate to the **Properties** tab.
3. Find the **Static website hosting** option and click **Edit**.
4. Select **Enable** and enter `index.html` as both the index document and the error document (or as appropriate for your site structure).
5. Note the **Endpoint URL**. You'll use this URL to access your website.
6. Click **Save changes**.

## Step 4: Upload Your Website Files

1. Navigate to the **Objects** tab of your bucket.
2. Click **Upload**.
3. Add your website files. You can drag and drop files or use the upload dialog to select files.
4. Click **Upload**.

## Step 5: Set Bucket Permissions

To make your website publicly accessible, you need to modify the bucket policy:

1. Navigate to the **Permissions** tab of your bucket.
2. Click **Bucket policy**.
3. Enter a bucket policy that grants public read access to your website files. Here's an example policy (replace `your-bucket-name` with the name of your S3 bucket):

    ```json
    {
      "Version": "2012-10-17",
      "Statement": [{
        "Sid": "PublicReadGetObject",
        "Effect": "Allow",
        "Principal": "*",
        "Action": "s3:GetObject",
        "Resource": "arn:aws:s3:::your-bucket-name/*"
      }]
    }
    ```

4. Click **Save changes**.

## Step 6: Access Your Website

- Use the **Endpoint URL** you noted earlier to access your website. The URL should look something like `http://your-bucket-name.s3-website-region.amazonaws.com`.

## Optional Steps

- **Custom Domain**: If you want to use a custom domain (e.g., `www.yourportfolio.com`), you can configure a DNS record in Route 53 or your DNS provider to point to your S3 bucket URL.
- **SSL/TLS Certificate**: For HTTPS support, you can use Amazon CloudFront as your content delivery network (CDN) and attach an SSL/TLS certificate from AWS Certificate Manager (ACM) or another provider.

## Tips

- **Testing**: Before making your website public, test it thoroughly to ensure all pages and resources load correctly.
- **Backup**: Keep a backup of your website files in a separate location.
- **Updates**: To update your website, simply upload the new or modified files to your S3 bucket.

This guide outlines the basics of hosting a static website on Amazon S3. For more advanced setups, including setting up CloudFront for HTTPS, refer to the AWS documentation for detailed guides and best practices.
