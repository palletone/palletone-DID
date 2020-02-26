# PalletOne去中心化数字身份合约

PalletOne中的区块链合约分为系统合约和用户合约，PalletOne去中心化数字身份合约包含在系统合约中。

PalletOne 全节点Console下调用去中心化数字身份合约的命令语法如下：

###### 获得当前区块高度

```shell
> admin.nodeInfo
{
  id: "2e6941e017c28b19030e86739fae589c81e80c0ee7001addea4634958e0674eb29a29c1eea222f2a849b29f3c18947d7ad6eddc21e01a126b8276eea2264baaf",
  ip: "::",
  listenAddr: "[::]:30303",
  name: "Gptn/v1.0.5-hotfix1/linux-amd64/go1.13.7",
  pnode: "pnode://2e6941e017c28b19030e86739fae589c81e80c0ee7001addea4634958e0674eb29a29c1eea222f2a849b29f3c18947d7ad6eddc21e01a126b8276eea2264baaf@[::]:30303",
  ports: {
    discovery: 30303,
    listener: 30303
  },
  protocols: {
    PTN: {
      Index: 361477,
      genesis: "0x375ea9e8c2f5a2f21e45acdb3c1dfdc6490f56f1242f3afef2ff0667a4af53b7",
      head: "0x750ec0cb7760d09df981000fc4572bfba594e52ad7feb9eb89c1fd3a1b5df5c3",
      network: 1
    },
    lps: {
      Index: 361477,
      genesis: "0x375ea9e8c2f5a2f21e45acdb3c1dfdc6490f56f1242f3afef2ff0667a4af53b7",
      head: "0x750ec0cb7760d09df981000fc4572bfba594e52ad7feb9eb89c1fd3a1b5df5c3",
      network: 1
    }
  }
}
```

上述返回结果中`protocols.PTN.Index`即为PTN区块的高度。另外，PalletOne可以支持分区，每个分区对应一个Token类型，所以可以看到`lps`这种Token的高度为`361477` 。

###### 根据区块Hash查询区块高度

```shell
> dag.getUnitByHash('0x750ec0cb7760d09df981000fc4572bfba594e52ad7feb9eb89c1fd3a1b5df5c3')
"{\"unit_header\":{\"parents_hash\":[\"0x9e42037461fd0fe701959ec0848ae2a8b8615473b4ab6bd27acdc5a514797438\"],\"hash\":\"0x750ec0cb7760d09df981000fc4572bfba594e52ad7feb9eb89c1fd3a1b5df5c3\",\"mediator_address\":\"P176ptwPKjo4ZTvBHemGMxYikhNYtWqUE9V\",\"mediator_pubkey\":\"03d15b9791812edf714e5bab5b347b66c5a5be994d419d0bcc13c6e2431cf3b783\",\"mediator_sign\":\"304402203163f92fe6e67eeea95d58b7bb73afbc9cf7d2fab44695b1995588498230a6a102203a336838db94703ef1342c68267f881b20ec0bbe9449419fd5275cafb0ee061d\",\"group_sign\":\"\",\"group_pubKey\":\"7d473bb8f9e1657557951ce3eaa98c4916dad505b84044906496c4e21d9acb5902b268edf9a8cb835562ae2285f6a404353fec2250abc52c47204c3e3e647ac80d54a026879a3977bd4fca109f003d7a0e1561cd792e5c768ea4ad3ca11f19a11bcf1b0bd9d7484db6b519f9cf0d4ea3f07de7048e19f7c6c6aff542f5342ce9\",\"root\":\"0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421\",\"txs_illegal\":[],\"index\":{\"asset_id\":\"PTN\",\"index\":361477},\"extra\":\"\",\"creation_time\":\"2020-02-26T21:51:39+08:00\"},\"transactions\":[],\"unit_hash\":\"0x750ec0cb7760d09df981000fc4572bfba594e52ad7feb9eb89c1fd3a1b5df5c3\",\"unit_size\":252,\"reward\":\"0\"}"
```

上述的返回值的`unit_header.index.index`即为`0x750ec0cb7760d09df981000fc4572bfba594e52ad7feb9eb89c1fd3a1b5df5c3`区块所在高度。

###### 获取交易信息

```powershell
> dag.getTxByHash("<transaction hash>")
> dag.getTxByReqId("<user contract request id>")
```

第一个口令是根据交易Hash获取交易信息。第二个口令是根据Request ID获取交易信息，这个口令是专门用于合约交易信息的获取。



###### Sidetree交易上链操作Console接口

```shell
// 写入sidetree交易
contract.ccinvoketx("P1AqKPs9eEzDoPwSjsop6EsoAt2X2NcP3NA","P1AqKPs9eEzDoPwSjsop6EsoAt2X2NcP3NA","100","1","PC<did contract unique suffix address>",["addSidetreeTx", "<anchor file hash>"])

// 获得指定sidetree交易区间的所有交易信息
contract.ccquery("PC<di contract unique suffix address>", ["getSidetreeTxs", "<since-transaction-hash>", "<transaction-hash>"])

// 获得指定区块区间的所有sidetree交易信息
contract.ccquery("PC<di contract unique suffix address>", ["getSidetreeTxs", "<since-unit-hash>", "<unit-hash>"])

// 获取交易列表中第一个有效的交易
contract.ccquery("PC<di contract unique suffix address>", ["firstValidSidetreeTx", "<zip sidetree transactions json file>"])
```
