---
title: Možnosti migrace Cosmos DB
description: Tento dokument popisuje různé možnosti migrace místních nebo cloudových dat na Azure Cosmos DB
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: 4de6d4ba019af75b0f6179b2794ddb6c1e35e0c1
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90030068"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>Možnosti migrace místních nebo cloudových dat na Azure Cosmos DB

Data z různých zdrojů dat můžete načítat do Azure Cosmos DB. Vzhledem k tomu, že Azure Cosmos DB podporuje více rozhraní API, mohou být cílem jakákoli existující rozhraní API. Níže jsou uvedeny některé scénáře migrace dat do Azure Cosmos DB:

* Přesune data z jednoho kontejneru Azure Cosmos do jiného kontejneru ve stejné databázi nebo v jiné databázi. s
* Přesun dat mezi vyhrazenými kontejnery do kontejnerů sdílené databáze.
* Přesuňte data z účtu Azure Cosmos, který se nachází v Region1, na jiný účet Azure Cosmos ve stejné nebo jiné oblasti.
* Přesuňte data ze zdroje, jako je Azure Blob Storage, soubor JSON, Oracle Database, Couchbase, DynamoDB na Azure Cosmos DB.

Aby bylo možné podporovat cesty migrace z různých zdrojů do různých Azure Cosmos DB rozhraní API, existuje více řešení, která poskytují specializované zpracování pro každou cestu migrace. Tento dokument obsahuje seznam dostupných řešení a popisuje jejich výhody a omezení.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>Faktory ovlivňující výběr migračního nástroje

Následující faktory určují, jakým způsobem Nástroj pro migraci zvolíte:

* **Online migrace z offline**režimu: mnoho nástrojů pro migraci poskytuje cestu jenom pro jednorázovou migraci. To znamená, že aplikace, které přistupují k databázi, můžou nastat po určitou dobu výpadku. Některá řešení migrace poskytují způsob, jak provést migraci za provozu, kde je nastaven kanál replikace mezi zdrojem a cílem.

* **Zdroj dat**: existující data můžou být v různých zdrojích dat, jako je Oracle DB2, DataStax Cassanda, Azure SQL Database, PostgreSQL atd. Data mohou být také v existujícím účtu Azure Cosmos DB a cílem migrace je změnit datový model nebo znovu rozdělit data v kontejneru s jiným klíčem oddílu.

* **Rozhraní Azure Cosmos DB API**: pro rozhraní SQL api v Azure Cosmos DB existuje řada nástrojů vyvinutých týmem Azure Cosmos DB, které pomáhají v různých scénářích migrace. Všechna ostatní rozhraní API mají svou vlastní specializovanou sadu nástrojů vyvinutých a spravovaných komunitou. Vzhledem k tomu, že Azure Cosmos DB podporuje tato rozhraní API na úrovni přenosového protokolu, tyto nástroje by měly fungovat tak, jak jsou, při migraci dat do Azure Cosmos DB. Můžou ale vyžadovat vlastní zpracování omezení, protože tento koncept je specifický pro Azure Cosmos DB.

* **Velikost dat**: Většina nástrojů pro migraci má velmi dobře fungovat pro menší datové sady. Pokud datová sada přesáhne několik stovek gigabajtů, jsou možnosti nástrojů pro migraci omezené. 

* **Očekávaná doba trvání migrace**: migrace se můžou nakonfigurovat tak, aby se navzájem pomaleji, přírůstkový tempo, který spotřebovává méně propustnosti, nebo může spotřebovat celou propustnost zřízenou v cílovém Azure Cosmos DB kontejneru a dokončit migraci za kratší dobu.

## <a name="azure-cosmos-db-sql-api"></a>Rozhraní API služby Azure Cosmos DB pro SQL

|Typ migrace|Řešení|Podporované zdroje|Podporované cíle|Požadavky|
|---------|---------|---------|---------|---------|
|Offline|[Nástroj pro migraci dat](import-data.md)| &bull;Soubory JSON/CSV<br/>&bull;Rozhraní API služby Azure Cosmos DB pro SQL<br/>&bull;MongoDB<br/>&bull;SQL Server<br/>&bull;Table Storage<br/>&bull;AWS DynamoDB<br/>&bull;Blob Storage Azure|&bull;Rozhraní API služby Azure Cosmos DB pro SQL<br/>&bull;Rozhraní API pro Azure Cosmos DB tabulky<br/>&bull;Soubory JSON |&bull; Snadné nastavení a podpora více zdrojů. <br/>&bull; Není vhodné pro velké datové sady.|
|Offline|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)| &bull;Soubory JSON/CSV<br/>&bull;Rozhraní API služby Azure Cosmos DB pro SQL<br/>&bull;Rozhraní API služby Azure Cosmos DB pro MongoDB<br/>&bull;MongoDB <br/>&bull;SQL Server<br/>&bull;Table Storage<br/>&bull;Blob Storage Azure <br/> <br/>Další podporované zdroje najdete v článku o [Azure Data Factory](../data-factory/connector-overview.md) .|&bull;Rozhraní API služby Azure Cosmos DB pro SQL<br/>&bull;Rozhraní API služby Azure Cosmos DB pro MongoDB<br/>&bull;Soubory JSON <br/><br/> Další podporované cíle najdete v článku o [Azure Data Factory](../data-factory/connector-overview.md) . |&bull; Snadné nastavení a podpora více zdrojů.<br/>&bull; Využívá knihovnu hromadných prováděcích knihoven Azure Cosmos DB. <br/>&bull; Vhodné pro velké datové sady. <br/>&bull; Nedostatek kontrolního bodu – znamená to, že pokud během migrace dojde k problému, budete muset celý proces migrace restartovat.<br/>&bull; Nedostatek fronty nedoručených zpráv – znamená, že několik chybných souborů může zastavit celý proces migrace.|
|Offline|[Konektor Spark Azure Cosmos DB](spark-connector.md)|Azure Cosmos DB rozhraní SQL API. <br/><br/>Další zdroje můžete použít s dalšími konektory z ekosystému Spark.| Azure Cosmos DB rozhraní SQL API. <br/><br/>Další cíle můžete použít s dalšími konektory z ekosystému Spark.| &bull; Využívá knihovnu hromadných prováděcích knihoven Azure Cosmos DB. <br/>&bull; Vhodné pro velké datové sady. <br/>&bull; Potřebuje vlastní nastavení Sparku. <br/>&bull; Spark je citlivý na nekonzistence schématu a může se jednat o problém při migraci. |
|Offline|[Vlastní nástroj s Cosmos DB knihovny hromadného prováděcího modulu](migrate-cosmosdb-data.md)| Zdroj závisí na vlastním kódu. | Rozhraní API služby Azure Cosmos DB pro SQL| &bull; Poskytuje funkce pro vytváření kontrolních bodů a možnosti neaktivních zpráv, které zvyšují odolnost migrace. <br/>&bull; Vhodné pro velmi velké datové sady (10 TB +).  <br/>&bull; Vyžaduje vlastní nastavení tohoto nástroje spouštěného jako App Service. |
|Online|[Cosmos DB Functions + rozhraní API ChangeFeed](change-feed-functions.md)| Rozhraní API služby Azure Cosmos DB pro SQL | Rozhraní API služby Azure Cosmos DB pro SQL| &bull; Snadné nastavení. <br/>&bull; Funguje pouze v případě, že zdrojem je Azure Cosmos DB kontejner. <br/>&bull; Není vhodné pro velké datové sady. <br/>&bull; Nezachycuje odstranění ze zdrojového kontejneru. |
|Online|[Vlastní služba migrace pomocí ChangeFeed](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator)| Rozhraní API služby Azure Cosmos DB pro SQL | Rozhraní API služby Azure Cosmos DB pro SQL| &bull; Poskytuje sledování průběhu. <br/>&bull; Funguje pouze v případě, že zdrojem je Azure Cosmos DB kontejner. <br/>&bull; Funguje i pro větší datové sady.<br/>&bull; Vyžaduje, aby uživatel nastavil App Service pro hostování procesoru Change feed. <br/>&bull; Nezachycuje odstranění ze zdrojového kontejneru.|
|Online|[Striim](cosmosdb-sql-api-migrate-data-striim.md)| &bull;Oracle <br/>&bull;Apache Cassandra<br/><br/> Další podporované zdroje najdete na [webu Striim](https://www.striim.com/sources-and-targets/) . |&bull;Rozhraní API služby Azure Cosmos DB pro SQL <br/>&bull; Azure Cosmos DB rozhraní API Cassandra<br/><br/> Další podporované cíle najdete na [webu Striim](https://www.striim.com/sources-and-targets/) . | &bull; Funguje s velkým množstvím zdrojů, jako je Oracle, DB2, SQL Server.<br/>&bull; Snadné sestavování kanálů ETL a poskytuje řídicí panel pro monitorování. <br/>&bull; Podporuje větší datové sady. <br/>&bull; Vzhledem k tomu, že se jedná o nástroj třetí strany, musí být zakoupen z webu Marketplace a nainstalován v uživatelském prostředí.|

## <a name="azure-cosmos-db-mongo-api"></a>Rozhraní API pro Azure Cosmos DB Mongo

|Typ migrace|Řešení|Podporované zdroje|Podporované cíle|Požadavky|
|---------|---------|---------|---------|---------|
|Online|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)| MongoDB|Rozhraní API služby Azure Cosmos DB pro MongoDB |&bull; Využívá knihovnu hromadných prováděcích knihoven Azure Cosmos DB. <br/>&bull; Vhodné pro velké datové sady a postará se o replikaci živých změn. <br/>&bull; Funguje pouze s jinými MongoDB zdroji.|
|Offline|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)| MongoDB| Rozhraní API služby Azure Cosmos DB pro MongoDB| &bull; Využívá knihovnu hromadných prováděcích knihoven Azure Cosmos DB. <br/>&bull; Vhodné pro velké datové sady a postará se o replikaci živých změn. <br/>&bull; Funguje pouze s jinými MongoDB zdroji.|
|Offline|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)| &bull;Soubory JSON/CSV<br/>&bull;Rozhraní API služby Azure Cosmos DB pro SQL<br/>&bull;Rozhraní API služby Azure Cosmos DB pro MongoDB <br/>&bull;MongoDB<br/>&bull;SQL Server<br/>&bull;Table Storage<br/>&bull;Blob Storage Azure <br/><br/> Další podporované zdroje najdete v článku o [Azure Data Factory](../data-factory/connector-overview.md) . | &bull;Rozhraní API služby Azure Cosmos DB pro SQL<br/>&bull;Rozhraní API služby Azure Cosmos DB pro MongoDB <br/>&bull; Soubory JSON <br/><br/> Další podporované cíle najdete v článku o [Azure Data Factory](../data-factory/connector-overview.md) .| &bull; Snadné nastavení a podpora více zdrojů. <br/>&bull; Využívá knihovnu hromadných prováděcích knihoven Azure Cosmos DB. <br/>&bull; Vhodné pro velké datové sady. <br/>&bull; Nedostatečná kontrolní bod znamená, že při migraci by se vyžadovalo restartování celého procesu migrace.<br/>&bull; Nedostatku fronty nedoručených zpráv by znamenalo, že několik chybných souborů může zastavit celý proces migrace. <br/>&bull; Potřebuje vlastní kód pro zvýšení propustnosti čtení pro určité zdroje dat.|
|Offline|[Existující nástroje Mongo (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|MongoDB | Rozhraní API služby Azure Cosmos DB pro MongoDB| &bull; Snadné nastavení a integrace. <br/>&bull; Vyžaduje vlastní zpracování pro omezení.|

## <a name="azure-cosmos-db-cassandra-api"></a>API Cassandra v Azure Cosmos DB

|Typ migrace|Řešení|Podporované zdroje|Podporované cíle|Požadavky|
|---------|---------|---------|---------|---------|
|Offline|[cqlsh kopírování příkazu](cassandra-import-data.md#migrate-data-using-cqlsh-copy-command)|Soubory CSV | API Cassandra v Azure Cosmos DB| &bull; Snadné nastavení. <br/>&bull; Není vhodné pro velké datové sady. <br/>&bull; Funguje pouze v případě, že zdrojem je tabulka Cassandra.|
|Offline|[Kopírovat tabulku pomocí Sparku](cassandra-import-data.md#migrate-data-using-spark) | &bull;Apache Cassandra<br/>&bull;API Cassandra v Azure Cosmos DB| API Cassandra v Azure Cosmos DB | &bull; Může využívat možnosti Sparku k paralelizovat transformaci a ingestování. <br/>&bull; Vyžaduje konfiguraci s vlastní zásadou opakování pro zpracování omezení.|
|Online|[Striim (z Oracle DB/Apache Cassandra)](cosmosdb-cassandra-api-migrate-data-striim.md)| &bull;Oracle<br/>&bull;Apache Cassandra<br/><br/> Další podporované zdroje najdete na [webu Striim](https://www.striim.com/sources-and-targets/) .|&bull;Rozhraní API služby Azure Cosmos DB pro SQL<br/>&bull;API Cassandra v Azure Cosmos DB <br/><br/> Další podporované cíle najdete na [webu Striim](https://www.striim.com/sources-and-targets/) .| &bull; Funguje s velkým množstvím zdrojů, jako je Oracle, DB2, SQL Server. <br/>&bull; Snadné sestavování kanálů ETL a poskytuje řídicí panel pro monitorování. <br/>&bull; Podporuje větší datové sady. <br/>&bull; Vzhledem k tomu, že se jedná o nástroj třetí strany, musí být zakoupen z webu Marketplace a nainstalován v uživatelském prostředí.|
|Online|[Blitzz (z Oracle DB/Apache Cassandra)](oracle-migrate-cosmos-db-blitzz.md)|&bull;Oracle<br/>&bull;Apache Cassandra<br/><br/>Další podporované zdroje najdete na [webu Blitzz](https://www.blitzz.io/) . |Azure Cosmos DB rozhraní API Cassandra. <br/><br/>Další podporované cíle najdete na [webu Blitzz](https://www.blitzz.io/) . | &bull; Podporuje větší datové sady. <br/>&bull; Vzhledem k tomu, že se jedná o nástroj třetí strany, musí být zakoupen z webu Marketplace a nainstalován v uživatelském prostředí.|

## <a name="other-apis"></a>Other APIs

V případě jiných rozhraní API, než je rozhraní API SQL, Mongo API a rozhraní API Cassandra, jsou pro každé z existujících ekosystémů rozhraní API podporovány různé nástroje. 

**Rozhraní Table API** 

* [Nástroj pro migraci dat](table-import.md#data-migration-tool)
* [AzCopy](table-import.md#migrate-data-by-using-azcopy)

**Rozhraní API pro Gremlin**

* [Knihovna hromadného prováděcího modulu grafu](bulk-executor-graph-dotnet.md)
* [Gremlin Spark](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>Další kroky

* Další informace získáte vyzkoušením ukázkových aplikací, které využívají knihovnu hromadných prováděcích modulů v jazycích [.NET](bulk-executor-dot-net.md) a [Java](bulk-executor-java.md). 
* Knihovna hromadného prováděcího modulu je integrovaná do konektoru Cosmos DB Spark. Další informace najdete v článku [Azure Cosmos DB Spark Connector](spark-connector.md) .  
* Obraťte se na Azure Cosmos DB produktového týmu otevřením lístku podpory v části problémový typ problému "Obecné poradenství" a "velké (TB +) migrace" pro další nápovědu k migracím ve velkém měřítku.
