---
title: Optimalizace dotazů hive v Azure HDInsight
description: Tento článek popisuje, jak optimalizovat vaše apache hive dotazy pro Hadoop v HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: 4955df718dcc8f169232052979ccf4a636c3be80
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390302"
---
# <a name="optimize-apache-hive-queries-in-azure-hdinsight"></a>Optimalizace dotazů Apache Hivu v Azure HDInsightu

V Azure HDInsight existuje několik typů clusterů a technologií, které můžou spouštět dotazy Apache Hive. Zvolte příslušný typ clusteru, který vám pomůže optimalizovat výkon pro vaše potřeby pracovního vytížení.

Chcete-li například optimalizovat pro `ad hoc`interaktivní dotazy, zvolte typ clusteru **interaktivních dotazů.** Zvolte typ clusteru Apache **Hadoop** pro optimalizaci pro dotazy Hive používané jako dávkový proces. **Typy** clusterů Spark a **HBase** můžou taky spouštět dotazy Hive. Další informace o spouštění dotazů Hive na různých typech clusterů HDInsight najdete v tématu [Co je Apache Hive a HiveQL na Azure HDInsight?](hadoop/hdinsight-use-hive.md)

Clustery HDInsight typu clusteru Hadoop nejsou ve výchozím nastavení optimalizovány pro výkon. Tento článek popisuje některé nejběžnější metody optimalizace výkonu Hive, které můžete použít na vaše dotazy.

## <a name="scale-out-worker-nodes"></a>Horizontální navýšení kapacity pracovních uzlů

Zvýšení počtu pracovních uzlů v clusteru HDInsight umožňuje práci používat více mapovačů a reduktorů, které mají být spuštěny paralelně. Existují dva způsoby, jak zvýšit kapacitu v HDInsight:

* Při vytváření clusteru můžete určit počet pracovních uzlů pomocí portálu Azure, Azure PowerShellu nebo rozhraní příkazového řádku.  Další informace najdete v tématu [Vytvoření clusterů HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Následující snímek obrazovky ukazuje konfiguraci pracovního uzlu na webu Azure Portal:
  
    ![Uzly velikosti clusteru Portálu Azure](./media/hdinsight-hadoop-optimize-hive-query/azure-portal-cluster-configuration.png "scaleout_1")

* Po vytvoření můžete také upravit počet pracovních uzlů pro další horizontální navýšení kapacity clusteru bez opětovného vytvoření:

    ![Velikost clusteru škálování portálu Azure](./media/hdinsight-hadoop-optimize-hive-query/azure-portal-settings-nodes.png "scaleout_2")

Další informace o škálování HDInsightu najdete v [tématu Škálování clusterů HDInsight](hdinsight-scaling-best-practices.md)

## <a name="use-apache-tez-instead-of-map-reduce"></a>Použijte Apache Tez místo Map Reduce

[Apache Tez](https://tez.apache.org/) je alternativní spuštění motoru MapReduce motoru. Clustery HDInsight založené na Linuxu mají tez ve výchozím nastavení povolené.

![Diagram přehledu HDInsight Apache Tez](./media/hdinsight-hadoop-optimize-hive-query/hdinsight-tez-engine.png)

Tez je rychlejší, protože:

* **Spusťte řízený acyklický graf (DAG) jako jednu úlohu v modulu MapReduce**. Dag vyžaduje, aby každá sada mapovačů následovala jedna sada reduktorů. Tento požadavek způsobí, že více MapReduce úlohy, které mají být spun off pro každý dotaz Hive. Tez nemá takové omezení a může zpracovat komplexní DAG jako jednu úlohu, která minimalizuje počáteční nároky na spuštění úlohy.
* **Zabraňuje zbytečným zápisům**. Více úloh se používají ke zpracování stejného dotazu Hive v Modulu MapReduce. Výstup každé úlohy MapReduce je zapsán do HDFS pro zprostředkující data. Vzhledem k tomu, že Tez minimalizuje počet úloh pro každý dotaz Hive, je schopen vyhnout se zbytečným zápisům.
* **Minimalizuje zpoždění při spuštění**. Tez je lépe schopen minimalizovat zpoždění start-up snížením počtu mapovačů, které potřebuje ke spuštění a také zlepšením optimalizace v celém textu.
* **Opakovaně používá nádoby**. Kdykoli je to možné, Tez znovu použije kontejnery, aby zajistil, že latence od spuštění kontejnerů je snížena.
* **Techniky průběžné optimalizace**. Tradičně optimalizace byla provedena během fáze kompilace. Nicméně další informace o vstupy je k dispozici, které umožňují lepší optimalizaci za běhu. Tez používá techniky kontinuální optimalizace, které mu umožňují optimalizovat plán dále do fáze runtime.

Další informace o těchto konceptech naleznete v [tématu Apache TEZ](https://tez.apache.org/).

Libovolný dotaz Hive Tez můžete povolit předponou dotazu pomocí následujícího příkazu set:

```hive
set hive.execution.engine=tez;
```

## <a name="hive-partitioning"></a>Dělení úlu

Vstupně-no operace jsou hlavní problémové místo výkonu pro spuštění dotazů Hive. Výkon lze zlepšit, pokud lze snížit množství dat, která je třeba číst. Ve výchozím nastavení dotazy Hive prohledává celé tabulky Hive. Však pro dotazy, které stačí skenovat malé množství dat (například dotazy s filtrováním), toto chování vytváří zbytečné režie. Dělení hive umožňuje dotazům Hive přístup pouze k potřebnému množství dat v tabulkách Hive.

Dělení hive je implementováno reorganizací nezpracovaných dat do nových adresářů. Každý oddíl má svůj vlastní adresář souborů. Dělení je definováno uživatelem. Následující diagram znázorňuje rozdělení tabulky Hive podle sloupce *Year*. Pro každý rok je vytvořen nový adresář.

![HDInsight Apache Hive dělení](./media/hdinsight-hadoop-optimize-hive-query/hdinsight-partitioning.png)

Některé aspekty dělení:

* **Nepod oddíl** - dělení na sloupce s pouze několik hodnot může způsobit několik oddílů. Například dělení na pohlaví vytvoří pouze dva oddíly, které mají být vytvořeny (muž a žena), takže snížit latenci maximálně o polovinu.
* **Nepřekažovat oddíl** - Na druhé straně extrému vytvoření oddílu na sloupec s jedinečnou hodnotou (například userid) způsobí více oddílů. Přes oddíl způsobuje velké napětí na název uzlu clusteru, protože má zpracovat velký počet adresářů.
* **Vyhněte se zkosení dat** – zvolte klíč dělení moudře tak, aby všechny oddíly byly rovnoměrné velikosti. Například dělení na *stav* sloupec může zkreslit distribuci dat. Vzhledem k tomu, že stát Kalifornie má populaci téměř 30x více než Vermont, velikost oddílu je potenciálně zkosená a výkon se může výrazně lišit.

Chcete-li vytvořit tabulku oddílů, použijte *klauzuli Partitioned By:*

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

* **Statické dělení** znamená, že již máte rozdělená data v příslušných adresářích. U statických oddílů přidáte oddíly Hive ručně na základě umístění adresáře. Následující fragment kódu je příkladem.
  
   ```sql
   INSERT OVERWRITE TABLE lineitem_part
   PARTITION (L_SHIPDATE = '5/23/1996 12:00:00 AM')
   SELECT * FROM lineitem
   WHERE lineitem.L_SHIPDATE = '5/23/1996 12:00:00 AM'

   ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = '5/23/1996 12:00:00 AM')
   LOCATION 'wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
   ```

* **Dynamické dělení** znamená, že chcete, aby Hive vytvářet oddíly automaticky pro vás. Vzhledem k tomu, že jste již vytvořili tabulku dělení z pracovní tabulky, stačí vložit data do dělené tabulky:
  
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

Další informace naleznete v [tématu Partitioned Tables](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

## <a name="use-the-orcfile-format"></a>Použití formátu ORCFile

Hive podporuje různé formáty souborů. Příklad:

* **Text**: výchozí formát souboru a pracuje s většinou scénářů.
* **Avro**: funguje dobře pro scénáře interoperability.
* **ORC/Parkety:** nejvhodnější pro výkon.

Formát ORC (Optimized Row Columnar) je vysoce efektivní způsob ukládání dat Hive. Ve srovnání s jinými formáty, ORC má následující výhody:

* podpora pro komplexní typy včetně DateTime a komplexní a polostrukturované typy.
* až 70% komprese.
* indexuje každých 10 000 řádků, které umožňují přeskakování řádků.
* významného poklesu spuštění za běhu.

Chcete-li povolit formát ORC, nejprve vytvořte tabulku s klauzulí *Uloženo jako ORC*:

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

Dále vložíte data do tabulky ORC z pracovní tabulky. Příklad:

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

Více informací o formátu ORC si můžete přečíst v [příručce Apache Hive Language Manual](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

## <a name="vectorization"></a>Vektorizace

Vektorizace umožňuje Hive zpracovat dávku 1024 řádků společně namísto zpracování jeden řádek najednou. To znamená, že jednoduché operace jsou prováděny rychleji, protože je třeba spustit méně interního kódu.

Povolení vektorizace předponu dotaz Ujetí pomocí následujícího nastavení:

```hive
set hive.vectorized.execution.enabled = true;
```

Další informace naleznete v tématu [Vectorized query execution](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).

## <a name="other-optimization-methods"></a>Jiné optimalizační metody

Existuje více optimalizačních metod, které můžete zvážit, například:

* **Hive bucketing:** technika, která umožňuje clusterovat nebo segmentovat velké sady dat pro optimalizaci výkonu dotazu.
* **Optimalizace spojení:** optimalizace plánování provádění dotazů hive ke zlepšení efektivity spojení a snížení potřeby uživatelských rad. Další informace naleznete v [tématu Optimalizace spojení](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
* **Zvyšte reduktory**.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli několik běžných metod optimalizace dotazů Hive. Další informace naleznete v následujících článcích:

* [Použití Apache Hive v HDInsight](hadoop/hdinsight-use-hive.md)
* [Analýza dat zpoždění letu pomocí interaktivního dotazu v HDInsightu](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
* [Analyzujte data z Twitteru pomocí Apache Hive v HDInsightu](hdinsight-analyze-twitter-data-linux.md)
