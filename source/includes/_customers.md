# Customers

## List All Customers

```shell
curl "https://partial.ly/api/customer"
  -H "Authorization: Bearer your_api_key"
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/customer',
  headers: {
    Authorization: 'Bearer your_api_key'
  }
};

request(options, function (error, response, customers) {
  // asynchronous callback function
  // offers will be already decoded JSON array of customers
});
```

> The above command returns JSON structured like this:

```json
{
    "total_pages": 1,
    "total_entries": 2,
    "page_size": 10,
    "page_number": 1,
    "customers": [
        {
            "timezone": "America/New_York",
            "shipto_state": "FL",
            "shipto_postal_code": "33601",
            "shipto_name": "Sample Customer",
            "shipto_country": "US",
            "shipto_city": "Tampa",
            "shipto_address2": null,
            "shipto_address": "123 N. Tampa St.",
            "phone": "81325551212",
            "last_name": "Customer",
            "inserted_at": "2015-09-11T05:54:27.000000",
            "id": "6d30d3c8-d024-45cc-9c3f-356fc8aee23d",
            "first_name": "Sample",
            "email": "sample.customer@gmail.com"
        },
        {
            "timezone": "America/New_York",
            "shipto_state": null,
            "shipto_postal_code": null,
            "shipto_name": null,
            "shipto_country": null,
            "shipto_city": null,
            "shipto_address2": null,
            "shipto_address": null,
            "phone": "5042265544",
            "last_name": "Appleseed",
            "inserted_at": "2015-09-23T15:03:20.000000",
            "id": "9a426155-a927-4984-8738-0bd1ffc15248",
            "first_name": "John",
            "email": "jappleseed@yahoo.com"
        }
    ]
}
```

###

This endpoint retrieves all customers.

### HTTP Request

`GET /customer`

### Query Parameters

Parameter | Description
--------- | -----------
q | Search query. Will search for email if query contains an @, otherwise will search for partial match on first and last names

## Get a Specific Customer

```shell
curl "https://partial.ly/api/customer/9a426155-a927-4984-8738-0bd1ffc15248"
  -H "Authorization: Bearer your_api_key"
```

```javascript
var request = require('request');

var options = {
  url: 'https://partial.ly/api/customer/9a426155-a927-4984-8738-0bd1ffc15248',
  headers: {
    Authorization: 'Bearer your_api_key'
  }
};

request(options, function (error, response, customer) {

});
```

> The above command returns JSON structured like this:

```json
{
    "timezone": "America/New_York",
    "shipto_state": null,
    "shipto_postal_code": null,
    "shipto_name": null,
    "shipto_country": null,
    "shipto_city": null,
    "shipto_address2": null,
    "shipto_address": null,
    "phone": "5042265544",
    "last_name": "Appleseed",
    "inserted_at": "2015-09-23T15:03:20.000000",
    "id": "9a426155-a927-4984-8738-0bd1ffc15248",
    "first_name": "John",
    "email": "jappleseed@yahoo.com"
}
```

This endpoint retrieves a specific customer.

### HTTP Request

`GET /customer/:id`

*replace :id in the URL with the id of the customer to retrieve*

## Create a new customer

```shell
curl "https://partial.ly/api/customer" \
  -X POST \
  -H "Authorization: Bearer your_api_key" \
  --data '{"email": "testing@aol.com", "first_name": "John", "last_name": "Doe"}'
```

```javascript
var request = require('request');

var options = {
  url: 'https://partial.ly/api/customer',
  headers: {
    Authorization: 'Bearer your_api_key'
  },
  method: 'POST',
  json: true,
  body: {
    email: 'testing@aol.com',
    first_name: 'John',
    last_name: 'Doe'
  }
};

request(options, function (error, response, customer) {

});
```

> The above command returns JSON structured like this:

```json
{
    "timezone": "America/New_York",
    "shipto_state": null,
    "shipto_postal_code": null,
    "shipto_name": null,
    "shipto_country": "US",
    "shipto_city": null,
    "shipto_address2": null,
    "shipto_address": null,
    "phone": null,
    "last_name": "Doe",
    "inserted_at": "2018-11-27T20:10:19.437168",
    "id": "53479e98-27a9-49e2-856c-bf47d0796ffc",
    "first_name": "John",
    "email": "testing@aol.com"
}
```

This endpoint creates a new customer

### HTTP Request

`POST /customer`

### Parameters

Parameter | Type | Required | Default | Description
--------- | -----------  | ----------- | -------- | ------
email |  string | yes | | Customer email
first_name | string | no |  | First name
last_name | string | no |  | Last name
phone | string | no |  | Phone number
language | string | no | en | Customer interface display language
timezone | string | no | America/New_York | timezone for date formatting
shipto_name | string | no | | default ship to name for customer's payment plans
shipto_address | string | no | | street address
shipto_address2 | string | no | | street address line 2
shipto_city | string | no | | city
shipto_state | string | no | | 2 letter state/province/region code
shipto_country | string | no | US | 2 letter country code
shipto_postal_code | string | no | | ZIP/postal code

## Update a customer

```shell
curl "https://partial.ly/api/customer/53479e98-27a9-49e2-856c-bf47d0796ffc" \
  -X PUT \
  -H "Authorization: Bearer your_api_key" \
  --data '{"phone": "+12125551212"}'
```

```javascript
var request = require('request');

var options = {
  url: 'https://partial.ly/api/customer/53479e98-27a9-49e2-856c-bf47d0796ffc',
  headers: {
    Authorization: 'Bearer your_api_key'
  },
  method: 'PUT',
  json: true,
  body: {
    phone: '+12125551212'
  }
};

request(options, function (error, response, customer) {

});
```

> The above command returns JSON structured like this:

```json
{
    "timezone": "America/New_York",
    "shipto_state": null,
    "shipto_postal_code": null,
    "shipto_name": null,
    "shipto_country": "US",
    "shipto_city": null,
    "shipto_address2": null,
    "shipto_address": null,
    "phone": "+12125551212",
    "last_name": "Doe",
    "inserted_at": "2018-11-27T20:10:19.437168",
    "id": "53479e98-27a9-49e2-856c-bf47d0796ffc",
    "first_name": "John",
    "email": "testing@aol.com"
}
```

Updates an existing customer

`PUT /customer/:id`

*replace :id with the id of the customer to update*

Same parameters as create customer

## GDPR remove

```shell
curl "https://partial.ly/api/customer/gdpr_remove/53479e98-27a9-49e2-856c-bf47d0796ffc" \
  -X PUT \
  -H "Authorization: Bearer your_api_key"
```

```javascript
var request = require('request');

var options = {
  url: 'https://partial.ly/api/customer/gdpr_remove/53479e98-27a9-49e2-856c-bf47d0796ffc',
  headers: {
    Authorization: 'Bearer your_api_key'
  },
  method: 'PUT',
  json: true
};

request(options, function (error, response, body) {

});
```

> The above command returns JSON structured like this:

```json
{
    "message": "Customer account deleted"
}
```

Removal of customer information for GDPR. Customers with open payment plans cannot be deleted.
If the customer has payment and payment plan information in Partial.ly, the payment and payment plan information will not be deleted,
but all personally identifiable customer information will be anonymized. If the customer has no payment plan history all information will be deleted.

`PUT /customer/gdpr_remove/:id`

*replace :id with the id of the customer to to remove*

## Log in with password

```shell
curl "https://partial.ly/api/customer/login_password" \
  -X POST \
  -H "Authorization: Bearer your_api_key" \
  --data '{"email": "aaa@y.co", "password": "test"}'
```

```javascript
var request = require('request');

var options = {
  url: 'https://partial.ly/api/customer/login_password',
  headers: {
    Authorization: 'Bearer your_api_key'
  },
  method: 'POST',
  json: true,
  body: {
    email: 'aaa@y.co',
    password: 'test'
  }
};

request(options, function (error, response, cust) {

});
```

> The above command returns JSON structured like this:

```json
{
    "timezone": "America/New_York",
    "shipto_state": null,
    "shipto_postal_code": null,
    "shipto_name": null,
    "shipto_country": "US",
    "shipto_city": null,
    "shipto_address2": null,
    "shipto_address": null,
    "phone": null,
    "last_name": "Person",
    "inserted_at": "2018-11-28T16:57:42.923471",
    "id": "e3cbf1dc-0c11-483f-b604-d44fd93aac90",
    "first_name": "Testing",
    "email": "aaa@y.co"
}
```

Authenticate a customer with their email and password. Alternatively, customers can have a token generated and sent to their email with the [email login token](#email-login-token) method, which can then be used to login with the [login with a token](#login-with-a-token) method.

`POST /customer/login_password`

### Parameters
Parameter | Type | Required
--------- | -----------  | --------
email |  string | yes
password | string | yes

## Email login token

```shell
curl "https://partial.ly/api/customer/login_email" \
  -X POST \
  -H "Authorization: Bearer your_api_key" \
  --data '{"email": "aaa@y.co"}'
```

```javascript
var request = require('request');

var options = {
  url: 'https://partial.ly/api/customer/login_email',
  headers: {
    Authorization: 'Bearer your_api_key'
  },
  method: 'POST',
  json: true,
  body: {
    email: 'aaa@y.co'
  }
};

request(options, function (error, response, body) {

});
```

> The above command returns JSON structured like this:

```json
{
    "message": "Token sent"
}
```

Generates a token which can be used with the [login with a token](#login-with-a-token) method and emails it to the customer.

`POST /customer/login_email`

### Parameters
Parameter | Type | Required
--------- | -----------  | --------
email |  string | yes

## Login with a token

```shell
curl "https://partial.ly/api/customer/login_token" \
  -X POST \
  -H "Authorization: Bearer your_api_key" \
  --data '{"token": "N5E9gL"}'
```

```javascript
var request = require('request');

var options = {
  url: 'https://partial.ly/api/customer/login_token',
  headers: {
    Authorization: 'Bearer your_api_key'
  },
  method: 'POST',
  json: true,
  body: {
    token: 'N5E9gL'
  }
};

request(options, function (error, response, cust) {

});
```

> The above command returns JSON structured like this:

```json
{
    "timezone": "America/New_York",
    "shipto_state": null,
    "shipto_postal_code": null,
    "shipto_name": null,
    "shipto_country": "US",
    "shipto_city": null,
    "shipto_address2": null,
    "shipto_address": null,
    "phone": null,
    "last_name": "Person",
    "inserted_at": "2018-11-28T16:57:42.923471",
    "id": "e3cbf1dc-0c11-483f-b604-d44fd93aac90",
    "first_name": "Testing",
    "email": "aaa@y.co"
}
```

Authenticate a customer with a token generated from the [email a login token](#email-login-token) step.

`POST /customer/login_token`

### Parameters
Parameter | Type | Required
--------- | -----------  | --------
token |  string | yes
