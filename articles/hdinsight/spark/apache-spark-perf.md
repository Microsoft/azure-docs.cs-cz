---
title: Optimalizace úloh Spark pro výkon – Azure HDInsight
description: Zobrazte běžné strategie pro nejlepší výkon clusterů Apache Spark v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: 3d8f4a28961be7e0ece517e00026d9711d8f67e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198867"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Optimalizace pracovních míst Apache Spark v HDInsightu

Přečtěte si, jak optimalizovat konfiguraci clusteru [Apache Spark](https://spark.apache.org/) pro konkrétní úlohu.  Nejběžnější výzvou je tlak paměti, protože nesprávné konfigurace (zejména chybné velikosti vykonavatele), dlouhotrvající operace a úkoly, které vedou k operacím kartézy. Můžete urychlit úlohy s vhodným ukládáním do mezipaměti a povolením [zkosení dat](#optimize-joins-and-shuffles). Pro nejlepší výkon, monitorování a kontrolu dlouhotrvající a prostředky náročné spuštění úloh y Spark. Informace o tom, jak začít s Apache Spark na HDInsight, najdete v [tématu Vytvoření clusteru Apache Spark pomocí portálu Azure](apache-spark-jupyter-spark-sql-use-portal.md).

V následujících částech jsou popsány běžné optimalizace úloh a doporučení spark.

## <a name="choose-the-data-abstraction"></a>Výběr abstrakce dat

Dřívější verze Spark používají RDD k abstraktním datům, Spark 1.3 a 1.6 představil dataframes a datasets, resp. Zamyslete se nad následujícími relativními přednostmi:

* **Datové rámce**
    * Nejlepší volba ve většině situací.
    * Poskytuje optimalizaci dotazů prostřednictvím catalyst.
    * Generování kódu celé fáze.
    * Přímý přístup do paměti.
    * Nízká režie uvolňování paměti (GC).
    * Není tak vhodné pro vývojáře jako datasets, protože neexistují žádné kontroly v době kompilace nebo programování objektů domény.
* **Soubory**
    * Dobré v komplexních kanálech ETL, kde je přijatelný dopad na výkon.
    * Není dobré v agregacích, kde dopad na výkon může být značný.
    * Poskytuje optimalizaci dotazů prostřednictvím catalyst.
    * Pro vývojáře poskytuje programování objektů domény a kontroly v době kompilace.
    * Přidá režii serializace/deserializace.
    * Vysoká gc režie.
    * Přestávky generování kódu celé fáze.
* **RDDs**
    * Není nutné používat RDD, pokud potřebujete vytvořit nový vlastní RDD.
    * Žádná optimalizace dotazů prostřednictvím catalystu.
    * Žádné generování kódu celé fáze.
    * Vysoká gc režie.
    * Je nutné používat rozhraní API pro starší verze Spark 1.x.

## <a name="use-optimal-data-format"></a>Použití optimálního formátu dat

Spark podporuje mnoho formátů, jako jsou csv, json, xml, parkety, orc a avro. Spark lze rozšířit na podporu mnoha dalších formátů s externími zdroji dat - další informace naleznete v [tématu Balíčky Apache Spark](https://spark-packages.org).

Nejlepší formát pro výkon je parkety s *elegantní kompresí*, což je výchozí hodnota v Spark 2.x. Parkety ukládají data ve sloupcovém formátu a jsou vysoce optimalizované ve Sparku.

## <a name="select-default-storage"></a>Vybrat výchozí úložiště

Když vytvoříte nový cluster Spark, můžete jako výchozí úložiště clusteru vybrat Azure Blob Storage nebo Azure Data Lake Storage. Obě možnosti poskytují výhodu dlouhodobého úložiště pro přechodné clustery, takže se vaše data při odstranění clusteru automaticky neodstraní. Můžete znovu vytvořit přechodný cluster a stále přistupovat k datům.

| Store Type | Systém souborů | Rychlost | Přechodné | Případy použití |
| --- | --- | --- | --- | --- |
| Azure Blob Storage | **wasb:**//url/ | **Standard** | Ano | Přechodný cluster |
| Azure Blob Storage (zabezpečené) | **wasbs:**//url/ | **Standard** | Ano | Přechodný cluster |
| Azure Data Lake Storage Gen2| **abfs:**//url/ | **Rychlejší** | Ano | Přechodný cluster |
| Úložiště datového jezera Azure Gen 1| **adl:**//url/ | **Rychlejší** | Ano | Přechodný cluster |
| Místní hdfs | **hdfs:**//url/ | **Nejrychlejší** | Ne | Interaktivní cluster 24/7 |

Úplný popis možností úložiště dostupných pro clustery HDInsight najdete v [tématu Porovnání možností úložiště pro použití s clustery Azure HDInsight](../hdinsight-hadoop-compare-storage-options.md).

## <a name="use-the-cache"></a>Použití mezipaměti

Spark poskytuje své vlastní nativní mechanismy ukládání do mezipaměti, `.cache()`které `CACHE TABLE`lze použít různými metodami, jako `.persist()`jsou , , a . Tento nativní ukládání do mezipaměti je efektivní s malými sadami dat, stejně jako v kanálech ETL, kde je třeba ukládat do mezipaměti průběžné výsledky. Nativní ukládání do mezipaměti Spark však v současné době nefunguje dobře s dělením, protože tabulka uložená v mezipaměti neuchovává data oddílů. Obecnější a spolehlivější ukládání do mezipaměti technika je *ukládání vrstvy ukládání do mezipaměti*.

* Nativní ukládání do mezipaměti Spark (nedoporučuje se)
    * Dobré pro malé datové sady.
    * Nefunguje s dělením, které se mohou v budoucích verzích Spark změnit.

* Ukládání do mezipaměti úrovně úložiště (doporučeno)
    * Lze implementovat na HDInsight pomocí funkce [IO Cache.](apache-spark-improve-performance-iocache.md)
    * Používá ukládání do mezipaměti v paměti a ukládání Do mezipaměti SSD.

* Místní HDFS (doporučeno)
    * `hdfs://mycluster`Cestu.
    * Používá ukládání do mezipaměti SSD.
    * Při odstranění clusteru dojde ke ztrátě dat uložených v mezipaměti, což vyžaduje opětovné sestavení mezipaměti.

## <a name="use-memory-efficiently"></a>Efektivní využití paměti

Spark funguje tak, že umísťuje data do paměti, takže správa paměťových prostředků je klíčovým aspektem optimalizace provádění úloh Spark.  Existuje několik technik, které můžete použít pro efektivní využití paměti clusteru.

* Preferujte menší datové oddíly a účet pro velikost dat, typy a distribuci ve strategii dělení.
* Zvažte novější, efektivnější [serializaci kryo dat](https://github.com/EsotericSoftware/kryo), spíše než výchozí serializace Java.
* Preferujte použití YARN, `spark-submit` protože se odděluje podle dávky.
* Monitorujte a vylaďte nastavení konfigurace Spark.

Pro vaši referenci se na následujícím obrázku zobrazí struktura paměti Spark a některé parametry paměti vykonavatele klíčů.

### <a name="spark-memory-considerations"></a>Důležité informace o paměti jiskry

Pokud používáte [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), pak YARN řídí maximální součet paměti používané všechny kontejnery na každém uzlu Spark.  Následující diagram znázorňuje klíčové objekty a jejich vztahy.

![Správa paměti YARN Spark](./media/apache-spark-perf/apache-yarn-spark-memory.png)

Chcete-li adresovat zprávy s nepaměti, vyzkoušejte:

* Projděte si dag management shuffles. Snižte redukci na straně mapy, zdrojová data před oddílem (nebo vybočování), maximalizujte jednotlivé náhodné přehrávání a snižte množství odeslaných dat.
* Preferují `ReduceByKey` s jeho `GroupByKey`pevné omezení paměti , který poskytuje agregace, okna a další funkce, ale má ann neomezené omezení paměti.
* Preferovat `TreeReduce`, který dělá více práce na vykonavatele nebo oddíly, na `Reduce`, který dělá všechny práce na ovladači.
* Využijte dataframe spíše než objekty RDD nižší úrovně.
* Vytvořte ComplexTypes, které zapouzdřují akce, jako je například "Top N", různé agregace nebo operace oken.

Další kroky řešení potíží najdete [v tématu Výjimky outofmemoryerror pro Apache Spark v Azure HDInsight](apache-spark-troubleshoot-outofmemory.md).

## <a name="optimize-data-serialization"></a>Optimalizace serializace dat

Úlohy Spark jsou distribuovány, takže vhodná serializace dat je důležitá pro nejlepší výkon.  Pro Spark existují dvě možnosti serializace:

* Výchozí je serializace jazyka Java.
* Kryo serializace je novější formát a může mít za následek rychlejší a kompaktnější serializaci než Java.  Kryo vyžaduje, abyste zaregistrovali třídy v programu a ještě nepodporuje všechny serializovatelné typy.

## <a name="use-bucketing"></a>Použití kbelíku

Bucketing je podobný dělení dat, ale každý segment může obsahovat sadu hodnot sloupců, nikoli pouze jeden. Bucketing funguje dobře pro dělení na velké (v milionech nebo více) počet hodnot, jako jsou identifikátory produktu. Kbelík je určen hašováním klíče kbelíku řádku. Tabulky s kontejnery nabízejí jedinečné optimalizace, protože ukládají metadata o tom, jak byly bucketed a seřazeny.

Některé pokročilé funkce kbelíku jsou:

* Optimalizace dotazů na základě zobrazení metadat.
* Optimalizované agregace.
* Optimalizovaná spojení.

Můžete použít dělení a bucketing ve stejnou dobu.

## <a name="optimize-joins-and-shuffles"></a>Optimalizace spojení a zamíchacích měsíčů

Pokud máte pomalé úlohy na připojit nebo shuffle, příčinou je pravděpodobně *zkosení dat*, což je asymetrie v datech úlohy. Úloha mapy může například trvat 20 sekund, ale spuštění úlohy, kde jsou data připojena nebo zamíchána, trvá hodiny. Chcete-li opravit zkosení dat, měli byste solit celý klíč nebo použít *izolovanou sůl* pouze pro některé podmnožiny klíčů. Pokud používáte izolovanou sůl, měli byste dále filtrovat, abyste izolovali podmnožinu slaných klíčů v mapových spojeních. Další možností je nejprve zavést sloupec segmentu a předem agregovat v kontejnerech.

Dalším faktorem, který způsobuje pomalé spojení může být typ spojení. Ve výchozím nastavení `SortMerge` Spark používá typ spojení. Tento typ spojení je nejvhodnější pro velké datové sady, ale je jinak výpočtově nákladné, protože musí nejprve seřadit levé a pravé strany dat před jejich sloučením.

Spojení `Broadcast` je nejvhodnější pro menší sady dat nebo kde jedna strana spojení je mnohem menší než na druhé straně. Tento typ spojení vysílá jednu stranu všem vykonavatelům, a proto vyžaduje více paměti pro vysílání obecně.

Typ spojení v konfiguraci můžete `spark.sql.autoBroadcastJoinThreshold`změnit nastavením nebo můžete nastavit nápovědu spojení`dataframe.join(broadcast(df2))`pomocí api datových rámců ( ).

```scala
// Option 1
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", 1*1024*1024*1024)

// Option 2
val df1 = spark.table("FactTableA")
val df2 = spark.table("dimMP")
df1.join(broadcast(df2), Seq("PK")).
    createOrReplaceTempView("V_JOIN")

sql("SELECT col1, col2 FROM V_JOIN")
```

Pokud používáte kontejnerové tabulky, pak máte třetí typ `Merge` spojení, spojení. Správně předrozdělená a předem seřazená datová sada `SortMerge` přeskočí fázi nákladného řazení ze spojení.

Pořadí spojení záležitostí, zejména ve složitějších dotazů. Začněte s nejselektivnějšími spojeními. Také přesunout spojení, které zvyšují počet řádků po agregace, pokud je to možné.

Chcete-li spravovat paralelismus pro kartézská spojení, můžete přidat vnořené struktury, okna a možná přeskočit jeden nebo více kroků ve vaší úloze Spark.

## <a name="customize-cluster-configuration"></a>Přizpůsobení konfigurace clusteru

V závislosti na zatížení clusteru Spark můžete určit, že by nevýchozí konfigurace Spark vedla k optimalizovanějšímu spuštění úloh y Spark.  Proveďte srovnávací testy s ukázkovými úlohami a ověřte všechny konfigurace clusteru, které nejsou výchozí.

Zde jsou některé běžné parametry, které můžete upravit:

* `--num-executors`nastaví příslušný počet vykonavatelů.
* `--executor-cores`nastaví počet jader pro každého vykonavatele. Obvykle byste měli mít středně velké vykonavatele, protože jiné procesy spotřebovávají část dostupné paměti.
* `--executor-memory`nastaví velikost paměti pro každý prováděcí modul, který řídí velikost haldy na YARN. Měli byste ponechat nějakou paměť pro provádění režie.

### <a name="select-the-correct-executor-size"></a>Výběr správné velikosti vykonavatele

Při rozhodování o konfiguraci vykonavatele, zvažte java uvolňování paměti (GC) režie.

* Faktory pro snížení velikosti exekutora:
    1. Zmenšete velikost haldy pod 32 GB, aby < 10 % byla <
    2. Snižte počet jader, aby se < 10% <

* Faktory pro zvýšení velikosti exekutora:
    1. Snižte režii komunikace mezi vykonavateli.
    2. Snižte počet otevřených připojení mezi vykonavateli (N2) ve větších clusterech (>100 vykonavatelů).
    3. Zvětšete velikost haldy tak, aby vyhovovala úlohám náročným na paměť.
    4. Volitelné: Snížit nároky na paměť vykonavatele.
    5. Volitelné: Zvyšte využití a souběžnost tím, že se přehlášuje procesor.

Jako obecné pravidlo při výběru velikosti vykonavatele:

1. Začněte s 30 GB na vykonavatele a distribuujte dostupná jádra počítače.
2. Zvyšte počet jader vykonavatele pro větší clustery (> 100 vykonavatelů).
3. Upravte velikost na základě zkušebních spuštění i předchozích faktorů, jako je například režie gc.

Při spouštění souběžných dotazů zvažte následující skutečnosti:

1. Začněte s 30 GB na vykonavatele a všechna jádra počítače.
2. Vytvořte více paralelních aplikací Spark tím, že se přehlášuje težce procesoru (přibližně 30% zlepšení latence).
3. Distribuujte dotazy mezi paralelními aplikacemi.
4. Upravte velikost na základě zkušebních spuštění i předchozích faktorů, jako je například režie gc.

Další informace o konfiguraci prováděcích modulů pomocí Ambari najdete v [tématu Nastavení Apache Spark – Spark executors](apache-spark-settings.md#configuring-spark-executors).

Sledujte výkon dotazu pro odlehlé hodnoty nebo jiné problémy s výkonem, a to tak, jak se podíváte na zobrazení časové osy, graf SQL, statistiky úloh a tak dále. Informace o ladění úloh Spark pomocí YARN a serveru Historie Spark najdete v tématu [Ladění úloh Apache Spark spuštěných na Azure HDInsight](apache-spark-job-debugging.md). Tipy k používání serveru YARN Timeline Server najdete [v tématu Přístup k protokolům aplikací Apache Hadoop YARN](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Někdy jeden nebo několik vykonavatelů jsou pomalejší než ostatní a úkoly trvat mnohem déle, než se provádí. K tomu často dochází ve větších clusterech (> 30 uzlech). V takovém případě rozdělte práci na větší počet úkolů, aby plánovač mohl kompenzovat pomalé úkoly. Například mají alespoň dvakrát tolik úkolů jako počet jader prováděcího modulu v aplikaci. Můžete také povolit spekulativní provádění `conf: spark.speculation = true`úkolů s .

## <a name="optimize-job-execution"></a>Optimalizace provádění úloh

* Cache podle potřeby, například pokud použijete data dvakrát, pak do mezipaměti.
* Proměnné vysílání všem vykonavatelům. Proměnné jsou serializovány pouze jednou, což vede k rychlejšívyhledávání.
* Použijte fond vláken na ovladači, což má za následek rychlejší provoz pro mnoho úkolů.

Pravidelně sledujte spuštěné úlohy, pokud hledáte problémy s výkonem. Pokud potřebujete více informací o určitých problémech, zvažte jeden z následujících nástrojů pro profilování výkonu:

* [Nástroj Intel PAL](https://github.com/intel-hadoop/PAT) tool monitoruje využití cpu, úložiště a šířky pásma sítě.
* [Oracle Java 8 Mission Control](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) profily Spark a executor kód.

Klíčem k výkonu dotazů Spark 2.x je modul tungsten, který závisí na generování kódu celé fáze. V některých případech může být zakázáno generování kódu celé fáze. Pokud například ve výrazu agregace použijete neproměnlivý `SortAggregate` typ ( `HashAggregate``string`), zobrazí se místo . Chcete-li například dosáhnout lepšího výkonu, vyzkoušejte následující a znovu povolte generování kódu:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Další kroky

* [Ladění úloh Apache Spark spuštěných ve službě Azure HDInsight](apache-spark-job-debugging.md)
* [Správa prostředků pro cluster Apache Spark na HDInsightu](apache-spark-resource-manager.md)
* [Použití rozhraní Apache Spark REST API k odesílání vzdálených úloh do clusteru Apache Spark](apache-spark-livy-rest-interface.md)
* [Ladění Apache Spark](https://spark.apache.org/docs/latest/tuning.html)
* [Jak skutečně naladit Apache Spark práce tak, aby práce](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
* [Kryo serializace](https://github.com/EsotericSoftware/kryo)
