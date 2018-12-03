# Orders

## Place a New Order

You can place two types of orders: limit and market.
Orders can only be placed if your account has sufficient funds.
Once an order is placed, your account funds will be put on hold
for the duration of the order. How much and which funds are put
on hold depends on the order type and parameters specified. 
See the Holds details below.

TYPE
When placing an order, you can specify the order type. The order type you specify will influence which other order parameters are required as well as how your order will be executed by the matching engine. If type is not specified, the order will default to a limit order.

### HTTP Request

`POST order/putLimit`

```json
{
 	"market_name" : "BCHBTC",
 	"side" : 2,
 	"amount" : "1",
 	"price" : "0.99",
 	"user_id" : 1
}
```
limit orders are both the default and basic order type. A limit order requires specifying a price and size. The size is the number of bitcoin to buy or sell, and the price is the price per bitcoin. The limit order will be filled at the price specified or better. A sell order can be filled at the specified price per bitcoin or a higher price per bitcoin and a buy order can be filled at the specified price or a lower price depending on market conditions. If market conditions cannot fill the limit order immediately, then the limit order will become part of the open order book until filled by another incoming order or canceled by the user.

### HTTP Request

`POST order/putMarket`

```json
{
	"market_name" : "BTCBCH",
	"side" : 2,
	"amount" : "0.001"
}
```
market orders differ from limit orders in that they provide no pricing guarantees. They however do provide a way to buy or sell specific amounts of bitcoin or fiat without having to specify the price. Market orders execute immediately and no part of the market order will go on the open order book. Market orders are always considered takers and incur taker fees. When placing a market order you can specify funds and/or size. Funds will limit how much of your quote currency account balance is used and size will limit the bitcoin amount transacted.


### ORDER PARAMETERS

Parameter |  Description
--------- |  -----------
market_name |  name of market "BTCBCH", "BTCETH", "BTCCNY"
side |  side of the market, has to be either 1 for MARKER_ORDER_SIDE_ASK (seller) 2 for MARKER_ORDER_SIDE_BID (buyer)
amount | amount no of units (quantity or volume) 
price |  price no of units (quantity or volume)
user_id |  id of user updating balance of

### RESPONSE PARAMETERS
```shell
response   
```
```json
{
    "error": null,
    "result": {
        "deal_fee": "0",
        "deal_stock": "0",
        "id": 108,
        "deal_money": "0",
        "market": "BCHBTC",
        "type": 1,
        "side": 2,
        "mtime": 1542914066.846037,
        "price": "0.99",
        "user": 1,
        "ctime": 1542914066.846037,
        "amount": "1",
        "taker_fee": "0.001",
        "maker_fee": "0.001",
        "left": "1"
    }
}
```
Parameter |  Description
--------- |  -----------
deal_fee | total fee of order is non-zero if deal completes
deal_stock | actual amount on which deal happened is non-zero if deal completes
id | unique identifier for order place
deal_money | actual price on which deal happened is non-zero if deal completes
market | exchange market e.g. BTCBCH, LTCBCH
type |  type: type of order has to be either - 1 for ORDER_LIMIT 2 for ORDER_MARKET
side | side of the market (type of order) side of the market, has to be either - 1 for MARKER_ORDER_SIDE_ASK (seller) 2 for MARKER_ORDER_SIDE_BID (buyer)               
mtime | order modified time   
price | price user had set for order 
user | user who performed order  
ctime | order creation time 
amount | net units user requested in order       
taker_fee | total fee deducted from taker (buyer) for deal it is equal to amount if order is not traded, and it is non-zero if order is partially traded, it is equal to deal_stock if order is fully traded
maker_fee | total fee deducted from maker (seller) for deal
left | total available units/amount/volume of asset available in order for trade

## Cancel an Order

cancel an order placed by specified user

### HTTP Request

`POST order/cancel`

```json
{
	"market_name" : "BTCBCH",
	"order_id" : 371
}
```
Parameter |  Description
--------- |  -----------
market_name | name of market "BTCBCH", "BTCETH", "BTCCNY"
order id | id of the order which needs to be cancelled

```shell
response   
```
```json
{
    "error": null,
    "result": {
        "deal_fee": "0",
        "deal_stock": "0",
        "id": 108,
        "deal_money": "0",
        "market": "BCHBTC",
        "type": 1,
        "side": 2,
        "mtime": 1542914066.846037,
        "price": "0.99",
        "user": 1,
        "ctime": 1542914066.846037,
        "amount": "1",
        "taker_fee": "0.001",
        "maker_fee": "0.001",
        "left": "1"
    }
}
```

Parameter |  Description
--------- |  -----------
deal_fee | total fee of order is non-zero if deal completes
deal_stock | actual amount on which deal happened is non-zero if deal completes
id | unique identifier for order place
deal_money | actual price on which deal happened is non-zero if deal completes
market | exchange market e.g. BTCBCH, LTCBCH
type |  type: type of order has to be either - 1 for ORDER_LIMIT 2 for ORDER_MARKET
side | side of the market (type of order) side of the market, has to be either - 1 for MARKER_ORDER_SIDE_ASK (seller) 2 for MARKER_ORDER_SIDE_BID (buyer)               
mtime | order modified time   
price | price user had set for order 
user | user who performed order  
ctime | order creation time 
amount | net units user requested in order       
taker_fee | total fee deducted from taker (buyer) for deal it is equal to amount if order is not traded, and it is non-zero if order is partially traded, it is equal to deal_stock if order is fully traded
maker_fee | total fee deducted from maker (seller) for deal
left | total available units/amount/volume of asset available in order for trade

### CANCEL REJECT

If the order could not be canceled (already filled or previously canceled, etc), then an error response will indicate the reason in the error field.

## Cancel all

With best effort, cancel all open orders. The response is a success message.

### HTTP Request

`POST order/cancelAll`

```shell
response   
```
```json
{"message":"All orders cancelled"}
```


