---
title: Documentation for Palladium API 

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  
toc_footers:
  - <a href='https://qa.pallapi.com/admin'>Get your access key from the dashboard</a>

includes:
#  - errors

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for the Pallapi API
---

# Introduction

Welcome to the Palladium API! You can use our API to access Palladium API endpoints to receive payments, disburse payments and get advanced disbursement

Our API is using REST. Our API has predictable, resource-oriented URLs, and uses HTTP response codes to indicate API errors. We use built-in HTTP features and HTTP verbs, which are understood by off-the-shelf HTTP clients. JSON is returned by all API responses, including errors.

Please feel free to [contact us](mailto:admin@pallapi.com) if you have any questions. 

# Dashboards

Please access qa dashboard from [here](https://qa.pallapi.com/admin)

Please access prod dashboard from [here](https://pallapi.com/admin)

# API URL

QA: [http://qa.pallapi.com/api/v2/end-point-name](http://qa.pallapi.com/api/v2/end-point-name)

PROD: [https://pallapi.com/api/v2/end-point-name] (https://pallapi.com/api/v2/end-point-name)

# Authentication

> To authorize, we use bearer token, and please get your token from the dashboard -> "Access Token"


```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here" \
  -H "Authorization: bearer-token"
```

> Make sure to replace `bearer-token` with your API key.

Please use API keys to allow access to the API. You can see your API key at our [qa dashboard](https://qa.pallapi.com) or [prod dashboard](https://pallapi.com).

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
account_name | Yes | This is the name that will show up in the internet banking / ATM
is_single_use | Yes | If true, then this virtual account can only be used once
amount | Yes | The amount that you are expecting the payer to pay


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
```

This endpoint create payment endpoint from retail outlet (such as alfamart)

### HTTP Request

`POST https://qa.pallapi.com/api/v2/create-retail-payment`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
retail_outlet_name| Yes | Please specify 'ALFAMART' for alfamart
name      | Yes     | The account name 
amount | Yes | The amount that you are expecting the payer to pay
external_id | Yes | This is the ID that you can set to correspond to your ID


# Disburse Payments
You can send money to Indonesian bank accounts using our API.

### HTTP Request

`POST https://qa.pallapi.com/api/v2/create-disbursement`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
retail_outlet_name| Yes | Please specify 'ALFAMART' for alfamart
name      | Yes     | The account name 
amount | Yes | The amount that you are expecting the payer to pay
external_id | Yes | This is the ID that you can set to correspond to your ID

```shell
curl --location --request POST 'https://qa.pallapi.com/api/v2/create-disbursement' \
--header 'Authorization: Bearer secret_key' \
--form 'bank_code="BCA"' \
--form 'account_name="Dono Warkop"' \
--form 'account_number="12345678"' \
--form 'description="Bayar Nasabah"' \
--form 'external_id="124"' \
--form 'email_to="dono@gmail.com"' \
--form 'email_cc="dono@gmail.com"' \
--form 'email_bcc="dono@gmail.com"' \
--form 'amount="20000000"'
```
> The above command returns JSON structured like this:

```json
{
    "id": "b39ff2bfb731485ba159585d44759f15",
    "status": "pending",
    "external_id": "124",
    "bank_code": "BCA",
    "account_name": "Dono Warkop",
    "account_number": "12345678",
    "description": "Bayar Nasabah",
    "amount": 20000000
}
```

# Callbacks

When payer pays via Virtual Account or Retail Outlet, you can specify a callback function that will be called upon the payment event.
Please specify the callbacks in the dashboard.

There are three callbacks function that you can specify:
- Virtual Account Created Event
- Virtual Account Paid Event
- Retail Outlet Paid Event