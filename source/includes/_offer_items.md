# Offer Items

Offer items are additional line items that can automatically be added to a payment plan, a customer fee for instance.
Offer items can be for fixed amounts or a percent of the payment plan subtotal.

## List offer items

```shell
curl "https://partial.ly/api/offer_item?offer_id=60aed439-473f-48e0-80ef-3a8627dd243a"
  -H "Authorization: Bearer your_api_key"
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/offer_item?offer_id=60aed439-473f-48e0-80ef-3a8627dd243a',
  headers: {
    Authorization: 'Bearer your_api_key'
  }
};

request(options, function (error, response, body) {
  // asynchronous callback function
  // body.offer_items
});
```

> The above command returns JSON structured like this:

```json
{
    "offer_items": [
        {
            "updated_at": "2018-10-05T14:49:20.558357",
            "offer_id": "60aed439-473f-48e0-80ef-3a8627dd243a",
            "name": "test api creation",
            "inserted_at": "2018-10-05T14:44:59.631679",
            "id": "61ed5fdf-fbdf-4b06-9a8a-0fb05cdf6f26",
            "amount_type": "percent",
            "amount": 2.2
        }
    ]
}
```

Gets all the offer items associated with an offer

### HTTP Request

`GET /offer_item`

### Parameters

Parameter | Type | Required | Description
--------- | -----------  | -------- | ------
offer_id | string | yes | offer id to get offer items for

## Create a new offer item

```shell
curl "https://partial.ly/api/offer_item"
  -H "Authorization: Bearer your_api_key" \
  -H "Content-Type: application/json" \
  -X POST \
  --data '{"name": "processing fee", "amount": 2.5, "amount_type": "percent", "offer_id": "60aed439-473f-48e0-80ef-3a8627dd243a"}'
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/offer_item',
  headers: {
    Authorization: 'Bearer your_api_key'
  },
  method: 'POST',
  json: true,
  body: {
    offer_id: '60aed439-473f-48e0-80ef-3a8627dd243a',
    name: 'processing fee',
    amount: 2.5,
    amount_type: 'percent'
  }
};

request(options, function (error, response, offer_item) {
  // asynchronous callback function
});
```

> The above command returns JSON structured like this:

```json
{
    "updated_at": "2018-11-27T21:00:47.730189",
    "offer_id": "60aed439-473f-48e0-80ef-3a8627dd243a",
    "name": "processing fee",
    "inserted_at": "2018-11-27T21:00:47.730182",
    "id": "a9bec449-da6e-4dea-ad4a-a716aa90d40c",
    "amount_type": "percent",
    "amount": 2.5
}
```

Creates a new offer item associated with an offer

### HTTP Request

`POST /offer_item`

### Parameters

Parameter | Type | Required | Default | Description
--------- | -----------  | ----------- | -------- | ------
name |  string | yes | | The name of the offer item
offer_id | string | yes | | id of offer to associate item with
amount_type | string | yes | | either percent or fixed
amount | decimal | yes | | The amount to add to the plan. For percent will be this percent of the plan subtotal

## Update offer item

```shell
curl "https://partial.ly/api/offer_item/a9bec449-da6e-4dea-ad4a-a716aa90d40c"
  -H "Authorization: Bearer your_api_key" \
  -H "Content-Type: application/json" \
  -X PUT \
  --data '{"amount": 3.75}'
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/offer_item/a9bec449-da6e-4dea-ad4a-a716aa90d40c',
  headers: {
    Authorization: 'Bearer your_api_key'
  },
  method: 'PUT',
  json: true,
  body: {
    amount: 3.75
  }
};

request(options, function (error, response, offer_item) {
  // asynchronous callback function
});
```

> The above command returns JSON structured like this:

```json
{
    "updated_at": "2018-11-27T21:00:47.730189",
    "offer_id": "60aed439-473f-48e0-80ef-3a8627dd243a",
    "name": "processing fee",
    "inserted_at": "2018-11-27T21:00:47.730182",
    "id": "a9bec449-da6e-4dea-ad4a-a716aa90d40c",
    "amount_type": "percent",
    "amount": 3.75
}
```

updates an existing offer item

`PUT /offer_item/:id`

*replace :id with the id of the offer item to update*

Same parameters as create offer item

## Delete offer item

```shell
curl "https://partial.ly/api/offer_item/a9bec449-da6e-4dea-ad4a-a716aa90d40c"
  -H "Authorization: Bearer your_api_key" \
  -X DELETE
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/offer_item/a9bec449-da6e-4dea-ad4a-a716aa90d40c',
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
    "message": "OfferItem deleted"
}
```
deletes an offer item

`DELETE /offer_item/:id`

*replace :id with the id of the offer item to delete*
