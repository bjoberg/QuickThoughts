# Hosting a Low-Cost, Scalable, Performant Website Using Amazon S3 & CloudFront

A great option for hosting a low-cost, highly-scalable, and performant website is with [Amazon Simple Storage Service (S3)](https://aws.amazon.com/s3/) paired with [Amazon CloudFront](https://aws.amazon.com/cloudfront/).

## Using AWS Console

### Step 1: Create a new S3 bucket

The S3 bucket is required as storage for the website's static files. Although not required, I recommend creating a new bucket just for your website.

1. Navigate to the S3 service page.
1. Select, "Create bucket" button.
1. Update the following settings, use the defaults for the rest:
   - **Block Public Access settings for this bucket**: Block all public access
   - **Bucket Versioning**: Enabled
1. Click, "Create bucket" button.

### Step 2: Add your static files to the bucket

1. Navigate into the bucket (created in the previous step).
1. Click, "upload" button.
1. Select your files to upload.

### Step 3: Create a CloudFront distribution

The CloudFront distribution is needed to deliver our content with lower-latency and reduce the number of request to S3, in affect lowering costs.

1. Navigate to the CloudFront service page.
1. Select, "Create distribution" button.
1. Update the following settings, use the defaults for the rest:
   - **Origin domain**: _S3 bucket created above_
   - **Origin access**: Origin access control settings
     - Click "Create a new OAC" using default settings.
   - **Viewer protocol policy**: Redirect HTTP to HTTPS
   - **Cache key and origin requests**: CachingOptimized
   - **Web Application Firewall (WAF)**: Enable security protections
1. Click, "Create distribution" button.

### Step 4: Update S3 bucket policy

The bucket policy is required so that CloudFront can interact with the S3 bucket.

1. Select your CloudFront distribution from the list.
1. Click, "Origins" tab.
1. Select, the S3 origin.
1. Click, "Edit" button.
1. Select, "Copy policy" button.
1. Navigate to the S3 service page.
1. Select S3 bucket.
1. Click, "Permissions" tab.
1. In the "Bucket policy" section select, "Edit" button.
1. Paste the bucket policy.
1. Click, "Save changes" button.

### Step 5: View your site

1. Navigate to the CloudFront service page.
1. Select your CloudFront distribution.
1. Copy the "Distribution domain name"
1. In a new browser tab, navigate to: `<<domain_name>>/index.html`

### [Optional] Step 6: Set default root object

If you want your users to see the index file when navigating directly to your domain without specifically typing `index.html` as the path, complete these steps.

1. Navigate to the CloudFront service page.
1. Select, "General" tab.
1. In the "Settings" section click, "Edit" button.
1. In the "Default root object" textfield paste `index.html`.
1. Click, "Save changes" button.
1. Wait for the distribution to re-deploy.
1. In a new browser tab, navigate to: `<<domain_name>>`

### [Optional] Step 7: Setup routing for single page applications

Single page applications (SPA) use client-side routing to route users through their application. This is problematic because when CloudFront receives a request it will forward the request to the origin (e.g. S3) in search for the object at that path. Since the only actual path is the `index.html` file all other origin requests will bypass the client-side router defined within the root object.

To solve this, you can use CloudFront error pages to reroute the user back through the index when the path does not exist in the origin.

1. Navigate to the CloudFront service page.
1. Select your distribution.
1. Click, "Error pages" tab.
1. In the "Error pages" section, click "Create custom error response" button.
1. Update the following settings, use the defaults for the rest:
   - **HTTP error code**: 403
   - **Error caching minimum TTL**: 3600
   - **Customize error response**: Yes
     - **Response page path**: `/index.html`
     - **HTTP Response code**: 200
1. Click, "Create custom error response" button.
1. Wait for the distribution to re-deploy.
