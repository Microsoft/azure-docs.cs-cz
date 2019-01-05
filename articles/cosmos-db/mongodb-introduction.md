---
title: Úvod do služby Azure Cosmos DB pro rozhraní API pro MongoDB
description: Zjistěte, jak můžete pomocí služby Azure Cosmos DB k ukládání a dotazování velkých objemů dat pomocí rozhraní API služby Azure Cosmos DB pro MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: d0f61afaba094a1e499a91f9937a31554438759e
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54042168"
---
# <a name="azure-cosmos-dbs-api-for-mongodb"></a>Rozhraní API služby Azure Cosmos DB pro MongoDB

[Azure Cosmos DB](introduction.md) je globálně distribuovaná databázová služba Microsoftu s více modely pro klíčové aplikace. Azure Cosmos DB poskytuje [globální distribuci na klíč](distribute-data-globally.md), [elastické škálování propustnosti a úložiště](partition-data.md) po celém světě, latence v řádu milisekund na 99. percentilu, zaručenou vysokou dostupnost. To vše je zajištěno [nejlepšími smlouvami SLA v oboru](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [automaticky indexuje data](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), aniž by vyžadovala zapojení správy schémat a indexů. Zahrnuje více modelů a podporuje modely dokumentů, klíčových hodnot, grafů a sloupcových dat. Ve výchozím nastavení budete moct setkat s Cosmos DB pomocí rozhraní SQL API. Kromě toho služby Cosmos DB implementuje protokoly přenosu pro běžné rozhraní API typu NoSQL, včetně Cassandra, MongoDB, Gremlin a Azure Table Storage. To umožňuje používat známé ovladače klienta NoSQL a nástroje k interakci s databází Cosmos.

## <a name="wire-protocol-compatibility"></a>Přenosový protokol kompatibility

Azure Cosmos DB implementuje přenosový protokoly common databáze NoSQL, včetně Cassandra, MongoDB, Gremlin a Azure Table Storage. Tím, že poskytuje nativní implementace protokolů přenosu efektivně a přímo ve službě Azure Cosmos, je možné existující klientské sady SDK, ovladačů a nástrojů z databáze NoSQL pro interakci s Cosmos DB transparentně. Cosmos DB nepoužívá žádný zdrojový kód databází k zajištění přenosu kompatibilní s rozhraním API pro žádnou z databází NoSQL.

Ve výchozím nastavení je kompatibilní s verzí 3.2 přenosový protokol MongoDB pro rozhraní API služby Azure Cosmos DB pro MongoDB. Funkce nebo operátory dotazu byly přidány ve verzi 3.4 přenosový protokol jsou nyní dostupné jako funkce ve verzi preview. Všechny ovladače klienta MongoDB, která analyzuje tyto verze protokolu by měl být nativně připojit ke službě Cosmos DB.

![Rozhraní API služby Azure Cosmos DB pro MongoDB](./media/mongodb-introduction/cosmosdb-mongodb.png) 

## <a name="key-benefits"></a>Klíčové výhody

Klíčové výhody jako plně spravovaná, globálně distribuovaná databáze jako služba Cosmos DB jsou popsány [tady](introduction.md). Kromě toho implementací nativně při přenosu protokolů z oblíbených rozhraní API typu NoSQL, Cosmos DB poskytuje následující výhody:

* Snadno migrujte své aplikace do služby Cosmos DB při zachování významné části vaší aplikace logiky.
* Zachovat aplikaci přenosné a nadále zůstávají nezávislá na cloud dodavatele.
* Získání oboru přední a finančně zajištěné smlouvy o úrovni služeb pro společné rozhraní API NoSQL s využitím Cosmos DB.
* Elastické škálování propustnosti a úložiště pro vaše databáze Cosmos podle svých potřeb a Plaťte jenom za propustnost a úložiště, které potřebujete. To vede k výrazné úspory nákladů.
* Na klíč, globální distribuci s multimasterovou replikací.

## <a name="cosmos-dbs-api-for-mongodb"></a>Rozhraní API služby cosmos DB pro MongoDB

Postupujte podle šablon rychlý start k vytvoření účtu služby Cosmos a migrovat stávající aplikace MongoDB pomocí služby Azure Cosmos DB nebo vytvářet nové:

* [Migrace stávající webové aplikace Node.js s MongoDB](create-mongodb-nodejs.md).
* [Vytvoření webové aplikace pomocí rozhraní API služby Azure Cosmos DB pro MongoDB a sady .NET SDK](create-mongodb-dotnet.md)
* [Sestavit aplikaci konzoly pomocí rozhraní API služby Azure Cosmos DB pro MongoDB a sady Java SDK](create-mongodb-java.md)

## <a name="next-steps"></a>Další postup

Tady jsou odkazy na informace, které vám pomůžou začít:

* Postupujte podle [připojení aplikace MongoDB ke službě Azure Cosmos DB](connect-mongodb-account.md) kurzu se dozvíte, jak získat informace o vašem účtu připojovací řetězec.
* Postupujte podle [použití studia 3T pomocí služby Azure Cosmos DB](mongodb-mongochef.md) kurzu se dozvíte, jak vytvořit připojení mezi vaší databáze Cosmos a aplikaci MongoDB v sadě Studio 3 T.
* Postupujte podle [data importovat MongoDB do služby Azure Cosmos DB](mongodb-migrate.md) kurzu a importovat data do databáze Cosmos.
* Připojte se k účtu Cosmos pomocí [Robo 3T](mongodb-robomongo.md).
* Zjistěte, jak [konfigurace načíst předvolby pro globálně distribuované aplikace](../cosmos-db/tutorial-global-distribution-mongodb.md).

<sup>Poznámka: Tento článek popisuje funkce služby Azure Cosmos DB, která poskytuje kompatibilitu s protokolem při přenosu pomocí databáze MongoDB. Microsoft se nespustí databáze MongoDB pro tuto službu poskytovat. Azure Cosmos DB není přidružený žádný MongoDB, Inc.</sup>
