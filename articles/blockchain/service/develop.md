---
title: Přehled vývoje služeb Azure blockchain
description: Úvod k vývoji řešení ve službě Azure blockchain.
ms.date: 03/26/2020
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 4a98e5ea025894303bc5c77bba0c6154a08315f2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "80348386"
---
# <a name="azure-blockchain-service-development-overview"></a>Přehled vývoje služeb Azure blockchain

Pomocí služby Azure blockchain můžete vytvářet sítě konsorcia blockchain, které umožní podnikovým scénářům, jako je sledování prostředků, digitální tokeny, věrnostní funkce, finanční a prokázané dodavatelského řetězce. V následujících částech se zavádí vývoj služeb Azure blockchain pro implementaci podnikových řešení blockchain.

## <a name="connecting-to-azure-blockchain-service"></a>Připojování ke službě Azure blockchain

Existují různé typy klientů pro blockchain sítě, včetně úplných uzlů, lehkých uzlů a vzdálených klientů. Služba Azure blockchain vytvoří síť blockchain, která obsahuje uzly. Pro vývoj blockchain můžete použít různé klienty jako bránu ke službě Azure blockchain. Služba Azure blockchain nabízí základní ověřování nebo přístupový klíč jako koncový bod vývoje. Níže jsou populární klienti, kteří můžete použít k připojení.

### <a name="visual-studio-code"></a>Visual Studio Code

K členům konsorcia se můžete připojit pomocí rozšíření Visual Studio Code Azure blockchain Development Kit. Po připojení k konsorciu můžete kompilovat, sestavovat a nasazovat inteligentní kontrakty na člena konsorcia služeb Azure blockchain.

Aby bylo možné vyvíjet sofistikovaná podniková řešení blockchain, je potřeba pro připojení k různým sítím blockchain a správě životního cyklu inteligentních kontraktů vývojové rozhraní. Většina projektů pracuje s alespoň dvěma blockchain uzly. Vývojáři používají během vývoje místní blockchain. Když je aplikace připravená k testování nebo vydání, vývojář se nasadí do blockchain sítě. Například hlavní veřejná Ethereem síť nebo služba Azure blockchain. Azure blockchain Development Kit pro rozšíření Ethereem v Visual Studio Code používá Truffle. Truffle je oblíbená blockchain vývojová platforma pro psaní, kompilování, nasazování a testování decentralizovaných aplikací na Ethereem blockchainy. Můžete si také představit Truffle jako rozhraní, které se pokusí hladkou integraci vývoje inteligentních smluv a tradičního vývoje webů.

Další informace najdete v tématu [rychlý Start: použití Visual Studio Code k připojení k síti konsorcia služeb Azure blockchain](connect-vscode.md).

### <a name="metamask"></a>MetaMask

MetaMask je peněženka založená na prohlížeči (vzdálený klient), klient RPC a základní Průzkumník kontraktů. Na rozdíl od jiných kapesních prohlížečů MetaMask vloží instanci web3 do kontextu JavaScript v prohlížeči, který funguje jako klient RPC, který se připojuje k nejrůznějším Ethereem blockchainy (*mainnet*, *Ropsten testnet*, *Kovan* testnet, místnímu uzlu RPC atd.). Můžete snadno nastavit vlastní RPC pro připojení ke službě Azure blockchain a zahájit vývoj blockchain pomocí Remix.

Další informace najdete v tématu [rychlý Start: použití MetaMask k připojení a nasazení inteligentní smlouvy.](connect-metamask.md)

### <a name="geth"></a>Geth

Geth je rozhraní příkazového řádku pro spuštění úplného uzlu Ethereem implementovaného v příkazu Přejít. Nemusíte spouštět úplný uzel, ale můžete spustit jeho interaktivní konzolu, která poskytuje běhové prostředí JavaScriptu, které zpřístupňuje rozhraní API jazyka JavaScript pro interakci se službou Azure blockchain.

Další informace najdete v tématu [rychlý Start: použití Geth k připojení k uzlu transakce služby Azure blockchain](connect-geth.md).

## <a name="ethereum-quorum-private-transactions"></a>Privátní transakce kvora ethereem

Kvorum je protokol distribuované hlavní knihy založený na Ethereem s transakcí a ochranou osobních údajů smluv a novými mechanismy konsensu. Mezi Go-Ethereum jsou klíčové vylepšení:

* **Soukromí** – kvorum podporuje privátní transakce a soukromé kontrakty prostřednictvím oddělení veřejných a soukromých stavů a využívá zašifrované zprávy typu peer-to-peer, které slouží k přímému přenosu privátních dat do účastníků sítě.
* **Alternativní** požadavky na konsensu – ověření platnosti nebo ověření příložení není pro udělenou síť nutné. Kvorum nabízí více mechanismů konsensu, které jsou navrženy pro konsorcia, jako jsou například vory a IBFT.  Služba Azure blockchain používá mechanismus IBFT konsensu.
* Oprávnění **peer-to** -node a peer s oprávněními pomocí inteligentních kontraktů zajišťuje, že se k síti budou moci připojit pouze známí oprávnění.
* **Vyšší výkon** – kvorum nabízí vyšší výkon než veřejný Geth.

## <a name="block-explorers"></a>Blokovat Průzkumníka

Blokování Průzkumníkovi jsou online prohlížeče blockchain, které zobrazují jednotlivé blokované obsahy, data adres transakcí a historii. Základní informace o bloku jsou k dispozici prostřednictvím Azure Monitor ve službě Azure blockchain. Pokud ale během vývoje potřebujete podrobnější informace, můžou být užitečné Průzkumníka bloků.  Následující blokové Průzkumníka pracují se službou Azure blockchain:

* [Epirus Azure blockchain Service Explorer](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview) z Web3 Labs
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

Můžete také vytvořit vlastní blokování Průzkumníka pomocí Data Manager blockchain a Azure Cosmos DB najdete v tématu [kurz: použití data Manager blockchain k odesílání dat do Azure Cosmos DB](data-manager-cosmosdb.md).

## <a name="tps-measurement"></a>TPS měření

Protože blockchain se používá ve více podnikových scénářích, rychlost transakcí za sekundu (TPS) je důležitá, aby nedocházelo k kritickým bodům a neefektivitě systému. Vysoké sazby za transakce můžou být obtížné udržovat v rámci decentralizovaného blockchain. Přesné měření TPS může být ovlivněno různými faktory, jako je například vlákno serveru, velikost fronty transakcí, latence sítě a zabezpečení. Pokud během vývoje potřebujete změřit rychlost TPS, je oblíbený open source nástroj [ChainHammer](https://github.com/drandreaskrueger/chainhammer).

## <a name="next-steps"></a>Další kroky

Vyzkoušejte si rychlý Start pomocí Azure blockchain Development Kit pro Ethereem, který se připojí k konsorciu ve službě Azure blockchain.

> [!div class="nextstepaction"]
> [Použití Visual Studio Code k připojení ke službě Azure blockchain](connect-vscode.md)