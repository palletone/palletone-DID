# PalletOne DID规范

## DID字段说明

DID语法格式： `did:ptn:<did unique suffix>`

各字段的说明：

| 字段                | 说明                                                         |
| ------------------- | ------------------------------------------------------------ |
| `did:ptn`           | PalletOne DID 方法名称                                       |
| `did unique suffix` | DID的唯一标识后缀，生成规则（参考比特币地址的生成规则）如下：<br />`Base58(RIPEMD160(sha256(Base DID Document)))` |



## Base DID Document

上述DID Document对应的Base DID Document如下所示。Base DID Document被完整包含在DID创建的请求中。

```json
{
    "@context": "https://www.w3.org/ns/did/v1"
	"controller": ["did:ptn:5678"],
    "publicKey": [
        {
            "id": "#keys-1",
            "type": "secp256k1",
            "publicKeyHex": "04ca29d84972c51af7f09c481951f28f2bb1b5ea03f4997b68977c7be013d7f35c079bec522d0bef84a116498dee4f100d8ceb29cd196bd416b444e17569bf19356b5c62d4d555e73e3341f8cc36edcfe2ebf240fa6a292af39dff68a769ee42c9",
  		}, {
            "id": "#keys-2",
            "type": "secp256k1",
            "controller": "did:ptn:5678#keys-1",
  		}, {
        	"id": "#keys-3",
          	"type": "secp256k1",
          	"publicKeyHex": "0467cd4a6cbb17609dc6c34639ec35fac229e8612f8b8046ba2f64e2642fe88f801894b5636de3dbcd94dfc1bac95143b1104714306ad329a1e069fe12f055497a5ebb10dc9d9a9dfb1092055e4b29da06110cad1da121086944ba73cc6484dc1e"
        }
    ],
    "authentication": ["#keys-1", "#keys-2", "#keys-3"],
    "updation":["#keys-1", "#keys-2"],
    "deletion":["#keys-1"],
    "recovery":["#keys-1"]
}
```

Base DID Document示例中各个字段的说明（<font color=#ff0000>*</font>为必须字段，其他为可选字段）：

- <font color=#ff0000 face="黑体">*</font> `context`   单个值或数组，指定DID Document格式符合的语法标准。

- `controller`  单个值或数组，DID Document的其他所属者。可以指定其他DID管理该文件，其他DID的权限在后面对应操作authentication、updation、deletion、recovery中进行设定。

- <font color=#ff0000>*</font> `publicKey`  单个值或数组，控制该DID Document的私钥所对应的公钥信息。
  - <font color=#ff0000>*</font> `id`  公钥的ID，统一以`#keys-<num>`方式表示，`<num>`从`1`开始递增
  - <font color=#ff0000>*</font> `type`  公钥生成的算法，与链上统一，
  - `controller`  该公钥的所属者，与上一级别中的`controller`对应。格式是`<DID>#keys-<num>`。默认情况由该文档DID控制。`<DID>`取值在上一级中的`controller`中，`#keys-<num>`为`<DID>`对应的某个公钥`id`。
  - `publicKeyHex`  公钥的十六进制信息。当上述<u>controller为缺省的时候该字段为**必须字段**</u>。
- <font color=#ff0000>*</font> `authentication`  指定`publicKey`中哪些字段可以用作身份验证。
- `updation`  指定`publicKey`中哪些字段可以用作DID Document**<u>更新</u>**操作，比如更新pubkey或者service等信息。
- `deletion`  指定`publicKey`中哪些字段可以用作DID Document<u>**删除**</u>操作。
- `recovery`  指定`publicKey`中哪些字段可以用作DID Document<u>**恢复**</u>操作。



## DID Document语法说明

DID Document的示例如下：

```json
{
    "@context": "https://www.w3.org/ns/did/v1",
    "id": "did:ptn:1234",
	"controller": ["did:ptn:1234", "did:ptn:5678"],
    "publicKey": [
        {
            "id": "#keys-1",
            "type": "secp256k1",
            "publicKeyHex": "04ca29d84972c51af7f09c481951f28f2bb1b5ea03f4997b68977c7be013d7f35c079bec522d0bef84a116498dee4f100d8ceb29cd196bd416b444e17569bf19356b5c62d4d555e73e3341f8cc36edcfe2ebf240fa6a292af39dff68a769ee42c9",
  		}, {
            "id": "#keys-2",
            "type": "secp256k1",
            "controller": "did:ptn:5678#keys-1",
  		}, {
        	"id": "#keys-3",
          	"type": "secp256k1",
          	"publicKeyHex": "0467cd4a6cbb17609dc6c34639ec35fac229e8612f8b8046ba2f64e2642fe88f801894b5636de3dbcd94dfc1bac95143b1104714306ad329a1e069fe12f055497a5ebb10dc9d9a9dfb1092055e4b29da06110cad1da121086944ba73cc6484dc1e"
        }
    ],
    "authentication": ["#keys-1", "#keys-2", "#keys-3"],
    "updation":["#keys-1", "#keys-2"],
    "deletion":["#keys-1"],
    "recovery":["#keys-1"],
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
    		"creator": "#keys-1",
    		"signatureValue": "30650230327bd3d73dbeeae61627e877238fc039934dcf559c03935a739a61ac59a6966f7215b21d0d4d2d34dd2c16a92a8e5810023100b14417f7ef0e3c9404c9fc0b1bf077f225c368d6e119bba754985f5c601204024d2a84001f5c9675d86a8f456a378cdc" 
        },{
           	"type": "secp256k1",
    		"creator": "#keys-2",
    		"signatureValue": "3065023100c443ecf723d1b2132fcfd6500a2db31e5fe1b89cd70a1c0593689f803d11331a5b480f5b3ae98c74ed31e4b6d9f80952023026963f070d2fcbc0ff2786824790d0c946ad1dc7ae25ec074b49b4c9025a0858006d8fd6c3b0e5ee3c3627b007bd52b5" 
        },{
           	"type": "secp256k1",
    		"creator": "#keys-3",
    		"signatureValue": "3066023100b57575ef16202cb557e319308d6ec4856857c9867351238811cc0c2e0e4a017acce408c32ee81364ee15ad04c4385c0b023100fab36a23731d41e8c16daf52af2e729c28a5ee185e5f7ffc0fb8f3930e79e77f350f4ad8a71f15823e31fedbc49a7eca" 
        }
    ]
}
```

上述DID Document示例中，未出现在Base DID Document中的字段的解释如下：

- `id`  DID Document文档的唯一标识符，即为DID。

- `controller`  单个值或数组，DID Document的所属者。可以指定其他DID管理该文件，其他DID的权限在后面对应操作authentication、updation、deletion、recovery中进行设定。

- `service`   表示该DID与关联DID或者其他实体进行通信的方式。该字段一般在发证方的DID Document中进行提供。

  - `id`  用于检索与DID关联的可验证凭证。
  - `type`   服务类型，在DID Resolution中进行定义
  - `serviceEndpoint`  代表DID主体或者其他主体运行的网络地址。比如发现服务，社交网络，文件存储服务和可验证的声明存储库服务。服务端点也可以由通用数据交换协议（例如可扩展数据交换）提供。

- `proof`  用于证明DID Document文档的所属权，自签。

  - `type`  自签算法。
  - `creator`  自签使用的公私钥对。
  - `signatureValue`  对Base DID Document进行签名的签名信息，例子中的签名数据不是真实数据。

  

## 参考文献

| 说明                     | URL                                                          |
| ------------------------ | ------------------------------------------------------------ |
| W3C的DID定义V1.0         | https://www.w3.org/TR/did-core/                              |
| Sidetree Node.js版本说明 | https://github.com/decentralized-identity/sidetree/blob/master/docs/protocol.md<br />https://github.com/decentralized-identity/sidetree/blob/master/docs/protocol.md |

