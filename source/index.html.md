---
title: Partial.ly API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell: cURL
  - javascript: Node

toc_footers:
  - <a href='https:/partial.ly/merchant'>Partial.ly Merchant Portal</a>

includes:
  - offers
  - offer_items
  - customers
  - payment_methods
  - payment_plans
  - payments
  - payment_schedules
  - installments
  - refunds
  - disputes
  - webhooks

search: true
---

# Introduction

The Partial.ly API allows you to programmatically access the data in your Partial.ly merchant account and create payment plans and payments.
The API is currently in beta mode, so there may be minor changes. If you have feedback we would love to hear it.

Our API follows REST principles and accepts and returns all data in [JSON](http://www.json.org/) format. POST and PUT data must be JSON encoded, and must have the `Content-Type: application/json` HTTP header.
The base URL for all API calls is **https://partial.ly/api/v1**. Future versions of the API will have a different base URL, ensuring that we will never make breaking changes.

We also have a staging server at demo.partial.ly, so if you want to test your integration please use the base URL **https://demo.partial.ly/api/v1**. Note that you will have to register for a separate account here than your live Partial.ly account.

If you have any questions you can't find the answer to here, please contact our [support](mailto:support@partial.ly).

# Authentication

> To authorize, use this code:

```shell
curl "https://partial.ly/api/v1/offer"
  -H "Authorization: Bearer your_api_key"
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/v1/offer',
  headers: {
    Authorization: 'Bearer your_api_key'
  }
};

request(options, function (error, response, body) {
  // asynchronous callback function
});
```

> Make sure to replace `your_api_key` with your API key.

Partial.ly uses API keys to allow access to the API. You can find your API key in the [Partial.ly merchant portal](https://partial.ly/merchant) by navigating to Settings > general. If you have don't have any API key yet, click the generate key link to generate a new API key. Your API key carry many privileges, so be sure to keep them secure! Do not share your secret API keys in publicly accessible areas such as GitHub, client-side code, and so forth.

Partial.ly expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: Bearer your_api_key`

<aside class="notice">
You must replace <code>your_api_key</code> with your personal API key.
</aside>

All API requests must be made over (HTTPS)[https://en.wikipedia.org/wiki/HTTPS]. Calls made over plain HTTP will be redirected to the corresponding HTTPS URL. API requests without authentication will also fail.

# Pagination

Most API calls to list resources can be paginated using the following parameters. Some resources will have additional parameters you can use to filter the results.

Parameter | Description
--------- | -----------
page | the page number to return results for
per_page | the number of results to return per page

All response objects will have the following properties, in addition to a property containing the array of results.

Name | Description
--------- | -----------
page_number | the current page
page_size | the number of results per page
total_entires | the total number of objects returned
total_pages | the total number of pages found

# Errors

```json
{
    "message": "Could not create offer",
    "errors": [
        "Name: can't be blank"
    ]
}
```

Successful API calls will return HTTP status code 200. API calls that result in an error will either return HTTP status code 400 is there is a problem with the input, or 404 if a given object could not be found. The response body will be JSON object with a message key giving an overview of the issue. If there are issues with specific fields, there will also be an errors key with an array of field specific errors.
