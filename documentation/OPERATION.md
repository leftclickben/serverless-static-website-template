# Deployment & Operation of static websites

There are two ways to deploy your website, depending on whether or not you want to use [NodeJS](https://nodejs.org/en/) and [npm](https://www.npmjs.com/).

## Deploying with `npm`

To deploy with `npm`, you can use the `npm run deploy` command, with parameters (see below) passed in like `--parameter=value`.

Some automation is provided compared to manually running the AWS CLI commands:

+ The storage and distribution stacks can be created in a single command
+ The S3 bucket domain name and Origin Access Identity are ascertained automatically from the storage stack and passed to the distribution stack
+ The service name and content region are configured once only in `package.json` (other parameters may vary between stages)

Here is an example that overrides all possible options:

```
npm run deploy --stage=test --domain=test.example.com --redirect=sub_to_root --subdomain=web --validation=EMAIL --index=default.html --zoneid=ABCDEFGHIJKLMN
```

Additionally, there are some settings that are configured in `package.json`, namely:

+ `service` is the short code representing your website, it is used to form resource names and stack names
+ `content_region` is the region to which the `storage` component is deployed
+ `default_stage` is the stage that will be used if none is specified using the `--stage=[stage]` command argument

You should configure these only once per website.

## Deploying by calling the AWS SDK directly

To deploy without installing `nodejs` and `npm`, you can call the commands in the same sequence as the scripts in `package.json`:

1. Deploy the storage stack
1. Obtain the content bucket's domain name and OAI from the storage stack
1. Deploy the distribution stack

The following commands represent the same example as above (`npm` example):

```
aws cloudformation deploy --region ap-southeast-2 --template-file cloudformation/storage.yaml --stack-name example-storage-test --parameter-overrides Service=example Stage=test --no-fail-on-empty-changeset
BUCKET_NAME=$(aws cloudformation describe-stacks --region ap-southeast-2 --output text --stack-name example-storage-test --query "Stacks[0].Outputs[?OutputKey=='BucketName'].OutputValue")
BUCKET_DOMAIN_NAME=$(aws cloudformation describe-stacks --region ap-southeast-2 --output text --stack-name example-storage-test --query "Stacks[0].Outputs[?OutputKey=='BucketDomainName'].OutputValue")
ORIGIN_ACCESS_IDENTITY=$(aws cloudformation describe-stacks --region ap-southeast-2 --output text --stack-name example-storage-test --query "Stacks[0].Outputs[?OutputKey=='OriginAccessIdentity'].OutputValue")
aws cloudformation deploy --region us-east-1 --template-file cloudformation/distribution.yaml --stack-name example-distribution-test --capabilities CAPABILITY_IAM --parameter-overrides Service=example Stage=test DomainName=test.example.com Subdomain=web DomainRedirectMode=sub_to_root CertificateValidationMethod=EMAIL IndexDocument=default.html Route53HostedZoneId=ABCDEFGHIJKLMN OriginAccessIdentity=${ORIGIN_ACCESS_IDENTITY} OriginBucketDomainName=${BUCKET_DOMAIN_NAME} --no-fail-on-empty-changeset
aws s3 sync public s3://${BUCKET_NAME}
```

## Integration with frontend frameworks

You can use any frontend single page application framework or static site generator.  Add the relevant build commands to `package.json`.  If the content to be published is generated in a different directory (i.e. not `public/`), then you can modify the `deploy:content` script in `package.json`.

## Certificate validation

When deploying a given stack for the first time, it will configure a new SSL certificate.  The deployment will not succeed until the validation is complete.  See [validating with DNS](https://docs.aws.amazon.com/acm/latest/userguide/gs-acm-validate-dns.html) and [validating with email](https://docs.aws.amazon.com/acm/latest/userguide/gs-acm-validate-email.html) from the ACM documentation for details.

Subsequent deployments do not need to regenerate the certificate unless the domain name and/or subdomain are changed.

## CloudFront distribution initialisation

Initial deployments, and deployments that change the configuration of the CloudFront distribution (by changing the domain name(s), redirection rules or index document configuration), will require the CloudFront distribution to be created or updated.  This can take some time as the configuration change must be replicated to all edge locations.

## CI/CD

The deployment process is designed to be easily integrated into CI/CD.  Simply call the above commands (with or without `npm`) in your pipeline.
