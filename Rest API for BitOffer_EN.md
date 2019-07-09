# Public Rest API for BitOffer
# General API Information
* The base endpoint and the illustrate are listed below.
* The base endpoint is: https://api.bitoffer.com 
* All endpoints is return either a JSON object or array.
* All time and timestamp related fields are in milliseconds.
* code `500001` Internal server error
* code `503001` System maintenance in progress, please wait...
* code `400001` Request parameter error
* code `0003` Request rate limit exceeded
* The correct payload is as follows:
```javascript
{
  "code": 0,
  "msg": "success"，
  "data": null
}
```
* Any endpoint can return an ERROR; the error payload is as follows：
```javascript
{
  "code": 0003,
  "msg": "failed"
  "data": null
}
```
# LIMITS
* The request rate limits of REST API：The limit is 120 times per minute, it will return co code'0003' Request rate limit exceeded when breaking a request rate limit.
* The connection limit of websocket：The limit is 120 times per minute，connection will be unavailable when breaking a connection limit.
* Breaking the limits will result in being banned from 5 minutes to 5 days, or even a IP ban.
# REST API Endpoints
## 1.	Get Trading Symbol
```
GET /v1/api/cash/instrument
```
**return:**
```javascript
	{
	 "code": 0,
	 "msg": "success",
	 "data": [
	         {
	             "instrumentId": 1,                     // Trading symbol ID
	             "symbol": "ETH-BTC",                   // Trading symbol name
	             "priceTick": 0.000000010000000000,     // The lowest quote
	             "lotSize": 0.000001000000000000,       // Minimum trading volume
	             "productId": 1,                        // Product ID
	             "coinId": 2,                           // Coin ID
	             "status": 2,                           // Trading symbol status:1.Unlisted 2.Call auction 3.Continuous trading 4.Suspended for trading 5.De-listed, unavailable for trading
	             "fresh": 1                             // 0-Old trading symbol 1-New trading symbol
	         }
	     ]
	}
```

## 2.	Get Token Information list
```
GET /v1/api/asset/coins
```
**return:**
```javascript
{
	 "code": 0,
	 "msg": "success",
	 "data": [
	         {
	             "coinId": 1,                       // Coin ID
	             "nameCn": "ETH",                   // Coin name in Chinese
	             "symbolDesc": "Ethereum ETH",      // Coin description
	             "prefixUrl": "https://etherscan.io/tx/",       
	                                                // Coin explore url
	             "displayPrecision": 8,             // page display digits
                 "forbidWithdraw": 0,               // Is withdrawal allowed?: 0 Yes 1 No
                 "forbidRecharge": 0,               // Is deposit allowed?: 0 Yes 1 No
                 "enabled": 0,                      // Coin Status: 0Enabled 1disabled 2deleted
                 "imgUrl": "https://bitoffer-image.oss-cn-hangzhou.aliyuncs.com/coins/ETH.png",     
                                                    // Image url
                 "minWithdrawQuantity": 0.05,       // Minimum withdrawal amount
                 "blockConfirmationQuantity": 12,   // Block Confirmation Quantity
                 "minRechargeQuantity": 0.000001    // Minimum deposit amount
	         }
	     ]
	}

```

## 3.	3.	Get Latest Tickers for All Pairs
```
GET /v1/api/quot/market
```
**return:**
```javascript
{
    "code": 0,
    "msg": "success",
    "data": [
    	{
            "instrumentId": 1,                  //Trading symbol
            "timestamp": 1561852794120315,      //Timestamp	
            "last": 0.02672,                    //The latest price	
            "vol": 0.657,                       //The latest trading volume
            "open": 0,                          //The opening price
            "high": 0.027102,                   //Day High
            "low": 0.025448,                    //Day low
            "turnover": 797.790941028,          //Daily Turnover
            "volume": 30598.164,                //Daily trading volume
            "changeRatio": 0.0429352068696331   //Daily change ratio
        }
    ]
}

```

## 4.	Get a single trading symbol snapshot
```
GET /v1/api/quot/snapshot
```

**Request parameter:**

name | data type | required | description
------------ | ------------ | ------------ | ------------
instrumentId | INT | YES |Trading symbol ID
depth | INT | NO | position

**return:**
```javascript
{
    "code": 0,
    "msg": "success",
    "data": {
        "bids": [
            [
              "4.00000000",     // price
              "431.00000000",   // pending order quantity
            ]
          ],
          "asks": [
            [
              "4.00000200",
              "12.00000000",
            ]
    }
}

```
**•	remark:** 

The structure of bids and asks：List<List>
如


```
[
	 [${price}, ${amount}]
]
```
## 5.	Get a single trading symbol tick
```
GET  /v1/api/quot/tick
```

**Request parameters:**

name | data type | required | description
------------ | ------------ | ------------ | ------------
instrumentId | INT | YES |Trading symbol ID
limit | INT | NO | Default:20

**return:**
```javascript
{
    "code": 0,
    "msg": "success",
    "data": {
        "ticks": [
            [
              1562156484484460,      // Time
              293.51,                // Transaction price
              7,                     // Trading volume
              1                      // Trading direction
            ]
          ]
    }
}

```
**•	remark:** 

ticks format：
Time,Transaction price, Trading volume, Trading direction
如
```
[
	 [timestamp,price,quantity,takerSide]
]
```

## 6.	Get a single trading symbol’s Klines(Candles)
```
GET /v1/api/quot/candlestick
```

**Request parameter:**

Name | Data type | Required | Description
------------ | ------------ | ------------ | ------------
instrumentId | INT | YES |Trading symbol ID
range | INT | YES | Value range:[60000,60000,300000,900000,1800000,3600000,86400000,604800000]


**return:**
```javascript
{
    "code": 0,
    "msg": "success",
    "data": {
        "candles": []
    }
}

```
**remark:** 

candles: Time、High、Open、Low、Close、Trading volume

```
range:
1Min  ： 60000
5Min  ： 300000
15Min ： 900000
30Min ： 1800000
1Hour ： 3600000
4Hour ： 14400000
1Day  ： 86400000
1Week ： 604800000
```

# Web Socket Streams for BitOffer
# General WSS information
* The base endpoint is: **wss://socket.bitoffer.com/websocket**

**Examples of subscribe theme and return data**
```
1.	Subscribe method：{"op":"subscribe","args":[]}
2.	The parameters after args are corresponding to specific themes and  requirements. The format is：{"topic":"","params":{}}
2.	
3.	//Order book，depth means position，default: 30
2.	{"op":"subscribe","args":[{"topic":"snapshot_book","params":{"instrumentId":1,"depth":6}}]}
3.	
4.	//Market snapshot
5.	{"op":"subscribe","args":[{"topic":"snapshot_market"}]}
6.	
7.	//ticks
8.	{"op":"subscribe","args":[{"topic":"tick","params":{"instrumentId":1}}]}
9.	
10.	//Kline
11.	{"op":"subscribe","args":[{"topic":"kline","params":{"instrumentId":1,"range":600000}}]}
12.	
13.	Send back the result to the pushing system：
14.	{"op":"subscribe","result":{"code":,"msg":,"data":}}
15.	When Code is shown as 0, it means subscription succeeded. Otherwise, subscription fails. Data is the theme list of failing subscription.
16.	
20.	Pushing data：
21.	pushing data： {"topic":"","data":{}}
22.	topic means the subscribe topic，data is the corresponding data.
```
## 1. Subscribe all trading symbol market snapshot list
**Request parameter:**

Name | Data Type | Required | Description
------------ | ------------ | ------------ | ------------
topic | STRING | YES |Theme name snapshot_market

**Example：**
```javascript
	//
	{"op":"subscribe","args":[{"topic":"snapshot_market"}]
	
	Result returns from pushing system：
	{"op":"subscribe","result":{"code":,"msg":,"data":}]
	When Code is shown as 0, it means subscription succeeded. Otherwise, subscription fails. Data is the theme list of failing subscription.
```
**return:**
```javascript
{
    "topic": "snapshot_market",
    "data": [
        {
        "instrumentId": 1,                  //Trading symbol
        "timestamp": 1561852794120,      //Timestamp	
        "last": 0.02672,                    //The latest price	
        "vol": 0.657,                       //Trading volume
        "open": 0,                          //The opening price
        "high": 0.027102,                   //Day High
        "low": 0.025448,                    //Day Low
        "turnover": 797.790941028,          //Daily Turnover
        "volume": 30598.164,                //Daily trading volume
        "changeRatio": 0.0429352068696331   //Daily change ratio
        }
    ]
}

```

## 2. Subscribe a single trading symbol snapshot
**Request parameter:**

Name | Data type | Required | Description
------------ | ------------ | ------------ | ------------
topic | STRING | YES |Theme name snapshot_book
instrumentId | INT | YES |Trading symbol ID
depth | INT | NO |Position
**Example：**
```javascript
	//Order book，depth means depth，default: 30
	{"op":"subscribe","args":[{"topic":"snapshot_book","params":{"instrumentId":1,"depth":6}}]}
	
	return from pushing system：
	{"op":"subscribe","result":{"code":,"msg":,"data":}]
	When Code is shown as 0, it means subscription succeeded. Otherwise, subscription fails. Data is the theme list of failing subscription.
```
**return:**
```javascript
{
    "topic": "snapshot_book",
    "data": {
        "bids": [
            [
              "4.00000000",     // price
              "431.00000000",   // Pending order quantity
            ]
          ],
          "asks": [
            [
              "4.00000200",
              "12.00000000",
            ]
        "instrumentId": 1,                  //Trading symbol ID
        "timestamp": 1561852794120,      //Timestamp	
        "last": 0.02672,                    //The latest price	
        "vol": 0.657,                       //The latest trading volume
        "open": 0,                          //The opening price
        "high": 0.027102,                   //Day High
        "low": 0.025448,                    //Day Low
        "turnover": 797.790941028,          //Daily Turnover
        "volume": 30598.164,                //Daily Trading Volume
        "changeRatio": 0.0429352068696331   //Daily Change Ratio
    }
}
```

**Remark:**
//Order book，depth means depth，default: 30

```
{"op":"subscribe","args":[{"topic":"snapshot_book","params":{"instrumentId":1,"depth":6}}]}
```
Return from pushing system：

```
{"op":"subscribe","result":{"code":,"msg":,"data":}]
```

When Code is shown as 0, it means subscription succeeded. Otherwise, subscription fails. Data is the theme list of failing subscription.

## 3. Subscribe a single trading symbol tick

**Request parameter:**

Name | Data Type | Required | Description
------------ | ------------ | ------------ | ------------
topic | STRING | YES |Theme name tick
instrumentId | INT | YES |Trading symbol ID
**Example：**
```javascript
//Ticks
{"op":"subscribe","args":[{"topic":"tick","params":{"instrumentId":1}}]

return from pushing system：
{"op":"subscribe","result":{"code":,"msg":,"data":}]
When Code is shown as 0, it means subscription succeeded. Otherwise, subscription fails. Data is the theme list of failing subscription.
```
**return:**
```javascript
{
    "topic": "tick",
    "data": {
        "instrumentId": 1,                  //Trading symbol
        "tick": []
    }
}
```

## ４. Subscribe a single trading symbol Kline

**Request parameter:**

Name | Data type | Required | Description
------------ | ------------ | ------------ | ------------
topic | STRING | YES |Theme name kline
instrumentId | INT | YES |Trading symbol ID
range | INT | YES | Value range:[60000,60000,300000,900000,1800000,3600000,86400000,604800000]
**Example：**
```javascript
//Ticks
{"op":"subscribe","args":[{"topic":"tick","params":{"instrumentId":1}}]
return from pushing system：
{"op":"subscribe","result":{"code":,"msg":,"data":}]
When Code is shown as 0, it means subscription succeeded. Otherwise, subscription fails. Data is the theme list of failing subscription.
```
**return:**
```javascript
{
    "topic": "kline",
    "data": {
        "candles": []，
        "instrumentId": 1,  //trading symbol
        "range": 60000      //Value range
    }
}
```
**备注:** 

candles: Time、High、Open、Low、Close、Trading Volume

```
range:
1Min  ： 60000
5Min  ： 300000
15Min ： 900000
30Min ： 1800000
1Hour ： 3600000
4Hour ： 14400000
1Day  ： 86400000
1Week ： 604800000


```