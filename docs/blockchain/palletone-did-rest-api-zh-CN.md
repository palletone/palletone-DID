# PalletOne去中心化身份管理RPC接口

返回值说明：

| 返回值 | 描述                   |
| ------ | ---------------------- |
| 200    | 成功                   |
| 401    | 未经身份验证或未经授权 |
| 400    | 错误的客户请求         |
| 404    | 资源不存在             |
| 500    | 服务器错误             |

 

下面的区块链信息都来自PalletOne。

## 1.1    获取区块链最新高度

### 1.1.1    请求数据

#### 1.1.1.1   请求路径

```http
GET /ledger/height
```

#### 1.1.1.2   请求头

无。

#### 1.1.1.3   请求Body

无。

### 1.1.2    响应数据

#### 1.1.2.1   响应头

| 字段名            | 字段值              |
| ----------------- | ------------------- |
| ` Content-Type  ` | `application/json ` |

#### 1.1.2.2   响应Body

各个字段说明：

```json
{
  "height": "The logical blockchain height.",
  "hash": "The hash associated with the blockchain height."
}
```

示例：

```json
{
  "height": 3660513,
  "hash": "3143056076a54565dfe32fd73d1ca71039350ce595c3108693f1e7289ecda0e2"
}
```

 

## 1.2    根据区块Hash查询区块高度

### 1.2.1    请求数据

#### 1.2.1.1   请求路径

```http
GET /ledger/height/<unit-hash>
```

示例：

```http
Get /ledger/height/3143056076a54565dfe32fd73d1ca71039350ce595c3108693f1e7289ecda0e2
```

 

#### 1.2.1.2   请求头

`无。`

#### 1.2.1.3   请求Body

`无。`

### 1.2.2    响应数据

#### 1.2.2.1   响应头

| 字段名         | 字段值              |
| -------------- | ------------------- |
| `Content-Type` | `application/json ` |

#### 1.2.2.2   响应Body

字段说明：

```json
{
  "height": "The logical blockchain height.",
  "hash": "The hash associated with the blockchain height, must be the height as the value given in query path."
}
```

示例：

```json
{
  "height": 3660513,
  "hash": "3143056076a54565dfe32fd73d1ca71039350ce595c3108693f1e7289ecda0e2"
}
```

 

## 1.3    获取Sidetree交易信息列表

功能：根据区块链高度顺序返回Sidetree交易列表信息，返回列表的限制是1024个。需要获取所有的交易列表信息，则需要多次请求。

### 1.3.1    请求数据

#### 1.3.1.1   请求路径

```http
GET /ledger/transactions?since-transaction-hash=<transaction-hash>&transaction-hash=<transaction-hash>
```

路径参数说明：

| 参数字段                 | 参数值说明                                                   |
| ------------------------ | ------------------------------------------------------------ |
| `since-transaction-hash` | 为空：表示从第一个交易开始  非空：则表示从返回某个开始的交易（包含该交易） |
| `transaction-hash`       | 为空：表示返回到最新区块的所有Sidetree交易  非空：返回到该交易停止的交易信息（包含该交易），可以跨区块高度 |

示例：

```json
GET /leger/transactions?since-transaction-hash=0000000000000000002352597f8ec45c56ad19994808e982f5868c5ff6cfef2e&transaction-time-hash=00000000000000000000100158f474719e5a319933856f7f464fcc65a3cb2253
```

 

#### 1.3.1.2   请求头

`无。`

#### 1.3.1.3   请求Body

`无。`

### 1.3.2    响应数据

#### 1.3.2.1   响应头

| 字段名            | 字段值                |
| ----------------- | --------------------- |
| ` Content-Type  ` | ` application/json  ` |

#### 1.3.2.2   响应Body

Body各字段说明

```json
{
  "moreTransactions": "True if there are more transactions beyond the returned batch. False otherwise.",
  "transactions": [
    {
      "transactionNumber": "A monotonically increasing number (need NOT be by 1) that identifies a Sidtree transaction.",
      "transactionHeight": "The logical blockchain height this transaction is anchored. Used for protocol version selection.",
      "transactionHash": "The hash associated with the transaction height.",
      "anchorString": "The string written to the blockchain for this transaction.",
      "transactionFeePaid": "A number representing the fee paid for this transaction.",
      "normalizedTransactionFee": "A number representing the normalized transaction fee used for proof-of-fee calculation."
    },
    ...
  ]
}
```

示例：

```json
HTTP/1.1 200 OK
 
{
  "moreTransactions": false,
  "transactions": [
    {
      "transactionNumber": 89,
      "transactionHeight": 545236,
      "transactionHash": "0000000000000000002352597f8ec45c56ad19994808e982f5868c5ff6cfef2e",
      "anchorString": "QmWd5PH6vyRH5kMdzZRPBnf952dbR4av3Bd7B2wBqMaAcf",
      "transactionFeePaid": 40000,
      "normalizedTransactionFee": 100
    },
    {
      "transactionNumber": 100,
      "transactionHeight": 545236,
      "transactionTHash": "00000000000000000000100158f474719e5a319933856f7f464fcc65a3cb2253",
      "anchorString": "QmbJGU4wNti6vNMGMosXaHbeMHGu9PkAUZtVBb2s2Vyq5d",
      "transactionFeePaid": 600000,
      "normalizedTransactionFee": 400
    }
  ]
}
```

 

## 1.4    获取交易列表中第一个有效的交易

### 1.4.1    请求数据

#### 1.4.1.1   请求路径

```http
POST /ledger/transactions/firstValid HTTP/1.1
```

#### 1.4.1.2   请求头

| 字段名         | 字段值                |
| -------------- | --------------------- |
| `Content-Type` | ` application/json  ` |

#### 1.4.1.3   请求Body

Body各个字段说明：

```json
{
  "transactions": [
    {
      "transactionNumber": "The transaction to be validated.",
      "transactionHeight": "The logical blockchain height this transaction is anchored. Used for protocol version selection.",
      "transactionHash": "The hash associated with the transaction height.",
      "anchorString": "The string written to the blockchain for this transaction.",
      "transactionFeePaid": "A number representing the fee paid for this transaction.",
      "normalizedTransactionFee": "A number representing the normalized transaction fee used for proof-of-fee calculation."
    },
    ...
  ]
}
```

示例：

```json
POST /ledger/transactions/firstValid HTTP/1.1

Content-Type: application/json
 
{
  "transactions": [
    {
      "transactionNumber": 19,
      "transactionHeight": 545236,
      "transactionHash": "0000000000000000002352597f8ec45c56ad19994808e982f5868c5ff6cfef2e",
      "anchorString": "Qm28BKV9iiM1ZNzMsi3HbDRHDPK5U2DEhKpCYhKk83UPEg",
      "transactionFeePaid": 5000,
      "normalizedTransactionFee": 100
    },
    {
      "transactionNumber": 18,
      "transactionHeight ": 545236,
      "transactionHash ": "0000000000000000000054f9719ef6ca646e2503a9c5caac1c6ea95ffb4af587",
      "anchorString": "Qmb2wxUwvEpspKXU4QNxwYQLGS2gfsAuAE9LPcn5LprS1nb",
      "transactionFeePaid": 30,
      "normalizedTransactionFee": 10
 
    },
    {
      "transactionNumber": 16,
      "transactionHeight ": 545200,
      "transactionHash ": "0000000000000000000f32c84291a3305ad9e5e162d8cc363420831ecd0e2800",
      "anchorString": "QmbBPdjWSdJoQGHbZDvPqHxWqqeKUdzBwMTMjJGeWyUkEzK",
      "transactionFeePaid": 50000,
      "normalizedTransactionFee": 150
    },
    {
      "transactionNumber": 12,
      "transactionHeight": 545003,
      "transactionHash": "0000000000000000001e002080595267fe034d370897b7b506d119ad29da1541",
      "anchorString": "Qmss3gKdm9uU9YLx3MPRHQTcUq1CR1Xv9Zpdu7EBG9Pk9Y",
      "transactionFeePaid": 1000000,
      "normalizedTransactionFee": 200
    },
    {
      "transactionNumber": 4,
      "transactionHeight": 544939,
      "transactionHash": "00000000000000000000100158f474719e5a319933856f7f464fcc65a3cb2253",
      "anchorString": "QmdcDrVPWy3ZXoZcuvFq7fDVqatks22MMqPAxDqXsZzGhy"
      "transactionFeePaid": 100,
      "normalizedTransactionFee": 50
    }
  ]
}
```

如果上述的参数有误则返回如下的数据：

```http
HTTP/1.1 400 Bad Request
 
{
  "code": "invalid_transaction_number_or_time_hash"
}
```

 

### 1.4.2    响应数据

#### 1.4.2.1   响应头

| 字段名           | 字段值               |
| ---------------- | -------------------- |
| `Content-Type  ` | ` application/json ` |

#### 1.4.2.2   响应Body

响应各字段说明：

```json
{
  "transactionNumber": "The transaction number of the first valid transaction in the given list",
  "transactionHeight": "The logical blockchain time this transaction is anchored. Used for protocol version selection.",
  "transactionHash": "The hash associated with the transaction time.",
  "anchorString": "The string written to the blockchain for this transaction.",
  "transactionFeePaid": "A number representing the fee paid for this transaction.",
  "normalizedTransactionFee": "A number representing the normalized transaction fee used for proof-of-fee calculation."
}
```

示例：

```json
HTTP/1.1 200 OK
 
{
  "transactionNumber": 16,
  "transactionHeight": 545200,
  "transactionHash": "0000000000000000000f32c84291a3305ad9e5e162d8cc363420831ecd0e2800",
  "anchorString": "QmbBPdjWSdJoQGHbZDvPqHxWqqeKUdzBwMTMjJGeWyUkEzK",
  "transactionFeePaid": 50000,
  "normalizedTransactionFee": 50
}
```

 

## 1.5    Sidetree交易上链

### 1.5.1    请求数据

如果交易上链错误返回如下数据：

| 错误码                            | 错误码说明       |
| --------------------------------- | ---------------- |
| `spending_cap_per_period_reached` | 费用超过使用限制 |
| `not_enough_balace_for_write`     | 余额不足         |

#### 1.5.1.1   请求路径

```http
POST /ledger/write/transactions
```

#### 1.5.1.2   请求头

| 字段名         | 字段值             |
| -------------- | ------------------ |
| `Content-Type` | `application/json` |

#### 1.5.1.3   请求Body

Body各字段说明：

```json
{
  "fee": "A number representing the transaction fee to be paid to write this transaction to the blockchain.",
  "anchorString": "The string to be written to the blockchain for this transaction."
}
```

示例：

```json
POST /ledger/write/transactions HTTP/1.1
 
{
  "fee": 200000,
  "anchorString": "QmbJGU4wNti6vNMGMosXaHbeMHGu9PkAUZtVBb2s2Vyq5d"
}
```

 

### 1.5.2    响应数据

#### 1.5.2.1   响应头

`无。`

#### 1.5.2.2   响应Body

`无。`

## 1.6    获取区块链上Sidetree交易的交易费收费标准

### 1.6.1    请求数据

#### 1.6.1.1   请求路径

请求标准交易费用：

```http
GET /ledger/fee
```

请求是标准费用说明。

请求的是某个区块链中评价交易费用：

```http
GET /ledger/fee/<blockchain-hash>
```

 

#### 1.6.1.2   请求头

`无。`

#### 1.6.1.3   请求Body

`无。`

### 1.6.2    响应数据

#### 1.6.2.1   响应头

| 字段名         | 字段值              |
| -------------- | ------------------- |
| `Content-Type` | ` application/json` |

#### 1.6.2.2   响应Body

Body字段说明：

```json
{
  "normalizedTransactionFee": "A number representing the normalized transaction fee used for proof-of-fee calculation."
}
```

示例：

```json
HTTP/1.1 200 OK
 
{
  "normalizedTransactionFee": 200000
}
```

## 1.7    获取当前区块链的版本信息

### 1.7.1    请求数据

#### 1.7.1.1   请求路径

```http
GET /ledger/version
```

#### 1.7.1.2   请求头

`无。`

#### 1.7.1.3   请求Body

`无。`

### 1.7.2    响应数据

#### 1.7.2.1   响应头

| 字段名          | 字段值              |
| --------------- | ------------------- |
| `Content-Type ` | `application/json ` |

#### 1.7.2.2   响应Body

响应各个字段说明：

```json
{
  "name": "A string representing the name of the service",
  "version": "A string representing the version of currently running service."
}
```

示例：

```json
HTTP/1.1 200 OK
 
{
  "name": "palletone",
  "version": "1.0.0"
}
```

 

