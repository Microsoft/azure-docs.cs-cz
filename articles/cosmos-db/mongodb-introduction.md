---
title: Úvod do služby Azure Cosmos DB přes rozhraní API pro MongoDB
description: Zjistěte, jak můžete používat službu Azure Cosmos DB k ukládání a dotazování velkého množství dokumentů JSON s nízkou latencí pomocí oblíbených OSS MongoDB.
keywords: Co je MongoDB
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.topic: overview
ms.date: 02/12/2018
ms.author: sclyon
ms.openlocfilehash: c4c33094f070e603057042eaea8187e29270fb77
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53606839"
---
# <a name="introduction-to-azure-cosmos-db-api-for-mongodb"></a>Úvod do služby Azure Cosmos DB přes rozhraní API pro MongoDB

[Azure Cosmos DB](../cosmos-db/introduction.md) je globálně distribuovaná databázová služba Microsoftu s více modely pro klíčové aplikace. Azure Cosmos DB poskytuje [globální distribuci na klíč](distribute-data-globally.md), [elastické škálování propustnosti a úložiště](partition-data.md) po celém světě, latence v řádu milisekund na 99. percentilu, zaručenou vysokou dostupnost. To vše je zajištěno [nejlepšími smlouvami SLA v oboru](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [automaticky indexuje data](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), aniž by vyžadovala zapojení správy schémat a indexů. Zahrnuje více modelů a podporuje modely dokumentů, klíčových hodnot, grafů a sloupcových dat. 

![Rozhraní API služby Azure Cosmos DB pro MongoDB](./media/mongodb-introduction/cosmosdb-mongodb.png) 

Databáze Azure Cosmos DB lze použít jako úložiště dat aplikací psaných pro [MongoDB](https://docs.mongodb.com/manual/introduction/). Tato funkce znamená, že s využitím existujících [ovladačů](https://docs.mongodb.org/ecosystem/drivers/) teď aplikace psané pro MongoDB mohou komunikovat se službou Azure Cosmos DB a používat databáze Azure Cosmos DB místo databází MongoDB. V mnoha případech můžete přejít od používání MongoDB na Azure Cosmos DB jednoduchou změnou připojovacího řetězce. Díky tomu můžete jednoduše vytvářet a provozovat globálně distribuované databázové aplikace MongoDB v cloudu Azure se službou Azure Cosmos DB a jejími [nejlepšími smlouvami SLA v oboru](https://azure.microsoft.com/support/legal/sla/cosmos-db) a zároveň dál používat známé dovednosti a nástroje pro MongoDB.

**Kompatibilita MongoDB**: Jak služby Azure Cosmos DB implementuje přenosový protokol MongoDB můžete použít stávající odborné znalosti, aplikace kódu a nástroje pro MongoDB. Můžete vyvíjet aplikace využívající MongoDB a nasazovat je do produkce pomocí plně spravované a globálně distribuované služby Azure Cosmos DB. Další informace o podporovaných verzích najdete v tématu popisujícím [podporu protokolu MongoDB](mongodb-feature-support.md#mongodb-protocol-support).

Azure Cosmos DB API pro MongoDB nelze použít jako direct koncový bod pro služby, jako je Azure Stream Analytics, protože it používá stejný [ovladače klienta](https://docs.mongodb.org/ecosystem/drivers/) jako nativní MongoDB. Chcete-li integrovat se službou Azure Stream Analytics, zvažte použití [služby Azure App Service](../app-service/overview.md) nebo [služba Azure Functions](../azure-functions/functions-overview.md) jako služba middlewaru, který umožňuje zápis dat do Azure Cosmos DB API pro MongoDB.

## <a name="what-is-the-benefit-of-using-azure-cosmos-db-for-mongodb-applications"></a>Jaké přínosy má používání služby Azure Cosmos DB pro aplikace MongoDB?

**Elasticky škálovatelná propustnost a úložiště:** Splňte své aplikace díky snadné škálování směrem nahoru nebo dolů vaše databáze MongoDB. Data se ukládají na discích SSD (solid-state drive), které nabízí nízkou a předvídatelnou latenci. Azure Cosmos DB podporuje kolekce MongoDB, které lze škálovat na prakticky neomezenou velikost úložiště a zřízenou propustnost. S růstem vaší aplikace je možné službu Azure Cosmos DB bezproblémově elasticky škálovat s předvídatelným výkonem. 

**Replikace ve více oblastech:** Azure Cosmos DB transparentně replikuje data do všech oblastí, které jste přidružili k účtu MongoDB umožňuje vyvíjet aplikace, které vyžadují globální přístup k datům a zároveň poskytují kompromisy mezi konzistencí, dostupností a výkonem , vše s odpovídajícími zárukami. Azure Cosmos DB poskytuje transparentní regionální převzetí služeb při selhání se souběžnou instalací rozhraní API a schopností elastického škálování propustnosti a úložiště po celém světě. Další informace najdete v části [Globální distribuce dat](distribute-data-globally.md).

**Není potřeba Správa serveru**: Není nutné spravovat a škálovat databáze MongoDB. Azure Cosmos DB je plně spravovaná služba, což znamená, že se sami nemusíte starat o žádnou infrastrukturu virtuálních počítačů. Služba Azure Cosmos DB je dostupná ve více než 30 [oblastech Azure](https://azure.microsoft.com/regions/services/).

**Přizpůsobitelných úrovní konzistence:** Vzhledem k tomu, že Azure Cosmos DB podporuje rozhraní API pro více modelů konzistence nastavení se vztahují na úrovni účtu a vynucení konzistence se řídí každé rozhraní API. Až do MongoDB 3.6 nebyl koncept konzistence typu relace, tak když nastavíte účet rozhraní API služby Azure Cosmos DB pro MongoDB pomocí konzistence typu relace, konzistence je při použití rozhraní API služby Azure Cosmos DB pro MongoDB downgradovat na konečný výsledek. Pokud potřebujete se zárukou your vlastní w pro rozhraní API Azure Cosmos DB pro MongoDB, měli byste nastavit výchozí úroveň konzistence účtu pro silné nebo omezená neaktuálnost. Další informace najdete v tématu popisujícím [využití úrovní konzistence pro maximalizaci dostupnosti a výkonu](consistency-levels.md).

| Výchozí úroveň konzistence pro Azure Cosmos DB |   Rozhraní Mongo API (3.4) |
|---|---|
|Nahodilé| Nahodilé |
|Konzistentní předpona| Nahodilá s konzistentním pořadím |
|Relace| Nahodilá s konzistentním pořadím |
|Omezená neaktuálnost| Silné |
| Silné | Silné |

**Automatické indexování**: Ve výchozím nastavení služby Azure Cosmos DB automaticky indexuje všechny vlastnosti v rámci dokumenty v databázi MongoDB a neočekává ani nevyžaduje žádné schéma nebo vytváření sekundárních indexů. Schopnost jedinečného indexu navíc umožňuje omezení jedinečnosti jakýchkoli polí dokumentů, které už jsou automaticky indexované ve službě Azure Cosmos DB.

**Podnikové**: Azure Cosmos DB podporuje více místních replik poskytovat 99,99 % dostupnost a ochranu dat i v případě místního a regionálního selhání. Azure Cosmos DB má [osvědčení pro dodržování předpisů](https://www.microsoft.com/trustcenter) a funkce zabezpečení na podnikové úrovni. 

## <a name="how-to-get-started"></a>Jak začít

Pomocí rychlých startů MongoDB vytvořte účet Azure Cosmos DB a migrujte svou existující aplikaci MongoDB tak, aby používala Azure Cosmos DB, nebo vytvořte novou:

* [Migrace existující webové aplikace MongoDB v jazyce Node.js](create-mongodb-nodejs.md)
* [Vytvoření webové aplikace s využitím .NET a webu Azure portal pomocí rozhraní API služby Azure Cosmos DB pro MongoDB](create-mongodb-dotnet.md)
* [Sestavení aplikace konzoly v jazyce Java a webu Azure portal pomocí rozhraní API služby Azure Cosmos DB pro MongoDB](create-mongodb-java.md)

## <a name="next-steps"></a>Další postup

Informace o rozhraní API Azure Cosmos DB pro MongoDB je integrovaná do služby Azure Cosmos DB dokumentace se celkově, ale tady je pár ukazatele, které vám pomůžou začít:

* V kurzu o [připojení k účtu MongoDB](connect-mongodb-account.md) se dozvíte, jak získat informace o připojovacím řetězci účtu.
* V kurzu o [použití Studio 3T (MongoChef) se službou Azure Cosmos DB](mongodb-mongochef.md) zjistíte, jak vytvořit propojení mezi databází Azure Cosmos DB a aplikací MongoDB ve Studio 3T.
* V kurzu o [migraci dat do Azure Cosmos DB pomocí podpory protokolů pro MongoDB](mongodb-migrate.md) naimportujete svá data do rozhraní API databáze MongoDB.
* Pomocí [Robomongo](mongodb-robomongo.md) se připojíte k rozhraní API účtu MongoDB.
* Zjistíte, jak [nakonfigurovat předvolby čtení pro globálně distribuované aplikace](../cosmos-db/tutorial-global-distribution-mongodb.md).
