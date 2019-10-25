---
title: Možnosti migrace Cosmos DB
description: Tento dokument popisuje různé možnosti migrace místních nebo cloudových dat na Azure Cosmos DB
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: bharathb
ms.openlocfilehash: 9111193bb441487b9e3c49bc9ee1a296d49f8a31
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882391"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>Možnosti migrace místních nebo cloudových dat na Azure Cosmos DB

Data z různých zdrojů dat můžete načítat do Azure Cosmos DB. Kromě toho, vzhledem k tomu, že Azure Cosmos DB podporuje více rozhraní API, mohou být cílem jakákoli existující rozhraní API. Aby bylo možné podporovat cesty migrace z různých zdrojů do různých Azure Cosmos DB rozhraní API, existuje více řešení, která poskytují specializované zpracování pro každou cestu migrace. Tento dokument obsahuje seznam dostupných řešení a popisuje jejich výhody a omezení.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>Faktory ovlivňující výběr migračního nástroje

Následující faktory určují, jakým způsobem Nástroj pro migraci zvolíte:
* **Online migrace z offline**režimu: mnoho nástrojů pro migraci poskytuje cestu jenom pro jednorázovou migraci. To znamená, že aplikace, které přistupují k databázi, můžou nastat po určitou dobu výpadku. Některá řešení migrace poskytují způsob, jak provést migraci za provozu, kde je nastaven kanál replikace mezi zdrojem a cílem.

* **Zdroj dat**: existující data můžou být v různých zdrojích dat, jako je Oracle DB2, DataStax Cassanda, Azure SQL Server, PostgreSQL atd. Data mohou být také v existujícím účtu Azure Cosmos DB a cílem migrace je změnit datový model nebo znovu rozdělit data v kontejneru s jiným klíčem oddílu.

* **Rozhraní Azure Cosmos DB API**: pro rozhraní SQL api v Azure Cosmos DB existuje řada nástrojů vyvinutých týmem Azure Cosmos DB, které pomáhají v různých scénářích migrace. Všechna ostatní rozhraní API mají svou vlastní specializovanou sadu nástrojů vyvinutých a spravovaných komunitou. Vzhledem k tomu, že Azure Cosmos DB podporuje tato rozhraní API na úrovni přenosového protokolu, tyto nástroje by měly fungovat tak, jak jsou, při migraci dat do Azure Cosmos DB. Můžou ale vyžadovat vlastní zpracování omezení, protože tento koncept je specifický pro Azure Cosmos DB.

* **Velikost dat**: Většina nástrojů pro migraci má velmi dobře fungovat pro menší datové sady. Pokud datová sada přesáhne několik stovek gigabajtů, jsou možnosti nástrojů pro migraci omezené. 

* **Očekávaná doba trvání migrace**: migrace se můžou nakonfigurovat tak, aby se navzájem pomaleji, přírůstkový tempo, který spotřebovává méně propustnosti, nebo může spotřebovat celou propustnost zřízenou v cílovém Azure Cosmos DB kontejneru a dokončit migraci méně. interval.

## <a name="azure-cosmos-db-sql-api"></a>Rozhraní API služby Azure Cosmos DB pro SQL
|**Typ migrace**|**Řešení**|**Důležité informace**|
|---------|---------|---------|
|Stav|[Nástroj pro migraci dat](https://docs.microsoft.com/azure/cosmos-db/import-data)|nastavení a podpora více zdrojů &bull; Snadná <br/>&bull; není vhodné pro velké datové sady.|
|Stav|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|nastavení a podpora více zdrojů &bull; Snadná <br/>&bull; využívá knihovnu hromadných prováděcích modulů Azure Cosmos DB <br/>&bull; vhodné pro velké datové sady <br/>&bull; nedostatečného kontrolního bodu – to znamená, že pokud během migrace dojde k problému, je nutné restartovat celý proces migrace.<br/>&bull; nedostatku fronty nedoručených zpráv – znamená, že některé chybné soubory mohou zastavit celý proces migrace.|
|Stav|[Konektor Spark Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/spark-connector)|&bull; využívá knihovnu hromadných prováděcích modulů Azure Cosmos DB <br/>&bull; vhodné pro velké datové sady <br/>&bull; potřebuje vlastní nastavení Sparku. <br/>&bull; Spark je citlivý na nekonzistence schématu a to může být problém při migraci. |
|Stav|[Vlastní nástroj s Cosmos DB knihovny hromadného prováděcího modulu](https://docs.microsoft.com/azure/cosmos-db/migrate-cosmosdb-data)|&bull; poskytuje funkce pro vytváření kontrolních bodů a možnosti neaktivních zpráv, které zvyšují odolnost proti migraci. <br/>&bull; vhodné pro velmi velké datové sady (10 TB +)  <br/>&bull; vyžaduje vlastní nastavení tohoto nástroje spuštěného jako App Service |
|Online|[Cosmos DB Functions + rozhraní API ChangeFeed](https://docs.microsoft.com/azure/cosmos-db/change-feed-functions)|nastavení je &bull; snadné. <br/>&bull; funguje pouze v případě, že zdrojem je Azure Cosmos DB kontejner. <br/>&bull; není vhodné pro velké datové sady. <br/>&bull; nezachycuje odstranění ze zdrojového kontejneru |
|Online|[Vlastní služba migrace pomocí ChangeFeed](https://aka.ms/CosmosDBMigrationSample)|&bull; poskytuje sledování průběhu <br/>&bull; funguje pouze v případě, že zdrojem je Azure Cosmos DB kontejner. <br/>&bull; funguje i pro větší datové sady. <br/>&bull; vyžaduje, aby uživatel nastavil App Service pro hostování procesoru Change feed. <br/>&bull; nezachycuje odstranění ze zdrojového kontejneru|
|Online|[Striim](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-sql-api-migrate-data-striim)|&bull; funguje s velkým množstvím zdrojů, jako je Oracle, DB2, SQL Server <br/>&bull; snadného sestavování kanálů ETL a poskytuje řídicí panel pro monitorování <br/>&bull; podporuje větší datové sady <br/>&bull; vzhledem k tomu, že se jedná o nástroj třetí strany, musí být zakoupen z webu Marketplace a nainstalován v uživatelském prostředí.|

## <a name="azure-cosmos-db-mongo-api"></a>Rozhraní API pro Azure Cosmos DB Mongo
|**Typ migrace**|**Řešení**|**Důležité informace**|
|---------|---------|---------|
|Stav|[Nástroj pro migraci dat](https://docs.microsoft.com/azure/cosmos-db/import-data)|nastavení a podpora více zdrojů &bull; Snadná <br/>&bull; není vhodné pro velké datové sady.|
|Stav|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|nastavení a podpora více zdrojů &bull; Snadná <br/>&bull; využívá knihovnu hromadných prováděcích modulů Azure Cosmos DB <br/>&bull; vhodné pro velké datové sady <br/>&bull; nedostatečného kontrolního bodu znamená, že při migraci by nějaký problém vyžadoval restartování celého procesu migrace.<br/>&bull; nedostatku fronty nedoručených zpráv by znamenalo, že několik chybných souborů může zastavit celý proces migrace. <br/>&bull; potřebuje ke zvýšení propustnosti čtení pro určité zdroje dat vlastní kód.|
|Stav|[Existující nástroje Mongo (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|nastavení a integrace &bull; snadno <br/>&bull; potřebuje vlastní zpracování pro omezení|
|Online|[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/tutorial-mongodb-cosmos-db-online)|&bull; využívá knihovnu hromadných prováděcích modulů Azure Cosmos DB <br/>&bull; vhodné pro velké datové sady a postará se o replikaci živých změn <br/>&bull; funguje pouze s jinými MongoDB zdroji|

## <a name="azure-cosmos-db-cassandra-api"></a>Rozhraní Cassandra API pro Azure Cosmos DB
|**Typ migrace**|**Řešení**|**Důležité informace**|
|---------|---------|---------|
|Stav|[cqlsh kopírování příkazu](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-cqlsh-copy-command)|nastavení je &bull; snadné. <br/>&bull; není vhodné pro velké datové sady. <br/>&bull; funguje pouze v případě, že zdrojem je tabulka Cassandra.|
|Stav|[Kopírovat tabulku pomocí Sparku](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-spark) |&bull; může využít možnosti Spark k paralelizovat transformaci a ingestování. <br/>&bull; potřebuje konfiguraci s vlastní zásadou opakování pro zpracování omezení.|
|Online|[Striim (z Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-cassandra-api-migrate-data-striim)|&bull; funguje s velkým množstvím zdrojů, jako je Oracle, DB2, SQL Server <br/>&bull; snadného sestavování kanálů ETL a poskytuje řídicí panel pro monitorování <br/>&bull; podporuje větší datové sady <br/>&bull; vzhledem k tomu, že se jedná o nástroj třetí strany, musí být zakoupen z webu Marketplace a nainstalován v uživatelském prostředí.|
|Online|[Blitzz (z Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/oracle-migrate-cosmos-db-blitzz)|<br/>&bull; podporuje větší datové sady <br/>&bull; vzhledem k tomu, že se jedná o nástroj třetí strany, musí být zakoupen z webu Marketplace a nainstalován v uživatelském prostředí.|

## <a name="other-apis"></a>Další rozhraní API
V případě jiných rozhraní API, než je rozhraní API SQL, Mongo API a rozhraní API Cassandra, jsou pro každé z existujících ekosystémů rozhraní API podporovány různé nástroje. 

**Rozhraní Table API** 
* [Nástroj pro migraci dat](https://docs.microsoft.com/azure/cosmos-db/table-import#data-migration-tool)
* [AzCopy](https://docs.microsoft.com/azure/cosmos-db/table-import#migrate-data-by-using-azcopy)

**Rozhraní API pro Gremlin**
* [Knihovna hromadného prováděcího modulu grafu](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-graph-dotnet)
* [Gremlin Spark](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>Další kroky

* Další informace získáte vyzkoušením ukázkových aplikací, které využívají knihovnu hromadných prováděcích modulů v jazycích [.NET](bulk-executor-dot-net.md) a [Java](bulk-executor-java.md). 
* Knihovna hromadného prováděcího modulu je integrovaná do konektoru Cosmos DB Spark. Další informace najdete v článku [Azure Cosmos DB Spark Connector](spark-connector.md) .  
* Obraťte se na Azure Cosmos DB produktového týmu otevřením lístku podpory v části problémový typ problému "Obecné poradenství" a "velké (TB +) migrace" pro další nápovědu k migracím ve velkém měřítku.
