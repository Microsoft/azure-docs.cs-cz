---
title: Globální distribuce dat pomocí služby Azure Cosmos DB | Dokumentace Microsoftu
description: Další informace o globálním měřítku geografickou replikaci, více hlavních databází, převzetí služeb při selhání a obnovení dat pomocí globálních databází ze služby Azure Cosmos DB, globálně distribuovaná a vícemodelová databázová služba.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.openlocfilehash: 227c243d82665dc533e3bfa6a1fe3e9bb775a262
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47408891"
---
# <a name="global-data-distribution-with-azure-cosmos-db"></a>Distribuce globálních dat pomocí služby Azure Cosmos DB

Azure je všudypřítomná – to globální stopu napříč více než 50 geografických oblastí a pokus se neustále rozšiřuje. Globální přítomnost služeb a podporu několika hlavními databázemi jednou diferencované možnosti, které Azure nabízí svým vývojářům je schopnost vytvářet, nasazovat a spravovat snadno globálně distribuované aplikace.

[Azure Cosmos DB](../cosmos-db/introduction.md) je globálně distribuovaná databázová služba Microsoftu s více modely pro klíčové aplikace. Azure Cosmos DB poskytuje globální distribuce na klíč, [elastické škálování propustnosti a úložiště](../cosmos-db/partition-data.md) po celém světě, jednociferné čtení a zápisu milisekund na 99. percentilu, [jasně definovaných konzistence modely](consistency-levels.md)a zaručenou vysokou dostupnost, vše zajištěné [špičkové komplexní smlouvy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [automaticky indexuje všechna data](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) aniž byste se museli starat o správu schématu nebo indexů.

## <a name="global-distribution-with-multi-master"></a>Globální distribuce s několika hlavními uzly

Jako cloudová služba Azure Cosmos DB pečlivě navržené pro podporu více tenantů, globální distribuci a více hlavních databází pro dokument, klíč hodnota, graf a sloupcových dat. modely.

![Kontejner Azure Cosmos DB rozdělit na oddíly a distribuovat ve třech oblastech](./media/distribute-data-globally/global-apps.png)

**Jeden kontejner Azure Cosmos DB rozdělit na oddíly a distribuované napříč několika oblastmi Azure**

Protože jsme zjistili při vytváření služby Azure Cosmos DB, přidávání globální distribuce nemůže být opomíjet. Nemůže být "přišroubovány na" imitovaná databázový systém "jedna lokalita". Funkce nabízené globálně distribuovanou databázi span nad rámec funkcí, které nabízí obnovením tradiční zeměpisné po havárii (Geo-DR) nabízí "jednou lokalitou" databází. Nabízí možnost Geo-DR databáze jedné lokality jsou striktní podmnožinou globálně distribuované databáze.

S globální distribuce služby Azure Cosmos DB na klíč nemají vývojářům vytvářet své vlastní replikace generování uživatelského rozhraní pomocí vzoru Lambda přes protokol databáze nebo provedením "double zápisy" napříč několika oblastmi. Děláme *není* doporučujeme tyto přístupy, protože jde o neuskutečnitelnou zajistili její správnost tyto přístupy a poskytovat zvukové smlouvy o úrovni služeb.

## <a id="Next Steps"></a>Další kroky

* [Jak služby Azure Cosmos DB umožňuje globální distribuce na klíč](distribute-data-globally-turnkey.md)

* [Azure Cosmos DB klíčové výhody globální distribuce](distribute-data-globally-benefits.md)

* [Jak nakonfigurovat službu Azure Cosmos DB globální replikace databáze](tutorial-global-distribution-sql-api.md)

* [Povolení více hlavních databází pro účty služby Azure Cosmos DB](enable-multi-master.md)

* [Jak funguje automatické a ruční převzetí služeb při selhání ve službě Azure Cosmos DB](regional-failover.md)

* [Principy řešení konfliktů ve službě Azure Cosmos DB](multi-master-conflict-resolution.md)

* [Použití více hlavních databází s databázemi NoSQL otevřít zdroj](multi-master-oss-nosql.md)
