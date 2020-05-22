---
title: Optimalizace úloh Sparku pro výkon – Azure HDInsight
description: Zobrazit společné strategie pro nejlepší výkon Apache Sparkch clusterů ve službě Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: f92a351087670ce0b37921a496eabfa883a3b1fc
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780114"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Optimalizace úloh Apache Spark v HDInsight

Tento článek poskytuje přehled strategií pro optimalizaci Apache Spark úloh v Azure HDInsight.

## <a name="overview"></a>Přehled

Výkon vašich Apache Spark úloh závisí na několika faktorech. Mezi tyto faktory výkonu patří: způsob ukládání dat, způsob konfigurace clusteru a operace, které se používají při zpracování dat.

Mezi běžné problémy, které byste mohli dotýkat, patří omezení paměti z důvodu nesprávné velikosti prováděcích modulů, dlouhotrvajících operací a úloh, které způsobují kartézském operace.

Existují také různé strategie, které vám pomůžou překonat tyto výzvy, jako je ukládání do mezipaměti a umožnění zešikmení dat.

V každém z následujících článků můžete najít běžné výzvy a řešení pro jiný aspekt optimalizace Sparku.

* [Optimalizace úložiště dat](optimize-data-storage.md)
* [Optimalizace zpracování dat](optimize-data-processing.md)
* [Optimalizace využití paměti](optimize-memory-usage.md)
* [Optimalizace konfigurace clusteru](optimize-cluster-configuration.md)

## <a name="next-steps"></a>Další kroky

* [Ladění úloh Apache Spark spuštěných ve službě Azure HDInsight](apache-spark-job-debugging.md)
* [Správa prostředků pro cluster Apache Spark v HDInsight](apache-spark-resource-manager.md)
* [Konfigurace nastavení Apache Sparku](apache-spark-settings.md)
