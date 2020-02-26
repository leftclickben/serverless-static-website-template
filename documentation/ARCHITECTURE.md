# Cloud Architecture

This approach describes the static website using two separate CloudFormation stacks:

+ A `storage` stack, which contains the S3 bucket that contains the content of your website, and related policies; this can be deployed to any region
+ A `distribution` stack, which contains the CloudFront distribution, Lambda@Edge functions, SSL certificate and related resources; this is always deployed to `us-east-1`

<div class="mxgraph" style="max-width:100%;border:1px solid transparent;" data-mxgraph="{&quot;highlight&quot;:&quot;#0000ff&quot;,&quot;nav&quot;:true,&quot;resize&quot;:true,&quot;toolbar&quot;:&quot;zoom layers lightbox&quot;,&quot;xml&quot;:&quot;&lt;mxfile host=\&quot;www.draw.io\&quot; modified=\&quot;2020-02-26T03:06:52.093Z\&quot; agent=\&quot;Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:72.0) Gecko/20100101 Firefox/72.0\&quot; etag=\&quot;STVqkQwlGlcRZTjiEMbl\&quot; version=\&quot;12.7.6\&quot; type=\&quot;google\&quot;&gt;&lt;diagram id=\&quot;abyvwXGgJ5nQDaOQdFrX\&quot; name=\&quot;Page-1\&quot;&gt;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&lt;/diagram&gt;&lt;/mxfile&gt;&quot;}"></div> <script type="text/javascript" src="https://www.draw.io/js/viewer.min.js"></script>

## Storage stack

### Resources

+ S3 bucket, where your website content is uploaded
+ [Origin Access Identity](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-restricting-access-to-s3.html) (OAI) in CloudFront
+ S3 bucket policy that gives the OAI permission to read from the bucket

### Parameters

+ `Service` is the service name of the website, used as the base name of all resources
+ `Stage` is used as a suffix to differentiate between environments

### Outputs

+ `BucketName`, the name of the bucket
+ `BucketDomainName`, the (regional) domain name of the bucket
+ `OriginAccessIdentity`, the name of the OAI

## Distribution stack

### Transform

This template uses the `AWS::Serverless` transform, so that we can make use of the `AWS::Serverless::Function` resource type.

### Resources

+ CloudFront distribution
+ SSL certificate that covers the root domain and the www domain using subject alternative names
+ Route53 record sets (up to 4)
+ Lambda@Edge functions (up to 2)
+ IAM role to run the Lambda functions
+ a bucket for CloudFront logging

### Parameters

+ `Service` is the service name of the website, used as the base name of all resources
+ `Stage` is used as a suffix to differentiate between environments
+ `DomainName` is the root domain name to use for the website
* `Subdomain` is the child domain to create under the root domain name, `www` by default
+ `DomainRedirectMode` instructs the template how to configure domain name redirects (see below)
+ `CertificateValidationMethod` can be either `DNS` (the default) or `EMAIL` to trigger one of ACM's validation options
+ `IndexDocument` specifies the filename of objects to serve as directory-level index files, `index.html` by default
+ `Route53HostedZoneId` must be determined manually and passed in
+ `OriginBucketDomainName` is the domain name of the bucket created in the storage stack and output as `BucketDomainName`
+ `OriginAccessIdentity` is the value output from the storage stack as `OriginAccessIdentity` to grant access to the origin bucket

If you use the `npm` command wrapper scripts, then the last two parameters will be automatically retrieved from the deployed corresponding storage stack.

For more information about these parameters and how to use them, see [Configuration](CONFIGURATION.md).

### Outputs

+ `DistributionDomainName` is the native CloudFront distribution DNS name, you will need this if you are hosting DNS outside of Route53
+ `LogsBucketName` is the name of the bucket to which CloudFormation access logs are written
