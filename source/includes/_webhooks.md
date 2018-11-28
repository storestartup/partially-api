# Webhooks

## Create a webhook

```shell
curl "https://partial.ly/api/v1/webhook"
  -H "Authorization: Bearer your_api_key" \
  -X POST \
  --data '{"url": "http://localhost:8888/hook"}'
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/v1/webhook',
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
    "id": "d5e43bed-041c-4ff4-b422-a401b10aad79"
}
```

Creates a new webhook

### HTTP request

`POST /webhook`

### Parameters
Parameter | Type | Required | Description
--------- | -----------  | -------- | ------
url | string | yes | complete URL of the webhook, including https

## List webhooks

```shell
curl "https://partial.ly/api/v1/webhook"
  -H "Authorization: Bearer your_api_key"
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/v1/webhook',
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
        "id": "0aeece05-8e24-419b-a2ad-466a70de9b4b"
    }
]
```

Gets all webhooks

### HTTP request
`GET /webhook`

## Delete a webhook

```shell
curl "https://partial.ly/api/v1/webhook/a9bec449-da6e-4dea-ad4a-a716aa90d40c"
  -H "Authorization: Bearer your_api_key" \
  -X DELETE
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/v1/webhook/a9bec449-da6e-4dea-ad4a-a716aa90d40c',
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
