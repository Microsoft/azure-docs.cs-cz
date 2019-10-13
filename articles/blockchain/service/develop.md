---
title: Přehled vývoje služeb Azure blockchain
description: Úvod k vývoji řešení ve službě Azure blockchain.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/11/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 2990917243507d93cb6a86da11cf771f3a42eebd
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298429"
---
# <a name="azure-blockchain-service-development-overview"></a>Přehled vývoje služeb Azure blockchain

Pomocí služby Azure blockchain můžete vytvářet sítě konsorcia blockchain, které umožní podnikovým scénářům, jako je sledování prostředků, digitální tokeny, věrnostní funkce, finanční a prokázané dodavatelského řetězce. Tento článek je Úvod k vývoji služeb Azure blockchain a klíčovým tématům pro implementaci blockchain pro podnik.

## <a name="client-connection-to-azure-blockchain-service"></a>Připojení klienta ke službě Azure blockchain

Existují různé typy klientů pro blockchain sítě, včetně úplných uzlů, lehkých uzlů a vzdálených klientů. Služba Azure blockchain vytvoří síť blockchain, která obsahuje uzly. Pro vývoj blockchain můžete použít různé klienty jako bránu ke službě Azure blockchain. Služba Azure blockchain nabízí základní ověřování nebo přístupový klíč jako koncový bod vývoje. Níže jsou populární klienti, kteří můžete použít k připojení.

### <a name="metamask"></a>MetaMask

MetaMask je peněženka založená na prohlížeči (vzdálený klient), klient RPC a základní Průzkumník kontraktů. Na rozdíl od jiných kapesních prohlížečů MetaMask vloží instanci web3 do kontextu JavaScript v prohlížeči, který funguje jako klient RPC, který se připojuje k celé řadě Ethereem blockchainy (*mainnet*, *Ropsten testnet*, *KOVAN*testnet, místní uzel RPC, atd.). Můžete snadno nastavit vlastní RPC pro připojení ke službě Azure blockchain a zahájit vývoj blockchain pomocí Remix.

### <a name="geth"></a>Geth

Geth je rozhraní příkazového řádku pro spuštění úplného uzlu Ethereem implementovaného v příkazu Přejít. Nemusíte spouštět úplný uzel, ale můžete spustit jeho interaktivní konzolu, která poskytuje běhové prostředí JavaScriptu, které zpřístupňuje rozhraní API jazyka JavaScript pro interakci se službou Azure blockchain.

## <a name="development-framework-configuration"></a>Konfigurace vývojového rozhraní

Aby bylo možné vyvíjet sofistikovaná podniková řešení blockchain, je pro připojení k různým sítím blockchain nutné použít vývojové rozhraní, spravovat životní cyklus inteligentních kontraktů, automatizovat testování, nasazovat inteligentní kontrakt se skripty a vybavit interaktivní konzolu.

Truffle je oblíbená blockchain vývojová platforma pro psaní, kompilování, nasazování a testování decentralizovaných aplikací na Ethereem blockchainy. Můžete si také představit Truffle jako rozhraní, které se pokusí hladkou integraci vývoje inteligentních smluv a tradičního vývoje webů.

I nejmenší projekt komunikuje s alespoň dvěma blockchain uzly: jeden na počítači vývojáře a druhý představuje síť, ve které vývojář nasadí svoji aplikaci. Například hlavní veřejná Ethereem síť nebo služba Azure blockchain. Truffle poskytuje systém pro správu artefaktů kompilace a nasazení pro každou síť a takovým způsobem, který zjednodušuje konečné nasazení aplikace. Další informace najdete v tématu [rychlý Start: použití Truffle pro připojení k síti služby Azure blockchain](connect-truffle.md).

## <a name="ethereum-quorum-private-transaction"></a>Privátní transakce kvora ethereem

Kvorum je protokol distribuované hlavní knihy založený na Ethereem s transakcí a ochranou osobních údajů smluv a novými mechanismy konsensu. Mezi klíčová vylepšení v cestách Ethereem patří:

* Soukromí – kvorum podporuje privátní transakce a soukromé kontrakty prostřednictvím oddělení veřejných a soukromých stavů a využívá zašifrované zprávy typu peer-to-peer, které slouží k přímému přenosu privátních dat do účastníků sítě.
* Alternativní mechanismy konsensu – bez nutnosti ověření platnosti nebo položení kontrolního výtisku v oprávněné síti. Kvorum nabízí více mechanismů konsensu, které jsou navrženy pro konsorcia, jako jsou například vory a IBFT.  Služba Azure blockchain používá mechanismus IBFT konsensu.

* Oprávnění peer-to-node a peer-to s použitím inteligentních kontraktů, které zajišťují, aby se k síti mohly připojit jenom známé strany
* Vyšší výkon – kvorum nabízí vyšší výkon než veřejná Geth

Příklad soukromé transakce najdete v tématu [kurz: odeslání transakce pomocí služby Azure blockchain](send-transaction.md) .

## <a name="block-explorers"></a>Blokovat Průzkumníka

Blokování Průzkumníkovi jsou online prohlížeče blockchain, které zobrazují jednotlivé blokované obsahy, data adres transakcí a historii. Základní informace o blokování jsou k dispozici prostřednictvím Azure Monitor ve službě Azure blockchain. Pokud ale během vývoje potřebujete podrobnější informace, můžou být užitečné Průzkumníka bloků.  Existují oblíbené otevřené průzkumníky bloků, které můžete použít. Níže je seznam blokovaných Průzkumníka, které pracují se službou Azure blockchain Service:

* [Epirus Azure blockchain Service Explorer](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview) z Web3 Labs
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

## <a name="tps-measurement"></a>TPS měření

Protože blockchain se používá ve více podnikových scénářích, rychlost transakcí za sekundu (TPS) je důležitá, aby nedocházelo k kritickým bodům a neefektivitě systému. Vysoké sazby za transakce můžou být obtížné udržovat v rámci decentralizovaného blockchain. Přesné měření TPS může být ovlivněno různými faktory, jako je například vlákno serveru, velikost fronty transakcí, latence sítě a zabezpečení. Pokud během vývoje potřebujete změřit rychlost TPS, je oblíbený open source nástroj [ChainHammer](https://github.com/drandreaskrueger/chainhammer).

## <a name="next-steps"></a>Další kroky

[Rychlý Start: použití Truffle pro připojení k síti služby Azure blockchain](connect-truffle.md)
