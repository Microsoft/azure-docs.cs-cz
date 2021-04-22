---
title: Úvod do rozhraní API Azure Cosmos DB pro MongoDB
description: Naučte se, jak můžete použít Azure Cosmos DB k ukládání a dotazování obrovských objemů dat pomocí rozhraní API Azure Cosmos DB pro MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 04/21/2021
author: sivethe
ms.author: sivethe
adobe-target: true
adobe-target-activity: DocsExp– 396298–A/B–Docs–IntroToCosmosDBAPIforMongoDB-Revamp–FY21Q4
adobe-target-experience: Experience B
adobe-target-content: ./mongodb-introduction-experiment
ms.openlocfilehash: 518eaadf75a5ff2cabc541586fcdf029b0ca1c60
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107887085"
---
# <a name="azure-cosmos-dbs-api-for-mongodb"></a>Rozhraní API služby Azure Cosmos DB pro MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

[Azure Cosmos DB](introduction.md) je databázová služba Microsoftu s více modely pro klíčové aplikace použitelná v celosvětovém měřítku. Azure Cosmos DB poskytuje [globální distribuci na klíč](distribute-data-globally.md), [elastické škálování propustnosti a úložiště](partitioning-overview.md) po celém světě, latence v řádu milisekund na 99. percentilu, zaručenou vysokou dostupnost. To vše je zajištěno [nejlepšími smlouvami SLA v oboru](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [automaticky indexuje data](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), aniž by vyžadovala zapojení správy schémat a indexů. Zahrnuje více modelů a podporuje modely dokumentů, klíčových hodnot, grafů a sloupcových dat. Služba Azure Cosmos DB implementuje přenosové protokoly pro společná rozhraní API NoSQL, včetně Cassandra, MongoDB, Gremlin a Azure Table Storage. Díky tomu můžete používat známé klientské ovladače a nástroje NoSQL k interakci s databází Cosmos.

> [!NOTE]
> [Režim kapacity bez serveru](serverless.md) je teď k dispozici v rozhraní Azure Cosmos DB API pro MongoDB.

## <a name="wire-protocol-compatibility"></a>Kompatibilita přenosových protokolů

Azure Cosmos DB implementuje přenosový protokol pro MongoDB. Tato implementace umožňuje transparentní kompatibilitu s nativními sadami SDK, ovladači a nástroji klienta MongoDB. Azure Cosmos DB nehostuje databázový stroj MongoDB. Podrobnosti o podporovaných funkcích podle MongoDB najdete tady: 
- [Rozhraní API pro Mongo DB verze 4,0 Azure Cosmos DB](mongodb-feature-support-40.md)
- [Rozhraní API pro Mongo DB verze 3,6 Azure Cosmos DB](mongodb-feature-support-36.md)

Ve výchozím nastavení jsou nové účty vytvořené pomocí rozhraní API Azure Cosmos DB pro MongoDB kompatibilní s verzí 3,6 protokolu MongoDB. Jakýkoli ovladač klienta MongoDB, který rozumí této verzi protokolu, by měl být schopný nativně se připojit k Cosmos DB.

:::image type="content" source="./media/mongodb-introduction/cosmosdb-mongodb.png" alt-text="Rozhraní API služby Azure Cosmos DB pro MongoDB" border="false":::

## <a name="key-benefits"></a>Klíčové výhody

[Tady](introduction.md)jsou popsány klíčové výhody Cosmos DB jako plně spravovaná globálně distribuovaná databáze jako služba. Kromě toho nativně implementací přenosových protokolů oblíbených rozhraní NoSQL Cosmos DB API přináší následující výhody:

* Snadná migrace aplikace do Cosmos DB a zachování významných částí logiky aplikace.
* Zajistěte, aby vaše aplikace byla přenosná a nadále zůstala dodavatel v cloudu – nezávislá.
* Získejte špičkovou a finančně zálohovanou SLA pro společná rozhraní NoSQL API založená na Cosmos DB.
* Elastickě Škálujte zřízenou propustnost a úložiště pro databáze Cosmos podle potřeby a platíte jenom za propustnost a úložiště, které potřebujete. To vede ke značným úsporám nákladů.
* Klíč globální distribuce s mezioblastí zapisuje replikaci.

## <a name="cosmos-dbs-api-for-mongodb"></a>Rozhraní API pro MongoDB Cosmos DB

Pomocí rychlých startů vytvořte účet Azure Cosmos a migrujte existující aplikaci MongoDB pro použití Azure Cosmos DB nebo vytvořte novou:

* [Migrujte existující Node.js webovou aplikaci v MongoDB](create-mongodb-nodejs.md).
* [Sestavení webové aplikace pomocí rozhraní API Azure Cosmos DB pro MongoDB a .NET SDK](create-mongodb-dotnet.md)
* [Vytvoření konzolové aplikace pomocí rozhraní API Azure Cosmos DB pro MongoDB a Java SDK](create-mongodb-java.md)

## <a name="next-steps"></a>Další kroky

Tady jsou odkazy na informace, které vám pomůžou začít:

* Pomocí kurzu [připojení aplikace MongoDB k Azure Cosmos DB](connect-mongodb-account.md) se dozvíte, jak získat informace o připojovacím řetězci k účtu.
* Postupujte podle kurzu [použití studia 3T with Azure Cosmos DB](mongodb-mongochef.md) a Naučte se, jak vytvořit propojení mezi databází Cosmos a aplikací MongoDB v studiu 3T.
* Postupujte podle kurzu [Import MongoDB data do Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json) a importujte data do databáze Cosmos.
* Připojte se k účtu Cosmos pomocí [Robo 3T](mongodb-robomongo.md).
* Naučte se [konfigurovat předvolby pro čtení globálně distribuovaných aplikací](../cosmos-db/tutorial-global-distribution-mongodb.md).
* V našem [Průvodci odstraňováním potíží](mongodb-troubleshoot.md) najdete řešení pro běžně nalezené chyby.


<sup>Poznámka: Tento článek popisuje funkci Azure Cosmos DB, která poskytuje kompatibilitu s protokolem sítě s databázemi MongoDB. Společnost Microsoft nespouští MongoDB databáze pro poskytování této služby. K Azure Cosmos DB není přidružena společnost MongoDB, Inc.</sup>
