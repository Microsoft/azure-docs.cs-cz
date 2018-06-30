---
title: Extrakce, transformace a načítání (ETL) ve velkém měřítku - Azure HDInsight | Microsoft Docs
description: Zjistěte, jak je ETL používanou v HDInsight se systémem Hadoop.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 9314b8ae8dceb29a9aee63ba3093ab191d4fdef3
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37115965"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Extrakce, transformace a načítání (ETL) ve velkém měřítku

Extrakce, transformace a načítání (ETL) je proces, pomocí kterého dat je získali z různých zdrojů, v standardní umístění, čištění a zpracování a nakonec načtena do úložiště, ze kterého může být dotazován. Starší verze procesů ETL importovat data, vyčistit ho na místě a pak jej uložit v modul relačních dat. S HDInsight podporují celou řadu součástí ekosystému Hadoop provádění ETL ve velkém měřítku. 

Použití služby HDInsight v procesu ETL jde vyhodnotit pomocí tohoto kanálu:

![Přehled HDInsight ETL](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

V následujících částech prozkoumejte všechny fáze ETL a jejich přidružených součásti.

## <a name="orchestration"></a>Orchestrace

Orchestration platí pro všechny fáze kanálu ETL. Úlohy ETL v prostředí HDInsight často zahrnují několik různých produktů ve spolupráci mezi sebou.  Hive můžete použít pro čištění některé část dat, zatímco Pig vyčistí další část.  Azure Data Factory můžete použít k načtení dat do databáze SQL Azure z Azure Data Lake Store.

Orchestration je potřebné ke spuštění příslušné úlohy v příslušnou dobu.

### <a name="oozie"></a>Oozie

Apache Oozie je systém koordinace pracovního postupu, který spravuje úloh Hadoop. Oozie běží v clusteru služby HDInsight a je integrována do zásobníku Hadoop. Oozie podporuje úlohy systému Hadoop pro Apache MapReduce, Apache Pig, Apache Hive a Apache Sqoop. Oozie lze také plánování úloh, které jsou specifické pro systém, jako jsou programy v jazyce Java nebo skripty prostředí.

Další informace najdete v tématu [Oozie použití se systémem Hadoop k definování a spuštění workflowu v HDInsight](../hdinsight-use-oozie-linux-mac.md) podrobné informace znázorňující způsob použití Oozie k řízení kanál začátku do konce, najdete v části [zprovoznit datovém kanálu](../hdinsight-operationalize-data-pipeline.md). 

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory poskytuje možnosti orchestration ve formě platforma jako služba. Je služba integrace cloudové data, která vám umožní vytvořit datové pracovních postupů v cloudu pro orchestruje a automatizuje přesouvání dat a dat transformaci. 

Pomocí Azure Data Factory, můžete:

1. Vytvoření a plánování řízené daty pracovních postupů (nazývané kanály) které ingestují data z různorodých datových úložišť.
2. Proces a transformace dat pomocí výpočetních služeb, například Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics, Azure Batch a Azure Machine Learning.
3. Publikovat výstupní data do úložišť dat, jako je Azure SQL Data Warehouse, a umožnit jejich využití v aplikacích business intelligence (BI).

Další informace o Azure Data Factory najdete v tématu [dokumentaci](../../data-factory/introduction.md).

## <a name="ingest-file-storage-and-result-storage"></a>Ingestování úložiště souborů a úložiště výsledků

Zdroj datové soubory jsou obvykle načíst do umístění v Azure Storage nebo Azure Data Lake Store. Soubory mohou být v libovolném formátu, ale obvykle jsou plochých souborů jako sdílené svazky clusteru. 

### <a name="azure-storage"></a>Azure Storage 

[Úložiště Azure](https://azure.microsoft.com/services/storage/blobs/) má [cíle škálovatelnosti konkrétní](../../storage/common/storage-scalability-targets.md).  Azure Storage nejvíce analytické uzly škáluje nejlépe při plánování práce s mnoha menších souborů.  Úložiště Azure zaručuje stejného výkonu, bez ohledu na to, kolik souborů nebo jak velkých souborů (pokud jsou v rámci vaší limitů).  To znamená, že můžete ukládat terabajtů dat a stále získat konzistentní výkon, ať používáte podmnožinu dat nebo všechna data.

Úložiště Azure má několik různých typů objektů BLOB.  *Připojit blob* představuje skvělou možnost pro ukládání webových protokolů nebo data snímačů.  

Více objektů BLOB mohou být distribuovány na mnoha serverech chcete škálovat. přístup k nim, ale jediného objektu blob může obsluhovat pouze jeden server. Zatímco objekty BLOB mohou být logicky seskupeny do kontejnery objektů blob, neexistují žádné rozdělení dopady z toto seskupení.

Úložiště Azure má také vrstvu rozhraní API webhdfs, které pro úložiště objektů blob.  Všechny služby v HDInsight, můžete přístup k souborům v Azure Blob Storage pro čištění dat a zpracování dat, podobně jako na tom, jak by tyto služby používat Hadoop Distributed soubory System (HDFS).

Data je obvykle konzumována do Azure Storage pomocí prostředí PowerShell, sada SDK úložiště Azure nebo AZCopy.

### <a name="azure-data-lake-store"></a>Azure Data Lake Store

Azure Data Lake Store (ADLS) je spravovaná, hyperškálovatelný systém úložiště pro analýzy dat, který je kompatibilní s HDFS.  ADLS používá zlepší návrh, který je podobný HDFS a nabízí neomezená škálovatelnost z hlediska celkové kapacity a velikosti jednotlivých souborů. Při práci s velkých souborů, vzhledem k tomu, že mezi několika uzly mohou být uloženy velký soubor, je velmi dobré ADLS.  Segmentace dat v ADLS probíhá na pozadí.  Získáte mimořádně velkou propustnost pro spouštění analytických úloh s tisíci souběžnými vykonavateli, kteří efektivně čtou a zapisují stovky terabajtů dat.

Data je obvykle nasávaného ADLS pomocí Azure Data Factory, sady SDK ADLS, AdlCopy služby, Apache DistCp nebo Apache Sqoop.  Tyto služby používat do značné míry závisí na kterém jsou data.  Pokud data je aktuálně v existující cluster Hadoop, můžete použít Apache DistCp, AdlCopy služby nebo Azure Data Factory.  Pokud je v Azure Blob Storage, můžete použít .NET SDK služby Azure Data Lake Store, prostředí Azure PowerShell nebo Azure Data Factory.

ADLS je také optimalizovaná pro přijímání událostí pomocí centra událostí Azure nebo Apache Storm.

#### <a name="considerations-for-both-storage-options"></a>Důležité informace pro obě možnosti úložiště

Nahrát datové sady v rozsahu terabajt, latence sítě může být hlavní problém, zejména v případě, že data pochází z místní umístění.  V takových případech můžete pomocí následujících možností:

* Azure ExpressRoute: Azure ExpressRoute vám umožňuje vytvářet privátní připojení mezi datovými centry Azure a v místní infrastruktuře. Tato připojení poskytují spolehlivé možnost pro přenos velkých objemů dat. Další informace najdete v tématu [dokumentace Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

* "Do režimu offline" nahrát data. Můžete použít [služba Azure Import/Export](../../storage/common/storage-import-export-service.md) pro odeslání jednotky pevného disku s daty pro datové centrum Azure. Vaše data, je nejprve nahrán do úložiště objektů BLOB služby Azure. Pak můžete použít [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md) nebo [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md) nástroj pro kopírování dat z Azure úložiště objektů BLOB do Data Lake Store.

### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Azure SQL DW je skvělou volbou pro ukládání čištění a připravené výsledky pro budoucí analýzu.  Azure HDInsight lze použít k provedení těchto služeb pro Azure SQL DW.

Azure SQL Data Warehouse (SQL DW) je optimalizovaná pro analytické úlohy úložiště relační databáze.  Azure SQL DW škáluje na základě na dělené tabulky.  Mezi několika uzly můžou být dělené tabulky.  Azure SQL DW uzly jsou vybrány v době vytvoření.  Můžete škálovat ve skutečnosti, ale který je aktivní proces, který může vyžadovat přesun dat. V tématu [SQL Data Warehouse – spravovat výpočetní](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md) Další informace.

### <a name="hbase"></a>HBase

Apache HBase je k dispozici v Azure HDInsight úložiště dvojic klíč hodnota.  Apache HBase je NoSQL databáze typu open source, která je založena na Hadoop a modelována podle Google BigTable. HBase poskytuje původce náhodný přístup a silnou konzistenci pro velké objemy nestrukturovaných a částečně strukturovaných dat v databázi schemaless uspořádané podle rodin sloupců.

Data se ukládají na řádky tabulky a data v řádku jsou seskupena podle rodin sloupců. HBase je schemaless databáze ve smyslu, že před jejich použitím není třeba definovat sloupce ani v nich uložený typ dat. Kód open-source se škáluje lineárně pro manipulaci s petabajty dat na tisících uzlech. HBase můžete spoléhat na redundanci dat, zpracování dávky a další funkce, které jsou poskytovány pomocí distribuovaných aplikací v ekosystému Hadoop.   

HBase je vynikající cíl pro data snímačů a protokolu pro budoucí analýzu.

Škálovatelnost HBase je závislý na počtu uzlů v clusteru HDInsight.

### <a name="azure-sql-database-and-azure-database"></a>Azure SQL Database a Azure databáze

Azure nabízí tři různé relační databáze jako platforma jako služba (PAAS).

* [Azure SQL Database](../../sql-database/sql-database-technical-overview.md) je implementace systému Microsoft SQL Server. Další informace o výkonu, najdete v části [ladění výkonu v Azure SQL Database](../../sql-database/sql-database-performance-guidance.md).
* [Azure databáze pro databázi MySQL](../../mysql/overview.md) je implementací Oracle MySQL.
* [Azure databázi PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) je implementací PostgreSQL.

Tyto produkty vertikálně navýšit kapacitu, což znamená, že jsou vážena přidáním další procesoru a paměti.  Můžete také použít prémiové disky s produkty pro lepší výkon vstupně-výstupní operace.

## <a name="azure-analysis-services"></a>Azure Analysis Services 

Azure Analysis Services (jak) je modul analytických dat použít v rozhodnutí podporu a obchodní analýza, poskytne analytická data pro obchodní sestavy a klientské aplikace jako Power BI, Excel, sestavy služby Reporting Services a další data Nástroje pro vizualizaci.

Analýza datové krychle, můžete škálovat změnou úrovně každé jednotlivé datové krychle.  Další informace najdete v tématu [ceník služby Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="extract-and-load"></a>Extrakce a zatížení

Po data existuje v Azure, můžete extrahovat a načtěte ho do jiné produkty mnoho služeb.  HDInsight podporuje Sqoop a Flume. 

### <a name="sqoop"></a>Sqoop

Apache Sqoop je nástroj navržený pro efektivní přenosu dat mezi zdroji strukturovaná, částečně strukturovaná i nestrukturovaná data. 

Sqoop používá MapReduce k importu a exportu dat, zajistit paralelní operace a odolnost proti chybám.

### <a name="flume"></a>Flume

Apache Flume je distribuované, spolehlivým a k dispozici služba pro efektivní shromažďování, agregace a přesouvání velkých objemů dat protokolu. Flume má jednoduchá a flexibilní architekturu podle streamování datové toky. Flume je robustní a odolný proti chybám s mechanismy přizpůsobitelné spolehlivost a mnoho mechanismy převzetí služeb při selhání a obnovení. Flume používá jednoduchý extensible datového modelu, který umožňuje online analytické aplikaci.

Apache Flume nelze použít s Azure HDInsight.  Hadoop místní instalace můžete použít Flume k odesílání dat do úložiště objektů BLOB služby Azure nebo Azure Data Lake Store.  Další informace najdete v tématu [pomocí Apache Flume s HDInsight](https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/).

## <a name="transform"></a>Transformace

Po dat existuje ve zvolené umístění, budete muset vyčistit ho, ho spojovat nebo jeho přípravu pro konkrétní využití vzoru.  Hive, Pig a Spark SQL se všechny velmi dobře hodí pro tento druh práce.  Že jsou všechny podporované v HDInsight. 

## <a name="next-steps"></a>Další postup

* [Použijte Pig s Hadoop v HDInsight](hdinsight-use-pig.md)
* [Používání Apache Hive jako nástroj ETL](apache-hadoop-using-apache-hive-as-an-etl-tool.md) 
