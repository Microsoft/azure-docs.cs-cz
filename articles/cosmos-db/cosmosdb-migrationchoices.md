---
title: Možnosti migrace Cosmos DB
description: Tento dokument popisuje různé možnosti migrace místních nebo cloudových dat do Azure Cosmos DB
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: bharathb
ms.openlocfilehash: 34698a215477abdd7d68c3dfe050657ecf049690
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984891"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>Možnosti migrace místních nebo cloudových dat do Azure Cosmos DB

Data z různých zdrojů dat můžete načíst do Azure Cosmos DB. Navíc vzhledem k tomu, že Azure Cosmos DB podporuje více api, cíle může být libovolné existující api. Za účelem podpory cesty migrace z různých zdrojů do různých azure cosmos DB API, existuje několik řešení, která poskytují specializované zpracování pro každou cestu migrace. Tento dokument obsahuje seznam dostupných řešení a popisuje jejich výhody a omezení.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>Faktory ovlivňující volbu nástroje pro migraci

Výběr nástroje pro migraci určují následující faktory:
* **Migrace online vs offline**: Mnoho nástrojů pro migraci poskytuje cestu pouze k jednorázové migraci. To znamená, že aplikace přistupující k databázi může dojít k období prostojů. Některá řešení migrace poskytují způsob, jak provést migraci za provozu, kde je replikační kanál nastaven mezi zdrojem a cílem.

* **Zdroj dat**: Existující data mohou být v různých zdrojích dat, jako je Oracle DB2, Datastax Cassanda, Azure SQL Server, PostgreSQL atd. Data mohou být také v existujícím účtu Azure Cosmos DB a záměrem migrace může být změna datového modelu nebo přerozdělení dat v kontejneru s jiným klíčem oddílu.

* **Azure Cosmos DB API**: Pro rozhraní SQL API v Azure Cosmos DB existuje celá řada nástrojů vyvinutých týmem Azure Cosmos DB, které pomáhají v různých scénářích migrace. Všechna ostatní api mají vlastní specializovanou sadu nástrojů vyvinutých a udržovaných komunitou. Vzhledem k tomu, že Azure Cosmos DB podporuje tato api na úrovni drátového protokolu, tyto nástroje by měly fungovat tak, jak jsou při migraci dat do Azure Cosmos DB. Mohou však vyžadovat vlastní zpracování pro omezení, protože tento koncept je specifický pro Azure Cosmos DB.

* **Velikost dat**: Většina nástrojů pro migraci funguje velmi dobře pro menší datové sady. Pokud sada dat překročí několik set gigabajtů, jsou možnosti nástrojů pro migraci omezené. 

* **Očekávaná doba trvání migrace**: Migrace lze nakonfigurovat tak, aby probíhaly pomalým přírůstkovým tempem, které spotřebovává menší propustnost nebo může spotřebovávat celou propustnost zřízenou v cílovém kontejneru Azure Cosmos DB a dokončit migraci za kratší dobu.

## <a name="azure-cosmos-db-sql-api"></a>Rozhraní API služby Azure Cosmos DB pro SQL
|**Typ migrace**|**Řešení**|**Požadavky**|
|---------|---------|---------|
|Offline|[Nástroj pro migraci dat](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Snadné nastavení a podporuje více zdrojů <br/>&bull;Nevhodné pro velké datové sady|
|Offline|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Snadné nastavení a podporuje více zdrojů <br/>&bull;Využívá knihovnu hromadného vykonavatele služby Azure Cosmos DB <br/>&bull;Vhodné pro velké datové sady <br/>&bull;Nedostatek kontrolních bodů - To znamená, že pokud dojde k problému v průběhu migrace, je třeba restartovat celý proces migrace<br/>&bull;Nedostatek fronty nedoručených zpráv - To znamená, že několik chybných souborů může zastavit celý proces migrace.|
|Offline|[Konektor Azure Cosmos DB Spark](https://docs.microsoft.com/azure/cosmos-db/spark-connector)|&bull;Využívá knihovnu hromadného vykonavatele služby Azure Cosmos DB <br/>&bull;Vhodné pro velké datové sady <br/>&bull;Potřebuje vlastní nastavení Spark <br/>&bull;Spark je citlivý na nekonzistence schématu a to může být problém během migrace |
|Offline|[Vlastní nástroj s knihovnou hromadného vykonavatele Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/migrate-cosmosdb-data)|&bull;Poskytuje možnosti vytváření nedoručených bodů, které zvyšují odolnost migrace. <br/>&bull;Vhodné pro velmi velké datové sady (10 TB+)  <br/>&bull;Vyžaduje vlastní nastavení tohoto nástroje spuštěného jako služba App Service. |
|Online|[Funkce Cosmos DB + Rozhraní API ChangeFeed](https://docs.microsoft.com/azure/cosmos-db/change-feed-functions)|&bull;Snadné nastavení <br/>&bull;Funguje pouze v případě, že zdroj je kontejner Azure Cosmos DB <br/>&bull;Nevhodné pro velké datové sady <br/>&bull;Nezachytí odstranění ze zdrojového kontejneru. |
|Online|[Služba vlastní migrace pomocí služby ChangeFeed](https://github.com/nomiero/CosmosDBLiveETLSample)|&bull;Poskytuje sledování průběhu <br/>&bull;Funguje pouze v případě, že zdroj je kontejner Azure Cosmos DB <br/>&bull;Funguje také pro větší datové sady. <br/>&bull;Vyžaduje, aby uživatel nastavil službu App Service pro hostování procesoru kanálu Změn. <br/>&bull;Nezachytí odstranění ze zdrojového kontejneru.|
|Online|[Striim](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-sql-api-migrate-data-striim)|&bull;Pracuje s širokou škálou zdrojů, jako jsou Oracle, DB2, SQL Server <br/>&bull;Snadné sestavení etl kanálů a poskytuje řídicí panel pro monitorování <br/>&bull;Podporuje větší datové sady <br/>&bull;Vzhledem k tomu, že se jedná o nástroj jiného výrobce, je třeba jej zakoupit na trhu a nainstalovat do prostředí uživatele.|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB Mongo API
|**Typ migrace**|**Řešení**|**Požadavky**|
|---------|---------|---------|
|Offline|[Nástroj pro migraci dat](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Snadné nastavení a podporuje více zdrojů <br/>&bull;Nevhodné pro velké datové sady|
|Offline|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Snadné nastavení a podporuje více zdrojů <br/>&bull;Využívá knihovnu hromadného vykonavatele služby Azure Cosmos DB <br/>&bull;Vhodné pro velké datové sady <br/>&bull;Nedostatek kontrolních bodů znamená, že jakýkoli problém v průběhu migrace by vyžadoval restartování celého procesu migrace<br/>&bull;Nedostatek fronty nedoručených zpráv by znamenal, že několik chybných souborů by mohlo zastavit celý proces migrace. <br/>&bull;Potřebuje vlastní kód pro zvýšení propustnost pro čtení pro určité zdroje dat|
|Offline|[Stávající Nástroje Mongo (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull;Snadné nastavení a integrace <br/>&bull;Potřebuje vlastní manipulaci pro škrticí klapky|
|Online|[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/tutorial-mongodb-cosmos-db-online)|&bull;Využívá knihovnu hromadného vykonavatele služby Azure Cosmos DB <br/>&bull;Vhodné pro velké datové sady a stará se o replikaci živých změn <br/>&bull;Pracuje pouze s jinými zdroji MongoDB|

## <a name="azure-cosmos-db-cassandra-api"></a>API Cassandra v Azure Cosmos DB
|**Typ migrace**|**Řešení**|**Požadavky**|
|---------|---------|---------|
|Offline|[cqlsh COPY, příkaz](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-cqlsh-copy-command)|&bull;Snadné nastavení <br/>&bull;Nevhodné pro velké datové sady <br/>&bull;Funguje pouze v případě, že zdrojem je tabulka Cassandra|
|Offline|[Kopírování tabulky pomocí Sparku](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-spark) |&bull;Může využít funkce Spark k paralelizaci transformace a požití <br/>&bull;Potřebuje konfiguraci s vlastní zásadou opakování pro zpracování omezení.|
|Online|[Striim (od společnosti Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-cassandra-api-migrate-data-striim)|&bull;Pracuje s širokou škálou zdrojů, jako jsou Oracle, DB2, SQL Server <br/>&bull;Snadné sestavení etl kanálů a poskytuje řídicí panel pro monitorování <br/>&bull;Podporuje větší datové sady <br/>&bull;Vzhledem k tomu, že se jedná o nástroj jiného výrobce, je třeba jej zakoupit na trhu a nainstalovat do prostředí uživatele.|
|Online|[Blitzz (od společnosti Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/oracle-migrate-cosmos-db-blitzz)|<br/>&bull;Podporuje větší datové sady <br/>&bull;Vzhledem k tomu, že se jedná o nástroj jiného výrobce, je třeba jej zakoupit na trhu a nainstalovat do prostředí uživatele.|

## <a name="other-apis"></a>Other APIs
Pro rozhraní API než rozhraní SQL API, Rozhraní MONGO API a rozhraní CASSANDRA API existují různé nástroje podporované každým existujícím ekosystémem rozhraní API. 

**Rozhraní Table API** 
* [Nástroj pro migraci dat](https://docs.microsoft.com/azure/cosmos-db/table-import#data-migration-tool)
* [AzCopy](https://docs.microsoft.com/azure/cosmos-db/table-import#migrate-data-by-using-azcopy)

**Rozhraní Gremlin API**
* [Knihovna hromadného vykonavatele modulu grafů](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-graph-dotnet)
* [Gremlin Jiskra](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>Další kroky

* Další informace naleznete v vyzkoušení ukázkových aplikací, které spotřebovávají knihovnu hromadných prováděcích modulů v [rozhraní .NET](bulk-executor-dot-net.md) a [Vja](bulk-executor-java.md). 
* Knihovna hromadného prováděcího modulu je integrovaná do konektoru Cosmos DB Spark, abyste se dozvěděli další informace, přečtěte si článek [konektoru Azure Cosmos DB Spark.](spark-connector.md)  
* Obraťte se na produktový tým Azure Cosmos DB otevřením lístku podpory v rámci typu problému "Obecné poradenství" a "Velké (TB+) migrace podtyp pro další pomoc s migrací ve velkém měřítku.
