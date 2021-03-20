---
title: Optimalizace Apache Spark konfigurace clusteru – Azure HDInsight
description: Naučte se konfigurovat cluster Apache Spark pro maximalizaci propustnosti v Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: f70e9f242b0f12abf58e72554e7cb6819ce8f7a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944736"
---
# <a name="cluster-configuration-optimization-for-apache-spark"></a>Optimalizace konfigurace clusteru pro Apache Spark

Tento článek popisuje, jak optimalizovat konfiguraci Apache Spark clusteru, aby se co nejlépe vyzpůsobilo pro Azure HDInsight.

## <a name="overview"></a>Přehled

V závislosti na zatížení clusteru Spark se můžete rozhodnout, že konfigurace Sparku, která není výchozí, by vedla k optimalizaci spouštění úloh Sparku.  Proveďte testování srovnávacích testů s ukázkovými úlohami a ověřte všechny konfigurace clusteru, které nejsou výchozí.

Tady je několik běžných parametrů, které můžete upravit:

|Parametr |Popis |
|---|---|
|--NUM-prováděcí moduly|Nastaví příslušný počet prováděcích modulů.|
|--prováděcí modul – jádra|Nastaví počet jader pro každý prováděcí modul. Obvykle byste měli mít vykonavatele střední velikosti, protože jiné procesy spotřebovávají z dostupné paměti.|
|--prováděcí modul – paměť|Nastaví velikost paměti pro každý prováděcí modul, který ovládá velikost haldy na VLÁKNě. Pro režie spuštění ponechte nějakou paměť.|

## <a name="select-the-correct-executor-size"></a>Vyberte správnou velikost prováděcího modulu.

Při rozhodování o konfiguraci prováděcího modulu zvažte režii uvolňování paměti Java.

* Faktory pro snížení velikosti prováděcího modulu:
    1. Zmenšení velikosti haldy pod 32 GB, aby se zajistila režie GC < 10%.
    2. Snižte počet jader pro zachování režie GC < 10%.

* Faktory pro zvýšení velikosti prováděcího modulu:
    1. Snižte náklady na komunikaci mezi prováděcími moduly.
    2. Snižte počet otevřených připojení mezi prováděcími moduly (N2) na větších clusterech (prováděcích modulech >100).
    3. Zvětšete velikost haldy pro úlohy náročné na paměť.
    4. Volitelné: Snižte nároky na paměť pro vykonavatele.
    5. Volitelné: Zvyšte využití a souběžnost tím, že se přeruší předplatné procesoru.

Při výběru velikosti prováděcího modulu se jako obecné pravidlo použije:

1. Začněte s 30 GB na vykonavatel a distribuujte dostupné jádra počítačů.
2. Zvyšte počet jader prováděcích modulů pro větší clustery (prováděcí modul > 100).
3. Upravte velikost na základě zkušebních běhů a na předchozích faktorech, jako je například režie GC.

Při spouštění souběžných dotazů Vezměte v úvahu:

1. Začněte s 30 GB na prováděcí modul a všechny jádro počítačů.
2. Vytvořte několik paralelních aplikací Spark, protože se přeruší předplatné CPU (přibližně 30% zlepšení latence).
3. Distribuujte dotazy napříč paralelními aplikacemi.
4. Upravte velikost na základě zkušebních běhů a na předchozích faktorech, jako je například režie GC.

Další informace o použití Ambari ke konfiguraci prováděcích modulů najdete v tématu [nastavení Apache Spark – vykonavatelé Spark](apache-spark-settings.md#configuring-spark-executors).

Monitorujte výkon dotazů pro odlehlé nebo jiné problémy s výkonem. Prohlédněte si zobrazení Časová osa. Také SQL Graph, Statistika úloh a tak dále. Informace o ladění úloh Sparku pomocí PŘÍZu a serveru historie Spark najdete v tématu [ladění Apache Spark úlohy spuštěné v Azure HDInsight](apache-spark-job-debugging.md). Tipy k použití serveru časové osy PŘÍZ najdete v tématu [přístup Apache Hadoopch protokolů aplikací příze](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Někdy je jeden nebo několik prováděcích modulů pomalejší než u ostatních a provádění úloh trvá mnohem déle. Tato zpomalace dochází často na větších clusterech (> 30 uzlů). V takovém případě rozdělte práci do většího počtu úkolů, aby mohl Scheduler kompenzovat pomalé úlohy. Například musí mít alespoň dvakrát tolik úloh jako počet jader prováděcích modulů v aplikaci. Můžete také povolit spekulativní provádění úkolů pomocí `conf: spark.speculation = true` .

## <a name="next-steps"></a>Další kroky

* [Optimalizujte zpracování dat pro Apache Spark](optimize-cluster-configuration.md)
* [Optimalizace úložiště dat pro Apache Spark](optimize-data-storage.md)
* [Optimalizace využití paměti pro Apache Spark](optimize-memory-usage.md)
