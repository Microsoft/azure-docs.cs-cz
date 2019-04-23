---
title: Před migrací postup pro migraci dat z MongoDB API služby Azure Cosmos DB pro MongoDB
description: Tento dokument obsahuje přehled požadavky pro migraci dat z MongoDB do služby Cosmos DB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: roaror
ms.openlocfilehash: 476a143555323bbb5058541000a5b1a26d23b71a
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013577"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Před migrací postup pro migraci dat z MongoDB API služby Azure Cosmos DB pro MongoDB

Než zahájíte migraci dat z MongoDB (ať už místní nebo v cloudu (IaaS)) na rozhraní API služby Azure Cosmos DB pro MongoDB, měli byste:

1. [Vytvoření účtu služby Azure Cosmos DB](#create-account)
2. [Odhadnout propustnost, které jsou potřebné pro vaše úlohy](#estimate-throughput)
3. [Vyberte si klíč rozhraní optimální oddílů pro vaše data](#partitioning)
4. [Vysvětlení zásady indexování nastavené na vaše data](#indexing)

Pokud jste už dokončili výše uvedené požadavky pro migraci, najdete v článku [data migrace MongoDB API služby Azure Cosmos DB pro MongoDB](../dms/tutorial-mongodb-cosmos-db.md) pro pokyny k migraci skutečná data. Pokud ne, tento dokument obsahuje pokyny pro zpracování těchto požadavků. 

## <a id="create-account"></a> Vytvoření účtu služby Azure Cosmos DB 

Před zahájením migrace, budete muset [vytvoření účtu služby Azure Cosmos pomocí rozhraní API služby Azure Cosmos DB pro MongoDB](create-mongodb-dotnet.md). 

Při vytváření účtu můžete vybrat nastavení, která [globálně distribuovat](distribute-data-globally.md) vaše data. Máte také možnost Povolit více oblastí zápisu (nebo několika hlavními databázemi konfigurace), umožňující každé z vašich oblasti i zápis a čtení oblasti.

![Account-Creation](./media/mongodb-pre-migration/account-creation.png)

## <a id="estimate-throughput"></a> Odhadnout propustnost potřeby vašich úloh

Před zahájením migrace s použitím [Database Migration Service (DMS)](../dms/dms-overview.md), by měl odhadnou množství propustnost pro zřizování pro databáze Azure Cosmos a kolekce.

Propustnost můžete zřídit buď:

- Kolekce

- Databáze

> [!NOTE]
> Také můžete mít kombinaci výše, kde může mít vyhrazené některé kolekce v databázi zřízené propustnosti a ostatním může sdílet propustnost. Podrobnosti najdete v tématu [nastavení propustnosti na databázi a kontejner](set-throughput.md) stránky.
>

Nejdřív byste měli rozhodnout, zda chcete zřídit databáze nebo kolekce úrovně propustnosti nebo kombinaci obojího. Obecně se doporučuje nakonfigurovat vyhrazené propustnosti na úrovni kolekce. Zřizování propustnosti na úrovni databáze umožňuje kolekcí v databázi pro sdílení zřízenou propustnost. Pomocí sdílených propustnost ale neexistuje žádná záruka, že konkrétní propustnosti na každé jednotlivé kolekce a nezískají předvídatelný výkon na žádnou konkrétní kolekci.

Pokud si nejste jisti, jakou propustnost by měl být vyhrazen pro každé jednotlivé kolekce, můžete propustnost na úrovni databáze. Si můžete představit zřízená propustnost nakonfigurovaný ve vaší databázi Azure Cosmos jako logický ekvivalent na tento výpočetní kapacitu virtuálního počítače MongoDB nebo fyzický server, ale cenově výhodnější umožňuje Elasticky škálovat. Další informace najdete v tématu [zřizování propustnosti na kontejnery Azure Cosmos a databází](set-throughput.md).

Pokud zřizujete propustnosti na úrovni databáze, musí být vytvořeny všechny kolekce vytvořené v rámci této databáze s klíčem oddílu/horizontálních oddílů. Další informace o dělení najdete v tématu [dělení a horizontální škálování ve službě Azure Cosmos DB](partition-data.md). Pokud nezadáte klíč oddílu/horizontálního oddílu během migrace, Azure Database Migration Service automaticky vyplnit pole klíče horizontálního oddílu s *_id* atribut, který je automaticky generována pro každý dokument.

### <a name="optimal-number-of-request-units-rus-to-provision"></a>Optimální počet jednotek žádosti (ru) pro zřizování

Ve službě Azure Cosmos DB propustnost je předem zřízený a se měří v jednotkách žádostí (RU) za sekundu. Pokud máte úlohy, které běží MongoDB na virtuálním počítači nebo na místě, představit RU jako jednoduchý abstrakci pro fyzické prostředky, například pro velikost virtuálního počítače nebo – s místní servery a prostředky jsou k dispozici, například paměti, procesoru, vstupně-výstupních operací. 

Na rozdíl od virtuálních počítačů nebo na místních serverech se snadno škálovat nahoru a dolů v každém okamžiku RU. Počet zřízených ru můžete změnit během několika sekund a fakturuje se vám jenom pro maximální počet jednotek RU, které zřizujete za dané období hodinová. Další informace najdete v tématu [jednotky žádosti v Azure Cosmos DB](request-units.md).

Toto jsou klíčové faktory, které ovlivňují počet požadovaných jednotek ru:
- **Velikost položky (například dokument)**: Jak se zvyšuje velikost položky či dokumentu, počet jednotek ru spotřebovaných pro čtení nebo zápis dokumentu nebo položky také zvyšuje.
- **Počet vlastností položky**: Za předpokladu, že [výchozí indexování](index-overview.md) u všech vlastností, počet jednotek ru spotřebovaných pro zápis položky se zvyšuje s rostoucím zvýší počet vlastností položky. Můžete snížit spotřebu jednotek žádosti pro operace zápisu podle [omezením počtu indexovaných vlastností](index-policy.md).
- **Souběžné operace**: Požádat o spotřebované jednotky závisí také na frekvenci, se kterou různé operace CRUD (jako jsou zápisy, čtení, aktualizace, odstranění) a že jsou provedeny složitější dotazy. Můžete použít [mongostat](https://docs.mongodb.com/manual/reference/program/mongostat/) výstup souběžnosti potřeby aktuální data MongoDB.
- **Dotazování vzory**: Složitost dotazu má vliv na tom, kolik jednotek žádosti se spotřebovávají dotazu.

Pokud exportujete pomocí souborů JSON [mongoexport](https://docs.mongodb.com/manual/reference/program/mongoexport/) a porozumět, kolik zápisy, čtení, aktualizace a odstranění, které se provádějí za sekundu, můžete použít [Plánovač kapacity služby Azure Cosmos DB](https://www.documentdb.com/capacityplanner) odhadnout Počáteční počet jednotek ru na zřízení funkce. Capacity planner zvážit není náklady na složitější dotazy. Takže pokud máte složité dotazy na data, bude používat další jednotky RU. Kalkulačce také předpokládá, že všechna pole jsou indexována, a konzistence typu relace se používá. Nejlepší způsob, jak migrovat vaše data (nebo ukázková data) do služby Azure Cosmos DB je náklady na dotazy [připojení ke koncovému bodu služby Cosmos DB](connect-mongodb-account.md) a spustit ukázkový dotaz z MongoDB Shell pomocí `getLastRequestStastistics` příkazu získejte zátěž žádostí, které bude výstup počet spotřebovaných rezervovaných jednotek:

`db.runCommand({getLastRequestStatistics: 1})`

Tento příkaz na dokument JSON podobný následujícímu výstupu:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

Až porozumíte počet ru spotřebovaných dotazem a souběžnost potřebuje pro daný dotaz, můžete upravit počet zřízených RU. Optimalizace rezervovaných jednotek není jednorázová – byste měli průběžně optimalizovat nebo vertikálně navýšit kapacitu ru zřízených, v závislosti na tom, jestli nejsou očekává velkému provozu, na rozdíl od úlohy heavy nebo importu dat.

## <a id="partitioning"></a>Zvolte svůj klíč oddílu
Dělení je zásadním aspektem ohled před migrací do globálně distribuovanou databázi jako službu Azure Cosmos DB. Azure Cosmos DB používá dělení škálování jednotlivých kontejnerů v databázi pro potřeby škálovatelnost a výkon vaší aplikace. Při dělení položek v kontejneru dělí do různých podmnožiny označované jako logické oddíly. Podrobnosti a doporučení pro výběr klíče oddílu správné pro vaše data, najdete v tématu [volba klíče oddílu části](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). 

## <a id="indexing"></a>Indexování dat
Ve výchozím nastavení služby Azure Cosmos DB indexuje všechna datová pole po ingestování. Můžete upravit [zásady indexování](index-policy.md) ve službě Azure Cosmos DB v každém okamžiku. Ve skutečnosti se často doporučuje vypnout indexování kódu při migraci dat a pak ho znova zapnout při dat je již v databázi Cosmos DB. Podrobné informace o indexování, si můžete přečíst více o řezu [indexování ve službě Azure Cosmos DB](index-overview.md) oddílu. 

[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) automaticky provede migraci kolekce MongoDB s jedinečné indexy. Však musí být jedinečné indexy vytvořené před migrací. Azure Cosmos DB nepodporuje vytváření jedinečných indexů, pokud již existuje dat v kolekcích. Další informace najdete v tématu [jedinečné klíče ve službě Azure Cosmos DB](unique-keys.md).

## <a name="next-steps"></a>Další postup
* [Migrate MongoDB data do služby Cosmos DB pomocí službu Database Migration Service.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Zřizování propustnosti na kontejnery Azure Cosmos a databází](set-throughput.md)
* [Dělení ve službě Azure Cosmos DB](partition-data.md)
* [Globální distribuce ve službě Azure Cosmos DB](distribute-data-globally.md)
* [Indexování ve službě Azure Cosmos DB](index-overview.md)
* [Jednotky žádosti v Azure Cosmos DB](request-units.md)
