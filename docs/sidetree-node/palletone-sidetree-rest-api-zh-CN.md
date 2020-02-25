# PalletOne Sidetree REST API说明

返回值说明：

| 返回值 | 描述                   |
| ------ | ---------------------- |
| 200    | 成功                   |
| 401    | 未经身份验证或未经授权 |
| 400    | 错误的客户请求         |
| 404    | 资源不存在             |
| 500    | 服务器错误             |

返回400时，会返回对应的code信息，示例如下：

```json
HTTP/1.1 400 Bad Request
 
{
  "code": "invalid_transaction_number_or_time_hash"
}
```

 

## 1.1    DID And DID Document Creation

### 1.1.1    请求数据

#### 1.1.1.1   请求路径

```http
POST /did/creation HTTP/1.1
```



#### 1.1.1.2   请求头

| 字段名         | 字段值             |
| -------------- | ------------------ |
| `Content-Type` | `application/json` |

#### 1.1.1.3   Body数据语法

```json
{
  "type": "create",
  "suffixData": "Base64URL(suffix data file)",
  "operationData": " Base64URL(operation data file)"
}
```

##### 1.1.1.3.1  Body中suffix data file文件生成语法

```json
{
  "operationDataHash": "Hash of the encoded operation data string. ",
  "recoveryKey": {
    "publicKeyHex": "The recovery public key as a HEX string. "
  },
  "nextRecoveryOtpHash": "Hash of the one-time password to be used for the next recovery. "
}
```

##### 1.1.1.3.2  Body中operation data file文件生成语法：

```json
{
    "nextUpdateOtpHash": "Hash of the one-time password to be used for the next update.
 ",
    "document": "Opaque content."
 
}
```

##### 1.1.1.3.3  Document Json文件示例

```json
{
  "publicKey": [
    {
      "id": "#key1",
      "type": "Secp256k1VerificationKey2018",
      "publicKeyHex": "02f49802fb3e09c6dd43f19aa41293d1e0dad044b68cf81cf7079499edfd0aa9f1",
      "usage": "signing"
    }
  ],
  "service": [
    {
      "type": "IdentityHub",
      "publicKey": "#key1",
      "serviceEndpoint": {
        "@context": "schema.identity.foundation/hub",
        "@type": "UserServiceEndpoint",
        "instances": [
          "did:bar:456",
          "did:zaz:789"
        ]
      }
    }
  ]
}
```

### 1.1.2    响应数据

#### 1.1.2.1   响应数据头

| 字段名         | 字段值             |
| -------------- | ------------------ |
| `Content-Type` | `application/json` |

#### 1.1.2.2   响应Body数据

通过DID构造的DID Document，示例如下：

```json
{
  "@context": "https://w3id.org/did/v1",
  "id": "did:sidetree:EiBJz4qd3Lvof3boqBQgzhMDYXWQ_wZs67jGiAhFCiQFjw",
  "publicKey": [{
    "id": "#key1",
    "type": "Secp256k1VerificationKey2018",
    "publicKeyHex": "029a4774d543094deaf342663ae672728e12f03b3b6d9816b0b79995fade0fab23"
  }],
  "service": [{
    "id": "IdentityHub",
    "type": "IdentityHub",
    "serviceEndpoint": {
      "@context": "schema.identity.foundation/hub",
      "@type": "UserServiceEndpoint",
      "instance": ["did:bar:456", "did:zaz:789"]
    }
  }]
}
```

 

## 1.2    DID Resolution

该API功能是根据用户提供的DID返回其对应的DID文档。目前有两种方案：

1)    标准查询：did:sidetree:<unique-portion>

2)    未发布的DID查询：did:sidetree:<unique-portion>;initial-values=<encoded-original-did-document>

当发布了一个DID Document之后，发布者又需要立即使用该DID Document，则在调用的时候将上一个未被写入Blockchain的DID Document写入到DID信息里面，例如：

```json
did:sidetree:exKwW0HjS5y4zBtJ7vYDwglYhtckdO15JDt1j5F5Q0A;initial-values=ewogICAgICAiQGNvbnRleHQiOiAiaHR0cHM6Ly93M2lkLm9yZy9kaWQvdjEiLAogICAgICAicHVibGljS2V5IjogWwogICAgICAgIHsKICAgICAgICAgICAgImlkIjogIiNrZXkxIiwKICAgICAgICAgICAgInR5cGUiOiAiU2VjcDI1NmsxVmVyaWZpY2F0aW9uS2V5MjAxOCIsCiAgICAgICAgICAgICJwdWJsaWNLZXlIZXgiOiAiMDM0ZWUwZjY3MGZjOTZiYjc1ZThiODljMDY4YTE2NjUwMDdhNDFjOTg1MTNkNmE5MTFiNjEzN2UyZDE2ZjFkMzAwIgogICAgICAgIH0KICAgICAgXQogICAgfQ
 
```

上述initial-values继续Base64URL解码得到原DID Document如下：

```json
{
	"@context": "https://w3id.org/did/v1",
	"publicKey": [
        {
			"id": "#key1",
			"type": "Secp256k1VerificationKey2018",
			"publicKeyHex": "034ee0f670fc96bb75e8b89c068a1665007a41c98513d6a911b6137e2d16f1d300"
        }
    ]
}
```

### 1.2.1    请求数据

#### 1.2.1.1   请求路径

```http
GET /did/resolution/<did-or-method-name-prefixed-encoded-original-did-document> HTTP/1.1
```

#### 1.2.1.2   请求头

无

#### 1.2.1.3   请求数据示例

1)    标准请求示例

```http
GET /did/resolution/did:sidetree:exKwW0HjS5y4zBtJ7vYDwglYhtckdO15JDt1j5F5Q0A HTTP/1.1
```

2)    未发布DID查询示例

```http
GET /did/resolution/did:sidetree:exKwW0HjS5y4zBtJ7vYDwglYhtckdO15JDt1j5F5Q0A;initial-values=ewogICAgICAiQGNvbnRleHQiOiAiaHR0cHM6Ly93M2lkLm9yZy9kaWQvdjEiLAogICAgICAicHVibGljS2V5IjogWwogICAgICAgIHsKICAgICAgICAgICAgImlkIjogIiNrZXkxIiwKICAgICAgICAgICAgInR5cGUiOiAiU2VjcDI1NmsxVmVyaWZpY2F0aW9uS2V5MjAxOCIsCiAgICAgICAgICAgICJwdWJsaWNLZXlIZXgiOiAiMDM0ZWUwZjY3MGZjOTZiYjc1ZThiODljMDY4YTE2NjUwMDdhNDFjOTg1MTNkNmE5MTFiNjEzN2UyZDE2ZjFkMzAwIgogICAgICAgIH0KICAgICAgXQogICAgfQ HTTP/1.1
```

 

### 1.2.2    响应数据

返回DID Document文件。

#### 1.2.2.1   响应数据头

| 字段名         | 字段值             |
| -------------- | ------------------ |
| `Content-Type` | `application/json` |

#### 1.2.2.2   响应Body数据

返回DID Document文件示例如下：

```json
{
  "@context": "https://w3id.org/did/v1",
  "id": "did:sidetree:EiBJz4qd3Lvof3boqBQgzhMDYXWQ_wZs67jGiAhFCiQFjw",
  "publicKey": [{
    "id": "#key1",
    "type": "Secp256k1VerificationKey2018",
    "publicKeyHex": "029a4774d543094deaf342663ae672728e12f03b3b6d9816b0b79995fade0fab23"
  }],
  "service": [{
    "id": "IdentityHub",
    "type": "IdentityHub",
    "serviceEndpoint": {
      "@context": "schema.identity.foundation/hub",
      "@type": "UserServiceEndpoint",
      "instance": ["did:bar:456", "did:zaz:789"]
    }
  }]
}
```

 

## 1.3    DID Document Updation

更新DID Document信息。

### 1.3.1    请求数据

#### 1.3.1.1   请求路径

```http
POST /did/updation HTTP/1.1
```

#### 1.3.1.2   请求头

| 字段名         | 字段值             |
| -------------- | ------------------ |
| `Content-Type` | `application/json` |

 

#### 1.3.1.3   Body数据

Body数据中各个字段说明：

```json
{
  "protected": "Encoded protected header.",
  "payload": "Encoded update payload JSON object defined by the schema below.",
  "signature": "Encoded signature."
}
```

##### 1.3.1.3.1  Body中Payload字段字段说明

```json
{
  "type": "update",
  "didUniqueSuffix": "The unique suffix of the DID",
  "patches": ["An array of patches each must adhere to the patch schema defined below."],
  "updateOtp": "此次更新所使用的一次性密钥公钥",
  "nextUpdateOtpHash": "下次更新所使用的一次性密钥公钥",
}
 
```

##### 1.3.1.3.2  Payload中patches字段语法

1)    添加公钥

```json
{
  "action": "add-public-keys",
  "publicKeys": [
    {
      "id": "A string that begins with '#'.",
      "type": "Secp256k1VerificationKey2018 | RsaVerificationKey2018",
      "publicKeyHex": "Must be compressed format (66 chars) for Secp256k1VerificationKey2018, else any property can be used.",
    }
  ]
}
```

示例：

```json
{
  "action": "add-public-keys",
  "publicKeys": [
    {
      "id": "#key1",
      "type": "Secp256k1VerificationKey2018",
      "publicKeyHex": "0268ccc80007f82d49c2f2ee25a9dae856559330611f0a62356e59ec8cdb566e69"
    },
    {
      "id": "#key2",
      "type": "RsaVerificationKey2018",
      "publicKeyPem": "-----BEGIN PUBLIC KEY...END PUBLIC KEY-----"
    }
  ]
}
```

2)    删除公钥

```json
{
  "action": "remove-public-keys",
  "publicKeys": ["Array of 'id' property of public keys to remove."]
}
```

示例：

```json
{
  "action": "remove-public-keys",
  "publicKeys": ["#key1", "#key2"]
}
```

 

3)    添加服务终端

```json
{
  "action": "add-service-endpoints",
  "serviceType": "IdentityHub",
  "serviceEndpoints": [
    "Array of DID to add."
  ]
}
```

示例：

```json
{
  "action": "add-service-endpoints",
  "serviceType": "IdentityHub",
  "serviceEndpoints": [
    "did:sidetree:EiDk2RpPVuC4wNANUTn_4YXJczjzi10zLG1XE4AjkcGOLA",
    "did:sidetree:EiBQilmIz0H8818Cmp-38Fl1ao03yOjOh03rd9znsK2-8A"
  ]
}
```

 

4)    移除服务终端

```json
{
  "action": "remove-service-endpoints",
  "serviceType": "IdentityHub",
  "serviceEndpoints": [
    "Array of DID to remove."
  ]
}
```

示例：

```json
{
  "action": "remove-service-endpoints",
  "serviceType": "IdentityHub",
  "serviceEndpoints": [
    "did:sidetree:EiDk2RpPVuC4wNANUTn_4YXJczjzi10zLG1XE4AjkcGOLA",
    "did:sidetree:EiBQilmIz0H8818Cmp-38Fl1ao03yOjOh03rd9znsK2-8A"
  ]
}
```

##### 1.3.1.3.3  Upload的完整示例

```json
{
  "type": "update",
  "didUniqueSuffix": "EiBQilmIz0H8818Cmp-38Fl1ao03yOjOh03rd9znsK2-8A",
  "patches": [
    {
      "action": "add-public-keys",
      "publicKeys": [
        {
          "id": "#key1",
          "type": "Secp256k1VerificationKey2018",
          "publicKeyHex": "0268ccc80007f82d49c2f2ee25a9dae856559330611f0a62356e59ec8cdb566e69"
        },
        {
          "id": "#key2",
          "type": "RsaVerificationKey2018",
          "publicKeyPem": "-----BEGIN PUBLIC KEY...END PUBLIC KEY-----"
        }
      ]
    },
    {
      "action": "remove-service-endpoints",
      "serviceType": "IdentityHub",
      "serviceEndpoints": [
        "did:sidetree:EiBJz4qd3Lvof3boqBQgzhMDYXWQ_wZs67jGiAhFCiQFjw",
        "did:sidetree:EiAJ6AlyUPaEOxXk-AdXoEikeTf7DhcXvF61MfgnjJgazg"
      ]
    }
  ],
  "updateOtp": "iDk2RpPVuC4wNANUTn_4YXJczjzi10zLG1XE4AjkcGOLAa",
  "nextUpdateOtpHash": "EiDJesPq9hAIPrBiDw7PBZG8OUCG4XT5d6debxCUIVFUrg",
}
```

 

### 1.3.2    响应数据

Body部分无。

## 1.4    DID Deletion

删除某个DID及对应的DID Document信息。

### 1.4.1    请求数据

#### 1.4.1.1   请求路径

```http
POST /did/deletion
```

 

#### 1.4.1.2   请求头

| 字段名         | 字段值             |
| -------------- | ------------------ |
| `Content-Type` | `application/json` |

 

#### 1.4.1.3   请求Body

```json
{
  "protected": "Encoded protected header.",
  "payload": "Encoded delete payload JSON object define by the schema below.",
  "signature": "Encoded signature."
}
```

##### 1.4.1.3.1  Payload的语法

```json
{
  "type": "delete",
  "didUniqueSuffix": "The unique suffix of the DID to be deleted.",
  "recoveryOtp": "The current one-time recovery password."
}
```

示例：

```json
{
  "type": "delete",
  "didUniqueSuffix": "EiAJ6AlyUPaEOxXk-AdXoEikeTf7DhcXvF61MfgnjJgazg",
  "recoveryOtp": "BJzEi4qd3Lvof3boqBQgzhMDYXWQ_wZs67jGiAhFCiQFjw"
}
```

 

### 1.4.2    响应数据

无。

## 1.5    DID Recovery

### 1.5.1    请求数据

#### 1.5.1.1   请求路径

```http
POST /did/recovery
```

#### 1.5.1.2   请求头

| 字段名         | 字段值             |
| -------------- | ------------------ |
| `Content-Type` | `application/json` |

#### 1.5.1.3   请求Body

```json
{
  "protected": "Encoded protected header.",
  "payload": "Encoded recovery payload JSON object defined by the schema below.",
  "signature": "Encoded signature."
}
```

##### 1.5.1.3.1  Payload的语法

```json
{
  "type": "recover",
  "didUniqueSuffix": "The unique suffix of the DID to be recovered.",
  "recoveryOtp": "The one-time password to be used for this recovery.",
  "newDidDocument": "The encoded new DID Document.",
  "nextRecoveryOtpHash": "Hash of the one-time password to be used for the next recovery.",
  "nextUpdateOtpHash": "Hash of the one-time password to be used for the next update.",
}
```

 

### 1.5.2    响应数据

`无。`

## 1.6    Proof Claim Creation

### 1.6.1    请求数据

#### 1.6.1.1   请求路径

```http
POST /claim/creation HTTP/1.1
```

#### 1.6.1.2   请求头

| 字段名         | 字段值             |
| -------------- | ------------------ |
| `Content-Type` | `application/json` |

#### 1.6.1.3   请求Body数据语法

```json
{
  "type": "claimCreate",
  "documentHash": "Hash of the encoded operation data string. ",
  "document": " Base64URL(opreation data file)"
}
```

##### 1.6.1.3.1  Document Json文件示例

```json
{
"type": ["VerifiableCredential", "AlumniCredential"],
  "credentialSubject": {
    "id": "did:example:ebfeb1f712ebc6f1c276e12ec21",
    "alumniOf": {
      "id": "did:example:c276e12ec21ebfeb1f712ebc6f1",
      "name": [{
        "value": "Example University",
        "lang": "en"
      }, {
        "value": "Exemple d'Université",
        "lang": "fr"
      }]
    }
  },
  "proof": {  }
}
```

### 1.6.2    响应数据

#### 1.6.2.1   响应数据头

| 字段名         | 字段值             |
| -------------- | ------------------ |
| `Content-Type` | `application/json` |

#### 1.6.2.2   响应Body数据

对应的proof claim文档示例如下：

```json
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://www.w3.org/2018/credentials/examples/v1"
  ],
  "id": "claim:ptn:<unique suffix data>",
  "type": ["VerifiableCredential", "AlumniCredential"],
  "credentialSubject": {
    "id": "did:example:ebfeb1f712ebc6f1c276e12ec21",
    "alumniOf": {
      "id": "did:example:c276e12ec21ebfeb1f712ebc6f1",
      "name": [{
        "value": "Example University",
        "lang": "en"
      }, {
        "value": "Exemple d'Université",
        "lang": "fr"
      }]
    }
  },
  "proof": {  }
}
```

## 1.7 Proof Claim Revocation

### 1.7.1    请求数据

#### 1.7.1.1   请求路径

```http
POST /proof/revocation
```

#### 1.7.1.2   请求头

| 字段名         | 字段值             |
| -------------- | ------------------ |
| `Content-Type` | `application/json` |

 

#### 1.7.1.3   请求Body

```json
{
  "protected": "Encoded protected header.",
  "payload": "Encoded delete payload JSON object define by the schema below.",
  "signature": "Encoded signature."
}
```

##### 1.7.1.3.1  Payload的语法

```json
{
  "type": "claimRevocate",
  "claimUniqueSuffix": "The unique suffix of the proof claim to be revocated.",
  "revocationTime": "It can be current or future time",
  "reasonsFor": "Optional, the reasons to revocate this proof claim",
}
```

 

### 1.7.2    响应数据

`无。`

## 1.8 Proof Claim授权

### 1.8.1    请求数据

#### 1.8.1.1   请求路径

```http
POST /proof/authorization
```

#### 1.8.1.2   请求头

| 字段名         | 字段值             |
| -------------- | ------------------ |
| `Content-Type` | `application/json` |

 

#### 1.8.1.3   请求Body

```json
{
  "protected": "Encoded protected header.",
  "payload": "Encoded delete payload JSON object define by the schema below.",
  "signature": "Encoded signature."
}
```

##### 1.8.1.3.1  Payload的语法

```json
{
  "type": "claimAuthorize",
  "didUniqueSuffix": "The unique suffix of the DID",
  "claimUniqueSuffix": "The unique suffix of the proof claim.",
  "publicKey": "<didUniqueSuffix>#<pubkey number>",
  "signature": "Optional, the reasons to revocate this proof claim",
}
```

### 1.8.2    响应数据

#### 1.8.2.1   响应数据头

| 字段名         | 字段值             |
| -------------- | ------------------ |
| `Content-Type` | `application/json` |

#### 1.8.2.2   响应Body数据

返回DID Document文件示例如下：

```json
{
  "authentionID": "A unique number for ",
  "didUniqueSuffix": " The unique suffix of the DID ",
  "claimUniqueSuffix": " The unique suffix of the proof claim."
}
```

## 1.9 Proof Claim Revolution

### 1.9.1    请求数据

#### 1.9.1.1   请求路径

```http
GET /proof/resolution/<proof id> HTTP/1.1
```

#### 1.9.1.2   请求头

无

### 1.9.2    响应数据

返回DID Document文件。

#### 1.9.2.1   响应数据头

| 字段名         | 字段值             |
| -------------- | ------------------ |
| `Content-Type` | `application/json` |

#### 1.9.2.2   响应Body数据

返回DID Document文件示例如下：

```json
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://www.w3.org/2018/credentials/examples/v1"
  ],
  "id": "proof:ptn:<unique suffix data>",
  "type": ["VerifiableCredential", "AlumniCredential"],
  "credentialSubject": {
    "id": "did:example:ebfeb1f712ebc6f1c276e12ec21",
    "alumniOf": {
      "id": "did:example:c276e12ec21ebfeb1f712ebc6f1",
      "name": [{
        "value": "Example University",
        "lang": "en"
      }, {
        "value": "Exemple d'Université",
        "lang": "fr"
      }]
    }
  },
  "proof": {  }
}
```

## 1.10 获取当前Sidetree节点版本信息

### 1.10.1  请求数据

#### 1.10.1.1 请求路径

```http
GET /version
```

#### 1.10.1.2 请求头

`无。`

#### 1.10.1.3 请求Body

`无。`

### 1.10.2  响应数据

#### 1.10.2.1 响应数据头

| 字段名         | 字段值             |
| -------------- | ------------------ |
| `Content-Type` | `application/json` |

#### 1.10.2.2 响应Body数据

Body各字段说明：

```json
[
  {
    "name": "A string representing the name of the service",
    "version": "A string representing the version of currently running service."
  },
  ...
]
```

示例：

```json
HTTP/1.1 200 OK
 
[
  {
  "name":"core",
  "version":"0.4.1"
  },
  {
    "name":"bitcoin",
    "version":"0.4.1"
  },
  {
    "name":"ipfs",
    "version":"0.4.1"
  }
]
```

 