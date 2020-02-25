# PalletOne Sidetree CAS REST API说明

# 1  CAS REST API

​                               

返回值说明：

| 返回值 | 描述                   |
| ------ | ---------------------- |
| 200    | 成功                   |
| 401    | 未经身份验证或未经授权 |
| 400    | 错误的客户请求         |
| 404    | 资源不存在             |
| 500    | 服务器错误             |

CAS的删除、更新、创建等操作是在DID对应的操作中同时进行的。

## 1.1    读取内容数据

### 1.1.1    请求数据

#### 1.1.1.1   请求路径

```http
GET /content/<hash>?max-size=<maximum-allowed-size>
```

`max-size`  返回文件大小限制，如果超出则返回`content_exceeds_maximum_allowed_size`错误码。

示例：

```http
GET /content/QmWd5PH6vyRH5kMdzZRPBnf952dbR4av3Bd7B2wBqMaAcf
```

#### 1.1.1.2   请求头

`无。`

#### 1.1.1.3   请求Body

`无。`

### 1.1.2    响应数据

#### 1.1.2.1   响应头

| 字段名         | 字段值                     |
| -------------- | -------------------------- |
| `Content-Type` | `application/octet-stream` |

#### 1.1.2.2   响应Body

```http
HTTP/1.1 200   返回对应的文件数据
HTTP/1.1 404 Not Found
HTTP/1.1 400 Bad Request
```

返回`400`的时候，出错码具体定义如下：

| Code                                   | Code说明         |
| -------------------------------------- | ---------------- |
| `content_exceeds_maximum_allowed_size` | 文件大小超过限制 |
| `Content_not_a_file`                   | 内容不是文件     |
| `content_hash_invalid`                 | 内容Hash无效     |

出错示例：

```json
HTTP/1.1 400 Bad Request
 
{
  "code": "content_exceeds_maximum_allowed_size"
}
```

 

## 1.2    写入数据

### 1.2.1    请求数据

#### 1.2.1.1   请求路径

```http
POST /content/write
```

#### 1.2.1.2   请求头

| 字段名         | 字段值                     |
| -------------- | -------------------------- |
| `Content-Type` | `application/octet-stream` |

#### 1.2.1.3   请求Body

`文件数据。`

### 1.2.2    响应数据

#### 1.2.2.1   响应头

| 字段名         | 字段值             |
| -------------- | ------------------ |
| `Content-Type` | `application/json` |

#### 1.2.2.2   响应Body

响应`Body`的字段定义：

```json
{
  "hash": "Hash of data written to CAS"
}
```

示例：

```json
{
  "hash": "QmWd5PH6vyRH5kMdzZRPBnf952dbR4av3Bd7B2wBqMaAcf"
}
```

 

## 1.3    获取IPFS版本信息

### 1.3.1    请求数据

#### 1.3.1.1   请求路径

```http
GET /content/version
```

#### 1.3.1.2   请求头

`无。`

#### 1.3.1.3   请求Body

`无。`

### 1.3.2    响应数据

#### 1.3.2.1   响应头

| 字段名         | 字段值             |
| -------------- | ------------------ |
| `Content-Type` | `application/json` |

#### 1.3.2.2   响应Body

响应Body字段说明：

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
  "name": "ipfs",
  "version": "1.0.0"
}
```

 

# 2     CAS存储

## 2.1    Batch File

DID操作的批处理文件，以.zip的文件格式进行存储。

文件定义格式如下：

```json
{
  "operations": [
    "Encoded operation",
    "Encoded operation",
    ...
  ]
}
```

其中操作数据部分是[Sidetree REST API](https://github.com/palletone/palletone-DID/blob/master/docs/sidetree-node/palletone-sidetree-rest-api-zh-CN.md)中的DID And DID Document Creation、DID Document Updation、DID Deletion、DID Recovery的请求Body部分。

文件有效性验证标准：

1)    文件语法是否合规；

2)    文件大小是否合规；

3)    Hash算法是否合规；

4)    DID Unique Suffix顺序与Anchor File一致，且所有的DID Unique Suffix能在Anchor File中找到。

## 2.2    Map File

该文件包含对一个或多个批处理文件的引用。目前，此映射文件仅引用一个批处理文件，但是该设计允许将操作数据分离为多个批处理文件，以实现按需优化解决方案。

Map File的文件定义如下所示：

```json
{
  "batchFileHash": "Encoded multihash of the batch file.",
}
```

 

## 2.3    Anchor File

Anchor File文件的哈希值作为Sidetree 交易写入到区块链，因此名称为“ anchor”。该文件包含以下内容：

```json
{
  "mapFileHash": "Encoded multihash of the map file.",
  "didUniqueSuffixes": ["Unique suffix of DID of 1st operation", "Unique suffix of DID of 2nd operation", "..."]
}
```

上述didUniqueSuffixes中DID顺序必须与Batch File中DID顺序一致。

增加Anchor File，而不是直接将Map File的Hash值上链是为了轻节点的实现。轻节点可以快速地找到对应的操作数据和DID信息，从而进行快速的验证。同时该设计未进一步其他应用提供可能性。

验证标准：

1)    语法检测；

2)    文件大小检测；

3)    Hash算法检测；

4)    DID Unique Suffix必须是唯一的。（？？应该是和Bath顺序一致）

 

## 2.4  DID Document

[PalletOne DID Method概述](https://github.com/palletone/palletone-DID/blob/master/docs/overview/palletone-did-method-overview-zh-CN.md)

[DID规范](https://github.com/palletone/palletone-DID/blob/master/docs/overview/palletone-did-syntaxes-zh-CN.md)



## 2.5  DID Proof Claim

[可验证声明规范](https://github.com/palletone/palletone-DID/blob/master/docs/overview/palletone-proof-claim-zh-CN.md)

## 2.6  Proof Claim的Batch File、Map file和Anchor file

Proof Claim的Batch File、Map file和Anchor file与§1.1～§1.3节中的定义类似，其中的`didUniqueSuffix`更换为`claimUniqueSuffix`。