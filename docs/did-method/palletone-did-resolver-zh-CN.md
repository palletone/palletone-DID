# PalletOne DID 解析器

PalletOne DID解析器首先从PalletOne DID注册中心获取DID信息的合法性，并从CAS中获取对应的DID文档信息。如果是DID URL解析，则进一步根据URL字段进一步进行返回对应的内容。

从W3C对DID解析器的说明文档[Decentralized Identifier Resolution (DID Resolution) v0.2](https://w3c-ccg.github.io/did-resolution/#architectures)中我们可以看到，DID解析的两个主要功能是：

- 解析DID，返回DID文档，方法是`resolve ( did, input-options )`。
- 根据DID URL信息返回对应的DID文档或者其它类型资源，方法是`dereference ( did-url, input-options )`。

## DID解析

解析DID或者其他DID是将某个DID作为输入，将其对应的[DID Document](https://github.com/palletone/palletone-DID/blob/master/docs/overview/palletone-did-syntaxes-zh-CN.md)作为输出，示例如下所示。

输入：`did:ptn:1234`

输出：

```json
{
    "@context": "https://www.w3.org/ns/did/v1",
    "id": "did:ptn:1234",
    "publicKey": [
        {
            "id": "did:ptn:1234#keys-1",
            "type": "secp256k1",
            "publicKeyHex": "04ca29d84972c51af7f09c481951f28f2bb1b5ea03f4997b68977c7be013d7f35c079bec522d0bef84a116498dee4f100d8ceb29cd196bd416b444e17569bf19356b5c62d4d555e73e3341f8cc36edcfe2ebf240fa6a292af39dff68a769ee42c9",
  		}, {
            "id": "did:ptn:1234#keys-2",
            "type": "secp256k1",
            "controller": "did:ptn:5678#keys-1",
  		}, {
        	"id": "did:ptn:1234#keys-3",
          	"type": "secp256k1",
          	"publicKeyHex": "0467cd4a6cbb17609dc6c34639ec35fac229e8612f8b8046ba2f64e2642fe88f801894b5636de3dbcd94dfc1bac95143b1104714306ad329a1e069fe12f055497a5ebb10dc9d9a9dfb1092055e4b29da06110cad1da121086944ba73cc6484dc1e"
        }
    ],
    "authentication": ["did:ptn:1234#keys-1", "did:ptn:1234#keys-2", "did:ptn:1234#keys-3"],
    "updation":["did:ptn:1234#keys-1", "did:ptn:1234#keys-2"],
    "deletion":["did:ptn:1234#keys-1"],
    "recovery":["did:ptn:1234#keys-1"],
    "service": [
        {
    		"id": "did:ptn:1234#resolver",
			"type": "DIDResolve",
    		"serviceEndpoint": "https://did.palleone.com/"
  		}
    ],
    "proof":[
        {
           	"type": "secp256k1",
    		"creator": "did:ptn:1234#keys-1",
    		"signatureValue": "30650230327bd3d73dbeeae61627e877238fc039934dcf559c03935a739a61ac59a6966f7215b21d0d4d2d34dd2c16a92a8e5810023100b14417f7ef0e3c9404c9fc0b1bf077f225c368d6e119bba754985f5c601204024d2a84001f5c9675d86a8f456a378cdc" 
        },{
           	"type": "secp256k1",
    		"creator": "did:ptn:1234#keys-2",
    		"signatureValue": "3065023100c443ecf723d1b2132fcfd6500a2db31e5fe1b89cd70a1c0593689f803d11331a5b480f5b3ae98c74ed31e4b6d9f80952023026963f070d2fcbc0ff2786824790d0c946ad1dc7ae25ec074b49b4c9025a0858006d8fd6c3b0e5ee3c3627b007bd52b5" 
        },{
           	"type": "secp256k1",
    		"creator": "did:ptn:1234#keys-3",
    		"signatureValue": "3066023100b57575ef16202cb557e319308d6ec4856857c9867351238811cc0c2e0e4a017acce408c32ee81364ee15ad04c4385c0b023100fab36a23731d41e8c16daf52af2e729c28a5ee185e5f7ffc0fb8f3930e79e77f350f4ad8a71f15823e31fedbc49a7eca" 
        }
    ]
}
```



## DID URL解析

DID URL解析与HTTP URL解析类似，根据DID URL的语法格式进行解析，输出对应的资源信息。其实上述的DID解析也可以算作是DID URL解析的一种形式。

下面通过几个常用例子展示DID URL的，同时下面的示例都是以上述DID Document为准。

### 示例1：公钥信息获取

输入：`did:ptn:1234#keys-1`

输出：

```json
{
  "@context": "https://www.w3.org/ns/did/v1",
  "id": "did:ptn:1234#keys-1",
  "type": "secp256k1",
  "publicKeyHex": "04ca29d84972c51af7f09c481951f28f2bb1b5ea03f4997b68977c7be013d7f35c079bec522d0bef84a116498dee4f100d8ceb29cd196bd416b444e17569bf19356b5c62d4d555e73e3341f8cc36edcfe2ebf240fa6a292af39dff68a769ee42c9"
}
```

### 示例2：服务信息获取

输入：`did:ptn:1234#resolver`

输出：

```json
{
	"@context": "https://www.w3.org/ns/did/v1",
    "id": "did:ptn:1234#resolver",
    "type": "DIDResolve",
    "serviceEndpoint": "https://did.palleone.com/"
}
```

### 示例3：获取其他资源

输入：`did:ptn:1234;service=resolver/did:ptn:1234`

输出：上述`did:ptn:1234`对应的DID Document信息。

本例子的解析过程如下图所示：

![did-resolver](https://github.com/palletone/palletone-DID/blob/master/src/images/did-resolver.png?raw=true)

