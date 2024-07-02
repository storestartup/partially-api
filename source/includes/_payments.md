# Payments

Payments can have a *paid*, *pending*, or *failed* status. Payments made with a bank account payment method
will be pending for up to 5 business days while we wait for them to be confirmed.

## Create a payment

> Pay the entire balance

```shell
curl "https://partial.ly/api/payment/create" \
  -H "Authorization: Bearer your_api_key" \
  -H "Content-Type: application/json" \
  -X POST \
  -- data '{"payment_plan_id": "1597c18b-ffd4-4641-b2ca-78cccd3547f5", "amount": 241.87}'
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/payment/create',
  headers: {
    Authorization: 'Bearer your_api_key'
  },
  method: 'POST',
  json: true,
  body: {
    payment_plan_id: '1597c18b-ffd4-4641-b2ca-78cccd3547f5',
    amount: 241.87
  }
};

request(options, function (error, response, payment) {
  // asynchronous callback function
});
```

> The above command returns JSON structured like this:

```json
{
    "status": "paid",
    "retry_number": 0,
    "processor_id": "ch_1DbYxLEs15StTpSvFVKDzPIx",
    "processor_destination_id": null,
    "processor": "stripe",
    "payment_plan_id": "1597c18b-ffd4-4641-b2ca-78cccd3547f5",
    "paid_at": "2018-11-28T19:44:21",
    "message": null,
    "inserted_at": "2018-11-28T19:44:21.874251",
    "id": "2c84ae4a-962c-479b-bed4-4696ef25099d",
    "currency": "USD",
    "amount": 241.87,
    "fee": 12.39,
    "customer_fee_amount": 0.0
}
```

> Making a payment for an amount less than the balance

```shell
curl "https://partial.ly/api/payment/create" \
  -H "Authorization: Bearer your_api_key" \
  -H "Content-Type: application/json" \
  -X POST \
  -- data '{"payment_plan_id": "027bb022-40b4-4763-945c-baddc612cbbb", "amount": 5.99}'
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/payment/create',
  headers: {
    Authorization: 'Bearer your_api_key'
  },
  method: 'POST',
  json: true,
  body: {
    payment_plan_id: '027bb022-40b4-4763-945c-baddc612cbbb',
    amount: 5.99
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
        "term": 4,
        "starts_date": null,
        "starts_auto": true,
        "repay_by_date": "2019-02-25",
        "payment_amount": 116.91,
        "num_payments": 8,
        "inserted_at": null,
        "id": null,
        "frequency_units": "weeks",
        "frequency": 2,
        "down_payment_amount": 5.99,
        "description": null,
        "contract_signed_date": null,
        "contract_signature": null,
        "contract_body": "By submitting your order and authorizing the charges on your card, you are legally bound to the following terms:\r\n\r\nLayaway Agreement\r\n\r\nThis Layaway Agreement (\"Agreement\") is made...",
        "balance": 935.26,
        "auto_process": true,
        "amount": 941.25
    },
    "installments": [
        {
            "scheduled": "2018-11-24T00:00:00",
            "retry_number": 0,
            "inserted_at": null,
            "id": null,
            "amount": 116.91
        },
        {
            "scheduled": "2018-12-06T23:26:47.492554",
            "retry_number": 0,
            "inserted_at": null,
            "id": null,
            "amount": 116.91
        },
        {
            "scheduled": "2018-12-20T23:26:47.492554",
            "retry_number": 0,
            "inserted_at": null,
            "id": null,
            "amount": 116.91
        },
        {
            "scheduled": "2019-01-03T23:26:47.492554",
            "retry_number": 0,
            "inserted_at": null,
            "id": null,
            "amount": 116.91
        },
        {
            "scheduled": "2019-01-17T23:26:47.492554",
            "retry_number": 0,
            "inserted_at": null,
            "id": null,
            "amount": 116.91
        },
        {
            "scheduled": "2019-01-31T23:26:47.492554",
            "retry_number": 0,
            "inserted_at": null,
            "id": null,
            "amount": 116.91
        },
        {
            "scheduled": "2019-02-14T23:26:47.492554",
            "retry_number": 0,
            "inserted_at": null,
            "id": null,
            "amount": 116.91
        },
        {
            "scheduled": "2019-02-28T23:26:47.492554",
            "retry_number": 0,
            "inserted_at": null,
            "id": null,
            "amount": 116.89
        }
    ]
}
```

Make a payment on a payment plan for an arbitrary amount.

If the payment is for the entire remaining balance of the plan, it will be processed immediately.

If the payment is for less than the balance, a new payment schedule will need to be generated and signed by the customer.
This step does not actually process
the payment, but instead returns the adjusted payment schedule,
installments, and contract for the customer to sign. Which is completed in the [Confirm a payment](#confirm-a-payment) step

In case the supplied payment method requires 3d secure authentication (required for [Strong Customer Authentication](https://stripe.com/payments/strong-customer-authentication)), the resulting payment will have status "requires_action" and will contain the "redirect_url" key. In this scenario, you should redirect the user to the "redirect_url" to authorize the payment, after which point they will be redirected back to the "return_url" you provide.

### HTTP Request

`POST /payment/create`

### Parameters
Parameter | Type | Required | Description
--------- | -----------  | -------- | ------
payment_plan_id | string | yes | id of the plan to make a payment on
amount | decimal | yes | amount of the payment
return_url | string | no | your URL to redirect user to after 3d secure authentication

## Confirm a payment

```shell
curl "https://partial.ly/api/payment/confirm" \
  -H "Authorization: Bearer your_api_key" \
  -H "Content-Type: application/json" \
  -X POST \
  -- data '{"payment_plan_id": "027bb022-40b4-4763-945c-baddc612cbbb", "amount": 5.99, "contract_signature": "Customer signature"}'
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/payment/confirm',
  headers: {
    Authorization: 'Bearer your_api_key'
  },
  method: 'POST',
  json: true,
  body: {
    payment_plan_id: '027bb022-40b4-4763-945c-baddc612cbbb',
    amount: 5.99,
    contract_signature: 'Customer signature'
  }
};

request(options, function (error, response, payment) {
  // asynchronous callback function
});
```

> The above command returns JSON structured like this:

```json
{
    "status": "paid",
    "retry_number": 0,
    "processor_id": "ch_1DbZ8sEs15StTpSvDs76XSVh",
    "processor_destination_id": null,
    "processor": "stripe",
    "payment_plan_id": "027bb022-40b4-4763-945c-baddc612cbbb",
    "paid_at": "2018-11-28T19:56:17",
    "message": null,
    "inserted_at": "2018-11-28T19:56:17.050690",
    "id": "e632f099-4c2b-4de9-b632-3966539be2d5",
    "currency": "USD",
    "amount": 5.99,
    "fee": 0.6,
    "customer_fee_amount": 0.0
}
```

step 2 in making a payment on a payment plan, adjusting future installments
signs the updated payment schedule contract from the create step and processes the payment

In case the supplied payment method requires 3d secure authentication, the resulting payment will have status "requires_action" and will contain the "redirect_url" key. In this scenario, you should redirect the user to the "redirect_url" to authorize the payment, after which point they will be redirected back to the "return_url" you provide.

### HTTP Request

`POST /payment/confirm`

### Parameters
Parameter | Type | Required | Description
--------- | -----------  | -------- | ------
payment_plan_id | string | yes | id of the plan to make a payment on
amount | decimal | yes | amount of the payment
contract_signature | string | yes | the customer's signature for the new payment schedule contract
return_url | string | no | your URL to redirect user to after 3d secure authentication

## List all payments

```shell
curl "https://partial.ly/api/payment" \
  -H "Authorization: Bearer your_api_key"
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/payment',
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
    "total_pages": 24,
    "total_entries": 234,
    "payments": [
        {
            "status": "paid",
            "retry_number": 0,
            "processor_id": "ch_1DbZ8sEs15StTpSvDs76XSVh",
            "processor_destination_id": "py_E3gVHD2z4rtVl9",
            "processor": "stripe",
            "payment_plan_id": "027bb022-40b4-4763-945c-baddc612cbbb",
            "paid_at": "2018-11-28T19:56:17",
            "message": null,
            "inserted_at": "2018-11-28T19:56:17.050690",
            "id": "e632f099-4c2b-4de9-b632-3966539be2d5",
            "currency": "USD",
            "amount": 5.99,
            "fee": 0.6,
            "customer_fee_amount": 0.0
        },
        {
            "status": "paid",
            "retry_number": 0,
            "processor_id": "ch_1DbYxLEs15StTpSvFVKDzPIx",
            "processor_destination_id": "py_E3gK6PBRACnhi5",
            "processor": "stripe",
            "payment_plan_id": "1597c18b-ffd4-4641-b2ca-78cccd3547f5",
            "paid_at": "2018-11-28T19:44:21",
            "message": null,
            "inserted_at": "2018-11-28T19:44:21.874251",
            "id": "2c84ae4a-962c-479b-bed4-4696ef25099d",
            "currency": "USD",
            "amount": 241.87,
            "fee": 12.39,
            "customer_fee_amount": 0.0
        },
        // ...
    ],
    "page_size": 10,
    "page_number": 1
}
```

Lists all payments, with the optional filters listed below

### HTTP Request

`GET /payment`

### Parameters

Parameter | Type | Description
--------- | -----------  | --------
payment_plan_id | string | payments only for the given plan
q | string | a stripe payment id
status | string | payment status. paid, pending, or failed
currency | string | 3 letter currency code
date | date | payment created date. YYYY-MM-DD
dateRange | string | payments created in a range of dates separated by the &#124; character. Ex. use "2018-01-01&#124;2018-02-01"
customer | string | payments with the given customer id

## Retrieve a payment

```shell
curl "https://partial.ly/api/payment/b1873714-bb39-4b07-ac54-10501a60c98a" \
  -H "Authorization: Bearer your_api_key"
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/payment/b1873714-bb39-4b07-ac54-10501a60c98a',
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
    "amount": 52.5,
    "charge_type": "direct",
    "currency": "USD",
    "customer_fee_amount": 0.0,
    "fee": 1.05,
    "id": "b1873714-bb39-4b07-ac54-10501a60c98a",
    "inserted_at": "2024-06-11T14:39:20Z",
    "message": null,
    "paid_at": "2024-06-11T14:41:28Z",
    "payment_method": {
        "id": "ee067511-2028-40e0-855d-50420dda6fcb",
        "inserted_at": "2024-02-17T21:32:51Z",
        "integration_details": {
            "brand": "visa",
            "checks": {
                "address_line1_check": null,
                "address_postal_code_check": "pass",
                "cvc_check": "pass"
            },
            "country": "US",
            "display_brand": "visa",
            "exp_month": 11,
            "exp_year": 2026,
            "fingerprint": "nSxT4fpABgc4m4xm",
            "funding": "credit",
            "generated_from": null,
            "last4": "4242",
            "networks": {
                "available": [
                    "visa"
                ],
                "preferred": null
            },
            "three_d_secure_usage": {
                "supported": true
            },
            "wallet": null
        },
        "type": "card"
    },
    "payment_plan_id": "5f179a30-264f-41d2-84bf-f62f2e1870d4",
    "processor": "stripe",
    "processor_destination_id": null,
    "processor_id": "pi_2PQVr02J8rf5dfhT1nbObkMv",
    "redirect_url": null,
    "retry_number": 0,
    "status": "paid",
    "type": "down_merchant",
    "user": {
        "email": "customer@google.com",
        "id": "b2cb785c-6230-414e-ab43-311f8baf97ed",
        "inserted_at": "2017-09-06T04:46:42Z"
    }
}
```

Get a single payment by id

### HTTP Request

`GET /payment/:id` replace :id with the id of the payment
