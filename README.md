# cloudformation-static-site

Create a static site in AWS with a custom domain, SSL certificate, and proper redirects using CloudFormation.

[Read an extensive post about this repo](https://blog.stefanolaru.com/create-a-static-site-on-aws-with-cloudformation)

Please update the `package.json` per your needs.

### Create the stack

The `create-stack` command is as below:

```shell
aws cloudformation create-stack \
--stack-name STACK_NAME \
--template-body file://template.yml \
--profile AWS_PROFILE \
--region us-east-1 \
--parameters ParameterKey=DomainName,ParameterValue=DOMAIN_NAME ParameterKey=HostedZoneId,ParameterValue=HOSTED_ZONE_ID
```

Where

-   `STACK_NAME` - is your stack name
-   `AWS_PROFILE` - is your [credentials profile](https://docs.aws.amazon.com/sdk-for-php/v3/developer-guide/guide_credentials_profiles.html) in the
-   `DOMAIN_NAME` - is your domain name (the naked domain, e.g. _example.com_)
-   `HOSTED_ZONE_ID` - is your Route53 zone for the domain, should look like _Z07414553OA4KK51T5EZA_

### Deploy the site

The `deploy` command will deploy your static site to the S3 bucket and invalidate the CloudFront cache. You may update as per your needs, by default the copy command excludes all paths in the current folder, in order to prevent deploying sensitive info.

```shell
aws s3 cp ./ s3://S3_BUCKET_NAME \
--acl public-read \
--profile AWS_PROFILE \
--recursive \
--exclude "*" \
--include index.html \
--include 404.html \
&& aws cloudfront create-invalidation \
--profile monithor \
--distribution-id DISTRIBUTION_ID \
--paths "/*"
```

Where:

-   `S3_BUCKET_NAME` - is your S3 bucket name
-   `AWS_PROFILE` - is your [credentials profile](https://docs.aws.amazon.com/sdk-for-php/v3/developer-guide/guide_credentials_profiles.html)
-   `DISTRIBUTION_ID` - is your CloudFront distribution ID
