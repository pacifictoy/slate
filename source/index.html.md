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
You must replace <code>bearer-token</code> with your personal API key.
</aside>

# Receiving Payments

## Virtual Accounts

```shell
curl --location --request POST 'https://qa.pallapi.com/api/v2/create-virtual-account' \
--header 'Authorization: Bearer secret_key' \
--form 'bank_code="MANDIRI"' \
--form 'name="PT Test Bersama"' \
--form 'is_single_use="true"' \
--form 'amount="10000000"' \
--form 'external_id="124"' \
--form 'expiration_date="2024-01-21"' 
```

> The above command returns JSON structured like this:

```json
{
    "id": "d718b709b87a83e0950eca8803a123f9",
    "status": "pending",
    "external_id": "124",
    "bank_code": "MANDIRI",
    "account_name": "PT Test Bersama",
    "account_number": "889089999421835",
    "amount": 10000000,
    "is_single_use": true,
    "expiration_date": "2024-01-21 07:00:00"
}
```

This endpoint create virtual accounts to receive payments.

### HTTP Request

`POST https://qa.pallapi.com/api/v2/create-virtual-account`

### Query Parameters

Parameter | Required? | Description
--------- | ------- | -----------
bank_code | Yes | You can specify "BNI, "BRI", "BSS" (Bank Sahabat Sampoerna), "BCA" (BCA coming soon)
external_id | Yes | This is the ID that you can set to correspond to your ID
account_name | Yes | This is the name that will show up in the internet banking / ATM
is_single_use | Yes | If true, then this virtual account can only be used once
amount | Yes | The amount that you are expecting the payer to pay
expiration_date | No | The expiration date of the virtual account

### Notes Regarding Status

The "status" field in the resulting API means:

Status | Meaning
-----  | -------
Unpaid | API call has been made successfully and we are now waiting for payer to transfer money into the virtual account.
Pending| Payer has paid money into the virtual account, but we are waiting for bank settlement (2pm daily).
Done   | Payer has paid money into the virtual account and the balance is now in your income wallet. (observable from the callback and dashboard)

### Notes Regarding QA Testing

When you call create-virtual-account in QA environment, it will be in "Unpaid" status for one minute, then it'll turn to "Pending" status for five minutes, finally it will turn to "Done" status.

### Notes Regarding Errors

All errors will be displayed with the proper error messages.
Please let us know if you need assistance.

## Retail Outlet (Alfamart)

```shell
curl --location --request POST 'https://qa.pallapi.com/api/v2/create-retail-payment' \
--header 'Authorization: Bearer secret_key' \
--form 'name="PT Test Bersama"' \
--form 'retail_outlet_name="ALFAMART"' \
--form 'amount="1000000"' \
--form 'external_id="123"' \
--form 'expiration_date="2023-01-21"'
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
    "expiration_date": "2023-01-21 07:00:00"  
}
```

This endpoint create payment endpoint from retail outlet (alfamart)

### HTTP Request

`POST https://qa.pallapi.com/api/v2/create-retail-payment`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
retail_outlet_name| Yes | Please specify 'ALFAMART' for alfamart
name      | Yes     | The account name 
amount | Yes | The amount that you are expecting the payer to pay
external_id | Yes | This is the ID that you can set to correspond to your ID
expiration_date | No | The expiration date of the payment code

### Notes Regarding Status

The "status" field in the resulting API means:

Status | Meaning
-----  | -------
Unpaid | API call has been made successfully and we are now waiting for payer to pay money in the retail outlet
Pending| Payer has paid money in the retail outlet, and we are waiting for settlement. 
Done   | Payer has paid money in the retail outlet and the balance is now available in your income wallet. (observable from the callback and dashboard)

### Notes Regarding QA Testing

When you call create-retail-payment in QA environment, it will be in "Unpaid" status for one minute, then it'll turn to "Pending" status for five minutes, finally it will turn to "Done" status.

### Notes Regarding Errors

All errors will be displayed with the proper error messages.
Please let us know if you need assistance.


## QRIS

```shell
curl --location --request POST 'https://qa.pallapi.com/api/v2/create-qris' \
--header 'Authorization: Bearer secret_key' \
--form 'amount="200000"' \
--form 'external_id="12345"'
```
> The above command returns JSON structured like this:

```json
{
 "id": "639fa43877d5a7c9979a7bd169a948e5",
    "status": "unpaid",
    "external_id": "12345",
    "amount": 200000,
    "fees": 5900,
    "expiration_date": "2022-02-23 19:20:28",
    "timeout_in_seconds": 86400,
    "qris_string": "0002010102122666001011893600817022000897302151702220000089730303URE51440014ID.CO.QRIS.WWW0215ID20221532054320303URE52043333530336054062000005802ID5925xxxxxx  
    xxxxxxxxxxx 610510160624101055160905126448880667730712D3WWBP19GJ3P63043F83"
}
```

This endpoint creates a QRIS code so that your customer can pay using Gopay, OVO, Jenius, etc.
The QRIS will be in a form of string and you need to render it into a QR code using publically available QR Code renderer.
If you are just testing manually, you can copy-paste into this website: https://www.patrick-wied.at/static/qrgen/

### HTTP Request

`POST https://qa.pallapi.com/api/v2/create-qris`

Note: The resulting QRIS will be valid for 24h. Currently there is no way to control this expiration date via API.

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
amount | Yes | The amount that you are expecting the payer to pay
external_id | Yes | This is the ID that you can set to correspond to your ID

### Notes Regarding Status

The "status" field in the resulting API means:

Status | Meaning
-----  | -------
Unpaid | API call has been made successfully and we are now waiting for payer to scan the QRIS and pay
Pending| Payer has scanned the QRIS and paid. Now we are waiting for settlement. 
Done   | The balance is now available in your income wallet. (observable from the callback and dashboard)


# Disburse Payments
You can send money to Indonesian bank accounts using our API.
Please make sure that you have enough balance in the disburse wallet else this call will fail.
To increase your balance in your disburse wallet, please do "Top Up" from the dashboard and notify us for approval.

### HTTP Request

`POST https://qa.pallapi.com/api/v2/create-disbursement`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
bank_code | Yes | Target bank for disbursal (please see list below)
account_name      | Yes     | The account name 
account_number | Yes | The account number at the bank for disbursal
description | Yes | description
external_id | Yes | This corresponds to your ID
amount | Yes | The amount to disburse (integer rupiah)
use_flashloan | No | Whether you want to use the flashloan feature, please use 1 for yes, 0 for no

### Notes Regarding Status

The "status" field in the resulting API means:

Status | Meaning
-----  | -------
Pending| API call has been made successfully and our system will disburse to the recipient.
Done   | Disbursal process is completed and the receipient should be able to access the money. (observable from callback and dashboard)

### Notes Regarding QA Testing

When you call create-disbursement in QA environment, it will be in Pending status by default.
If you call it with the amount of IDR 90,000, it will be in Pending status then a minute later it should turn into Done status.

### Notes Regarding Errors

All errors will be displayed with the proper error messages.
Please let us know if you need assistance.

```shell
curl --location --request POST 'https://qa.pallapi.com/api/v2/create-disbursement' \
--header 'Authorization: Bearer secret_key' \
--form 'bank_code="BCA"' \
--form 'account_name="James Bond"' \
--form 'account_number="12345678"' \
--form 'description="Bayar Nasabah"' \
--form 'external_id="124"' \
--form 'amount="20000000"'
```
> The above command returns JSON structured like this:

```json
{
    "id": "b39ff2bfb731485ba159585d44759f15",
    "status": "pending",
    "external_id": "124",
    "bank_code": "BCA",
    "account_name": "James Bond",
    "account_number": "12345678",
    "description": "Bayar Nasabah",
    "amount": 1000000,
    "fees": 8400,
    "flashloan_status": "not_flashloan"
}
```

# Callbacks

When payer pays via Virtual Account or Retail Outlet, you can specify a callback function that will be called upon the payment event. Instead of checking the payment status periodically into our server, please specify your callback endpoint so your system knows when payment is made.
Please specify your URL callbacks in the dashboard.

There are three callbacks function that you can specify:

* Virtual Account Created Event
* Virtual Account Paid Event
* Retail Outlet Paid Event

Every callback from pallapi will have a verification token in the header of the callback, with the parameter X-CALLBACK-TOKEN.

You can find the token on your dashboard -> settings -> access token

Please reject any callbacks that are sent to your URL that does not have this same token on the header of the callback.

# Querying Your Transactions

Despite our callbacks, in case you want to query the status and detail of your transactions, you can do that by using the following APIs below.

## Querying Virtual Account Transaction

```shell
curl --location --request GET 'https://qa.pallapi.com/api/v2/query-virtual-account?id=8fa0275e0bacecc3a5e0bebf411bf525' \
--header 'Authorization: Bearer secret_key'
```
> The above command returns JSON structured like this:

```json
[
    {
        "id": "8fa0275e0bacecc3a5e0bebf411bf525",
        "status": "done",
        "external_id": "164204282674209c1ceec",
        "bank_code": "BRI",
        "account_name": "PT Test Bersama",
        "account_number": "920019999476603",
        "amount": 30000,
        "is_single_use": false,
        "expiration_date": "2023-01-21 00:00:00"
    }
]
```

## Querying Retail Payment Transaction

```shell
curl --location --request GET 'https://qa.pallapi.com/api/v2/query-retail-payment?id=b6bc8d1ef421bbe59511e4c22872ff39' \
--header 'Authorization: Bearer secret_key'
```
> The above command returns JSON structured like this:

```json
[
    {
        "id": "b6bc8d1ef421bbe59511e4c22872ff39",
        "status": "done",
        "external_id": "123",
        "retail_outlet_name": "ALFAMART",
        "name": "123456789012345678901234567890123456789",
        "payment_code": "TEST62620",
        "amount": 200000,
        "is_single_use": false,
        "expiration_date": "2023-01-21 00:00:00"
    }
]
```

## Querying Disbursement Transaction

```shell
curl --location --request GET 'https://qa.pallapi.com/api/v2/query-disbursement?id=0230607a5744348b4ad9272e3c4c48b1' \
--header 'Authorization: Bearer secret_key' \
```
> The above command returns JSON structured like this:

```json
[
    {
        "id": "0230607a5744348b4ad9272e3c4c48b1",
        "status": "done",
        "external_id": "124",
        "bank_code": "TABUNGAN_PENSIUNAN_NASIONAL",
        "account_name": "Dono Warkop",
        "account_number": "0123456",
        "description": "Bayar Nasabah",
        "amount": 10000,
        "fees": 8888,
        "flashloan_status": "not_flashloan"
    }
]
```

## Querying QRIS Transaction

```shell
curl --location --request GET 'https://qa.pallapi.com/api/v2/query-qris?id=f80aa195bb8e7d49f86566ddba2b4022' \
--header 'Authorization: Bearer secret_key' \
```
> The above command returns JSON structured like this:

```json
[
    {
        "id": "f80aa195bb8e7d49f86566ddba2b4022",
        "status": "unpaid",
        "external_id": "12345",
        "amount": 15000,
        "fees": -8505
    }
]
```

# Querying Your Wallet Balance

You can query your wallet balance.
Principally, our system differentiates between the income wallet vs disburse wallet.
The detail of the calculation of the balance of the income wallet and disburse wallet can be viewed in dashboard.
But this API will give you the usable balance in income wallet and disburse wallet.

**What is withdrawable amount?**

This is the amount that is in the income wallet that you can withdraw to your bank account.

**What is disburseable amount?**

This is the amount that is in the disburse wallet that you can disburse to your users.

**Can I use the amount in the withdrawable amount directly to disburse to my users?**

Principally, it is not possible to do that directly. The proper way to do this is to withdraw the money from income wallet to your bank account. Then make a bank transfer from your bank account into the topup bank account, and the balance will appear in the disburseable amount.

### HTTP Request

`POST https://qa.pallapi.com/api/v2/me`

### Query Parameter
 
 leave it empty please

```shell
curl --location --request GET 'https://qa.pallapi.com/api/v2/me' \
--header 'Authorization: Bearer secret_key'
```
> The above command returns JSON structured like this:

```json
{
    "name": "PT Test Client",
    "email": "qa@testclient.com",
    "remaining_withdrawable_amount": 5337593200,
    "remaining_disbursable_amount": 7425800,
    "max_allowable_loan": 6994120,
    "total_used_loan_amount": "1011400"
}
```

# Querying Your Transactions

You can view your transactions in the GUI dashboard.
However, if you prefer to use API, then you can call our API to get all of your transactions.

### HTTP Request

`GET https://qa.pallapi.com/api/v2/get-transaction-list?start_date=2022-01-01&end_date=2022-01-31`

### Query Parameter
 
 must have start_date and end_date in the GET query parameter

```shell
curl --location --request GET 'https://qa.pallapi.com/api/v2/get-transaction-list?start_date=2022-01-01&end_date=2022-01-31 \
--header 'Authorization: Bearer secret_key'
```
> The above command returns JSON structured like this:

```json
[
    {
        "transaction_type": "bni",
        "flashloan_status": "not_flashloan",
        "amount": 1000000,
        "fee": -8400,
        "total": 991600,
        "bank_code": "BNI",
        "name": null,
        "description": null,
        "account_number": "8808999966811159",
        "date": "2022-01-10"
    },
    {
        "transaction_type": "disbursement",
        "flashloan_status": "not_flashloan",
        "amount": 90000,
        "fee": 8400,
        "total": 98400,
        "bank_code": "BCA",
        "name": "Test Account",
        "description": "disbursement IDR 90.000",
        "account_number": "1234567890",
        "date": "2022-01-10"
    },
]
```



# Supported Banks for Disbursal
| Bank Code                | Channel Type | Bank Name                                                                                            |   |   |
|-----------------------------|--------------|------------------------------------------------------------------------------------------------------|---|---|
| ACEH                        | Bank         | BPD Aceh                                                                                             |   |   |
| ACEH_UUS                    | Bank         | BPD Aceh UUS                                                                                         |   |   |
| AGRIS                       | Bank         | Bank Agris                                                                                           |   |   |
| AGRONIAGA                   | Bank         | Bank BRI Agroniaga                                                                                   |   |   |
| AMAR                        | Bank         | Bank Amar Indonesia (formerly Anglomas International Bank)                                           |   |   |
| ANDARA                      | Bank         | Bank Andara                                                                                          |   |   |
| ANGLOMAS                    | Bank         | Anglomas International Bank                                                                          |   |   |
| ANZ                         | Bank         | Bank ANZ Indonesia                                                                                   |   |   |
| ARTA_NIAGA_KENCANA          | Bank         | Bank Arta Niaga Kencana                                                                              |   |   |
| ARTHA                       | Bank         | Bank Artha Graha International                                                                       |   |   |
| ARTOS                       | Bank         | Bank Artos Indonesia (Bank Jago)                                                                     |   |   |
| BALI                        | Bank         | BPD Bali                                                                                             |   |   |
| BAML                        | Bank         | Bank of America Merill-Lynch                                                                         |   |   |
| BANGKOK                     | Bank         | Bangkok Bank                                                                                         |   |   |
| BANTEN                      | Bank         | BPD Banten (formerly Bank Pundi Indonesia)                                                           |   |   |
| BCA                         | Bank         | Bank Central Asia (BCA)                                                                              |   |   |
| BCA_DIGITAL                 | Bank         | Bank Central Asia Digital (BluBCA)                                                                   |   |   |
| BCA_SYR                     | Bank         | Bank Central Asia (BCA) Syariah                                                                      |   |   |
| BENGKULU                    | Bank         | BPD Bengkulu                                                                                         |   |   |
| BISNIS_INTERNASIONAL        | Bank         | Bank Bisnis Internasional                                                                            |   |   |
| BJB                         | Bank         | Bank BJB                                                                                             |   |   |
| BJB_SYR                     | Bank         | Bank BJB Syariah                                                                                     |   |   |
| BNI                         | Bank         | Bank Negara Indonesia (BNI)                                                                          |   |   |
| BNP_PARIBAS                 | Bank         | Bank BNP Paribas                                                                                     |   |   |
| BOC                         | Bank         | Bank of China (BOC)                                                                                  |   |   |
| BRI                         | Bank         | Bank Rakyat Indonesia (BRI)                                                                          |   |   |
| BTN                         | Bank         | Bank Tabungan Negara (BTN)                                                                           |   |   |
| BTN_UUS                     | Bank         | Bank Tabungan Negara (BTN) UUS                                                                       |   |   |
| BTPN_SYARIAH                | Bank         | BTPN Syariah (formerly BTPN UUS and Bank Sahabat Purba Danarta)                                      |   |   |
| BUKOPIN                     | Bank         | Bank Bukopin                                                                                         |   |   |
| BUKOPIN_SYR                 | Bank         | Bank Syariah Bukopin                                                                                 |   |   |
| BUMI_ARTA                   | Bank         | Bank Bumi Arta                                                                                       |   |   |
| BSI                         | Bank         | Bank Syariah Indonesia (BSI)                                                                         |   |   |
| CAPITAL                     | Bank         | Bank Capital Indonesia                                                                               |   |   |
| CCB                         | Bank         | China Construction Bank Indonesia (formerly Bank Antar Daerah and Bank Windu Kentjana International) |   |   |
| CENTRATAMA                  | Bank         | Centratama Nasional Bank                                                                             |   |   |
| CHINATRUST                  | Bank         | Bank Chinatrust Indonesia                                                                            |   |   |
| CIMB                        | Bank         | Bank CIMB Niaga                                                                                      |   |   |
| CIMB_UUS                    | Bank         | Bank CIMB Niaga UUS                                                                                  |   |   |
| CITIBANK                    | Bank         | Citibank                                                                                             |   |   |
| COMMONWEALTH                | Bank         | Bank Commonwealth                                                                                    |   |   |
| DAERAH_ISTIMEWA             | Bank         | BPD Daerah Istimewa Yogyakarta (DIY)                                                                 |   |   |
| DAERAH_ISTIMEWA_UUS         | Bank         | BPD Daerah Istimewa Yogyakarta (DIY) UUS                                                             |   |   |
| DANAMON                     | Bank         | Bank Danamon                                                                                         |   |   |
| DANAMON_UUS                 | Bank         | Bank Danamon UUS                                                                                     |   |   |
| DBS                         | Bank         | Bank DBS Indonesia                                                                                   |   |   |
| DEUTSCHE                    | Bank         | Deutsche Bank                                                                                        |   |   |
| DINAR_INDONESIA             | Bank         | Bank Dinar Indonesia                                                                                 |   |   |
| DKI                         | Bank         | Bank DKI                                                                                             |   |   |
| DKI_UUS                     | Bank         | Bank DKI UUS                                                                                         |   |   |
| EXIMBANK                    | Bank         | Indonesia Eximbank (formerly Bank Ekspor Indonesia)                                                  |   |   |
| FAMA                        | Bank         | Bank Fama International                                                                              |   |   |
| GANESHA                     | Bank         | Bank Ganesha                                                                                         |   |   |
| HANA                        | Bank         | Bank Hana                                                                                            |   |   |
| HARDA_INTERNASIONAL         | Bank         | Bank Harda Internasional                                                                             |   |   |
| HIMPUNAN_SAUDARA            | Bank         | Bank Himpunan Saudara 1906                                                                           |   |   |
| HSBC                        | Bank         | HSBC Indonesia (formerly Bank Ekonomi Raharja)                                                       |   |   |
| HSBC_UUS                    | Bank         | Hongkong and Shanghai Bank Corporation (HSBC) UUS                                                    |   |   |
| ICBC                        | Bank         | Bank ICBC Indonesia                                                                                  |   |   |
| INA_PERDANA                 | Bank         | Bank Ina Perdania                                                                                    |   |   |
| INDEX_SELINDO               | Bank         | Bank Index Selindo                                                                                   |   |   |
| INDIA                       | Bank         | Bank of India Indonesia                                                                              |   |   |
| JAMBI                       | Bank         | BPD Jambi                                                                                            |   |   |
| JAMBI_UUS                   | Bank         | BPD Jambi UUS                                                                                        |   |   |
| JASA_JAKARTA                | Bank         | Bank Jasa Jakarta                                                                                    |   |   |
| JAWA_TENGAH                 | Bank         | BPD Jawa Tengah                                                                                      |   |   |
| JAWA_TENGAH_UUS             | Bank         | BPD Jawa Tengah UUS                                                                                  |   |   |
| JAWA_TIMUR                  | Bank         | BPD Jawa Timur                                                                                       |   |   |
| JAWA_TIMUR_UUS              | Bank         | BPD Jawa Timur UUS                                                                                   |   |   |
| JPMORGAN                    | Bank         | JP Morgan Chase Bank                                                                                 |   |   |
| JTRUST                      | Bank         | Bank JTrust Indonesia (formerly Bank Mutiara)                                                        |   |   |
| KALIMANTAN_BARAT            | Bank         | BPD Kalimantan Barat                                                                                 |   |   |
| KALIMANTAN_BARAT_UUS        | Bank         | BPD Kalimantan Barat UUS                                                                             |   |   |
| KALIMANTAN_SELATAN          | Bank         | BPD Kalimantan Selatan                                                                               |   |   |
| KALIMANTAN_SELATAN_UUS      | Bank         | BPD Kalimantan Selatan UUS                                                                           |   |   |
| KALIMANTAN_TENGAH           | Bank         | BPD Kalimantan Tengah                                                                                |   |   |
| KALIMANTAN_TIMUR            | Bank         | BPD Kalimantan Timur                                                                                 |   |   |
| KALIMANTAN_TIMUR_UUS        | Bank         | BPD Kalimantan Timur UUS                                                                             |   |   |
| KESEJAHTERAAN_EKONOMI       | Bank         | Bank Kesejahteraan Ekonomi                                                                           |   |   |
| LAMPUNG                     | Bank         | BPD Lampung                                                                                          |   |   |
| MALUKU                      | Bank         | BPD Maluku                                                                                           |   |   |
| MANDIRI                     | Bank         | Bank Mandiri                                                                                         |   |   |
| MANDIRI_TASPEN              | Bank         | Mandiri Taspen Pos (formerly Bank Sinar Harapan Bali)                                                |   |   |
| MASPION                     | Bank         | Bank Maspion Indonesia                                                                               |   |   |
| MAYAPADA                    | Bank         | Bank Mayapada International                                                                          |   |   |
| MAYBANK                     | Bank         | Bank Maybank                                                                                         |   |   |
| MAYBANK_SYR                 | Bank         | Bank Maybank Syariah Indonesia                                                                       |   |   |
| MAYORA                      | Bank         | Bank Mayora                                                                                          |   |   |
| MEGA                        | Bank         | Bank Mega                                                                                            |   |   |
| MEGA_SYR                    | Bank         | Bank Syariah Mega                                                                                    |   |   |
| MESTIKA_DHARMA              | Bank         | Bank Mestika Dharma                                                                                  |   |   |
| MITRA_NIAGA                 | Bank         | Bank Mitra Niaga                                                                                     |   |   |
| MITSUI                      | Bank         | Bank Sumitomo Mitsui Indonesia                                                                       |   |   |
| MIZUHO                      | Bank         | Bank Mizuho Indonesia                                                                                |   |   |
| MNC_INTERNASIONAL           | Bank         | Bank MNC Internasional                                                                               |   |   |
| MUAMALAT                    | Bank         | Bank Muamalat Indonesia                                                                              |   |   |
| MULTI_ARTA_SENTOSA          | Bank         | Bank Multi Arta Sentosa                                                                              |   |   |
| NATIONALNOBU                | Bank         | Bank Nationalnobu                                                                                    |   |   |
| NUSA_TENGGARA_BARAT         | Bank         | BPD Nusa Tenggara Barat                                                                              |   |   |
| NUSA_TENGGARA_BARAT_UUS     | Bank         | BPD Nusa Tenggara Barat UUS                                                                          |   |   |
| NUSA_TENGGARA_TIMUR         | Bank         | BPD Nusa Tenggara Timur                                                                              |   |   |
| NUSANTARA_PARAHYANGAN       | Bank         | Bank Nusantara Parahyangan                                                                           |   |   |
| OCBC                        | Bank         | Bank OCBC NISP                                                                                       |   |   |
| OCBC_UUS                    | Bank         | Bank OCBC NISP UUS                                                                                   |   |   |
| OKE                         | Bank         | Bank Oke Indonesia (formerly Bank Andara)                                                            |   |   |
| PANIN                       | Bank         | Bank Panin                                                                                           |   |   |
| PANIN_SYR                   | Bank         | Bank Panin Syariah                                                                                   |   |   |
| PAPUA                       | Bank         | BPD Papua                                                                                            |   |   |
| PERMATA                     | Bank         | Bank Permata                                                                                         |   |   |
| PERMATA_UUS                 | Bank         | Bank Permata UUS                                                                                     |   |   |
| PRIMA_MASTER                | Bank         | Prima Master Bank                                                                                    |   |   |
| QNB_INDONESIA               | Bank         | Bank QNB Indonesia (formerly Bank QNB Kesawan)                                                       |   |   |
| RABOBANK                    | Bank         | Bank Rabobank International Indonesia                                                                |   |   |
| RBS                         | Bank         | Royal Bank of Scotland (RBS)                                                                         |   |   |
| RESONA                      | Bank         | Bank Resona Perdania                                                                                 |   |   |
| RIAU_DAN_KEPRI              | Bank         | BPD Riau Dan Kepri                                                                                   |   |   |
| RIAU_DAN_KEPRI_UUS          | Bank         | BPD Riau Dan Kepri UUS                                                                               |   |   |
| ROYAL                       | Bank         | Bank Royal Indonesia                                                                                 |   |   |
| SAHABAT_SAMPOERNA           | Bank         | Bank Sahabat Sampoerna                                                                               |   |   |
| SBI_INDONESIA               | Bank         | Bank SBI Indonesia                                                                                   |   |   |
| SHINHAN                     | Bank         | Bank Shinhan Indonesia (formerly Bank Metro Express)                                                 |   |   |
| SINARMAS                    | Bank         | Bank Sinarmas                                                                                        |   |   |
| SINARMAS_UUS                | Bank         | Bank Sinarmas UUS                                                                                    |   |   |
| STANDARD_CHARTERED          | Bank         | Standard Charted Bank                                                                                |   |   |
| SULAWESI                    | Bank         | BPD Sulawesi Tengah                                                                                  |   |   |
| SULAWESI_TENGGARA           | Bank         | BPD Sulawesi Tenggara                                                                                |   |   |
| SULSELBAR                   | Bank         | BPD Sulselbar                                                                                        |   |   |
| SULSELBAR_UUS               | Bank         | BPD Sulselbar UUS                                                                                    |   |   |
| SULUT                       | Bank         | BPD Sulut                                                                                            |   |   |
| SUMATERA_BARAT              | Bank         | BPD Sumatera Barat                                                                                   |   |   |
| SUMATERA_BARAT_UUS          | Bank         | BPD Sumatera Barat UUS                                                                               |   |   |
| SUMSEL_DAN_BABEL            | Bank         | BPD Sumsel Dan Babel                                                                                 |   |   |
| SUMSEL_DAN_BABEL_UUS        | Bank         | BPD Sumsel Dan Babel UUS                                                                             |   |   |
| SUMUT                       | Bank         | BPD Sumut                                                                                            |   |   |
| SUMUT_UUS                   | Bank         | BPD Sumut UUS                                                                                        |   |   |
| TABUNGAN_PENSIUNAN_NASIONAL | Bank         | Bank Tabungan Pensiunan Nasional (BTPN)                                                              |   |   |
| TOKYO                       | Bank         | Bank of Tokyo Mitsubishi UFJ (MUFJ)                                                                  |   |   |
| UOB                         | Bank         | Bank UOB Indonesia                                                                                   |   |   |
| VICTORIA_INTERNASIONAL      | Bank         | Bank Victoria Internasional                                                                          |   |   |
| VICTORIA_SYR                | Bank         | Bank Victoria Syariah                                                                                |   |   |
| WOORI                       | Bank         | Bank Woori Indonesia                                                                                 |   |   |
| WOORI_SAUDARA               | Bank         | Bank Woori Saudara Indonesia 1906 (formerly Bank Himpunan Saudara and Bank Woori Indonesia)          |   |   |
| YUDHA_BHAKTI                | Bank         | Bank Yudha Bhakti (Bank Neo Commerce)                                                                |   |   |