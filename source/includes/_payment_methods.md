# Payment Methods

A saved payment method attached to a customer. May be any type of credit or debit card, or a bank account for supported US based customers.

## Create payment method

```shell
curl "https://partial.ly/api/payment_method" \
  -H "Authorization: Bearer your_api_key" \
  -H "Content-Type: application/json" \
  -X POST \
  --data '{"type": "card", "token_id": "tok_ch", "customer_id": "452cc42f-d999-4c0f-998b-325c4e0e8f57"}'
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/payment_method',
  headers: {
    Authorization: 'Bearer your_api_key'
  },
  method: 'POST',
  json: true,
  body: {
    customer_id: '452cc42f-d999-4c0f-998b-325c4e0e8f57',
    type: 'card',
    token_id: 'tok_ch'
  }
};

request(options, function (error, response, payment_method) {
  // asynchronous callback function
});
```

> The above command returns JSON structured like this:

```json
{
    "type": "card",
    "integration_details": {
        "last4": "0009",
        "funding": "credit",
        "exp_year": 2019,
        "exp_month": 11,
        "country": "CH",
        "brand": "Visa"
    },
    "inserted_at": "2018-11-27T21:38:39.520750",
    "id": "36afc659-f03d-485b-a850-65871fa759a7"
}
```

> Example HTML to capture card details with Stripe.js

```html
<form id="payment-form">
  <div id="card-element">
    <!-- A Stripe Element will be inserted here. -->
  </div>

  <button id="btnSubmit">Add Payment Method</button>
</form>

<script src="https://js.stripe.com/v3/"></script>
<script>
// partial.ly public key
var publicKey = 'pk_test_eV3vdXbE4SrfLjJYn9XUSUwx',
  stripe = Stripe(publicKey),
  elements = stripe.elements();

// Create an instance of the card Element.
var card = elements.create('card');

// Add an instance of the card Element into the `card-element` <div>.
card.mount('#card-element');

// Handle form submission.
var form = document.getElementById('payment-form');
form.addEventListener('submit', function() {
  // tokenize payment data with Stripe
  stripe.createToken(card).then(function(result) {
    if (result.error) {
      // Inform the user if there was an error.
      alert(result.error.message);
    } else {
      // use result.token.id to send in the token_id field to Partial.ly
    }
  });
});
</script>
```

> Example HTML to capture bank account details with Plaid

```html
<a href="#" id="addBankAccount">Add bank account</a>
<script src="https://cdn.plaid.com/link/v2/stable/link-initialize.js"></script>
<script>
var linkHandler = Plaid.create({
  env: 'production',
  clientName: 'Partial.ly',
  key: '5763eb087a2251e8bf13f1b53b9ab8',
  product: 'auth',
  selectAccount: true,
  apiVersion: 'v2',
  onSuccess: function(public_token, meta) {
    // send public_token to Partial.ly
    // send meta.account.id to Partial.ly in account_id parameter
  }
});

document.getElementById('addBankAccount').onclick = function() {
  linkHandler.open();
};
</script>
```

Payment method details must not be sent directly to Partial.ly. They need to be captured and tokenized with [Stripe.js](https://stripe.com/docs/stripe-js) before being sent to Partial.ly.

When creating your Stripe.js object, use the following public key to initialize the Stripe SDK

`pk_live_huSEiMMI7gSK7ORKzZB448xr`

When testing with our [Partial.ly test server](https://demo.partial.ly), use the following public key

`pk_test_eV3vdXbE4SrfLjJYn9XUSUwx`

To create bank account payment methods for US based customers, you must use [Plaid Link](https://plaid.com/docs/quickstart/)

When setting up Plaid, use the following public key to initialize the Plaid SDK.

`5763eb087a2251e8bf13f1b53b9ab8`

When testing with our test server, set the plaid env to `sandbox`, otherwise set the plaid env to `production`

### HTTP Request

`POST /payment_method`

### Parameters

Parameter | Type | Required | Description
--------- | -----------  | -------- | ------
customer_id | string | yes | id of customer to associate with
type | string | yes | card or bank_account
token_id | string | no | the token id returned from Stripe.js. Required for card type
public_token | string | no | token returned from Plaid. Required for bank_account type
account_id | string | no | account id returned from Plaid. Required for bank_account type

## List payment methods

```shell
curl "https://partial.ly/api/payment_method?customer_id=452cc42f-d999-4c0f-998b-325c4e0e8f57" \
  -H "Authorization: Bearer your_api_key"
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/payment_method?customer_id=452cc42f-d999-4c0f-998b-325c4e0e8f57',
  headers: {
    Authorization: 'Bearer your_api_key'
  }
};

request(options, function (error, response, payment_methods) {
  // asynchronous callback function
});
```

> The above command returns JSON structured like this:

```json
{
    "payment_methods": [
        {
            "type": "card",
            "integration_details": {
                "last4": "0009",
                "funding": "credit",
                "exp_year": 2019,
                "exp_month": 11,
                "country": "CH",
                "brand": "Visa"
            },
            "inserted_at": "2018-11-27T21:38:39.520750",
            "id": "36afc659-f03d-485b-a850-65871fa759a7"
        },
        {
            "type": "bank_account",
            "integration_details": {
                "routing_number": "110000000",
                "plaid_access_token": "access-sandbox-a5232606-49c8-4768-b07e-4fd0baf38c21",
                "last4": "6789",
                "country": "US",
                "bank_name": "STRIPE TEST BANK",
                "account_holder_type": null,
                "account_holder_name": null
            },
            "inserted_at": "2018-06-05T13:33:04.980000",
            "id": "1754a33c-419f-4233-9c62-7fd9f5f7d088"
        }
    ]
}
```

Gets the list of payment methods associated with a customer

### HTTP Request

`GET /payment_method`

### Parameters

Parameter | Type | Required | Description
--------- | -----------  | -------- | ------
customer_id | string | yes | id of customer to get payment methods for

## Delete payment method

```shell
curl "https://partial.ly/api/payment_method/36afc659-f03d-485b-a850-65871fa759a7" \
  -H "Authorization: Bearer your_api_key" \
  -X DELETE
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/payment_method/36afc659-f03d-485b-a850-65871fa759a7',
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
    "message": "Payment method deleted"
}
```

Deletes a payment method. Payment methods which are attached to open payment plans may not be deleted.

`DELETE /payment_method/:id`

*replace :id with the id of the payment method to delete*
