---
title: Optimalizace úložiště dat pro Apache Spark – Azure HDInsight
description: Naučte se optimalizovat úložiště dat pro použití s Apache Spark v Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 10f99bdc4a5d418ae1b432a6799c5979e473c5ed
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98926996"
---
# <a name="data-storage-optimization-for-apache-spark"></a>Optimalizace úložiště dat pro Apache Spark

Tento článek popisuje strategie pro optimalizaci úložiště dat pro efektivní Apache Spark provádění úloh v Azure HDInsight.

## <a name="overview"></a>Přehled

Spark podporuje mnoho formátů, jako je CSV, JSON, XML, Parquet, orc a Avro. Spark se dá rozšířit tak, aby podporoval mnoho dalších formátů s externími zdroji dat. Další informace najdete v tématu [Apache Spark balíčky](https://spark-packages.org).

Nejlepší formát pro výkon je Parquet s *kompresí s přichycením*, což je výchozí hodnota ve Sparku 2. x. Parquet ukládá data ve sloupcovém formátu a je vysoce optimalizovaná ve Sparku.

## <a name="choose-data-abstraction"></a>Zvolit abstrakci dat

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

## <a name="select-default-storage"></a>Vybrat výchozí úložiště

Když vytváříte nový cluster Spark, můžete jako výchozí úložiště clusteru vybrat Azure Blob Storage nebo Azure Data Lake Storage. Obě možnosti poskytují výhody dlouhodobého úložiště pro přechodný clustery. Takže se vaše data při odstranění clusteru automaticky neodstraní. Můžete znovu vytvořit přechodný cluster a stále získat přístup k datům.

| Store Type | Systém souborů | Rychlost | Dočasný | Případy použití |
| --- | --- | --- | --- | --- |
| Azure Blob Storage | **wasb:**//URL/ | **Standard** | Yes | Přechodný cluster |
| Azure Blob Storage (zabezpečení) | **wasbs:**//URL/ | **Standard** | Yes | Přechodný cluster |
| Azure Data Lake Storage Gen2| **ABFS:**//URL/ | **Zrychlení** | Yes | Přechodný cluster |
| Azure Data Lake Storage Gen 1| **ADL:**//URL/ | **Zrychlení** | Yes | Přechodný cluster |
| Místní HDFS | **HDFS:**//URL/ | **Způsobem** | No | Interaktivní cluster 24/7 |

Úplný popis možností úložiště najdete v tématu [porovnání možností úložiště pro použití s clustery Azure HDInsight](../hdinsight-hadoop-compare-storage-options.md).

## <a name="use-the-cache"></a>Použití mezipaměti

Spark poskytuje vlastní nativní mechanismy ukládání do mezipaměti, které je možné použít prostřednictvím různých metod, jako jsou `.persist()` , `.cache()` a `CACHE TABLE` . Toto nativní ukládání do mezipaměti je efektivní s malými datovými sadami a v kanálech ETL, kde je třeba ukládat mezilehlé výsledky do mezipaměti. Nicméně nativní ukládání do mezipaměti v současnosti nefunguje dobře s vytvářením oddílů, protože tabulka v mezipaměti neuchovává data dělení. Obecnější a spolehlivá technika ukládání do mezipaměti je *ukládání vrstev úložiště do mezipaměti*.

* Nativní ukládání do mezipaměti Spark (nedoporučuje se)
    * Vhodný pro malé datové sady.
    * Nefunguje s dělením na oddíly, které se mohou v budoucích vydáních Spark změnit.

* Ukládání na úrovni úložiště do mezipaměti (doporučeno)
    * Dá se implementovat na HDInsight pomocí funkce [mezipaměti v/](apache-spark-improve-performance-iocache.md) v.
    * Používá ukládání do mezipaměti SSD v paměti a SSD.

* Místní HDFS (doporučeno)
    * `hdfs://mycluster` dílčí.
    * Používá ukládání do mezipaměti SSD.
    * Po odstranění clusteru dojde ke ztrátě dat uložených v mezipaměti, které vyžaduje opětovné sestavení mezipaměti.

## <a name="optimize-data-serialization"></a>Optimalizace serializace dat

Úlohy Sparku jsou distribuované, takže pro nejlepší výkon je důležité, aby byla vhodná serializace dat.  Pro Spark existují dvě možnosti serializace:

* Výchozím nastavením je serializace Java.
* `Kryo` serializace je novější formát a může mít za následek rychlejší a kompaktnější serializaci než Java.  `Kryo` vyžaduje, abyste v programu zaregistrovali třídy a zatím nepodporovaly všechny Serializovatelné typy.

## <a name="use-bucketing"></a>Použití rozdělování do kbelíků

Sesadaování je podobné jako dělení dat. Každý kontejner ale může obsahovat sadu hodnot sloupce, nikoli jenom jeden. Tato metoda funguje dobře pro dělení velkých (v milionech nebo více) čísel hodnot, jako jsou například identifikátory produktu. Sada je určena pomocí algoritmu hash pro klíč kontejneru řádku. Rozdělené tabulky nabízejí jedinečné optimalizace, protože ukládají metadata o způsobu jejich seřazení a řazení.

Některé pokročilé funkce pro seintervalování jsou tyto:

* Optimalizace dotazů založená na seintervalování meta-informací.
* Optimalizované agregace.
* Optimalizované spojení.

Můžete použít dělení a zablokování současně.

## <a name="next-steps"></a>Další kroky

* [Optimalizujte zpracování dat pro Apache Spark](optimize-cluster-configuration.md)
* [Optimalizace využití paměti pro Apache Spark](optimize-memory-usage.md)
* [Optimalizuje konfiguraci clusteru pro Apache Spark.](optimize-cluster-configuration.md)
