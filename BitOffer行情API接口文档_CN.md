# BitOffer行情API接口文档
# 基本信息
* 本篇列出REST接口和websocket说明
* REST接口的baseurl: https://api.bitoffer.com 
* 所有接口的响应都是JSON格式
* 所有时间、时间戳均为UNIX时间，单位为微秒
* code `500001` 服务内部错误
* code `503001` 系统维护中，请稍后重试
* code `400001` 请求参数错误
* code `0003` 访问次数太频繁
* 成功响应格式如下:
```javascript
{
  "code": 0,
  "msg": "success"，
  "data": null
}
```
* 每个接口都有可能抛出异常，异常响应格式如下：
```javascript
{
  "code": 0003,
  "msg": "failed"
  "data": null
}
```
# 访问限制
* REST接口的访问限制：每分钟限制为120次，超过限制，返回code `0003` 访问次数太频繁
* websocket连接限制：每分钟连接次数为120次，超过限制，则不能继续连接
* 如超过限制，则封禁时间从5分钟到最长3天，甚至封禁IP
# REST接口
## 1.	获取交易对列表
```
GET /v1/api/cash/instrument
```
**响应:**
```javascript
	{
	 "code": 0,
	 "msg": "success",
	 "data": [
	         {
	             "instrumentId": 1,                     // 交易对ID
	             "symbol": "ETH-BTC",                   // 交易对名称
	             "priceTick": 0.000000010000000000,     // 最小报价
	             "lotSize": 0.000001000000000000,       // 最小交易量
	             "productId": 1,                        // 商品币种ID
	             "coinId": 2,                           // 货币币种ID
	             "status": 2,                           // 交易对状态：1未上市，2集合竞价，3连续交易，4交易暂停，5已摘牌
	             "fresh": 1                             // 0-旧交易对 1-新交易对
	         }
	     ]
	}
```

## 2.	获取币种信息列表
```
GET /v1/api/asset/coins
```
**响应:**
```javascript
{
	 "code": 0,
	 "msg": "success",
	 "data": [
	         {
	             "coinId": 1,                       // 币种ID
	             "nameCn": "ETH",                   // 币种中文名
	             "symbolDesc": "Ethereum ETH",      // 币种简介
	             "prefixUrl": "https://etherscan.io/tx/",       
	                                                // 币种浏览器地址
	             "displayPrecision": 8,             // 页面显示位数
                 "forbidWithdraw": 0,               // 是否允许提币：0允许、1禁止
                 "forbidRecharge": 0,               // 是否允许充值：0允许，1禁止
                 "enabled": 0,                      // 币状态：0启用，1不启用,2删除
                 "imgUrl": "https://bitoffer-image.oss-cn-hangzhou.aliyuncs.com/coins/ETH.png",     
                                                    // 图片路径
                 "minWithdrawQuantity": 0.05,       // 最小提现数
                 "blockConfirmationQuantity": 12,   // 区块确认数
                 "minRechargeQuantity": 0.000001    // 最小充值数
	         }
	     ]
	}

```

## 3.	获取所有交易对行情列表
```
GET /v1/api/quot/market
```
**响应:**
```javascript
{
    "code": 0,
    "msg": "success",
    "data": [
    	{
            "instrumentId": 1,                  //交易对
            "timestamp": 1561852794120315,      //时间戳	
            "last": 0.02672,                    //最新价	
            "vol": 0.657,                       //最新成交量
            "open": 0,                          //开盘价
            "high": 0.027102,                   //当日最高价
            "low": 0.025448,                    //当日最低价
            "turnover": 797.790941028,          //当日成交额
            "volume": 30598.164,                //当日成交量
            "changeRatio": 0.0429352068696331   //当日涨跌率
        }
    ]
}

```

## 4.	获取单个交易对快照
```
GET /v1/api/quot/snapshot
```

**参数:**

名称 | 类型 | 是否必须 | 描述
------------ | ------------ | ------------ | ------------
instrumentId | INT | YES |交易对ID
depth | INT | NO | 档位

**响应:**
```javascript
{
    "code": 0,
    "msg": "success",
    "data": {
        "bids": [
            [
              "4.00000000",     // 价位
              "431.00000000",   // 挂单量
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
**•	备注:** 

bids与asks结构：List<List>
如


```
[
	 [${价格}, ${数量}]
]
```
## 5.	获取单个交易对逐笔成交
```
GET  /v1/api/quot/tick
```

**参数:**

名称 | 类型 | 是否必须 | 描述
------------ | ------------ | ------------ | ------------
instrumentId | INT | YES |交易对ID
limit | INT | NO | 默认值20

**响应:**
```javascript
{
    "code": 0,
    "msg": "success",
    "data": {
        "ticks": [
            [
              1562156484484460,      // 时间
              293.51,                // 成交价
              7,                     // 成交量
              1                      // 买卖方向
            ]
          ]
    }
}

```
**•	备注:** 

ticks里面格式：
时间、成交价、成交量、买卖方向
如
```
[
	 [timestamp,price,quantity,takerSide]
]
```

## 6.	获取单个交易对K线
```
GET /v1/api/quot/candlestick
```

**参数:**

名称 | 类型 | 是否必须 | 描述
------------ | ------------ | ------------ | ------------
instrumentId | INT | YES |交易对ID
range | INT | YES | 取值范围:[60000,60000,300000,900000,1800000,3600000,86400000,604800000]


**响应:**
```javascript
{
    "code": 0,
    "msg": "success",
    "data": {
        "candles": []
    }
}

```
**备注:** 

candles: 时间、高、开、低、收、成交量

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

# Web Socket 行情接口
# 基本信息
* 本篇所列出的所有wss接口的baseurl为: **wss://socket.bitoffer.com/websocket**

**订阅主题和返回数据示例**
```
1.	订阅的方式：{"op":"subscribe","args":[]}
2.	args后面为对应的主题和需要的参数，格式为：{"topic":"","params":{}}
2.	
3.	//委托簿，depth表示档数，不传则默认30档
2.	{"op":"subscribe","args":[{"topic":"snapshot_book","params":{"instrumentId":1,"depth":6}}]}
3.	
4.	//市场行情
5.	{"op":"subscribe","args":[{"topic":"snapshot_market"}]}
6.	
7.	//逐笔成交
8.	{"op":"subscribe","args":[{"topic":"tick","params":{"instrumentId":1}}]}
9.	
10.	//k线
11.	{"op":"subscribe","args":[{"topic":"kline","params":{"instrumentId":1,"range":600000}}]}
12.	
13.	向推送系统发送订阅结果返回：
14.	{"op":"subscribe","result":{"code":,"msg":,"data":}}
15.	code为0表示订阅成功，否则为失败，data为失败的订阅主题列表
16.	
20.	推送数据：
21.	推送数据： {"topic":"","data":{}}
22.	topic表示订阅的主题，data为相对应的数据
```
## 1. 订阅所有交易对行情列表
**参数:**

名称 | 类型 | 是否必须 | 描述
------------ | ------------ | ------------ | ------------
topic | STRING | YES |主题名称 snapshot_market

**示例：**
```javascript
	//市场行情
	{"op":"subscribe","args":[{"topic":"snapshot_market"}]
	
	推送系统结果返回：
	{"op":"subscribe","result":{"code":,"msg":,"data":}]
	code为0表示订阅成功，否则为失败，data为失败的订阅主题列表
```
**响应:**
```javascript
{
    "topic": "snapshot_market",
    "data": [
        {
        "instrumentId": 1,                  //交易对
        "timestamp": 1561852794120,      //时间戳	
        "last": 0.02672,                    //最新价	
        "vol": 0.657,                       //成交量
        "open": 0,                          //开盘价
        "high": 0.027102,                   //当日最高价
        "low": 0.025448,                    //当日最低价
        "turnover": 797.790941028,          //当日成交额
        "volume": 30598.164,                //当日成交量
        "changeRatio": 0.0429352068696331   //当日涨跌率
        }
    ]
}

```

## 2. 订阅单个交易对快照
**参数:**

名称 | 类型 | 是否必须 | 描述
------------ | ------------ | ------------ | ------------
topic | STRING | YES |主题名称 snapshot_book
instrumentId | INT | YES |交易对ID
depth | INT | NO |档数
**示例：**
```javascript
	//委托簿，depth表示档数，不传则默认30档
	{"op":"subscribe","args":[{"topic":"snapshot_book","params":{"instrumentId":1,"depth":6}}]}
	
	推送服务返回：
	{"op":"subscribe","result":{"code":,"msg":,"data":}]
	code为0表示订阅成功，否则为失败，data为失败的订阅主题列表
```
**响应:**
```javascript
{
    "topic": "snapshot_book",
    "data": {
        "bids": [
            [
              "4.00000000",     // 价位
              "431.00000000",   // 挂单量
            ]
          ],
          "asks": [
            [
              "4.00000200",
              "12.00000000",
            ]
        "instrumentId": 1,                  //交易对
        "timestamp": 1561852794120,      //时间戳	
        "last": 0.02672,                    //最新价	
        "vol": 0.657,                       //最新成交量
        "open": 0,                          //开盘价
        "high": 0.027102,                   //当日最高价
        "low": 0.025448,                    //当日最低价
        "turnover": 797.790941028,          //当日成交额
        "volume": 30598.164,                //当日成交量
        "changeRatio": 0.0429352068696331   //当日涨跌率
    }
}
```

**备注:**
//委托簿，depth表示档数，不传则默认30档

```
{"op":"subscribe","args":[{"topic":"snapshot_book","params":{"instrumentId":1,"depth":6}}]}
```
推送服务返回：

```
{"op":"subscribe","result":{"code":,"msg":,"data":}]
```

code为0表示订阅成功，否则为失败，data为失败的订阅主题列表

## 3. 订阅单个交易对逐笔成交

**参数:**

名称 | 类型 | 是否必须 | 描述
------------ | ------------ | ------------ | ------------
topic | STRING | YES |主题名称 tick
instrumentId | INT | YES |交易对ID
**示例：**
```javascript
//逐笔成交
{"op":"subscribe","args":[{"topic":"tick","params":{"instrumentId":1}}]

推送服务返回：
{"op":"subscribe","result":{"code":,"msg":,"data":}]
code为0表示订阅成功，否则为失败，data为失败的订阅主题列表
```
**响应:**
```javascript
{
    "topic": "tick",
    "data": {
        "instrumentId": 1,                  //交易对
        "tick": []
    }
}
```

## ４. 订阅单个交易对K线

**参数:**

名称 | 类型 | 是否必须 | 描述
------------ | ------------ | ------------ | ------------
topic | STRING | YES |主题名称 kline
instrumentId | INT | YES |交易对ID
range | INT | YES | 取值范围:[60000,60000,300000,900000,1800000,3600000,86400000,604800000]
**示例：**
```javascript
//逐笔成交
{"op":"subscribe","args":[{"topic":"tick","params":{"instrumentId":1}}]

推送服务返回：
{"op":"subscribe","result":{"code":,"msg":,"data":}]
code为0表示订阅成功，否则为失败，data为失败的订阅主题列表
```
**响应:**
```javascript
{
    "topic": "kline",
    "data": {
        "candles": []，
        "instrumentId": 1,  //交易对
        "range": 60000      //取值范围
    }
}
```
**备注:** 

candles: 时间、高、开、低、收、成交量

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