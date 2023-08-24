# Payment Plans

## Create a new payment plan

```shell
curl "https://partial.ly/api/payment_plan" \
  -H "Authorization: Bearer your_api_key" \
  -H "Content-Type: application/json" \
  -X POST \
  --data '{"amount": "1000", "customer_id": "452cc42f-d999-4c0f-998b-325c4e0e8f57", "offer_id": "60aed439-473f-48e0-80ef-3a8627dd243a"}'
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/payment_plan',
  headers: {
    Authorization: 'Bearer your_api_key'
  },
  method: 'POST',
  json: true,
  body: {
    amount: 1000,
    customer: {
      email: 'aaa@y.co',
      first_name: 'Testing',
      last_name: 'Person'
    },
    offer_id: '60aed439-473f-48e0-80ef-3a8627dd243a'
  }
};

request(options, function (error, response, body) {
  // asynchronous callback function
});
```

> The above command returns JSON structured like this:

```json
{
    "payment_schedule": {
        "term_units": "months",
        "term": 3,
        "starts_date": null,
        "starts_auto": true,
        "repay_by_date": "2019-02-28",
        "payment_amount": 256.25,
        "num_payments": 3,
        "inserted_at": "2018-11-28T17:20:46.753612",
        "id": "8e2db276-7b45-4f1b-b479-0c25544d90c2",
        "frequency_units": "months",
        "frequency": 1,
        "down_payment_amount": 256.25,
        "description": null,
        "contract_signed_date": null,
        "contract_signature": null,
        "contract_body": "By submitting your order and authorizing the charges on your card, you are legally bound to...",
        "balance": 768.75,
        "auto_process": true,
        "amount": 1025
    },
    "payment_plan": {
        "user_agent": null,
        "subtotal": 1000,
        "status": "checkout",
        "number": null,
        "meta": null,
        "merchant_notes": null,
        "ip_address": null,
        "integration_id": null,
        "integration": null,
        "inserted_at": "2018-11-28T17:20:46.711165",
        "id": "2ab17c1a-860d-4575-be11-dcd5bbab467d",
        "customer_id": "e3cbf1dc-0c11-483f-b604-d44fd93aac90",
        "customer": {
            "timezone": "America/New_York",
            "shipto_state": null,
            "shipto_postal_code": null,
            "shipto_name": null,
            "shipto_country": "US",
            "shipto_city": null,
            "shipto_address2": null,
            "shipto_address": null,
            "phone": null,
            "last_name": "Person",
            "inserted_at": "2018-11-28T16:57:42.923471",
            "id": "e3cbf1dc-0c11-483f-b604-d44fd93aac90",
            "first_name": "Testing",
            "email": "aaa@y.co"
        },
        "currency": "USD",
        "amount_paid": 0,
        "amount": 1025
    },
    "line_items": [
        {
            "quantity": 1,
            "meta": null,
            "inserted_at": "2018-11-28T17:20:46.766599",
            "id": "f345d9da-b67f-4056-8224-f7d9599f9439",
            "dynamic_type": "generic",
            "dynamic": true,
            "description": "processing fee",
            "amount": 25
        }
    ],
    "installments": [
        {
            "scheduled": "2018-12-28T17:20:46.752714Z",
            "retry_number": 0,
            "inserted_at": null,
            "id": null,
            "amount": 256.25
        },
        {
            "scheduled": "2019-01-28T17:20:46.752714Z",
            "retry_number": 0,
            "inserted_at": null,
            "id": null,
            "amount": 256.25
        },
        {
            "scheduled": "2019-02-28T17:20:46.752714Z",
            "retry_number": 0,
            "inserted_at": null,
            "id": null,
            "amount": 256.25
        }
    ]
}
```

Creates a new payment plan in *checkout* status. The payment schedule can automatically be created from an offer by sending an *offer_id* parameter, otherwise a *payment_schedule* can be specified. Likewise, either a *customer_id* for an existing customer can be specified, or a customer object can be sent and a new customer will be created, or the existing one attached.
To open the payment plan, use the [open](#open-a-payment-plan) method after it has been created.

A payment schedule will also be created and associated with the payment plan. The scheduled installments will also be generated and returned, but they will not be stored in the database until the payment plan is opened.

### HTTP Request

`POST /payment_plan`

### Parameters

Parameter | Type | Required | Description
--------- | -----------  | -------- | ------
amount | decimal | yes | total amount of the payment plan
customer_id | string | no | id of customer to associate with, required if customer not set
customer | object | no | [customer](#customers) object to associate with, required if customer_id not set
offer_id | string | no | id of offer to use to generate payment schedule, required if payment_schedule not set
payment_schedule | object | no | [payment schedule](#payment-schedules) parameters, required if offer_id not set
meta | object | no | any custom meta data. May also set line_items key to an array of `line_items` (see below)
currency | string | no | 3 letter currency code. Default to USD
ip_address | string | no | ip address of customer
user_agent | string | no | user agent of customer
shipto_name | string | no | shipping address name
shipto_address | string | no | street address
shipto_address2 | string | no | street address line 2
shipto_city | string | no | city
shipto_state | string | no | 2 letter state/region/province code
shipto_postal_code | string | no | zip/postal code
shipto_country | string | no | 2 letter country abbreviation
integration | string | no | third party integration to send payment plan to. shopify, woocommerce, bigcommerce, opencart, or prestashop
status | string | no | checkout or pending. default is checkout
send_plan_request | string | no | set to true to send a plan request email to customer to complete checkout. plan must be in pending status


### line_items
Parameter | Type | Required | Description
--------- | -----------  | -------- | ------
name | string | yes | description of the line item
price | decimal | yes | unit price of the line item
quantity | integer | yes | quantity
image | string | no | URL for an image to display in Partial.ly
weight | decimal | no | weight of the item
weight_units | string | no | lb, g, kg, oz
meta | object | no | additional meta data for line item. For Shopify integrations, meta.product_id and meta.variant_id must be set

## Open a payment plan

```shell
curl "https://partial.ly/api/payment_plan/open/ef2b5088-10cc-4246-914d-1f2de7a4075c" \
  -H "Authorization: Bearer your_api_key" \
  -H "Content-Type: application/json" \
  -X PUT \
  --data '{"payment_schedule": {"contract_signature": "Customer Signature"}, "payment_method": {"type": "card", "token_id": "tok_ch"}}'
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/payment_plan/open/ef2b5088-10cc-4246-914d-1f2de7a4075c',
  headers: {
    Authorization: 'Bearer your_api_key'
  },
  method: 'PUT',
  json: true,
  body: {
    payment_schedule: {
      contract_signature: 'Customer Signature'
    },
    payment_method: {
      type: 'card',
      token_id: 'tok_ch'
    }
  }
};

request(options, function (error, response, payment_plan) {
  // asynchronous callback function
});
```

> The above command returns JSON structured like this:

```json
{
    "user_agent": null,
    "subtotal": 6981,
    "status": "open",
    "number": 142,
    "meta": {
        "quickbooks_invoice_number": "1045",
        "description": "Payment Plan for invoice 1045"
    },
    "merchant_notes": null,
    "ip_address": null,
    "integration_id": "167",
    "integration": "quickbooks",
    "inserted_at": "2018-11-05T22:19:33.441694",
    "id": "ef2b5088-10cc-4246-914d-1f2de7a4075c",
    "customer_id": "b4b15b3b-281a-41be-9690-e7f51666b8ef",
    "currency": "USD",
    "amount_paid": 30,
    "amount": 7330.05
}
```

Opens a payment plan by signing the customer contract, attaching a payment method (new or existing), and processing the down payment if there is one. Only payment plans in *checkout* or *pending* status can be opened.

In case the supplied payment method requires 3d secure authentication (required for [Strong Customer Authentication](https://stripe.com/payments/strong-customer-authentication)), the resulting payment plan will have status "requires_action" and will contain the "redirect_url" key. In this scenario, you should redirect the user to the "redirect_url" to authorize the payment, after which point they will be redirected back to the "return_url" you provide.

### HTTP request

`PUT /payment_plan/open/:id`

*replace :id with the id of the payment plan to open*

### Parameters

Parameter | Type | Required | Description
--------- | -----------  | -------- | ------
payment_schedule.contract_signature | string | yes | customer's signature
payment_method.id | string | no | existing payment method id
payment_method.type | string | no | "card", required if payment_method.id not sent
payment_method.token_id | string | no | see [Payment Methods](#payment-methods) for details on creating new payment methods
return_url | string | no | your URL to redirect user to after 3d secure authentication

## Cancel a payment plan

```shell
curl "https://partial.ly/api/payment_plan/cancel/ef2b5088-10cc-4246-914d-1f2de7a4075c" \
  -H "Authorization: Bearer your_api_key" \
  -X PUT
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/payment_plan/cancel/ef2b5088-10cc-4246-914d-1f2de7a4075c',
  headers: {
    Authorization: 'Bearer your_api_key'
  },
  method: 'PUT'
};

request(options, function (error, response, body) {
  // asynchronous callback function
});
```

> The above command returns JSON structured like this:

```json
{
    "message": "PaymentPlan canceled"
}
```

Cancels a payment plan. Only payment plans in *open* or *pending* status may be canceled

### HTTP Request

`PUT /payment_plan/cancel/:id`
*replace :id with the id of the payment plan to cancel*

Parameter | Type | Required | Description
--------- | -----------  | -------- | ------
cancel_shopify | boolean | no | if this payment plan is for a Shopify order, also cancel the Shopify order
cancel_shopify_restock | boolean | no | if this payment plan is for a Shopify order, also restock the items from the order

## Update a payment plan

```shell
curl "https://partial.ly/api/payment_plan/cancel/8f999efe-5798-4b51-a6c5-d21b0d04124b" \
  -H "Authorization: Bearer your_api_key" \
  -H "Content-Type: application/json" \
  -X PUT \
  -- data '{"merchant_notes": "Customer is very happy"}'
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/payment_plan/8f999efe-5798-4b51-a6c5-d21b0d04124b',
  headers: {
    Authorization: 'Bearer your_api_key'
  },
  method: 'PUT',
  json: true,
  body: {
    merchant_notes: 'Customer is very happy'
  }
};

request(options, function (error, response, plan) {
  // asynchronous callback function
});
```

> The above command returns JSON structured like this:

```json
{
    "user_agent": null,
    "subtotal": 1000,
    "status": "open",
    "number": 140,
    "meta": {
        "items": [
            {
                "quantity": 1,
                "price": 900,
                "name": "Widget",
                "id": "widget-id"
            },
            {
                "quantity": 2,
                "price": 50,
                "name": "Small product",
                "id": "prod-sm"
            }
        ],
        "description": "Sample api plan"
    },
    "merchant_notes": "Customer is very happy",
    "ip_address": null,
    "integration_id": null,
    "integration": null,
    "inserted_at": "2018-11-28T17:13:14.181300",
    "id": "8f999efe-5798-4b51-a6c5-d21b0d04124b",
    "customer_id": "e3cbf1dc-0c11-483f-b604-d44fd93aac90",
    "currency": "USD",
    "amount_paid": 256.25,
    "amount": 1025
}
```

Updates an existing payment plan

### HTTP Request

`PUT /payment_plan/:id`
*replace :id with the id of the plan to update*

### Parameters

only the following properties may be updated

Parameter | Type | Description
--------- | -----------  | --------
merchant_notes | string | general notes, not visible to customer
payment_method_id | string | id of a payment method to use when processing payments for this plan
shipto_name | string |
shipto_address | string |
shipto_address2 | string |
shipto_city | string |
shipto_state | string |
shipto_postal_code | string |
shipto_country | string |
integration | string | third party service to integrate plan with, for example "shopify"
integration_id | string | third party service id, for example shopify order id

## Retrieve a payment plan

```shell
curl "https://partial.ly/api/payment_plan/cancel/8f999efe-5798-4b51-a6c5-d21b0d04124b" \
  -H "Authorization: Bearer your_api_key"
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/payment_plan/8f999efe-5798-4b51-a6c5-d21b0d04124b',
  headers: {
    Authorization: 'Bearer your_api_key'
  },
  method: 'GET'
};

request(options, function (error, response, plan) {
  // asynchronous callback function
});
```

> The above command returns JSON structured like this:

```json
{
    "refunds": [
        {
            "status": "succeeded",
            "reason": "requested_by_customer",
            "processor_id": "re_1DbaweEs15StTpSviCrp112t",
            "processor": "stripe",
            "notes": "discount",
            "inserted_at": "2018-11-28T21:51:46.423002",
            "id": "a0990737-9234-44fc-857b-eac7b4ef3cbf",
            "failure_reason": null,
            "failure_merchant_transfer_id": null,
            "amount": 5.99
        }
    ],
    "payments": [
        {
            "status": "paid",
            "retry_number": 0,
            "processor_id": "ch_1DbXklEs15StTpSvJfo02FRM",
            "processor_destination_id": "py_E3f42E5m2H1p8b",
            "processor": "stripe",
            "payment_plan_id": "8f999efe-5798-4b51-a6c5-d21b0d04124b",
            "paid_at": "2018-11-28T18:27:16",
            "message": null,
            "inserted_at": "2018-11-28T18:27:16.896811",
            "id": "2d1d0234-87c4-4184-a49f-02f4c0c30abe",
            "currency": "USD",
            "amount": 256.25
        },
        {
            "status": "paid",
            "retry_number": 0,
            "processor_id": "ch_1DbaigEs15StTpSvhzmfGjSA",
            "processor_destination_id": "py_E3i82Rh38K8Wg7",
            "processor": "stripe",
            "payment_plan_id": "8f999efe-5798-4b51-a6c5-d21b0d04124b",
            "paid_at": "2018-11-28T21:37:21",
            "message": null,
            "inserted_at": "2018-11-28T21:37:21.296658",
            "id": "291221ab-b0a2-41dc-909b-1d1793bd23d8",
            "currency": "USD",
            "amount": 256.25
        }
    ],
    "payment_schedule": {
        "term_units": "months",
        "term": 3,
        "starts_date": null,
        "starts_auto": true,
        "repay_by_date": "2019-02-28",
        "payment_amount": 256.25,
        "num_payments": 3,
        "inserted_at": "2018-11-28T17:13:14.235119",
        "id": "1f5d9716-a041-47ef-8c57-86af22efa31c",
        "frequency_units": "months",
        "frequency": 1,
        "down_payment_amount": 256.25,
        "description": null,
        "contract_signed_date": "2018-11-28T18:27:17",
        "contract_signature": "Customer Signature",
        "contract_body": "By submitting your order and authorizing...",
        "balance": 768.75,
        "auto_process": true,
        "amount": 1025
    },
    "payment_plan": {
        "user_agent": null,
        "subtotal": 1000,
        "status": "open",
        "number": 140,
        "offer_id": "e3cbf1dc-0c11-483f-b604-d44fd93aac90",
        "meta": {
            "items": [
                {
                    "quantity": 1,
                    "price": 900,
                    "name": "Widget",
                    "id": "widget-id"
                },
                {
                    "quantity": 2,
                    "price": 50,
                    "name": "Small product",
                    "id": "prod-sm"
                }
            ],
            "description": "Sample api plan"
        },
        "merchant_notes": "Customer is very happy",
        "ip_address": null,
        "integration_id": null,
        "integration": null,
        "inserted_at": "2018-11-28T17:13:14.181300",
        "id": "8f999efe-5798-4b51-a6c5-d21b0d04124b",
        "customer_id": "e3cbf1dc-0c11-483f-b604-d44fd93aac90",
        "customer": {
            "timezone": "America/New_York",
            "shipto_state": "FL",
            "shipto_postal_code": "33601",
            "shipto_name": "Sample Customer",
            "shipto_country": "US",
            "shipto_city": "Tampa",
            "shipto_address2": null,
            "shipto_address": "123 N. Tampa St.",
            "phone": "81325551212",
            "last_name": "Customer",
            "inserted_at": "2015-09-11T05:54:27.000000",
            "id": "6d30d3c8-d024-45cc-9c3f-356fc8aee23d",
            "first_name": "Sample",
            "email": "sample.customer@gmail.com"
        },
        "currency": "USD",
        "balance": 518.49,
        "amount_refunded": 5.99,
        "amount_pending": 0,
        "amount_paid": 512.5,
        "amount_disputed": 0,
        "amount": 1025
    },
    "line_items": [
        {
            "quantity": 1,
            "meta": {
                "quantity": 1,
                "price": 900,
                "name": "Widget",
                "id": "widget-id"
            },
            "inserted_at": "2018-11-28T17:13:14.262856",
            "id": "0da61119-ca96-410b-a1cb-ca059f2f8395",
            "dynamic_type": "generic",
            "dynamic": false,
            "description": "Widget",
            "amount": 900
        },
        {
            "quantity": 2,
            "meta": {
                "quantity": 2,
                "price": 50,
                "name": "Small product",
                "id": "prod-sm"
            },
            "inserted_at": "2018-11-28T17:13:14.264749",
            "id": "16aa3a67-5efb-42c4-8b3b-e59940a7423f",
            "dynamic_type": "generic",
            "dynamic": false,
            "description": "Small product",
            "amount": 50
        },
        {
            "quantity": 1,
            "meta": null,
            "inserted_at": "2018-11-28T17:13:14.249186",
            "id": "1b7c8912-ee07-4731-a301-503160dc3365",
            "dynamic_type": "generic",
            "dynamic": true,
            "description": "processing fee",
            "amount": 25
        }
    ],
    "installments": [
        {
            "scheduled": "2018-12-28T18:27:16.910618",
            "retry_number": 0,
            "inserted_at": "2018-11-28T18:27:16.911366",
            "id": "6a85b229-69e6-4850-8309-edf7bfc3cec1",
            "amount": 256.25
        },
        {
            "scheduled": "2019-01-28T18:27:16.910618",
            "retry_number": 0,
            "inserted_at": "2018-11-28T18:27:16.927898",
            "id": "0caa4d95-2252-4855-8f78-a0ecf426bf09",
            "amount": 256.25
        },
        {
            "scheduled": "2019-02-28T18:27:16.910618",
            "retry_number": 0,
            "inserted_at": "2018-11-28T18:27:16.931100",
            "id": "7a6c0aba-1b55-43dc-8367-d7fada4f1bfa",
            "amount": 256.25
        }
    ],
    "disputes": []
}
```

Retrieves an existing payment plan

### HTTP Request

`GET /payment_plan/:id`
*replace :id with the id of the plan*

## List all plans

```shell
curl "https://partial.ly/api/payment_plan?date=2018-11-28" \
  -H "Authorization: Bearer your_api_key"
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/payment_plan?date=2018-11-28',
  headers: {
    Authorization: 'Bearer your_api_key'
  }
};

request(options, function (error, response, body) {
  // asynchronous callback function
});
```

> The above command returns JSON structured like this:

```json
{
    "total_pages": 1,
    "total_entries": 1,
    "payment_plans": [
        {
            "user_agent": null,
            "subtotal": 1000,
            "status": "open",
            "number": 140,
            "offer_id": "e3cbf1dc-0c11-483f-b604-d44fd93aac90",
            "meta": {
                "items": [
                    {
                        "quantity": 1,
                        "price": 900,
                        "name": "Widget",
                        "id": "widget-id"
                    },
                    {
                        "quantity": 2,
                        "price": 50,
                        "name": "Small product",
                        "id": "prod-sm"
                    }
                ],
                "description": "Sample api plan"
            },
            "merchant_notes": "Customer is very happy",
            "ip_address": null,
            "integration_id": null,
            "integration": null,
            "inserted_at": "2018-11-28T17:13:14.181300",
            "id": "8f999efe-5798-4b51-a6c5-d21b0d04124b",
            "customer_id": "e3cbf1dc-0c11-483f-b604-d44fd93aac90",
            "customer": {
                "timezone": "America/New_York",
                "shipto_state": "FL",
                "shipto_postal_code": "33601",
                "shipto_name": "Sample Customer",
                "shipto_country": "US",
                "shipto_city": "Tampa",
                "shipto_address2": null,
                "shipto_address": "123 N. Tampa St.",
                "phone": "81325551212",
                "last_name": "Customer",
                "inserted_at": "2015-09-11T05:54:27.000000",
                "id": "6d30d3c8-d024-45cc-9c3f-356fc8aee23d",
                "first_name": "Sample",
                "email": "sample.customer@gmail.com"
            },
            "currency": "USD",
            "amount_paid": 256.25,
            "amount": 1025,
            "line_items": [
              {
                  "quantity": 1,
                  "meta": {
                      "quantity": 1,
                      "price": 900,
                      "name": "Widget",
                      "id": "widget-id"
                  },
                  "inserted_at": "2018-11-28T17:13:14.262856",
                  "id": "0da61119-ca96-410b-a1cb-ca059f2f8395",
                  "dynamic_type": "generic",
                  "dynamic": false,
                  "description": "Widget",
                  "amount": 900
              }
            ],
            "payment_schedule": {
                "term_units": "months",
                "term": 3,
                "starts_date": null,
                "starts_auto": true,
                "repay_by_date": "2019-02-28",
                "payment_amount": 256.25,
                "num_payments": 3,
                "inserted_at": "2018-11-28T17:13:14.235119",
                "id": "1f5d9716-a041-47ef-8c57-86af22efa31c",
                "frequency_units": "months",
                "frequency": 1,
                "down_payment_amount": 256.25,
                "description": null,
                "contract_signed_date": "2018-11-28T18:27:17",
                "contract_signature": "Customer Signature",
                "contract_body": "By submitting your order and authorizing...",
                "balance": 768.75,
                "auto_process": true,
                "amount": 1025,
                "installments": [
                    {
                        "scheduled": "2018-12-28T18:27:16.910618",
                        "retry_number": 0,
                        "inserted_at": "2018-11-28T18:27:16.911366",
                        "id": "6a85b229-69e6-4850-8309-edf7bfc3cec1",
                        "amount": 256.25
                    },
                    {
                        "scheduled": "2019-01-28T18:27:16.910618",
                        "retry_number": 0,
                        "inserted_at": "2018-11-28T18:27:16.927898",
                        "id": "0caa4d95-2252-4855-8f78-a0ecf426bf09",
                        "amount": 256.25
                    },
                    {
                        "scheduled": "2019-02-28T18:27:16.910618",
                        "retry_number": 0,
                        "inserted_at": "2018-11-28T18:27:16.931100",
                        "id": "7a6c0aba-1b55-43dc-8367-d7fada4f1bfa",
                        "amount": 256.25
                    }
                ]
            }
        }
    ],
    "page_size": 10,
    "page_number": 1
}
```

Lists all payment plans, with the optional filters listed below

### HTTP Request

`GET /payment_plan`

### Parameters

Parameter | Type | Description
--------- | -----------  | --------
status | string | plan status. checkout, pending, open, paid, canceled, or defaulted
currency | string | 3 letter currency code
date | date | plan created date. YYYY-MM-DD
dateRange | string | plans created in a range of dates separated by the &#124; character. Ex. use "2018-01-01&#124;2018-02-01"
customer | string | plans with the given customer id

## Send plan request email

```shell
curl "https://partial.ly/api/payment_plan/send_plan_request/8f999efe-5798-4b51-a6c5-d21b0d04124b" \
  -H "Authorization: Bearer your_api_key" \
  -H "Content-Type: application/json" \
  -X POST \
  --data ''
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/payment_plan/send_plan_request/8f999efe-5798-4b51-a6c5-d21b0d04124b',
  headers: {
    Authorization: 'Bearer your_api_key'
  },
  method: 'POST',
  json: true,
  body: {}
};

request(options, function (error, response, body) {
  // asynchronous callback function
});
```

> The above command returns JSON structured like this:

```json
{
    "message": "Plan request email sent"
}
```

Send an emailed request to the customer to open the payment plan via Partially checkout. The payment plan must be in pending, canceled, defaulted, or paused status, and the merchant account must have an active Stripe account connected. 

You can optionally choose to also update the payment plan status to pending, which will also allow the customer to open the payment plan from their portal.

### HTTP Request

`POST /payment_plan/send_plan_request/:id`

Parameter | Type | Description
--------- | -----------  | --------
update_status | string | can only be updated to `"pending"`
