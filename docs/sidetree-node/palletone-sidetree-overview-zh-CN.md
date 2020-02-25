# PalletOne Sidetree节点总体介绍

## Sidetree节点数据流转图

Sidetree节点与PalletOne、IPFS之间的数据流程图如下图所示：

![img](https://raw.githubusercontent.com/palletone/palletone-DID/master/src/images/sidetree-node-data-flow.png)

上图中，箭头代表数据的流动方向，双向箭头表示数据既有流出也有流入。

## Sidetree节点模块

Sidetree节点总共由4部分组成，分别是：Sidetree Core、Blockchain REST API、Sidetree REST API、CAS REST API。每个模块的功能如下图所示：

![img](https://github.com/palletone/palletone-DID/blob/master/src/images/sidetree-node-modules.png?raw=true)



## 参考文献

| 文献名                   | URL                                                          |
| ------------------------ | ------------------------------------------------------------ |
| W3C的DID定义V1.0         | https://www.w3.org/TR/did-core/                              |
| Sidetree Node.js版本说明 | https://github.com/decentralized-identity/sidetree/blob/master/docs/protocol.md<br />https://github.com/decentralized-identity/sidetree/blob/master/docs/protocol.md |

