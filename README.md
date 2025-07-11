My personal website, built with [Jekyll](https://jekyllrb.com/).

https://www.davidmerrick.me

# Running locally

Run `docker-compose up`
Visit http://localhost:4000

# Infrastructure

This site has CloudFormation templates for deployment to AWS. Its infrastructure consists of:
* S3 bucket
* Route53 Domain
* CloudFront distribution for edge caching
* Lambda@Edge to redirect non-www requests to the www subdomain

To create the AWS resources from the template, do the following.

### Set your env vars correctly
```bash
export AWS_PROFILE=cloudformation
export AWS_REGION=us-east-1
```

### Create the S3 bucket and allow permissions
Manually create the S3 bucket. Then run: 
```bash
aws s3api put-public-access-block \
        --bucket davidmerrick.me \
        --public-access-block-configuration \
          "BlockPublicAcls=true,IgnorePublicAcls=true,BlockPublicPolicy=false,RestrictPublicBuckets=false"
```

### Package the templates:
`aws cloudformation package --template-file etc/root.yaml --output-template packaged.yaml --s3-bucket io.github.davidmerrick.davidmerrickme.cloudformation`

### Deploy the templates:

_Note: Make sure to customize the parameter values_

`aws cloudformation deploy --template-file packaged.yaml --capabilities CAPABILITY_IAM --parameter-overrides Referer=myReferer --stack-name davidmerrickdotme`

Make SURE to update the cloudfront distribution id in the Github workflows.

### Manually approve the certificates

The certificates require manual approval in the AWS console via 
creating Route53 records to validate the domain ownership.

# Gotchas
- You need to manually create the S3 bucket which is annoying.
- Lambda@Edge doesn't allow env vars, so we've got to hardcode the domain name.
- Lambda@Edge has a 5-second limit on timeouts.

# Todo
- Convert CloudFormation templates to Terraform


# Manually building
```shell
docker run --rm \
  --volume "$PWD:/srv/jekyll" \
  --volume "$PWD/vendor/bundle:/usr/local/bundle" \
  -w /srv/jekyll \
  jekyll/builder:4 \
  jekyll build --destination ./_site
```