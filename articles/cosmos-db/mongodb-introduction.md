---
title: Úvod do rozhraní API Azure Cosmos DB pro MongoDB
description: Zjistěte, jak můžete pomocí Azure Cosmos DB ukládat a dotazovat obrovské množství dat pomocí rozhraní API Azure Cosmos DB pro MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 10/1/2019
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 670020d276b9d4a868f24eb4a3f522581060adca
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "72754980"
---
# <a name="azure-cosmos-dbs-api-for-mongodb"></a>Rozhraní API služby Azure Cosmos DB pro MongoDB

[Azure Cosmos DB](introduction.md) je databázová služba Microsoftu s více modely pro klíčové aplikace použitelná v celosvětovém měřítku. Azure Cosmos DB poskytuje [globální distribuci na klíč](distribute-data-globally.md), [elastické škálování propustnosti a úložiště](partition-data.md) po celém světě, latence v řádu milisekund na 99. percentilu, zaručenou vysokou dostupnost. To vše je zajištěno [nejlepšími smlouvami SLA v oboru](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [automaticky indexuje data](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), aniž by vyžadovala zapojení správy schémat a indexů. Zahrnuje více modelů a podporuje modely dokumentů, klíčových hodnot, grafů a sloupcových dat. Ve výchozím nastavení můžete pracovat s Cosmos DB pomocí rozhraní SQL API. Kromě toho služba Cosmos DB implementuje drátové protokoly pro běžná rozhraní API NoSQL včetně Cassandra, MongoDB, Gremlin a Azure Table Storage. To vám umožní používat známé noSQL klientské ovladače a nástroje pro interakci s databází Cosmos.

## <a name="wire-protocol-compatibility"></a>Kompatibilita drátového protokolu

Azure Cosmos DB implementuje drátové protokoly běžných databází NoSQL, včetně Cassandra, MongoDB, Gremlin a Azure Tables Storage. Tím, že poskytuje nativní implementaci drátových protokolů přímo a efektivně uvnitř Cosmos DB, umožňuje existujícím klientským sadám SDK, ovladačům a nástrojům databází NoSQL transparentní interakci s Cosmos DB. Cosmos DB nepoužívá žádný zdrojový kód databází pro poskytování rozhraní API kompatibilní s drátem pro některou z databází NoSQL.

Ve výchozím nastavení jsou nové účty vytvořené pomocí rozhraní API Azure Cosmos DB pro MongoDB kompatibilní s verzí 3.6 drátového protokolu MongoDB. Každý ovladač klienta MongoDB, který rozumí této verzi protokolu by měl být schopen nativně připojit k Cosmos DB.

![Rozhraní API služby Azure Cosmos DB pro MongoDB](./media/mongodb-introduction/cosmosdb-mongodb.png)

## <a name="key-benefits"></a>Klíčové výhody

Hlavní výhody Cosmos DB jako plně spravované, globálně distribuované databáze jako služby jsou popsány [zde](introduction.md). Navíc nativně implementací drátových protokolů populárních nosql API Cosmos DB poskytuje následující výhody:

* Snadno migrujte aplikaci do Cosmos DB při zachování významné části aplikační logiky.
* Udržujte aplikaci přenosnou a nadále zůstat cloud dodavatele matnostik.
* Získejte vedoucí a finančně podporované sla pro běžná api NoSQL využívající Cosmos DB.
* Elasticky škálujte zřízenou propustnost a úložiště pro vaše databáze Cosmos na základě vašich potřeb a plaťte jenom za propustnost a úložiště, které potřebujete. To vede k významným úsporám nákladů.
* Na klíč, globální distribuce s replikací více hlavních serverů.

## <a name="cosmos-dbs-api-for-mongodb"></a>Cosmos DB API pro MongoDB

Podle rychlých startů vytvořte účet Azure Cosmos a migrujte stávající aplikaci MongoDB, abyste použili Azure Cosmos DB, nebo vytvořte novou:

* [Migrujte existující webovou aplikaci MongoDB Node.js](create-mongodb-nodejs.md).
* [Vytvoření webové aplikace pomocí rozhraní API Azure Cosmos DB pro MongoDB a .NET SDK](create-mongodb-dotnet.md)
* [Vytvoření konzolové aplikace pomocí rozhraní API Azure Cosmos DB pro MongoDB a Java SDK](create-mongodb-java.md)

## <a name="next-steps"></a>Další kroky

Tady jsou odkazy na informace, které vám pomůžou začít:

* Postupujte podle [kurzu Připojit aplikaci MongoDB k Azure Cosmos DB](connect-mongodb-account.md) a zjistěte, jak získat informace o řetězci připojení účtu.
* Postupujte podle [kurzu Use Studio 3T s Azure Cosmos DB](mongodb-mongochef.md) a zjistěte, jak vytvořit připojení mezi databází Cosmos a aplikací MongoDB ve Studiu 3T.
* Postupujte podle [importu dat MongoDB do](mongodb-migrate.md) kurzu Azure Cosmos DB a importujte data do databáze Cosmos.
* Připojte se k účtu Cosmos pomocí [Robo 3T](mongodb-robomongo.md).
* Přečtěte [si, jak nakonfigurovat předvolby čtení pro globálně distribuované aplikace](../cosmos-db/tutorial-global-distribution-mongodb.md).

<sup>Poznámka: Tento článek popisuje funkci Azure Cosmos DB, která poskytuje kompatibilitu drátového protokolu s databázemi MongoDB. Společnost Microsoft nespouštěla databáze MongoDB k poskytování této služby. Azure Cosmos DB není přidružená k MongoDB, Inc.</sup>
