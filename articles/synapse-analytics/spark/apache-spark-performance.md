---
title: Optimalizace úloh Sparku pro výkon
description: Tento článek poskytuje Úvod do Apache Spark ve službě Azure synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: f8eb87909ffdf9ce15108d78bed425bf6c142262
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91249463"
---
# <a name="optimize-apache-spark-jobs-preview-in-azure-synapse-analytics"></a>Optimalizace úloh Apache Spark (Preview) ve službě Azure synapse Analytics

Naučte se optimalizovat konfiguraci [Apache Spark](https://spark.apache.org/) clusteru pro konkrétní zatížení.  Nejběžnější výzvou je zatížení paměti kvůli nesprávným konfiguracím (zejména kvůli nesprávně nastavené velikosti exekutorů), dlouhotrvajícím operacím a úlohám, které vedou ke kartézským operacím. Úlohy můžete urychlit s vhodným ukládáním do mezipaměti a díky tomu, aby bylo možné [Zkosit data](#optimize-joins-and-shuffles). Nejlepšího výkonu dosáhnete, když budete monitorovat a kontrolovat dlouhodobá spuštění úloh Sparku, která jsou náročná na prostředky.

Následující části popisují běžné optimalizace úloh Spark a doporučení.

## <a name="choose-the-data-abstraction"></a>Zvolit abstrakci dat

Starší verze Sparku používají RDD k abstraktním datům, Spark 1,3 a 1,6 zavedla datové rámce a datové sady v uvedeném pořadí. Vezměte v úvahu následující relativní věci:

* **Datové rámce**
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
* **Sady RDD**
  * Nemusíte používat RDD, pokud nepotřebujete vytvářet nové vlastní RDD.
  * Žádná optimalizace dotazů prostřednictvím Catalyst.
  * Nevytváření celého připraveného kódu.
  * Vysoká režie GC.
  * Musí používat starší rozhraní API Spark 1. x.

## <a name="use-optimal-data-format"></a>Použití optimálního formátu dat

Spark podporuje mnoho formátů, jako je CSV, JSON, XML, Parquet, orc a Avro. Spark se dá rozšířit tak, aby podporoval mnoho dalších formátů s externími zdroji dat. Další informace najdete v tématu [Apache Spark balíčky](https://spark-packages.org).

Nejlepší formát pro výkon je Parquet s *kompresí s přichycením*, což je výchozí hodnota ve Sparku 2. x. Parquet ukládá data ve sloupcovém formátu a je vysoce optimalizovaná ve Sparku. I když *Komprese přichycení* může mít za následek větší soubory, než říkáte kompresi gzip. Vzhledem k povaze těchto souborů se tyto soubory dekomprimuje rychleji]

## <a name="use-the-cache"></a>Použití mezipaměti

Spark poskytuje vlastní nativní mechanismy ukládání do mezipaměti, které je možné použít prostřednictvím různých metod, jako jsou `.persist()` , `.cache()` a `CACHE TABLE` . Tato nativní mezipaměť je platná u malých datových sad i v kanálech ETL, kde potřebujete ukládat do mezipaměti mezilehlé výsledky. Nicméně nativní ukládání do mezipaměti v současnosti nefunguje dobře s vytvářením oddílů, protože tabulka v mezipaměti neuchovává data dělení.

## <a name="use-memory-efficiently"></a>Efektivní využití paměti

Spark funguje tak, že umístí data do paměti, takže Správa prostředků paměti je klíčovým aspektem Optimalizace spouštění úloh Sparku.  Existuje několik postupů, které můžete použít pro efektivní použití paměti clusteru.

* Preferovat menší datové oddíly a účet pro velikost, typy a distribuci dat v strategii dělení.
* Zvažte novější a efektivnější [serializaci dat kryo](https://github.com/EsotericSoftware/kryo)místo výchozí serializace Java.
* Monitorování a optimalizace nastavení konfigurace Sparku

Pro referenci se v dalším obrázku zobrazí struktura paměti Spark a některé parametry paměti vykonavatele klíče.

### <a name="spark-memory-considerations"></a>Požadavky na paměť Spark

Apache Spark ve službě Azure synapse používá PŘÍZe [Apache HADOOP příze](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), příze řídí maximální součet paměti využívané všemi kontejnery na jednotlivých uzlech Spark.  Následující diagram znázorňuje klíčové objekty a jejich vztahy.

![Správa paměti PŘÍZe Spark](./media/apache-spark-perf/apache-yarn-spark-memory.png)

Pokud chcete adresovat zprávy o nedostatku paměti, zkuste:

* Projděte si přehledy DAG Management. Zmenšete ze zdrojových dat zmenšeného na straně mapy, před rozdělením na oddíly (nebo nastavit interval), maximalizujte jednotlivá místa a snižte množství odesílaných dat.
* Preferovat `ReduceByKey` s pevným omezením paměti `GroupByKey` , které poskytuje agregace, okna a další funkce, ale má Ann nevázaný limit paměti.
* Raději `TreeReduce` , což více funguje na vykonavatelích nebo oddílech, na `Reduce` , který vše funguje na ovladači.
* Využijte místo objektů RDD na nižší úrovni datový rámec.
* Vytvořte ComplexTypes, které zapouzdřují akce, například "horních N", různé agregace nebo operace s okny.

## <a name="optimize-data-serialization"></a>Optimalizace serializace dat

Úlohy Sparku jsou distribuované, takže pro nejlepší výkon je důležité, aby byla vhodná serializace dat.  Pro Spark existují dvě možnosti serializace:

* Výchozím nastavením je serializace Java.
* Serializace kryo je novější formát a může mít za následek rychlejší a kompaktnější serializaci než Java.  Kryo vyžaduje, abyste v programu zaregistrovali třídy a zatím nepodporovaly všechny Serializovatelné typy.

## <a name="use-bucketing"></a>Použití rozdělování do kbelíků

Sestavování se podobá dělení dat, ale každá sada může obsahovat sadu hodnot sloupce, nikoli jenom jednu. Vytváření bloků funguje dobře pro dělení na velké (v milionech a více) číslech hodnot, jako jsou například identifikátory produktu. Sada je určena pomocí algoritmu hash pro klíč kontejneru řádku. Rozdělené tabulky nabízejí jedinečné optimalizace, protože ukládají metadata o způsobu jejich seřazení a řazení.

Některé pokročilé funkce pro seintervalování jsou tyto:

* Optimalizace dotazů založená na seintervalování meta-informací.
* Optimalizované agregace.
* Optimalizované spojení.

Můžete použít dělení a zablokování současně.

## <a name="optimize-joins-and-shuffles"></a>Optimalizace spojení a náhodného prohazování metodou shuffle

Pokud máte pomalé úlohy při spojení nebo náhodně, příčinou je pravděpodobně *zkosení dat*, což je asymetrie v datech úlohy. Například úloha mapy může trvat 20 sekund, ale při spuštění úlohy, kde se data připojí nebo rozchází, trvá hodiny. Chcete-li opravit zešikmení dat, měli byste nasoleit celý klíč nebo použít *izolovanou hodnotu Salt* pouze pro některé podmnožiny klíčů. Pokud používáte izolovanou sůl, měli byste další filtr k izolaci vaší podmnožiny nasolených klíčů v rámci spojení map. Další možností je zavést sloupec intervalu a předem agregovat do kontejnerů.

Dalším faktorem způsobující pomalé spojení může být typ spojení. Ve výchozím nastavení používá Spark `SortMerge` typ spojení. Tento typ spojení je nejvhodnější pro velké datové sady, ale je jinak výpočetně nákladný, protože před jejich sloučením musí nejprve seřadit levou a pravou stranu dat.

`Broadcast`Spojení je nejvhodnější pro menší datové sady nebo v případě, že je jedna strana spojení mnohem menší než druhá strana. Tento typ spojení vysílá jednu stranu na všechny prováděcí moduly, a proto vyžaduje více paměti pro vysílání obecně.

Typ spojení můžete v konfiguraci změnit nastavením `spark.sql.autoBroadcastJoinThreshold` nebo můžete nastavit pomocný parametr Join pomocí rozhraní API dataframe ( `dataframe.join(broadcast(df2))` ).

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

Pokud používáte rozdělené tabulky, pak máte k dispozici třetí typ spojení `Merge` . Správně předělená a předem vytříděná datová sada přeskočí nákladovou fázi řazení z `SortMerge` JOIN.

Pořadí spojení, zejména v složitějších dotazech. Začněte s nejvyšším selektivním spojením. Pokud je to možné, přesuňte také spojení, která zvyšují počet řádků po agregacích.

Pro správu paralelismu pro kartézském spojení můžete přidat vnořené struktury, okna a možná přeskočit jeden nebo více kroků v rámci úlohy Spark.

### <a name="select-the-correct-executor-size"></a>Vyberte správnou velikost prováděcího modulu.

Při rozhodování o konfiguraci prováděcího modulu zvažte režii uvolňování paměti Java.

* Faktory pro snížení velikosti prováděcího modulu:
  * Zmenšení velikosti haldy pod 32 GB, aby se zajistila režie GC < 10%.
  * Snižte počet jader pro zachování režie GC < 10%.

* Faktory pro zvýšení velikosti prováděcího modulu:
  * Snižte náklady na komunikaci mezi prováděcími moduly.
  * Snižte počet otevřených připojení mezi prováděcími moduly (N2) na větších clusterech (prováděcích modulech >100).
  * Zvětšete velikost haldy pro úlohy náročné na paměť.
  * Volitelné: Snižte nároky na paměť pro vykonavatele.
  * Volitelné: Zvyšte využití a souběžnost tím, že se přeruší předplatné procesoru.

Při výběru velikosti prováděcího modulu se jako obecné pravidlo pomění:

* Začněte s 30 GB na vykonavatel a distribuujte dostupné jádra počítačů.
* Zvyšte počet jader prováděcích modulů pro větší clustery (prováděcí modul > 100).
* Upravte velikost na základě zkušebních běhů a na předchozích faktorech, jako je například režie GC.

Při spouštění souběžných dotazů Vezměte v úvahu následující skutečnosti:

* Začněte s 30 GB na prováděcí modul a všechny jádro počítačů.
* Vytvořte několik paralelních aplikací Spark, protože se přeruší předplatné CPU (přibližně 30% zlepšení latence).
* Distribuujte dotazy napříč paralelními aplikacemi.
* Upravte velikost na základě zkušebních běhů a na předchozích faktorech, jako je například režie GC.

Monitorujte výkon dotazů pro odlehlé nebo jiné problémy s výkonem. Prohlédněte si zobrazení Časová osa, SQL Graph, Statistika úloh a tak dále. Někdy je jeden nebo několik prováděcích modulů pomalejší než u ostatních a provádění úloh trvá mnohem déle. K tomu často dochází na větších clusterech (> 30 uzlů). V takovém případě rozdělte práci do většího počtu úkolů, aby mohl Scheduler kompenzovat pomalé úlohy. 

Například musí mít alespoň dvakrát tolik úloh jako počet jader prováděcích modulů v aplikaci. Můžete také povolit spekulativní provádění úkolů pomocí `conf: spark.speculation = true` .

## <a name="optimize-job-execution"></a>Optimalizace spouštění úloh

* Ukládat do mezipaměti podle potřeby, například pokud používáte data dvakrát a pak je Uložit do mezipaměti.
* Všesměrové vysílání proměnných do všech prováděcích modulů. Proměnné jsou serializovány pouze jednou, což vede k rychlejšímu vyhledávání.
* Použijte fond vláken na ovladači, což vede k rychlejší operaci pro mnoho úkolů.

Klíčem k výkonu dotazů Spark 2. x je modul Tungsten, který závisí na generování celého fáze vytváření kódu. V některých případech může být generování celého fáze kódu zakázáno. 

Například pokud použijete neproměnlivý typ ( `string` ) v agregačním výrazu, `SortAggregate` zobrazí se místo `HashAggregate` . Pro lepší výkon například vyzkoušejte následující a pak znovu povolte generování kódu:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Další kroky

- [Apache Spark ladění](https://spark.apache.org/docs/latest/tuning.html)
- [Jak ve skutečnosti ladit úlohy Apache Spark, aby fungovaly](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
- [Serializace kryo](https://github.com/EsotericSoftware/kryo)
