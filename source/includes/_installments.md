# Installments

Installments represent a payment to be processed in the future, as scheduled by a payment plan's payment schedule.

## Pay scheduled installment

```shell
curl "https://partial.ly/api/installment/pay/6a85b229-69e6-4850-8309-edf7bfc3cec1" \
  -H "Authorization: Bearer your_api_key" \
  -X PUT
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/installment/pay/6a85b229-69e6-4850-8309-edf7bfc3cec1',
  headers: {
    Authorization: 'Bearer your_api_key'
  },
  method: 'PUT'
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
    "processor_id": "ch_1DbaigEs15StTpSvhzmfGjSA",
    "processor_destination_id": null,
    "processor": "stripe",
    "payment_plan_id": "8f999efe-5798-4b51-a6c5-d21b0d04124b",
    "paid_at": "2018-11-28T21:37:21",
    "message": null,
    "inserted_at": "2018-11-28T21:37:21.296658",
    "id": "291221ab-b0a2-41dc-909b-1d1793bd23d8",
    "currency": "USD",
    "amount": 256.25
}
```

Pays a scheduled installment. Must be scheduled in the future and not already paid. If successful will return the payment.

### HTTP request

`PUT /installment/pay/:id`

## List installments

```shell
curl "https://partial.ly/api/installment?payment_schedule_id=1f5d9716-a041-47ef-8c57-86af22efa31c" \
  -H "Authorization: Bearer your_api_key"
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/installment?payment_schedule_id=1f5d9716-a041-47ef-8c57-86af22efa31c',
  headers: {
    Authorization: 'Bearer your_api_key'
  }
};

request(options, function (error, response, installments) {
  // asynchronous callback function
});
```
> Returns an array of installments as follows

```json
[
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
```

Gets all installments for the given *payment_schedule_id*. You can also list all upcoming installments for a customer by providing a *customer_id* parameter.
