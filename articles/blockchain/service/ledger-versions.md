---
title: Verze hlavní knihy služeb Azure blockchain, opravy, aktualizace &
description: Přehled podporovaných verzí v hlavní knize ve službě Azure blockchain. Zahrnuje zásady pro opravy a upgrady systémů.
ms.date: 06/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: ea7c54e229178320329204a3199ab2b4c44058fe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85807736"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Podporované verze hlavní knihy služby Azure blockchain

Služba Azure blockchain používá hlavní knihu [kvora](https://www.goquorum.com/developers) založenou na ethereem navrženou pro zpracování privátních transakcí v rámci skupiny známých účastníků, která je určená jako konsorcium ve službě Azure blockchain Service.

V současné době služba Azure blockchain podporuje [kvorum verze 2.6.0](https://github.com/jpmorganchase/quorum/releases/tag/v2.6.0) a [správce transakcí Tessera](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Správa aktualizací a upgradů

Správa verzí v kvoru se provádí prostřednictvím hlavní, vedlejší a opravné verze. Pokud je například kvorum verze 2.0.1, typ verze bude zařazen následovně:

|Hlavní | Vedlejší  | Patch  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Služba Azure blockchain automaticky aktualizuje vydané verze kvora na stávající běžící členy do 30 dnů od jejího zpřístupnění z kvora.

## <a name="availability-of-new-ledger-versions"></a>Dostupnost nových verzí hlavní knihy

Služba Azure blockchain poskytuje nejnovější hlavní a dílčí verze hlavní knihy kvora do 60 dnů od jejího zpřístupnění od výrobce kvora. Pro konsorcia si můžete při zřizování nového člena a konsorcia poskytnout maximálně čtyři dílčí verze. Upgrade z verze na hlavní nebo dílčí verzi se v tuto chvíli nepodporuje. Například pokud používáte verzi 2. x, upgrade na verzi 3. x není aktuálně podporován. Podobně pokud používáte verzi 2,2, upgrade na verzi 2,3 není aktuálně podporován.

## <a name="how-to-check-quorum-ledger-version"></a>Postup kontroly verze hlavní knihy kvora

Verzi kvora můžete ve vašem členovi služby Azure blockchain zjistit připojením k uzlu pomocí geth nebo zobrazením diagnostických protokolů.

### <a name="using-geth"></a>Použití geth

Připojte se k uzlu služby Azure blockchain pomocí geth. Například, `geth attach https://myblockchainmember.blockchain.azure.com:3200/<Access key>`.

Po připojení uzlu geth oznamuje verzi kvora podobnou následujícímu výstupu:

``` text
instance: Geth/v1.9.7-stable-9339be03(quorum-v2.6.0)/linux-amd64/go1.13.12
```

Další informace o použití geth najdete v tématu [rychlý Start: použití geth pro připojení k uzlu transakce služby Azure blockchain](connect-geth.md).

### <a name="using-diagnostic-logs"></a>Použití diagnostických protokolů

Pokud povolíte diagnostické protokoly, bude pro uzly transakcí hlášena verze kvora. Například následující informační protokolová zpráva o uzlu obsahuje verzi kvora.

``` text 
{"NodeName":"transaction-node","Message":"INFO [06-22|05:31:45.156] Starting peer-to-peer node instance=Geth/v1.9.7-stable-9339be03(quorum-v2.6.0)/linux-amd64/go1.13.12\n"}
{"NodeName":"transaction-node","Message":"[*] Starting Quorum node with QUORUM_VERSION=2.6.0, TESSERA_VERSION=0.10.5 and PRIVATE_CONFIG=/working-dir/c/tm.ipc\n"}
111
```

Další informace o diagnostických protokolech najdete v tématu [monitorování služby Azure blockchain prostřednictvím Azure monitor](monitor-azure-blockchain-service.md#diagnostic-settings).

## <a name="how-to-check-genesis-file-content"></a>Jak kontrolovat obsah souboru Genesis

Chcete-li zjistit obsah souboru Genesis vašeho uzlu blockchain, můžete použít následující rozhraní API pro Ethereem JavaScript:

``` bash
admin.nodeInfo.protocols
```
Rozhraní API můžete volat pomocí konzoly geth nebo knihovny web3. Další informace o použití geth najdete v tématu [rychlý Start: použití geth pro připojení k uzlu transakce služby Azure blockchain](connect-geth.md).

## <a name="next-steps"></a>Další kroky

[Omezení ve službě Azure blockchain](limits.md)
