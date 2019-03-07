# Payment Schedules

A payment schedule represents the terms and schedule for a payment plan. A payment plan can have multiple payment schedules if the customer makes a one off payment that adjusts the current payment schedule. A payment plan only has one active payment schedule  

## Update a payment schedule

```shell
curl "https://partial.ly/api/payment_schedule/64823d54-9d47-4cd9-9db0-b293294ca341"
  -H "Authorization: Bearer your_api_key" \
  -H "Content-Type: application/json" \
  -X PUT \
  -- data '{"term": 3}'
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/payment_schedule/64823d54-9d47-4cd9-9db0-b293294ca341',
  headers: {
    Authorization: 'Bearer your_api_key'
  },
  method: 'PUT',
  json: true,
  body: {
    term: 3
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
        "repay_by_date": "2019-03-21",
        "payment_amount": 256.25,
        "num_payments": 3,
        "inserted_at": "2018-12-21T22:01:18.623163",
        "id": "64823d54-9d47-4cd9-9db0-b293294ca341",
        "frequency_units": "months",
        "frequency": 1,
        "down_payment_amount": 256.25,
        "description": null,
        "contract_signed_date": null,
        "contract_signature": null,
        "contract_body": "By submitting your order and authorizing the charges on your card...",
        "balance": 768.75,
        "auto_process": true,
        "amount": 1025
    },
    "installments": [
        {
            "scheduled": "2019-01-21T22:10:14.518787Z",
            "retry_number": 0,
            "inserted_at": null,
            "id": null,
            "amount": 256.25
        },
        {
            "scheduled": "2019-02-21T22:10:14.518787Z",
            "retry_number": 0,
            "inserted_at": null,
            "id": null,
            "amount": 256.25
        },
        {
            "scheduled": "2019-03-21T22:10:14.518787Z",
            "retry_number": 0,
            "inserted_at": null,
            "id": null,
            "amount": 256.25
        }
    ]
}
```

Can only be done for active schedules of plans in *checkout* status. If customer flexibility enabled, updates either the term, frequency, or down payment, and reevaluates the payment schedule and installments.

### HTTP request
`PUT /payment_schedule/:id`

### Parameters

Parameter | Description
--------- | -----------
down_payment_amount | amount of down payment
term | term or length of payment plan
frequency | frequency of payments

## Get contract pdf

Gets the binary pdf of the signed contract

### HTTP Request

`GET /payment_schedule/contract_pdf/:id`

## Create a new payment schedule

```shell
curl "https://partial.ly/api/payment_schedule"
  -H "Authorization: Bearer your_api_key" \
  -H "Content-Type: application/json" \
  -X POST \
  -- data '{"payment_plan_id": "71f98dc3-6d89-4e56-b14b-c0e27dac0158", "amount": 188.88, "term": 1, "term_units": "months", "frequency": 1, "frequency_units": "weeks"}'
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/payment_schedule',
  headers: {
    Authorization: 'Bearer your_api_key'
  },
  method: 'POST',
  json: true,
  body: {
    payment_plan_id: '71f98dc3-6d89-4e56-b14b-c0e27dac0158',
    amount: 188.88,
    term: 1,
    term_units: 'months',
    frequency: 1,
    frequency_units: 'weeks'
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
        "term": 1,
        "starts_date": null,
        "starts_auto": true,
        "repay_by_date": "2018-12-26",
        "payment_amount": 47.21,
        "num_payments": 4,
        "inserted_at": "2018-11-28T20:49:46.792362",
        "id": "0ec6f76b-8675-449f-9414-ccf44cc9c148",
        "frequency_units": "weeks",
        "frequency": 1,
        "down_payment_amount": 0,
        "description": null,
        "contract_signed_date": null,
        "contract_signature": null,
        "contract_body": null,
        "balance": 188.8,
        "auto_process": true,
        "amount": 188.8
    },
    "installments": [
        {
            "scheduled": "2018-12-05T20:49:46.791705Z",
            "retry_number": 0,
            "inserted_at": null,
            "id": null,
            "amount": 47.21
        },
        {
            "scheduled": "2018-12-12T20:49:46.791705Z",
            "retry_number": 0,
            "inserted_at": null,
            "id": null,
            "amount": 47.21
        },
        {
            "scheduled": "2018-12-19T20:49:46.791705Z",
            "retry_number": 0,
            "inserted_at": null,
            "id": null,
            "amount": 47.21
        },
        {
            "scheduled": "2018-12-26T20:49:46.791705Z",
            "retry_number": 0,
            "inserted_at": null,
            "id": null,
            "amount": 47.17
        }
    ]
}
```

Creates a new PaymentSchedule for a PaymentPlan,
making it the active schedule and deactivating all others.
Can't be done for open or paid status plans.

### HTTP Request

`POST /payment_schedule`

### Parameters

Parameter | Type | Required | Default | Description
--------- | -----------  | ----------- | -------- | ------
payment_plan_id | string | yes | | id of the payment plan
amount |  decimal | yes | | amount of the payment schedule
auto_process | boolean | no | true | true to automatically schedule future payments, false for manual payments
description | string | no | | for manual payment schedules (auto_process=false), a description of how the payments will be processed
currency | string | no | currency in merchant settings | The currency to use for payment plans
down_payment | decimal | no | 0 | the amount or percent of down payment required, depending on down_payment_type
down_payment_type | string | no | percent | percent or amount
down_payment_flexible | boolean | no | false | allow the customer to choose their down payment within a specified range
down_payment_min | decimal | no | | min down payment percent or amount
down_payment_max | decimal | no | | max down payment percent or amount
term | integer | no | 3 | the length of the payment plan
term_units | string | no | months | type of units for the payment plan term. weeks, months, years, payments (a fixed number of payments), or date (pay by a date)
term_date | date | no | | final payment date when using term_units=date
term_flexible | boolean | no | false | allow the customer to choose their term within the specified range
term_min | integer | no | | the minimum term the customer can choose
term_max | integer | no | | the maximum term the customer can choose
frequency | integer | no | 1 | the frequency of scheduled payments
frequency_units | string | no | months | type of units for the payment plan term. days, weeks, months, days_month (for specific days of the month)
frequency_days | array | no | | array of specific days of the month for payments, for example [1, 15] for payments on the 1st and 15th of the month
frequency_flexible | boolean | no | false | allow the customer to choose their payment frequency within the specified range
frequency_min | integer | no | | the minimum payment frequency the customer can choose
frequency_max | integer | no | | the maximum payment frequency the customer can choose
