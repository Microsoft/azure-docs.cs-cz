---
title: 'Úvod do Azure Cosmos DB: MongoDB rozhraní API | Microsoft Docs'
description: Zjistěte, jak můžete používat Azure Cosmos databázi k ukládání a dotaz ohromné objemy dokumentů JSON s nízkou latencí pomocí Oblíbené MongoDB rozhraní API operačních systémů.
keywords: Co je MongoDB
services: cosmos-db
author: AndrewHoh
manager: kfile
documentationcenter: ''
ms.assetid: 4afaf40d-c560-42e0-83b4-a64d94671f0a
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: anhoh
ms.openlocfilehash: bebf8b8830255277b5ea492f06e130ee667affa5
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="introduction-to-azure-cosmos-db-mongodb-api"></a>Úvod do Azure Cosmos DB: MongoDB rozhraní API

[Azure Cosmos DB](../cosmos-db/introduction.md) je globálně distribuovaná databázová služba Microsoftu s více modely pro klíčové aplikace. Poskytuje Azure Cosmos DB [globální distribuční klíč](distribute-data-globally.md), [elastické škálování propustnost a úložiště](partition-data.md) latence po celém světě, jednociferné milisekund na 99th percentilu a zaručenou vysoká dostupnost, všechny zálohovány pomocí [špičkový SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [automaticky indexuje data](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), aniž by vyžadovala zapojení správy schémat a indexů. Zahrnuje více modelů a podporuje modely dokumentů, klíčových hodnot, grafů a sloupcových dat. 

![Azure Cosmos DB: MongoDB rozhraní API](./media/mongodb-introduction/cosmosdb-mongodb.png) 

Azure Cosmos DB databází lze použít jako úložiště dat pro aplikace napsané pro [MongoDB](https://docs.mongodb.com/manual/introduction/). Tato funkce znamená, že pomocí stávající [ovladače](https://docs.mongodb.org/ecosystem/drivers/), vaše aplikace napsané pro MongoDB teď můžete komunikovat s Azure Cosmos DB a používat Azure Cosmos DB databáze místo databáze MongoDB. V mnoha případech můžete přepínat pomocí MongoDB k databázi Azure Cosmos jednoduše změnou připojovací řetězec. Pomocí této funkce lze snadno vytvářet a spouštět aplikace databázi MongoDB globálně distribuované ve službě Azure cloud s Azure Cosmos databáze a její [komplexní SLA špičkový](https://azure.microsoft.com/support/legal/sla/cosmos-db), nadále používat známé dovedností a nástrojů pro MongoDB.

**Kompatibilita MongoDB**: Azure Cosmos DB implementuje protokol přenosová MongoDB 3.4 (verze 5) a podporuje můžete použít existující MongoDB znalosti, kód aplikace a nástrojů [MongoDB agregace kanálu](mongodb-feature-support.md#aggregation-pipeline). Můžete vyvíjet aplikace, které používají MongoDB a nasadit je na produkčním prostředí pomocí plně spravovaná a globálně distribuované služby Azure Cosmos DB.

## <a name="what-is-the-benefit-of-using-azure-cosmos-db-for-mongodb-applications"></a>Co je výhodou používání Azure Cosmos DB pro MongoDB aplikace?

**Elasticky škálovatelná propustnost a úložiště:** splňují vaše aplikace potřebuje pomocí snadno škálování nahoru nebo dolů, databázi MongoDB. Data se ukládají na discích SSD (solid-state drive), které nabízí nízkou a předvídatelnou latenci. Azure Cosmos DB podporuje MongoDB kolekce, které je možné škálovat na prakticky neomezené velikosti úložiště a zřízené propustnosti. S růstem vaší aplikace je možné službu Azure Cosmos DB bezproblémově elasticky škálovat s předvídatelným výkonem. 

**Replikace více oblast:** Azure Cosmos DB transparentně replikuje data do všech oblastí, které jste spojené s vaším účtem MongoDB umožňuje vyvíjet aplikace, které vyžadují globální přístup k datům při současném poskytování kompromisy mezi konzistence, dostupnosti a výkonu, všechny s odpovídající záruky. Azure Cosmos DB poskytuje transparentní regionální převzetí služeb při selhání se souběžnou instalací rozhraní API a schopností elastického škálování propustnosti a úložiště po celém světě. Další informace v [distribuci dat globálně](distribute-data-globally.md).

**Žádný server správy**: Nemáte ke správě a škálovat vaše databáze MongoDB. Azure Cosmos DB je plně spravovaná služba, což znamená, že nemusíte spravovat všechny infrastruktury nebo virtuální počítače sami. Je k dispozici v 30 + Azure Cosmos DB [oblasti Azure](https://azure.microsoft.com/regions/services/).

**Přizpůsobitelné úrovně konzistence:** Azure Cosmos DB aktuálně implementuje MongoDB verze 3.4, který má dvě nastavení konzistence silné a případnou. Protože Azure Cosmos DB více api, toto nastavení konzistence lze použít na úrovni účtu a vynucení konzistence se řídí každé rozhraní API. Dokud MongoDB 3.6, byl žádná koncepce konzistence relace, takže pokud nastavíte účet rozhraní API MongoDB chcete použít konzistence typu relace, je při použití rozhraní API MongoDB snížit na případné konzistence. Pokud potřebujete záruku pro čtení a vaše vlastníte zápis pro účet rozhraní API MongoDB, výchozí úroveň konzistence pro účet by mělo být nastavené silné nebo ohraničenou typu prošlostí. Další informace najdete v tématu popisujícím [využití úrovní konzistence pro maximalizaci dostupnosti a výkonu](consistency-levels.md).

| Azure Cosmos DB výchozí úroveň konzistence |   Rozhraní API mongo (3.4) |
|---|---|
|Nahodilé| Nahodilé |
|Konzistentní předpona| Závěrečné s konzistentní pořadí |
|Relace| Závěrečné s konzistentní pořadí |
|Omezená neaktuálnost| Silné |
| Silné | Silné |

**Automatické indexování**: ve výchozím nastavení, Azure Cosmos DB automaticky indexuje všechny vlastnosti v rámci dokumenty ve vaší MongoDB databáze a nemá očekávat nebo nevyžaduje žádné schéma nebo vytváření sekundárních indexů. Kromě toho funkce jedinečný index umožňuje omezení jedinečnosti na všechna pole dokumentu, které už jsou indexované automaticky v Azure Cosmos DB.

**Podnikové úrovni**: Azure Cosmos DB podporuje více místní repliky k poskytování 99,99 % dostupnost a ochranu dat při krátkodobém místní a regionální selhání. Azure Cosmos DB má podnikové úrovni [dodržování předpisů certifikace](https://www.microsoft.com/trustcenter) a funkce zabezpečení. 

Další informace v tomto videu se Azure Cosmos DB hlavní programový manažer, Aleksey Savateyev.

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T136/player]
> 

## <a name="how-to-get-started"></a>Jak začít

Postupujte podle quickstarts MongoDB k vytvoření účtu Azure Cosmos DB a migrovat stávající aplikace MongoDB pomocí Azure Cosmos DB nebo vytvořit nový:

* [Migrovat stávající webovou aplikaci Node.js MongoDB](create-mongodb-nodejs.md).
* [Vytvoření webové aplikace MongoDB API pomocí rozhraní .NET a portálu Azure](create-mongodb-dotnet.md)
* [Sestavení rozhraní API MongoDB konzolovou aplikaci Java a portálu Azure](create-mongodb-java.md)

## <a name="next-steps"></a>Další postup

Informace o rozhraní API služby Azure DB Cosmos MongoDB je integrována do dokumentace se celkově Azure Cosmos DB, ale tady jsou na několik věcí, které vám pomůžou začít:

* Postupujte podle [připojit k účtu MongoDB](connect-mongodb-account.md) kurzu se dozvíte, jak získat informace o účtu připojovací řetězec.
* Postupujte podle [3T pomocí Studio (MongoChef) s Azure Cosmos DB](mongodb-mongochef.md) kurzu se dozvíte, jak vytvořit připojení mezi Azure Cosmos DB databáze a MongoDB aplikace v Studio 3 T.
* Postupujte podle [migrace dat do databáze Cosmos Azure s protokolem podporu pro MongoDB](mongodb-migrate.md) kurzu pro import dat do rozhraní API pro databázi MongoDB.
* Připojit k rozhraní API pro použití účtu MongoDB [Robomongo](mongodb-robomongo.md).
* Zjistěte, kolik RUs jsou vaše operace pomocí [GetLastRequestStatistics příkaz a metriku portálu Azure](set-throughput.md#GetLastRequestStatistics).
* Zjistěte, jak [konfigurace pro čtení předvolby pro globálně distribuované aplikace](../cosmos-db/tutorial-global-distribution-mongodb.md).
