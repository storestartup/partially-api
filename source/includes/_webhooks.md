# Webhooks

## Create a webhook

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

Creates a new webhook. Events that can be subscribed to are plan_opened, plan_paid, plan_defaulted, payment_succeeded, and payment_failed

### HTTP request

`POST /webhook`

### Parameters
Parameter | Type | Required | Description
--------- | -----------  | -------- | ------
url | string | yes | complete URL of the webhook, including https
event | string | no | the event to subscribe to, or * for all events (default)

## List webhooks

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

Gets all webhooks

### HTTP request
`GET /webhook`

## Update a webhook

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

Updates an existing webhook

### HTTP request

`PUT /webhook/:id`

*replace :id with the id of the webhook to update*

### Parameters
Parameter | Type | Required | Description
--------- | -----------  | -------- | ------
url | string | no | complete URL of the webhook, including https
event | string | no | the event to subscribe to, or * for all events (default)
active | boolean | no | whether or not the webhook is active

## Delete a webhook

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
deletes a webhook

`DELETE /webhook/:id`

*replace :id with the id of the webhook to delete*
