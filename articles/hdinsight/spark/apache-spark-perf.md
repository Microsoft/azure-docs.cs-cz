---
title: Optimalizace úloh Sparku pro výkon – Azure HDInsight
description: Zobrazit společné strategie pro nejlepší výkon Apache Sparkch clusterů ve službě Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: d808339dd842e88e74efce5d56d12bc5250eb238
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97029290"
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
