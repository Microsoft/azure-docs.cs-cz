---
title: Přehled vývoje Azure Blockchain služby
description: Úvod k vývoji řešení ve službě Azure Blockchain Service.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 388a5d8c80c3e2462602959e9d5cbc1452974d1f
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027897"
---
# <a name="azure-blockchain-service-development-overview"></a>Přehled vývoje Azure Blockchain služby

Se službou Azure Blockchain můžete vytvořit consortium blockchain sítě umožňuje podnikové scénáře jako asset sledování digitální token, věrnostních programů a potřebu, dodavatelský řetězec finanční a původu. Tento článek je úvodem do klíče témata k implementaci blockchain pro podnikové a Přehled služby Azure Blockchain vývoje.

## <a name="client-connection-to-azure-blockchain-service"></a>Připojení klienta ke službě Azure Blockchain

Existují různé typy klientů pro blockchain sítě, včetně úplné uzly, světla uzly a vzdálených klientů. Služba Azure Blockchain sestavení blockchain sítě, který obsahuje uzly. Můžete použít různé klienty jako brána ke službě Azure Blockchain pro vývoj blockchainů. Služba Azure Blockchain nabízí základní ověřování nebo přístupového klíče jako koncový bod vývoje. Následují oblíbených klientů můžete použít připojení.

### <a name="metamask"></a>MetaMask

MetaMask je založené na prohlížeči peněženky (vzdáleného klienta), klient vzdáleného volání Procedur a základního kontraktu explorer. Na rozdíl od jiných peněženky prohlížeče MetaMask vkládá web3 instance do kontextu JavaScript prohlížeče funguje jako klient protokolu RPC, který se připojuje k různým Etherea blockchainy (*mainnet*, *Ropsten testnet*, *Kovan testnet*, místní uzel wsfc RPC, atd.). Můžete nastavit vlastní vzdáleného volání Procedur snadno připojit ke službě Azure Blockchain a spustit pomocí Remix vývoj blockchainů.

### <a name="geth"></a>Geth

Geth se rozhraní příkazového řádku pro spuštění úplné uzel Etherea implementované v Go. Není nutné spustit úplné uzlu, ale můžete spustit své interaktivní konzolu, která poskytuje úniku JavaScript API k interakci se službou Azure Blockchain prostředí runtime jazyka JavaScript.

## <a name="development-framework-configuration"></a>Konfigurace architektury pro vývoj

K vývoji propracované podnikové blockchainová řešení, je vývojářská platforma potřebné k připojení k sítím různých blockchain, Správa životního cyklu inteligentní smlouvy, automatizovat testování, nasazení inteligentní kontraktu pomocí skriptů a vybavení interaktivní konzoly.

Truffle je vývojářská platforma oblíbených blockchain k zápisu, kompilace, nasazování a testování aplikací decentralizované na blockchainy Etherea. Můžete také představit Truffle jako architektura, která se pokusí bezproblémově integrovat vývoj inteligentních smlouvy a tradičními webovými.

I nejmenší projektu komunikuje s alespoň dva uzly blockchain: Jeden v počítači vývojáře a další představující sítě, kdy vývojář nasadí své aplikace. Například hlavní veřejné síti Etherea nebo služba Blockchain v Azure. Truffle poskytuje systém pro správu artefakty sestavení a nasazení pro každou síť a provádí se způsobem, který zjednodušuje nasazení poslední aplikace. Další informace najdete v tématu [rychlý start: Použít pro připojení k Truffle síť služby Azure Blockchain](connect-truffle.md).

## <a name="ethereum-quorum-private-transaction"></a>Privátní transakce Etherea kvora

Kvora je protokol na základě ethereum během distribuované účetní knihy s transakcí a smlouvy o ochraně osobních údajů a nové shody mechanismy. Klíče přes Go Etherea vylepšení:

* Soukromí – kvora podporuje privátní transakce a privátní smlouvy prostřednictvím veřejných a privátních stavu oddělení, které využívá výměny šifrovaných zpráv peer-to-peer řízené přenos dat soukromých účastníky v síti.
* Alternativní mechanismus Caiq – bez nutnosti pro testování práce nebo do testování podíl shody v síti udělili oprávnění. Kvora nabízí několik mechanismů shody, které jsou určeny pro řetězy consortium, jako je například RAFT a IBFT.  Služby Azure Blockchain používá mechanismus IBFT shody.

* Vytvoření partnerského vztahu videí - uzel a peer videí pomocí chytrých kontraktů, zajištění jenom známé strany se může připojit k síti
* Vyšší výkon - kvora nabízí vyšší výkon než veřejné Geth

Zobrazit [kurzu: Odeslat transakci pomocí služby Azure Blockchain](send-transaction.md) příklad privátní transakce.

## <a name="block-explorers"></a>Průzkumníci bloku

Průzkumníci bloku jsou online blockchain prohlížečů, které zobrazují jednotlivá blokovat obsah, transakce adresu data a historie. Informace o základním bloku je k dispozici prostřednictvím služby Azure Monitor ve službě Azure Blockchain, ale pokud potřebujete podrobnější informace během vývoje, průzkumníci bloku může být užitečné.  Existují průzkumníci oblíbených open source bloku, která vám pomůže. Následuje seznam průzkumníci bloku, které fungovaly se službou Azure Blockchain:

* [Průzkumník služby Azure Blockchain](https://web3labs.com/azure-offer) z Web3 Labs
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

## <a name="tps-measurement"></a>TPS měření

Jak blockchain se používá ve více scénářích enterprise, je důležité vyhnout se kritickým bodům a nedostatků systému transakcí za druhé rychlost (TPS). Může být obtížné udržovat v rámci decentralizované blockchain vysoké sazby. Přesná měření TPS může být ovlivňován různými faktory, jako je například server vlákna, velikost fronty transakcí, latence sítě a zabezpečení. Pokud potřebujete k měření rychlosti TPS během vývoje, je oblíbený open source nástroj [ChainHammer](https://github.com/drandreaskrueger/chainhammer).

## <a name="next-steps"></a>Další postup

[Rychlé zprovoznění: Použít pro připojení k Truffle síť služby Azure Blockchain](connect-truffle.md)
