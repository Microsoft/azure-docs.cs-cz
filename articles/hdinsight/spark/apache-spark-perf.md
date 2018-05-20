---
title: Optimalizace úlohy Spark pro výkon - Azure HDInsight | Microsoft Docs
description: Zobrazuje běžné strategie pro nejlepší výkon ze clustery Spark.
services: hdinsight
documentationcenter: ''
author: maxluk
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: maxluk
ms.openlocfilehash: f35ed98efb26dfa0d75a57ca3646f567a7949dae
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="optimize-spark-jobs"></a>Optimalizace sparkových úloh

Informace o optimalizaci konfigurace clusteru Spark pro konkrétní úlohy.  Je nejběžnější výzvu přetížení paměti z důvodu nesprávné konfigurace (zejména nesprávné velikosti vykonavatelů), dlouhotrvající operace a úlohy, jejichž výsledkem kartézských operace. Můžete urychlit úlohy s příslušnou ukládání do mezipaměti a tím, že pro [data zkosení](#optimize-joins-and-shuffles). Nejlepšího výkonu dosáhnete monitorování a zkontrolujte dlouho běžící a spotřeba prostředků spuštěních úloh Spark.

Následující části popisují běžné úlohy optimalizace Spark a doporučení.

## <a name="choose-the-data-abstraction"></a>Zvolte abstrakci dat

Spark 1.x používá RDDs abstraktní dat, a pak Spark 2.x zavedená DataFrames a datové sady. Vezměte v úvahu následující relativní věci:

* **DataFrames**
    * Nejlepší volbou ve většině případů
    * Poskytuje optimalizace dotazů prostřednictvím zprostředkující
    * Generování kódu celé fáze
    * Přímý přístup do paměti
    * Režie spojená s nízkým uvolňování paměti (GC)
    * Není jako uživatelsky jako datové sady, protože neexistují žádné kontroly kompilaci nebo programování objektu domény
* **Datové sady**
    * Dobré v komplexní ETL kanály, kde je přijatelné dopad na výkon
    * Nevhodní v agregace, kde může být značný dopad na výkon
    * Poskytuje optimalizace dotazů prostřednictvím zprostředkující
    * Uživatelsky tím, že poskytuje kontroly programovací a kompilaci objektu domény
    * Přidá režijní náklady na serializaci nebo deserializaci
    * Vysoké režijní náklady na globální Katalog
    * Dělí generování kódu celé fáze
* **RDDs**
    * V Spark 2.x, není nutné používat RDDs, pokud potřebujete vytvořit nové vlastní RDD
    * Žádná optimalizace dotazů prostřednictvím zprostředkující
    * Žádné generování kódu celé fáze
    * Vysoké režijní náklady na globální Katalog
    * Musíte použít Spark 1.x starší verze rozhraní API

## <a name="use-optimal-data-format"></a>Použijte formát optimální dat

Spark podporuje mnoho formáty, například csv, json, xml, parquet, orc a avro. Spark je možné rozšířit na podporu mnoha více formátů s externími zdroji dat – Další informace najdete v tématu [Spark balíčky](https://spark-packages.org).

Nejlepší formát výkonu je parquet s *Tenhle komprese*, což je výchozí hodnota v Spark 2.x. Parquet ukládá data ve sloupcovém formátu a je vysoce optimalizovaný v Spark.

## <a name="select-default-storage"></a>Vyberte výchozí úložiště

Když vytvoříte nový cluster Spark, máte možnost vybrat jako váš cluster výchozí úložiště Azure Blob Storage nebo Azure Data Lake Store. Obě možnosti poskytují výhodou dlouhodobé úložiště pro přechodný clusterů, tak data není získat automaticky odstraněny při odstranění clusteru. Můžete znovu vytvořit cluster přechodný a nadále přistupovat k datům.

| Typ úložiště | Systém souborů | Rychlost | Dočasná | Případy použití |
| --- | --- | --- | --- | --- |
| Azure Blob Storage | **wasb:**//url/ | **Standard** | Ano | Přechodný clusteru |
| Azure Data Lake Store | **adl:**//url/ | **Rychlejší** | Ano | Přechodný clusteru |
| Místní HDFS | **hdfs:**//url/ | **Nejrychlejší** | Ne | Interaktivní 24 hodin denně 7 clusteru |

## <a name="use-the-cache"></a>Použití mezipaměti

Spark poskytuje vlastní nativní ukládání do mezipaměti mechanismy, které můžete použít různé metody, jako `.persist()`, `.cache()`, a `CACHE TABLE`. Tento nativní ukládání do mezipaměti je účinné s malých datových sad a jako kanály ETL, kde je potřeba mezipaměti mezilehlých výsledků. Však Spark nativní ukládání do mezipaměti v aktuálně není fungují dobře u oddílů, protože v mezipaměti tabulku neuchovává rozdělení data. Další obecné a spolehlivé ukládání do mezipaměti technika je *ukládání do mezipaměti vrstvy úložiště*.

* Nativní Spark ukládání do mezipaměti (nedoporučuje se)
    * Vhodná pro malé datové sady.
    * Funguje není s oddíly, které může v budoucích verzích Spark změnit.

* Úložiště úrovně ukládání do mezipaměti (doporučeno)
    * Může být implementovaná pomocí [Alluxio](http://www.alluxio.org/).
    * Používá se v paměti a ukládání do mezipaměti SSD.

* Místní HDFS (doporučeno)
    * `hdfs://mycluster` cesta.
    * Používá ukládání do mezipaměti SSD.
    * Data uložená v mezipaměti se při odstranění clusteru, které vyžadují opětovné sestavení mezipaměti ztratí.

## <a name="use-memory-efficiently"></a>Efektivní použití paměti

Spark funguje tak, že data v paměti, tak správu prostředků paměti je klíčovým prvkem optimalizace provádění úloh Spark.  Existuje několik postupů, které můžete použít pro váš cluster paměti efektivně používat.

* Raději menší oddíly dat a účet pro velikost dat, typy a distribuce v strategie dělení.
* Zvažte novější, efektivnější [serializace dat Kryo](https://github.com/EsotericSoftware/kryo), namísto výchozí serializace Java.
* Dáváte přednost pomocí YARN, jak ji odděluje `spark-submit` dávkou.
* Monitorovat a vyladit nastavení konfigurace Spark.

Pro vaši informaci strukturu paměti Spark a některé parametry paměti klíče vykonavatele jsou uvedené v další bitové kopie.

### <a name="spark-memory-considerations"></a>Požadavky na paměť Spark

Pokud používáte YARN, YARN řídí maximální součtem velikostí paměti používané všechny kontejnery v každém uzlu Spark.  Následující diagram znázorňuje klíče objekty a jejich vztahů.

![Správa paměti YARN Spark](./media/apache-spark-perf/yarn-spark-memory.png)

Chcete-li vyřešit, nedostatek paměti' zprávy, zkuste:

* Zkontrolujte podle okolí posouvá správu DAG. Omezit tím, že reducting straně mapy, předem oddílu (nebo bucketize) zdroj dat, maximalizovat jeden podle okolí posouvá a snížit množství dat odesílaných.
* Dáváte přednost `ReduceByKey` s jeho omezení paměti pevné k `GroupByKey`, který poskytuje agregace, oddílová a další funkce, ale má limit ann bez vazby paměti.
* Dáváte přednost `TreeReduce`, na které se další práci na vykonavatelů nebo oddílů, k `Reduce`, na které se veškerá práce na ovladače.
* Využívejte DataFrames spíše než objekty RDD nižší úrovně.
* Vytvořte ComplexTypes, který zapouzdřit akce, jako je "Top N" různé agregace operace nebo okna.

## <a name="optimize-data-serialization"></a>Optimalizace serializace dat

Úlohy Spark se distribuují, takže příslušná data serializace je důležité pro nejlepší výkon.  Existují dvě možnosti serializace pro Spark:

* Java serializace je výchozí.
* Serializace kryo je novější formát a může mít za následek rychlejší a více než Java compact serializace.  Kryo vyžaduje, aby registraci třídy v programu a zatím nepodporuje všechny Serializovatelné typy.

## <a name="use-bucketing"></a>Použít bucketing

Bucketing je podobná dělení dat do oddílů, ale každý sady mohou být uloženy sadu hodnoty ve sloupcích, nikoli pouze jeden. Bucketing funguje dobře pro dělení na minimálně miliony) (v velkého počtu hodnot, například identifikátory produktu. Blok je určen podle algoritmu hash sady klíč řádku. Kategorizovaná tabulky nabízejí jedinečné optimalizace, protože ukládají metadata o tom, jak by byly kategorizovaná a seřazeny.

Některé pokročilé funkce bucketing jsou:

* Dotaz optimalizace na základě bucketing meta informací
* Optimalizované agregace
* Optimalizované spojení

Můžete použít vytváření oddílů a bucketing ve stejnou dobu.

## <a name="optimize-joins-and-shuffles"></a>Optimalizace spojení a podle okolí posouvá

Pokud máte pomalé úlohy na spojení nebo náhodně, příčinou je pravděpodobně *data zkosení*, což je asymetrie ve vašich datech úlohy. Například úlohu mapy může trvat 20 sekund, ale spuštěním úlohy, kde je připojené k data nebo je na nesprávním místě trvá hodin.   Pokud chcete opravit data zkosení, by měl salt celý klíč nebo použijte *izolované salt* pro pouze některé dílčí klíče.  Pokud používáte izolované salt, by měl dále filtrovat izolovat podmnožinou vašich solené klíče ve spojeních mapy. Další možností je zavedení sloupce sady a předem agregace v kbelíků nejprve.

Dalším faktorem způsobuje pomalé spojení, může být typ spojení. Ve výchozím nastavení, Spark používá `SortMerge` typ spojení. Tento typ spojení je nejvhodnější pro velké sady dat, ale je jinak náročné, protože je nutné nejprve seřadit levé a pravé straně dat před jejich sloučení.

A `Broadcast` spojení je nejvhodnější pro menší sady dat nebo kde je mnohem menší než druhá strana jedna strany spojení. Tento typ spojení vysílá jedné straně na všechny vykonavatelů a proto vyžaduje více paměti pro všesměrové obecně.

Můžete změnit typ spojení v konfiguraci nastavení `spark.sql.autoBroadcastJoinThreshold`, nebo můžete nastavit pomocný parametr spojení pomocí rozhraní API DataFrame (`dataframe.join(broadcast(df2))`).

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

Pokud používáte kategorizovaná tabulky, pak máte třetí typ, spojení `Merge` spojení. Datové sady správně předem oddílů a předem seřazené přeskočí fázi nákladné řazení z `SortMerge` spojení.

Záleží na pořadí spojení, zejména v složitější dotazy. Začněte s nejvíce selektivní spojení. Navíc přesunete spojení, která se zvyšují počet řádků po agregace, pokud je to možné.

Ke správě paralelismu, konkrétně v případě kartézských spojení, můžete přidat vnořené struktury oddílová a případně přeskočit jednu nebo více kroků v úloze Spark.

## <a name="customize-cluster-configuration"></a>Přizpůsobení konfigurace clusteru

V závislosti na vaší zatížení clusteru Spark, možná zjistíte, že jiné než výchozí konfigurace Spark by způsobilo více optimalizované provádění úlohy Spark.  Proveďte testování pomocí ukázkové úlohy k ověření konfigurace jakékoli jiné než výchozí clusteru srovnávacího testu.

Zde jsou některé společné parametry, které můžete upravit:

* `--num-executors` Nastaví odpovídající počet vykonavatelů.
* `--executor-cores` Nastaví počet jader pro každý prováděcího modulu. Obvykle byste měli mít middle-sized vykonavatelů, jak využívat jinými procesy, některé dostupné paměti.
* `--executor-memory` Nastaví velikost paměti pro každý prováděcího modulu, který řídí velikost haldy na rozhraní YARN. Nechte některé paměť pro spuštění režii.

### <a name="select-the-correct-executor-size"></a>Vyberte správný vykonavatele velikost

Při rozhodování o konfiguraci prováděcího modulu, zvažte režii Java paměti kolekce (GC).

* Faktory ke snížení velikosti vykonavatele:
    1. Snižte velikost haldy nižší než 32 GB zachovat GC režijní < 10 %.
    2. Snižte počet jader, který má zachovat GC režijní < 10 %.

* Zvětšete velikost vykonavatele faktorů:
    1. Snižte komunikace mezi vykonavatelů režie.
    2. Snižte počet otevřená připojení mezi vykonavatelů (N2) na větší clustery (> 100 vykonavatelů).
    3. Zvětšete velikost haldy pro umístění pro úlohy náročné na paměť.
    4. Volitelné: Snížit režijní náklady na vykonavatele paměti.
    5. Volitelné: Zvýšit využití a souběžnost oversubscribing procesoru.

Jako existuje obecné pravidlo při výběru vykonavatele velikost:
    
1. Začněte s 30 GB za vykonavatele a distribuovat dostupný počítač jader.
2. Zvyšte počet jader vykonavatele pro větší clustery (vykonavatelů > 100).
3. Zvýšení nebo snížení velikosti založená na běží zkušební verze a na předchozích faktorech, například režijní náklady na globální Katalog.

Při spuštění souběžných dotazů, zvažte následující:

1. Začněte s 30 GB za prováděcího modulu a všechny počítače jader.
2. Vytvořit více paralelních Spark aplikace oversubscribing procesoru (přibližně 30 % zlepšení latence).
3. Dotazy distribuujte mezi paralelní aplikace.
4. Zvýšení nebo snížení velikosti založená na běží zkušební verze a na předchozích faktorech, například režijní náklady na globální Katalog.

Monitorujte výkon dotazů pro odlehlých nebo jiné problémy s výkonem, pohledem na zobrazení časové osy, SQL grafu, Statistika projektu a tak dále. Někdy jeden nebo několik vykonavatelů je nižší než ostatní a úlohy trvat déle, provést. K tomu často dochází větší clustery (> 30 uzlů). V takovém případě rozdělte práci do větší počet úloh, takže Plánovač můžete kompenzovat pomalé úlohy. Například můžete máte alespoň dvakrát tolik úloh jako počet jader vykonavatele v aplikaci. Můžete také Povolit spekulativní provádění úlohy s `conf: spark.speculation = true`.

## <a name="optimize-job-execution"></a>Provádění úlohy optimalizace

* Ukládat do mezipaměti podle potřeby, třeba pokud dvakrát používat data, můžete ho do mezipaměti.
* Pro všechny vykonavatelů všesměrového vysílání proměnné. Proměnné jsou serializovat pouze jednou, výsledkem je rychlejší hledání.
* Použití fondu vláken na ovladače, což vede k operace rychlejší pro celou řadu úloh.

Sledování vaší spuštěných úloh pravidelně pro problémy s výkonem. Pokud potřebujete další aspekty určité problémy, zvažte jednu z následujících výkon nástroje pro profilaci:

* [Nástroj PAL Intel](https://github.com/intel-hadoop/PAT) monitoruje využití procesoru, úložiště a využití šířky pásma sítě.
* [Oracle Java 8 mise řízení](http://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) profily Spark a vykonavatele kódu.

Klíčem k výkon dotazů Spark 2.x je wolframu modul, který závisí na generování kódu celé fáze. V některých případech mohou být zakázány generování kódu celé fáze. Například, pokud použijete neměnitelnou typ (`string`) ve výrazu agregace `SortAggregate` se zobrazí místo `HashAggregate`. Například pro lepší výkon, zkuste následující a pak znovu povolte generování kódu:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Další postup

* [Ladění běží v Azure HDInsight Spark úlohy](apache-spark-job-debugging.md)
* [Správa prostředků v clusteru Spark v HDInsight](apache-spark-resource-manager.md)
* [Použít rozhraní API REST Spark vzdálené úlohy do clusteru Spark](apache-spark-livy-rest-interface.md)
* [Ladění Spark](https://spark.apache.org/docs/latest/tuning.html)
* [Jak ve skutečnosti optimalizaci vašeho Spark na úlohy, takže nebudou fungovat](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
* [Kryo serializace](https://github.com/EsotericSoftware/kryo)
