---
title: Extrakce, transformace a načítání (ETL) ve velkém měřítku – Azure HDInsight
description: Přečtěte si, jak se v HDInsight s Apache Hadoop používá extrakce, transformace a načtení.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/03/2020
ms.openlocfilehash: 8a4205002a98a5b9670839b0de7b53d81e0221a6
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2020
ms.locfileid: "78271934"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Extrakce, transformace a načítání (ETL) ve velkém měřítku

Extrakce, transformace a načítání (ETL) je proces, při kterém se data získávají z různých zdrojů, shromážděná ve standardním umístění, vyčištěná a zpracovaná a nakonec se načtou do úložiště dat, ze kterého se dá dotazovat. Starší verze procesů ETL importuje data, čistí je a pak je uloží do relačního datového stroje. S HDInsight podporuje rozsáhlá škála Apache Hadoopch komponent ekosystémů ve velkém měřítku.

Pro použití HDInsight v procesu ETL se dá vytvořit souhrn pomocí tohoto kanálu:

![Přehled ETL HDInsight v škálování](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

Následující části obsahují všechny fáze ETL a jejich přidružené součásti.

## <a name="orchestration"></a>Orchestrace

Orchestrace se rozpíná napříč všemi fázemi kanálu ETL. Úlohy ETL v HDInsight často zahrnují více různých produktů, které spolu vzájemně spolupracují.  K vyčištění některých částí dat můžete použít podregistr, zatímco prase vyčistí jinou část.  Pomocí Azure Data Factory můžete načíst data do Azure SQL Database z Azure Data Lake Store.

Orchestrace je nutná ke spuštění příslušné úlohy ve vhodnou dobu.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie je systém koordinace pracovních postupů, který spravuje úlohy Hadoop. Oozie běží v rámci clusteru HDInsight a je integrovaný do zásobníku Hadoop. Oozie podporuje úlohy Hadoop pro Apache Hadoop MapReduce, Apache prasete, Apache Hive a Apache Sqoop. Oozie lze také použít k plánování úloh, které jsou specifické pro systém, jako jsou programy Java nebo skripty prostředí.

Další informace najdete v tématu [použití nástroje Apache Oozie s Apache Hadoop k definování a spuštění pracovního postupu v HDInsight](../hdinsight-use-oozie-linux-mac.md) pro hloubkové podrobně ukazující, jak používat Oozie k řízení kompletního kanálu, najdete v tématu [zprovoznění The data Pipeline](../hdinsight-operationalize-data-pipeline.md).

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory poskytuje možnosti orchestrace v podobě platformy jako služby. Je to cloudová služba pro integraci dat, která umožňuje vytvářet pracovní postupy řízené daty v cloudu za účelem Orchestrace a automatizace přesunu dat a transformace dat.

Pomocí Azure Data Factory můžete:

1. Vytvářejte a naplánujte pracovní postupy řízené daty (nazývané kanály), které ingestují data z různorodých úložišť dat.
2. Zpracujte a Transformujte data pomocí výpočetních služeb, jako jsou Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics, Azure Batch a Azure Machine Learning.
3. Publikovat výstupní data do úložišť dat, jako je Azure SQL Data Warehouse, a umožnit jejich využití v aplikacích business intelligence (BI).

Další informace o Azure Data Factory najdete v [dokumentaci](../../data-factory/introduction.md).

## <a name="ingest-file-storage-and-result-storage"></a>Ingestování úložiště souborů a výsledků úložiště výsledků

Zdrojové datové soubory jsou obvykle načteny do umístění v Azure Storage nebo Azure Data Lake Storage. Soubory mohou být v libovolném formátu, ale obvykle se jedná o ploché soubory, jako je CSV.

### <a name="azure-storage"></a>Azure Storage

[Azure Storage](https://azure.microsoft.com/services/storage/blobs/) má specifické cíle škálovatelnosti. Další informace najdete v tématu [škálovatelnost a výkonnostní cíle pro úložiště objektů BLOB](../../storage/blobs/scalability-targets.md). U většiny analytických uzlů Azure Storage lépe škálovat při práci s mnoha menšími soubory.  Azure Storage garantuje stejný výkon, bez ohledu na počet souborů nebo velikost souborů (Pokud jste v rámci vašich limitů).  To znamená, že můžete ukládat terabajty dat a stále dosáhnout konzistentního výkonu, ať už používáte podmnožinu dat nebo všechna data.

Azure Storage má několik různých typů objektů BLOB.  *Doplňovací objekt BLOB* je skvělou možností pro ukládání webových protokolů nebo dat ze senzorů.  

Pro horizontální navýšení kapacity přístupu může být několik objektů BLOB distribuováno na více serverů, ale jeden objekt BLOB může být obsluhován pouze jediným serverem. I když se objekty blob dají logicky seskupovat v kontejnerech objektů blob, neexistují žádné důsledky dělení z tohoto seskupení.

Azure Storage má také vrstvu rozhraní API WebHDFS pro úložiště objektů BLOB.  Všechny služby v HDInsight mají přístup k souborům v Azure Blob Storage pro čištění a zpracování dat, podobně jako by tyto služby používaly systém Hadoop Distributed Files System (HDFS).

Data se obvykle ingestují do Azure Storage s využitím PowerShellu, Azure Storage SDK nebo AZCopy.

### <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

Azure Data Lake Storage (ADLS) je spravované úložiště s škálovatelným škálováním pro analytická data, která jsou kompatibilní se službou HDFS.  ADLS používá paradigma návrhu, který je podobný HDFS, a nabízí neomezenou škálovatelnost z pohledu celkové kapacity a velikosti jednotlivých souborů. ADLS je velmi dobré při práci s velkými soubory, protože velký soubor může být uložený v několika uzlech.  Dělení dat v ADLS se provádí na pozadí.  Získáte obrovské propustnost pro spouštění analytických úloh s tisíci souběžnými prováděcími moduly, které efektivně čtou a zapisují stovky terabajtů dat.

Data se obvykle ingestují do ADLS s využitím Azure Data Factory, ADLS sad SDK, AdlCopy služby, Apache DistCp nebo Apache Sqoop.  Které z těchto služeb se mají využít převážně, záleží na tom, kde jsou data.  Pokud jsou data aktuálně v existujícím clusteru Hadoop, můžete použít službu Apache DistCp, AdlCopy Service nebo Azure Data Factory.  Pokud je v Azure Blob Storage, můžete použít sadu Azure Data Lake Storage .NET SDK, Azure PowerShell nebo Azure Data Factory.

ADLS je taky optimalizovaná pro příjem událostí pomocí centra událostí Azure nebo Apache Storm.

#### <a name="considerations-for-both-storage-options"></a>Předpoklady pro obě možnosti úložiště

V případě nahrávání datových sad v rozsahu terabajtů může být latence sítě významným problémem, zejména pokud data pocházejí z místního umístění.  V takových případech můžete použít následující možnosti:

* Azure ExpressRoute: Azure ExpressRoute umožňuje vytvářet privátní připojení mezi datacentry Azure a místní infrastrukturou. Tato připojení poskytují spolehlivou možnost pro přenos velkých objemů dat. Další informace najdete v [dokumentaci ke službě Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

* "Offline" nahrávání dat. Pomocí [služby Azure import/export](../../storage/common/storage-import-export-service.md) můžete dodávat pevné disky s daty do datového centra Azure. Vaše data se napřed nahrají do Azure Storage objektů BLOB. Potom můžete pomocí [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md) nebo nástroje [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md) kopírovat data z Azure Storage objektů blob do data Lake Storage.

### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Azure SQL DW je skvělou volbou pro ukládání vyčištěných a připravených výsledků budoucích analýz.  Azure HDInsight se dá využít k provádění těchto služeb pro Azure SQL DW.

Azure SQL Data Warehouse (SQL DW) je relační úložiště databáze optimalizované pro analytické úlohy.  Azure SQL DW škáluje na základě dělených tabulek.  Tabulky mohou být rozděleny mezi více uzlů.  V době vytváření jsou vybrané uzly Azure SQL DW.  Můžou se škálovat po faktu, ale to je aktivní proces, který může vyžadovat přesun dat. Další informace najdete v tématu [SQL Data Warehouse – Správa výpočetních](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)prostředků.

### <a name="apache-hbase"></a>Apache HBase

Apache HBA je úložiště hodnot klíč-hodnota dostupné ve službě Azure HDInsight.  Apache HBase je NoSQL databáze typu open source, která je založena na Hadoop a modelována podle Google BigTable. HBA poskytují náhodný přístup a silnou konzistenci pro velké objemy nestrukturovaných a částečně strukturovaných dat v databázi bez schématu uspořádané podle rodin sloupců.

Data se ukládají na řádky tabulky a data v řádku jsou seskupena podle rodin sloupců. HBase je schemaless databáze ve smyslu, že před jejich použitím není třeba definovat sloupce ani v nich uložený typ dat. Kód open-source se škáluje lineárně pro manipulaci s petabajty dat na tisících uzlech. HBA mohou spoléhat na redundanci dat, dávkové zpracování a další funkce, které jsou poskytovány distribuovanými aplikacemi v ekosystému Hadoop.

HBA je vynikající cíl pro data senzorů a protokolů pro účely budoucí analýzy.

Škálovatelnost HBA závisí na počtu uzlů v clusteru HDInsight.

### <a name="azure-sql-database-and-azure-database"></a>Azure SQL Database a databáze Azure

Azure nabízí jako platformu jako službu (PAAS) tři různé relační databáze.

* [Azure SQL Database](../../sql-database/sql-database-technical-overview.md) je implementace Microsoft SQL Server. Další informace o výkonu najdete v tématu [optimalizace výkonu v Azure SQL Database](../../sql-database/sql-database-performance-guidance.md).
* [Azure Database for MySQL](../../mysql/overview.md) je implementace Oracle MySQL.
* [Azure Database for PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) je implementace PostgreSQL.

Tyto produkty se škálují nahoru, což znamená, že se škálují přidáním více PROCESORů a paměti.  Můžete také použít prémiové disky s produkty pro lepší výkon vstupně-výstupních operací.

## <a name="azure-analysis-services"></a>Azure Analysis Services

Azure Analysis Services (AAS) je analytický datový stroj, který se používá v rozhodovací podpoře a obchodní analýze a poskytuje analytická data pro obchodní sestavy a klientské aplikace, jako jsou Power BI, Excel, sestavy služby Reporting Services a další data. nástroje pro vizualizaci.

Datové krychle analýzy se můžou škálovat změnou vrstev pro každou jednotlivou datovou krychli.  Další informace najdete v tématu [Azure Analysis Services ceny](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="extract-and-load"></a>Extrahovat a načíst

Jakmile data v Azure existují, můžete k jejich extrakci a načtení do jiných produktů použít spoustu služeb.  HDInsight podporuje Sqoop a Flume.

### <a name="apache-sqoop"></a>Apache Sqoop

Apache Sqoop je nástroj určený k efektivnímu přenosu dat mezi strukturovanými, částečně strukturovanými a nestrukturovanými zdroji dat.

Sqoop používá MapReduce k importu a exportu dat, aby se zajistila paralelní operace a odolnost proti chybám.

### <a name="apache-flume"></a>Apache Flume

Apache Flume je distribuovaná, spolehlivá a dostupná služba pro efektivní shromažďování, agregaci a přesouvání velkých objemů dat protokolů. Flume má jednoduchou a flexibilní architekturu na základě toků streamování dat. Flume je robustní a odolný proti chybám díky mechanismům spolehlivosti přizpůsobitelné a mnoha mechanismům pro převzetí služeb při selhání a obnovení. Flume používá jednoduchý rozšiřitelný datový model, který umožňuje online analytickou aplikaci.

Apache Flume se nedá použít se službou Azure HDInsight.  Místní instalace systému Hadoop může používat Flume k posílání dat do Azure Storage objektů BLOB nebo Azure Data Lake Storage.  Další informace najdete v tématu [použití Apache Flume se službou HDInsight](https://web.archive.org/web/20190217104751/https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/).

## <a name="transform"></a>Transformace

Jakmile data ve vybraném umístění existují, je nutné je vyčistit, zkombinovat nebo připravit na konkrétní vzor použití.  Pro tento druh práce jsou k dispozici všechny vhodné možnosti pro podregistr, prasata a Spark SQL.  Jsou všechny podporované v HDInsight.

## <a name="next-steps"></a>Další kroky

* [Použití Apache Hive jako nástroje ETL](apache-hadoop-using-apache-hive-as-an-etl-tool.md)
* [Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Přesunutí dat z Azure SQL Database do tabulky Apache Hive](./apache-hadoop-use-sqoop-mac-linux.md)
