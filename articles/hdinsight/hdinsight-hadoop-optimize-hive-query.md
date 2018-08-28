---
title: Optimalizace dotazů Hive v Azure HDInsight
description: Zjistěte, jak optimalizovat dotazy Hive pro Hadoop v HDInsight.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jasonh
ms.openlocfilehash: 1fd3ff89fc8428f03d22f4aa195dabf0e988ef57
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43106483"
---
# <a name="optimize-hive-queries-in-azure-hdinsight"></a>Optimalizace dotazů Hive v Azure HDInsight

Ve výchozím nastavení nejsou clustery Hadoop optimalizované pro výkon. Tento článek popisuje některé nejběžnější metody optimalizace výkonu Hive, které můžete provést u vašich dotazů.

## <a name="scale-out-worker-nodes"></a>Horizontálně navýšit kapacitu pracovních uzlů

Zvyšování počtu pracovních uzlů v clusteru mohou využívat více mapovačů a reduktorů běžet paralelně. Existují dva způsoby, jak můžete zvýšit horizontální navýšení kapacity v HDInsight:

* Během zřizování můžete zadat počet uzlů pracovního procesu pomocí webu Azure portal, prostředí Azure PowerShell nebo rozhraní příkazového řádku pro různé platformy.  Další informace najdete v tématu [Vytvoření clusterů HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Následující snímek obrazovky ukazuje pracovní proces konfigurace uzlu na portálu Azure portal:
  
    ![scaleout_1][image-hdi-optimize-hive-scaleout_1]
* V době běhu můžete taky škálovat cluster bez nutnosti opětovného vytvoření jeden:

    ![scaleout_1][image-hdi-optimize-hive-scaleout_2]

Další informace o různých virtuálních počítačů nepodporuje HDInsight najdete v tématu [ceny HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="enable-tez"></a>Povolení Tez

[Apache Tez](http://hortonworks.com/hadoop/tez/) je alternativní spouštěcí modul pro modul MapReduce:

![tez_1][image-hdi-optimize-hive-tez_1]

Tez je rychlejší, protože:

* **Spustit orientovaného Acyklického grafu (DAG) jako jednu úlohu v modulu MapReduce**. Orientovaného acyklického grafu vyžaduje každá sada mapovače následovat jednu sadu reduktorů. To způsobí, že více úloh MapReduce se podařilo pro každý dotaz Hive. Tez nemá žádné takové omezení a zpracovávat složité DAG jako jedna úloha tedy i režijní náklady na spuštění úlohy.
* **Zabraňuje zbytečným zápisy**. Z důvodu více úloh prováděných pro stejný dotaz Hive v modulu MapReduce výstup každé úlohy zapisovat do rozhraní HDFS pro zprostředkující data. Protože Tez minimalizuje počet úloh pro každý dotaz Hive je schopni vyhnout se zbytečné zápisu.
* **Minimalizuje zpoždění spuštění**. Tez je lépe Minimalizovat zpoždění spuštění snížením počtu mapovačů, je potřeba spustit a vylepšuje optimalizace v průběhu.
* **Opětovně používá kontejnery**. Vždy, když je možné Tez mohli znovu použít kontejnerů a ujistěte se, že se snižuje latenci kvůli spouštění kontejnerů.
* **Techniky průběžné optimalizace**. Optimalizace tradičně bylo provedeno během fáze kompilace. Ale je k dispozici další informace o vstupy, které umožňují lepší optimalizace za běhu. Tez využívá i metody průběžné optimalizaci umožňujících optimalizaci plán dále do fáze modulu runtime.

Další informace o těchto konceptech najdete v tématu [Apache TEZ](http://hortonworks.com/hadoop/tez/).

Můžete vytvořit jakýkoli dotaz Hive Tez povolené vložením prefixu dotaz s následujícím nastavením:

    set hive.execution.engine=tez;

Clustery se systémem Linux HDInsight mají Tez ve výchozím nastavení povolená.


## <a name="hive-partitioning"></a>Hive, vytváření oddílů

Vstupně-výstupní operace je hlavní výkonu kritickým bodem pro spouštění dotazů Hive. Výkon lze zvýšit, pokud lze snížit množství dat, která potřebuje ke čtení. Ve výchozím nastavení dotazy Hive skenování celé tabulky Hive. To je skvělé pro dotazy, jako je prohledávání tabulky. Ale pro dotazy, které stačí ke skenování malé množství dat (například dotazů s filtrováním), toto chování vytvoří zbytečné režie. Hive dělení umožňuje dotazů Hive pro přístup k pouze nezbytné množství dat v tabulkách Hivu.

Dělení Hive je implementováno uspořádání nezpracovaných dat do nového adresáře s každý oddíl s vlastní adresář – ve kterém je oddíl definovaný uživatelem. Následující diagram znázorňuje rozdělení podle sloupce tabulky Hive *rok*. Vytvoří nový adresář pro každý rok.

![Vytváření oddílů][image-hdi-optimize-hive-partitioning_1]

Dělení zvážit:

* **Proveďte není v rámci oddílu** -dělení na sloupcích s pouze několik hodnot může způsobit, že několik oddílů. Například dělení na pohlaví vytvoří pouze dva oddíly, které chcete vytvořit (mužského a Ženský), tedy pouze snižuje latenci maximální výše polovinu.
* **Proveďte nikoli prostřednictvím oddílu** – na jiné extreme, vytváření oddílů u sloupce s jedinečnou hodnotu (například ID uživatele) způsobí, že několik oddílů. V oddílu způsobí mnoho zátěže namenode clusteru je na něm ke zpracování velkého počtu adresářů.
* **Vyhněte se Nerovnoměrná distribuce dat** – zvolte klíč dělení dobře tak, že všechny oddíly jsou i velikost. Příkladem je dělení na *stavu* může způsobit, že počet záznamů v Kalifornii bude téměř 30 x u Vermont kvůli rozdílům v naplnění.

Chcete-li vytvořit tabulku oddílů, použijte *dělené podle* klauzule:

    CREATE TABLE lineitem_part
        (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE            STRING, L_SHIPINSTRUCT STRING, L_SHIPMODE STRING,
         L_COMMENT STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    STORED AS TEXTFILE;

Jakmile vytvoříte dělenou tabulku, můžete buď vytvořit dělení statické nebo dynamické dělení na oddíly.

* **Statické dělení** znamená, že máte již horizontálně dělených dat v příslušné adresáře a můžete požádat o Hive oddíly ručně podle umístění adresáře. Následující fragment kódu je příklad.
  
        INSERT OVERWRITE TABLE lineitem_part
        PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’)
        SELECT * FROM lineitem 
        WHERE lineitem.L_SHIPDATE = ‘5/23/1996 12:00:00 AM’
  
        ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’))
        LOCATION ‘wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
* **Dynamické dělení na oddíly** znamená, že chcete Hive vytvoření oddílů automaticky za vás. Protože jsme už vytvořili dělení tabulky z pracovní tabulky, je vše, co musíme udělat, vložte data do dělené tabulky:
  
        SET hive.exec.dynamic.partition = true;
        SET hive.exec.dynamic.partition.mode = nonstrict;
        INSERT INTO TABLE lineitem_part
        PARTITION (L_SHIPDATE)
        SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY , 
             L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
              L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
             L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as           L_RETURNFLAG, L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as           L_SHIPDATE_PS, L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as      L_RECEIPTDATE, L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as      L_SHIPMODE, L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;

Další informace najdete v tématu [dělené tabulky](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

## <a name="use-the-orcfile-format"></a>Použijte formát souborů Orc
Hive podporuje různé formáty souborů. Příklad:

* **Text**: Toto je výchozí formát souboru a spolupracuje s většinou scénářů
* **Avro**: funguje dobře pro scénáře interoperability
* **ORC/Parquet**: nejvhodnější pro výkon

Formát ORC (optimalizované řádek úložiště se sloupcovou strukturou) je velmi efektivní způsob, jak ukládat Hive data. Porovnání do jiných formátů, ORC má následující výhody:

* Podpora pro komplexní typy, včetně data a času a částečně strukturovaných a komplexní typy
* až o 70 % komprese
* indexuje každých 10 000 řádky, které umožňují přeskočí řádky
* výrazné snížení výkonu za běhu

Pokud chcete povolit formátu ORC, nejprve vytvoříte tabulku s klauzulí *uložené jako ORC*:

    CREATE TABLE lineitem_orc_part
        (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
         L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT      STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    STORED AS ORC;

V dalším kroku vložit data do tabulky ORC z pracovní tabulky. Příklad:

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

Další informace o formátu ORC [tady](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

## <a name="vectorization"></a>Vektorizace

Vektorizace umožňuje Hive ke zpracování dávku řádků 1024 společně, namísto zpracování jeden řádek v čase. Znamená to, že jednoduché operace jsou rychlejší dokončení, protože méně vnitřní kód potřebuje ke spuštění.

Aby vektorizace předpony dotaz Hive s následujícím nastavením:

    set hive.vectorized.execution.enabled = true;

Další informace najdete v tématu [Vektorizovaná provádění dotazu](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).

## <a name="other-optimization-methods"></a>Jiné metody optimalizace
Existují další metody optimalizace, které můžete zvážit, například:

* **Hive kontejnery:** technika, která umožňuje clusteru nebo segment velkých sad dat k optimalizaci výkonu dotazů.
* **Připojte se k optimalizaci:** optimalizace provádění dotazu Hive pro plánování ke zlepšení efektivity spojení a tak snížit potřeba pomocné parametry uživatele. Další informace najdete v tématu [připojte se k optimalizaci](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
* **Zvýšit Reduktorů**.

## <a name="next-steps"></a>Další postup
V tomto článku jste se naučili několik běžných metody optimalizace dotazů Hive. Další informace naleznete v následujících článcích:

* [Použití Apache Hivu ve službě HDInsight](hadoop/hdinsight-use-hive.md)
* [Analyzovat zpoždění letů pomocí Hivu ve službě HDInsight](hdinsight-analyze-flight-delay-data.md)
* [Analýza dat Twitteru pomocí Hivu ve službě HDInsight](hdinsight-analyze-twitter-data.md)
* [Analýza dat senzoru pomocí konzoly pro dotazy Hive platformy hadoop v HDInsight](hadoop/apache-hive-analyze-sensor-data.md)
* [Použití Hivu se službou HDInsight k analýze protokolů z webů](hadoop/apache-hive-analyze-website-log.md)

[image-hdi-optimize-hive-scaleout_1]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_1.png
[image-hdi-optimize-hive-scaleout_2]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_2.png
[image-hdi-optimize-hive-tez_1]: ./media/hdinsight-hadoop-optimize-hive-query/tez_1.png
[image-hdi-optimize-hive-partitioning_1]: ./media/hdinsight-hadoop-optimize-hive-query/partitioning_1.png
