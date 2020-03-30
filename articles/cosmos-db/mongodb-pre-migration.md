---
title: Kroky před migrací pro migraci do rozhraní API Azure Cosmos DB pro MongoDB
description: Tento dokument poskytuje přehled předpokladů pro migraci dat z MongoDB do Cosmos DB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: lbosq
ms.openlocfilehash: 73ac1a6ffd5fc2b2d52f169e1e0332044638f9f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75942078"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Kroky před migrací pro migrace dat z MongoDB do rozhraní API Azure Cosmos DB pro MongoDB

Před migrací dat z MongoDB (buď místně nebo v cloudu) do rozhraní API Azure Cosmos DB pro MongoDB, měli byste:

1. [Přečtěte si klíčové aspekty používání rozhraní API Azure Cosmos DB pro MongoDB](#considerations)
2. [Výběr možnosti migrace dat](#options)
3. [Odhad propustnosti potřebné pro vaše úlohy](#estimate-throughput)
4. [Výběr optimálního klíče oddílu pro vaše data](#partitioning)
5. [Principy zásad indexování, které můžete nastavit u dat](#indexing)

Pokud jste již dokončili výše uvedené předpoklady pro migraci, můžete [migrovat data MongoDB do rozhraní API Azure Cosmos DB pro MongoDB pomocí služby Migrace databáze Azure](../dms/tutorial-mongodb-cosmos-db.md). Pokud jste si účet nevytvořili, můžete procházet všechny [rychlé starty,](create-mongodb-dotnet.md) které zobrazují kroky k vytvoření účtu.

## <a name="considerations-when-using-azure-cosmos-dbs-api-for-mongodb"></a><a id="considerations"></a>Důležité informace při používání rozhraní API Azure Cosmos DB pro MongoDB

Následují specifické charakteristiky rozhraní API Azure Cosmos DB pro MongoDB:

- **Model kapacity**: Kapacita databáze v Azure Cosmos DB je založená na modelu založeném na propustnosti. Tento model je založen na [jednotky požadavku za sekundu](request-units.md), což je jednotka, která představuje počet databázových operací, které lze provést proti kolekci na základě za sekundu. Tuto kapacitu lze přidělit na [úrovni databáze nebo kolekce](set-throughput.md)a může být zřízena na modelu přidělení nebo pomocí modelu [AutoPilot](provision-throughput-autopilot.md).

- **Jednotky požadavků:** Každá operace databáze má náklady na přidružené jednotky požadavků (RU) v Azure Cosmos DB. Při spuštění se odečte od úrovně dostupných jednotek požadavků na danou sekundu. Pokud požadavek vyžaduje více ru než aktuálně přidělené RU/s existují dvě možnosti řešení problému – zvýšit množství ru nebo počkejte, až se spustí další sekunda a potom opakujte operaci.

- **Elastická kapacita**: Kapacita pro danou kolekci nebo databázi se může kdykoli změnit. To umožňuje databázi elasticky přizpůsobit požadavky na propustnost vašeho pracovního vytížení.

- **Automatické dělení**: Azure Cosmos DB poskytuje systém automatického dělení, který vyžaduje pouze oddíl (nebo klíč oddílu). [Mechanismus automatického dělení](partition-data.md) je sdílen a všechna řešení API Db Azure Cosmos a umožňuje bezproblémová data a škálování horizontální mnoství.

## <a name="migration-options-for-azure-cosmos-dbs-api-for-mongodb"></a><a id="options"></a>Možnosti migrace pro rozhraní API Azure Cosmos DB pro MongoDB

Rozhraní [API Azure Cosmos DB pro MongoDB](../dms/tutorial-mongodb-cosmos-db.md) poskytuje mechanismus, který zjednodušuje migraci dat tím, že poskytuje plně spravovanou hostingovou platformu, možnosti monitorování migrace a automatické zpracování omezení. Úplný seznam možností je následující:

|**Typ migrace**|**Řešení**|**Požadavky**|
|---------|---------|---------|
|Offline|[Nástroj pro migraci dat](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Snadné nastavení a podporuje více zdrojů <br/>&bull;Není vhodné pro velké datové sady.|
|Offline|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Snadné nastavení a podporuje více zdrojů <br/>&bull;Využívá knihovnu hromadného vykonavatele služby Azure Cosmos DB <br/>&bull;Vhodné pro velké datové sady <br/>&bull;Nedostatek kontrolních bodů znamená, že jakýkoli problém v průběhu migrace by vyžadoval restartování celého procesu migrace<br/>&bull;Nedostatek fronty nedoručených zpráv by znamenal, že několik chybných souborů by mohlo zastavit celý proces migrace. <br/>&bull;Potřebuje vlastní kód pro zvýšení propustnost pro čtení pro určité zdroje dat|
|Offline|[Stávající Nástroje Mongo (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull;Snadné nastavení a integrace <br/>&bull;Potřebuje vlastní manipulaci pro škrticí klapky|
|Online|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull;Plně spravovaná migrační služba.<br/>&bull;Poskytuje řešení pro hostování a monitorování úlohy migrace. <br/>&bull;Vhodné pro velké datové sady a stará se o replikaci živých změn <br/>&bull;Pracuje pouze s jinými zdroji MongoDB|


## <a name="estimate-the-throughput-need-for-your-workloads"></a><a id="estimate-throughput"></a>Odhadte propustnost pro vaše úlohy

V Azure Cosmos DB propustnost se zřídí předem a měří se v jednotkách požadavků (RU) za sekundu. Na rozdíl od virtuálních počítačů nebo místních serverů se ru snadno vertikují vertikálně nahoru a dolů. Počet zřízených ru můžete okamžitě změnit. Další informace najdete v tématu [Požadavek jednotek v Azure Cosmos DB](request-units.md).

[Kalkulačka kapacity Azure Cosmos DB](https://cosmos.azure.com/capacitycalculator/) můžete určit množství jednotek požadavků na základě konfigurace databázového účtu, množství dat, velikosti dokumentu a požadovaných čtení a zápisů za sekundu.

Níže jsou uvedeny klíčové faktory, které ovlivňují počet požadovaných ru:
- **Velikost dokumentu**: Jak se zvětšuje velikost položky/dokumentu, zvyšuje se také počet ru spotřebovaných ke čtení nebo zápisu položky nebo dokumentu.

- **Počet vlastností dokumentu**: Počet ru spotřebovaných k vytvoření nebo aktualizaci dokumentu souvisí s počtem, složitostí a délkou jeho vlastností. Spotřebu jednotky požadavku pro operace zápisu můžete snížit [omezením počtu indexovaných vlastností](mongodb-indexing.md).

- **Vzorky dotazu**: Složitost dotazu ovlivňuje, kolik jednotek požadavků je dotazem spotřebováno. 

Nejlepší způsob, jak porozumět nákladům na dotazy, je použít ukázková data v Azure Cosmos DB `getLastRequestStastistics` a spustit [ukázkové dotazy z prostředí MongoDB](connect-mongodb-account.md) pomocí příkazu k získání poplatku za požadavek, který bude výstupem počtu spotřebovaných ru:

`db.runCommand({getLastRequestStatistics: 1})`

Tento příkaz vyveslne dokument JSON podobný následujícímu:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

Můžete také použít [nastavení diagnostiky](cosmosdb-monitor-resource-logs.md) k pochopení četnosti a vzory dotazů provedených proti Azure Cosmos DB. Výsledky z diagnostických protokolů lze odeslat do účtu úložiště, instance EventHub nebo [Azure Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).  

## <a name="choose-your-partition-key"></a><a id="partitioning"></a>Zvolte klíč oddílu
Dělení, označované také jako střep, je klíčovým bodem zvažování před migrací dat. Azure Cosmos DB používá plně spravované dělení ke zvýšení kapacity v databázi ke splnění požadavků na úložiště a propustnost. Tato funkce nepotřebuje hostování nebo konfiguraci směrovacích serverů.   

Podobným způsobem možnost rozdělení automaticky přidá kapacitu a odpovídajícím způsobem znovu vyvažuje data. Podrobnosti a doporučení ohledně výběru správného klíče oddílu pro vaše data naleznete v [článku Výběr klíče oddílu](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). 

## <a name="index-your-data"></a><a id="indexing"></a>Indexování vašich dat
Ve výchozím nastavení Azure Cosmos DB poskytuje automatické indexování na všechna vložená data. Možnosti indexování poskytované Azure Cosmos DB zahrnují přidání kompozitních indexů, jedinečných indexů a indexů Time-to-live (TTL). Rozhraní pro správu indexu `createIndex()` je mapováno na příkaz. Další informace najdete [v rozhraní API Azure Cosmos DB pro MongoDB](mongodb-indexing.md).

[Služba migrace databáze Azure](../dms/tutorial-mongodb-cosmos-db.md) automaticky migruje kolekce MongoDB s jedinečnými indexy. Jedinečné indexy však musí být vytvořeny před migrací. Azure Cosmos DB nepodporuje vytváření jedinečných indexů, když už jsou data ve vašich kolekcích. Další informace najdete [v tématu Jedinečné klíče v Azure Cosmos DB](unique-keys.md).

## <a name="next-steps"></a>Další kroky
* [Migrujte data MongoDB do cosmos DB pomocí služby migrace databáze.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Zřizování propustnost na kontejnerech a databázích Azure Cosmos](set-throughput.md)
* [Dělení ve službě Azure Cosmos DB](partition-data.md)
* [Globální distribuce v Azure Cosmos DB](distribute-data-globally.md)
* [Indexování ve službě Azure Cosmos DB](index-overview.md)
* [Jednotky požadavků v Azure Cosmos DB](request-units.md)
