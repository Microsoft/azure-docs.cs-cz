---
title: Úvod do rozhraní API Azure Cosmos DB pro MongoDB
description: Naučte se, jak můžete použít Azure Cosmos DB k ukládání a dotazování obrovských objemů dat pomocí rozhraní API Azure Cosmos DB pro MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 10/1/2019
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 5f4a90859831e6134530b94dfa80c11eb740301a
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2020
ms.locfileid: "85113309"
---
# <a name="azure-cosmos-dbs-api-for-mongodb"></a>Rozhraní API služby Azure Cosmos DB pro MongoDB

[Azure Cosmos DB](introduction.md) je databázová služba Microsoftu s více modely pro klíčové aplikace použitelná v celosvětovém měřítku. Azure Cosmos DB poskytuje [globální distribuci na klíč](distribute-data-globally.md), [elastické škálování propustnosti a úložiště](partition-data.md) po celém světě, latence v řádu milisekund na 99. percentilu, zaručenou vysokou dostupnost. To vše je zajištěno [nejlepšími smlouvami SLA v oboru](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [automaticky indexuje data](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), aniž by vyžadovala zapojení správy schémat a indexů. Zahrnuje více modelů a podporuje modely dokumentů, klíčových hodnot, grafů a sloupcových dat. Ve výchozím nastavení můžete pracovat s Cosmos DB pomocí rozhraní SQL API. Kromě toho služba Cosmos DB implementuje přenosové protokoly pro společná rozhraní API NoSQL, včetně Cassandra, MongoDB, Gremlin a Azure Table Storage. Díky tomu můžete používat známé klientské ovladače a nástroje NoSQL k interakci s databází Cosmos.

## <a name="wire-protocol-compatibility"></a>Kompatibilita přenosových protokolů

Azure Cosmos DB implementuje přenosové protokoly běžných databází NoSQL, včetně Cassandra, MongoDB, Gremlin a úložiště tabulek Azure. Díky přímému a efektivnímu zajištění nativní implementace přenosových protokolů v Cosmos DB umožňuje existujícím klientským sadám SDK, ovladačům a nástrojům databáze NoSQL pracovat s Cosmos DB transparentně. Cosmos DB nepoužívá žádný zdrojový kód databází pro poskytování rozhraní API kompatibilních s drátem pro žádnou z databází NoSQL.

Ve výchozím nastavení jsou nové účty vytvořené pomocí rozhraní API Azure Cosmos DB pro MongoDB kompatibilní s verzí 3,6 protokolu MongoDB. Jakýkoli ovladač klienta MongoDB, který rozumí této verzi protokolu, by měl být schopný nativně se připojit k Cosmos DB.

:::image type="content" source="./media/mongodb-introduction/cosmosdb-mongodb.png" alt-text="Rozhraní API služby Azure Cosmos DB pro MongoDB" border="false":::

## <a name="key-benefits"></a>Klíčové výhody

[Tady](introduction.md)jsou popsány klíčové výhody Cosmos DB jako plně spravovaná globálně distribuovaná databáze jako služba. Kromě toho nativně implementací přenosových protokolů oblíbených rozhraní NoSQL Cosmos DB API přináší následující výhody:

* Snadná migrace aplikace do Cosmos DB a zachování významných částí logiky aplikace.
* Zajistěte, aby vaše aplikace byla přenosná a nadále zůstala dodavatel v cloudu – nezávislá.
* Získejte špičkovou a finančně zálohovanou SLA pro společná rozhraní NoSQL API založená na Cosmos DB.
* Elastickě Škálujte zřízenou propustnost a úložiště pro databáze Cosmos podle potřeby a platíte jenom za propustnost a úložiště, které potřebujete. To vede ke značným úsporám nákladů.
* Klíč, globální distribuce s replikací s více hlavními servery.

## <a name="cosmos-dbs-api-for-mongodb"></a>Rozhraní API pro MongoDB Cosmos DB

Pomocí rychlých startů vytvořte účet Azure Cosmos a migrujte existující aplikaci MongoDB pro použití Azure Cosmos DB nebo vytvořte novou:

* [Migrujte existující Node.js webovou aplikaci v MongoDB](create-mongodb-nodejs.md).
* [Sestavení webové aplikace pomocí rozhraní API Azure Cosmos DB pro MongoDB a .NET SDK](create-mongodb-dotnet.md)
* [Vytvoření konzolové aplikace pomocí rozhraní API Azure Cosmos DB pro MongoDB a Java SDK](create-mongodb-java.md)

## <a name="next-steps"></a>Další kroky

Tady jsou odkazy na informace, které vám pomůžou začít:

* Pomocí kurzu [připojení aplikace MongoDB k Azure Cosmos DB](connect-mongodb-account.md) se dozvíte, jak získat informace o připojovacím řetězci k účtu.
* Postupujte podle kurzu [použití studia 3T with Azure Cosmos DB](mongodb-mongochef.md) a Naučte se, jak vytvořit propojení mezi databází Cosmos a aplikací MongoDB v studiu 3T.
* Postupujte podle kurzu [Import MongoDB data do Azure Cosmos DB](mongodb-migrate.md) a importujte data do databáze Cosmos.
* Připojte se k účtu Cosmos pomocí [Robo 3T](mongodb-robomongo.md).
* Naučte se [konfigurovat předvolby pro čtení globálně distribuovaných aplikací](../cosmos-db/tutorial-global-distribution-mongodb.md).

<sup>Poznámka: Tento článek popisuje funkci Azure Cosmos DB, která poskytuje kompatibilitu s protokolem sítě s databázemi MongoDB. Společnost Microsoft nespouští MongoDB databáze pro poskytování této služby. K Azure Cosmos DB není přidružena společnost MongoDB, Inc.</sup>
