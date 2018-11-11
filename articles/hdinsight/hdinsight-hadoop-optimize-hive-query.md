---
title: Optimalizace dotazů Hive v Azure HDInsight
description: Tento článek popisuje, jak optimalizovat dotazy Apache Hive pro Hadoop v HDInsight.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 6729a0e3ccbb96dc178925bbab4cfbf8189c4a14
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51278255"
---
# <a name="optimize-hive-queries-in-azure-hdinsight"></a>Optimalizace dotazů Hive v Azure HDInsight

V Azure HDInsight existuje několik typů clusteru a technologie, které můžete spouštět dotazy Apache Hive. Při vytváření clusteru HDInsight, zvolte typ příslušného clusteru za účelem optimalizace výkonu pro potřeby vašich úloh. 

Například zvolte **Interactive Query** typ optimalizace pro ad-hoc, interaktivních dotazů clusteru. Zvolte Apache **Hadoop** typ optimalizace dotazů Hive používá jako dávkové zpracování clusteru. **Spark** a **HBase** typy clusteru můžete také spouštět dotazy Hive. Další informace o spouštění dotazů Hive na různé typy clusterů HDInsight, naleznete v tématu [co je Apache Hive a HiveQL v Azure HDInsight?](hadoop/hdinsight-use-hive.md).

Clustery HDInsight Hadoop cluster typu nejsou optimalizovány pro výkon ve výchozím nastavení. Tento článek popisuje některé z nejběžnější metody optimalizace výkonu Hive, které můžete provést u vašich dotazů.

## <a name="scale-out-worker-nodes"></a>Horizontálně navýšit kapacitu pracovních uzlů

Zvyšování počtu pracovních uzlů v clusteru služby HDInsight umožňuje práce, která využívají více mapovačů a reduktorů běžet paralelně. Existují dva způsoby, jak můžete zvýšit horizontální navýšení kapacity v HDInsight:

* V době, kdy vytvořit cluster můžete zadat počet uzlů pracovního procesu pomocí webu Azure portal, prostředí Azure PowerShell nebo rozhraní příkazového řádku.  Další informace najdete v tématu [Vytvoření clusterů HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Následující snímek obrazovky ukazuje pracovní proces konfigurace uzlu na portálu Azure portal:
  
    ![scaleout_1][image-hdi-optimize-hive-scaleout_1]
    
* Po vytvoření můžete také upravit počet uzlů pracovního procesu pro horizontální navýšení kapacity clusteru dál bez nutnosti opětovného vytvoření jednoho:

    ![scaleout_1][image-hdi-optimize-hive-scaleout_2]

Další informace o škálování HDInsight najdete v tématu [HDInsight škálování clusterů](hdinsight-scaling-best-practices.md)

## <a name="use-tez-instead-of-map-reduce"></a>Používat Tez místo Map Reduce

[Apache Tez](http://hortonworks.com/hadoop/tez/) je alternativní spouštěcí modul pro modul MapReduce. Clustery se systémem Linux HDInsight mají Tez ve výchozím nastavení povolená.

![tez_1][image-hdi-optimize-hive-tez_1]

Tez je rychlejší, protože:

* **Spustit orientovaného Acyklického grafu (DAG) jako jednu úlohu v modulu MapReduce**. Orientovaného acyklického grafu vyžaduje každá sada mapovače následovat jednu sadu reduktorů. To způsobí, že více úloh MapReduce se podařilo pro každý dotaz Hive. Tez nemá žádné takové omezení a zpracovávat složité DAG jako jedna úloha tedy i režijní náklady na spuštění úlohy.
* **Zabraňuje zbytečným zápisy**. Více úloh se používají ke zpracování stejný dotaz Hive v modul MapReduce. Výstup každé úlohy MapReduce je pro dočasných dat zapisovat do rozhraní HDFS. Protože Tez minimalizuje počet úloh pro každý dotaz Hive, je možné vyhnout se zbytečné zápisy.
* **Minimalizuje zpoždění spuštění**. Tez je lépe Minimalizovat zpoždění spuštění snížením počtu mapovačů, je potřeba spustit a vylepšuje optimalizace v průběhu.
* **Opětovně používá kontejnery**. Vždy, když je možné Tez mohli znovu použít kontejnerů a ujistěte se, že se snižuje latenci kvůli spouštění kontejnerů.
* **Techniky průběžné optimalizace**. Optimalizace tradičně bylo provedeno během fáze kompilace. Ale je k dispozici další informace o vstupy, které umožňují lepší optimalizace za běhu. Tez využívá i metody průběžné optimalizaci umožňujících optimalizaci plán dále do fáze modulu runtime.

Další informace o těchto konceptech najdete v tématu [Apache TEZ](http://hortonworks.com/hadoop/tez/).

Můžete vytvořit jakýkoli dotaz Hive Tez povolené vložením prefixu dotazu pomocí následujícího příkazu set:

   ```hive
   set hive.execution.engine=tez;
   ```

## <a name="hive-partitioning"></a>Hive, vytváření oddílů

Vstupně-výstupní operace jsou hlavní výkonu kritickým bodem pro spouštění dotazů Hive. Výkon lze zvýšit, pokud lze snížit množství dat, která potřebuje ke čtení. Ve výchozím nastavení dotazy Hive skenování celé tabulky Hive. Ale pro dotazy, které stačí ke skenování malé množství dat (například dotazů s filtrováním), toto chování vytvoří zbytečné režie. Hive dělení umožňuje dotazů Hive pro přístup k pouze nezbytné množství dat v tabulkách Hivu.

Dělení Hive je implementováno uspořádání nezpracovaných dat do nového adresáře. Každý oddíl má svůj vlastní adresář souboru. Rozdělení je definovaný uživatelem. Následující diagram znázorňuje rozdělení podle sloupce tabulky Hive *rok*. Vytvoří nový adresář pro každý rok.

![Hive, vytváření oddílů][image-hdi-optimize-hive-partitioning_1]

Dělení zvážit:

* **Proveďte není v rámci oddílu** -dělení na sloupcích s pouze několik hodnot může způsobit, že několik oddílů. Například dělení na pohlaví vytvoří pouze dva oddíly, které chcete vytvořit (mužského a Ženský), tedy pouze snižuje latenci maximální výše polovinu.
* **Proveďte nikoli prostřednictvím oddílu** – na jiné extreme, vytváření oddílů u sloupce s jedinečnou hodnotu (například ID uživatele) způsobí, že několik oddílů. V oddílu způsobí mnoho zátěže namenode clusteru je na něm ke zpracování velkého počtu adresářů.
* **Vyhněte se Nerovnoměrná distribuce dat** – zvolte klíč dělení dobře tak, že všechny oddíly jsou i velikost. Například dělení na *stavu* sloupce mohou zkosení distribuci dat. Protože stát Kalifornia má populace téměř 30 x, že z Vermont, potenciálně je zešikmená velikost vytvoření oddílu a výkonu může výrazně lišit.

Chcete-li vytvořit tabulku oddílů, použijte *dělené podle* klauzule:

   ```hive
   CREATE TABLE lineitem_part
       (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
        L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
        L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
        L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING, 
        L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT STRING)
   PARTITIONED BY(L_SHIPDATE STRING)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
   STORED AS TEXTFILE;
   ```
   
Jakmile vytvoříte dělenou tabulku, můžete buď vytvořit dělení statické nebo dynamické dělení na oddíly.

* **Statické dělení** znamená, že máte již horizontálně dělených dat v příslušné adresáře. Statické oddílů přidáte ručně v závislosti na umístění adresáře oddíly Hive. Následující fragment kódu je příklad.
  
   ```hive
   INSERT OVERWRITE TABLE lineitem_part
   PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’)
   SELECT * FROM lineitem 
   WHERE lineitem.L_SHIPDATE = ‘5/23/1996 12:00:00 AM’
   
   ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’))
   LOCATION ‘wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
   ```
   
* **Dynamické dělení na oddíly** znamená, že chcete Hive vytvoření oddílů automaticky za vás. Vzhledem k tomu, že jste již vytvořili dělení tabulky z pracovní tabulky, je vše, co musíte udělat, vložte data do dělené tabulky:
  
   ```hive
   SET hive.exec.dynamic.partition = true;
   SET hive.exec.dynamic.partition.mode = nonstrict;
   INSERT INTO TABLE lineitem_part
   PARTITION (L_SHIPDATE)
   SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY , 
       L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
       L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
       L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as L_RETURNFLAG,
       L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as L_SHIPDATE_PS,
       L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as L_RECEIPTDATE,
       L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as L_SHIPMODE, 
       L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;
   ```
   
Další informace najdete v tématu [dělené tabulky](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

## <a name="use-the-orcfile-format"></a>Použijte formát souborů Orc
Hive podporuje různé formáty souborů. Příklad:

* **Text**: výchozí formát souboru a spolupracuje s většinou scénářů
* **Avro**: funguje dobře pro scénáře interoperability
* **ORC/Parquet**: nejvhodnější pro výkon

Formát ORC (optimalizované řádek úložiště se sloupcovou strukturou) je velmi efektivní způsob, jak ukládat Hive data. Porovnání do jiných formátů, ORC má následující výhody:

* Podpora pro komplexní typy, včetně data a času a částečně strukturovaných a komplexní typy
* až o 70 % komprese
* indexuje každých 10 000 řádky, které umožňují přeskočí řádky
* výrazné snížení výkonu za běhu

Pokud chcete povolit formátu ORC, nejprve vytvoříte tabulku s klauzulí *uložené jako ORC*:

   ```hive
   CREATE TABLE lineitem_orc_part
       (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
        L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
        L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
        L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
        L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT      STRING)
   PARTITIONED BY(L_SHIPDATE STRING)
   STORED AS ORC;
   ```
   
V dalším kroku vložit data do tabulky ORC z pracovní tabulky. Příklad:

   ```hive
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
   ```
   
Další informace na formát ORC v [Hive jazyk ruční](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

## <a name="vectorization"></a>Vektorizace

Vektorizace umožňuje Hive ke zpracování dávku řádků 1024 společně, namísto zpracování jeden řádek v čase. Znamená to, že jednoduché operace jsou rychlejší dokončení, protože méně vnitřní kód potřebuje ke spuštění.

Aby vektorizace předpony dotaz Hive s následujícím nastavením:

   ```hive
    set hive.vectorized.execution.enabled = true;
   ```

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
