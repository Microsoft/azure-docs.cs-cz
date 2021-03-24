---
title: Optimalizace dotazů na podregistry ve službě Azure HDInsight
description: Tento článek popisuje, jak optimalizovat dotazy Apache Hive ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 10/28/2020
ms.openlocfilehash: 551d985ea78e83397e507676c5fd7ecfce12ff7b
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864240"
---
# <a name="optimize-apache-hive-queries-in-azure-hdinsight"></a>Optimalizace dotazů Apache Hivu v Azure HDInsightu

Tento článek popisuje některé z nejběžnějších optimalizací výkonu, které můžete použít ke zlepšení výkonu Apache Hive dotazů.

## <a name="cluster-type-selection"></a>Výběr typu clusteru

V Azure HDInsight můžete spouštět dotazy Apache Hive na několika různých typech clusterů. 

Vyberte vhodný typ clusteru, který vám umožní optimalizovat výkon vašich potřeb pro úlohy:

* Vyberte typ clusteru **interaktivních dotazů** , který se má optimalizovat pro `ad hoc` interaktivní dotazy. 
* Vyberte typ clusteru Apache **Hadoop** pro optimalizaci pro dotazy na podregistr používané jako dávkové zpracování. 
* Typy clusterů **Spark** a **HBA** můžou také spouštět dotazy na podregistr a můžou být vhodné, pokud tyto úlohy spouštíte. 

Další informace o spouštění dotazů na podregistry na různých typech clusterů HDInsight najdete v tématu [co je Apache Hive a HiveQL v Azure HDInsight](hadoop/hdinsight-use-hive.md).

## <a name="scale-out-worker-nodes"></a>Horizontální navýšení kapacity pracovních uzlů

Zvýšení počtu pracovních uzlů v clusteru HDInsight umožňuje práci používat více mapovačů a reduktorů spustit paralelně. Existují dva způsoby, jak můžete rozšířit horizontální navýšení kapacity v HDInsight:

* Při vytváření clusteru můžete zadat počet pracovních uzlů pomocí rozhraní Azure Portal, Azure PowerShell nebo rozhraní příkazového řádku.  Další informace najdete v tématu [Vytvoření clusterů HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Následující snímek obrazovky ukazuje konfiguraci uzlu pracovní proces na Azure Portal:
  
    :::image type="content" source="./media/hdinsight-hadoop-optimize-hive-query/azure-portal-cluster-configuration.png" alt-text="Uzly Azure Portal velikosti clusteru":::

* Po vytvoření můžete také upravit počet pracovních uzlů pro další horizontální navýšení kapacity clusteru, aniž by bylo nutné ho znovu vytvořit:

    :::image type="content" source="./media/hdinsight-hadoop-optimize-hive-query/azure-portal-settings-nodes.png " alt-text="Velikost clusteru Azure Portal škálování":::

Další informace o škálování HDInsight najdete v tématu [škálování clusterů HDInsight](hdinsight-scaling-best-practices.md) .

## <a name="use-apache-tez-instead-of-map-reduce"></a>Použít Apache Tez místo zmenšení mapy

[Apache tez](https://tez.apache.org/) je alternativní spouštěcí modul pro modul MapReduce. Clustery HDInsight se systémem Linux mají ve výchozím nastavení povolené TEZ.

:::image type="content" source="./media/hdinsight-hadoop-optimize-hive-query/hdinsight-tez-engine.png" alt-text="Diagram přehledu Apache Tez HDInsight":::

Tez je rychlejší z těchto důvodů:

* **Spusťte acyklického graf (DAG) jako jednu úlohu v modulu MapReduce**. DAG vyžaduje, aby na každé sadě mapovačů následovala jedna sada reduktorů. Tento požadavek způsobí, že pro každý dotaz na podregistr se má vypínat více úloh MapReduce. Tez nemá takové omezení a může zpracovat komplexní DAG jako jednu úlohu, která minimalizuje náklady na spuštění úlohy.
* **Nepoužívejte zbytečné zápisy**. Ke zpracování stejného dotazu podregistru v modulu MapReduce se používá víc úloh. Výstup každé úlohy MapReduce se zapisuje do HDFS pro mezilehlé údaje. Vzhledem k tomu, že tez minimalizuje počet úloh pro každý dotaz na podregistr, je možné vyhnout se zbytečnému zápisu.
* **Minimalizuje prodlevy při spuštění**. Tez je lepším řešením pro minimalizaci počáteční prodlevy tím, že snižuje počet mapovačů, které musí spustit, a také zlepšuje optimalizaci v celém systému.
* Znovu **použije kontejnery**. Kdykoli je to možné, tez znovu použije kontejnery, aby se snížila latence od spuštění kontejnerů.
* **Techniky plynulé optimalizace**. Během fáze kompilace byla provedena tradičně optimalizace. K dispozici jsou ale další informace o vstupech, které umožňují lepší optimalizaci během běhu. Tez využívá techniky kontinuální optimalizace, které umožňují IT optimalizaci plánu dále do fáze modulu runtime.

Další informace o těchto konceptech najdete v článku [Apache tez](https://tez.apache.org/).

Pomocí příkazu set můžete vytvořit libovolný dotaz tez s podregistrumi, a to tak, že zadáte předponu dotazu:

```hive
set hive.execution.engine=tez;
```

## <a name="hive-partitioning"></a>Dělení podregistru

Vstupně-výstupní operace jsou zásadním kritickým bodem pro spouštění dotazů na podregistr. Výkon lze zvýšit, pokud množství dat, která je třeba číst, může být sníženo. Ve výchozím nastavení dotazy na podregistr prohledají celé tabulky podregistru. V případě dotazů, které potřebují pouze prohledávání malých objemů dat (například dotazů s filtrováním), ale toto chování vytváří zbytečné režijní náklady. Dělení podregistru umožňuje dotazům na podregistr přístup pouze k potřebným objemům dat v tabulkách podregistru.

Dělení oddílů se implementuje změnou uspořádání nezpracovaných dat do nových adresářů. Každý oddíl má svůj vlastní adresář souborů. Dělení je definováno uživatelem. Následující diagram znázorňuje rozdělení tabulky podregistru podle sloupce *year*. Vytvoří se nový adresář pro každý rok.

:::image type="content" source="./media/hdinsight-hadoop-optimize-hive-query/hdinsight-partitioning.png" alt-text="Apache Hive vytváření oddílů v HDInsight":::

Některé předpoklady dělení:

* V části dělení na **oddíly** na sloupcích, které mají jenom několik hodnot, může dojít k několika oddílům. Například při vytváření oddílů u pohlaví se vytvoří jenom dva oddíly, které se mají vytvořit (muž a žena), takže se latence omezí na maximálně polovinu.
* **Nepoužívejte oddíl** – na druhé extrémní straně vytvoření oddílu na sloupec s jedinečnou hodnotou (například UserID) způsobí více oddílů. Po oddílech se cluster namenode mnohem zátěží, protože je potřeba zvládnout velký počet adresářů.
* **Vyhněte se zkosení dat** – vyberte klíč pro dělení, aby bylo možné všechny oddíly rovnoměrně měnit. Například vytváření oddílů ve sloupci *stav* může zkosit distribuci dat. Vzhledem k tomu, že stát Kalifornie má naplnění skoro 30krát jako Vermont, velikost oddílu je potenciálně zkosená a výkon se může výrazně lišit.

Chcete-li vytvořit tabulku oddílů, použijte klauzuli *partitiond by* :

```sql
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

Po vytvoření dělené tabulky můžete vytvořit statické dělení nebo dynamické dělení.

* **Statické dělení** znamená, že už jste horizontálně dělené data v příslušných adresářích. Při statických oddílech se oddíly podregistru přidávají ručně na základě umístění adresáře. Následující fragment kódu je příklad.
  
   ```sql
   INSERT OVERWRITE TABLE lineitem_part
   PARTITION (L_SHIPDATE = '5/23/1996 12:00:00 AM')
   SELECT * FROM lineitem
   WHERE lineitem.L_SHIPDATE = '5/23/1996 12:00:00 AM'

   ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = '5/23/1996 12:00:00 AM')
   LOCATION 'wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
   ```

* **Dynamické dělení** znamená, že chcete, aby podregistr vytvořil oddíly automaticky. Vzhledem k tomu, že jste už vytvořili dělenou tabulku z pracovní tabulky, stačí vložit data do dělené tabulky:
  
   ```hive
   SET hive.exec.dynamic.partition = true;
   SET hive.exec.dynamic.partition.mode = nonstrict;
   INSERT INTO TABLE lineitem_part
   PARTITION (L_SHIPDATE)
   SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY,
       L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
       L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
       L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as L_RETURNFLAG,
       L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as L_SHIPDATE_PS,
       L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as L_RECEIPTDATE,
       L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as L_SHIPMODE,
       L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;
   ```

Další informace najdete v tématu [dělené tabulky](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

## <a name="use-the-orcfile-format"></a>Použití formátu ORCFile

Podregistr podporuje různé formáty souborů. Například:

* **Text**: výchozí formát souboru a funguje ve většině scénářů.
* **Avro**: funguje dobře pro scénáře interoperability.
* **ORC/Parquet**: nejvhodnější pro výkon.

ORC (optimalizovaný sloupcový řádek) je vysoce účinný způsob, jak ukládat data z podregistru. Ve srovnání s jinými formáty ORC má následující výhody:

* Podpora komplexních typů, včetně hodnot DateTime a složitých a částečně strukturovaných typů.
* až 70% komprese.
* indexuje každých 10 000 řádků, což povoluje přeskočení řádků.
* významné přerušení spuštění.

Pokud chcete povolit formát ORC, musíte nejdřív vytvořit tabulku s klauzulí *uloženou jako ORC*:

```sql
CREATE TABLE lineitem_orc_part
      (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
      L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
      L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
      L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
      L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT      STRING)
PARTITIONED BY(L_SHIPDATE STRING)
STORED AS ORC;
```

V dalším kroku vložíte data do tabulky ORC z pracovní tabulky. Například:

```sql
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

Další informace o formátu ORC najdete v [příručce k jazyku Apache Hive](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

## <a name="vectorization"></a>Vektorizaci

Rozvektorování umožňuje podregistru zpracovat dávku 1024 řádků společně namísto zpracování jednoho řádku. To znamená, že jednoduché operace jsou rychlejší, protože je nutné spustit méně interní kód.

Chcete-li povolit vlastní předponu dotazu na podregistr pomocí následujícího nastavení:

```hive
set hive.vectorized.execution.enabled = true;
```

Další informace najdete v tématu [zpracování vektorového dotazu](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).

## <a name="other-optimization-methods"></a>Další metody optimalizace

Existuje více metod optimalizace, které je možné zvážit, například:

* Sestavování **podregistru:** technika, která umožňuje clusteru a segmentovat velké sady dat pro optimalizaci výkonu dotazů.
* **Optimalizace spojení:** optimalizace plánování spouštění dotazů na podregistr pro zlepšení efektivity spojení a omezení nutnosti pomocného parametru pro uživatele. Další informace najdete v tématu věnovaném [optimalizaci spojení](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
* **Zvyšte reduktorů**.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se seznámili s několika běžnými metodami optimalizace dotazů na podregistr. Další informace najdete v těchto článcích:

* [Optimalizace Apache Hivu](./optimize-hive-ambari.md)
* [Analýza dat zpoždění letů pomocí interaktivního dotazu ve službě HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
* [Analýza dat Twitteru pomocí Apache Hive ve službě HDInsight](hdinsight-analyze-twitter-data-linux.md)
