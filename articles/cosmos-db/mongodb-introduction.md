---
title: Úvod do služby Azure Cosmos DB pro rozhraní MongoDB API
description: Zjistěte, jak můžete používat službu Azure Cosmos DB k ukládání a dotazování velkého množství dokumentů JSON s nízkou latencí pomocí oblíbených rozhraní API OSS MongoDB.
keywords: Rozhraní API služby Azure Cosmos DB pro MongoDB
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.topic: overview
ms.date: 12/19/2018
ms.author: sclyon
ms.openlocfilehash: fc6c9567073bbdfa77d753b9b274f7ed5e901405
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53720560"
---
# <a name="azure-cosmos-db-for-mongodb-api-clients"></a>Azure Cosmos DB pro rozhraní MongoDB API klienty

[Azure Cosmos DB](introduction.md) je globálně distribuovaná databázová služba Microsoftu s více modely pro klíčové aplikace. Azure Cosmos DB poskytuje [globální distribuci na klíč](distribute-data-globally.md), [elastické škálování propustnosti a úložiště](partition-data.md) po celém světě, latence v řádu milisekund na 99. percentilu, zaručenou vysokou dostupnost. To vše je zajištěno [nejlepšími smlouvami SLA v oboru](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [automaticky indexuje data](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), aniž by vyžadovala zapojení správy schémat a indexů. Zahrnuje více modelů a podporuje modely dokumentů, klíčových hodnot, grafů a sloupcových dat. Ve výchozím nastavení budete moct setkat s Cosmos DB pomocí rozhraní SQL API. Kromě toho služby Cosmos DB implementuje protokoly přenosu pro běžné rozhraní API typu NoSQL, včetně Cassandra, MongoDB, Gremlin a Azure Table Storage. To umožňuje používat známé ovladače klienta NoSQL a nástroje k interakci s databází Cosmos.

## <a name="wire-protocol-compatibility"></a>Přenosový protokol kompatibility

Cosmos DB implementuje přenosový protokoly common databáze NoSQL, včetně Cassandra, MongoDB, Gremlin a Azure Table Storage. Tím, že poskytuje nativní implementace protokolů přenosu efektivně a přímo ve službě Azure Cosmos, je možné existující klientské sady SDK, ovladačů a nástrojů z databáze NoSQL pro interakci s Cosmos DB transparentně. Cosmos DB nepoužívá žádný zdrojový kód databází k zajištění přenosu kompatibilní s rozhraním API pro žádnou z databází NoSQL.

Ve výchozím nastavení je kompatibilní s verze 3.2 přenosový protokol MongoDB API služby Azure Cosmos DB. Funkce nebo operátory dotazu byly přidány ve verzi 3.4 přenosový protokol jsou nyní dostupné jako funkce ve verzi preview. Všechny ovladače klienta, která analyzuje tyto verze protokolu pro MongoDB API by měl být nativně připojit ke službě Azure Cosmos DB.

![Rozhraní API služby Azure Cosmos DB pro MongoDB](./media/mongodb-introduction/cosmosdb-mongodb.png) 

## <a name="key-benefits"></a>Klíčové výhody

Klíčové výhody jako plně spravovaná, globálně distribuovaná databáze jako služba Cosmos DB jsou popsány [tady](introduction.md). Kromě toho implementací nativně při přenosu protokolů z oblíbených rozhraní API typu NoSQL, Cosmos DB poskytuje následující výhody:

* Snadno migrujte své aplikace do služby Cosmos DB při zachování významné části vaší aplikace logiky.
* Zachovat aplikaci přenosné a nadále zůstávají nezávislá na cloud dodavatele.
* Získání oboru přední a finančně zajištěné smlouvy o úrovni služeb pro společné rozhraní API NoSQL s využitím Cosmos DB.
* Elastické škálování propustnosti a úložiště pro vaše databáze Cosmos podle svých potřeb a Plaťte jenom za propustnost a úložiště, které potřebujete. To vede k výrazné úspory nákladů.
* Na klíč, globální distribuci s multimasterovou replikací.

## <a name="cosmos-db-for-mongodb-api-clients"></a>Cosmos DB pro rozhraní MongoDB API klienty

Pomocí rychlých startů MongoDB vytvořte účet Azure Cosmos DB a migrujte svou existující aplikaci MongoDB tak, aby používala Azure Cosmos DB, nebo vytvořte novou:

* [Migrace existující webové aplikace MongoDB v jazyce Node.js](create-mongodb-nodejs.md)
* [Vytvoření webové aplikace s využitím .NET a webu Azure portal pomocí služby Azure Cosmos DB pro rozhraní MongoDB API](create-mongodb-dotnet.md)
* [Sestavení aplikace konzoly v jazyce Java a webu Azure portal pomocí služby Azure Cosmos DB pro rozhraní MongoDB API](create-mongodb-java.md)

## <a name="next-steps"></a>Další postup

Tady jsou odkazy na informace, které vám pomůžou začít:

* V kurzu o [připojení k účtu MongoDB](connect-mongodb-account.md) se dozvíte, jak získat informace o připojovacím řetězci účtu.
* V kurzu o [použití Studio 3T (MongoChef) se službou Azure Cosmos DB](mongodb-mongochef.md) zjistíte, jak vytvořit propojení mezi databází Azure Cosmos DB a aplikací MongoDB ve Studio 3T.
* Postupujte podle [podporují migrace dat do služby Azure Cosmos DB pomocí protokolu pro MongoDB API](mongodb-migrate.md) kurzu a importovat data do databáze Cosmos.
* Připojte se k účtu Cosmos pomocí [Robomongo](mongodb-robomongo.md).
* Zjistíte, jak [nakonfigurovat předvolby čtení pro globálně distribuované aplikace](../cosmos-db/tutorial-global-distribution-mongodb.md).

<sup>Poznámka: Tento článek popisuje funkce služby Azure Cosmos DB, která poskytuje kompatibilitu s protokolem při přenosu pomocí databáze MongoDB. Microsoft se nespustí databáze MongoDB pro tuto službu poskytovat. Azure Cosmos DB není přidružený žádný MongoDB, Inc.</sup>
