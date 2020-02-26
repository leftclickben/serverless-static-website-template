# Serverless static website template

Elegant, configurable, highly automated deployment mechanism for serverless static websites hosted with AWS S3, CloudFront and Lambda.

For background information, please read [this blog post](https://blog.leftclick.com.au/2020/02/26/_20200226-hosting-static-websites-with-s3-cloudfront-lambda/).  You can also comment there or contact [@leftclickben on Twitter](https://twitter.com/leftclickben) if you have questions.

## Features

+ Supports any frontend framework (static content, static generators and single-page applications)
+ Configurable, directory-level index documents
+ Configurable URL redirects between root domain and a subdomain
+ Automatic SSL certificates for custom domains with DNS or email validation
+ Automatic Route53 DNS configuration
+ Conform to organisational policies by creating content bucket in any region
+ Support for multiple stages

## Basic overview

Add your website content to `public/`, and then run the following command:

```
npm run deploy --stage=test --domain=test.example.org --zoneid=ABCD1234XYZ
```

This will deploy your website to `https://www.test.example.org` with DNS, SSL certificate, encryption and security automation, and a redirect from `test.example.org` to the `www` subdomain.

All of this is configurable, check out the documentation below for details.

## Documentation

+ [Usage](documentation/USAGE.md)
+ [Architecture](documentation/ARCHITECTURE.md)
+ [Configuration](documentation/CONFIGURATION.md)
+ [Operation](documentation/OPERATION.md)

## Issues

If you encounter any problems, please [report an issue](https://github.com/leftclickben/serverless-static-website-template/issues).
