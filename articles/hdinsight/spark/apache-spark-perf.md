---
title: Optimalizace úloh Sparku pro výkon – Azure HDInsight
description: Zobrazit společné strategie pro nejlepší výkon Apache Sparkch clusterů ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 567fc2637538408d9727d07d3185a5b0e3cf20c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98929930"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Optimalizace úloh Apache Spark v HDInsight

Tento článek poskytuje přehled strategií pro optimalizaci Apache Spark úloh v Azure HDInsight.

## <a name="overview"></a>Přehled

Výkon vašich Apache Spark úloh závisí na několika faktorech. Mezi tyto faktory výkonu patří: způsob ukládání dat, způsob konfigurace clusteru a operace, které se používají při zpracování dat.

Mezi běžné problémy, které by se vám mohly vzniknout, patří: omezení paměti kvůli nesprávně změně velikosti prováděcích modulů, dlouhotrvajících operací a úloh, které způsobují kartézském operace.

K dispozici je také mnoho optimalizací, které vám pomůžou překonat tyto výzvy, jako je ukládání do mezipaměti a umožnění zešikmení dat.

V každém z následujících článků můžete najít informace o různých aspektech funkce pro optimalizaci Sparku.

* [Optimalizace úložiště dat pro Apache Spark](optimize-data-storage.md)
* [Optimalizujte zpracování dat pro Apache Spark](optimize-data-processing.md)
* [Optimalizace využití paměti pro Apache Spark](optimize-memory-usage.md)
* [Optimalizace konfigurace clusteru HDInsight pro Apache Spark](optimize-cluster-configuration.md)

## <a name="next-steps"></a>Další kroky

* [Ladění úloh Apache Spark spuštěných ve službě Azure HDInsight](apache-spark-job-debugging.md)
* [Správa prostředků pro cluster Apache Spark v HDInsight](apache-spark-resource-manager.md)
* [Konfigurace nastavení Apache Sparku](apache-spark-settings.md)
