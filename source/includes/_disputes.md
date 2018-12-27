# Disputes

## List disputes

```shell
curl "https://partial.ly/api/dispute"
  -H "Authorization: Bearer your_api_key"
```

```javascript
// examples use the request library
// https://github.com/request/request
var request = require('request');

var options = {
  url: 'https://partial.ly/api/dispute',
  headers: {
    Authorization: 'Bearer your_api_key'
  }
};

request(options, function (error, response, disputes) {
  // asynchronous callback function
});
```
> Returns an array of disputes as follows

```json
[
  {
    "payment_id": "3098916e-1eb1-4a02-83a2-99e32d6d606f",
    "amount": 25.65,
    "inserted_at": "2017-09-05T07:35:45.000000",
    "status": "warning_needs_response",
    "reason": "unrecognized"
  }
]
```

Gets all disputes

### HTTP request
`GET /dispute`

### Parameters
Parameter | Type | Description
--------- | -----------  | --------
status | string | status of the dispute
reason | string | reason for the dispute
date | date | dispute created date. YYYY-MM-DD
dateRange | string | disputes created in a range of dates. Ex. use "2018-01-01|2018-02-01"
customer | string | disputes with the given customer id
