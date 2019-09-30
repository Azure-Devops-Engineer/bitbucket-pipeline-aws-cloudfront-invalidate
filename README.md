# Bitbucket Pipelines Pipe: AWS CloudFront create invalidation

Create an invalidation of your CloudFront distribution

Remove a file(s) from CloudFront edge caches before it expires. The next time a viewer requests the file, CloudFront returns to the origin to fetch the latest version of the file. This is specially useful to refresh the content served by static websites deployed to S3 buckets. For more information about file invalidation in CloudFront, please, check the AWS official docs (https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Invalidation.html).
You can use Amazon CloudFront to improve the performance of your website. CloudFront makes your website’s files (such as HTML, images, and video) available from data centers around the world (called edge locations). When a visitor requests a file from your website, CloudFront automatically redirects the request to a copy of the file at the nearest edge location. This results in faster download times than if the visitor had requested the content from a data center that is located farther away.

## YAML Definition

Add the following snippet to the script section of your `bitbucket-pipelines.yml` file:

```yaml
- pipe: atlassian/aws-cloudfront-invalidate:0.1.2
  variables:
    AWS_ACCESS_KEY_ID: "<string>"
    AWS_SECRET_ACCESS_KEY: "<string>"
    AWS_DEFAULT_REGION: "<string>"
    DISTRIBUTION_ID: "<string>"
    # PATHS: "<string>" # Optional
    # DEBUG: "<boolean>" # Optional
```
## Variables

| Variable              | Usage                                                       |
| --------------------- | ----------------------------------------------------------- |
| AWS_ACCESS_KEY_ID (*) | AWS access key id |
| AWS_SECRET_ACCESS_KEY (*) | AWS secret key |
| AWS_DEFAULT_REGION (*) | AWS region |
| DISTRIBUTION_ID (*)   | The id of your CloudFront distribution. |
| PATHS                   | List of white space separated paths to invalidate. Default: `/*`.|
| DEBUG                 | Turn on extra debug information. Default: `false`. |

_(*) = required variable._

## Prerequisites

1. CloudFront distribution set up. Here are the details about how to create a CloudFront distribution, with an AWS S3 origin: https://docs.aws.amazon.com/AmazonS3/latest/dev/website-hosting-cloudfront-walkthrough.html
2. CloudFront distribution id. It can obtained from the CloudFront AWS console: https://console.aws.amazon.com/cloudfront/

## Examples

Basic example creating and invalidation:

```yaml
script:
  - pipe: atlassian/aws-cloudfront-invalidate:0.1.2
    variables:
      AWS_ACCESS_KEY_ID: $AWS_ACCESS_KEY_ID
      AWS_SECRET_ACCESS_KEY: $AWS_SECRET_ACCESS_KEY
      AWS_DEFAULT_REGION: $AWS_DEFAULT_REGION
      DISTRIBUTION_ID: '123xyz'
```

Advanced example creating an invalidation for a list of paths:

```yaml
script:
  - pipe: atlassian/aws-cloudfront-invalidate:0.1.2
    variables:
      AWS_ACCESS_KEY_ID: $AWS_ACCESS_KEY_ID
      AWS_SECRET_ACCESS_KEY: $AWS_SECRET_ACCESS_KEY
      AWS_DEFAULT_REGION: $AWS_DEFAULT_REGION
      DISTRIBUTION_ID: '123xyz'
      PATHS: '/index.html /home.html'
```

Example using an `aws-s3-deploy` pipe to sync your files to S3 and triggering a distribution invalidation to refresh the CDN caches:
```
script:
  - pipe: atlassian/aws-s3-deploy:0.3.2
    variables:
      AWS_ACCESS_KEY_ID: $AWS_ACCESS_KEY_ID
      AWS_SECRET_ACCESS_KEY: $AWS_SECRET_ACCESS_KEY
      AWS_DEFAULT_REGION: 'us-east-1'
      S3_BUCKET: 'my-bucket-name'
      LOCAL_PATH: 'build'

  - pipe: atlassian/aws-cloudfront-invalidate:0.1.2
    variables:
      AWS_ACCESS_KEY_ID: $AWS_ACCESS_KEY_ID
      AWS_SECRET_ACCESS_KEY: $AWS_SECRET_ACCESS_KEY
      AWS_DEFAULT_REGION: $AWS_DEFAULT_REGION
      DISTRIBUTION_ID: '123xyz'
```
## Support
If you’d like help with this pipe, or you have an issue or feature request, [let us know on Community](https://community.atlassian.com/t5/forums/postpage/choose-node/true/interaction-style/qanda?add-tags=bitbucket-pipelines,pipes).

If you’re reporting an issue, please include:

- the version of the pipe
- relevant logs and error messages
- steps to reproduce