---
title: Kroky před migrací pro migraci dat do rozhraní API Azure Cosmos DB pro MongoDB
description: Tento dokument poskytuje přehled požadavků na migraci dat z MongoDB do Cosmos DB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: roaror
ms.openlocfilehash: 4dc7038d0ff5180f15a43268fd3f3aa0cbb0c7a0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445199"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Kroky před migrací pro migraci dat z MongoDB Azure Cosmos DB do rozhraní API pro MongoDB

Před migrací dat z MongoDB (místně nebo v cloudu (IaaS)) pro Azure Cosmos DB rozhraní API pro MongoDB byste měli:

1. [Vytvoření účtu služby Azure Cosmos DB](#create-account)
2. [Odhad propustnosti potřebných pro vaše úlohy](#estimate-throughput)
3. [Vyberte pro svá data optimální klíč oddílu.](#partitioning)
4. [Pochopení zásad indexování, které můžete nastavit pro vaše data](#indexing)

Pokud jste už dokončili výše uvedené požadavky na migraci, přečtěte si téma [migrace dat MongoDB Azure Cosmos DB a rozhraní API pro MongoDB](../dms/tutorial-mongodb-cosmos-db.md) pro skutečné pokyny k migraci dat. V takovém případě tento dokument poskytuje pokyny pro zpracování těchto požadavků. 

## <a id="create-account"></a>Vytvoření účtu Azure Cosmos DB 

Před zahájením migrace je potřeba [vytvořit účet Azure Cosmos pomocí rozhraní API služby Azure Cosmos DB pro MongoDB](create-mongodb-dotnet.md). 

Při vytváření účtu můžete zvolit nastavení pro [globální distribuci](distribute-data-globally.md) dat. Máte také možnost Povolit zápisy ve více oblastech (nebo konfiguraci s více hlavními servery), která umožňuje, aby každá z vašich oblastí byla v oblasti zápisu i čtení.

![Vytváření účtů](./media/mongodb-pre-migration/account-creation.png)

## <a id="estimate-throughput"></a>Odhad propustnosti pro vaše úlohy

Než začnete s migrací pomocí [Database Migration Service (DMS)](../dms/dms-overview.md), měli byste odhadnout množství propustnosti, která se má zřídit pro databáze a kolekce Azure Cosmos.

Propustnost lze zřídit buď:

- Kolekce

- databáze

> [!NOTE]
> Můžete mít také kombinaci výše uvedeného, kde některé kolekce v databázi můžou mít vyhrazenou zřízenou propustnost a jiné můžou propustnost sdílet. Podrobnosti najdete v tématu [nastavení propustnosti pro databázi a stránku kontejneru](set-throughput.md) .
>

Nejdřív se rozhodněte, jestli chcete zřídit propustnost databáze nebo na úrovni kolekce, nebo kombinaci obou. Obecně se doporučuje nakonfigurovat vyhrazenou propustnost na úrovni kolekce. Zajištění propustnosti na úrovni databáze umožňuje kolekcím ve vaší databázi sdílet zřízenou propustnost. U sdílené propustnosti ale neexistuje žádná záruka na konkrétní propustnost pro každou jednotlivou kolekci a nedosáhnete předvídatelného výkonu u žádné konkrétní kolekce.

Pokud si nejste jistí, kolik propustnosti by mělo být vyhrazeno pro každou jednotlivou kolekci, můžete zvolit propustnost na úrovni databáze. Zřízenou propustnost nakonfigurovanou v databázi Azure Cosmos si můžete představit jako logický ekvivalent k výpočetní kapacitě virtuálního počítače MongoDB nebo fyzického serveru, ale s možností elastického škálování. Další informace najdete v tématu [zřízení propustnosti v kontejnerech a databázích Azure Cosmos](set-throughput.md).

Pokud zřizujete propustnost na úrovni databáze, musí být všechny kolekce vytvořené v této databázi vytvořeny s použitím oddílu partition/horizontálních oddílů. Další informace o dělení najdete v tématu [dělení a horizontální škálování v Azure Cosmos DB](partition-data.md). Pokud během migrace nezadáte oddíl/horizontálních oddílů klíč, Azure Database Migration Service automaticky vyplní pole horizontálních oddílů Key pomocí atributu *_ID* , který se automaticky vygeneroval pro každý dokument.

### <a name="optimal-number-of-request-units-rus-to-provision"></a>Optimální počet jednotek žádostí (ru), které se mají zřídit

V Azure Cosmos DB propustnost je zajištěná předem a měří se v jednotkách žádosti (RU) za sekundu. Pokud máte úlohy, které používají MongoDB na virtuálním počítači nebo v místním prostředí, vezměte RU jako jednoduchou abstrakci pro fyzické prostředky, třeba pro velikost virtuálního počítače nebo na místní server a prostředky, které vlastní, například paměť, procesor, IOPs. 

Na rozdíl od virtuálních počítačů nebo místních serverů se ru snadno škáluje nahoru a dolů. Počet zřízených ru můžete změnit během několika sekund a účtuje se vám jenom maximální počet ru, které zřídíte po dobu jedné hodiny. Další informace najdete v tématu [jednotky žádostí v Azure Cosmos DB](request-units.md).

Níže jsou uvedené klíčové faktory, které ovlivňují počet požadovaných ru:
- **Položka (tj. dokument) velikost**: při zvyšování velikosti položky nebo dokumentu se zvýší i počet ru spotřebovaných na čtení nebo zápis položky nebo dokumentu.
- **Počet vlastností položky**: za předpokladu, že [Výchozí indexování](index-overview.md) u všech vlastností se zvyšuje počet ru spotřebovaných na zápis položky při zvyšování počtu vlastností položky. [Omezením počtu indexovaných vlastností](index-policy.md)můžete snížit spotřebu jednotek požadavků pro operace zápisu.
- **Souběžné operace**: spotřebované jednotky žádosti také závisejí na četnosti, se kterou se spouštějí různé operace CRUD (například zápisy, čtení, aktualizace, odstranění) a složitější dotazy. [Mongostat](https://docs.mongodb.com/manual/reference/program/mongostat/) můžete použít k výstupu požadavků na souběžnost vašich aktuálních dat MongoDB.
- **Vzorce dotazů**: složitost dotazu ovlivňuje počet jednotek požadavků, které dotaz spotřebovává.

Pokud exportujete soubory JSON pomocí [mongoexport](https://docs.mongodb.com/manual/reference/program/mongoexport/) a rozumíte tomu, kolik operací zápisu, čtení, aktualizace a odstranění probíhají za sekundu, můžete pomocí [Azure Cosmos DB kapacity plánovače](https://www.documentdb.com/capacityplanner) odhadnout počáteční počet ru, které se mají zřídit. Plánovač kapacity nemá v ceně složitějších dotazů faktor. Takže pokud máte vaše data složitých dotazů, budou se používat další ru. Kalkulačka také předpokládá, že všechna pole jsou indexována, a je použita konzistence relace. Nejlepším způsobem, jak porozumět nákladům na dotazy, je migrovat data (nebo ukázková data) na Azure Cosmos DB, [připojit se ke koncovému bodu Cosmos DB](connect-mongodb-account.md) a spustit vzorový dotaz z prostředí MongoDB pomocí příkazu `getLastRequestStastistics` a získat poplatek za požadavek, který vypíše počet spotřebovaných ru:

`db.runCommand({getLastRequestStatistics: 1})`

Tento příkaz vytvoří výstup dokumentu JSON podobný následujícímu:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

Až porozumíte počtu ru spotřebovaných dotazem a potřebám souběžnosti pro tento dotaz, můžete upravit počet zřízené ru. Optimalizace ru není jednorázová událost – měli byste průběžně optimalizovat nebo škálovat ru zřízené v závislosti na tom, jestli neočekáváte velký objem provozu, a to na rozdíl od vysoké zátěže nebo importu dat.

## <a id="partitioning"></a>Zvolit klíč oddílu
Vytváření oddílů je klíčovým bodem, který je potřeba zvážit před migrací na globálně distribuovanou databázi, jako je Azure Cosmos DB. Azure Cosmos DB používá dělení ke škálování jednotlivých kontejnerů v databázi, aby splňovala požadavky na škálovatelnost a výkon vaší aplikace. Při dělení jsou položky v kontejneru rozděleny do samostatných dílčích množin nazývaných logické oddíly. Podrobnosti a doporučení pro výběr správného klíče oddílu pro vaše data najdete v [části Výběr klíče oddílu](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). 

## <a id="indexing"></a>Indexace dat
Ve výchozím nastavení Azure Cosmos DB indexuje všechna datová pole po ingestování. [Zásady indexování](index-policy.md) v Azure Cosmos DB můžete kdykoli upravit. Při migraci dat se často doporučuje vypnout indexování a pak je znovu zapnout, když jsou data v Cosmos DB. Další informace o indexování najdete v části věnované [indexování v Azure Cosmos DB](index-overview.md) . 

[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) automaticky migruje kolekce MongoDB s jedinečnými indexy. Jedinečné indexy ale musí být vytvořené před migrací. Azure Cosmos DB nepodporuje vytváření jedinečných indexů, pokud v kolekcích již existují data. Další informace najdete v tématu [jedinečné klíče v Azure Cosmos DB](unique-keys.md).

## <a name="next-steps"></a>Další kroky
* [Migrujte data MongoDB na Cosmos DB pomocí Database Migration Service.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Zajištění propustnosti v kontejnerech a databázích Azure Cosmos](set-throughput.md)
* [Dělení ve službě Azure Cosmos DB](partition-data.md)
* [Globální distribuce v Azure Cosmos DB](distribute-data-globally.md)
* [Indexování v Azure Cosmos DB](index-overview.md)
* [Jednotky žádostí ve službě Azure Cosmos DB](request-units.md)
