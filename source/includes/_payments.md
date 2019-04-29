# Payments

Payments can have a *paid*, *pending*, or *failed* status. Payments made with a bank account payment method
will be pending for up to 5 business days while we wait for them to be confirmed.

## Create a payment

> Pay the entire balance

```shell
curl "https://partial.ly/api/payment/create"
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
    "amount": 241.87
}
```

> Making a payment for an amount less than the balance

```shell
curl "https://partial.ly/api/payment/create"
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

### HTTP Request

`POST /payment/create`

### Parameters
Parameter | Type | Required | Description
--------- | -----------  | -------- | ------
payment_plan_id | string | yes | id of the plan to make a payment on
amount | decimal | yes | amount of the payment

## Confirm a payment

```shell
curl "https://partial.ly/api/payment/confirm"
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
    "amount": 5.99
}
```

step 2 in making a payment on a payment plan, adjusting future installments
signs the updated payment schedule contract from the create step and processes the payment

### HTTP Request

`POST /payment/confirm`

### Parameters
Parameter | Type | Required | Description
--------- | -----------  | -------- | ------
payment_plan_id | string | yes | id of the plan to make a payment on
amount | decimal | yes | amount of the payment
contract_signature | string | yes | the customer's signature for the new payment schedule contract

## List all payments

```shell
curl "https://partial.ly/api/payment"
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
            "amount": 5.99
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
            "amount": 241.87
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
