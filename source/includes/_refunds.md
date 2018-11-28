# Refunds

## Refund a payment

```shell
curl "https://partial.ly/api/v1/refund"
  -H "Authorization: Bearer your_api_key" \
  -X POST \
  --data '{"payment_id": "2d1d0234-87c4-4184-a49f-02f4c0c30abe", "amount": 5.99, "notes": "discount"}'
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/v1/refund',
  headers: {
    Authorization: 'Bearer your_api_key'
  },
  method: 'POST',
  json: true,
  body: {
    payment_id: '2d1d0234-87c4-4184-a49f-02f4c0c30abe',
    amount: 5.99,
    notes: 'discount'
  }
};

request(options, function (error, response, refund) {
  // asynchronous callback function
});
```

> The above command returns JSON structured like this:

```json
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
```

Refunds the given amount of the payment. The refund amount must not exceed the amount of the payment or the amount not already refunded.

### HTTP request

`POST /refund`

### Parameters
Parameter | Type | Required | Description
--------- | -----------  | -------- | ------
payment_id | string | yes | id of the payment to refund
amount | decimal | yes | amount to refund
notes | string | no | 

## List refunds

```shell
curl "https://partial.ly/api/v1/refund"
  -H "Authorization: Bearer your_api_key"
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/v1/refund',
  headers: {
    Authorization: 'Bearer your_api_key'
  }
};

request(options, function (error, response, refunds) {
  // asynchronous callback function
});
```
> Returns an array of refunds as follows

```json
{
    "total_pages": 1,
    "total_entries": 5,
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
        },
        {
            "status": "succeeded",
            "reason": "requested_by_customer",
            "processor_id": "re_1COApEEs15StTpSviNl0Utby",
            "processor": "stripe",
            "notes": null,
            "inserted_at": "2018-05-04T20:48:22.062965",
            "id": "dd9778b8-7576-40a2-912c-223fe8a1df29",
            "failure_reason": null,
            "failure_merchant_transfer_id": null,
            "amount": 5
        },
        {
            "status": "failed",
            "reason": "requested_by_customer",
            "processor_id": "re_1BTuvkEs15StTpSvSTTpommv",
            "processor": "stripe",
            "notes": null,
            "inserted_at": "2017-11-30T16:30:34.631848",
            "id": "aad5ba81-d73a-4a99-bd82-823e490b7d68",
            "failure_reason": "customer_account_closed",
            "failure_merchant_transfer_id": "tr_1BTv3SEs15StTpSvoFttIcYV",
            "amount": 2
        },
        {
            "status": "succeeded",
            "reason": "requested_by_customer",
            "processor_id": "re_1BR0AtEs15StTpSv4nRc0UZE",
            "processor": "stripe",
            "notes": "customer was furious. oh well",
            "inserted_at": "2017-11-22T15:30:09.120225",
            "id": "be123b2d-2d0d-4d58-abab-bff04382685c",
            "failure_reason": null,
            "failure_merchant_transfer_id": null,
            "amount": 50
        },
        {
            "status": "succeeded",
            "reason": null,
            "processor_id": "re_17FoZ1Es15StTpSvXcn4uLPp",
            "processor": "stripe",
            "notes": null,
            "inserted_at": "2015-12-08T16:43:43.000000",
            "id": "6e071d81-f030-41db-8511-6aa646f6dc75",
            "failure_reason": null,
            "failure_merchant_transfer_id": null,
            "amount": 472.19
        }
    ],
    "page_size": 10,
    "page_number": 1
}
```

Gets all refunds

### HTTP request
`GET /refund`

### Parameters
Parameter | Type | Description
--------- | -----------  | --------
reason | string | reason for the refund. requested_by_customer, duplicate, or fraudulent
date | date | refund created date. YYYY-MM-DD
dateRange | string | refunds created in a range of dates. Ex. use "2018-01-01|2018-02-01"
customer | string | refunds with the given customer id
