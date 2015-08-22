---
title: API Reference

language_tabs:
  - shell

toc_footers:
  - <p>&copy; payever.de</p>

includes:

search: false
---

# Introduction

Welcome to the payever API!

# Authentication
> Request:

```shell
# Method: GET, Entpoint: /oauth/v2/token
curl -k https://mein.payever.de/oauth/v2/token \
  -d client_id="{client_id}" \
  -d client_secret="{client_secret}" \ 
  -d grant_type="http://www.payever.de/api/payment" \ 
  -d scope="API_CREATE_PAYMENT"
```

> Response:

```json
{ 
  "access_token": "NWNkODc3ZjFkZWUyZDM1Mzg2Njg2NzMzYjJkOTljYWExYzI3MDAwNzAzYTgyOWYyOWI2ZDc3Z jUxNmJhYTlmYw",
  "expires_in": 3600,
  "token_type": "bearer",
  "scope": "API_CREATE_PAYMENT",
  "refresh_token": "ZWIyZGU4ZDVjOTEwMjczM2FmNzA3ZWUyZGFlZGYzY2UyZjRhMmNkMTQ0NDhhMjc5ZmMzNGNh Mzg5YzFjZjQ4Mw"
}
```

The authentication to the payever API is done by providing one of your API keys in the request. You can manage your API keys from your account. You can have multiple API keys active at one time. Your API keys carry many privileges, so be sure to keep them secret. All API requests must be made over HTTPS. Calls made over plain HTTP will fail. You must authenticate for all requests.

**Method**: `GET`

**Entpoint**: `/oauth/v2/token`

### Attributes:

Attribute | required | Description
--------- | ------- | -----------
client_id | true | Your payever client_id.
client_secret | true | Your payever client_secret
scope | true | The scope you want to access. Available are: API_CREATE_PAYMENT: create a new payment API_PAYMENT_INFO: list your payments and retrieve payment information API_PAYMENT_ACTIONS: refund and authorize payments
grant_type | true | Grant type is always: http://www.payever.de/api/payment


# Create payment

> Request:

```shell
# Method: POST, Entpoint: /api/payment
curl -k https://mein.payever.de/api/payment 
-X POST 
-d access_token="{access_token}" 
-d amount="100" 
-d fee="10" 
-d order_id="900001291100" 
-d currency="USD" 
-d cart=json_encode(
 array(
 array(
 'name' => 'Some article',
 'price' => '15',
 'quantity' => '3',
 'description' => 'The new article',
 'thumbnail' => 'https://someitem.com/thumbnail.jpg',
 'url' => 'URL to article'
 ),
 array(
 'name' => 'Some item',
 'price' => '15',
 'quantity' => '3',
 'description' => 'The new item in black',
 'thumbnail' => 'https://someitem.com/thumbnail',
 'url' => 'URL to article'
 )
 )
 )
 , 
-d first_name="John" 
-d last_name="Doe" 
-d city="New York" 
-d zip="10019" 
-d street="5th Ave" 
-d street_number="342" 
-d country="US" 
-d email="john.doe@payever.de" 
-d phone="+1 (800) 566756" 
-d success_url="https://your.shop.tld/checkout/finish/--PAYMENT-ID--" 
-d failure_url="https://your.shop.tld/checkout/failed/--PAYMENT-ID--" 
```

> Response:

```json
{
"call":
  {
    "id": "d0e7144fcdeac4dcfa7b4eed3206c9cf",
    "shop_id": "31012408fad6abc962c5af237e1bb930",
    "status": "new",
    "created_at": "2014-05-21T13:52:46+0200",
    "payment_id": "d44c2eb96a608dd61ed9cf7bafa8bd24",
    "amount": 100,
    "order_id": "900001291100",
    "currency": "USD",
    "cart": "Some Goods",
    "first_name": "John",
    "last_name": "Doe",
    "street": "5th Ave",
    "street_number": "342",
    "zip": "10019",
    "city": "New York",
    "country": "US",
    "phone": " 1 (800) 566756",
    "email": "john.doe@payever.de",
    "success_url": "https://your.shop.tld/checkout/finish",
    "failure_url": "https://your.shop.tld/checkout/failed",
    "type": "create"
  },
"redirect_url": "https://mein.payever.de/shop/payment/api/6f5ee0715cd35f212ec6cce0284aec35"
}
```

**Method**: `POST`

**Entpoint**: `/api/payment`

The create payment function generates an html snippet (redirect_url) which has to be placed within your checkout. On the snippet, a customer can choose his preferred payment method and finalizes the payment. Most of our attributes are optional but the more you send to our API the less your customer has to enter by himself. To use this function you have to generate an access token for the scope: **API_CREATE_PAYMENT**.

After the gateway is created you have to redirect the customer to the `{redirect_url}`.

You can do this either by a real redirect or inject the URL as an iFrame to your cart.

After a successful transaction, the customer will be redirected to your provided success_url.

### Attributes:

Attribute | required | type | Description
--------- | -------- | ---- | -----------
amount | true | float | Order amount
fee | false | float | Shipping fee
order_id | true | string | (50 symbols max). ID or reference to identify the order. <br/><br/> In case santander_installment payment option is forced – 35 symbols max (santander requirement). <br/><br/>Please note, if payment option is not forced (customer is free to choose any available payment options) and santander_installment was choosed manually in payment options list – the order_id will be cutted out to 35 symbols automatically.
currency | true | string | 3 Letter ISO currency code
cart | true | array | Cart items with name, description, quantity, price, thumbnail and url to article
payment_method | false | string | Select between santander_installment, invoice, stripe, sofort, paypal, paymill_creditcard and paymill_directdebit. <br/><br/>If not specified, the payment page will contain a list of available payment options, and customer will be able to select one of them.
first_name | false | string | The customer’s first name
last_name | false | string | The customer’s last name
street | false | string | The street of the customer’s shipping address
street_number | false | string | The street number of the customer’s shipping address
zip | false | string | The zip of the customer’s shipping address
city | false | string | The city of customer’s shipping address
country | false | string | 2 Letter ISO country code
birthdate | false | DateTime | The customer´s birthdate
phone | false | string | The customer´s phone number
email | false | string | The customer’s email address
success_url | false | string | URL to redirect customer on successful transaction. You can add –PAYMENT-ID– and –CALL-ID– as a placeholder to catch the right payment on redirect. E.g. [https://yourshop.com/success.php?payment_id=–PAYMENT-ID–&call_id=–CALL-ID–](https://yourshop.com/success.php?payment_id=–PAYMENT-ID–&call_id=–CALL-ID–)(query string parameters way) <br/><br/> or <br/><br/> [https://yourshop.com/success.php/payment_id/–PAYMENT-ID–/call_id/–CALL-ID–](https://yourshop.com/success.php/payment_id/–PAYMENT-ID–/call_id/–CALL-ID–) <br/><br/> Please note, CALL_ID and PAYMENT_ID placeholders are not required to be used in the url, they are just a helpful information. For example, if you don’t have a way to identify your cart payment inside your system – use our CALL_ID. The PAYMENT_ID will return our payment transaction identifier, can be helpful to retrieve the full payment details via API later.
failure_url | false | string | URL to redirect customer on failed transaction. You can add –PAYMENT-ID– and –CALL-ID– as a placeholder to catch the right payment on redirect. E.g. [https://yourshop.com/failure.php?payment_id=–PAYMENT-ID–](https://yourshop.com/failure.php?payment_id=–PAYMENT-ID–)
cancel_url | false | string | URL to redirect customer on canceled transaction. You can add –CALL-ID– as a placeholder to catch the right payment on redirect. E.g. [https://yourshop.com/cancel.php?call_id=–CALL-ID–](https://yourshop.com/cancel.php?call_id=–CALL-ID–). <br/><br/>Please note, on cancel we don’t have a transaction (payment) created and payment_id parameter can be empty if requested
notice_url | false | string | URL for notifications about occured transactions
plugin_version | false | string | Just the statistical information about your code/plugin version

# Retrieve payment

> Request:

```shell
curl -k https://mein.payever.de/api/payment/{payment_id} 
-X GET 
-H "Authorization: Bearer {access_token}" 
```

> Response:

```json
{
"call":
  {
    "id": "d8a6079d7e9ca4d164dc0ceacc5ca050",
    "shop_id": "31012408fad6abc962c5af237e1bb930",
    "status": "success",
    "created_at": "2014-05-21T14:52:02+0200",
    "payment_id": 0
  },
"result":
  {
    "id": "d44c2eb96a608dd61ed9cf7bafa8bd24",
    "status": "STATUS_NEW",
    "merchant_name": "Martin Saigger",
    "customer_name": "John Doe",
    "payment_type": "santander_installment",
    "created_at": "2014-05-21T14:10:29+0200",
    "updated_at": "2014-05-21T14:10:29+0200",
    "reference": "900001291100",
    "amount": 1000,
    "fee": 99.9,
    "payment_details": {}
  }
}
```

**Method**: `GET`

**Entpoint**: `/api/payment/{id}`

The retrieve payment function retrieves all information for a specific payment. You can use this function to update the status of a payment within your cart. To use this function you have to generate an access token for the scope: API_PAYMENT_INFO.

### Attributes:

Attribute | required | type | Description
--------- | -------- | ---- | -----------
access_token | true | string | To retrieve a payment you have to generate an access token for the scope API_PAYMENT_INFO
payment_id | true | string | To retrieve a specific payment you have to provide its payment_id. You obtain the payment_id by creating our gateway or by retrieving a list of previous made payments.


# List payments

> Request:

```shell
curl -k https://mein.payever.de/api/payment 
-X GET 
-H "Authorization: Bearer {access_token}" 
-d limit=5 
```

> Response:

```json
 {
"call":
  {
    "id": "195b2548d02c32b3da928b60455e832f",
    "shop_id": "31012408fad6abc962c5af237e1bb930",
    "status": "success",
    "created_at": "2014-05-09T17:03:18+0200",
    "limit": 5
  },
"result":
  [
    {
      "id": 157,
      "status": "STATUS_ACCEPTED",
      "merchant_name": "Some Merchant",
      "customer_name": "John Doe",
      "payment_type": "invoice",
      "last_action": "INVOICE_TRANSFER",
      "created_at": "2014-05-09T10:11:48+0200",
      "updated_at": "2014-05-09T14:54:35+0200",
      "reference": "900001291100",
      "amount": 100
    },
    {}
  ]
}
```

**Method**: `GET`

**Entpoint**: `/api/payment`

The list payment function responds an array with a specific set of transactions made with your account. To use this function you have to generate an access token for the scope: API_PAYMENT_INFO.

### Attributes:

Attribute | required | type | Description
--------- | -------- | ---- | -----------
access_token | true | string | To retrieve a payment, you have to generate an access_token for the scope API_PAYMENT_INFO.
payment_method | false | string | Retrieve only transactions for the given payment_method. Available are cash, santander installment, invoice, paypal, stripe, direct debit and sofort.
date | false | DateTime | Retrieve only transactions for the given date.
currency | false | string | Retrieve only payments with the given currency.
state | false | string | Retrieve only payments with the given state. These are the ones available: `STATUS_NEW, STATUS_IN_PROCESS, STATUS_ACCEPTED, STATUS_FAILED, STATUS_DECLINED, STATUS_IN_COLLECTION and STATUS_LATEPAYMENT`
limit | false | string | Limit the output of retrieved transactions. By default the last 10 transactions matching the given values are responded.

# Refund payment

> Request:

```shell
 curl -k https://mein.payever.de/api/payment/refund/{payment_id} \
-X POST \
-H "Authorization: Bearer {access_token}" \
-d amount="50"
```

> Response:

```json
 {
"call":
  {
    "id": "456d307932af3a099ec625132ee3ae9c",
    "shop_id": "31012408fad6abc962c5af237e1bb930",
    "status": "success",
    "created_at": "2014-06-04T10:47:06+02:00",
    "payment_id": "48b89a6f9c6f48fc765830a69032682e",
    "amount": 50,
    "type": "refund"
  },
  "result":{}
}
```

**Method**: `POST`

**Entpoint**: `/api/payment/refund/{payment_id}`

The refund payment method refunds a payment completely or partially. This function is only available for invoice payments. To use this function you have to generate an access token for the scope: API_PAYMENT_ACTIONS.

### Attributes:

Attribute | required | type | Description
--------- | -------- | ---- | -----------
amount | false | float | Specify the refund amount. If no amount is set, the whole amount will be refunded. It depends on the payment method if you have to take further action like wire-transfer the amount to the customer for invoice payments.

# Authorize payment

> Request:

```shell
 curl -k https://mein.payever.de/api/payment/authorize/48b89a6f9c6f48fc765830a69032682e \
-X POST \
-H "Authorization: Bearer {access_token}" \
-d customer_id="328" \
-d invoice_id="1000098991" \
-d invoice_date="2014-05-21"
```

> Response:

```json
{
"call":
  {
    "id": "b58b1c28f14ddb625c0ffd2ceca75f6e",
    "shop_id": "31012408fad6abc962c5af237e1bb930",
    "status": "success",
    "created_at": "2014-06-04T11:37:02+02:00",
    "payment_id": "12926734411fa4a1b82b46e7194990f0",
    "customer_id": "328",
    "invoice_id": "100",
    "invoice_date": "2014-05-21T00:00:00+02:00",
    "type": "authorize"
  },
  "result": {}
}
```

**Method**: `POST`

**Entpoint**: `/api/payment/authorize/{payment_id}`

Authorize a previously made payment. This function is only available for invoice payments.
To use this function you have to generate an access token for the scope: API_PAYMENT_ACTIONS.

### Attributes:

Attribute | required | type | Description
--------- | -------- | ---- | -----------
customer_id | false | string | The customer ID from your System. If nothing is set we use our own customer ID.
invoice_id | false | string | The invoice ID from your System. If nothing is set we use the generated payment_id.
invoice_date | false | dateTime | The Date you generated and shipped the invoice. If nothing is set we use the current date.

# Remind payment

> Request:

```shell
curl -k https://mein.payever.de/api/payment/remind/12926734411fa4a1b82b46e7194990f0 \
-X POST \
-H "Authorization: Bearer {access_token}"
```

> Response:

```json
{
"call":
  {
    "id": "b58b1c28f14ddb625c0ffd2ceca75f6e",
    "shop_id": "31012408fad6abc962c5af237e1bb930",
    "status": "success",
    "created_at": "2014-06-04T11:37:02+02:00",
    "payment_id": "12926734411fa4a1b82b46e7194990f0",
    "type": "remind"
  },
  "result": {}
}
```

**Method**: `POST`

**Entpoint**: `/api/payment/remind/{payment_id}`

With this function you can remind outstanding payments. The customer will receive an email from payever with your payment information. This function is only allowed if the payment is outstanding for more than 14 days after the generated invoice date. This function is only available for invoice payments.
To use this function you have to generate an access token for the scope: API_PAYMENT_ACTIONS.

### Attributes:

<aside class="notice">
none: This function does not need any attributes.
</aside>

# Collect payments

> Request:

```shell
 curl -k https://mein.payever.de/api/payment/collect/12926734411fa4a1b82b46e7194990f0 \
-X POST \
-H "Authorization: Bearer {access_token}"
```

> Response:

```json
{
"call":
  {
    "id": "b58b1c28f14ddb625c0ffd2ceca75f6e",
    "shop_id": "31012408fad6abc962c5af237e1bb930",
    "status": "success",
    "created_at": "2014-06-04T11:37:02+02:00",
    "payment_id": "12926734411fa4a1b82b46e7194990f0",
    "type": "collect"
  },
  "result": {}
}
```

**Method**: `POST`

**Entpoint**: `/api/payment/collect/{payment_id}`

With this function you can collect outstanding payments. This function is only allowed if the payment is outstanding for more than 14 days after the date of the invoice and you already sent a payment reminder. This function is only available for invoice payments. <br/>To use this function you have to generate an access token for the scope: API_PAYMENT_ACTIONS.

### Attributes:

<aside class="notice">
none: This function does not need any attributes.
</aside>

# Late payments

> Request:

```shell
 curl -k https://mein.payever.de/api/late-payment/12926734411fa4a1b82b46e7194990f0 \
-X POST \
-H "Authorization: Bearer {access_token}"
```

> Response:

```json
{
"call":
  {
    "id": "b58b1c28f14ddb625c0ffd2ceca75f6e",
    "shop_id": "31012408fad6abc962c5af237e1bb930",
    "status": "success",
    "created_at": "2014-06-04T11:37:02+02:00",
    "payment_id": "12926734411fa4a1b82b46e7194990f0",
    "type": "late-payment"
  },
  "result": {}
}
```

**Method**: `POST`

**Entpoint**: `/api/payment/late-payment/{payment_id}`

With this function you can notify any received late payments for all transactions you collected before with the “collect payment” function. This function is only allowed if the payment was set to “in collection” before. This function is only available for invoice payments. <br/>To use this function you have to generate an access token for the scope: API_PAYMENT_ACTIONS.

### Attributes:

<aside class="notice">
none: This function does not need any attributes.
</aside>

# List Payment Options

> Request:

```shell
curl -X GET https://mein.payever.de/api/shop//payment-options
Where <b><slug></b> is a shop public identifier.
```

> Response:

```json
{
"call":
  {
    "id": "callid",
    "status": "success | failed",
    "created_at": "20001-01-26T18:00:00+00:00", 
    "shop_id": "shop-slug",
    "action": "list_payment_options",
    "channel": "shopware",
    "type": "shop_info_list_payment_options",
    "result":
    [{
      "id": "123221",
      "name": "Stripe",
      "status": "active",
      "variable_fee": 1.23,
      "fixed_fee": 2.34,
      "accept_fee": "boolean",
      "description_offer": "Lorem ipsum",
      "description_fee": "Lorem ipsum",
      "min": 0,
      "max": 100000,
      "payment_method": "stripe",
      "type": "payment",
      "slug": "stripe",
      "thumbnail1": "https://mein.payever.de/media/cache/payment_option.list_icon/207120b7bb406bc4024b68c3d0f7142a.png",
      "thumbnail2": "https://mein.payever.de/media/cache/payment_option.list_icon/207120b7bb406bc4024b68c3d0f7142a.png",
      "thumbnail3": "https://mein.payever.de/media/cache/payment_option.list_icon/207120b7bb406bc4024b68c3d0f7142a.png",
      "options":{
        "currencies": ["EUR","GBP","USD"],
        "countries": ["DE", "AT", "ES"], 
        "actions": ["refund", "authorize", "remind", "collect", "late_payment"] 
      },
      "translations": [ {"locale":"en", "field":"conditions", "content":"Lorem Ipsum"} ]
    }]
  }
```

**Method**: `POST`

**Entpoint**: `/api/shop/<slug>/payment-options`

With this function you can get payment options accessible in the shop. Since payment options are public information, there are no security headers required.

### Attributes:

<aside class="notice">
none: This function does not need any attributes.
</aside>

# Changelog

##Version 1.0:
Implementation of the following API functions:

* authenticate
* create payment
* retrieve payment
* list payments

##Version 1.1:
Implementation of the following API functions:

* refund
* authorize
* remind
* collect
* late-payment

Response value changed for create payment function:

{redirect_url} will now give the complete redirect_url. It is no longer necessary to add the host https://mein.payever.de in front of the received redirect_url.

##Version 1.2:
Fixes in payment options list:

* attribute fix_fee renamed to fixed_fee
* added accept_fee attribute

Fixes in create payment call:

* added new attribute plugin_version
