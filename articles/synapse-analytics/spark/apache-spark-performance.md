---
title: Optimalizace úloh Spark pro výkon v Azure Synapse Analytics
description: Tento článek obsahuje úvod do Apache Spark v Azure Synapse Analytics a různé koncepty.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 6ffe7f3d9faf82c892975e9ffa03b383d3610c36
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424619"
---
# <a name="optimize-apache-spark-jobs-preview-in-azure-synapse-analytics"></a>Optimalizace úloh Apache Spark (preview) v Azure Synapse Analytics

Přečtěte si, jak optimalizovat konfiguraci clusteru [Apache Spark](https://spark.apache.org/) pro konkrétní úlohu.  Nejběžnější výzvou je tlak paměti, protože nesprávné konfigurace (zejména chybné velikosti vykonavatele), dlouhotrvající operace a úkoly, které vedou k operacím kartézy. Můžete urychlit úlohy s vhodným ukládáním do mezipaměti a povolením [zkosení dat](#optimize-joins-and-shuffles). Pro nejlepší výkon, monitorování a kontrolu dlouhotrvající a prostředky náročné spuštění úloh y Spark.

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

Nejlepší formát pro výkon je parkety s *elegantní kompresí*, což je výchozí hodnota v Spark 2.x. Parkety ukládají data ve sloupcovém formátu a jsou vysoce optimalizované ve Sparku. Kromě toho *při snappy komprese* může mít za následek větší soubory, než říkají komprese gzip. Vzhledem k rozdělení charakter těchto souborů budou dekomprimovat rychleji]

## <a name="use-the-cache"></a>Použití mezipaměti

Spark poskytuje své vlastní nativní mechanismy ukládání do mezipaměti, `.cache()`které `CACHE TABLE`lze použít různými metodami, jako `.persist()`jsou , , a . Tento nativní ukládání do mezipaměti je efektivní s malými sadami dat, stejně jako v kanálech ETL, kde je třeba ukládat do mezipaměti průběžné výsledky. Nativní ukládání do mezipaměti Spark však v současné době nefunguje dobře s dělením, protože tabulka uložená v mezipaměti neuchovává data oddílů.

## <a name="use-memory-efficiently"></a>Efektivní využití paměti

Spark funguje tak, že umísťuje data do paměti, takže správa paměťových prostředků je klíčovým aspektem optimalizace provádění úloh Spark.  Existuje několik technik, které můžete použít pro efektivní využití paměti clusteru.

* Preferujte menší datové oddíly a účet pro velikost dat, typy a distribuci ve strategii dělení.
* Zvažte novější, efektivnější [serializaci kryo dat](https://github.com/EsotericSoftware/kryo), spíše než výchozí serializace Java.
* Monitorujte a vylaďte nastavení konfigurace Spark.

Pro vaši referenci se na následujícím obrázku zobrazí struktura paměti Spark a některé parametry paměti vykonavatele klíčů.

### <a name="spark-memory-considerations"></a>Důležité informace o paměti jiskry

Apache Spark v Azure Synapse používá YARN [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), YARN řídí maximální součet paměti používané všechny kontejnery na každém uzlu Spark.  Následující diagram znázorňuje klíčové objekty a jejich vztahy.

![Správa paměti YARN Spark](./media/apache-spark-perf/apache-yarn-spark-memory.png)

Chcete-li adresovat zprávy s nepaměti, vyzkoušejte:

* Projděte si dag management shuffles. Snižte snížení maskérna, předoddíl (nebo bucketize) zdrojová data, maximalizovat jednotlivé shuffles a snížit množství odeslaných dat.
* Preferují `ReduceByKey` s jeho `GroupByKey`pevné omezení paměti , který poskytuje agregace, okna a další funkce, ale má ann neomezené omezení paměti.
* Preferovat `TreeReduce`, který dělá více práce na vykonavatele nebo oddíly, na `Reduce`, který dělá všechny práce na ovladači.
* Využijte dataframe spíše než objekty RDD nižší úrovně.
* Vytvořte ComplexTypes, které zapouzdřují akce, jako je například "Top N", různé agregace nebo operace oken.

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

### <a name="select-the-correct-executor-size"></a>Výběr správné velikosti vykonavatele

Při rozhodování o konfiguraci vykonavatele, zvažte java uvolňování paměti (GC) režie.

* Faktory pro snížení velikosti exekutora:
  * Zmenšete velikost haldy pod 32 GB, aby < 10 % byla <
  * Snižte počet jader, aby se < 10% <

* Faktory pro zvýšení velikosti exekutora:
  * Snižte režii komunikace mezi vykonavateli.
  * Snižte počet otevřených připojení mezi vykonavateli (N2) ve větších clusterech (>100 vykonavatelů).
  * Zvětšete velikost haldy tak, aby vyhovovala úlohám náročným na paměť.
  * Volitelné: Snížit nároky na paměť vykonavatele.
  * Volitelné: Zvyšte využití a souběžnost tím, že se přehlášuje procesor.

Jako obecné pravidlo při výběru velikosti vykonavatele:

* Začněte s 30 GB na vykonavatele a distribuujte dostupná jádra počítače.
* Zvyšte počet jader vykonavatele pro větší clustery (> 100 vykonavatelů).
* Upravte velikost na základě zkušebních spuštění i předchozích faktorů, jako je například režie gc.

Při spouštění souběžných dotazů zvažte následující skutečnosti:

* Začněte s 30 GB na vykonavatele a všechna jádra počítače.
* Vytvořte více paralelních aplikací Spark tím, že se přehlášuje težce procesoru (přibližně 30% zlepšení latence).
* Distribuujte dotazy mezi paralelními aplikacemi.
* Upravte velikost na základě zkušebních spuštění i předchozích faktorů, jako je například režie gc.

Sledujte výkon dotazu pro odlehlé hodnoty nebo jiné problémy s výkonem, a to tak, jak se podíváte na zobrazení časové osy, graf SQL, statistiky úloh a tak dále. Někdy jeden nebo několik vykonavatelů jsou pomalejší než ostatní a úkoly trvat mnohem déle, než se provádí. K tomu často dochází ve větších clusterech (> 30 uzlech). V takovém případě rozdělte práci na větší počet úkolů, aby plánovač mohl kompenzovat pomalé úkoly. 

Například mají alespoň dvakrát tolik úkolů jako počet jader prováděcího modulu v aplikaci. Můžete také povolit spekulativní provádění `conf: spark.speculation = true`úkolů s .

## <a name="optimize-job-execution"></a>Optimalizace provádění úloh

* Cache podle potřeby, například pokud použijete data dvakrát, pak do mezipaměti.
* Proměnné vysílání všem vykonavatelům. Proměnné jsou serializovány pouze jednou, což vede k rychlejšívyhledávání.
* Použijte fond vláken na ovladači, což má za následek rychlejší provoz pro mnoho úkolů.

Klíčem k výkonu dotazů Spark 2.x je modul tungsten, který závisí na generování kódu celé fáze. V některých případech může být zakázáno generování kódu celé fáze. 

Pokud například ve výrazu agregace použijete neproměnlivý `SortAggregate` typ ( `HashAggregate``string`), zobrazí se místo . Chcete-li například dosáhnout lepšího výkonu, vyzkoušejte následující a znovu povolte generování kódu:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Další kroky

- [Ladění Apache Spark](https://spark.apache.org/docs/latest/tuning.html)
- [Jak skutečně naladit Apache Spark práce tak, aby práce](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
- [Kryo serializace](https://github.com/EsotericSoftware/kryo)
