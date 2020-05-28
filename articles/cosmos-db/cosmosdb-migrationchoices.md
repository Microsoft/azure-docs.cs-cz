---
title: Možnosti migrace Cosmos DB
description: Tento dokument popisuje různé možnosti migrace místních nebo cloudových dat na Azure Cosmos DB
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: bharathb
ms.openlocfilehash: 281cdea42975172b7e2366ab5d3e1606f61d5025
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84020536"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>Možnosti migrace místních nebo cloudových dat na Azure Cosmos DB

Data z různých zdrojů dat můžete načítat do Azure Cosmos DB. Kromě toho, vzhledem k tomu, že Azure Cosmos DB podporuje více rozhraní API, mohou být cílem jakákoli existující rozhraní API. Aby bylo možné podporovat cesty migrace z různých zdrojů do různých Azure Cosmos DB rozhraní API, existuje více řešení, která poskytují specializované zpracování pro každou cestu migrace. Tento dokument obsahuje seznam dostupných řešení a popisuje jejich výhody a omezení.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>Faktory ovlivňující výběr migračního nástroje

Následující faktory určují, jakým způsobem Nástroj pro migraci zvolíte:
* **Online migrace z offline**režimu: mnoho nástrojů pro migraci poskytuje cestu jenom pro jednorázovou migraci. To znamená, že aplikace, které přistupují k databázi, můžou nastat po určitou dobu výpadku. Některá řešení migrace poskytují způsob, jak provést migraci za provozu, kde je nastaven kanál replikace mezi zdrojem a cílem.

* **Zdroj dat**: existující data můžou být v různých zdrojích dat, jako je Oracle DB2, DataStax Cassanda, Azure SQL Database, PostgreSQL atd. Data mohou být také v existujícím účtu Azure Cosmos DB a cílem migrace je změnit datový model nebo znovu rozdělit data v kontejneru s jiným klíčem oddílu.

* **Rozhraní Azure Cosmos DB API**: pro rozhraní SQL api v Azure Cosmos DB existuje řada nástrojů vyvinutých týmem Azure Cosmos DB, které pomáhají v různých scénářích migrace. Všechna ostatní rozhraní API mají svou vlastní specializovanou sadu nástrojů vyvinutých a spravovaných komunitou. Vzhledem k tomu, že Azure Cosmos DB podporuje tato rozhraní API na úrovni přenosového protokolu, tyto nástroje by měly fungovat tak, jak jsou, při migraci dat do Azure Cosmos DB. Můžou ale vyžadovat vlastní zpracování omezení, protože tento koncept je specifický pro Azure Cosmos DB.

* **Velikost dat**: Většina nástrojů pro migraci má velmi dobře fungovat pro menší datové sady. Pokud datová sada přesáhne několik stovek gigabajtů, jsou možnosti nástrojů pro migraci omezené. 

* **Očekávaná doba trvání migrace**: migrace se můžou nakonfigurovat tak, aby se navzájem pomaleji, přírůstkový tempo, který spotřebovává méně propustnosti, nebo může spotřebovat celou propustnost zřízenou v cílovém Azure Cosmos DB kontejneru a dokončit migraci za kratší dobu.

## <a name="azure-cosmos-db-sql-api"></a>Rozhraní API služby Azure Cosmos DB pro SQL
|**Typ migrace**|**Řešení**|**Požadavky**|
|---------|---------|---------|
|Offline|[Nástroj pro migraci dat](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Snadné nastavení a podpora více zdrojů <br/>&bull;Nevhodné pro velké datové sady|
|Offline|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Snadné nastavení a podpora více zdrojů <br/>&bull;Využívá knihovnu hromadných prováděcích knihoven Azure Cosmos DB. <br/>&bull;Vhodné pro velké datové sady <br/>&bull;Nedostatek kontrolního bodu – znamená to, že pokud během migrace dojde k problému, budete muset celý proces migrace restartovat.<br/>&bull;Nedostatek fronty nedoručených zpráv – znamená, že několik chybných souborů může zastavit celý proces migrace.|
|Offline|[Konektor Spark Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/spark-connector)|&bull;Využívá knihovnu hromadných prováděcích knihoven Azure Cosmos DB. <br/>&bull;Vhodné pro velké datové sady <br/>&bull;Vyžaduje vlastní nastavení Sparku. <br/>&bull;Spark je citlivý na nekonzistence schématu a může se jednat o problém při migraci. |
|Offline|[Vlastní nástroj s Cosmos DB knihovny hromadného prováděcího modulu](https://docs.microsoft.com/azure/cosmos-db/migrate-cosmosdb-data)|&bull;Poskytuje funkce pro vytváření koncových bodů, které zvyšují odolnost proti migraci. <br/>&bull;Vhodné pro velmi velké datové sady (10 TB +)  <br/>&bull;Vyžaduje vlastní nastavení tohoto nástroje spouštěného jako App Service |
|Online|[Cosmos DB Functions + rozhraní API ChangeFeed](https://docs.microsoft.com/azure/cosmos-db/change-feed-functions)|&bull;Snadné nastavení <br/>&bull;Funguje pouze v případě, že zdrojem je Azure Cosmos DB kontejner. <br/>&bull;Nevhodné pro velké datové sady <br/>&bull;Nezachycuje odstranění ze zdrojového kontejneru. |
|Online|[Vlastní služba migrace pomocí ChangeFeed](https://github.com/nomiero/CosmosDBLiveETLSample)|&bull;Poskytuje sledování průběhu <br/>&bull;Funguje pouze v případě, že zdrojem je Azure Cosmos DB kontejner. <br/>&bull;Funguje i pro větší datové sady. <br/>&bull;Vyžaduje, aby uživatel nastavil App Service pro hostování procesoru Change feed. <br/>&bull;Nezachycuje odstranění ze zdrojového kontejneru.|
|Online|[Striim](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-sql-api-migrate-data-striim)|&bull;Funguje s velkým množstvím zdrojů, jako je Oracle, DB2, SQL Server <br/>&bull;Snadné sestavování kanálů ETL a poskytuje řídicí panel pro monitorování <br/>&bull;Podporuje větší datové sady <br/>&bull;Vzhledem k tomu, že se jedná o nástroj třetí strany, musí být zakoupen z webu Marketplace a nainstalován v uživatelském prostředí.|

## <a name="azure-cosmos-db-mongo-api"></a>Rozhraní API pro Azure Cosmos DB Mongo
|**Typ migrace**|**Řešení**|**Požadavky**|
|---------|---------|---------|
|Offline|[Nástroj pro migraci dat](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Snadné nastavení a podpora více zdrojů <br/>&bull;Nevhodné pro velké datové sady|
|Offline|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Snadné nastavení a podpora více zdrojů <br/>&bull;Využívá knihovnu hromadných prováděcích knihoven Azure Cosmos DB. <br/>&bull;Vhodné pro velké datové sady <br/>&bull;Nedostatečná kontrolní bod znamená, že při migraci by se vyžadovalo restartování celého procesu migrace.<br/>&bull;Nedostatku fronty nedoručených zpráv by znamenalo, že několik chybných souborů může zastavit celý proces migrace. <br/>&bull;Vyžaduje vlastní kód pro zvýšení propustnosti čtení pro určité zdroje dat.|
|Offline|[Existující nástroje Mongo (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull;Snadné nastavení a integrace <br/>&bull;Pro omezení potřebuje vlastní zpracování|
|Online|[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/tutorial-mongodb-cosmos-db-online)|&bull;Využívá knihovnu hromadných prováděcích knihoven Azure Cosmos DB. <br/>&bull;Vhodné pro velké datové sady a postará se o replikaci živých změn <br/>&bull;Funguje jenom s jinými MongoDB zdroji.|

## <a name="azure-cosmos-db-cassandra-api"></a>API Cassandra v Azure Cosmos DB
|**Typ migrace**|**Řešení**|**Požadavky**|
|---------|---------|---------|
|Offline|[cqlsh kopírování příkazu](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-cqlsh-copy-command)|&bull;Snadné nastavení <br/>&bull;Nevhodné pro velké datové sady <br/>&bull;Funguje pouze v případě, že zdrojem je tabulka Cassandra.|
|Offline|[Kopírovat tabulku pomocí Sparku](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-spark) |&bull;Může využívat možnosti Sparku k paralelizovat transformaci a ingestování. <br/>&bull;Vyžaduje konfiguraci s vlastní zásadou opakování pro zpracování omezení.|
|Online|[Striim (z Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-cassandra-api-migrate-data-striim)|&bull;Funguje s velkým množstvím zdrojů, jako je Oracle, DB2, SQL Server <br/>&bull;Snadné sestavování kanálů ETL a poskytuje řídicí panel pro monitorování <br/>&bull;Podporuje větší datové sady <br/>&bull;Vzhledem k tomu, že se jedná o nástroj třetí strany, musí být zakoupen z webu Marketplace a nainstalován v uživatelském prostředí.|
|Online|[Blitzz (z Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/oracle-migrate-cosmos-db-blitzz)|<br/>&bull;Podporuje větší datové sady <br/>&bull;Vzhledem k tomu, že se jedná o nástroj třetí strany, musí být zakoupen z webu Marketplace a nainstalován v uživatelském prostředí.|

## <a name="other-apis"></a>Other APIs
V případě jiných rozhraní API, než je rozhraní API SQL, Mongo API a rozhraní API Cassandra, jsou pro každé z existujících ekosystémů rozhraní API podporovány různé nástroje. 

**Rozhraní Table API** 
* [Nástroj pro migraci dat](https://docs.microsoft.com/azure/cosmos-db/table-import#data-migration-tool)
* [AzCopy](https://docs.microsoft.com/azure/cosmos-db/table-import#migrate-data-by-using-azcopy)

**Rozhraní Gremlin API**
* [Knihovna hromadného prováděcího modulu grafu](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-graph-dotnet)
* [Gremlin Spark](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>Další kroky

* Další informace získáte vyzkoušením ukázkových aplikací, které využívají knihovnu hromadných prováděcích modulů v jazycích [.NET](bulk-executor-dot-net.md) a [Java](bulk-executor-java.md). 
* Knihovna hromadného prováděcího modulu je integrovaná do konektoru Cosmos DB Spark. Další informace najdete v článku [Azure Cosmos DB Spark Connector](spark-connector.md) .  
* Obraťte se na Azure Cosmos DB produktového týmu otevřením lístku podpory v části problémový typ problému "Obecné poradenství" a "velké (TB +) migrace" pro další nápovědu k migracím ve velkém měřítku.
