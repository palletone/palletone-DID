# PalletOne可验证声明规范

## Proof Claim

在PalletOne中，如何使用使用基于DID的去中心化数字身份，通过引入可验证声明使得用户可以使用DID登录到其他的第三方应用，下面是可验证声明的示例，并基于该示例对可验证声明中的各个字段进行说明。

```json
{
    "@context": [
        "https://www.w3.org/2018/credentials/v1",
        "https://www.w3.org/2018/credentials/examples/v1"
  	],
  	"id": "proof:ptn:<unique suffix data>",
  	"type": ["ProofClaim"],
  	"issuer": {
        "id": "did:ptn:91011",
        "name": "Example University"
  	},
  	"issuanceDate": "2010-01-01T19:23:24Z",
  	"credentialSubject": [
        {
      		"id": "did:ptn:1234",
            "type": "DegreeProof",
            "shortSubject":"BachelorDegree",
            "detailSubject": "Bachelor of Science and Arts"
		}
	],
    "credentialStatus": {
        "id": "https://example.edu/status/24",
        "type": "CredentialStatusList2020",
        "protected": "translucent"
    },
  	"proof": [
		{
            "type": "Secp256k1",
            "creator": "did:ptn:91011#keys-1",
            "signatureValue": "eyJhbGciOiJSUzI1NiIsImI2NCI6ZmFsc2UsImNyaXQiOlsiYjY0Il19"
        }
    ]
}
```

上述可验证声明中各个字段具体说明如下：

- <img src="C:\Users\Administrator\AppData\Local\YNote\data\yiyaxuelu@163.com\cc29f2e5405e47f8867d1c8dea3b282b\star__.png" alt="img" style="zoom: 67%;" />`context`  指定可验证声明使用的JSON格式版本。
- <img src="C:\Users\Administrator\AppData\Local\YNote\data\yiyaxuelu@163.com\cc29f2e5405e47f8867d1c8dea3b282b\star__.png" alt="img" style="zoom: 67%;" />`id`  可验证声明的ID信息，定义格式为`proof:ptn:<unique suffix data>`。其中`<unique suffix data>`计算方式为`Base58(RIPEMD160(sha256(Base Proof Claim Document)))` 。Base Proof Claim Document的定义如下一节所示。
- <img src="C:\Users\Administrator\AppData\Local\YNote\data\yiyaxuelu@163.com\cc29f2e5405e47f8867d1c8dea3b282b\star__.png" alt="img" style="zoom: 67%;" />`type`  该可验证声明的类型，目前均为`ProofClaim`。随着业务的增长，可以增加其他可验证声明类型。
- <img src="C:\Users\Administrator\AppData\Local\YNote\data\yiyaxuelu@163.com\cc29f2e5405e47f8867d1c8dea3b282b\star__.png" alt="img" style="zoom: 67%;" />`issuer`  发证方， 该字段还可以直接这样说明：`"issuer": "did:ptn:91011"`。发证方信息由发证方在请求可验证声明上链时在请求字段中给出。
  - `id`   发证方的DID。
  - `name`  发证方的机构名称，可选字段，可以增加可信度。
- <img src="C:\Users\Administrator\AppData\Local\YNote\data\yiyaxuelu@163.com\cc29f2e5405e47f8867d1c8dea3b282b\star__.png" alt="img" style="zoom: 67%;" />`issuanceDate`  可验证声明的发证日期。
- <img src="C:\Users\Administrator\AppData\Local\YNote\data\yiyaxuelu@163.com\cc29f2e5405e47f8867d1c8dea3b282b\star__.png" alt="img" style="zoom: 67%;" />`credentialSubject`   可验证声明的主体信息。
  - `id`  申请可验证声明的主体DID。
  - `type`  可验证声明的类型。
  - `shortSubject`  该可验证声明的简短说明，通常情况下为社会上对个证明的通俗叫法，比如“学历证明”、“在职证明”、“收入证明”等。
  - `detailSubject`  可验证声明的完整信息描述，比如“中国xxx大学xx学院xxx专业的学士学位”。
- `credentialStatus`  表示可验证声明的状态，一般为证明链接，由发证方在请求可验证声明上链时提供。
  - `id`  一般为发证方提供的可验证声明查看地址，其他人通过该地址可以获取用户的可验证声明信息。
  - `type`  可验证声明状态类型，由发证方指定。
  - `protected`  该字段表示可验证声明是否为个人文件。该值有3个状态： 如果是`yes`状态，表示任何人都无法查看。如果是  `translucent` 状态，则需要用户的授权码才能查看；如果是 `public`状态，则任何人可以直接查看该可验证声明，该状态不常用。
- <img src="C:\Users\Administrator\AppData\Local\YNote\data\yiyaxuelu@163.com\cc29f2e5405e47f8867d1c8dea3b282b\star__.png" alt="img" style="zoom: 67%;" />`proof`  发证方的身份证明
  - `type`  签名类型
  - `creator`  签名使用的公钥信息
  - `signatureValue`  签名内容



## Base Proof Claim

```json
{
    "@context": [
        "https://www.w3.org/2018/credentials/v1",
        "https://www.w3.org/2018/credentials/examples/v1"
  	],
  	"type": ["ProofClaim"],
  	"credentialSubject": [
        {
      		"id": "did:ptn:1234",
            "type": "DegreeProof",
            "shortSubject":"BachelorDegree",
            "detailSubject": "Bachelor of Science and Arts"
		}
	],
  	"proof": [
		{
            "type": "Secp256k1",
            "creator": "did:ptn:91011#keys-1",
            "signatureValue": "eyJhbGciOiJSUzI1NiIsImI2NCI6ZmFsc2UsImNyaXQiOlsiYjY0Il19"
        }
    ]
}
```

上述的Base Proof Claim在发证方请求上链时会在请求中完整给出。



## 参考文献

| 文献名            | URL                                  |
| ----------------- | ------------------------------------ |
| W3C可验证声明规范 | https://www.w3.org/TR/vc-data-model/ |