---
title: Optimalizace úloh Sparku pro výkon – Azure HDInsight
description: Zobrazit společné strategie pro nejlepší výkon Apache Sparkch clusterů ve službě Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 8d058c55eab3d161e625d7d4ca3ef53b36497e00
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814073"
---
# <a name="optimize-apache-spark-jobs"></a>Optimalizace úloh Apache Spark

Naučte se optimalizovat [Apache Spark](https://spark.apache.org/) konfiguraci clusteru pro konkrétní zatížení.  Nejběžnějším problémem je tlak na paměť, z důvodu nesprávných konfigurací (zvláště nesprávného vykonavatele), dlouhotrvajících operací a úloh, které způsobují kartézském operace. Úlohy můžete urychlit s vhodným ukládáním do mezipaměti a díky tomu, aby bylo možné [Zkosit data](#optimize-joins-and-shuffles). Nejlepšího výkonu dosáhnete, když budete monitorovat a kontrolovat dlouhodobá spuštění úloh Sparku, která jsou náročná na prostředky.

Následující části popisují běžné optimalizace úloh Spark a doporučení.

## <a name="choose-the-data-abstraction"></a>Zvolit abstrakci dat

Starší verze Sparku používají RDD k abstraktním datům, Spark 1,3 a 1,6 zavedla datové rámce a datové sady v uvedeném pořadí. Vezměte v úvahu následující relativní věci:

* **DataFrames**
    * Nejlepší volba ve většině situací.
    * Poskytuje optimalizaci dotazů prostřednictvím Catalyst.
    * Generování kódu v celém fázi.
    * Přímý přístup do paměti.
    * Režie v nízkém uvolňování paměti (GC).
    * Ne jako datové sady srozumitelné pro vývojáře, protože neexistují žádné kontroly doby kompilace nebo programování doménových objektů.
* **Datové sady**
    * Dobrá v komplexních kanálech ETL, kde je dopad na výkon přijatelný.
    * Nedobrý v agregacích, kde dopad na výkon může být značný.
    * Poskytuje optimalizaci dotazů prostřednictvím Catalyst.
    * Vývojář – uživatelsky přívětivé poskytováním programování doménových objektů a kontrol při kompilaci.
    * Přidá režii serializace/deserializace.
    * Vysoká režie GC.
    * Zruší generování kódu na celé fázi.
* **RDD**
    * Pokud nepotřebujete vytvářet nové vlastní RDD, nemusíte používat RDD.
    * Žádná optimalizace dotazů prostřednictvím Catalyst.
    * Nevytváření celého připraveného kódu.
    * Vysoká režie GC.
    * Musí používat starší rozhraní API Spark 1. x.

## <a name="use-optimal-data-format"></a>Použít optimální datový formát

Spark podporuje mnoho formátů, jako je CSV, JSON, XML, Parquet, orc a Avro. Spark se dá rozšířit tak, aby podporoval mnoho dalších formátů s externími zdroji dat. Další informace najdete v tématu [Apache Spark balíčky](https://spark-packages.org).

Nejlepší formát pro výkon je Parquet s *kompresí s přichycením*, což je výchozí hodnota ve Sparku 2. x. Parquet ukládá data ve sloupcovém formátu a je vysoce optimalizovaná ve Sparku.

## <a name="select-default-storage"></a>Vybrat výchozí úložiště

Při vytváření nového clusteru Spark máte možnost vybrat si Azure Blob Storage nebo Azure Data Lake Storage jako výchozí úložiště clusteru. Obě možnosti poskytují výhody dlouhodobého úložiště pro přechodný clustery, takže vaše data se při odstranění clusteru automaticky neodstraní. Můžete znovu vytvořit přechodný cluster a stále získat přístup k datům.

| Typ úložiště | Systém souborů | Rychlost | Přechodná | Případy použití |
| --- | --- | --- | --- | --- |
| Azure Blob Storage | **wasb:** //URL/ | **Standard** | Ano | Přechodný cluster |
| Azure Data Lake Storage Gen 2| **ABFS [s]:** //URL/ | **Zrychlení** | Ano | Přechodný cluster |
| Azure Data Lake Storage Gen1| **adl:** //url/ | **Zrychlení** | Ano | Přechodný cluster |
| Místní HDFS | **HDFS:** //URL/ | **Způsobem** | Ne | Interaktivní cluster 24/7 |

## <a name="use-the-cache"></a>Použití mezipaměti

Spark poskytuje vlastní nativní mechanismy ukládání do mezipaměti, které je možné použít prostřednictvím různých metod `.persist()`, jako jsou, `CACHE TABLE` `.cache()`a. Tato nativní mezipaměť je platná u malých datových sad i v kanálech ETL, kde potřebujete ukládat do mezipaměti mezilehlé výsledky. Nicméně nativní ukládání do mezipaměti v současnosti nefunguje dobře s vytvářením oddílů, protože tabulka v mezipaměti neuchovává data oddílů. Obecnější a spolehlivá technika ukládání do mezipaměti je *ukládání vrstev úložiště do mezipaměti*.

* Nativní ukládání do mezipaměti Spark (nedoporučuje se)
    * Vhodný pro malé datové sady.
    * Nefunguje s rozdělením na oddíly, které se mohou v budoucích vydáních Spark změnit.

* Ukládání na úrovni úložiště do mezipaměti (doporučeno)
    * Lze implementovat pomocí [Alluxio](https://www.alluxio.org/).
    * Používá ukládání do mezipaměti SSD v paměti a SSD.

* Místní HDFS (doporučeno)
    * `hdfs://mycluster`dílčí.
    * Používá ukládání do mezipaměti SSD.
    * Po odstranění clusteru dojde ke ztrátě dat uložených v mezipaměti, které vyžaduje opětovné sestavení mezipaměti.

## <a name="use-memory-efficiently"></a>Efektivní využití paměti

Spark funguje tak, že umístí data do paměti, takže Správa prostředků paměti je klíčovým aspektem Optimalizace spouštění úloh Sparku.  Existuje několik postupů, které můžete použít pro efektivní použití paměti clusteru.

* Preferovat menší datové oddíly a účet pro velikost, typy a distribuci dat v strategii dělení.
* Zvažte novější a efektivnější [serializaci dat kryo](https://github.com/EsotericSoftware/kryo)místo výchozí serializace Java.
* Raději použijte přízi, jak je oddělením `spark-submit` Batch.
* Monitorování a optimalizace nastavení konfigurace Sparku

Pro referenci se v dalším obrázku zobrazí struktura paměti Spark a některé parametry paměti vykonavatele klíče.

### <a name="spark-memory-considerations"></a>Požadavky na paměť Spark

Pokud používáte [Apache HADOOP nitě](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), pak nitě řídí maximální součet paměti využívané všemi kontejnery na jednotlivých uzlech Spark.  Následující diagram znázorňuje klíčové objekty a jejich vztahy.

![Správa paměti PŘÍZe Spark](./media/apache-spark-perf/yarn-spark-memory.png)

Pokud chcete adresovat zprávy o nedostatku paměti, zkuste:

* Projděte si přehledy DAG Management. Zmenšuje se ze zdrojů dat na straně mapy, předrozdělitelné (neboli nastavit interval) zdrojových dat, maximalizujte jednotlivá započet a snižte množství odesílaných dat.
* Preferovat `ReduceByKey` s pevným `GroupByKey`omezením paměti, které poskytuje agregace, okna a další funkce, ale má Ann nevázaný limit paměti.
* Raději `TreeReduce`, což více funguje na vykonavatelích nebo oddílech, na `Reduce`, který vše funguje na ovladači.
* Využijte místo objektů RDD na nižší úrovni datový rámec.
* Vytvořte ComplexTypes, které zapouzdřují akce, například "horních N", různé agregace nebo operace s okny.

## <a name="optimize-data-serialization"></a>Optimalizovat serializaci dat

Úlohy Sparku jsou distribuované, takže pro nejlepší výkon je důležité, aby byla vhodná serializace dat.  Pro Spark existují dvě možnosti serializace:

* Výchozím nastavením je serializace Java.
* Serializace kryo je novější formát a může mít za následek rychlejší a kompaktnější serializaci než Java.  Kryo vyžaduje, abyste v programu zaregistrovali třídy a zatím nepodporovaly všechny Serializovatelné typy.

## <a name="use-bucketing"></a>Použít zablokování

Sestavování se podobá dělení dat, ale každá sada může obsahovat sadu hodnot sloupce, nikoli jenom jednu. Vytváření bloků funguje dobře pro dělení na velké (v milionech a více) číslech hodnot, jako jsou například identifikátory produktu. Sada je určena pomocí algoritmu hash pro klíč kontejneru řádku. Rozdělené tabulky nabízejí jedinečné optimalizace, protože ukládají metadata o způsobu jejich seřazení a řazení.

Některé pokročilé funkce pro seintervalování jsou tyto:

* Optimalizace dotazů založená na seintervalování meta-informací.
* Optimalizované agregace.
* Optimalizované spojení.

Můžete použít dělení a zablokování současně.

## <a name="optimize-joins-and-shuffles"></a>Optimalizace spojení a náhodného navýšení

Pokud máte pomalé úlohy při spojení nebo náhodně, příčinou je pravděpodobně *zkosení dat*, což je asymetrie v datech úlohy. Například úloha mapy může trvat 20 sekund, ale při spuštění úlohy, kde se data připojí nebo rozchází, trvá hodiny.   Chcete-li opravit zešikmení dat, měli byste nasoleit celý klíč nebo použít *izolovanou hodnotu Salt* pouze pro některé podmnožiny klíčů.  Pokud používáte izolovanou sůl, měli byste dále filtrovat, abyste izolují podmnožinu nasolených klíčů v rámci spojení map. Další možností je zavést sloupec intervalu a předem agregovat do kontejnerů.

Dalším faktorem způsobující pomalé spojení může být typ spojení. Ve výchozím nastavení používá `SortMerge` Spark typ spojení. Tento typ spojení je nejvhodnější pro velké datové sady, ale je jinak výpočetně nákladný, protože před jejich sloučením musí nejprve seřadit levou a pravou stranu dat.

`Broadcast` Spojení je nejvhodnější pro menší datové sady nebo v případě, že je jedna strana spojení mnohem menší než druhá strana. Tento typ spojení vysílá jednu stranu na všechny prováděcí moduly, a proto vyžaduje více paměti pro vysílání obecně.

Typ spojení můžete v konfiguraci `spark.sql.autoBroadcastJoinThreshold`změnit nastavením nebo můžete nastavit pomocný parametr Join pomocí rozhraní API dataframe (`dataframe.join(broadcast(df2))`).

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

Pokud používáte rozdělené tabulky, pak máte `Merge` k dispozici třetí typ spojení. Správně předělená a předem vytříděná datová sada přeskočí nákladovou fázi řazení z `SortMerge` JOIN.

Pořadí spojení, zejména v složitějších dotazech. Začněte s nejvyšším selektivním spojením. Pokud je to možné, přesuňte také spojení, která zvyšují počet řádků po agregacích.

Chcete-li spravovat paralelismus, konkrétně v případě spojení kartézském, můžete přidat vnořené struktury, okna a případně přeskočit jeden nebo více kroků v rámci úlohy Spark.

## <a name="customize-cluster-configuration"></a>Přizpůsobení konfigurace clusteru

V závislosti na zatížení clusteru Spark se můžete rozhodnout, že konfigurace Sparku, která není výchozí, by vedla k optimalizaci spouštění úloh Sparku.  Proveďte testování testů pomocí ukázkových úloh a ověřte všechny konfigurace clusteru, které nejsou výchozí.

Tady je několik běžných parametrů, které můžete upravit:

* `--num-executors`Nastaví příslušný počet prováděcích modulů.
* `--executor-cores`Nastaví počet jader pro každý prováděcí modul. Obvykle byste měli mít vykonavatele střední velikosti, protože jiné procesy spotřebovávají z dostupné paměti.
* `--executor-memory`Nastaví velikost paměti pro každý prováděcí modul, který ovládá velikost haldy na VLÁKNě. Pro režii spuštění byste měli ponechat nějakou paměť.

### <a name="select-the-correct-executor-size"></a>Vyberte správnou velikost prováděcího modulu.

Při rozhodování o konfiguraci prováděcího modulu zvažte režii uvolňování paměti Java.

* Faktory pro snížení velikosti prováděcího modulu:
    1. Zmenšení velikosti haldy pod 32 GB, aby se zajistila režie GC < 10%.
    2. Snižte počet jader pro zachování režie GC < 10%.

* Faktory pro zvýšení velikosti prováděcího modulu:
    1. Snižte náklady na komunikaci mezi prováděcími moduly.
    2. Snižte počet otevřených připojení mezi prováděcími moduly (N2) na větších clusterech (> 100 prováděcích modulů).
    3. Zvětšete velikost haldy pro úlohy náročné na paměť.
    4. Volitelné: Snižte režii paměti vázané na vykonavatele.
    5. Volitelné: Zvyšte využití a souběžnost tím, že se přeruší předplatné procesoru.

Při výběru velikosti prováděcího modulu se jako obecné pravidlo pomění:
    
1. Začněte s 30 GB na vykonavatel a distribuujte dostupné jádra počítačů.
2. Zvyšte počet jader prováděcích modulů pro větší clustery (prováděcí modul > 100).
3. Zvětšete nebo zmenšete velikost na základě zkušebních běhů a výše uvedených faktorů, jako je například režie GC.

Při spouštění souběžných dotazů Vezměte v úvahu následující skutečnosti:

1. Začněte s 30 GB na prováděcí modul a všechny jádro počítačů.
2. Vytvořte několik paralelních aplikací Spark, protože se přeruší předplatné CPU (přibližně 30% zlepšení latence).
3. Distribuujte dotazy napříč paralelními aplikacemi.
4. Zvětšete nebo zmenšete velikost na základě zkušebních běhů a výše uvedených faktorů, jako je například režie GC.

Monitorujte výkon dotazů pro odlehlé nebo jiné problémy s výkonem. Prohlédněte si zobrazení Časová osa, SQL Graph, Statistika úloh a tak dále. Někdy je jeden nebo několik prováděcích modulů pomalejší než u ostatních a provádění úloh trvá mnohem déle. K tomu často dochází na větších clusterech (> 30 uzlů). V takovém případě rozdělte práci do většího počtu úkolů, aby mohl Scheduler kompenzovat pomalé úlohy. Například musí mít alespoň dvakrát tolik úloh jako počet jader prováděcích modulů v aplikaci. Můžete také povolit spekulativní provádění úkolů pomocí `conf: spark.speculation = true`.

## <a name="optimize-job-execution"></a>Optimalizace provádění úloh

* Ukládat do mezipaměti podle potřeby, například pokud používáte data dvakrát a pak je Uložit do mezipaměti.
* Všesměrové vysílání proměnných do všech prováděcích modulů. Proměnné jsou serializovány pouze jednou, což vede k rychlejšímu vyhledávání.
* Použijte fond vláken na ovladači, což vede k rychlejší operaci pro mnoho úkolů.

Pravidelně monitorujte spuštěné úlohy a problémy s výkonem. Pokud potřebujete získat lepší přehled o určitých problémech, zvažte jeden z následujících nástrojů pro profilaci výkonu:

* [Nástroj Intel PAL](https://github.com/intel-hadoop/PAT) monitoruje využití šířky pásma procesoru, úložiště a sítě.
* [Oracle Java 8 řídí profily řídicích prvků](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) Spark a prováděcí kód.

Klíčem k výkonu dotazů Spark 2. x je modul Tungsten, který závisí na generování celého fáze vytváření kódu. V některých případech může být generování celého fáze kódu zakázáno. Například pokud použijete neproměnlivý typ (`string`) v agregačním výrazu, `SortAggregate` zobrazí se místo `HashAggregate`. Pro lepší výkon například vyzkoušejte následující a pak znovu povolte generování kódu:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Další kroky

* [Ladění úloh Apache Spark spuštěných ve službě Azure HDInsight](apache-spark-job-debugging.md)
* [Správa prostředků pro cluster Apache Spark v HDInsight](apache-spark-resource-manager.md)
* [Použití REST API Apache Spark k odeslání vzdálených úloh do clusteru Apache Spark](apache-spark-livy-rest-interface.md)
* [Apache Spark ladění](https://spark.apache.org/docs/latest/tuning.html)
* [Jak ve skutečnosti ladit úlohy Apache Spark, aby fungovaly](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
* [Serializace kryo](https://github.com/EsotericSoftware/kryo)
