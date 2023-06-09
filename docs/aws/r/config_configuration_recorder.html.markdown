---
subcategory: "Config"
layout: "aws"
page_title: "AWS: aws_config_configuration_recorder"
description: |-
  Provides an AWS Config Configuration Recorder.
---

# Resource: aws_config_configuration_recorder

Provides an AWS Config Configuration Recorder. Please note that this resource **does not start** the created recorder automatically.

~> **Note:** _Starting_ the Configuration Recorder requires a [delivery channel](/docs/providers/aws/r/config_delivery_channel.html) (while delivery channel creation requires Configuration Recorder). This is why [`aws_config_configuration_recorder_status`](/docs/providers/aws/r/config_configuration_recorder_status.html) is a separate resource.

## Example Usage

```terraform
resource "aws_config_configuration_recorder" "foo" {
  name     = "example"
  role_arn = aws_iam_role.r.arn
}

data "aws_iam_policy_document" "assume_role" {
  statement {
    effect = "Allow"

    principals {
      type        = "Service"
      identifiers = ["config.amazonaws.com"]
    }

    actions = ["sts:AssumeRole"]
  }
}

resource "aws_iam_role" "r" {
  name               = "awsconfig-example"
  assume_role_policy = data.aws_iam_policy_document.assume_role.json
}
```

## Argument Reference

The following arguments are supported:

* `name` - (Optional) The name of the recorder. Defaults to `default`. Changing it recreates the resource.
* `role_arn` - (Required) Amazon Resource Name (ARN) of the IAM role. Used to make read or write requests to the delivery channel and to describe the AWS resources associated with the account. See [AWS Docs](http://docs.aws.amazon.com/config/latest/developerguide/iamrole-permissions.html) for more details.
* `recording_group` - (Optional) Recording group - see below.

### `recording_group`

* `all_supported` - (Optional) Specifies whether AWS Config records configuration changes for every supported type of regional resource (which includes any new type that will become supported in the future). Conflicts with `resource_types`. Defaults to `true`.
* `include_global_resource_types` - (Optional) Specifies whether AWS Config includes all supported types of _global resources_ with the resources that it records. Requires `all_supported = true`. Conflicts with `resource_types`.
* `resource_types` - (Optional) A list that specifies the types of AWS resources for which AWS Config records configuration changes (for example, `AWS::EC2::Instance` or `AWS::CloudTrail::Trail`). See [relevant part of AWS Docs](http://docs.aws.amazon.com/config/latest/APIReference/API_ResourceIdentifier.html#config-Type-ResourceIdentifier-resourceType) for available types. In order to use this attribute, `all_supported` must be set to false.

## Attributes Reference

In addition to all arguments above, the following attributes are exported:

* `id` - Name of the recorder

## Import

Configuration Recorder can be imported using the name, e.g.,

```
$ terraform import aws_config_configuration_recorder.foo example
```
