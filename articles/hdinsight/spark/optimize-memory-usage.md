---
title: Optimalizace využití paměti v Apache Spark – Azure HDInsight
description: Naučte se optimalizovat využití paměti v Apache Spark ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: d54cf4e9025dfd75d9029dec534fc5dc5dd990a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944183"
---
# <a name="memory-usage-optimization-for-apache-spark"></a>Optimalizace využití paměti pro Apache Spark

Tento článek popisuje, jak optimalizovat správu paměti Apache Spark clusteru, aby se co nejlépe vyzpůsobilo pro Azure HDInsight.

## <a name="overview"></a>Přehled

Spark funguje tak, že umístí data do paměti. Takže Správa prostředků paměti je klíčovým aspektem Optimalizace spouštění úloh Spark.  Existuje několik postupů, které můžete použít pro efektivní použití paměti clusteru.

* Preferovat menší datové oddíly a účet pro velikost, typy a distribuci dat v strategii dělení.
* Zvažte novější, efektivnější [`Kryo data serialization`](https://github.com/EsotericSoftware/kryo) , a ne výchozí serializaci Java.
* Raději použijte PŘÍZi, jak je oddělením `spark-submit` Batch.
* Monitorování a optimalizace nastavení konfigurace Sparku

Pro referenci se v dalším obrázku zobrazí struktura paměti Spark a některé parametry paměti vykonavatele klíče.

## <a name="spark-memory-considerations"></a>Požadavky na paměť Spark

Pokud používáte Apache Hadoop PŘÍZe, pak PŘÍZe řídí paměť využívanou všemi kontejnery v každém uzlu Spark.  Následující diagram znázorňuje klíčové objekty a jejich vztahy.

![Správa paměti PŘÍZe Spark](./media/apache-spark-perf/apache-yarn-spark-memory.png)

Pokud chcete adresovat zprávy o nedostatku paměti, zkuste:

* Projděte si přehledy DAG Management. Zmenšuje se ze zdrojů dat na straně mapy, předrozdělitelné (neboli nastavit interval) zdrojových dat, maximalizujte jednotlivá započet a snižte množství odesílaných dat.
* Preferovat `ReduceByKey` s pevným omezením paměti `GroupByKey` , které poskytuje agregace, okna a další funkce, ale má Ann nevázaný limit paměti.
* Raději `TreeReduce` , což více funguje na vykonavatelích nebo oddílech, na `Reduce` , který vše funguje na ovladači.
* Místo objektů RDD na nižší úrovni použijte místo nich datový rámec.
* Vytvořte ComplexTypes, které zapouzdřují akce, například "horních N", různé agregace nebo operace s okny.

Další kroky pro řešení potíží najdete v tématu [výjimky OutOfMemoryError pro Apache Spark ve službě Azure HDInsight](apache-spark-troubleshoot-outofmemory.md).

## <a name="next-steps"></a>Další kroky

* [Optimalizujte zpracování dat pro Apache Spark](optimize-cluster-configuration.md)
* [Optimalizace úložiště dat pro Apache Spark](optimize-data-storage.md)
* [Optimalizuje konfiguraci clusteru pro Apache Spark.](optimize-cluster-configuration.md)
