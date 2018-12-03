---
title: CryptoKart API 

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>


search: true
---

Introduction
============

Welcome to Cryptokart trader and developer documentation. These documents outline exchange functionality, market details, and APIs.

General Background
==================

##Order Matching System

Cryptokart operates a continuous first-come, first-serve order book. Orders are executed in price-time priority as received by the matching engine.

##Self Trade Prevention

Self-trading is not allowed, meaning two orders from the same user will not fill one another. How we prevent this is by not letting the user place an order that would match against one of their existing orders.

##Price Improvement

Orders are matched at the price of the maker order (existing order in order book), not at that of the taker order.

##Order Lifecycle

Valid orders sent to the matching engine are confirmed immediately. If an order executes against another order immediately, the order is considered finished. An order can execute in part or whole. Any part of the order not filled immediately, will be considered open and stay in the order book. Orders will stay in the open state until canceled or subsequently filled by new orders. Currently the only supported time in force policy is GTC (Good Till Canceled).

API Details
===========

API URLs
--------
| Environment | REST |
| --- | --- | --- |
| PROD | ? | Coming Soon |
| TEST | https://test.cryptokart.io | Coming Soon |

Requests
--------

All requests and responses are application/json content type and follow typical HTTP response status codes for success and failure.

Date & Time Format
------------------

All timestamps are returned in UNIX epoch time.

Number Format
-------------

All financial data, i.e. price, amount, fees, total, etc., are positive integers.

Note: we should mention precision of each field (especially tick size)

Pagination
----------

Parameters:

| Parameter | Description |
| --- | --- | --- |
| offset | Number of results offset. Default 0 |
| limit | Number of results per call. Accepted values: 0 - 1000. Default 10 |
| last_id | ? (see market deals API) |

Rate Limits
-----------

When a rate limit is exceeded, a status of 429 Too Many Requests will be returned.

Market data: 5 requests/second per IP

Trading: 5 request/second per user

Significant excess of the Rate Limits can lead to account suspension.

Authentication
--------------

TBD

ENUM definitions (keep here or have separate?) / terminology
------------------------------------------------------------

Add details at end once clear on all variables.

Market Data
===========

Kline Data
----------

### HTTP Request
```json
{
"market_name" : "BTCUSDT",
"start_time" : 1,
"end_time" : 1738485554,
"interval" : 3600
}
```
`GET {{domain}}/matchengine/market/kline`

Returns Kline/candlestick data for a market

| Name | Type | Mandatory | Description |
| --- | --- | --- | --- |
| market\_name | String | Yes | Ex: BTCUSDT |
| start\_time | Integer | Yes | Should be greater than 0 |
| end\_time | Integer | Yes | Should be greater than start time |
| interval | Integer | Yes | Valid inputs: [1, 60, 3600, 86400, multiple of 86400] |


### RESPONSE PARAMETERS

  
```json
{
   "error": null,
   "result": [
       [
           1542801600,  //Time
           "30",        //Open
           "20",        //Close
           "30",        //High
           "20",        //Low
           "0.3",        //Volume
           "8",         //Amount
           "BTCUSDT" //Market
       ]
   ]
}
```

Market Status
-------------

### HTTP Request

```json
{
"market_name" : "BTCUSDT"
}
```
`GET {{domain}}/matchengine/market/status`

? (not getting response)

Parameters:

| Name | Type | Mandatory | Description |
| --- | --- | --- | --- |
| market\_name | String | Yes |   |

### RESPONSE PARAMETERS

> Response

```json
{
"add response body": "pending"
}

```

Market Summary
--------------

### HTTP Request
```json
{
"marketName" : "BTCUSDT"
}
```
`GET {{domain}}/matchengine/market/summary`

Gives number and volume of orders in order book, to give idea on market depth

Parameters:

| Name | Type | Mandatory | Description |
| --- | --- | --- | --- |
| marketName | String | Yes | &quot;&quot;: Returns for all markets&quot;BTCUSDT&quot;: Returns for the specified market |

> Response

### RESPONSE PARAMETERS

```json
{
   "error": null,
   "result": [
       {
           "name": "BTCUSDT",
           "ask_amount": "300.4",  // Total volume of orders on ask (i.e. sell) side
           "ask_count": 231,  // Number of orders on ask (i.e. sell) side
           "bid_count": 197,  // Total volume of orders on bid (i.e. buy) side
           "bid_amount": "278.3"  // Number of orders on bid (i.e. buy) side
       }
   ]

}
```

Order Book
----------

### HTTP Request
```json
{
"market_name" : "LTCUSDT",
   "side" : 1,
   "offset" : 0,
   "limit" : 10
}
```
`GET {{domain}}/matchengine/order/book`

Return all open buy or sell orders in order book of a specified market

Parameters:

| Name | Type | Mandatory | Description |
| --- | --- | --- | --- |
| marketName | String | Yes | &quot;&quot;: Returns for all markets&quot;BTCUSDT&quot;: Returns for the specified market |


### RESPONSE PARAMETERS

> Response

```json
{
   "error": null,
   "result": {
       "offset": 0,
       "orders": [
           {
               "id": 97,  //Order ID
               "market": "LTCUSDT",
               "type": 1,  //Order Type. 1 = Limit Order, 2 = Market Order
               "side": 1,  //1 = Sell, 2 = Buy
               "price": "50",
               "amount": "1",  //Order amount
               "left": "1"  //Order amount that is unfilled
           }
       ],
       "total": 11,  //Number of orders
       "limit": 10
   }
}
```
Market Deals
------------

### HTTP Request
```json
{
"market_name" : "BTCUSDT",
"limit" : 100,
"last_id" : 0
}
```
`GET {{domain}}/matchengine/market/deals`

Returns trade history, as a list of the completed orders

Parameters:

| Name | Type | Mandatory | Description |
| --- | --- | --- | --- |
| market\_name | String | Yes |   |
| limit | Integer | Yes | ? |
| last\_id | Integer | Yes | ? |

### RESPONSE PARAMETERS

> Response

```json
{
   "error": null,
   "result": [
       {
           "id": 128,  //Order ID
           "price": "22",
           "time": 1543062276.540955,
           "amount": "0.3",  //Order amount
           "type": "buy"  //Side that was taker
       }
   ]

}
```

Trading  
=========

Place Limit Order
-----------------

### HTTP Request
```json
{
"market_name" : "BTCUSDT",
"side" : 2,
"amount" : "0.2",
"price" : "4000"
}
```
`GET {{domain}}/matchengine/order/putLimit`

Place a limit order

| Name | Type | Mandatory | Description |
| --- | --- | --- | --- |
| market\_name | String | Yes |   |
| side | Integer | Yes | 1 = sell 2 = buy |
| amount | String | Yes | Amount within quotes (ex: &quot;0.2&quot;) |
| price | String | Yes | Price within quotes (ex: &quot;4000&quot;) |


### RESPONSE PARAMETERS

> Response

```json
{
   "error": null,
   "result": {
       "deal_fee": "0",  //Fee paid by user
       "deal_stock": "0",  //Actual amount on which trade happen
       "id": 285,  //Order ID
       "deal_money": "0",  //Actual total (= price*amount) on which deal happened
       "market": "LTCUSDT",
       "type": 1,  //Order type. 1 = Limit, 2 = Market
       "side": 2,  //Side. 1 = Sell, 2 = Buy
       "mtime": 1543228938.770122,  //Order modified time
       "price": "44",  //Price set by user
       "user": 28,  //User ID who placed order
       "ctime": 1543228938.770122,  //Order created time
       "amount": "0.2",  //Order amount
       "taker_fee": "0.001",  //Taker fee rate
       "maker_fee": "0.001",  //Maker fee rate
       "left": "0.2"  //Order amount that is still open
   }
}
```

Place Market Order
------------------

### HTTP Request
```json
{
"market_name" : "LTCUSDT",
"side" : 2,
"amount" : "0.3"
}
```
`GET {{domain}}/matchengine/order/putMarket`

Place a market order

| Name | Type | Mandatory | Description |
| --- | --- | --- | --- |
| market\_name | String | Yes |   |
| side | Integer | Yes | 1 = sell 2 = buy |
| amount | String | Yes | Amount within quotes (ex: &quot;0.3&quot;) |

### RESPONSE PARAMETERS

> Response

```json
{
   "error": null,
   "result": {
       "deal_fee": "0.0003",  //Fee paid by user
       "deal_stock": "0.3",  //Actual amount on which trade happen
       "id": 287,  //Order ID
       "deal_money": "15",  //Actual total (=price*amount) on which trade happened
       "market": "LTCUSDT",
       "type": 2,  //Order type. 1 = Limit, 2 = Market
       "side": 2,  //Side. 1 = Sell, 2 = Buy
       "mtime": 1543230086.512801,  //Order modified time
       "price": "0",  //Price set by user (0 since its Market order)
       "user": 28,  //User ID who placed order
       "ctime": 1543230086.512716,  //Order created time
       "amount": "0.3",  //Order amount
       "taker_fee": "0.001",  //Taker fee rate
       "maker_fee": "0",  //Maker fee rate
       "left": "0e-8"  //Order amount that is still open
   }
}
```

Cancel Order
------------

### HTTP Request
```json
{
"market_name" : "LTCUSDT",
"order_id" : 285
}
```
`GET {{domain}}/order/cancel`

Cancel an order

| Name | Type | Mandatory | Description |
| --- | --- | --- | --- |
| market\_name | String | Yes |   |
| order\_id | Integer | Yes | Order ID of order you wish to cancel |


### RESPONSE PARAMETERS

> Response

```json
{
   "error": null,
   "result": {  //Details of the canceled order
       "deal_fee": "0",
       "deal_stock": "0",
       "id": 285,
       "deal_money": "0",
       "market": "LTCUSDT",
       "type": 1,
       "side": 2,
       "mtime": 1543228938.770122,
       "price": "44",
       "user": 28,
       "ctime": 1543228938.770122,
       "amount": "0.2",
       "taker_fee": "0.001",
       "maker_fee": "0.001",
       "left": "0.2"
   }
}
```

Cancel All Orders
-----------------

### HTTP Request

```json
{}  //Empty JSON
```
`GET {{domain}}/order/cancelAll`

Cancel all open orders

Parameters:

None


### RESPONSE PARAMETERS

> Response

```json
{"message":"All orders cancelled"}
```

Pending Orders
--------------

### HTTP Request

```json
{
   "market_name" : "LTCUSDT",
   "offset" : 0,
   "limit" : 10
}
```

`GET {{domain}}/matchengine/order/pending`

List all your open orders, including partially filled

Parameters:

| Name | Type | Mandatory | Description |
| --- | --- | --- | --- |
| market\_name | String | Yes |   |
| offset | Integer | Yes | ? |
| limit | Integer | Yes | ? |

### RESPONSE PARAMETERS

> Response

```json
{
   "error": null,
   "result": {
       "total": 1,  //Count of open orders
       "limit": 10,
       "offset": 0,
       "records": [
           {
               "deal_fee": "0",  //Fee paid by user (before discount)
               "deal_stock": "0",  //Actual amount on which trade happen
               "id": 284,  //Order ID
               "deal_money": "0",  //Actual total (=price*amount) on which trade happened
               "market": "LTCUSDT",
               "type": 1,  //Order type. 1 = Limit, 2 = Market
               "side": 2,  //Side. 1 = Sell, 2 = Buy
               "mtime": 1543228936.976983,  //Order modified time
               "price": "44",  //Price set by user (0 since its Market order)
               "user": 28,  //User ID who placed order
               "ctime": 1543228936.976983,  //Order created time
               "amount": "0.2",  //Order amount
               "taker_fee": "0.001",  //Taker fee rate
               "maker_fee": "0.001",  //Maker fee rate
               "left": "0.2"  //Order amount that is still open
           }
   ]
} 
}
```

Pending Order Details
---------------------

### HTTP Request

```json
{
"market_name" : "LTCUSDT",
   "order_id": 284
}
```
`GET {{domain}}/matchengine/order/pendingDetail`

Get details of a single open order by Order ID

Parameters:

| Name | Type | Mandatory | Description |
| --- | --- | --- | --- |
| market\_name | String | Yes |   |
| order\_id | Integer | Yes | Order ID |


### RESPONSE PARAMETERS

> Response

```json
{
   "error": null,
   "result": {
               "deal_fee": "0",  //Fee paid by user (before discount)
               "deal_stock": "0",  //Actual amount on which trade happen
               "id": 284,  //Order ID
               "deal_money": "0",  //Actual total (=price*amount) on which trade happened
               "market": "LTCUSDT",
               "type": 1,  //Order type. 1 = Limit, 2 = Market
               "side": 2,  //Side. 1 = Sell, 2 = Buy
               "mtime": 1543228936.976983,  //Order modified time
               "price": "44",  //Price set by user (0 since its Market order)
               "user": 28,  //User ID who placed order
               "ctime": 1543228936.976983,  //Order created time
               "amount": "0.2",  //Order amount
               "taker_fee": "0.001",  //Taker fee rate
               "maker_fee": "0.001",  //Maker fee rate
               "left": "0.2"  //Order amount that is still open
     }
}
```

Trade History
=============

Finished Orders
---------------

### HTTP Request

```json
{
    "market_name" : "LTCUSDT",
    "start_time" : 0,
    "end_time" : 1539158000987,
    "offset" : 0,
    "limit" : 10,
    "market_side": 2
}
```
`GET {{domain}}/matchengine/order/finished`

List all finished orders. Finished means no portion of that order is currently open.

Parameters:

| Name | Type | Mandatory | Description |
| --- | --- | --- | --- |
| market\_name | String | Yes |   |
| start\_time | Integer | Yes |   |
| end\_time | Integer | Yes |   |
| offset | Integer | Yes |   |
| limit | Integer | Yes |   |
| market\_side | Integer | Yes |   |

### RESPONSE PARAMETERS

> Response

```json
{
   "error": null,
   "result": {
       "offset": 0,
       "limit": 10,
       "records": [
           {
               "id": 287,  //Order ID
               "deal_fee": "0.0003",  //Fee paid by user (before discount)
               "ctime": 1543230086.512716,  //Trade created time
               "ftime": 1543230086.512801,  //Trade finished time
               "type": 2,  //Order type. 1 = Limit, 2 = Market
               "side": 2,  //Side. 1 = Sell, 2 = Buy
               "user": 28,  //User ID who placed order
               "market": "LTCUSDT",
               "price": "0",  //Price set by user (0 if Market order)
               "amount": "0.3",  //Amount set by user
               "taker_fee": "0.001",  //Taker fee rate
               "maker_fee": "0",  //Maker fee rate (shows 0 if user was Taker)
               "deal_stock": "0.3",  //Actual amount for which trade happen
               "deal_money": "15"  //Actual total (=price*amount) on which trade happened
           }
       ]
   }
}
```

Finished Order Details
----------------------

### HTTP Request
```json
{
"market_name" : "LTCUSDT",
   "offset" : 0,
   "limit" : 10,
   "order_id" : 287
}
```

`GET {{domain}}/matchengine/order/finishedDetail`

Get details of a finished order

Parameters:

| Name | Type | Mandatory | Description |
| --- | --- | --- | --- |
| market\_name | String | Yes |   |
| offset | Integer | Yes |   |
| limit | Integer | Yes |   |
| order\_id | Integer | Yes |   |

### RESPONSE PARAMETERS

> Response

```json
{
   "error": null,
   "result": {
               "id": 287,  //Order ID
               "deal_fee": "0.0003",  //Fee paid by user (before discount)
               "ctime": 1543230086.512716,  //Trade created time
               "ftime": 1543230086.512801,  //Trade finished time
               "type": 2,  //Order type. 1 = Limit, 2 = Market
               "side": 2,  //Side. 1 = Sell, 2 = Buy
               "user": 28,  //User ID who placed order
               "market": "LTCUSDT",
               "price": "0",  //Price set by user (0 if Market order)
               "amount": "0.3",  //Amount set by user
               "taker_fee": "0.001",  //Taker fee rate
               "maker_fee": "0",  //Maker fee rate (shows 0 if user was Taker)
               "deal_stock": "0.3",  //Actual amount for which trade happen
               "deal_money": "15"  //Actual total (=price*amount) on which trade happened
   }
}
```

Order Deals
-----------

### HTTP Request

```json
{
"order_id" : 283,
"offset": 0,
"limit" : 1
}
```
`GET {{domain}}/matchengine/order/deals`

List all Trades that correspond to a specified Order. Remember an order can be filled in partial amounts.

Parameters:

| Name | Type | Mandatory | Description |
| --- | --- | --- | --- |
| order\_id | Integer | Yes | Order ID |
| offset | Integer | Yes | ? |
| limit | Integer | Yes | ? |


### RESPONSE PARAMETERS

> Response

```json
{
   "error": null,
   "result": {
       "offset": 0,
       "limit": 1,
       "records": [
           {
               "time": 1543233413.923899,  //Time at which trade got executed
               "fee": "0.0002",  //Fee deducted from trade (before discount)
               "role": 1,  //Role of user in this trade. 1 = Sell, 2 = Buyer
               "amount": "0.2",  //Trade amount
               "user": 28,  //User ID
               "id": 140,  //Trade ID
               "deal": "8.8",  //Total (=price*amount) of trade
               "deal_order_id": 292,  //ID of respective order, with which trade took place
               "price": "44"  //Price of trade
           }
       ],
       "feeDetails": {
           "createdAt": "2018-11-26T11:56:54.000Z",  //Created time
           "updatedAt": "2018-11-26T11:56:54.000Z",  //Updated time
           "id": 191,  //?
           "userId": 28,  //User ID
           "dealId": 283,  //?
           "market": "LTCUSDT",
           "currency": "LTC",  //Currency in which fee is charged. If buy order then in base currency, if sell order, then in quote currency
           "discountedFee": "0.000200000",  //Fee after applying discount
           "discountPercent": "0"  //Discount percentage this fee is applicable for
       }
   }
}
```

Funds management
================

Funds Balance
-------------

Expose API?

Withdrawal
----------

Expose API?

Error
=====

(remarks: HitBTC shows it in easy to follow UI. Binance is the most thorough covers the most types of errors, and splits it nicely [10xx, 11xx, 9xxx]

| HTTP Status Code| Message | Description |
| --- | --- | --- | --- |
|   |   |   |   |
|   |   |   |   |
|   |   |   |   |
|   |   |   |   |
|   |   |   |   |
|   |   |   |   |
|   |   |   |   |
|   |   |   |   |
|   |   |   |   |
|   |   |   |   |
