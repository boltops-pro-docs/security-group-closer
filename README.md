<!-- note marker start -->
**NOTE**: This repo contains only the documentation for the private BoltsOps Pro repo code.
Original file: https://github.com/boltopspro/security-group-closer/blob/master/README.md
The docs are publish so they are available for interested customers.
For access to the source code, you must be a paying BoltOps Pro subscriber.
If are interested, you can contact us at contact@boltops.com or https://www.boltops.com

<!-- note marker end -->

# Security Group Closer Function Blueprint

[![BoltOps Badge](https://img.boltops.com/boltops/badges/boltops-badge.png)](https://www.boltops.com)

This blueprint provisions a [Lambda Function](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-lambda-function.html) and [CloudWatch Event Rule](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-events-rule.html). The example can be customized and used as-is, or it can serve as an example to be modified.

* Includes an example of Lambda Layer
* Includes necessary Lambda Permission and IAM Role
* Optionally supports [VpcConfig](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-lambda-function.html#cfn-lambda-function-vpcconfig). Variables can be used to set different values for multiple environments.
* Supports [Lambda X-Ray tracing](https://docs.aws.amazon.com/lambda/latest/dg/lambda-x-ray.html)
* Can customize [ScheduledExpression](https://docs.aws.amazon.com/eventbridge/latest/userguide/scheduled-events.html) or [EventPattern](https://docs.aws.amazon.com/eventbridge/latest/userguide/aws-events.html).

## Usage

1. Add blueprint to Gemfile
2. Configure: configs/security-group-closer values
3. Deploy blueprint

## Add

Add the blueprint to your lono project's `Gemfile`.

```ruby
gem "security-group-closer", git: "git@github.com:boltopspro/security-group-closer.git"
```

## Configure

Use the [lono seed](https://lono.cloud/reference/lono-seed/) command to generate a starter config params files.

    LONO_ENV=development lono seed security-group-closer
    LONO_ENV=production  lono seed security-group-closer

The files in `config/security-group-closer` folder will look something like this:

    configs/security-group-closer/
    └── variables
        ├── development.rb
        └── production.rb

Configure the `configs/security-group-closer/variables` files.

## Deploy

Use the [lono cfn deploy](http://lono.cloud/reference/lono-cfn-deploy/) command to deploy.

    LONO_ENV=development lono cfn deploy security-group-closer --sure --no-wait
    LONO_ENV=production  lono cfn deploy security-group-closer --sure --no-wait

If you are using One AWS Account, use these commands instead: [One Account](docs/one-account.md).

## Schedule Expression

The default `ScheduleExpression` is `cron(15 10 * * ? *)`.  You can override this with `@schedule_expression`.  Example:

configs/security-group-closer/variables/development.rb:

```ruby
@schedule_expression = "rate(1 minute)"
```

The [ScheduledExpression](https://docs.aws.amazon.com/eventbridge/latest/userguide/scheduled-events.html) docs are helpful.

## Event Pattern

You can also use an event pattern instead of a Scheduled Expression with `@event_pattern`. Here's an example with that monitors for the S3 CreateBucket event:

configs/security-group-closer/variables/development.rb:

```ruby
@event_pattern = {
  source: ["aws.s3"],
  "detail-type": ["AWS API Call via CloudTrail"],
  detail: {
    eventName: ["CreateBucket"],
    eventSource: ["s3.amazonaws.com"],
  }
}
```

Here are the [EventPattern](https://docs.aws.amazon.com/eventbridge/latest/userguide/aws-events.html) docs.

### Lambda Function in VPC

To configure the Lambda function with VpcConfig set `@subnet_ids` and either `@vpc_id` or `@security_group_ids`.

* When the `@vpc_id` is set, the template creates a managed security group for you and the Lambda function is configured to use that security group.
* When `@security_group_ids` is set, the Lambda function will use those existing security groups.
* The subnet must be a private subnet with configured with a NAT.

Here's an example of the managed security group.

configs/security-group-closer/variables/development.rb:

```ruby
@subnet_ids = ["subnet-111"]
@vpc_id = "vpc-111"
```

For Lambda VPC to work, the subnet must be a private subnet configured with a NAT. The security group must also allow access. Here's an example that opens all ports for the VPC CIDR range:

```ruby
@security_group_ingress = [{
  CidrIp: lookup_output("vpc.VpcCidr"),
  IpProtocol: -1,
}]
```

Note, Lambda functions configured with VPCs may take much longer to deploy, typically 30-45 minutes. This is because Lambda creates and attaches an ENI to the Lambda function to make the VPC feature possible. If the function is deleted or updated, requiring replacement, the ENI takes 30-45m to be removed. Because of this, it is recommended to write code for your Lambda function code without the VpcConfig first. Get it working and then add VpcConfig at the end.

### X-Ray Tracing

Lambda X-Ray tracing is set to `Active` by default. You can disable this by setting `@tracing_config_mode = false`. Example:

configs/security-group-closer/variables/development.rb:

```ruby
@tracing_config_mode = false
```

You can also change the mode with the same `@tracing_config_mode` variable:

```ruby
@tracing_config_mode = "Active" # or "PassThrough"
```

