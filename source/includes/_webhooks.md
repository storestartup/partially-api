# Webhooks

## Webhooks overview

```javascript
// sample server for partial.ly webhooks with signature validation
const express = require('express');
const crypto = require('crypto');
const app = express();
const port = 3000;

// make sure to validate signature before parsing body
const apiKey = 'sample-key';

// Middleware to parse the body as raw buffer
app.use(express.raw({ type: '*/*' }));

// Middleware to validate Partially-Signature header
app.use((req, res, next) => {
  const signature = req.headers['partially-signature'];
  if (!signature) {
    return res.status(401).send('No signature provided');
  }

  const hmac = crypto.createHmac('sha256', apiKey);
  const computedSignature = hmac.update(req.body).digest('hex');

  if (signature !== computedSignature) {
    return res.status(401).send('Invalid signature');
  }

  // Parse raw body to JSON after signature validation
  req.body = JSON.parse(req.body);
  
  next();
});

app.post('/', (req, res) => {
  console.log(`partial.ly event ${req.body.event}`);
  res.send('ok');
});

app.listen(port, () => {
  console.log(`Server is running at http://localhost:${port}`);
});

```

With our webhook system, when [key events](#webhook-events) are triggered in Partial.ly we will deliver the relevant data to any listeners you have configured to receive those events. This allows you to respond to events in real time, such as notifying support, integrating with other systems such as accounting, etc.

All events will be sent over HTTP and JSON encoded, with the following keys:

Key | Description
--------- | -----------
event | the event type, for example plan_opened
id | the id of the event
data | the data specific to the event

To secure webhook delivery and guarantee events are sent by Partial.ly and unmodified, we will always include the HTTP header *Partially-Signature* with every webhook event delivered. The value of this header will be the sha-256 [HMAC](https://en.wikipedia.org/wiki/HMAC) of the HTTP request body, using your API key as the secret key. You should calculate this value yourself and compare it to the value in the *Partially-Signature* header to guarantee the security and accuracy of events. Make sure to calculate using the request body before it's been parsed, as some programming languages may change the order of keys, which could change the value of the signature.

Your webhook listener should return an HTTP 200 status code response within 15 seconds, if not the request is considered failed. If a request fails, we will retry sending it after 1 hour. If there are 20 consecutive failed requests to resend a webhook, it will be automatically disabled.

Webhook listeners can be managed using the API, or through the [webhook user interface in the merchant portal](https://partial.ly/merchant/settings/webhooks).

## Create a webhook listener

```shell
curl "https://partial.ly/api/webhook" \
  -H "Authorization: Bearer your_api_key" \
  -H "Content-Type: application/json" \
  -X POST \
  --data '{"url": "http://localhost:8888/hook"}'
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/webhook',
  headers: {
    Authorization: 'Bearer your_api_key'
  },
  method: 'POST',
  json: true,
  body: {
    url: 'http://localhost:8888/hook'
  }
};

request(options, function (error, response, webhook) {
  // asynchronous callback function
});
```

> The above command returns JSON structured like this:

```json
{
    "version": "2.0",
    "url": "http://localhost:8888/hook",
    "updated_at": "2018-11-28T22:22:06.355439",
    "merchant_id": "60689b2d-d391-417e-9f59-7b65e3e50d8d",
    "inserted_at": "2018-11-28T22:22:06.355432",
    "id": "d5e43bed-041c-4ff4-b422-a401b10aad79",
    "event": "*"
}
```

Creates a new webhook listener. Events that can be subscribed to are [plan_opened](#plan_opened), plan_paid, plan_defaulted, plan_canceled, payment_succeeded, payment_failed, refund_created, dispute_created, dispute_closed, and checkout_abandoned.

### HTTP request

`POST /webhook`

### Parameters
Parameter | Type | Required | Description
--------- | -----------  | -------- | ------
url | string | yes | complete URL of the webhook, including https
event | string | no | the event to subscribe to, or * for all events (default)

## List webhook listeners

```shell
curl "https://partial.ly/api/webhook" \
  -H "Authorization: Bearer your_api_key"
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/webhook',
  headers: {
    Authorization: 'Bearer your_api_key'
  }
};

request(options, function (error, response, hooks) {
  // asynchronous callback function
});
```
> Returns an array of webhooks as follows

```json
[
    {
        "version": "2.0",
        "url": "http://prestashop.local:8080/index.php?fc=module&module=partially&controller=notify&action=webhook",
        "updated_at": "2018-03-08T15:34:35.207108",
        "merchant_id": "60689b2d-d391-417e-9f59-7b65e3e50d8d",
        "inserted_at": "2018-03-08T15:34:35.207102",
        "id": "0aeece05-8e24-419b-a2ad-466a70de9b4b",
        "event": "*"
    }
]
```

Gets all webhook listeners

### HTTP request
`GET /webhook`

## Update a webhook listener

```shell
curl "https://partial.ly/api/webhook/d5e43bed-041c-4ff4-b422-a401b10aad79" \
  -H "Authorization: Bearer your_api_key" \
  -H "Content-Type: application/json" \
  -X PUT \
  --data '{"url": "http://localhost:8888/hook"}'
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/webhook/d5e43bed-041c-4ff4-b422-a401b10aad79',
  headers: {
    Authorization: 'Bearer your_api_key'
  },
  method: 'PUT',
  json: true,
  body: {
    url: 'http://localhost:8888/hook'
  }
};

request(options, function (error, response, webhook) {
  // asynchronous callback function
});
```

> The above command returns JSON structured like this:

```json
{
    "version": "2.0",
    "url": "http://localhost:8888/hook",
    "updated_at": "2018-11-28T22:22:06.355439",
    "merchant_id": "60689b2d-d391-417e-9f59-7b65e3e50d8d",
    "inserted_at": "2018-11-28T22:22:06.355432",
    "id": "d5e43bed-041c-4ff4-b422-a401b10aad79",
    "event": "*",
    "active": true
}
```

Updates an existing webhook listener

### HTTP request

`PUT /webhook/:id`

*replace :id with the id of the webhook to update*

### Parameters
Parameter | Type | Required | Description
--------- | -----------  | -------- | ------
url | string | no | complete URL of the webhook, including https
event | string | no | the event to subscribe to, or * for all events (default)
active | boolean | no | whether or not the webhook is active

## Delete a webhook listener

```shell
curl "https://partial.ly/api/webhook/a9bec449-da6e-4dea-ad4a-a716aa90d40c" \
  -H "Authorization: Bearer your_api_key" \
  -X DELETE
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/webhook/a9bec449-da6e-4dea-ad4a-a716aa90d40c',
  headers: {
    Authorization: 'Bearer your_api_key'
  },
  method: 'DELETE'
};

request(options, function (error, response, body) {
  // asynchronous callback function
});
```

> The above command returns JSON structured like this:

```json
{
    "message": "Webhook deleted"
}
```
deletes a webhook listener

`DELETE /webhook/:id`

*replace :id with the id of the webhook to delete*

## Webhook events

These are the possible event types delivered via webhook, with examples for each.

## plan_opened event

```json
{
  "event": "plan_opened",
  "id": "test",
  "data": {
    "payment_plan": {
      "amount": 96.78999999999999,
      "amount_paid": 0.0,
      "currency": "USD",
      "customer": {
        "email": "bob@fake.com",
        "first_name": "Bob",
        "id": "f4948b98-cef1-49a6-8a8b-3f196f45f917",
        "inserted_at": "2017-09-27T14:20:50Z",
        "last_name": "Fake",
        "phone": null,
        "shipto_address": "345 bayshore blvd",
        "shipto_address2": null,
        "shipto_city": "TAMPA",
        "shipto_country": "US",
        "shipto_name": "Bob Fake",
        "shipto_postal_code": "33611",
        "shipto_state": "FL",
        "timezone": "America/New_York"
      },
      "customer_id": "f4948b98-cef1-49a6-8a8b-3f196f45f917",
      "id": "cefab646-aa25-4c03-979a-e4c291288f97",
      "inserted_at": "2022-05-02T16:04:48Z",
      "integration": null,
      "integration_id": null,
      "ip_address": null,
      "is_reopened": false,
      "line_items": [],
      "merchant_notes": null,
      "meta": {
        "description": ""
      },
      "number": 416,
      "offer_id": null,
      "payment_method_id": "8148229a-146c-4abe-8700-8fbe71707046",
      "payment_schedule": {
        "amount": 48.39000000000001,
        "auto_process": true,
        "balance": -0.009999999999990905,
        "contract_body": "By submitting your order and <em>authorizing</em> the charges on your card, you are legally bound to the following terms:\r\n\r\nI, Bob Fake, acknowledge that I authorize Partial.ly to charge $48.40 USD on behalf of Store Startup today, and  $48.40 USD on the following payment dates:\r\n\r\n$48.39 USD on Thursday June 2, 2022\r\n\r\nI acknowledge that a total of $96.79 USD USD will be paid to Store Startup by June 2, 2022.\r\n\r\n\r\nI <strong>acknowledge</strong> that the credit card(s) or payment method(s) I am using to make this purchase will be active, valid and have sufficient funds available during the entire term of the payback period. If for any reason my payment is declined, I will provide an alternative, valid payment method.\r\n\r\nI understand that I may be assessed a late fee(s) if a payment is missed on any of the agreed-upon payment dates for any reason whatsoever.\r\n\r\nI understand that I am legally bound to these terms and required by law to make all payments on the agreed-upon payment dates.\r\n\r\nStore Startup reserves the right to report delinquent payments to credit agencies and collections agencies.\r\n\r\n",
        "contract_signature": "Bilbo Baggins",
        "contract_signed_date": "2022-05-02T16:05:40Z",
        "description": null,
        "down_payment_amount": 48.4,
        "first_payment_date_buffer_days": null,
        "first_payment_date_buffer_days_enabled?": false,
        "frequency": 1,
        "frequency_days": [],
        "frequency_units": "months",
        "id": "d6fd47da-bbfe-41fd-bc63-0d9521716391",
        "inserted_at": "2022-05-02T16:05:40Z",
        "installments": [
          {
            "amount": 48.39,
            "id": "9ae02520-6be2-4fff-9d50-5f0c94f872fc",
            "inserted_at": "2022-05-02T16:05:40Z",
            "retry_number": 0,
            "scheduled": "2022-06-02T16:05:03Z"
          }
        ],
        "ip_address": "127.0.0.1",
        "num_payments": 1,
        "payment_amount": 48.4,
        "repay_by_date": "2022-06-02",
        "starts_auto": true,
        "starts_date": null,
        "term": 1,
        "term_date": null,
        "term_units": "months"
      },
      "shipto_address": null,
      "shipto_address2": null,
      "shipto_city": null,
      "shipto_country": "US",
      "shipto_name": null,
      "shipto_postal_code": null,
      "shipto_state": null,
      "status": "open",
      "subtotal": 96.78999999999999,
      "user_agent": null
    }
  }
}
```

Sent when a payment plan is opened. Note that this will be sent when a new plan is opened for the first time, as well as when an existing plan is re-opened (a defaulted plan being reopened, for example).

## plan_paid event

```json
{
  "event": "plan_paid",
  "id": "test",
  "data": {
    "payment_plan": {
      "amount": 3265.0,
      "amount_paid": 3265.0,
      "currency": "USD",
      "customer": {
        "email": "bob@fake.com",
        "first_name": "Bob",
        "id": "69acb831-9d4d-4d2c-8cc9-f2e75214aa5b",
        "inserted_at": "2016-09-12T16:07:44Z",
        "last_name": "Fake",
        "phone": "678-555-1212",
        "shipto_address": "",
        "shipto_address2": "",
        "shipto_city": "",
        "shipto_country": "",
        "shipto_name": "",
        "shipto_postal_code": "",
        "shipto_state": "",
        "timezone": "America/New_York"
      },
      "customer_id": "69acb831-9d4d-4d2c-8cc9-f2e75214aa5b",
      "id": "0c9593ff-22b3-4324-a123-919fb7fcca5d",
      "inserted_at": "2015-09-15T21:55:53Z",
      "integration": null,
      "integration_id": null,
      "ip_address": "::ffff:10.182.8.87",
      "is_reopened": false,
      "line_items": [],
      "merchant_notes": null,
      "meta": {
        "description": "Sample Design Invoice"
      },
      "number": 1,
      "offer_id": "60aed439-473f-48e0-80ef-3a8627dd243a",
      "payment_method_id": "ab27241e-eafb-4385-88b0-a488580afb4a",
      "payment_schedule": {
        "amount": 3265.0,
        "auto_process": true,
        "balance": 3065.0,
        "contract_body": "By submitting your order and authorizing the charges on your card, you are legally bound to the following terms:\n\nI, Bob Fake, acknowledge that I authorize Partial.ly to charge $200.00 on behalf of Store Startup today, and $510.84 on the following payment dates:\n\n$510.84 on October 15th, 2015\n\n$510.84 on November 15th, 2015\n\n$510.84 on December 15th, 2015\n\n$510.84 on January 15th, 2016\n\n$510.84 on February 15th, 2016\n\n$510.80 on March 15th, 2016\n\nI acknowledge that a total of $3,265.00 will be paid to Store Startup by April 15th, 2016.\n\nI acknowledge that the credit card(s) or payment method(s) I am using to make this purchase will be active, valid and have sufficient funds available during the entire term of the payback period. If for any reason my payment is declined, I will provide an alternative, valid payment method.\n\nI understand that I may be assessed a late fee(s) if a payment is missed on any of the agreed-upon payment dates for any reason whatsoever.\n\nI understand that I am legally bound to these terms and required by law to make all payments on the agreed-upon payment dates.\n\nStore Startup reserves the right to report delinquent payments to credit agencies and collections agencies.",
        "contract_signature": "Bob Fake",
        "contract_signed_date": "2015-09-15T21:55:52Z",
        "description": null,
        "down_payment_amount": 200.0,
        "first_payment_date_buffer_days": null,
        "first_payment_date_buffer_days_enabled?": false,
        "frequency": 1,
        "frequency_days": null,
        "frequency_units": "months",
        "id": "2f4dd052-8642-48d9-b9b8-92d6e3118473",
        "inserted_at": "2015-09-15T21:55:53Z",
        "installments": [],
        "ip_address": "::ffff:10.182.8.87",
        "num_payments": 6,
        "payment_amount": 510.84,
        "repay_by_date": "2016-04-15",
        "starts_auto": true,
        "starts_date": null,
        "term": 6,
        "term_date": null,
        "term_units": "months"
      },
      "shipto_address": null,
      "shipto_address2": null,
      "shipto_city": null,
      "shipto_country": "US",
      "shipto_name": null,
      "shipto_postal_code": null,
      "shipto_state": null,
      "status": "paid",
      "subtotal": 3265.0,
      "user_agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_5) AppleWebKit/600.8.9 (KHTML, like Gecko) Version/8.0.8 Safari/600.8.9"
    }
  }
}
```

The plan_paid event is triggered when a payment plan is fully paid.

## plan_defaulted event

```json
{
  "event": "plan_defaulted",
  "id": "test",
  "data": {
    "payment_plan": {
      "amount": 21.2,
      "amount_paid": 0.0,
      "currency": "USD",
      "customer": {
        "email": "test@testing.com",
        "first_name": "Test",
        "id": "bc8929b8-e0ab-4149-bb13-3159776cdceb",
        "inserted_at": "2016-10-20T11:25:09Z",
        "last_name": "Tester",
        "phone": "1234567890",
        "shipto_address": "12312 ",
        "shipto_address2": null,
        "shipto_city": "Tampa",
        "shipto_country": "US",
        "shipto_name": "test",
        "shipto_postal_code": "33611",
        "shipto_state": "FL",
        "timezone": "America/New_York"
      },
      "customer_id": "bc8929b8-e0ab-4149-bb13-3159776cdceb",
      "id": "80be6129-6a26-4330-983c-5f56f1619f72",
      "inserted_at": "2017-09-08T20:51:33Z",
      "integration": "woocommerce",
      "integration_id": null,
      "ip_address": "127.0.0.1",
      "is_reopened": true,
      "line_items": [
        {
          "amount": 10.0,
          "description": "Sample with variants",
          "dynamic": false,
          "dynamic_type": "generic",
          "id": "6dfae68a-3dbd-4d3b-bd44-48e429c6b2ea",
          "inserted_at": "2017-09-08T20:51:33Z",
          "integration": "woocommerce",
          "integration_id": "8e325a79cd78833c32ddd2afa10eec8a",
          "meta": {
            "id": "8e325a79cd78833c32ddd2afa10eec8a",
            "name": "Sample with variants",
            "price": "10",
            "product_id": "67",
            "quantity": "1",
            "sku": "abas23423",
            "total": "10",
            "variant_id": "70"
          },
          "quantity": 1,
          "weight": 0.0,
          "weight_units": "lb"
        }
      ],
      "merchant_notes": null,
      "meta": null,
      "number": 29,
      "offer_id": "fe042bc3-336f-4ade-bcdb-77fc1fc0ceb2",
      "payment_method_id": "9ecd1a09-0b8d-4d07-bfe6-3c2def0d1b2d",
      "payment_schedule": {
        "amount": 15.9,
        "auto_process": true,
        "balance": 15.9,
        "contract_body": "By submitting your order and authorizing the electronic debits from your bank account, you are legally bound to the following terms:\r\n\r\nI, Test Tester, acknowledge that I authorize Partial.ly to electronically debit my account  $5.30 on the following payment dates:\r\n\r\n$5.30 on Saturday October 14, 2017\r\n\r\n$5.30 on Tuesday November 14, 2017\r\n\r\n$5.30 on Thursday December 14, 2017\r\n\r\nI acknowledge that a total of $21.20 USD will be paid to Store Startup by Thursday December 14, 2017.\r\n\r\n\r\nI acknowledge that the credit card(s) or payment method(s) I am using to make this purchase will be active, valid and have sufficient funds available during the entire term of the payback period. If for any reason my payment is declined, I will provide an alternative, valid payment method.\r\n\r\nI understand that I may be assessed a late fee(s) if a payment is missed on any of the agreed-upon payment dates for any reason whatsoever.\r\n\r\nI understand that I am legally bound to these terms and required by law to make all payments on the agreed-upon payment dates.\r\n\r\nStore Startup reserves the right to report delinquent payments to credit agencies and collections agencies.\r\n\r\n",
        "contract_signature": "tha merchant",
        "contract_signed_date": "2017-09-14T18:13:28Z",
        "description": null,
        "down_payment_amount": 0.0,
        "first_payment_date_buffer_days": null,
        "first_payment_date_buffer_days_enabled?": false,
        "frequency": 1,
        "frequency_days": [],
        "frequency_units": "months",
        "id": "cde89c56-1110-4af8-aa21-04e0731f7617",
        "inserted_at": "2017-09-14T18:13:24Z",
        "installments": [
          {
            "amount": 5.3,
            "id": "58784b85-c59a-4720-bc8e-f02331b785a6",
            "inserted_at": "2017-09-14T18:13:28Z",
            "retry_number": 0,
            "scheduled": "2017-10-14T18:13:28Z"
          },
          {
            "amount": 5.3,
            "id": "d0e8fe1c-bc5d-4ca1-88c8-e079715d2cac",
            "inserted_at": "2017-09-14T18:13:28Z",
            "retry_number": 0,
            "scheduled": "2017-11-14T18:13:28Z"
          },
          {
            "amount": 5.3,
            "id": "30404e14-922b-47d2-80ad-56ffee3f5940",
            "inserted_at": "2017-09-14T18:13:28Z",
            "retry_number": 0,
            "scheduled": "2017-12-14T18:13:28Z"
          }
        ],
        "ip_address": "127.0.0.1",
        "num_payments": 3,
        "payment_amount": 5.3,
        "repay_by_date": "2017-12-14",
        "starts_auto": true,
        "starts_date": "2017-09-14",
        "term": 3,
        "term_date": "2017-09-14",
        "term_units": "months"
      },
      "shipto_address": "12312 ",
      "shipto_address2": null,
      "shipto_city": "Tampa",
      "shipto_country": "US",
      "shipto_name": "Test",
      "shipto_postal_code": "33611",
      "shipto_state": "FL",
      "status": "defaulted",
      "subtotal": 21.2,
      "user_agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.11; rv:55.0) Gecko/20100101 Firefox/55.0"
    }
  }
}
```

Sent when a payment plan status changes to defaulted, which will occurr when the configured number of consecutive scheduled installments have failed (default 3).

## payment_succeeded event

```json
{
  "event": "payment_succeeded",
  "id": "test",
  "data": {
    "payment": {
      "amount": 510.84,
      "charge_type": "destination",
      "currency": "USD",
      "customer_fee_amount": 0.0,
      "fee": 25.84,
      "id": "4b2f7372-b8ca-47fb-948d-b34a418150ce",
      "inserted_at": "2015-11-15T12:00:02Z",
      "message": null,
      "paid_at": "2015-11-15T12:00:02Z",
      "payment_method": {
        "id": "12b61a91-9934-453f-9fd0-ad22c343a087",
        "inserted_at": "2015-09-15T21:36:53Z",
        "integration_details": {
          "brand": "American Express",
          "country": "US",
          "exp_month": "8",
          "exp_year": "2019",
          "funding": null,
          "last4": "1111"
        },
        "type": "card"
      },
      "payment_plan": {
        "amount": 3265.0,
        "amount_paid": 3265.0,
        "currency": "USD",
        "customer": {
          "email": "bob@fake.com",
          "first_name": "Bob",
          "id": "69acb831-9d4d-4d2c-8cc9-f2e75214aa5b",
          "inserted_at": "2016-09-12T16:07:44Z",
          "last_name": "Fake",
          "phone": "678-520-8206",
          "shipto_address": "",
          "shipto_address2": "",
          "shipto_city": "",
          "shipto_country": "",
          "shipto_name": "",
          "shipto_postal_code": "",
          "shipto_state": "",
          "timezone": "America/New_York"
        },
        "customer_id": "69acb831-9d4d-4d2c-8cc9-f2e75214aa5b",
        "id": "0c9593ff-22b3-4324-a123-919fb7fcca5d",
        "inserted_at": "2015-09-15T21:55:53Z",
        "integration": null,
        "integration_id": null,
        "ip_address": "::ffff:10.182.8.87",
        "is_reopened": false,
        "merchant_notes": null,
        "meta": {
          "description": "Sample Design Invoice"
        },
        "number": 1,
        "offer_id": "60aed439-473f-48e0-80ef-3a8627dd243a",
        "payment_method_id": "ab27241e-eafb-4385-88b0-a488580afb4a",
        "shipto_address": null,
        "shipto_address2": null,
        "shipto_city": null,
        "shipto_country": "US",
        "shipto_name": null,
        "shipto_postal_code": null,
        "shipto_state": null,
        "status": "paid",
        "subtotal": 3265.0,
        "user_agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_5) AppleWebKit/600.8.9 (KHTML, like Gecko) Version/8.0.8 Safari/600.8.9"
      },
      "payment_plan_id": "0c9593ff-22b3-4324-a123-919fb7fcca5d",
      "processor": "stripe",
      "processor_destination_id": null,
      "processor_id": "ch_177PArEs15StTpSvMeqwLJFr",
      "redirect_url": null,
      "retry_number": null,
      "status": "paid",
      "type": "auto",
      "user": null
    }
  }
}
```

Sent when a payment suceeds. Note that for certain payment method types, such as bank account, this maybe event may be up to 5 business days after the payment is submit until it's confirmed.

## payment_failed event

```json
{
  "event": "payment_failed",
  "id": "test",
  "data": {
    "payment": {
      "amount": 2.5,
      "charge_type": "destination",
      "currency": "EUR",
      "customer_fee_amount": 0.0,
      "fee": 0.0,
      "id": "dcd36f39-0539-40ce-8fce-77709ea05008",
      "inserted_at": "2019-07-01T15:23:05Z",
      "message": "Error from Stripe: The card has insufficient funds",
      "paid_at": null,
      "payment_method": {
        "id": "b7a7b42a-a693-4fe9-b265-99d304cf3b93",
        "inserted_at": "2018-08-21T17:56:00Z",
        "integration_details": {
          "brand": "American Express",
          "country": "US",
          "exp_month": "8",
          "exp_year": "2019",
          "funding": null,
          "last4": "1111"
        },
        "type": "card"
      },
      "payment_plan": {
        "amount": 255.0,
        "amount_paid": 50.5,
        "currency": "EUR",
        "customer": {
          "email": "bob@fake.com",
          "first_name": "Bob",
          "id": "452cc42f-d999-4c0f-998b-325c4e0e8f57",
          "inserted_at": "2015-11-16T16:03:22Z",
          "last_name": "Fake",
          "phone": "+18135551212",
          "shipto_address": "345 bayshore blvd",
          "shipto_address2": null,
          "shipto_city": "TAMPA",
          "shipto_country": "US",
          "shipto_name": "Test Customer",
          "shipto_postal_code": "33611-4728",
          "shipto_state": "FL",
          "timezone": "America/New_York"
        },
        "customer_id": "452cc42f-d999-4c0f-998b-325c4e0e8f57",
        "id": "34661b40-3fcc-4e65-a156-4c57054527ec",
        "inserted_at": "2018-08-21T17:46:49Z",
        "integration": null,
        "integration_id": null,
        "ip_address": "127.0.0.1",
        "is_reopened": false,
        "merchant_notes": null,
        "meta": {
          "description": "testing manuel"
        },
        "number": 124,
        "offer_id": "203fb249-b43b-4f9e-a31b-6690a6d8f660",
        "payment_method_id": "b7a7b42a-a693-4fe9-b265-99d304cf3b93",
        "shipto_address": "123123",
        "shipto_address2": null,
        "shipto_city": "Tampa",
        "shipto_country": "DE",
        "shipto_name": "Test Customer",
        "shipto_postal_code": "33611",
        "shipto_state": null,
        "status": "open",
        "subtotal": 255.0,
        "user_agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.106 Safari/537.36"
      },
      "payment_plan_id": "34661b40-3fcc-4e65-a156-4c57054527ec",
      "processor": "stripe",
      "processor_destination_id": null,
      "processor_id": null,
      "redirect_url": null,
      "retry_number": 0,
      "status": "failed",
      "type": "auto",
      "user": null
    }
  }
}
```

Sent when a payment fails.

## refund_created event

```json
{
  "event": "refund_created",
  "id": "test",
  "data": {
    "refund": {
      "amount": 472.19,
      "failure_merchant_transfer_id": null,
      "failure_reason": null,
      "id": "6e071d81-f030-41db-8511-6aa646f6dc75",
      "inserted_at": "2015-12-08T16:43:43Z",
      "notes": null,
      "payment": {
        "amount": 472.19,
        "charge_type": "destination",
        "currency": "USD",
        "customer_fee_amount": 0.0,
        "fee": 23.91,
        "id": "5266501d-d953-4a1d-82f2-ed67e8a6af09",
        "inserted_at": "2015-12-08T16:30:57Z",
        "message": null,
        "paid_at": "2015-12-08T16:30:57Z",
        "payment_method": {
          "id": "12b61a91-9934-453f-9fd0-ad22c343a087",
          "inserted_at": "2015-09-15T21:36:53Z",
          "integration_details": {
            "brand": "American Express",
            "country": "US",
            "exp_month": "8",
            "exp_year": "2019",
            "funding": null,
            "last4": "1111"
          },
          "type": "card"
        },
        "payment_plan": {
          "amount": 1888.75,
          "amount_paid": 0.0,
          "currency": "USD",
          "customer": {
            "email": "bob@fake.com",
            "first_name": "Bob",
            "id": "69acb831-9d4d-4d2c-8cc9-f2e75214aa5b",
            "inserted_at": "2016-09-12T16:07:44Z",
            "last_name": "Fake",
            "phone": "678-520-8206",
            "shipto_address": "",
            "shipto_address2": "",
            "shipto_city": "",
            "shipto_country": "",
            "shipto_name": "",
            "shipto_postal_code": "",
            "shipto_state": "",
            "timezone": "America/New_York"
          },
          "customer_id": "69acb831-9d4d-4d2c-8cc9-f2e75214aa5b",
          "id": "17c6f090-de95-4e04-9469-32c017567b1d",
          "inserted_at": "2015-12-08T16:30:57Z",
          "integration": null,
          "integration_id": null,
          "ip_address": "::ffff:10.182.8.87",
          "is_reopened": false,
          "merchant_notes": null,
          "meta": {
            "description": "Harvest Invoice ID 158"
          },
          "number": 9,
          "offer_id": "60aed439-473f-48e0-80ef-3a8627dd243a",
          "payment_method_id": "12b61a91-9934-453f-9fd0-ad22c343a087",
          "shipto_address": null,
          "shipto_address2": null,
          "shipto_city": null,
          "shipto_country": "US",
          "shipto_name": null,
          "shipto_postal_code": null,
          "shipto_state": null,
          "status": "canceled",
          "subtotal": 1888.75,
          "user_agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_1) AppleWebKit/601.2.7 (KHTML, like Gecko) Version/9.0.1 Safari/601.2.7"
        },
        "payment_plan_id": "17c6f090-de95-4e04-9469-32c017567b1d",
        "processor": "stripe",
        "processor_destination_id": null,
        "processor_id": "ch_17FoMeEs15StTpSvpltBMWbg",
        "redirect_url": null,
        "retry_number": 0,
        "status": "paid",
        "type": "auto",
        "user": null
      },
      "payment_id": "5266501d-d953-4a1d-82f2-ed67e8a6af09",
      "processor": "stripe",
      "processor_id": "re_17FoZ1Es15StTpSvXcn4uLPp",
      "reason": null,
      "status": "succeeded"
    }
  }
}
```

Sent when a refund is created.

## dispute_created event

```json
{
  "event": "dispute_created",
  "id": "test",
  "data": {
    "dispute": {
      "amount": 150.0,
      "id": "bc1311c5-73db-4ca5-9550-891db55b767d",
      "inserted_at": "2023-07-16T18:02:26Z",
      "payment": {
        "amount": 150.0,
        "charge_type": "destination",
        "currency": "USD",
        "customer_fee_amount": 0.0,
        "fee": 6.6,
        "id": "53e64081-659a-4df8-ab8e-7bfb06ec09fa",
        "inserted_at": "2023-07-16T18:02:23Z",
        "message": null,
        "paid_at": "2023-07-16T18:02:23Z",
        "payment_method": {
          "id": "b828da1c-58f6-4a87-b870-e04c51f013c6",
          "inserted_at": "2023-07-16T17:59:07Z",
          "integration_details": {
            "brand": "visa",
            "country": "US",
            "exp_month": 11,
            "exp_year": 2028,
            "fingerprint": "cTA72UojsYE5ZUqQ",
            "funding": "credit",
            "last4": "1234"
          },
          "type": "card"
        },
        "payment_plan": {
          "amount": 450.0,
          "amount_paid": 300.0,
          "currency": "USD",
          "customer": {
            "email": "jdoe@fake.com",
            "first_name": "John",
            "id": "fa42b43d-af00-411a-89e1-dc415849652b",
            "inserted_at": "2021-04-19T17:55:09Z",
            "last_name": "Doe",
            "phone": "+15045551212",
            "shipto_address": "123 maine Ave",
            "shipto_address2": null,
            "shipto_city": "Tampa",
            "shipto_country": "US",
            "shipto_name": "John Doe",
            "shipto_postal_code": "33611",
            "shipto_state": "FL",
            "timezone": "America/New_York"
          },
          "customer_id": "fa42b43d-af00-411a-89e1-dc415849652b",
          "id": "b86e7f4a-abe9-4541-b42a-4cea49304c4f",
          "inserted_at": "2023-07-16T17:56:37Z",
          "integration": null,
          "integration_id": null,
          "ip_address": "127.0.0.1",
          "is_reopened": false,
          "merchant_notes": null,
          "meta": {
            "description": "test"
          },
          "number": 463,
          "offer_id": "998f5c9d-b359-42da-a9ac-0a416c819091",
          "payment_method_id": "48b5f2ac-e3b6-4c67-8077-bad0eba48bfc",
          "shipto_address": "123 main Ave",
          "shipto_address2": null,
          "shipto_city": "Tampa",
          "shipto_country": "US",
          "shipto_name": "J Doe",
          "shipto_postal_code": "33611",
          "shipto_state": "FL",
          "status": "open",
          "subtotal": 400.0,
          "user_agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/114.0.0.0 Safari/537.36"
        },
        "payment_plan_id": "b86e7f4a-abe9-4541-b42a-4cea49304c4f",
        "processor": "stripe",
        "processor_destination_id": "py_1NUZGzJdLv8qpyyQ1jz7ZO1M",
        "processor_id": "pi_3NUZGzEs15StTpSv0xROLwe8",
        "redirect_url": null,
        "retry_number": 0,
        "status": "paid",
        "type": "manual_admin",
        "user": {
          "email": "partiallyadmin@gmail.com",
          "id": "3a68691f-27c1-4fca-b618-c08eb56a2c1b",
          "inserted_at": "2017-09-06T04:46:42Z"
        }
      },
      "payment_id": "53e64081-659a-4df8-ab8e-7bfb06ec09fa",
      "reason": "product_not_received",
      "status": "needs_response"
    }
  }
}
```

Sent when a new dispute is created.

## dispute_closed event

```json
{
  "event": "dispute_closed",
  "id": "123456789",
  "data": {
    "dispute": {
      "amount": 25.0,
      "id": "123abc",
      "payment": {
        "amount": 25.0,
        "currency": "USD",
        "id": "143345",
        "payment_plan": {
          "amount": 100.0,
          "amount_paid": 50.0,
          "currency": "USD",
          "customer": {
            "email": "jdoe@gmail.com",
            "first_name": "John",
            "last_name": "Doe"
          },
          "id": "234234",
          "number": "1",
          "status": "open"
        },
        "status": "paid"
      },
      "reason": "unrecognized",
      "status": "won"
    }
  }
}
```

Sent when a dispute is closed, either won or lost.

## checkout_abandoned event

```json
{
  "event": "checkout_abandoned",
  "id": "test",
  "data": {
    "payment_plan": {
      "amount": 275.6,
      "amount_paid": 0.0,
      "currency": "USD",
      "customer": {
        "email": "jdoe@partial.ly",
        "first_name": "John",
        "id": "89c25cc8-d94c-45f6-b728-279928aeb3c8",
        "inserted_at": "2018-07-20T14:51:20Z",
        "last_name": "Doe",
        "phone": "+18135551212",
        "shipto_address": "123 main Ave",
        "shipto_address2": null,
        "shipto_city": "Tampa",
        "shipto_country": "US",
        "shipto_name": "J Doe",
        "shipto_postal_code": "33611",
        "shipto_state": "FL",
        "timezone": "America/New_York"
      },
      "customer_id": "89c25cc8-d94c-45f6-b728-279928aeb3c8",
      "id": "da8c46c5-518c-4a6b-87fb-4878a5b2ed8e",
      "inserted_at": "2018-08-06T14:59:58Z",
      "integration": "woocommerce",
      "integration_id": "191",
      "ip_address": "127.0.0.1",
      "is_reopened": false,
      "line_items": [
        {
          "amount": 250.0,
          "description": "The new hotness",
          "dynamic": false,
          "dynamic_type": "generic",
          "id": "7be7275d-1518-4837-ac9e-ddde24904b2a",
          "inserted_at": "2018-08-06T14:59:58Z",
          "integration": "woocommerce",
          "integration_id": null,
          "meta": {
            "name": "The new hotness",
            "price": 250,
            "product_id": 157,
            "quantity": 1,
            "sku": "",
            "total": "250"
          },
          "quantity": 1,
          "weight": 0.0,
          "weight_units": "lb"
        }
      ],
      "merchant_notes": null,
      "meta": {
        "checkout_cancel_url": "http://wootest.local:8080/cart/?cancel_order=true&order=wc_order_5b68626d9daac&order_id=191&redirect&_wpnonce=0f4253a8fe",
        "checkout_complete_url": "http://wootest.local:8080/checkout/order-received/191/?key=wc_order_5b68626d9daac",
        "items": [
          {
            "name": "The new hotness",
            "price": 250,
            "product_id": 157,
            "quantity": 1,
            "sku": "",
            "total": "250"
          }
        ]
      },
      "number": null,
      "offer_id": "fe042bc3-336f-4ade-bcdb-77fc1fc0ceb2",
      "payment_method_id": null,
      "payment_schedule": {
        "amount": 275.6,
        "auto_process": true,
        "balance": 206.70000000000002,
        "contract_body": null,
        "contract_signature": null,
        "contract_signed_date": null,
        "description": null,
        "down_payment_amount": 68.9,
        "first_payment_date_buffer_days": null,
        "first_payment_date_buffer_days_enabled?": false,
        "frequency": 2,
        "frequency_days": [],
        "frequency_units": "weeks",
        "id": "26cf43e4-1f19-4058-a7f1-b7ff57726ba4",
        "inserted_at": "2018-08-06T14:59:58Z",
        "installments": [],
        "ip_address": null,
        "num_payments": 6,
        "payment_amount": 34.46,
        "repay_by_date": "2018-10-29",
        "starts_auto": true,
        "starts_date": null,
        "term": 3,
        "term_date": "2017-09-27",
        "term_units": "months"
      },
      "shipto_address": "123 main ave",
      "shipto_address2": null,
      "shipto_city": "Tampa",
      "shipto_country": "US",
      "shipto_name": null,
      "shipto_postal_code": "33611",
      "shipto_state": "FL",
      "status": "checkout",
      "subtotal": 275.6,
      "user_agent": "WordPress/4.9.3; http://wootest.local:8080"
    }
  }
}
```

Sent when a Partial.ly checkout is abandoned. The amount of time it takes to triggered this can be configured in your abandoned cart settings in the Partial.ly merchant portal. Note that if the customer hasn't made it through the customer step of the checkout process by the time the checkout is considered abandoned, this information will be empty in the webhook.