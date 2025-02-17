---
# ----------------------------------------------------------------------------
#
#     ***     AUTO GENERATED CODE    ***    AUTO GENERATED CODE     ***
#
# ----------------------------------------------------------------------------
#
#     This file is automatically generated by Magic Modules and manual
#     changes will be clobbered when the file is regenerated.
#
#     Please read more about how to change this file in
#     .github/CONTRIBUTING.md.
#
# ----------------------------------------------------------------------------
subcategory: "Service Usage"
layout: "google"
page_title: "Google: google_service_usage_consumer_quota_override"
sidebar_current: "docs-google-service-usage-consumer-quota-override"
description: |-
  A consumer override is applied to the consumer on its own authority to limit its own quota usage.
---

# google\_service\_usage\_consumer\_quota\_override

A consumer override is applied to the consumer on its own authority to limit its own quota usage.
Consumer overrides cannot be used to grant more quota than would be allowed by admin overrides,
producer overrides, or the default limit of the service.

~> **Warning:** This resource is in beta, and should be used with the terraform-provider-google-beta provider.
See [Provider Versions](https://terraform.io/docs/providers/google/guides/provider_versions.html) for more details on beta resources.

To get more information about ConsumerQuotaOverride, see:

* How-to Guides
    * [Getting Started](https://cloud.google.com/service-usage/docs/getting-started)
    * [REST API documentation](https://cloud.google.com/service-usage/docs/reference/rest/v1beta1/services.consumerQuotaMetrics.limits.consumerOverrides)

## Example Usage - Consumer Quota Override


```hcl
resource "google_project" "my_project" {
  provider   = google-beta
  name       = "tf-test-project"
  project_id = "quota"
  org_id     = "123456789"
}

resource "google_service_usage_consumer_quota_override" "override" {
  provider       = google-beta
  project        = google_project.my_project.project_id
  service        = "servicemanagement.googleapis.com"
  metric         = "servicemanagement.googleapis.com%2Fdefault_requests"
  limit          = "%2Fmin%2Fproject"
  override_value = "95"
  force          = true
}
```
## Example Usage - Region Consumer Quota Override


```hcl
resource "google_project" "my_project" {
  provider   = google-beta
  name       = "tf-test-project"
  project_id = "quota"
  org_id     = "123456789"
}

resource "google_service_usage_consumer_quota_override" "override" {
  provider       = google-beta
  dimensions = {
    region = "us-central1"
  }
  project        = google_project.my_project.project_id
  service        = "compute.googleapis.com"
  metric         = "compute.googleapis.com%2Fn2_cpus"
  limit          = "%2Fproject%2Fregion"
  override_value = "8"
  force          = true
}
```

## Argument Reference

The following arguments are supported:


* `override_value` -
  (Required)
  The overriding quota limit value. Can be any nonnegative integer, or -1 (unlimited quota).

* `service` -
  (Required)
  The service that the metrics belong to, e.g. `compute.googleapis.com`.

* `metric` -
  (Required)
  The metric that should be limited, e.g. `compute.googleapis.com/cpus`.

* `limit` -
  (Required)
  The limit on the metric, e.g. `/project/region`.


- - -


* `dimensions` -
  (Optional)
  If this map is nonempty, then this override applies only to specific values for dimensions defined in the limit unit.

* `force` -
  (Optional)
  If the new quota would decrease the existing quota by more than 10%, the request is rejected.
  If `force` is `true`, that safety check is ignored.

* `project` - (Optional) The ID of the project in which the resource belongs.
    If it is not provided, the provider project is used.


## Attributes Reference

In addition to the arguments listed above, the following computed attributes are exported:

* `id` - an identifier for the resource with format `projects/{{project}}/services/{{service}}/consumerQuotaMetrics/{{metric}}/limits/{{limit}}/consumerOverrides/{{name}}`

* `name` -
  The server-generated name of the quota override.


## Timeouts

This resource provides the following
[Timeouts](/docs/configuration/resources.html#timeouts) configuration options:

- `create` - Default is 4 minutes.
- `update` - Default is 4 minutes.
- `delete` - Default is 4 minutes.

## Import


ConsumerQuotaOverride can be imported using any of these accepted formats:

```
$ terraform import google_service_usage_consumer_quota_override.default projects/{{project}}/services/{{service}}/consumerQuotaMetrics/{{metric}}/limits/{{limit}}/consumerOverrides/{{name}}
$ terraform import google_service_usage_consumer_quota_override.default {{project}}/{{service}}/{{metric}}/{{limit}}/{{name}}
$ terraform import google_service_usage_consumer_quota_override.default {{service}}/{{metric}}/{{limit}}/{{name}}
```

## User Project Overrides

This resource supports [User Project Overrides](https://www.terraform.io/docs/providers/google/guides/provider_reference.html#user_project_override).
