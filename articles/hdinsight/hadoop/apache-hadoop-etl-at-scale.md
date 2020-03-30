---
title: Extrahovat, transformovat a načíst (ETL) ve škále – Azure HDInsight
description: Zjistěte, jak se extrakt, transformace a zatížení používají v HDInsight u Apache Hadoop.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/03/2020
ms.openlocfilehash: f4be3343f090c4d31ccb85eba8e99f22a3b1fcae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529471"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Extrahovat, transformovat a načíst (ETL) v měřítku

Extrahovat, transformovat a načíst (ETL) je proces, kterým jsou data získávána z různých zdrojů, shromažďovány ve standardním umístění, vyčištěny a zpracovány a nakonec načteny do úložiště dat, ze kterého může být dotazován. Starší ETL zpracovává data importu, vyčistěte je na místě a pak je uložte do modulu relačních dat. S HDInsight podporuje široká škála ekosystémových komponent Apache Hadoop provádění ETL ve velkém měřítku.

Použití HDInsight v procesu ETL lze shrnout tímto kanálem:

![HDInsight ETL ve velkém měřítku přehled](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

Následující části popisují každou z fází ETL a jejich přidružené součásti.

## <a name="orchestration"></a>Orchestraci

Orchestrace zahrnuje všechny fáze kanálu ETL. Úlohy ETL v HDInsight často zahrnují několik různých produktů, které pracují ve vzájemnéspolupráci.  Můžete použít Hive k čištění některé části dat, zatímco Pig vyčistí jinou část.  Azure Data Factory můžete použít k načtení dat do Azure SQL Database z Azure Data Lake Store.

Orchestrace je potřeba spustit příslušnou úlohu ve vhodnou dobu.

### <a name="apache-oozie"></a>Apač Oozie

Apache Oozie je systém koordinace pracovních postupů, který spravuje úlohy systému Hadoop. Oozie běží v clusteru HDInsight a je integrován se zásobníkem Hadoop. Oozie podporuje úlohy Hadoop pro Apache Hadoop MapReduce, Apache Pig, Apache Hive a Apache Sqoop. Oozie lze také naplánovat úlohy, které jsou specifické pro systém, jako jsou programy Java nebo shell skripty.

Další informace najdete [v tématu Použití Apache Oozie s Apache Hadoop definovat a spustit pracovní postup na HDInsight](../hdinsight-use-oozie-linux-mac.md) Pro podrobné prohloubení ukazuje, jak používat Oozie řídit end-to-end potrubí, viz [Operationalize data pipeline](../hdinsight-operationalize-data-pipeline.md).

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory poskytuje možnosti orchestrace ve formě platformy jako služby. Jedná se o cloudovou službu integrace dat, která umožňuje vytvářet pracovní postupy řízené daty v cloudu pro orchestraci a automatizaci pohybu dat a transformace dat.

Pomocí Azure Data Factory můžete:

1. Vytvářejte a plánujte pracovní postupy řízené daty (nazývané kanály), které ingestují data z nesourodých úložišť dat.
2. Zpracovávejte a transformujte data pomocí výpočetních služeb, jako jsou Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics, Azure Batch a Azure Machine Learning.
3. Publikovat výstupní data do úložišť dat, jako je Azure SQL Data Warehouse, a umožnit jejich využití v aplikacích business intelligence (BI).

Další informace o Azure Data Factory najdete v [dokumentaci](../../data-factory/introduction.md).

## <a name="ingest-file-storage-and-result-storage"></a>Ingestování úložiště souborů a výsledků

Zdrojové datové soubory se obvykle načítají do umístění ve službě Azure Storage nebo Azure Data Lake Storage. Soubory mohou být v libovolném formátu, ale obvykle se nepovažují za ploché soubory, jako jsou soubory CSV.

### <a name="azure-storage"></a>Azure Storage

[Azure Storage](https://azure.microsoft.com/services/storage/blobs/) má konkrétní cíle škálovatelnosti. Další informace najdete v tématu [Škálovatelnost a cíle výkonu pro úložiště objektů Blob](../../storage/blobs/scalability-targets.md). Pro většinu analytických uzlů azure storage škáluje nejlepší při práci s mnoha menšími soubory.  Azure Storage zaručuje stejný výkon, bez ohledu na to, kolik souborů nebo jak velké soubory (pokud jste v rámci svých limitů).  To znamená, že můžete ukládat terabajty dat a stále získat konzistentní výkon, ať už používáte podmnožinu dat nebo všechna data.

Azure Storage má několik různých typů objektů BLOB.  Objekt *blob připojení* je skvělou volbou pro ukládání webových protokolů nebo dat senzorů.  

Více objektů BLOB lze distribuovat na mnoha serverech, aby se k nim vertikálně rozšířil přístup, ale jeden objekt blob může obsluhovat jenom jeden server. Zatímco objekty BLOB lze logicky seskupit v kontejnerech objektů blob, neexistují žádné důsledky dělení z tohoto seskupení.

Azure Storage má taky vrstvu rozhraní API WebHDFS pro úložiště objektů blob.  Všechny služby v HDInsight přístup k souborům v Úložišti objektů blob Azure pro čištění dat a zpracování dat, podobně jako jak tyto služby by používat Hadoop Distributed Files System (HDFS).

Data se obvykle ingestuje do Azure Storage pomocí PowerShellu, Azure Storage SDK nebo AZCopy.

### <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

Azure Data Lake Storage (ADLS) je spravované úložiště hyperškálování pro analytická data, která jsou kompatibilní s HDFS.  ADLS používá paradigma návrhu, který je podobný HDFS a nabízí neomezenou škálovatelnost z hlediska celkové kapacity a velikosti jednotlivých souborů. ADLS je velmi dobrá při práci s velkými soubory, protože velký soubor může být uložen ve více uzlech.  Dělení dat v ADLS se provádí na pozadí.  Získáte obrovskou propustnost pro spuštění analytických úloh s tisíci souběžných vykonavatelů, kteří efektivně čtou a zapisují stovky terabajtů dat.

Data se obvykle ingestuje do ADLS pomocí Azure Data Factory, ADLS SDK, AdlCopy Service, Apache DistCp nebo Apache Sqoop.  Které z těchto služeb, které mají být používány, do značné míry závisí na tom, kde jsou data.  Pokud jsou data aktuálně v existujícím clusteru Hadoop, můžete použít Apache DistCp, AdlCopy Service nebo Azure Data Factory.  Pokud je ve službě Azure Blob Storage, můžete použít Azure Data Lake Storage .NET SDK, Azure PowerShell nebo Azure Data Factory.

ADLS je také optimalizované pro ingestování událostí pomocí Azure Event Hub nebo Apache Storm.

#### <a name="considerations-for-both-storage-options"></a>Důležité informace pro obě možnosti úložiště

Pro nahrávání datových sad v rozsahu terabajtů může být latence sítě velkým problémem, zejména pokud data pocházejí z místního umístění.  V takových případech můžete použít níže uvedené možnosti:

* Azure ExpressRoute: Azure ExpressRoute umožňuje vytvářet privátní připojení mezi datovými centry Azure a místní infrastrukturou. Tato připojení poskytují spolehlivou možnost přenosu velkého množství dat. Další informace naleznete v [dokumentaci k Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

* "Offline" nahrávání dat. [Službu Azure Import/Export](../../storage/common/storage-import-export-service.md) můžete použít k dopolette pevné disky s daty do datového centra Azure. Vaše data se nejprve nahrají do objektů Blob s úložištěm Azure. Potom můžete použít [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md) nebo nástroj [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md) ke kopírování dat z objektů BLOB Azure Storage do úložiště datových jezer.

### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Azure SQL DW je skvělou volbou pro ukládání vyčištěných a připravených výsledků pro budoucí analýzy.  Azure HDInsight se dá použít k provádění těchto služeb pro Azure SQL DW.

Azure SQL Data Warehouse (SQL DW) je relační databázové úložiště optimalizované pro analytické úlohy.  Škály Azure SQL DW založené na dělených tabulkách.  Tabulky lze rozdělit mezi více uzlů.  Uzly Azure SQL DW jsou vybrány v době vytvoření.  Mohou škálovat po faktu, ale to je aktivní proces, který může vyžadovat přesun dat. Další informace naleznete v tématu [SQL Data Warehouse - Správa výpočetních prostředků](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).

### <a name="apache-hbase"></a>Apache HBase

Apache HBase je úložiště s klíči dostupné v Azure HDInsight.  Apache HBase je NoSQL databáze typu open source, která je založena na Hadoop a modelována podle Google BigTable. HBase poskytuje výkonný náhodný přístup a silnou konzistenci pro velké množství nestrukturovaných a polostrukturovaných dat v databázi bez schématu uspořádané podle rodin sloupců.

Data se ukládají na řádky tabulky a data v řádku jsou seskupena podle rodin sloupců. HBase je schemaless databáze ve smyslu, že před jejich použitím není třeba definovat sloupce ani v nich uložený typ dat. Kód open-source se škáluje lineárně pro manipulaci s petabajty dat na tisících uzlech. HBase se může spolehnout na redundanci dat, dávkové zpracování a další funkce, které poskytují distribuované aplikace v ekosystému Hadoop.

HBase je vynikající destinací pro data senzorů a protokolů pro budoucí analýzu.

Škálovatelnost HBase závisí na počtu uzlů v clusteru HDInsight.

### <a name="azure-sql-database-and-azure-database"></a>Azure SQL Database a databáze Azure

Azure nabízí tři různé relační databáze jako platformu jako službu (PAAS).

* [Azure SQL Database](../../sql-database/sql-database-technical-overview.md) je implementace Microsoft SQL Serveru. Další informace o výkonu najdete [v tématu Optimalizace výkonu v Azure SQL Database](../../sql-database/sql-database-performance-guidance.md).
* [Azure Database for MySQL](../../mysql/overview.md) je implementace Oracle MySQL.
* [Azure Database pro PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) je implementace PostgreSQL.

Tyto produkty navýšit kapacitu, což znamená, že jsou škálovány přidáním další procesor a paměť.  Můžete také použít prémiové disky s produkty pro lepší výkon vstupně-va.

## <a name="azure-analysis-services"></a>Azure Analysis Services

Azure Analysis Services (AAS) je analytický datový modul používaný v podpoře rozhodování a obchodní analýze, který poskytuje analytická data pro obchodní sestavy a klientské aplikace, jako jsou power bi, excel, sestavy reportingových služeb a další data. vizualizačních nástrojů.

Datové krychle analýzy lze škálovat změnou úrovní pro každou jednotlivou krychli.  Další informace najdete v tématu [Azure Analysis Services Pricing](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="extract-and-load"></a>Extrahovat a načíst

Jakmile data existují v Azure, můžete použít mnoho služeb k extrahování a načtení do jiných produktů.  HDInsight podporuje Sqoop a Flume.

### <a name="apache-sqoop"></a>Apačský sqoop

Apache Sqoop je nástroj určený pro efektivní přenos dat mezi strukturovanými, částečně strukturovanými a nestrukturovanými zdroji dat.

Sqoop používá MapReduce k importu a exportu dat, k zajištění paralelních operací a odolnosti proti chybám.

### <a name="apache-flume"></a>Apache Flume

Apache Flume je distribuovaná, spolehlivá a dostupná služba pro efektivní shromažďování, agregaci a přesouvání velkého množství dat protokolu. Flume má jednoduchou a flexibilní architekturu založenou na streamovaných datových tocích. Flume je robustní a odolný proti chybám s laditelnými mechanismy spolehlivosti a mnoha mechanismy převzetí služeb při selhání a obnovy. Flume používá jednoduchý rozšiřitelný datový model, který umožňuje online analytické aplikace.

Apache Flume se nedá používat s Azure HDInsight.  Místní instalace Hadoopu může použít Flume k odesílání dat do objektů Blob s azure storage nebo Azure Data Lake Storage.  Další informace naleznete [v tématu Using Apache Flume with HDInsight](https://web.archive.org/web/20190217104751/https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/).

## <a name="transform"></a>Transformace

Jakmile data existují ve zvoleném umístění, je třeba je vyčistit, zkombinovat nebo připravit na konkrétní vzor použití.  Hive, Pig a Spark SQL jsou všechny dobré volby pro tento druh práce.  Všechny jsou podporovány na HDInsight.

## <a name="next-steps"></a>Další kroky

* [Použití Apache Hive jako nástroje ETL](apache-hadoop-using-apache-hive-as-an-etl-tool.md)
* [Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Přesunutí dat z databáze Azure SQL do tabulky Apache Hive](./apache-hadoop-use-sqoop-mac-linux.md)
