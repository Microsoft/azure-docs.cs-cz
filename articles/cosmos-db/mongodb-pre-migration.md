---
title: Kroky před migrací pro migraci dat do rozhraní API Azure Cosmos DB pro MongoDB
description: Tento dokument poskytuje přehled požadavků na migraci dat z MongoDB do Cosmos DB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: lbosq
ms.openlocfilehash: ef3d56b4ec7e4dbe5f6f4097fdd5d8d125b074dc
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75932291"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Kroky před migrací pro migraci dat z MongoDB Azure Cosmos DB do rozhraní API pro MongoDB

Před migrací dat z MongoDB (místně nebo v cloudu), abyste Azure Cosmos DB rozhraní API pro MongoDB, měli byste:

1. [Přečtěte si klíčové informace o používání rozhraní API Azure Cosmos DB pro MongoDB.](#considerations)
2. [Volba možnosti migrace dat](#options)
3. [Odhad propustnosti potřebných pro vaše úlohy](#estimate-throughput)
4. [Vyberte pro svá data optimální klíč oddílu.](#partitioning)
5. [Pochopení zásad indexování, které můžete nastavit pro vaše data](#indexing)

Pokud jste už dokončili výše uvedené požadavky na migraci, můžete [migrovat data MongoDB, abyste mohli Azure Cosmos DB rozhraní API pro MongoDB pomocí Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md). Pokud jste ještě nevytvořili účet, můžete si také projít libovolným [rychlým startem](create-mongodb-dotnet.md).

## <a id="considerations"></a>Hlavní předpoklady při použití rozhraní API Azure Cosmos DB pro MongoDB

Níže jsou uvedené specifické charakteristiky rozhraní API Azure Cosmos DB pro MongoDB:
- **Model kapacity**: kapacita databáze na Azure Cosmos dB vychází z modelu založeného na propustnosti. Tento model je založen na [jednotkách žádosti za sekundu](request-units.md), což je jednotka, která představuje počet operací databáze, které mohou být provedeny na základě kolekce za sekundu. Tato kapacita se dá přidělit na [úrovni databáze nebo kolekce](set-throughput.md)a dá se zřídit podle modelu přidělení nebo pomocí [modelu autopilotu](provision-throughput-autopilot.md).
- **Jednotky žádosti**: každá databázová operace má v Azure Cosmos DB náklady na přidružené jednotky žádosti (ru). Po spuštění se toto odečte od úrovně dostupné jednotky žádosti v dané druhé. Pokud požadavek vyžaduje více ru než aktuálně přidělené dvě možnosti, zvýší se množství ru nebo počká, až se spustí další sekunda, a pak operaci zopakuje.
- **Elastická kapacita**: kapacita pro danou kolekci nebo databázi se může kdykoli změnit. Díky tomu bude databáze pružně přizpůsobena požadavkům na propustnost vašich úloh.
- **Automaticky horizontálního dělení**: Azure Cosmos DB poskytuje systém automatického dělení, který vyžaduje jenom horizontálních oddílů (nebo dělení). [Automatický mechanismus dělení](partition-data.md) se sdílí mezi všemi Azure Cosmos DB rozhraní API a umožňuje bezproblémovou a datovou škálu prostřednictvím horizontální distribuce.

## <a id="options"></a>Možnosti migrace pro rozhraní API Azure Cosmos DB pro MongoDB

[Azure Database Migration Service for Azure Cosmos DB API pro MongoDB](../dms/tutorial-mongodb-cosmos-db.md) poskytuje mechanismus, který zjednodušuje migraci dat tím, že poskytuje plně spravovanou hostující platformu, možnosti monitorování migrace a automatické zpracování omezení. Úplný seznam možností je následující:

|**Typ migrace**|**Řešení**|**Důležité informace**|
|---------|---------|---------|
|Offline|[Nástroj pro migraci dat](https://docs.microsoft.com/azure/cosmos-db/import-data)|nastavení a podpora více zdrojů &bull; Snadná <br/>&bull; nejsou vhodné pro velké datové sady.|
|Offline|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|nastavení a podpora více zdrojů &bull; Snadná <br/>&bull; využívá knihovnu hromadných prováděcích modulů Azure Cosmos DB <br/>&bull; vhodné pro velké datové sady <br/>&bull; nedostatečného kontrolního bodu znamená, že při migraci by nějaký problém vyžadoval restartování celého procesu migrace.<br/>&bull; nedostatku fronty nedoručených zpráv by znamenalo, že několik chybných souborů může zastavit celý proces migrace. <br/>&bull; potřebuje vlastní kód pro zvýšení propustnosti čtení pro určité zdroje dat.|
|Offline|[Existující nástroje Mongo (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|nastavení a integrace &bull; snadno <br/>&bull; potřebuje vlastní zpracování pro omezení|
|Online|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; plně spravovanou migrační službu.<br/>&bull; poskytuje řešení pro hostování a monitorování pro úlohu migrace. <br/>&bull; vhodné pro velké datové sady a postará se o replikaci živých změn <br/>&bull; funguje jenom s jinými MongoDB zdroji.|


## <a id="estimate-throughput"></a>Odhad propustnosti pro vaše úlohy

V Azure Cosmos DB propustnost je zajištěná předem a měří se v jednotkách žádosti (RU) za sekundu. Na rozdíl od virtuálních počítačů nebo místních serverů se ru snadno škáluje nahoru a dolů. Počet zřízených ru můžete okamžitě změnit. Další informace najdete v tématu [jednotky žádostí v Azure Cosmos DB](request-units.md).

Pomocí [kalkulačky Azure Cosmos DB kapacity](https://cosmos.azure.com/capacitycalculator/) můžete určit množství jednotek požadavků na základě konfigurace vašeho databázového účtu, množství dat, velikosti dokumentu a požadovaných čtení a zápisů za sekundu.

Níže jsou uvedené klíčové faktory, které ovlivňují počet požadovaných ru:
- **Velikost dokumentu**: v případě zvýšení velikosti položky nebo dokumentu se také zvýší počet ru spotřebovaných na čtení nebo zápis položky nebo dokumentu.
- **Počet vlastností dokumentu**: počet ru spotřebovaných k vytvoření nebo aktualizaci dokumentu souvisí s počtem, složitostí a délkou jeho vlastností. [Omezením počtu indexovaných vlastností](mongodb-indexing.md)můžete snížit spotřebu jednotek požadavků pro operace zápisu.
- **Vzorce dotazů**: složitost dotazu ovlivňuje počet jednotek požadavků, které dotaz spotřebovává. 

Nejlepším způsobem, jak porozumět nákladům na dotazy, je použití ukázkových dat v Azure Cosmos DB [a spuštění ukázkových dotazů z prostředí MongoDB](connect-mongodb-account.md) pomocí příkazu `getLastRequestStastistics` k získání poplatků za požadavek, který vrátí počet spotřebovaných ru:

`db.runCommand({getLastRequestStatistics: 1})`

Tento příkaz vytvoří výstup dokumentu JSON podobný následujícímu:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

[Nastavení diagnostiky](cosmosdb-monitor-resource-logs.md) můžete použít také k pochopení četnosti a vzorců dotazů provedených proti Azure Cosmos DB. Výsledky z diagnostických protokolů je možné odeslat do účtu úložiště, do instance EventHub nebo do [Azure Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).  

## <a id="partitioning"></a>Zvolit klíč oddílu
Rozdělení na oddíly, označované také jako horizontálního dělení, je klíčovým bodem, který je potřeba zvážit před migrací dat. Azure Cosmos DB používá plně spravované dělení ke zvýšení kapacity databáze, aby splňovala požadavky na úložiště a propustnost. Tato funkce nepotřebuje hostování nebo konfiguraci směrovacích serverů.   

Podobným způsobem funkce dělení automaticky přičítá kapacitu a znovu vyvažuje data. Podrobnosti a doporučení pro výběr správného klíče oddílu pro vaše data najdete v [článku o výběru klíče oddílu](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). 

## <a id="indexing"></a>Indexace dat
Ve výchozím nastavení Azure Cosmos DB poskytuje automatické indexování pro všechna vložená data. Možnosti indexování poskytované Azure Cosmos DB zahrnují přidání složených indexů, jedinečných indexů a indexů TTL (Time-to-Live). Rozhraní pro správu indexů je namapováno na příkaz `createIndex()`. Přečtěte si další informace o [indexování v rozhraní API Azure Cosmos DB pro MongoDB](mongodb-indexing.md).

[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) automaticky migruje kolekce MongoDB s jedinečnými indexy. Jedinečné indexy ale musí být vytvořené před migrací. Azure Cosmos DB nepodporuje vytváření jedinečných indexů, pokud v kolekcích již existují data. Další informace najdete v tématu [jedinečné klíče v Azure Cosmos DB](unique-keys.md).

## <a name="next-steps"></a>Další kroky
* [Migrujte data MongoDB na Cosmos DB pomocí Database Migration Service.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Zajištění propustnosti v kontejnerech a databázích Azure Cosmos](set-throughput.md)
* [Dělení ve službě Azure Cosmos DB](partition-data.md)
* [Globální distribuce v Azure Cosmos DB](distribute-data-globally.md)
* [Indexování v Azure Cosmos DB](index-overview.md)
* [Jednotky žádostí ve službě Azure Cosmos DB](request-units.md)
