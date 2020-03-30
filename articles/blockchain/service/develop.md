---
title: Přehled vývoje služby Azure Blockchain
description: Úvod k vývoji řešení ve službě Azure Blockchain Service.
ms.date: 03/26/2020
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 4a98e5ea025894303bc5c77bba0c6154a08315f2
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348386"
---
# <a name="azure-blockchain-service-development-overview"></a>Přehled vývoje služby Azure Blockchain

Se službou Azure Blockchain Service můžete vytvořit blockchainové sítě konsorcia, které umožní podnikové scénáře, jako je sledování majetku, digitální tokeny, loajalita a odměna, finanční dodavatelský řetězec a provenience. V následujících částech se zavádí vývoj služby Azure Blockchain Service pro implementaci podnikových blockchainových řešení.

## <a name="connecting-to-azure-blockchain-service"></a>Připojení ke službě Azure Blockchain

Existují různé typy klientů pro blockchainové sítě, včetně plných uzlů, lehkých uzlů a vzdálených klientů. Služba Azure Blockchain vytvoří blockchainovou síť, která obsahuje uzly. Jako bránu ke službě Azure Blockchain Service můžete používat různé klienty pro vývoj blockchainu. Služba Azure Blockchain service nabízí základní ověřovací nebo přístupový klíč jako koncový bod vývoje. Níže jsou uvedeny populární klienty, které můžete použít připojit.

### <a name="visual-studio-code"></a>Visual Studio Code

Ke členům konsorcia se můžete připojit pomocí rozšíření Azure Blockchain Development Kit Visual Studio Code. Po připojení ke konsorciu můžete kompilovat, vytvářet a nasazovat inteligentní kontrakty do člena konsorcia Azure Blockchain Service.

K vývoji sofistikovaných podnikových blockchainových řešení je zapotřebí vývojový rámec pro připojení k různým blockchainovým sítím a správu životního cyklu inteligentních smluv. Většina projektů interaguje s alespoň dvěma uzly blockchainu. Vývojáři používají během vývoje místní blockchain. Když je aplikace připravená k testování nebo vydání, vývojář se nasadí do blockchainové sítě. Například hlavní veřejná síť Ethereum nebo služba Azure Blockchain. Azure Blockchain Development Kit pro rozšíření Ethereum v visual studio kódu používá lanýže. Lanýž je populární blockchainový vývojový rámec pro psaní, kompilaci, nasazování a testování decentralizovaných aplikací na blockchainech Ethereum. Můžete si také myslet, že Lanýže je rámec, který se pokouší bezproblémově integrovat inteligentní vývoj smluv a tradiční vývoj webových aplikací.

Další informace najdete [v tématu Úvodní příručka: Pomocí kódu Visual Studia se můžete připojit k síti konsorcia Azure Blockchain Service](connect-vscode.md).

### <a name="metamask"></a>Metamaska

MetaMask je peněženka založená na prohlížeči (vzdálený klient), rpc klient a základní průzkumník smluv. Na rozdíl od jiných prohlížečových peněženek, MetaMask vstřikuje instanci web3 do kontextu prohlížeče JavaScript, který funguje jako rpc klient, který se připojuje k různým blockchainům Ethereum *(mainnet*, *Ropsten testnet*, *Kovan testnet*, místní uzel RPC atd.). Můžete nastavit vlastní Vzdálené volání procedur snadno se připojit ke službě Azure Blockchain Service a zahájit vývoj blockchainu pomocí Remixu.

Další informace naleznete [v tématu Úvodní příručka: Připojení a nasazení inteligentní smlouvy pomocí metamasku](connect-metamask.md)

### <a name="geth"></a>Geth (Š.)

Geth je rozhraní příkazového řádku pro spuštění úplného uzlu Ethereum implementovaného v Go. Nemusíte spouštět celý uzel, ale můžete spustit jeho interaktivní konzoli, která poskytuje prostředí javascriptového runtime, které vystavuje rozhraní JAVAScript API pro interakci se službou Azure Blockchain Service.

Další informace najdete [v tématu Úvodní příručka: Připojení k transakčnímu uzlu služby Azure Blockchain Service pomocí gethu.](connect-geth.md)

## <a name="ethereum-quorum-private-transactions"></a>Soukromé transakce ethereum Quorum

Kvorum je protokol distribuované hlavní knihy založený na Ethereu s transakcí a ochranou osobních údajů a novými mechanismy konsensu. Mezi klíčová vylepšení go-etherea patří:

* **Ochrana osobních údajů** – kvorum podporuje soukromé transakce a soukromé smlouvy prostřednictvím oddělení veřejného a soukromého státu a využívá výměny šifrovaných zpráv typu peer-to-peer pro řízený přenos soukromých dat účastníkům sítě.
* **Alternativní mechanismy konsensu** - pro povolení sítě není zapotřebí prokontovaný konsensus nebo shoda s protekcí sisu. Kvorum nabízí několik mechanismů konsensu, které jsou určeny pro řetězce konsorcia, jako jsou RAFT a IBFT.Služba Azure Blockchain používá konsenzuální mechanismus IBFT.
* **Peer oprávnění** - uzel a peer oprávnění pomocí inteligentní smlouvy zajišťuje pouze známé strany mohou připojit k síti.
* **Vyšší výkon** - Kvorum nabízí vyšší výkon než veřejné Geth.

## <a name="block-explorers"></a>Blokovat průzkumníky

Blokové průzkumníky jsou online blockchainové prohlížeče, které zobrazují individuální blokový obsah, data transakční adresy a historii. Základní informace o blocích jsou dostupné prostřednictvím Azure Monitoru ve službě Azure Blockchain Service. Pokud však potřebujete podrobnější informace během vývoje, mohou být užitečné průzkumníky bloků.  Následující průzkumníky bloků pracují se službou Azure Blockchain Service:

* [Průzkumník blockchainových služeb Epirus Azure](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview) z laboratoří Web3 Labs
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

Můžete si taky vytvořit vlastní průzkumník bloků pomocí Blockchain Data Manager a Azure Cosmos DB, [najdete v tématu Kurz: Použití Správce dat blockchainu k odesílání dat do Azure Cosmos DB](data-manager-cosmosdb.md).

## <a name="tps-measurement"></a>Měření TPS

Vzhledem k tomu, že se blockchain používá ve více podnikových scénářích, je důležitá rychlost transakcí za sekundu (TPS), aby se zabránilo úzkým místům a neefektivitě systému. Vysoké transakční sazby mohou být obtížné udržet v rámci decentralizovaného blockchainu. Přesné měření TPS může být ovlivněno různými faktory, jako je vlákno serveru, velikost transakční fronty, latence sítě a zabezpečení. Pokud potřebujete měřit rychlost TPS během vývoje, populární open-source nástroj je [ChainHammer](https://github.com/drandreaskrueger/chainhammer).

## <a name="next-steps"></a>Další kroky

Zkuste rychle začít používat Azure Blockchain Development Kit pro Ethereum připojit konsorcium na Azure Blockchain Service.

> [!div class="nextstepaction"]
> [Připojení ke službě Azure Blockchain Service pomocí kódu Visual Studia](connect-vscode.md)