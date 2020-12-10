---
title: Optimalizace zpracování dat pro Apache Spark – Azure HDInsight
description: Naučte se, jak zvolit nejúčinnější operace pro zpracování vašich dat v Apache Spark s využitím Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 3b85f6238bec4ef85d724a2fc48ea5988c3fceb2
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031721"
---
# <a name="data-processing-optimization-for-apache-spark"></a>Optimalizace zpracování dat pro Apache Spark

Tento článek popisuje, jak optimalizovat konfiguraci Apache Spark clusteru, aby se co nejlépe vyzpůsobilo pro Azure HDInsight.

## <a name="overview"></a>Přehled

Pokud máte pomalé úlohy na spojení nebo náhodně, příčinou je pravděpodobně jeho *zkosení*. V datech úlohy je asymetrie dat. Například úloha mapy může trvat 20 sekund. Ale spuštění úlohy, kde se data připojí nebo rozchází, trvá hodiny. Chcete-li opravit zešikmení dat, měli byste nasoleit celý klíč nebo použít *izolovanou hodnotu Salt* pouze pro některé podmnožiny klíčů. Pokud používáte izolovanou sůl, měli byste další filtr k izolaci vaší podmnožiny nasolených klíčů v rámci spojení map. Další možností je zavést sloupec intervalu a předem agregovat do kontejnerů.

Dalším faktorem způsobující pomalé spojení může být typ spojení. Ve výchozím nastavení používá Spark `SortMerge` typ spojení. Tento typ spojení se nejlépe hodí pro velké datové sady. V opačném případě je však výpočty nákladné, protože před jejich sloučením je třeba nejprve seřadit levou a pravou stranu dat.

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

## <a name="optimize-job-execution"></a>Optimalizace spouštění úloh

* Ukládat do mezipaměti podle potřeby, například pokud používáte data dvakrát a pak je Uložit do mezipaměti.
* Všesměrové vysílání proměnných do všech prováděcích modulů. Proměnné jsou serializovány pouze jednou, což vede k rychlejšímu vyhledávání.
* Použijte fond vláken na ovladači, což vede k rychlejší operaci pro mnoho úkolů.

Pravidelně monitorujte spuštěné úlohy a problémy s výkonem. Pokud potřebujete získat lepší přehled o určitých problémech, zvažte jeden z následujících nástrojů pro profilaci výkonu:

* [Nástroj Intel PAL](https://github.com/intel-hadoop/PAT) monitoruje využití šířky pásma procesoru, úložiště a sítě.
* [Oracle Java 8 řídí profily řídicích prvků](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) Spark a prováděcí kód.

Klíčem k výkonu dotazů Spark 2. x je modul Tungsten, který závisí na generování celého fáze vytváření kódu. V některých případech může být generování celého fáze kódu zakázáno. Například pokud použijete neproměnlivý typ ( `string` ) v agregačním výrazu, `SortAggregate` zobrazí se místo `HashAggregate` . Pro lepší výkon například vyzkoušejte následující a pak znovu povolte generování kódu:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Další kroky

* [Optimalizace úložiště dat pro Apache Spark](optimize-data-storage.md)
* [Optimalizace využití paměti pro Apache Spark](optimize-memory-usage.md)
* [Optimalizuje konfiguraci clusteru pro Apache Spark.](optimize-cluster-configuration.md)
