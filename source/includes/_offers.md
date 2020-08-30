# Offers

An offer is a set of terms for a potential payment plan. Think of an offer as a template for a payment plan. There are 3 variables that can be customized for an offer: down payment, payment frequency, and term. Offers can additionally have dynamically added line items, such as a payment plan fee or service charge, which can be a fixed amount or percent of the payment plan subtotal. Offers can be embedded on your website (Shopify, WooCommerce, etc.) to provide a payment plan checkout option for your customers. Or they can be used as template to quickly and easily create payment plans for invoices (FreshBooks, QuickBooks, Harvest)

## List All Offers

```shell
curl "https://partial.ly/api/offer" \
  -H "Authorization: Bearer your_api_key"
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/offer',
  headers: {
    Authorization: 'Bearer your_api_key'
  }
};

request(options, function (error, response, offers) {
  // asynchronous callback function
  // offers will be already decoded JSON array of offers
});
```

> The above command returns JSON structured like this:

```json
[
  {
        "updated_at": "2018-10-05T14:30:20.303624",
        "term_units": "months",
        "term_min": 3,
        "term_max": 6,
        "term_flexible": true,
        "term_date": "2017-09-27",
        "term": 3,
        "require_ship_to": null,
        "payment_schedule_description": null,
        "name": "euro test",
        "merchant_id": "60689b2d-d391-417e-9f59-7b65e3e50d8d",
        "integration_options": null,
        "inserted_at": "2015-09-11T05:50:30.000000",
        "id": "60aed439-473f-48e0-80ef-3a8627dd243a",
        "frequency_units": "months",
        "frequency_min": null,
        "frequency_max": null,
        "frequency_flexible": false,
        "frequency_days": [],
        "frequency": 1,
        "down_payment_type": "percent",
        "down_payment_min": 10,
        "down_payment_max": 70,
        "down_payment_flexible": false,
        "down_payment": 25,
        "currency": "EUR",
        "auto_process": true
    }
]
```

###

This endpoint retrieves all offers.

### HTTP Request

`GET /offer`

## Get a Specific Offer

```shell
curl "https://partial.ly/api/offer/60aed439-473f-48e0-80ef-3a8627dd243a" \
  -H "Authorization: Bearer your_api_key"
```

```javascript
var request = require('request');

var options = {
  url: 'https://partial.ly/api/offer/60aed439-473f-48e0-80ef-3a8627dd243a',
  headers: {
    Authorization: 'Bearer your_api_key'
  }
};

request(options, function (error, response, offer) {

});
```

> The above command returns JSON structured like this:

```json
{
    "updated_at": "2018-10-05T14:30:20.303624",
    "term_units": "months",
    "term_min": 3,
    "term_max": 6,
    "term_flexible": true,
    "term_date": "2017-09-27",
    "term": 3,
    "require_ship_to": null,
    "payment_schedule_description": null,
    "name": "euro test",
    "merchant_id": "60689b2d-d391-417e-9f59-7b65e3e50d8d",
    "integration_options": null,
    "inserted_at": "2015-09-11T05:50:30.000000",
    "id": "60aed439-473f-48e0-80ef-3a8627dd243a",
    "frequency_units": "months",
    "frequency_min": null,
    "frequency_max": null,
    "frequency_flexible": false,
    "frequency_days": [],
    "frequency": 1,
    "down_payment_type": "percent",
    "down_payment_min": 10,
    "down_payment_max": 70,
    "down_payment_flexible": false,
    "down_payment": 25,
    "currency": "EUR",
    "auto_process": true
}
```

This endpoint retrieves a specific offer.

### HTTP Request

`GET /offer/:id`

*replace :id in the URL with the id of the offer to retrieve*

## Create a new offer

```shell
curl "https://partial.ly/api/offer" \
  -X POST \
  -H "Authorization: Bearer your_api_key" \
  -H "Content-Type: application/json" \
  --data '{"name": "Sample offer"}'
```

```javascript
var request = require('request');

var options = {
  url: 'https://partial.ly/api/offer',
  headers: {
    Authorization: 'Bearer your_api_key'
  },
  method: 'POST',
  json: true,
  body: {
    name: 'Sample offer'
  }
};

request(options, function (error, response, offer) {

});
```

> The above command returns JSON structured like this:

```json
{
    "updated_at": "2018-10-05T14:30:20.303624",
    "term_units": "months",
    "term_min": 0,
    "term_max": 0,
    "term_flexible": false,
    "term_date": null,
    "term": 3,
    "require_ship_to": null,
    "payment_schedule_description": null,
    "name": "Sample offer",
    "merchant_id": "60689b2d-d391-417e-9f59-7b65e3e50d8d",
    "integration_options": null,
    "inserted_at": "2015-09-11T05:50:30.000000",
    "id": "60aed439-473f-48e0-80ef-3a8627dd243a",
    "frequency_units": "months",
    "frequency_min": null,
    "frequency_max": null,
    "frequency_flexible": false,
    "frequency_days": [],
    "frequency": 1,
    "down_payment_type": "percent",
    "down_payment_min": 0,
    "down_payment_max": 0,
    "down_payment_flexible": false,
    "down_payment": 0,
    "currency": "USD",
    "auto_process": true
}
```

This endpoint creates a new offer

### HTTP Request

`POST /offer`

### Parameters

Parameter | Type | Required | Default | Description
--------- | -----------  | ----------- | -------- | ------
name |  string | yes | | The name of the offer
auto_process | boolean | no | true | true to automatically schedule future payments, false for manual payments
payment_schedule_description | string | no | | for manual payment plans, a description of the payment schedule
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

## Update an offer

```shell
curl "https://partial.ly/api/offer/60aed439-473f-48e0-80ef-3a8627dd243a" \
  -X PUT \
  -H "Authorization: Bearer your_api_key" \
  -H "Content-Type: application/json" \
  --data '{"down_payment": 5}'
```

```javascript
var request = require('request');

var options = {
  url: 'https://partial.ly/api/offer/60aed439-473f-48e0-80ef-3a8627dd243a',
  headers: {
    Authorization: 'Bearer your_api_key'
  },
  method: 'PUT',
  json: true,
  body: {
    down_payment: 5
  }
};

request(options, function (error, response, offer) {

});
```

> The above command returns JSON structured like this:

```json
{
    "updated_at": "2018-10-05T14:30:20.303624",
    "term_units": "months",
    "term_min": 0,
    "term_max": 0,
    "term_flexible": false,
    "term_date": null,
    "term": 3,
    "require_ship_to": null,
    "payment_schedule_description": null,
    "name": "Sample offer",
    "merchant_id": "60689b2d-d391-417e-9f59-7b65e3e50d8d",
    "integration_options": null,
    "inserted_at": "2015-09-11T05:50:30.000000",
    "id": "60aed439-473f-48e0-80ef-3a8627dd243a",
    "frequency_units": "months",
    "frequency_min": null,
    "frequency_max": null,
    "frequency_flexible": false,
    "frequency_days": [],
    "frequency": 1,
    "down_payment_type": "percent",
    "down_payment_min": 0,
    "down_payment_max": 0,
    "down_payment_flexible": false,
    "down_payment": 5,
    "currency": "USD",
    "auto_process": true
}
```

Updates an existing offer

`PUT /offer/:id`

*replace :id with the id of the offer to update*

Same parameters as create offer
