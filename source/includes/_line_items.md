# Line Items

## Create a line item

```shell
curl "https://partial.ly/api/line_item"
  -H "Authorization: Bearer your_api_key" \
  -H "Content-Type: application/json" \
  -X POST \
  -- data '{"payment_plan_id": "4a2da2b5-ccbc-4631-93ad-ea30da49a412", "amount": 5.99, "description": "adjustment"}'
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/line_item',
  headers: {
    Authorization: 'Bearer your_api_key'
  },
  method: 'POST',
  json: true,
  body: {
    payment_plan_id: '4a2da2b5-ccbc-4631-93ad-ea30da49a412',
    amount: 5.99,
    description: 'adjustment'
  }
};

request(options, function (error, response, payment) {
  // asynchronous callback function
});
```

> The above command returns JSON structured like this:

```json
{
    "amount": 5.99,
    "description": "adjustment",
    "dynamic": false,
    "dynamic_type": "generic",
    "id": "2faa91f1-7b01-4111-9af9-967e70c272e8",
    "inserted_at": "2019-02-22T15:24:23",
    "meta": null,
    "quantity": 1
}
```

Adds a new line item to a payment plan. This can only be done on payment plans not in the open or paid statuses.

Adding a new line item to a payment plan will automatically adjust the payment plan total.

Line item amounts can be negative to decrease a payment plan amount, for example to give a customer a discount.

### HTTP Request

`POST /line_item`

### Parameters
Parameter | Type | Required | Description
--------- | -----------  | -------- | ------
payment_plan_id | string | yes | id of the plan to make a payment on
amount | decimal | yes | The unit price of the line item. May be positive or negative
description | string | yes | the line item's description
quantity | integer | no | Th quantity of the line item. Defaults to 1 if not specified
image | string | no | URL to an image for the line item
weight | decimal | no | weight of the line item
weight_units | string | no | g, kg, oz, or lb. defaults to lb
meta | object | no | additional meta information about the line item

## Delete a line item

```shell
curl "https://partial.ly/api/line_item/a9bec449-da6e-4dea-ad4a-a716aa90d40c"
  -H "Authorization: Bearer your_api_key" \
  -X DELETE
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/line_item/a9bec449-da6e-4dea-ad4a-a716aa90d40c',
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
    "message": "LineItem deleted"
}
```
Deletes a line item. This can only be done on payment plans not in the open or paid statuses.

Deleting a line item will automatically decrease the associated payment plan's total amount.

`DELETE /line_item/:id`

*replace :id with the id of the line item to delete*
