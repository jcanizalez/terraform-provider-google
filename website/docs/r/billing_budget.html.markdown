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
subcategory: "Cloud Billing"
layout: "google"
page_title: "Google: google_billing_budget"
sidebar_current: "docs-google-billing-budget"
description: |-
  Budget configuration for a billing account.
---

# google\_billing\_budget

Budget configuration for a billing account.


To get more information about Budget, see:

* [API documentation](https://cloud.google.com/billing/docs/reference/budget/rest/v1/billingAccounts.budgets)
* How-to Guides
    * [Creating a budget](https://cloud.google.com/billing/docs/how-to/budgets)

~> **Warning:** If you are using User ADCs (Application Default Credentials) with this resource,
you must specify a `billing_project` and set `user_project_override` to true
in the provider configuration. Otherwise the Billing Budgets API will return a 403 error.
Your account must have the `serviceusage.services.use` permission on the
`billing_project` you defined.

## Example Usage - Billing Budget Basic


```hcl
data "google_billing_account" "account" {
  billing_account = "000000-0000000-0000000-000000"
}

resource "google_billing_budget" "budget" {
  billing_account = data.google_billing_account.account.id
  display_name = "Example Billing Budget"
  amount {
    specified_amount {
      currency_code = "USD"
      units = "100000"
    }
  }
  threshold_rules {
      threshold_percent =  0.5
  }
}
```
## Example Usage - Billing Budget Lastperiod


```hcl
data "google_billing_account" "account" {
  billing_account = "000000-0000000-0000000-000000"
}

data "google_project" "project" {
}

resource "google_billing_budget" "budget" {
  billing_account = data.google_billing_account.account.id
  display_name = "Example Billing Budget"
  
  budget_filter {
    projects = ["projects/${data.google_project.project.number}"]
  }

  amount {
    last_period_amount = true
  }

  threshold_rules {
      threshold_percent =  10.0
      # Typically threshold_percent would be set closer to 1.0 (100%).
      # It has been purposely set high (10.0 / 1000%) in this example
      # so it does not trigger alerts during automated testing.
  }
}
```
## Example Usage - Billing Budget Filter


```hcl
data "google_billing_account" "account" {
  billing_account = "000000-0000000-0000000-000000"
}

data "google_project" "project" {
}

resource "google_billing_budget" "budget" {
  billing_account = data.google_billing_account.account.id
  display_name = "Example Billing Budget"

  budget_filter {
    projects = ["projects/${data.google_project.project.number}"]
    credit_types_treatment = "EXCLUDE_ALL_CREDITS"
    services = ["services/24E6-581D-38E5"] # Bigquery
  }

  amount {
    specified_amount {
      currency_code = "USD"
      units = "100000"
    }
  }

  threshold_rules {
    threshold_percent = 0.5
  }
  threshold_rules {
    threshold_percent = 0.9
    spend_basis = "FORECASTED_SPEND"
  }
}
```
## Example Usage - Billing Budget Notify


```hcl
data "google_billing_account" "account" {
  billing_account = "000000-0000000-0000000-000000"
}

data "google_project" "project" {
}

resource "google_billing_budget" "budget" {
  billing_account = data.google_billing_account.account.id
  display_name    = "Example Billing Budget"

  budget_filter {
    projects = ["projects/${data.google_project.project.number}"]
  }

  amount {
    specified_amount {
      currency_code = "USD"
      units         = "100000"
    }
  }

  threshold_rules {
    threshold_percent = 1.0
  }
  threshold_rules {
    threshold_percent = 1.0
    spend_basis       = "FORECASTED_SPEND"
  }
  
  all_updates_rule {
    monitoring_notification_channels = [
      google_monitoring_notification_channel.notification_channel.id,
    ]
    disable_default_iam_recipients = true
  }
}

resource "google_monitoring_notification_channel" "notification_channel" {
  display_name = "Example Notification Channel"
  type         = "email"
  
  labels = {
    email_address = "address@example.com"
  }
}
```

## Argument Reference

The following arguments are supported:


* `amount` -
  (Required)
  The budgeted amount for each usage period.
  Structure is documented below.

* `threshold_rules` -
  (Required)
  Rules that trigger alerts (notifications of thresholds being
  crossed) when spend exceeds the specified percentages of the
  budget.
  Structure is documented below.

* `billing_account` -
  (Required)
  ID of the billing account to set a budget on.


The `amount` block supports:

* `specified_amount` -
  (Optional)
  A specified amount to use as the budget. currencyCode is
  optional. If specified, it must match the currency of the
  billing account. The currencyCode is provided on output.
  Structure is documented below.

* `last_period_amount` -
  (Optional)
  Configures a budget amount that is automatically set to 100% of
  last period's spend.
  Boolean. Set value to true to use. Do not set to false, instead
  use the `specified_amount` block.


The `specified_amount` block supports:

* `currency_code` -
  (Optional)
  The 3-letter currency code defined in ISO 4217.

* `units` -
  (Optional)
  The whole units of the amount. For example if currencyCode
  is "USD", then 1 unit is one US dollar.

* `nanos` -
  (Optional)
  Number of nano (10^-9) units of the amount.
  The value must be between -999,999,999 and +999,999,999
  inclusive. If units is positive, nanos must be positive or
  zero. If units is zero, nanos can be positive, zero, or
  negative. If units is negative, nanos must be negative or
  zero. For example $-1.75 is represented as units=-1 and
  nanos=-750,000,000.

The `threshold_rules` block supports:

* `threshold_percent` -
  (Required)
  Send an alert when this threshold is exceeded. This is a
  1.0-based percentage, so 0.5 = 50%. Must be >= 0.

* `spend_basis` -
  (Optional)
  The type of basis used to determine if spend has passed
  the threshold.
  Default value is `CURRENT_SPEND`.
  Possible values are `CURRENT_SPEND` and `FORECASTED_SPEND`.

- - -


* `display_name` -
  (Optional)
  User data for display name in UI. Must be <= 60 chars.

* `budget_filter` -
  (Optional)
  Filters that define which resources are used to compute the actual
  spend against the budget.
  Structure is documented below.

* `all_updates_rule` -
  (Optional)
  Defines notifications that are sent on every update to the
  billing account's spend, regardless of the thresholds defined
  using threshold rules.
  Structure is documented below.


The `budget_filter` block supports:

* `projects` -
  (Optional)
  A set of projects of the form projects/{project_number},
  specifying that usage from only this set of projects should be
  included in the budget. If omitted, the report will include
  all usage for the billing account, regardless of which project
  the usage occurred on.

* `credit_types_treatment` -
  (Optional)
  Specifies how credits should be treated when determining spend
  for threshold calculations.
  Default value is `INCLUDE_ALL_CREDITS`.
  Possible values are `INCLUDE_ALL_CREDITS`, `EXCLUDE_ALL_CREDITS`, and `INCLUDE_SPECIFIED_CREDITS`.

* `services` -
  (Optional)
  A set of services of the form services/{service_id},
  specifying that usage from only this set of services should be
  included in the budget. If omitted, the report will include
  usage for all the services. The service names are available
  through the Catalog API:
  https://cloud.google.com/billing/v1/how-tos/catalog-api.

* `credit_types` -
  (Optional)
  A set of subaccounts of the form billingAccounts/{account_id},
  specifying that usage from only this set of subaccounts should
  be included in the budget. If a subaccount is set to the name of
  the parent account, usage from the parent account will be included.
  If the field is omitted, the report will include usage from the parent
  account and all subaccounts, if they exist.

* `subaccounts` -
  (Optional)
  A set of subaccounts of the form billingAccounts/{account_id},
  specifying that usage from only this set of subaccounts should
  be included in the budget. If a subaccount is set to the name of
  the parent account, usage from the parent account will be included.
  If the field is omitted, the report will include usage from the parent
  account and all subaccounts, if they exist.

* `labels` -
  (Optional)
  A single label and value pair specifying that usage from only
  this set of labeled resources should be included in the budget.

The `all_updates_rule` block supports:

* `pubsub_topic` -
  (Optional)
  The name of the Cloud Pub/Sub topic where budget related
  messages will be published, in the form
  projects/{project_id}/topics/{topic_id}. Updates are sent
  at regular intervals to the topic.

* `schema_version` -
  (Optional)
  The schema version of the notification. Only "1.0" is
  accepted. It represents the JSON schema as defined in
  https://cloud.google.com/billing/docs/how-to/budgets#notification_format.

* `monitoring_notification_channels` -
  (Optional)
  The full resource name of a monitoring notification
  channel in the form
  projects/{project_id}/notificationChannels/{channel_id}.
  A maximum of 5 channels are allowed.

* `disable_default_iam_recipients` -
  (Optional)
  Boolean. When set to true, disables default notifications sent
  when a threshold is exceeded. Default recipients are
  those with Billing Account Administrators and Billing
  Account Users IAM roles for the target account.

## Attributes Reference

In addition to the arguments listed above, the following computed attributes are exported:

* `id` - an identifier for the resource with format `billingAccounts/{{billing_account}}/budgets/{{name}}`

* `name` -
  Resource name of the budget. The resource name
  implies the scope of a budget. Values are of the form
  billingAccounts/{billingAccountId}/budgets/{budgetId}.


## Timeouts

This resource provides the following
[Timeouts](/docs/configuration/resources.html#timeouts) configuration options:

- `create` - Default is 4 minutes.
- `update` - Default is 4 minutes.
- `delete` - Default is 4 minutes.

## Import


Budget can be imported using any of these accepted formats:

```
$ terraform import google_billing_budget.default billingAccounts/{{billing_account}}/budgets/{{name}}
$ terraform import google_billing_budget.default {{billing_account}}/{{name}}
$ terraform import google_billing_budget.default {{name}}
```
