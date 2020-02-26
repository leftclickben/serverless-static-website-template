# Usage details

You can use this repository in different ways depending on whether you are starting from scratch or if you have existing content and just need to deploy it.

## To create a new website

1. Fork this repository into a repository for your website, and clone that repository locally
1. Put your content into the `public` directory, replacing the dummy web page with your own content
1. Configure your service name, content region and default stage name in `package.json`
1. _Optional_ Create a Hosted Zone in Route53 for your desired domain name or an ancestor domain (you might already have this)
1. Deploy your website, either using `npm` or by calling the AWS CLI directly (see [Configuration](CONFIGURATION.md) and [Operation](OPERATION.md))

## To automate deployment of an existing website

1. Copy the `cloudformation` templates and the `scripts` in `package.json` into your existing repository
1. If your content is not in a top-level `public` directory, you can modify the `script` with key `deploy:content` in `package.json`
1. _Optional_ Configure your service name, content region and default stage name in `package.json`
1. _Optional_ Create a Hosted Zone in Route53 for your desired domain name or an ancestor domain (you might already have this); delete any existing DNS records that will conflict
1. Deploy your website, either using `npm` or by calling the AWS CLI directly (see [Configuration](CONFIGURATION.md) and [Operation](OPERATION.md))
