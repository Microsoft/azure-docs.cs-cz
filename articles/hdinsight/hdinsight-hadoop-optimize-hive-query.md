---
title: Optimalizace dotazů Hive v Azure HDInsight | Microsoft Docs
description: Informace o optimalizaci své dotazy Hive pro Hadoop v HDInsight.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d6174c08-06aa-42ac-8e9b-8b8718d9978e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: e844b4f1e9898347da6af589dbfe41bf2ad3ab69
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="optimize-hive-queries-in-azure-hdinsight"></a>Optimalizace dotazů Hive v Azure HDInsight

Ve výchozím nastavení nejsou clusterů systému Hadoop optimalizované pro výkon. Tento článek se zabývá některé nejběžnější Hive výkonu optimalizace metody, které můžete použít pro své dotazy.

## <a name="scale-out-worker-nodes"></a>Horizontální navýšení kapacity uzlů pracovního procesu

Zvýšit počet uzlů pracovního procesu v clusteru s podporou můžete využít další mappers a přechodky ke spuštění paralelně. Existují dva způsoby, jak můžete zvýšit možností horizontálního rozšíření kapacity v HDInsight:

* Během zřizování můžete zadat počet uzlů pracovního procesu pomocí portálu Azure, Azure PowerShell nebo rozhraní příkazového řádku pro různé platformy.  Další informace najdete v tématu [Vytvoření clusterů HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Následující snímek obrazovky ukazuje pracovního procesu konfigurace uzlu na portálu Azure:
  
    ![scaleout_1][image-hdi-optimize-hive-scaleout_1]
* Za běhu můžete taky škálovat cluster bez nutnosti opětovného vytvoření jeden:

    ![scaleout_1][image-hdi-optimize-hive-scaleout_2]

Další informace o různé virtuální počítače podporované v prostředí HDInsight najdete v tématu [HDInsight ceny](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="enable-tez"></a>Povolení Tez

[Apache Tez](http://hortonworks.com/hadoop/tez/) je modul alternativní provádění k modulu MapReduce:

![tez_1][image-hdi-optimize-hive-tez_1]

Tez je rychlejší, protože:

* **Spuštění směrované Acyklické grafu (DAG) jako jednu úlohu v modulu MapReduce**. DAG vyžaduje každou sadu mappers pro jednu sadu přechodky následovat. To způsobí, že se pro každý dotaz Hive spuštěné více úloh MapReduce. Tez nemá takové omezení a může zpracovat komplexní DAG jako jednu úlohu, čímž dojde k minimalizaci režie spuštění úlohy.
* **Zabraňuje zbytečným zápisy**. Z důvodu několik úloh, které se spouštějí pro stejný dotaz Hive v rámci stroje MapReduce výstup každé úlohy zapsán do HDFS pro mezilehlá data. Vzhledem k tomu, že minimalizuje počet úloh pro každý dotaz Hive Tez je k tomu nepotřebné zápisu.
* **Minimalizuje zpoždění spuštění**. Tez je lépe minimalizovat snížení počtu mappers, musí se spustit a také vylepšení optimalizace v rámci zpoždění spuštění.
* **Opětovně používá kontejnery**. Vždy, když je možné Tez mohli znovu použít kontejnery zajistit, že se snižuje latence kvůli spuštění kontejnery.
* **Techniky průběžné optimalizace**. Optimalizace tradičně bylo provedeno během fáze kompilace. Ale je k dispozici další informace o vstupních hodnot, které umožňují lepší optimalizace za běhu. Tez používá průběžné optimalizace technik, které umožňují její Optimalizace plánu další do fáze modulu runtime.

Další informace o těchto pojmech najdete v tématu [Apache TEZ](http://hortonworks.com/hadoop/tez/).

Můžete použít jakýkoli dotaz Hive Tez povoleno pomocí prefixu dotaz pomocí tohoto nastavení:

    set hive.execution.engine=tez;

Clustery HDInsight se systémem Linux mít Tez ve výchozím nastavení povolené.


## <a name="hive-partitioning"></a>Hive, vytváření oddílů

Vstupně-výstupní operace je potíže hlavní výkonu pro spouštění dotazů Hive. Pokud se může snížit množství dat, který vyžaduje čtení, je možné zlepšit výkon. Ve výchozím nastavení kontrolu dotazů Hive celých tabulek Hive. Toto je skvělá pro dotazy jako prohledávání tabulky. Ale pro dotazy, které stačí ke kontrole malé množství dat (například dotazy s filtrování), toto chování vytvoří nepotřebné režie. Vytváření oddílů Hive umožňuje dotazů Hive přístup pouze nezbytné množství dat v tabulek Hive.

Vytváření oddílů Hive je implementováno modulem reorganizace nezpracovaná data do nového adresáře s každý oddíl má svou vlastní directory – kde je oddíl definovaných uživatelem. Následující diagram znázorňuje dělení tabulku Hive podle sloupce *roku*. Pro každý rok se vytvoří nový adresář.

![Dělení na oddíly][image-hdi-optimize-hive-partitioning_1]

Některé rozdělení aspekty:

* **Proveďte není v oddílu** – vytváření oddílů pro sloupce s pouze několik hodnot může způsobit několik oddílů. Například vytváření oddílů na pohlaví vytvoří pouze dva oddíly, které mají být vytvořené (mužského a ženského), tedy pouze snížit latenci nejvýše o polovinu.
* **Proveďte nikoli prostřednictvím oddílu** – na jiné extreme, vytváření oddílů u sloupce s jedinečnou hodnotu (například ID uživatele) způsobí, že více oddílů. V oddílu způsobí, že mnoho přízvuk na namenode clusteru jako má zpracovat velký počet adresářů.
* **Vyhněte se data zkosení** -vyberte klíč rozdělení dobře tak, aby všechny oddíly jsou i velikost. Příklad rozdělení do oddílů na *stavu* může způsobit, že počet záznamů v části kalifornské být téměř 30 x u Vermont kvůli rozdílu ve naplnění.

Chcete-li vytvořit tabulku oddílu, použijte *rozdělena na oddíly pomocí* klauzule:

    CREATE TABLE lineitem_part
        (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE            STRING, L_SHIPINSTRUCT STRING, L_SHIPMODE STRING,
         L_COMMENT STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    STORED AS TEXTFILE;

Po vytvoření oddílů tabulky, můžete buď vytvořit statické dělení nebo dynamické rozdělení.

* **Statické dělení** znamená, že máte již horizontálně dělená data v adresáři odpovídající a můžete požádat Hive oddíly ručně v závislosti na umístění adresáře. Následující fragment kódu je příklad.
  
        INSERT OVERWRITE TABLE lineitem_part
        PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’)
        SELECT * FROM lineitem 
        WHERE lineitem.L_SHIPDATE = ‘5/23/1996 12:00:00 AM’
  
        ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’))
        LOCATION ‘wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
* **Dynamické vytváření oddílů** znamená, že Hive, aby pro vás automaticky vytvořil oddíly. Vzhledem k tomu, že jsme vytvořili již rozdělení tabulky z pracovní tabulky, je potřeba udělat je vložit data do oddílů tabulky:
  
        SET hive.exec.dynamic.partition = true;
        SET hive.exec.dynamic.partition.mode = nonstrict;
        INSERT INTO TABLE lineitem_part
        PARTITION (L_SHIPDATE)
        SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY , 
             L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
              L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
             L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as           L_RETURNFLAG, L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as           L_SHIPDATE_PS, L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as      L_RECEIPTDATE, L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as      L_SHIPMODE, L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;

Další informace najdete v tématu [rozdělena na oddíly tabulky](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

## <a name="use-the-orcfile-format"></a>Použijte formát ORCFile
Hive podporuje jiné formáty souborů. Příklad:

* **Text**: Toto je výchozí formát souboru a spolupracuje s většinu scénářů
* **Avro**: funguje dobře pro scénáře interoperability
* **ORC/Parquet**: nejvhodnější pro výkon

Formát ORC (optimalizované řádek sloupcovém) je vysoce efektivní způsob, jak ukládat Hive data. Porovnání s jinými formáty, ORC má následující výhody:

* Podpora pro komplexní typy, včetně data a času a částečně strukturovaných a komplexní typy
* až 70 % komprese
* indexy každých 10 000 řádky, které povolit přeskočení řádků
* významné pokles v běhu provádění

Pokud chcete povolit ORC formátu, nejprve vytvoříte tabulku s klauzulí *uložené jako ORC*:

    CREATE TABLE lineitem_orc_part
        (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
         L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT      STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    STORED AS ORC;

V dalším kroku vkládání dat do tabulky ORC z pracovní tabulky. Příklad:

    INSERT INTO TABLE lineitem_orc
    SELECT L_ORDERKEY as L_ORDERKEY, 
           L_PARTKEY as L_PARTKEY , 
           L_SUPPKEY as L_SUPPKEY,
           L_LINENUMBER as L_LINENUMBER,
            L_QUANTITY as L_QUANTITY, 
           L_EXTENDEDPRICE as L_EXTENDEDPRICE,
           L_DISCOUNT as L_DISCOUNT,
           L_TAX as L_TAX,
           L_RETURNFLAG as L_RETURNFLAG,
           L_LINESTATUS as L_LINESTATUS,
           L_SHIPDATE as L_SHIPDATE,
           L_COMMITDATE as L_COMMITDATE,
           L_RECEIPTDATE as L_RECEIPTDATE, 
           L_SHIPINSTRUCT as L_SHIPINSTRUCT,
           L_SHIPMODE as L_SHIPMODE,
           L_COMMENT as L_COMMENT
    FROM lineitem;

Další informace o formátu ORC [zde](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

## <a name="vectorization"></a>Vectorization

Vectorization umožňuje Hive ke zpracování dávku řádků 1024 společně místo zpracování jeden řádek v čase. Znamená to, že jednoduché operace jsou rychlejší provést, protože menší interní kód je potřeba spustit.

Chcete-li povolit předpony vectorization dotaz Hive s následujícím nastavením:

    set hive.vectorized.execution.enabled = true;

Další informace najdete v tématu [Vectorized provádění dotazu](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).

## <a name="other-optimization-methods"></a>Ostatní metody optimalizace
Existují další metody optimalizace, které můžete zvážit, například:

* **Hive bucketing:** technika, který umožňuje clusteru nebo segmentovat velké nastaví dat za účelem optimalizace výkonu dotazů.
* **Připojení k optimalizaci:** optimalizace spouštění dotazů Hive na plánování ke zlepšení efektivity spojení a snížit nutnost pomocné parametry uživatele. Další informace najdete v tématu [připojení optimalizace](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
* **Zvýšit přechodky**.

## <a name="next-steps"></a>Další postup
V tomto článku jste se naučili několik běžné metody optimalizace dotazů Hive. Další informace naleznete v následujících článcích:

* [Používání Apache Hive v HDInsight](hadoop/hdinsight-use-hive.md)
* [Analýza dat zpoždění letu pomocí Hive v HDInsight](hdinsight-analyze-flight-delay-data.md)
* [Analýza dat Twitteru pomocí Hive v HDInsight](hdinsight-analyze-twitter-data.md)
* [Analýza dat snímačů v konzole dotaz Hive na Hadoop v HDInsight](hadoop/apache-hive-analyze-sensor-data.md)
* [Použijte Hive s HDInsight k analýze protokolů z webů](hadoop/apache-hive-analyze-website-log.md)

[image-hdi-optimize-hive-scaleout_1]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_1.png
[image-hdi-optimize-hive-scaleout_2]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_2.png
[image-hdi-optimize-hive-tez_1]: ./media/hdinsight-hadoop-optimize-hive-query/tez_1.png
[image-hdi-optimize-hive-partitioning_1]: ./media/hdinsight-hadoop-optimize-hive-query/partitioning_1.png
