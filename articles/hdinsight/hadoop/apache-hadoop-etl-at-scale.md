---
title: Extrakce, transformace a načítání (ETL) ve velkém měřítku – Azure HDInsight
description: Zjistěte, jak se používá ETL v HDInsight se Apache Hadoop.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 17aeb847a2c701abf03b46d47e34d13b6fb27316
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633321"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Extrakce, transformace a načítání (ETL) ve velkém měřítku

Extrakce, transformace a načítání (ETL) je proces, podle kterého data je získaných z různých zdrojů, shromažďují ve standardním umístění, čištění a zpracovat a nakonec načten do úložiště, ze kterého může být dotazována. Starší verze procesy ETL umožňuje importovat data, vyčistit na místě a pak jej uložit modul s relačními daty. S HDInsight podporují celou řadu součástí ekosystému Apache Hadoop, provádění ETL ve velkém měřítku. 

Pomocí HDInsight v procesu ETL může automaticky shrnutý podle tímto kanálem:

![Přehled HDInsight ETL](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

V dalších částech prozkoumejte každou z fází ETL a jejich přidružených součásti.

## <a name="orchestration"></a>Orchestrace

Orchestrace zahrnuje ve všech fázích kanálu ETL. Úlohy ETL v HDInsight často zahrnují několik různých produktů, funguje ve spojení mezi sebou.  Hive můžete použít k vyčištění nějakou část dat, zatímco jiné části maže Pig.  Azure Data Factory můžete použít k načtení dat do Azure SQL Database z Azure Data Lake Store.

Orchestrace je potřeba ke spuštění příslušné úlohy v příslušnou dobu.

### <a name="oozie"></a>Oozie

Apache Oozie je systém koordinace pracovních postupů, které spravuje úlohy platformy Hadoop. Oozie běží v rámci clusteru služby HDInsight a je integrován do zásobníku Hadoop. Oozie podporuje úlohy systému Hadoop pro Apache MapReduce, Apache Pig, Apache Hivu a Apache Sqoop. Oozie lze také použít k plánování úloh, které jsou specifické pro systém, jako jsou programy v jazyce Java nebo skripty prostředí.

Další informace najdete v tématu [použití Oozie s Hadoopem k definování a spuštění pracovního postupu na HDInsight](../hdinsight-use-oozie-linux-mac.md) podrobné informace o ukazující způsob použití Oozie Centrum umožňující prosazovat kanál začátku do konce, naleznete v tématu [zprovoznění datového kanálu](../hdinsight-operationalize-data-pipeline.md). 

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory poskytuje možnosti Orchestrace v podobě platformy jako služby. Služby pro integraci dat založené na cloudu, která umožňuje vytvářet pracovní postupy řízené daty v cloudu za účelem Orchestrace a automatizace přesunu a transformace dat je. 

Pomocí služby Azure Data Factory můžete:

1. Vytvářet a plánovat pracovní postupy řízené daty (nazývané kanály), které ingestovat data z různorodých úložišť dat.
2. Zpracovávejte a transformujte data pomocí výpočetních služeb, jako je například Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics, Azure Batch a Azure Machine Learning.
3. Publikovat výstupní data do úložišť dat, jako je Azure SQL Data Warehouse, a umožnit jejich využití v aplikacích business intelligence (BI).

Další informace o Azure Data Factory najdete v článku [dokumentaci](../../data-factory/introduction.md).

## <a name="ingest-file-storage-and-result-storage"></a>Ingestování souborového úložiště a úložiště výsledků

Zdrojové datové soubory jsou obvykle načtou do umístění ve službě Azure Storage nebo Azure Data Lake Store. Soubory mohou být v libovolném formátu, ale obvykle jsou plochých souborů jako sdílené svazky clusteru. 

### <a name="azure-storage"></a>Azure Storage 

[Azure Storage](https://azure.microsoft.com/services/storage/blobs/) má [cíle škálovatelnosti konkrétní](../../storage/common/storage-scalability-targets.md).  Azure Storage nejvíce analytické uzly, škáluje nejlépe při práci s velkým množstvím souborů menší.  Azure Storage zaručuje stejný výkon, bez ohledu na to, kolik souborů nebo jak velkých souborů (pokud jsou v rámci vaší omezení).  To znamená, že můžete ukládat terabajty dat a zachovat si konzistentní výkon, ať používáte podmnožinu dat nebo všechna data.

Azure Storage má několik různých typů objektů BLOB.  *Doplňovací objekt blob* je skvělá možnost pro ukládání protokolů webového nebo data ze senzorů.  

Víc objektů blob je možné distribuovat napříč mnoha servery pro horizontální navýšení kapacity k nim přístup, ale jeden objekt blob může být obsluhovaný pouze jeden server. Zatímco objekty BLOB mohou být logicky seskupeny do kontejnerů objektů blob, neexistují žádné dělení důsledky z této skupině.

Úložiště Azure má také vrstvu rozhraní API WebHDFS pro úložiště objektů blob.  Všechny služby HDInsight můžete získat přístup k souborům ve službě Azure Blob Storage pro čištění dat a zpracování dat, podobně jako na tom, jak by tyto služby používat soubory systému HDFS (Hadoop Distributed).

Data se obvykle ingestuje do služby Azure Storage pomocí prostředí PowerShell, sadu SDK služby Azure Storage nebo AZCopy.

### <a name="azure-data-lake-store"></a>Azure Data Lake Store

Azure Data Lake Store (ADLS) je spravovaná, velkokapacitní úložiště pro analýzy dat, který je kompatibilní s HDFS.  ADLS používá paradigma návrhu, který je podobný HDFS a nabízí neomezenou škálovatelnost z hlediska celková kapacita a velikosti jednotlivých souborů. ADLS je velmi dobré při práci s velkými soubory, protože s velkými soubory mohou být uloženy na více uzlech.  Dělení dat v ADLS probíhá na pozadí.  Získáte mimořádně velkou propustnost pro spouštění analytických úloh s tisíci souběžnými vykonavateli, kteří efektivně čtou a zapisují stovky terabajtů dat.

Data se obvykle ingestuje do služby ADLS pomocí služby Azure Data Factory, sady SDK ADLS, AdlCopy služby, Apache DistCp nebo Apache Sqoop.  Tyto služby používat do značné míry závisí na místě, kde data.  Pokud data je aktuálně v existujícím clusteru Hadoop, můžete použít Apache DistCp, služba AdlCopy nebo Azure Data Factory.  Pokud je ve službě Azure Blob Storage, můžete použít .NET SDK služby Azure Data Lake Store, Azure Powershellu nebo Azure Data Factory.

ADLS je také optimalizovaná pro příjem událostí pomocí Azure Event Hub nebo Apache Storm.

#### <a name="considerations-for-both-storage-options"></a>Důležité informace pro obě možnosti úložiště

Nahrát datových sad v oblasti terabajt, latence sítě může být závažný problém, zejména v případě, že data pochází z umístění v místním.  V takovém případě můžete pomocí následujících možností:

* Azure ExpressRoute: Azure ExpressRoute umožňuje vytvářet privátní připojení mezi datacentry Azure a vaší místní infrastruktury. Tato připojení poskytují spolehlivé variantou při přenosech velkých objemů dat. Další informace najdete v tématu [dokumentace ke službě Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

* "Do režimu offline" nahrávání dat. Můžete použít [služba Azure Import/Export](../../storage/common/storage-import-export-service.md) dodávat pevných disků se svými daty a datového centra Azure. Vaše data se nejprve nahrát do objektů BLOB Azure Storage. Pak můžete použít [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md) nebo [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md) nástroj pro kopírování dat z Azure Storage BLOB do Data Lake Store.

### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Azure SQL data Warehouse je skvělou volbou pro ukládání Vyčištěná a připravený pro budoucí analýzy výsledků.  Azure HDInsight je možné provádět tyto služby pro Azure SQL data Warehouse.

Azure SQL Data Warehouse (datový Sklad SQL) je relační databáze úložiště optimalizované pro analytické úlohy.  Azure SQL data Warehouse se škáluje na základě na dělené tabulky.  Tabulky můžete rozdělit na oddíly na více uzlech.  Azure SQL data Warehouse uzly jsou vybrány v době vytvoření.  Můžete škálovat po jejich výskytu, ale to je aktivní proces, který může vyžadovat přesun dat. Zobrazit [SQL Data Warehouse – spravovat výpočetní](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md) Další informace.

### <a name="hbase"></a>HBase

Apache HBase je k dispozici v Azure HDInsight úložiště dvojic klíč hodnota.  Apache HBase je NoSQL databáze typu open source, která je založena na Hadoop a modelována podle Google BigTable. HBase poskytuje výkonné náhodný přístup a silnou konzistenci pro velké objemy nestrukturovaných a částečně strukturovaných dat v databázi schemaless uspořádané podle rodin sloupců.

Data se ukládají na řádky tabulky a data v řádku jsou seskupena podle rodin sloupců. HBase je schemaless databáze ve smyslu, že před jejich použitím není třeba definovat sloupce ani v nich uložený typ dat. Kód open-source se škáluje lineárně pro manipulaci s petabajty dat na tisících uzlech. HBase můžete spoléhat na redundanci dat, dávkové zpracování a další funkce, které jsou poskytovány pomocí distribuovaných aplikací v ekosystému Hadoop.   

HBase je vynikající cíl pro data ze senzorů a protokolu pro pozdější analýzu.

Škálovatelnost HBase je závislý na počtu uzlů v clusteru HDInsight.

### <a name="azure-sql-database-and-azure-database"></a>Azure SQL Database a Azure Database

Azure nabízí tři různé relační databáze jako platform-as-a-service (PAAS).

* [Azure SQL Database](../../sql-database/sql-database-technical-overview.md) je implementace systému Microsoft SQL Server. Další informace o výkonu, naleznete v tématu [ladění výkonu ve službě Azure SQL Database](../../sql-database/sql-database-performance-guidance.md).
* [Azure Database for MySQL](../../mysql/overview.md) je implementace Oracle MySQL.
* [Azure Database for PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) je implementace PostgreSQL.

Tyto produkty vertikálně navýšit kapacitu, což znamená, že se upraví tak, že přidáte další procesoru a paměti.  Můžete také použít disky úrovně premium s produkty pro zajištění lepšího výkonu vstupně-výstupních operací.

## <a name="azure-analysis-services"></a>Azure Analysis Services 

Azure Analysis Services (AAS) je modul analytických dat používaných pro podporu rozhodování a obchodní analýzy, zajištění analytických dat pro obchodní sestavy a klientské aplikace jako Power BI, Excel, sestavy služby Reporting Services a další data Nástroje pro vizualizace.

Analýza datové krychle můžete škálovat tak, že změníte úrovně pro každé jednotlivé datové krychle.  Další informace najdete v tématu [ceny služby Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="extract-and-load"></a>Extrakce a načítání

Jakmile data existuje v Azure, vám pomůže mnoha služeb extrakce a načítání do jiných produktů.  HDInsight podporuje Sqoop a Flume. 

### <a name="sqoop"></a>Sqoop

Apache Sqoop je nástroj určený pro efektivní přenosu dat mezi strukturovaných, částečně strukturovaných a nestrukturovaných zdrojů. 

Sqoop používá MapReduce pro import a export dat, k poskytování paralelní operace a odolnost proti chybám.

### <a name="flume"></a>Flume

Apache Flume je distribuovaná, spolehlivé a k dispozici služba pro efektivní shromažďování, agregaci a přesouvání velkého objemu dat protokolu. Flume má jednoduché a flexibilní architekturu podle streamování datové toky. Flume je robustní a odolné proti chybám s možností vyladění spolehlivost mechanismy a mnoho mechanismů převzetí služeb při selhání a obnovení. Flume používá jednoduché extensible datový model, který umožňuje analytické aplikace v režimu online.

Apache Flume nelze použít s Azure HDInsight.  Místní instalace Hadoop můžete použít Flume k odesílání dat do objektů BLOB Azure Storage nebo Azure Data Lake Store.  Další informace najdete v tématu [pomocí Apache Flume s HDInsight](https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/).

## <a name="transform"></a>Transformace

Jakmile dat existuje ve zvolené umístění, budete muset vyčistit ho ho zkombinovat a připravený k použití konkrétní vzor.  Hive, Pig a Spark SQL jsou všechna vhodná rozhodnutí pro tento druh práce.  Všechny jsou podporované v HDInsight. 

## <a name="next-steps"></a>Další postup

* [Použití Pigu se systémem Hadoop v HDInsight](hdinsight-use-pig.md)
* [Použití Apache Hivu jako nástroj ETL](apache-hadoop-using-apache-hive-as-an-etl-tool.md) 
