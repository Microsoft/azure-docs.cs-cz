---
title: Přidat řešení pro správu clusteru HDInsight k analýze protokolů Azure | Microsoft Docs
description: Naučte se používat Azure Log Analytics vytvářet vlastní zobrazení pro clustery služby HDInsight.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: ab959d2cdaf840a1a88a71a0b8f29d84a8979330
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="add-hdinsight-cluster-management-solutions-to-log-analytics"></a>Přidat řešení pro správu clusteru HDInsight k analýze protokolů

HDInsight nabízí řešení pro správu specifických pro cluster, které můžete přidat pro analýzy protokolů Azure. [Řešení pro správu](../log-analytics/log-analytics-add-solutions.md) přidat funkce k analýze protokolů poskytnutí dalších dat a nástrojů pro analýzu. Tato řešení shromažďování metrik výkonu. důležité z clusterům HDInsight a poskytují nástroje pro hledání metriky. Tato řešení také poskytují vizualizace a řídicí panely pro většinu clusteru typy podporované v HDInsight. Pomocí metriky, které shromažďujete s řešením, můžete vytvořit vlastní pravidla monitorování a výstrahy. 

V tomto článku zjistěte, jak přidat do pracovního prostoru analýzy protokolů řešení pro správu specifických pro cluster.

## <a name="prerequisites"></a>Požadavky

* Musíte nakonfigurovat cluster služby HDInsight používat Azure Log Analytics. Pokyny najdete v tématu [použití Azure Log Analytics s clustery HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="add-cluster-specific-management-solutions"></a>Přidat řešení pro správu specifických pro cluster

V této části přidáte do řešení pro správu clusteru HBase k existujícímu pracovnímu prostoru analýzy protokolů.

1. Klikněte na Otevřít clusteru HDInsight na portálu Azure **monitorování**a potom klikněte na **otevřete řídicí panel OMS**.

    ![Otevřete řídicí panel služby Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-log-analytics-open-oms-dashboard.png "OMS otevřete řídicí panel")

1. Na řídicím panelu klikněte na tlačítko **řešení Galerie** nebo **Návrhář zobrazení** ikona v levém podokně.

    ![Přidat řešení pro správu v analýzy protokolů](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-add-management-solution-oms-portal.png "přidat řešení pro správu v Operations Management Suite")

2. V Galerii řešení klikněte na jednu z následujících dlaždic:

    - HDInsight Hadoop monitorování
    - HDInsight HBase monitorování (Preview)
    - HDInsight Kafka monitorování
    - HDInsight Storm monitorování
    - HDInsight Spark monitorování

3. Na další obrazovce klikněte na tlačítko **přidat**.  Následující snímek obrazovky ukazuje na tlačítko Přidat pro monitorování HBase.

     ![Přidat řešení pro správu HBase](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/add-hbase-management-solution.png "řešení pro správu přidat HBase")

4. Se zobrazí na dlaždici na řídicím panelu řešení pro správu HBase. Pokud cluster, který je přidružený k Operations Management Suite (jako součást předpokladem pro tento článek) je HBase cluster, dlaždici se zobrazuje název clusteru a počet uzlů v clusteru.

    ![Přidat řešení pro správu HBase](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/added-hbase-management-solution.png "HBase řešení pro správu přidán")

## <a name="next-steps"></a>Další postup

* [Dotaz Azure Log Analytics k monitorování clusterů HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)

## <a name="see-also"></a>Další informace najdete v tématech

* [Práce s analýzy protokolů](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Vytvořit pravidla výstrah v analýzy protokolů](../log-analytics/log-analytics-alerts-creating.md)
