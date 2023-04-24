---
layout: default
title: OTel trace group
parent: Processors
grand_parent: Pipelines
nav_order: 45
---

# OTel trace group processor

The OpenTelemetry (OTel) trace group processor completes missing trace group related fields in the collection of [span](https://github.com/opensearch-project/data-prepper/blob/834f28fdf1df6d42a6666e91e6407474b88e7ec6/data-prepper-api/src/main/java/org/opensearch/dataprepper/model/trace/Span.java) records by looking up the OpenSearch backend. The OTel trace group processor identifies the missing trace group information for a `spanId` by looking up the relevant fields in its root `span` stored in OpenSearch.

## OpenSearch

See the following example `YAML` configuration file for the OTel trace group processor when you connect to an OpenSearch cluster using your username and password:

``` YAML
pipeline:
  ...
  processor:
    - otel_trace_group:
        hosts: ["https://localhost:9200"]
        cert: path/to/cert
        username: YOUR_USERNAME_HERE
        password: YOUR_PASSWORD_HERE
```

See [OpenSearch security]({{site.url}}{{site.baseurl}}/data-prepper/pipelines/configuration/sinks/opensearch/#amazon-opensearch-service-domain-security) for a more detailed explanation of which OpenSearch credentials and permissions are required and how to configure those credentials for the OTel trace group processor.

### Amazon OpenSearch Service

See the following example `YAML` configuration file for the OTel trace group processor when you use Amazon OpenSearch Service:

``` YAML
pipeline:
  ...
  processor:
    - otel_trace_group:
        hosts: ["https://your-amazon-opensearch-service-endpoint"]
        aws_sigv4: true
        cert: path/to/cert
        insecure: false
```

## Configuration

You can configure the OTel trace group processor with the following options.

| Name | Description | Default value |
| -----| ----| -----------|
| `hosts`| A list of IP addresses of OpenSearch nodes. Required. | No default value. | 
| `cert` | A certificate authority (CA) certificate that is PEM-encoded. Accepts both .pem or .crt. This enables the client to trust the CA that has signed the certificate that OpenSearch is using. Optional. | `null` |
| `aws_sigv4` | A Boolean flag to sign the HTTP request with AWS credentials. Only applies to Amazon OpenSearch Service. See [OpenSearch security](https://github.com/opensearch-project/data-prepper/blob/129524227779ee35a327c27c3098d550d7256df1/data-prepper-plugins/opensearch/security.md) for details. Optional. | `false`. |
| `aws_region` | A string that represents the Region of Amazon OpenSearch Service domain, for example, `us-west-2`. Only applies to Amazon OpenSearch Service. Optional. | `us-east-1` |
| `aws_sts_role_arn`| An Identity and Access Management (IAM) role that the sink plugin assumes to sign the request to Amazon OpenSearch Service. If not provided, the plugin uses the [Default credentials](https://sdk.amazonaws.com/java/api/latest/software/amazon/awssdk/auth/credentials/DefaultCredentialsProvider.html). Optional. | `null` |
| `aws_sts_header_overrides` | A map of header overrides to make when assuming the IAM role for the sink plugin. Optional. | `null` |
| `insecure` | A Boolean flag to turn off SSL certificate verification. If set to `true`, CA certificate verification is turned off and insecure HTTP requests are sent. Optional. | `false` |
| `username` | A string that contains the username and is used in the [internal users](https://opensearch.org/docs/latest/security/access-control/users-roles/) of your OpenSearch cluster. Optional. | `null` |
| `password` | A string that contains the password and is used in the [internal users](https://opensearch.org/docs/latest/security/access-control/users-roles/) of your OpenSearch cluster. Optional. | `null` |

## Configuration option examples

You can define the configuration option values in the `aws_sts_header_overrides` option. See the following example.

```
aws_sts_header_overrides:
  x-my-custom-header-1: my-custom-value-1
  x-my-custom-header-2: my-custom-value-2
```

## Metrics

The following table describes custom metrics specific to the OTel trace group processor.

| Metric name | Type | Description |
| ------------- | ---- | ----------- |
| `recordsInMissingTraceGroup` | Counter | The number of ingress records missing trace group fields. | Custom |
| `recordsOutFixedTraceGroup` | Counter | The number of egress records with successfully completed trace group fields. | Custom |
| `recordsOutMissingTraceGroup` | Counter | The number of egress records missing trace group fields. | Custom |