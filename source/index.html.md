---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:
  - <a href='https://dashboard.spotii.me/signup' target='_blank'>Sign Up for a Merchant account</a>

includes:
  # - errors

search: true
---

# Introduction

Welcome to the Spotii API! 
---
Merchants can use our API to access Spotii API endpoints, which can [checkout](#checkout), [capture](#capture) and [refund](#refund). 

Spotii offers integration with the following eCommerce platforms

* [Magento 2](#magento-2)

# Authentication

> To authorize, use this code to obtain a temporary bearer token:

```shell
curl POST https://auth.sandbox.spotii.me/api/v1.0/merchant/authentication/
-d '{
      "public_key": "<your-public-key>",
      "private_key": "<your-private-key>"
    }'
```

> Make sure to replace `<your-public-key>` and `<your-private-key>` with your API keys.

> The above command returns JSON structured like this:

```json
{
  "token": "<temporary-bearer-token>"
}
```

Spotii uses OAuth 2.0 Bearer Token Usage to allow access to the APIs.

Spotii expects the bearer token authentication to be included in all API requests to the server in a header that looks like the following:

`Authorization: Bearer <temporary-bearer-token>`

<aside class="notice">
You must replace <<code>temporary-bearer-token</code>> with the returned temporary bearer token.
</aside>

# Checkout

## Workflow

<a href='/images/checkout-api-flow.png' target='_blank'><img src="/images/checkout-api-flow.png" /></a>

## Create a Checkout

```shell
curl POST https://api.sandbox.spotii.me/api/v1.0/checkouts/
-H 'Authorization: Bearer <temporary-bearer-token>'
-H 'Content-Type: application/json'
-d '{
      "total": "100.00",
      "currency": "AED",
      "description": "5deb4f318bcf9-000540022",
      "reference": "5deb4f318bcf9-000540022",
      "display_reference": "000000022",
      "reject_callback_url": "http://merchant/index.php/spotiipay/standard/cancel/",
      "confirm_callback_url": "http://merchant/index.php/spotiipay/standard/complete/id/000000003/magento_spotii_id/5deb4f318bcf9-000540022/",
      "order": {}
    }'
```

This endpoint creates a merchant checkout. Upon a successful request, a checkout_url link will be provided for the merchant's customer to process payment at Spotii's payment portal.  If payment is successful, the customer will be redirected to the provided confirm_callback_url link. Else if payment is rejected, the customer will be redirected to the provided reject_callback_url link.

### HTTP Request

`POST https://api.sandbox.spotii.me/api/v1.0/checkouts/`

### Query Parameters

Parameter | Type | Description
--------- | ------- | -----------
total | Decimal | Total amount of the order, including tax, shipping and discount.
currency | Currency | [ISO 4217](https://en.wikipedia.org/wiki/ISO_4217) order currency. Currently supports "USD" for sandbox and "AED" for production.
description | String | Order description
reference | String | Merchant's unique reference ID
display_reference | String | Display of merchant's reference ID
reject_callback_url | String | Merchant URL for rejected order
confirm_callback_url | String | Merchant URL for confirmed order
order | [Order](#order) | Order information.

## ⚬ Order

```json
{
  "tax_amount": null,
  "shipping_amount": null,
  "discount": null,
  "customer": {},
  "billing_address": {},
  "shipping_address": {},
  "lines": []
}
```

Parameter | Type | Description
--------- | ------- | -----------
tax_amount | Decimal | Tax amount.
shipping_amount | Decimal | Shipping amount.
discount | Decimal | Discount amount.
customer | [Customer](#customer) | Customer information.
billing_address | [Billing](#billing) | Billing address.
shipping_address | [Shipping](#shipping) | Shipping address.
lines | [Line](#line) | A list of line objects.

## ▫️ Customer

```json
{
  "first_name": "Veronica",
  "last_name": "Costello",
  "email": "roni_cost@example.com",
  "phone": "(555) 229-3326"
}
```

Parameter | Type | Description
--------- | ------- | -----------
first_name | String | First name.
last_name | String | Last name.
email | String | A valid email address.
phone | String | Phone number.

## ▫️ Billing

```json
{
  "line1": "6146 Honey Bluff Parkway",
  "line2": "",
  "line3": "",
  "line4": "Calder",
  "state": "MI",
  "postcode": "49628-7978",
  "country": "US",
  "phone": "(555) 229-3326"
}
```

Parameter | Type | Description
--------- | ------- | -----------
line1 | String | Street address.
line2 | String | Apartment, suite, floor, etc.
line3 | String | Optional
line4 | String | City
state | String | State or Region or Province.
postcode | String | Postal Code.
country | Country | [ISO 3166 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2) country code.
phone | String | Phone number.

## ▫️ Shipping

```json
{
  "line1": "6146 Honey Bluff Parkway",
  "line2": "",
  "line3": "",
  "line4": "Calder",
  "state": "MI",
  "postcode": "49628-7978",
  "country": "US",
  "phone": "(555) 229-3326"
}
```

Parameter | Type | Description
--------- | ------- | -----------
line1 | String | Street address.
line2 | String | Apartment, suite, floor, etc.
line3 | String | Optional
line4 | String | City
state | String | State or Region or Province.
postcode | String | Postal Code.
country | Country | [ISO 3166 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2) country code.
phone | String | Phone number.

## ▫️ Line

```json
{
  "title": "Proteus Fitness Jackshirt",
  "sku": "MJ12-L-Orange",
  "upc": null,
  "quantity": "1.0",
  "price": "100.00",
  "currency": "AED",
  "notes": "Notes"
}
```

Parameter | Type | Description
--------- | ------- | -----------
title | String | Line title.
sku | String | Line SKU.
quantity | Integer | Line quantity.
price | String | Line price, excluding tax, shipping and discount. 
currency | Currency | [ISO 4217](https://en.wikipedia.org/wiki/ISO_4217) order currency. Currently supports "USD" for sandbox and "AED" for production.

# Capture

```shell
curl POST https://api.sandbox.spotii.me/api/v1.0/orders/<reference>/capture
-H 'Authorization: Bearer <temporary-bearer-token>'
-H 'Content-Type: application/json'
```

This endpoint captures an order that has been approved by Spotii's payment portal.

### HTTP Request

`POST https://api.sandbox.spotii.me/api/v1.0/orders/<reference>/capture`

<aside class="notice">
You must replace <<code>reference</code>> with the merchant's unique reference ID.
</aside>

# Refund

```shell
curl POST https://api.sandbox.spotii.me/api/v1.0/orders/<reference>/refund
-H 'Authorization: Bearer <temporary-bearer-token>'
-H 'Content-Type: application/json'
-d '{
      "total": "100.00",
      "currency": "AED"
    }'
```

This endpoint refunds an order.

### HTTP Request

`POST https://api.sandbox.spotii.me/api/v1.0/orders/<reference>/refund`

### Query Parameters

Parameter | Type | Description
--------- | ------- | -----------
total | Decimal | Total amount of the order, including tax, shipping and discount.
currency | Currency | [ISO 4217](https://en.wikipedia.org/wiki/ISO_4217) order currency.

<aside class="notice">
You must replace <<code>reference</code>> with the merchant's unique reference ID.
</aside>

# Errors

Spotii uses conventional HTTP response codes to indicate the success or failure of an API request.

Error Code | Meaning
---------- | -----------
200 | OK -- Success.
400 | Bad Request -- The request was invalid, please double check the required parameter(s).
401 | Unauthorized -- No valid Bearer Token provided.
402 | Request Failed -- The parameters were valid but the request failed.
404 | Not Found -- The requested resource could not be found.
500 | Internal Server Error -- We had a problem with our server. Try again later.

# Platforms

## Magento 2

This extension allows you to use Spotii as a payment gateway in your Magento 2 store.

### 1. Installation steps

#### Composer
1. `composer require spotii/spotiipay`
2. `php bin/magento setup:upgrade`
3. `php bin/magento setup:di:compile`
4. `php bin/magento setup:static-content:deploy`
5. `php bin/magento cache:clean`

#### Manual
1. Login to your Spotii merchant account. Sign up <a href='https://dashboard.spotii.me/signup' target='_blank'>here</a> if you don’t have an account.
2. Navigate to settings > integrations to reference your API Keys.
3. Open a command line interface.
4. In your Magento 2 `[ROOT]/app/code/` directory, create a new folder name `Spotii`.
5. Change directory to the `Spotii` folder.
6. Create a new folder name `Spotiipay`.
7. Change directory into the `Spotiipay` folder.
8. Extract the files from this <a href='https://github.com/spotii-me/magento2' target='_blank'>repository</a>.
9. Run the below command to enable Spotii:
`php bin/magento module:enable Spotii_Spotiipay`
10. Run the Magento setup upgrade:
`php bin/magento setup:upgrade`
11. Run the Magento Dependencies Injection Compile:
`php bin/magento setup:di:compile`
12. Run the Magento Static Content deployment:
`php bin/magento setup:static-content:deploy`
13. Login to your Magento Admin 
14. Navigate to System > Cache Management
15. Flush the cache storage by selecting Flush Cache Storage

### 2a. Admin Configuration
1. Login to your Magento Admin
2. Navigate to Store > Configuration > Sales > Payment Methonds > Spotii > Payment Settings
3. Click `Register for Spotii` or `I’ve already setup Spotii, I want to edit my settings`
4. 

### 2b. Payment Setup
1. Set the Payment Mode to `Live` for LIVE and set it as `Sandbox` for SANDBOX.
2. Set the Merchant ID, Public Key and Private Key. The information can be found from your <a href='https://dashboard.spotii.me/settings/integrations' target='_blank'>merchant dashboard</a>.
3. Set `Payment Action` as `Authorize only` for doing payment authorization only and `Authorize and Capture` for doing authorization as well as payment capture.
4. Set the Merchant Country as per the origin.
5. Enable the log tracker to trace the Spotii checkout process.
6. Save the configuration and clear the cache.

### 3a. Product Widget Setup
1. Navigate to `Stores/Configuration/Sales/Payment Methods/Spotii/Widget Settings/Product Page` in your Magento admin.
2. Provide the following information below to display the Spotii widget at the product page.
   - Price Block Selector : XPath of the price element.
   - Product page:render to element path : Location where to render the widget.
   - Show in all countries : Yes/No.
   - Alignment : Position of the widget.
   - Theme : Widget theme.
   - Width type : Text width of the widget.
   - Image url : If you want to have different logo, paste the url here.
   - Hide classes : Classes to be hidden when spotii widget is in place.
3. Save the configuration
4. Clear the cache.

### 3b. Cart Widget Setup
1. Navigate to `Stores/Configuration/Sales/Payment Methods/Spotii/Widget Settings/Cart Page` in your Magento admin.
2. Provide the following information below to display the Spotii widget at the product page.
   - Price Block Selector : XPath of the price element.
   - Cart page:render to element path : Location where to render the widget.
   - Show in all countries : Yes/No.
   - Alignment : Position of the widget.
   - Theme : Widget theme.
   - Width type : Text width of the widget.
   - Image url : If you want to have different logo, paste the url here.
   - Hide classes : Classes to be hidden when spotii widget is in place.
3. Save the configuration 
4. Clear the cache.
