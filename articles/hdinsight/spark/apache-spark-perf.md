---
title: Optimalizace Sparkových úloh pro výkon – Azure HDInsight
description: Ukazuje běžných strategií pro nejlepší výkon ze všech clusterů Spark.
services: hdinsight
ms.service: hdinsight
author: maxluk
ms.author: maxluk
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/11/2018
ms.openlocfilehash: a6ab4d751be74b66d9e75a37f88bc8d441f9b003
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2018
ms.locfileid: "53653726"
---
# <a name="optimize-apache-spark-jobs"></a>Optimalizace úloh Apache Spark

Zjistěte, jak optimalizovat [Apache Spark](https://spark.apache.org/) konfigurace clusteru pro konkrétní úlohu.  Nejběžnější výzvou je přetížení paměti z důvodu nesprávné konfigurace (zejména nesprávné velikosti prováděcí moduly), dlouhotrvající operace a úlohy, jejichž výsledkem Kartézském operace. Můžete urychlit úlohy s odpovídající ukládání do mezipaměti a tím, že pro [Nerovnoměrná distribuce dat](#optimize-joins-and-shuffles). Pro zajištění nejlepšího výkonu monitorování a zkontrolujte dlouhotrvající a využívání prostředků se prováděné úlohy Spark.

Následující části popisují běžné úlohy optimalizace Spark a doporučení.

## <a name="choose-the-data-abstraction"></a>Zvolte abstrakce dat

Spark využívá 1.x Rdd k abstraktní data a poté Spark 2.x zavedené datových rámců a datové sady. Vezměte v úvahu následující relativní věci:

* **Datových rámců**
    * Ve většině případů je nejlepší volbou.
    * Poskytuje optimalizaci dotazu prostřednictvím zprostředkující.
    * Generování kódu celé fázi.
    * Přímý přístup do paměti.
    * Nízká režie uvolňování paměti (GC).
    * Není co vývojářsky přívětivé jako datové sady, protože neexistují žádné kontroly za kompilace nebo programování objektu domény.
* **Datové sady**
    * Dobré ve složitých kanály ETL, kterých je přijatelná dopad na výkon.
    * Nevhodní v agregacích, kde může být významný dopad na výkon.
    * Poskytuje optimalizaci dotazu prostřednictvím zprostředkující.
    * Vývojářsky přívětivé tím, že poskytuje kontroly programování a kompilaci objektu domény.
    * Přidává režijní náklady na serializaci nebo deserializaci.
    * Vysoké režijní náklady na uvolňování paměti.
    * Ukončí generování kódu celé fázi.
* **Rdd**
    * Ve Spark 2.x, není potřeba použít Rdd, pokud budete muset vytvořit nové vlastní RDD.
    * Bez optimalizace dotazů prostřednictvím zprostředkující.
    * Generování kódu celé fázi.
    * Vysoké režijní náklady na uvolňování paměti.
    * Musíte použít Spark 1.x starší verze rozhraní API.

## <a name="use-optimal-data-format"></a>Použijte formát optimální dat

Sparku jednodušší, podporuje řadu formátů, například csv, json, xml, parquet, orc a avro. Spark je možné rozšířit podporu mnoha více formátech s externím zdrojům dat – Další informace najdete v tématu [Apache Spark balíčky](https://spark-packages.org).

Nejlepší formát pro výkon je parquet s *Tenhle komprese*, což je výchozí hodnotou v Spark 2.x. Ukládá data ve sloupcovém formátu parquet a je vysoce optimalizovaných ve Sparku.

## <a name="select-default-storage"></a>Vyberte výchozí úložiště

Když vytvoříte nový cluster Spark, máte možnost vybrat si jako výchozím úložištěm clusteru v Azure Blob Storage nebo Azure Data Lake Storage. Obě možnosti získáte výhody dlouhodobé úložiště pro přechodný clustery, tak vaše data nejsou se automaticky odstraní při odstranění clusteru. Můžete znovu vytvořit cluster přechodné a stále přístup k vašim datům.

| Typ Store | Systém souborů | Rychlost | Přechodná | Případy použití |
| --- | --- | --- | --- | --- |
| Azure Blob Storage | **wasb:**//url/ | **Standard** | Ano | Přechodné clusteru |
| Azure Data Lake Storage | **adl:**//url/ | **Rychlejší** | Ano | Přechodné clusteru |
| Místní HDFS | **hdfs:**//url/ | **Nejrychlejší** | Ne | Interaktivní 24 hodin denně 7 clusteru |

## <a name="use-the-cache"></a>Použití mezipaměti

Spark poskytuje vlastní nativní ukládání do mezipaměti mechanismy, které se dají použít prostřednictvím různých metod, jako `.persist()`, `.cache()`, a `CACHE TABLE`. Tento nativní ukládání do mezipaměti je efektivní s malými sadami dat stejně jako v kanály ETL, které je potřeba mezipaměti mezilehlých výsledků. Však Spark nativní mezipaměti aktuálně nefunguje s rozdělení do oddílů, protože v mezipaměti tabulku nezachovává dělení dat. Je obecný a spolehlivé ukládání do mezipaměti techniku *vrstvy úložiště ukládání do mezipaměti*.

* Nativní Spark ukládání do mezipaměti (není doporučeno)
    * Vhodná pro malé datové sady.
    * Funguje není s dělení, který může změnit v budoucích verzích Spark.

* Storage úrovně ukládání do mezipaměti (doporučeno)
    * Je možné implementovat pomocí [Alluxio](https://www.alluxio.org/).
    * Používá se v paměti a ukládání do mezipaměti SSD.

* Místní HDFS (doporučeno)
    * `hdfs://mycluster` cesta.
    * Používá ukládání do mezipaměti SSD.
    * Data uložená v mezipaměti se při odstranění clusteru, které vyžadují opětovné sestavení mezipaměti ztratí.

## <a name="use-memory-efficiently"></a>Efektivní používání paměti

Spark funguje tak, že data v paměti, tak Správa prostředků paměti je klíčový aspekt optimalizace spouštění Sparkových úloh.  Existuje několik postupů, které můžete použít pro efektivní použití paměti vašeho clusteru.

* Raději menší datové oddíly a účet pro velikost dat, typy a distribuční strategie dělení.
* Vezměte v úvahu novější, efektivnější [serializaci dat Kryo](https://github.com/EsotericSoftware/kryo), namísto výchozí serializace Java.
* Přednost používání YARN, protože ji odděluje `spark-submit` službou batch.
* Sledování a ladění konfigurace nastavení Sparku.

Pro srovnání si struktura paměti Spark a některé parametry paměti klíče prováděcí modul uvádíme na dalším obrázku.

### <a name="spark-memory-considerations"></a>Požadavky na paměť Spark

Pokud používáte [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), pak YARN řídí maximální součtem velikostí paměti používané všechny kontejnery na každém uzlu Spark.  Následující diagram ukazuje klíčové objekty a jejich vztahy.

![Správa paměti YARN Spark](./media/apache-spark-perf/yarn-spark-memory.png)

K vyřešení "nedostatek paměti' zprávy, vyzkoušejte:

* Projděte si podle okolí správu posouvá DAG. Snížit reducting straně mapy, předem oddílu (nebo nastavit interval) zdroje dat, maximalizovat podle jedné okolí posouvá a snížení množství dat odesílaných.
* Preferovat `ReduceByKey` s jeho limit pevné paměti k `GroupByKey`, který poskytuje agregace, okna a další funkce, ale má limit paměti bez vazby ann.
* Preferovat `TreeReduce`, více práce na exekutor nebo oddíly, které se na `Reduce`, na které se všechny práce na ovladači.
* Využijte datových rámců a nikoli objekty RDD nižší úrovně.
* Vytváření komplexních typů, které zapouzdřují akce, jako je například "Top N" různých agregacích operace nebo časová okna.

## <a name="optimize-data-serialization"></a>Optimalizace serializaci dat

Sparkových úloh se distribuují, takže příslušná data serializace je důležité pro zajištění nejlepšího výkonu.  Existují dvě možnosti serializace pro Spark:

* Java serializace je výchozí nastavení.
* Serializace kryo je novější formát a může mít za následek rychlejší a více než Java compact serializace.  Kryo vyžaduje, aby registraci tříd ve svém programu a zatím nepodporuje všechny Serializovatelné typy.

## <a name="use-bucketing"></a>Používat kontejnery

Kontejnery se podobá dělení dat, ale každý interval může obsahovat sadu hodnot sloupců, nikoli jen jeden. Kontejnery funguje dobře pro dělení na (v miliónech nebo více) velký počet hodnot, například identifikátory produktu. Hashování kbelíku klíč řádku určuje kbelíky. Kategorizovaná tabulky poskytují jedinečné optimalizace, protože ukládají metadata o tom, jak byly kterých rozdělit a seřazeny.

Zde jsou některé pokročilé funkce kontejnery:

* Podle kontejnery meta-informace o optimalizaci dotazu.
* Optimalizované agregace.
* Optimalizované spojení.

Můžete použít dělení a kontejnery ve stejnou dobu.

## <a name="optimize-joins-and-shuffles"></a>Optimalizace spojení a podle okolí posouvá

Pokud máte pomalé úlohy na spojení nebo náhodně, příčinou je pravděpodobně *Nerovnoměrná distribuce dat*, což je asymetrie ve vašich datech úlohy. Třeba mapování úlohy může trvat 20 sekund, ale spuštěním úlohy, kde data připojený nebo které se náhodně pomíchají trvá hodiny.   Chcete-li vyřešit nerovnoměrnou distribuci dat, by měl salt celý kód nebo použijte *samostatný hodnota salt* pro pouze některé dílčí klíče.  Pokud používáte izolované hodnota salt, by měl dál filtrovat izolovat podmnožinou vašich solené klíče v mapě spojení. Další možností je zavedení sloupce kbelíku a předem agregovat v kbelíků nejprve.

Dalším faktorem způsobuje pomalé spojení může být typ spojení. Ve výchozím nastavení, Spark využívá `SortMerge` typ spojení. Tento typ spojení je nejvhodnější pro velké datové sady, ale je jinak výpočetně náročná, protože je nutné nejprve seřadit levé a pravé straně dat před sloučením.

A `Broadcast` spojení je nejvhodnější pro menší sady dat, nebo kde je mnohem menší než druhé straně jedna strana spojení. Tento typ spojení vysílá straně všechny moduly provádění a tedy vyžaduje více paměti pro vysílání obecně.

Typ spojení v konfiguraci můžete změnit nastavením `spark.sql.autoBroadcastJoinThreshold`, nebo můžete nastavit pomocný parametr spojení pomocí rozhraní API datového rámce (`dataframe.join(broadcast(df2))`).

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

Pokud používáte kategorizovaná tabulky pak máte třetí typ, spojení `Merge` spojení. Datové sady správně předem oddílů a předem seřazeno přeskočí fáze nákladné řazení z `SortMerge` spojení.

Pořadí spojení je důležité, zejména v složitější dotazy. Začněte s nejvíce selektivní spojení. Navíc přesuňte spojení, které navýšení tohoto počtu řádků po agregace, pokud je to možné.

Ke správě paralelismu, konkrétně v případě Kartézském spojení, můžete přidat vnořené struktury časová okna a případně jeden nebo více kroků v úloze, Spark.

## <a name="customize-cluster-configuration"></a>Přizpůsobení konfigurace clusteru

V závislosti na své úlohy clusteru Sparku, možná zjistíte, že konfigurace Spark jiné než výchozí by za následek více optimalizované spuštění úlohy Sparku.  Proveďte srovnávací test testování pomocí ukázkových úloh k ověření konfigurace clusteru jiné než výchozí.

Tady jsou některé společné parametry, které můžete upravit:

* `--num-executors` Nastaví odpovídající počtu prováděcích procesů.
* `--executor-cores` Nastaví počet jader pro každý prováděcího modulu. Obvykle byste měli mít middle-sized moduly provádění a jak využívat jiné procesy, některé dostupné paměti.
* `--executor-memory` Nastaví velikost paměti pro každou prováděcí modul, který řídí velikost haldy na technologii YARN. Byste měli nechat paměti pro režijní náklady na spuštění.

### <a name="select-the-correct-executor-size"></a>Vyberte velikost správné prováděcího modulu

Při rozhodování o konfiguraci prováděcího modulu, vezměte v úvahu jazyce Java režie uvolňování paměti (GC).

* Faktory, které je zmenšit velikost prováděcí modul:
    1. Snižte velikost haldy pod 32 GB až zůstane GC režie < 10 %.
    2. Snížení počtu jader, která se zachovat GC režie < 10 %.

* Faktory zvýšit velikost prováděcí modul:
    1. Snížení režie komunikaci prováděcí moduly.
    2. Snižte počet otevřená připojení mezi moduly provádění (N2) v clusterech větší (> 100 moduly provádění).
    3. Zvětšete velikost haldy pro přizpůsobení pro úlohy náročné na paměť.
    4. Volitelné: Snižte zatížení paměti za prováděcího modulu.
    5. Volitelné: Oversubscribing procesoru zvýšit využití a souběžnosti.

Jako obecné těchhle při výběru velikosti prováděcí modul:
    
1. Začněte s 30 GB za prováděcí modul a distribuci jader počítače k dispozici.
2. Zvýšíte počet jader prováděcí modul pro větší clustery (> 100 moduly provádění).
3. Zvýšení nebo snížení velikosti založené na spuštění zkušební verze a na předchozích faktorech, například režie uvolňování paměti.

Při spuštění souběžných dotazů, zvažte následující:

1. Začněte s 30 GB za prováděcí modul a všechna jádra počítače.
2. Vytváření více paralelních Spark aplikací můžete oversubscribing procesoru (přibližně 30 % zlepšení latence).
3. Dotazy distribuujte napříč paralelních aplikací.
4. Zvýšení nebo snížení velikosti založené na spuštění zkušební verze a na předchozích faktorech, například režie uvolňování paměti.

Monitorujte výkon dotazů pro odlehlé hodnoty nebo jiné problémy s výkonem, pohlédnutím na zobrazení časové osy, graf SQL, statistiky úlohy a tak dále. Někdy jsou pomalejší než ostatní jednu nebo několik moduly provádění a trvat déle ke spuštění úlohy. K tomu často dochází v clusterech větší (> 30 uzly). V takovém případě rozdělení práce do většího počtu úkolů, Plánovač může kompenzovat pomalých úloh. Například v aplikaci máte minimálně dvakrát tolik úloh jako počet jader prováděcího modulu. Můžete také povolit spekulativního spouštění úloh s `conf: spark.speculation = true`.

## <a name="optimize-job-execution"></a>Optimalizace provádění úlohy

* Ukládat do mezipaměti podle potřeby, například pokud použít data dvakrát a potom ji do mezipaměti.
* Vysílání proměnné pro všechny moduly provádění. Proměnné lze serializovat pouze jednou, výsledkem je rychlejší vyhledávání.
* Použití fondu vláken na ovladači, jehož výsledkem je rychlejší operace pro mnoho úloh.

Sledování vašich spuštěných úloh pravidelně pro problémy s výkonem. Pokud potřebujete další informace o některých problémech, zvažte jednu z následujících nástroje pro profilaci výkonu:

* [Nástroj PAL Intel](https://github.com/intel-hadoop/PAT) monitoruje využití procesoru, úložiště a využití šířky pásma sítě.
* [Řídicí středisko Oracle Java 8](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) profily kód Sparku a prováděcí modul.

Klíčem k výkonu dotazů Spark 2.x je wolframu modul, který závisí na generování kódu celé fázi. V některých případech může být zakázaná generování kódu celé fázi. Například, pokud není měnitelný typ (`string`) ve výrazu agregace `SortAggregate` se zobrazí místo `HashAggregate`. Například pro lepší výkon, zkuste následující a potom znovu povolit generování kódu:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Další postup

* [Ladění Apache Spark úloh spuštěných v Azure HDInsight](apache-spark-job-debugging.md)
* [Správa prostředků v clusteru Apache Spark v HDInsight](apache-spark-resource-manager.md)
* [Apache Spark REST API můžete odesílat vzdálené úlohy do clusteru Apache Spark](apache-spark-livy-rest-interface.md)
* [Ladění Apache Sparku](https://spark.apache.org/docs/latest/tuning.html)
* [Jak se ve skutečnosti vyladit Apache Spark úlohy tak fungují](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
* [Kryo serializace](https://github.com/EsotericSoftware/kryo)
