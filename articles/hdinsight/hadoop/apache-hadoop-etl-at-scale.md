---
title: Extrakce, transformace a načítání (ETL) ve velkém měřítku – Azure HDInsight
description: Přečtěte si, jak se v HDInsight s Apache Hadoop používá extrakce, transformace a načtení.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: fc488cecb762ff13bcb2999bb06521ebb70b44fb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946583"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Extrakce, transformace a načítání (ETL) ve velkém měřítku

Extrakce, transformace a načítání (ETL) je proces, při kterém jsou data získána z různých zdrojů. Data se shromažďují ve standardním umístění, vyčištěno a zpracovává se. Data jsou nakonec načtena do úložiště dat, ze kterého lze dotazovat. Starší verze procesů ETL importuje data, čistí je a pak je uloží do relačního datového stroje. S Azure HDInsight podporuje rozsáhlá škála Apache Hadoopch komponent prostředí ETL ve velkém měřítku.

Použití HDInsight v procesu ETL je shrnuto pomocí tohoto kanálu:

![Přehled ETL HDInsight v škálování](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

Následující části obsahují všechny fáze ETL a jejich přidružené součásti.

## <a name="orchestration"></a>Orchestrace

Orchestrace se rozpíná napříč všemi fázemi kanálu ETL. Úlohy ETL v HDInsight často zahrnují více různých produktů, které spolu vzájemně spolupracují. Příklad:

- Můžete použít Apache Hive k vyčištění části dat a Apache prasete k vyčištění další části.
- Pomocí Azure Data Factory můžete načíst data do Azure SQL Database z Azure Data Lake Store.

Orchestrace je nutná ke spuštění příslušné úlohy ve vhodnou dobu.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie je systém koordinace pracovních postupů, který spravuje úlohy systému Hadoop. Oozie běží v rámci clusteru HDInsight a je integrovaný do zásobníku Hadoop. Oozie podporuje úlohy Hadoop pro Apache Hadoop MapReduce, prase, podregistr a Sqoop. Oozie můžete použít k plánování úloh, které jsou specifické pro systém, jako jsou programy Java nebo skripty prostředí.

Další informace najdete v tématu [použití Apache Oozie s Apache Hadoop k definování a spuštění pracovního postupu v HDInsight](../hdinsight-use-oozie-linux-mac.md). Přečtěte si také téma [zprovoznění data Pipeline](../hdinsight-operationalize-data-pipeline.md).

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory poskytuje možnosti orchestrace ve formě platformy jako služby (PaaS). Azure Data Factory je cloudová datová integrační služba. Umožňuje vytvářet pracovní postupy řízené daty pro orchestraci a automatizaci přesunu dat a transformaci dat.

Použít Azure Data Factory k:

1. Vytvářejte a naplánujte pracovní postupy řízené daty. Tyto kanály ingestují data z různorodých úložišť dat.
1. Zpracujte a Transformujte data pomocí výpočetních služeb, jako je HDInsight nebo Hadoop. Pro tento krok můžete použít také Spark, Azure Data Lake Analytics, Azure Batch nebo Azure Machine Learning.
1. Publikujte výstupní data do úložišť dat, jako je Azure synapse Analytics, pro využívání aplikací BI.

Další informace o Azure Data Factory najdete v [dokumentaci](../../data-factory/introduction.md).

## <a name="ingest-file-storage-and-result-storage"></a>Ingestování úložiště souborů a výsledků úložiště výsledků

Zdrojové datové soubory se obvykle načítají do umístění na Azure Storage nebo Azure Data Lake Storage. Soubory jsou obvykle v nestrukturovaném formátu, jako je CSV. Ale můžou být v jakémkoli formátu.

### <a name="azure-storage"></a>Azure Storage

Azure Storage má specifické cíle přizpůsobení. Další informace najdete v tématu [škálovatelnost a výkonnostní cíle pro úložiště objektů BLOB](../../storage/blobs/scalability-targets.md) . U většiny analytických uzlů Azure Storage lépe škálovat při práci s mnoha menšími soubory. Pokud jste v rámci omezení účtu, Azure Storage garantuje stejný výkon bez ohledu na to, jak velké jsou soubory. Můžete ukládat terabajty dat a stále dosáhnout konzistentního výkonu. Tento příkaz je pravdivý bez ohledu na to, zda používáte podmnožinu nebo všechna data.

Azure Storage má několik typů objektů BLOB. *Doplňovací objekt BLOB* je skvělou možností pro ukládání webových protokolů nebo dat ze senzorů.

Pro horizontální navýšení kapacity přístupu může být víc objektů BLOB distribuované napříč mnoha servery. Jeden objekt BLOB ale obsluhuje jenom jeden server. I když se objekty blob dají logicky seskupovat v kontejnerech objektů blob, neexistují žádné důsledky dělení z tohoto seskupení.

Azure Storage má vrstvu rozhraní API WebHDFS pro úložiště objektů BLOB. Všechny služby HDInsight mají přístup k souborům v úložišti objektů BLOB v Azure pro čištění a zpracování dat. To se podobá tomu, jak by tyto služby používaly Hadoop systém souborů DFS (Distributed File System) (HDFS).

Data se obvykle ingestují do Azure Storage prostřednictvím PowerShellu, Azure Storage SDK nebo AZCopy.

### <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

Azure Data Lake Storage je spravované úložiště s škálovatelným škálováním pro analytické údaje. Je kompatibilní s a používá paradigmata návrhu, která je podobná HDFS. Data Lake Storage nabízí neomezenou přizpůsobivost pro celkovou kapacitu a velikost jednotlivých souborů. Je vhodná volba při práci s velkými soubory, protože mohou být uloženy mezi více uzly. Dělení dat v Data Lake Storage se provádí na pozadí. Získáte obrovské propustnost pro spouštění analytických úloh s tisíci souběžnými prováděcími moduly, které efektivně čtou a zapisují stovky terabajtů dat.

Data se obvykle ingestují do Data Lake Storage prostřednictvím Azure Data Factory. Můžete také použít sady SDK Data Lake Storage, službu AdlCopy, Apache DistCp nebo Apache Sqoop. Služba, kterou zvolíte, závisí na tom, kde jsou data. Pokud je v existujícím clusteru Hadoop, můžete použít Apache DistCp, službu AdlCopy nebo Azure Data Factory. Pro data v úložišti objektů BLOB v Azure můžete použít Azure Data Lake Storage .NET SDK, Azure PowerShell nebo Azure Data Factory.

Data Lake Storage je optimalizovaná pro přijímání událostí prostřednictvím Azure Event Hubs nebo Apache Storm.

### <a name="considerations-for-both-storage-options"></a>Předpoklady pro obě možnosti úložiště

Pro nahrávání datových sad v rozsahu terabajtů může být latence sítě významným problémem. To platí zejména v případě, že data přicházejí z místního umístění. V takových případech můžete použít tyto možnosti:

- **ExpressRoute Azure:** Vytvářejte privátní připojení mezi datacentry Azure a místní infrastrukturou. Tato připojení poskytují spolehlivou možnost pro přenos velkých objemů dat. Další informace najdete v [dokumentaci ke službě Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

- **Nahrávání dat z jednotek pevného disku:** [Službu Azure import/export](../../import-export/storage-import-export-service.md) můžete použít k dodávání pevných diskových jednotek s daty do datacentra Azure. Vaše data se napřed nahrají do úložiště objektů BLOB v Azure. Pak můžete pomocí Azure Data Factory nebo nástroje AdlCopy kopírovat data ze služby Azure Blob Storage do Data Lake Storage.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

Azure synapse Analytics je vhodná volba k uložení připravených výsledků. Azure HDInsight můžete použít k provádění těchto služeb pro Azure synapse Analytics.

Azure synapse Analytics je relační úložiště databáze optimalizované pro analytické úlohy. Škáluje se v závislosti na dělených tabulkách. Tabulky mohou být rozděleny mezi více uzlů. Uzly jsou vybrány v době vytváření. Můžou se škálovat po faktu, ale to je aktivní proces, který může vyžadovat přesun dat. Další informace najdete v tématu [Správa výpočetních prostředků ve službě Azure synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).

### <a name="apache-hbase"></a>Apache HBase

Apache HBA je úložiště typu klíč/hodnota dostupné v Azure HDInsight. Je to open source databáze NoSQL, která je postavená na Hadoop a modelována po Google BigTable. HBA poskytují náhodný přístup a silnou konzistenci pro velké objemy nestrukturovaných a částečně strukturovaných dat.

Protože HBA je databáze bez schématu, nemusíte definovat sloupce a datové typy předtím, než je použijete. Data jsou uložena v řádcích tabulky a jsou seskupena podle řady sloupců.

Kód open-source se škáluje lineárně pro manipulaci s petabajty dat na tisících uzlech. HBA spoléhají na redundanci dat, dávkové zpracování a další funkce, které jsou poskytovány distribuovanými aplikacemi v prostředí Hadoop.

HBA je dobrým cílem pro data senzorů a protokolů pro účely budoucí analýzy.

Přizpůsobitelnost HBA závisí na počtu uzlů v clusteru HDInsight.

### <a name="azure-sql-databases"></a>Databáze SQL Azure

Azure nabízí tři relační databáze PaaS:

* [Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md) je implementace Microsoft SQL Server. Další informace o výkonu najdete v tématu [optimalizace výkonu v Azure SQL Database](../../azure-sql/database/performance-guidance.md).
* [Azure Database for MySQL](../../mysql/overview.md)  je implementace Oracle MySQL.
* [Azure Database for PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) je implementace PostgreSQL.

Přidejte další procesor a paměť pro horizontální navýšení kapacity těchto produktů.  Můžete také použít prémiové disky s produkty pro lepší výkon vstupně-výstupních operací.

## <a name="azure-analysis-services"></a>Azure Analysis Services

Azure Analysis Services je analytický datový stroj, který se používá v rozhodovací podpoře a obchodní analýze. Poskytuje analytické údaje pro obchodní sestavy a klientské aplikace, jako je například Power BI. Analytická data fungují také v aplikacích Excel, SQL Server Reporting Services sestavách a dalších nástrojích pro vizualizaci dat.

Škálujte datové krychle analýz změnou vrstev pro každou jednotlivou datovou krychli. Další informace najdete v tématu [Azure Analysis Services ceny](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="extract-and-load"></a>Extrahovat a načíst

Jakmile data v Azure existují, můžete k jejich extrakci a načtení do jiných produktů použít spoustu služeb. HDInsight podporuje Sqoop a Flume.

### <a name="apache-sqoop"></a>Apache Sqoop

Apache Sqoop je nástroj určený k efektivnímu přenosu dat mezi strukturovanými, částečně strukturovanými a nestrukturovanými zdroji dat.

Sqoop používá MapReduce k importu a exportu dat, aby se zajistila paralelní operace a odolnost proti chybám.

### <a name="apache-flume"></a>Apache Flume

Apache Flume je distribuovaná, spolehlivá a dostupná služba pro efektivní shromažďování, agregaci a přesouvání velkých objemů dat protokolů. Jeho flexibilní architektura vychází z toků streamování dat. Flume je robustní a odolný proti chybám a mechanismy přizpůsobitelné spolehlivosti. Má mnoho mechanismů pro převzetí služeb při selhání a obnovení. Flume používá jednoduchý rozšiřitelný datový model, který umožňuje online a analytickou aplikaci.

Apache Flume se nedá použít se službou Azure HDInsight. Místní instalace Hadoop ale může používat Flume k posílání dat do služby Azure Blob Storage nebo Azure Data Lake Storage. Další informace najdete v tématu [použití Apache Flume se službou HDInsight](https://web.archive.org/web/20190217104751/https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/).

## <a name="transform"></a>Transformace

Jakmile data ve zvoleném umístění existují, musíte je vyčistit, zkombinovat nebo připravit na konkrétní vzor použití. Pro tento druh práce jsou k dispozici všechny vhodné možnosti pro podregistr, prasata a Spark SQL. Jsou všechny podporované v HDInsight.

## <a name="next-steps"></a>Další kroky

- [Použití Apache Hive jako nástroje ETL](apache-hadoop-using-apache-hive-as-an-etl-tool.md)
- [Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
- [Přesun dat z Azure SQL Database do tabulky Apache Hive](./apache-hadoop-use-sqoop-mac-linux.md)
