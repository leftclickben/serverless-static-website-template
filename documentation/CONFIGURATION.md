# Configuration of static website deployments

There are several configuration parameters that affect the way the static website works.  For more general information on deploying and running your website, see [Operation](OPERATION.md).

## Parameters

The deployment scripts support the following configuration parameters:

 | `npm` command parameter | `aws cloudformation` parameter | Default value | Description |
 |-----|-----|-----|-----|
 | _configured in `package.json`_ | `Service` | _required_ |
 | `--stage` | `Stage` | `npm` gives default of `development` as configured in `package.json` | The stage name, e.g. `development` or `production` |
 | `--domain` | `DomainName` | _required_ | The root domain name to configure, used for certificates and DNS configuration |
 | `--subdomain` | `Subdomain` | `www` | The subdomain to prepend to the root domain, unless the redirection mode is `none` |
 | `--redirect` | `DomainRedirectMode` | `root_to_sub` | The domain redirection mode, either `root_to_sub`, `sub_to_root` or `none` (see below) |
 | `--validation` | `CertificateValidationMethod` | `DNS` | The validation method to use for SSL certificates, either `DNS` (recommended) or `EMAIL` |
 | `--index` | `IndexDocument` | `index.html` | The document to serve for directory-level URLs, e.g. `/path/to/` or `/path/to` will return the content of `/path/to/index.html` |
 | `--zoneid` | `Route53HostedZoneId` | (optional) | The Route53 hosted zone ID in which to create DNS record sets; if omitted, no records are created |

 Note that parameters must be specified on every deployment, or customisations will regress.  For example, if a site is deployed with a zone ID, it configures Route53 record sets in that zone for the specified domain names.  If it is subsequently deployed without specifying a zone ID, those record sets will be deleted.


## Domain names and certificate validation

Because the distribution template contains an `AWS::ACM::Certificate` resource, that certificate will need to be validated when the template is first deployed for a given domain name.  This is not required subsequently, even if the stack is deleted and then re-deployed from scratch (with the same domain name).

Certificate validation is also possible via email, by overriding the `CertificateValidationMethod` parameter.  However, validation is required every time, and it must go to one of 5 predefined email addresses within the domain or a parent domain, so DNS validation is much easier.

### Redirection modes

There are three possible values for the `DomainRedirectMode` parameter, which affect various components of the stack:

| `DomainRedirectMode` value | Default? | Subdomain created<sup>1</sup>? | `viewer-request` function attached? | Lambda@Edge role created?
|-----|-----|-----|-----|-----
| `root_to_sub` | yes | yes | yes | yes
| `sub_to_root` | no | yes | yes | yes
| `none` | no | no | no | only if `IndexDocument` is set

<sup>1</sup> CloudFront alias, certificate SAN and DNS entries

### Index document

The index document configuration allows you to serve a different file, other than `index.html` (e.g. `default.htm`), when a directory is requested.

### DNS record generation

If a Route53 Zone ID is supplied to the distribution template, then it will create `A` and `AAAA` alias records for the relevant domains in that zone.  You need to ensure that the zone is relevant for the specified domain name.

If you do not specify a Zone ID, you are responsible for manually creating DNS records.  If this is in Route53, use alias records.  Outside of Route53, use a `CNAME` record with the native `cloudfront.net` domain name of the distribution.  You can see this in the `DistributionDomainName` output of the distribution stack.
