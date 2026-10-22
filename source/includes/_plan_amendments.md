# Plan Amendments

Plan amendments let you add, edit and remove line items on payment plans that are already open, for example to upsell a customer an additional product or service, or to remove an item they no longer want.

Amending a plan is a two step process:

1. [Preview the amendment](#preview-a-plan-amendment) with the line item changes you want to make. This creates a `pending` plan amendment showing the plan's new totals, the remaining installments with their new amounts, and the contract for the customer to sign. Nothing on the payment plan changes yet.
2. Get the customer's agreement, either by [sending them the amendment](#send-a-plan-amendment) to review, sign and accept themselves, or by [confirming the amendment](#confirm-a-plan-amendment) with their signature collected in your own UI. Once confirmed, the plan's line items and totals are updated and a new payment schedule replaces the current one.

The remaining installments keep their scheduled dates, and the plan's new balance is spread across them. Amendments never add a new down payment.

Amendments that increase the plan's total must be signed by the customer. Amendments that don't increase the total, such as a discount or a removed item, can be confirmed without a new signature, unless you set `require_signature` when previewing.

A preview is calculated against the payment plan as it is at that moment. If the plan changes before the amendment is confirmed (an installment is charged, a payment or refund is made, another amendment is confirmed, a line item is edited), the amendment can no longer be confirmed and you'll receive a `409` response. Preview a new amendment in that case so the customer never agrees to one set of amounts and gets charged another.

Pending amendments expire 14 days after being previewed by default.

## Plan amendment statuses

Status | Description
------ | -----------
pending | previewed and waiting to be confirmed
confirmed | applied to the payment plan
canceled | canceled by you, or declined by the customer
expired | not confirmed before its `expires_at` date

## Preview a plan amendment

```shell
curl "https://partial.ly/api/plan_amendment" \
  -H "Authorization: Bearer your_api_key" \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -X POST \
  --data '{"payment_plan_id": "2ab17c1a-860d-4575-be11-dcd5bbab467d", "line_items": [{"action": "add", "description": "Photo package", "amount": 300.0, "quantity": 1}]}'
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/plan_amendment',
  headers: {
    Authorization: 'Bearer your_api_key',
    Accept: 'application/json',
    'Content-Type': 'application/json'
  },
  method: 'POST',
  json: true,
  body: {
    payment_plan_id: '2ab17c1a-860d-4575-be11-dcd5bbab467d',
    line_items: [
      {
        action: 'add',
        description: 'Photo package',
        amount: 300.0,
        quantity: 1
      }
    ]
  }
};

request(options, function (error, response, body) {
  // asynchronous callback function
});
```

> The above command returns JSON structured like this:

```json
{
    "plan_amendment": {
        "id": "b8f3c2a1-5d4e-4f6a-9b7c-1e2d3f4a5b6c",
        "payment_plan_id": "2ab17c1a-860d-4575-be11-dcd5bbab467d",
        "status": "pending",
        "require_signature": false,
        "requires_customer_signature": true,
        "changes": [
            {
                "action": "add",
                "id": null,
                "attrs": {
                    "description": "Photo package",
                    "amount": 300.0,
                    "quantity": 1
                },
                "before": null,
                "after": {
                    "id": null,
                    "description": "Photo package",
                    "amount": 300.0,
                    "quantity": 1,
                    "total": 300.0,
                    "dynamic": false,
                    "dynamic_type": "generic",
                    "meta": null,
                    "image": null,
                    "integration": null,
                    "integration_id": null
                }
            }
        ],
        "line_items": [
            {
                "id": "2faa91f1-7b01-4111-9af9-967e70c272e8",
                "description": "Wedding photography",
                "amount": 1000.0,
                "quantity": 1,
                "total": 1000.0,
                "dynamic": false,
                "dynamic_type": "generic",
                "meta": null,
                "image": null,
                "integration": null,
                "integration_id": null
            },
            {
                "id": null,
                "description": "Photo package",
                "amount": 300.0,
                "quantity": 1,
                "total": 300.0,
                "dynamic": false,
                "dynamic_type": "generic",
                "meta": null,
                "image": null,
                "integration": null,
                "integration_id": null
            }
        ],
        "installments": [
            {
                "scheduled": "2026-11-15T00:00:00Z",
                "amount": 275.0,
                "previous_amount": 200.0
            },
            {
                "scheduled": "2026-12-15T00:00:00Z",
                "amount": 275.0,
                "previous_amount": 200.0
            },
            {
                "scheduled": "2027-01-15T00:00:00Z",
                "amount": 275.0,
                "previous_amount": 200.0
            },
            {
                "scheduled": "2027-02-15T00:00:00Z",
                "amount": 275.0,
                "previous_amount": 200.0
            }
        ],
        "previous_amount": 1000.0,
        "amount": 1300.0,
        "previous_subtotal": 1000.0,
        "subtotal": 1300.0,
        "previous_balance": 800.0,
        "balance": 1100.0,
        "currency": "USD",
        "payment_schedule_id": null,
        "contract_body": "By accepting this amendment to your payment plan, you agree to...",
        "contract_signature": null,
        "meta": null,
        "expires_at": "2026-11-05T15:24:23Z",
        "confirmed_at": null,
        "canceled_at": null,
        "inserted_at": "2026-10-22T15:24:23"
    },
    "payment_plan": {
        "id": "2ab17c1a-860d-4575-be11-dcd5bbab467d",
        "status": "open",
        "number": 1042,
        "amount": 1000.0,
        "subtotal": 1000.0,
        "amount_paid": 200.0,
        "currency": "USD",
        "customer_id": "e3cbf1dc-0c11-483f-b604-d44fd93aac90",
        "meta": null,
        "merchant_notes": null,
        "integration": null,
        "integration_id": null,
        "ip_address": null,
        "user_agent": null,
        "inserted_at": "2026-09-15T17:20:46.711165"
    }
}
```

Validates a set of line item changes against an open payment plan and creates a `pending` plan amendment previewing the result. The payment plan isn't changed until the amendment is [confirmed](#confirm-a-plan-amendment).

Each entry in `line_items` is a change with an `action` of `add`, `update` or `remove`:

* `add` creates a new line item, using the line item fields below
* `update` changes an existing line item on the plan. Give its `id` and only the fields you want to change, for example `{"action": "update", "id": "2faa91f1-7b01-4111-9af9-967e70c272e8", "quantity": 2}`
* `remove` deletes an existing line item from the plan. Give only its `id`, for example `{"action": "remove", "id": "2faa91f1-7b01-4111-9af9-967e70c272e8"}`

Line item amounts can be negative, for example to give the customer a discount.

<aside class="notice">
Tax is not recalculated when a plan is amended. If the items you're adding or changing are taxable, add or update a tax line item (with <code>dynamic</code> set to true and <code>dynamic_type</code> set to "tax") in the same amendment.
</aside>

The response contains the pending `plan_amendment` and the `payment_plan` it applies to. The amendment's `line_items` are the plan's line items as they'll be after the amendment, and `installments` are the plan's remaining installments with their new `amount` and their `previous_amount`. Use `contract_body` to show the customer the updated contract, and `requires_customer_signature` to know whether you'll need their signature to confirm it.

### HTTP Request

`POST /plan_amendment`

### Parameters
Parameter | Type | Required | Description
--------- | -----------  | -------- | ------
payment_plan_id | string | yes | id of the open payment plan to amend
line_items | array | yes | list of line item changes, see below
expires_at | string | no | ISO 8601 date and time the amendment can be confirmed until. Defaults to 14 days from now
require_signature | boolean | no | require the customer's signature to confirm, even if the amendment doesn't increase the plan total. Defaults to false
meta | object | no | additional meta information about the amendment, for your own use

### Line item change parameters
Parameter | Type | Required | Description
--------- | -----------  | -------- | ------
action | string | yes | "add", "update" or "remove"
id | string | update and remove | id of the line item on the plan to update or remove
description | string | add | the line item's description
amount | decimal | add | the unit price of the line item, in the plan's currency. May be positive or negative
quantity | integer | no | the quantity of the line item. Defaults to 1 if not specified
image | string | no | URL to an image for the line item
weight | decimal | no | weight of the line item
weight_units | string | no | g, kg, oz, or lb. defaults to lb
dynamic | boolean | no | true for line items such as tax or shipping
dynamic_type | string | no | type of dynamic line item, ex. "tax" or "shipping"
meta | object | no | additional meta information about the line item
integration | string | no | third party service to send line item to, ex. "shopify" or "bigcommerce"
integration_id | string | no | line item id for third party service

## Get a plan amendment

```shell
curl "https://partial.ly/api/plan_amendment/b8f3c2a1-5d4e-4f6a-9b7c-1e2d3f4a5b6c" \
  -H "Authorization: Bearer your_api_key" \
  -H "Accept: application/json"
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/plan_amendment/b8f3c2a1-5d4e-4f6a-9b7c-1e2d3f4a5b6c',
  headers: {
    Authorization: 'Bearer your_api_key',
    Accept: 'application/json'
  }
};

request(options, function (error, response, body) {
  // asynchronous callback function
});
```

> The above command returns JSON structured like the preview a plan amendment response

Retrieves a plan amendment and the payment plan it applies to.

### HTTP Request

`GET /plan_amendment/:id`

*replace :id with the id of the plan amendment*

## Send a plan amendment

```shell
curl "https://partial.ly/api/plan_amendment/b8f3c2a1-5d4e-4f6a-9b7c-1e2d3f4a5b6c/send" \
  -H "Authorization: Bearer your_api_key" \
  -H "Accept: application/json" \
  -X POST
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/plan_amendment/b8f3c2a1-5d4e-4f6a-9b7c-1e2d3f4a5b6c/send',
  headers: {
    Authorization: 'Bearer your_api_key',
    Accept: 'application/json'
  },
  method: 'POST'
};

request(options, function (error, response, body) {
  // asynchronous callback function
});
```

> The above command returns JSON structured like the preview a plan amendment response

Emails the customer a link to review the changes to their payment plan and the updated contract, where they can sign and accept the amendment, or decline it. Accepting confirms the amendment, and declining cancels it. Only pending amendments can be sent.

This is the simplest way to get a customer's signature on an amendment that increases their plan total.

### HTTP Request

`POST /plan_amendment/:id/send`

*replace :id with the id of the plan amendment to send*

## Confirm a plan amendment

```shell
curl "https://partial.ly/api/plan_amendment/b8f3c2a1-5d4e-4f6a-9b7c-1e2d3f4a5b6c/confirm" \
  -H "Authorization: Bearer your_api_key" \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -X POST \
  --data '{"contract_signature": "Customer Signature"}'
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/plan_amendment/b8f3c2a1-5d4e-4f6a-9b7c-1e2d3f4a5b6c/confirm',
  headers: {
    Authorization: 'Bearer your_api_key',
    Accept: 'application/json',
    'Content-Type': 'application/json'
  },
  method: 'POST',
  json: true,
  body: {
    contract_signature: 'Customer Signature'
  }
};

request(options, function (error, response, body) {
  // asynchronous callback function
});
```

> The above command returns JSON structured like the preview a plan amendment response, with the amendment's status "confirmed" and the payment plan's updated totals

Confirms a pending plan amendment and applies it to the payment plan. The plan's line items and totals are updated, a new signed payment schedule with the amended installments replaces the plan's current schedule, and a [plan_amended](#plan_amended-event) webhook event is sent.

If the amendment's `requires_customer_signature` is true, you must include the customer's `contract_signature`, collected after showing them the amendment's `contract_body`. Otherwise you can leave it out, and the signature on the plan's current payment schedule is used.

If the payment plan has changed since the amendment was previewed, a `409` response is returned and you should [preview](#preview-a-plan-amendment) a new amendment.

### HTTP Request

`POST /plan_amendment/:id/confirm`

*replace :id with the id of the plan amendment to confirm*

### Parameters
Parameter | Type | Required | Description
--------- | -----------  | -------- | ------
contract_signature | string | no | the customer's signature. Required if the amendment's `requires_customer_signature` is true

## Cancel a plan amendment

```shell
curl "https://partial.ly/api/plan_amendment/b8f3c2a1-5d4e-4f6a-9b7c-1e2d3f4a5b6c/cancel" \
  -H "Authorization: Bearer your_api_key" \
  -H "Accept: application/json" \
  -X POST
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/plan_amendment/b8f3c2a1-5d4e-4f6a-9b7c-1e2d3f4a5b6c/cancel',
  headers: {
    Authorization: 'Bearer your_api_key',
    Accept: 'application/json'
  },
  method: 'POST'
};

request(options, function (error, response, body) {
  // asynchronous callback function
});
```

> The above command returns JSON structured like the preview a plan amendment response, with the amendment's status "canceled"

Cancels a pending plan amendment so it can no longer be confirmed. The payment plan isn't changed. Only pending amendments can be canceled.

### HTTP Request

`POST /plan_amendment/:id/cancel`

*replace :id with the id of the plan amendment to cancel*
