---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  
toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for the Kittn API
---

# Introduction

Welcome to the Palladium API! You can use our API to access Palladium API endpoints to receive payments, disburse payments and get advanced disbursement

Our API is using REST. Our API has predictable, resource-oriented URLs, and uses HTTP response codes to indicate API errors. We use built-in HTTP features and HTTP verbs, which are understood by off-the-shelf HTTP clients. JSON is returned by all API responses, including errors.

Please feel free to [contact us](mailto:admin@pallapi.com) if you have any questions. 

# Dashboards

Please access qa dashboard from [here](https://qa.pallapi.com/admin)

Please access prod dashboard from [here](https://prod.pallapi.com/admin)

# API URL

QA: [http://qa.pallapi.com/api/v2/end-point-name](http://qa.pallapi.com/api/v2/end-point-name)

PROD: [https://prod.pallapi.com/api/v2/end-point-name] (https://prod.pallapi.com/api/v2/end-point-name)

# Authentication

> To authorize, we use bearer token, and please get your token from the dashboard -> "Access Token"


```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here" \
  -H "Authorization: bearer-token"
```

> Make sure to replace `bearer-token` with your API key.

Please use API keys to allow access to the API. You can see your API key at our [qa dashboard](http://qa.pallapi.com) or [prod dashboard](http://prod.pallapi.com).

We expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: bearer-token`

<aside class="notice">
You must replace <code>blablabla</code> with your personal API key.
</aside>

# Receiving Payments

## Virtual Accounts

```shell
curl --location --request POST 'https://qa.pallapi.com/api/v2/create-virtual-account' \
--header 'Authorization: Bearer bearer-token' \
--form 'bank_code="BNI"' \
--form 'name="PT Test Bersama"' \
--form 'is_single_use="true"' \
--form 'amount="10000000"' \
--form 'external_id="124"'
```

> The above command returns JSON structured like this:

```json
{
    "id": "d718b709b87a83e0950eca8803a123f9",
    "status": "pending",
    "external_id": "124",
    "bank_code": "BNI",
    "account_name": "XDT-PT Test Bersama",
    "account_number": "8808999986203536",
    "amount": 10000000,
    "is_single_use": true,
    "expiration_date": "2052-12-23 00:00:00"
}
```

This endpoint create virtual accounts to receive payments.

### HTTP Request

`POST https://qa.pallapi.com/api/v2/create-virtual-account`

### Query Parameters

Parameter | Required? | Description
--------- | ------- | -----------
bank_code | Yes | You can specify "BNI, "BCA"
external_id | Yes | This is the ID that you can set to correspond to your ID
account_name | Yes | This is what will show up in the internet banking / ATM

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Retail Outlet (Alfamart)

```shell
curl --location --request POST 'https://qa.pallapi.com/api/v2/create-retail-payment' \
--header 'Authorization: Bearer pallapi_secret_development_90f9c69a09ee36d31a1b734d75d8fa74' \
--form 'name="PT Test Bersama"' \
--form 'retail_outlet_name="ALFAMART"' \
--form 'amount="1000000"' \
--form 'external_id="123"'
```
> The above command returns JSON structured like this:

```json
{
    "id": "44533f6787696bb0aac54113c342d00d",
    "status": "pending",
    "external_id": "123",
    "retail_outlet_name": "ALFAMART",
    "name": "PT Test Bersama",
    "payment_code": "TEST307106",
    "amount": 1000000,
    "is_single_use": false,
    "expiration_date": "2052-12-23 00:00:00"  
}

### HTTP Request

`GET http://example.com/api/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Retail Payments (Alfamart)

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2" \
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

## Callbacks

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.delete(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.delete(2)
```

```shell
curl "http://example.com/api/kittens/2" \
  -X DELETE \
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.delete(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "deleted" : ":("
}
```

This endpoint deletes a specific kitten.

### HTTP Request

`DELETE http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to delete

# Disburse Payments