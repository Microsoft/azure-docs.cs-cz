---
title: Vysoká dostupnost s využitím Apache Kafka – Azure HDInsight
description: Zjistěte, jak zajistit vysokou dostupnost s využitím Apache Kafka ve službě Azure HDInsight. Naučte se obnovit rovnováhu replik oddílů v Kafka, aby byly v různých doménách selhání v rámci oblasti Azure, která obsahuje HDInsight.
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2018
ms.openlocfilehash: bd3b02d54e0a65411e45f0422a0d245645d59096
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43049967"
---
# <a name="high-availability-of-your-data-with-apache-kafka-on-hdinsight"></a>Vysoká dostupnost dat s využitím Apache Kafka ve službě HDInsight

Zjistěte, jak nakonfigurovat repliky oddílů pro témata Kafka a využít výhod konfigurace použitého hardwarového racku. Tato konfigurace zajišťuje dostupnost dat uložených v Apache Kafka ve službě HDInsight.

## <a name="fault-and-update-domains-with-kafka"></a>Domény selhání a aktualizační domény s využitím Kafka

Doména selhání je logické seskupení základního hardwaru v datovém centru Azure. Všechny domény selhání sdílí společný zdroje napájení a síťový přepínač. Virtuální počítače a spravované disky, které implementují uzly v clusteru služby HDInsight, jsou distribuované napříč těmito doménami selhání. Tato architektura omezuje potenciální dopad selhání fyzického hardwaru.

Každá oblast Azure má určitý počet domén selhání. Seznam domén a počet domén selhání, které obsahují, najdete v dokumentaci [Skupiny dostupnosti](../../virtual-machines/windows/regions-and-availability.md#availability-sets).

> [!IMPORTANT]
> Kafka nemá o doménách selhání žádné informace. Když vytvoříte téma v Kafka, může uložit všechny repliky oddílů ve stejné doméně selhání. K vyřešení tohoto problému služba HDInsight poskytuje [nástroj pro obnovení rovnováhy oddílů Kafka](https://github.com/hdinsight/hdinsight-kafka-tools).

## <a name="when-to-rebalance-partition-replicas"></a>Kdy obnovit rovnováhu replik oddílů

K zajištění nejvyšší dostupnost dat Kafka byste měli obnovit rovnováhu replik oddílů pro vaše téma v těchto situacích:

* Při vytvoření nového tématu nebo oddílu

* Při vertikálním navýšení kapacity clusteru

## <a name="replication-factor"></a>Faktor replikace

> [!IMPORTANT]
> Doporučujeme použít oblast Azure, která obsahuje tři domény selhání, a použít faktor replikace 3.

Pokud musíte použít oblast, která obsahuje jenom dvě domény selhání, použijte faktor replikace 4, abyste zajistili rovnoměrné rozložení replik napříč těmito dvěma doménami selhání.

Příklad vytvoření tématu a nastavení faktoru replikace najdete v dokumentu [Začínáme s Kafka ve službě HDInsight](apache-kafka-get-started.md).

## <a name="how-to-rebalance-partition-replicas"></a>Jak obnovit rovnováhu replik oddílů

K obnovení rovnováhy vybraných témat použijte [nástroj pro obnovení rovnováhy oddílů Kafka](https://github.com/hdinsight/hdinsight-kafka-tools). Tento nástroj se musí spustit z relace SSH na hlavní uzel clusteru Kafka.

Další informace o připojení ke službě HDInsight pomocí SSH najdete v dokumentu [Použití SSH s HDInsightem](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="next-steps"></a>Další postup

* [Škálovatelnost Kafka ve službě HDInsight](apache-kafka-scalability.md)
* [Zrcadlení s využitím Kafka ve službě HDInsight](apache-kafka-mirroring.md)
