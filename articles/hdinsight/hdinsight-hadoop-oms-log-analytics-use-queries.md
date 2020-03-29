---
title: Dotaz na protokoly Azure Monitor pro monitorování clusterů Azure HDInsight
description: Zjistěte, jak spouštět dotazy v protokolech Azure Monitoru a monitorovat úlohy spuštěné v clusteru HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/02/2019
ms.openlocfilehash: 65e85548420116bdfcab87fe9f81a20e66226beb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74803781"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Dotaz na protokoly Azure Monitor pro monitorování clusterů HDInsight

Naučte se některé základní scénáře, jak používat protokoly Azure Monitor ke sledování clusterů Azure HDInsight:

* [Analýza metrik clusteru HDInsight](#analyze-hdinsight-cluster-metrics)
* [Vytvoření upozornění na události](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Požadavky

Musíte nakonfigurovat cluster HDInsight pro použití protokolů Azure Monitor a do pracovního prostoru přidat řešení monitorování protokolů Azure Monitor specifických pro cluster HDInsight. Pokyny najdete [v tématu Použití protokolů Azure Monitor u clusterů HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>Analýza metrik clusteru HDInsight

Přečtěte si, jak hledat konkrétní metriky pro váš cluster HDInsight.

1. Otevřete pracovní prostor Log Analytics, který je přidružený k vašemu clusteru HDInsight, z webu Azure Portal.
1. V části **Obecné**vyberte **protokoly**.
1. Do vyhledávacího pole zadejte následující dotaz, chcete-li vyhledat všechny metriky pro všechny dostupné metriky pro všechny clustery HDInsight nakonfigurované pro použití protokolů Azure Monitor, a pak vyberte **Spustit**. Zkontrolujte výsledky.

    ```kusto
    search *
    ```

    ![Apache Ambari analytics prohledávají všechny metriky](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Hledat všechny metriky")

1. V levé nabídce vyberte kartu **Filtr.**

1. V části **Typ**vyberte **Prezenční signál**. Pak vyberte **Použít & spustit**.

    ![metriky specifické pro vyhledávání v analýzách protokolů](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Hledání konkrétních metrik")

1. Všimněte si, že dotaz v textovém poli se změní na:

    ```kusto
    search *
    | where Type == "Heartbeat"
    ```

1. Můžete kopat hlouběji pomocí možností dostupných v levém menu. Například:

    - Zobrazení protokolů z určitého uzlu:

        ![Hledání výstupů konkrétních chyb1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-node.png "Hledání výstupů konkrétních chyb1")

    - Chcete-li zobrazit protokoly v určitých časech:

        ![Hledání výstupů konkrétních chyb2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-time.png "Hledání výstupů konkrétních chyb2")

1. Vyberte **Použít & Spustit** a zkontrolujte výsledky. Všimněte si také, že dotaz byl aktualizován na:

    ```kusto
    search *
    | where Type == "Heartbeat"
    | where (Computer == "zk2-myhado") and (TimeGenerated == "2019-12-02T23:15:02.69Z" or TimeGenerated == "2019-12-02T23:15:08.07Z" or TimeGenerated == "2019-12-02T21:09:34.787Z")
    ```

### <a name="additional-sample-queries"></a>Další ukázkové dotazy

Ukázkový dotaz založený na průměru prostředků použitých v intervalu 10 minut, roztříděný podle názvu clusteru:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)
```

Namísto zpřesnění na základě průměru použitých prostředků můžete použít následující dotaz k upřesnění výsledků na základě toho, kdy byly použity maximální prostředky (stejně jako 90. a 95. percentil) v 10minutovém okně:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)
```

## <a name="create-alerts-for-tracking-events"></a>Vytváření upozornění pro sledování událostí

Prvním krokem k vytvoření výstrahy je doručení dotazu, na jehož základě je výstraha spuštěna. Můžete použít libovolný dotaz, který chcete vytvořit výstrahu.

1. Otevřete pracovní prostor Log Analytics, který je přidružený k vašemu clusteru HDInsight, z webu Azure Portal.
1. V části **Obecné**vyberte **protokoly**.
1. Spusťte následující dotaz, u kterého chcete vytvořit výstrahu, a vyberte **spustit**.

    ```kusto
    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0
    ```

    Dotaz obsahuje seznam neúspěšných aplikací spuštěných v clusterech HDInsight.

1. V horní části stránky vyberte **Nové pravidlo výstrahy.**

    ![Zadejte dotaz pro vytvoření výstrahy1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Zadejte dotaz pro vytvoření výstrahy1")

1. V okně **Vytvořit pravidlo** zadejte dotaz a další podrobnosti pro vytvoření výstrahy a pak vyberte Vytvořit **pravidlo výstrahy**.

    ![Zadejte dotaz pro vytvoření výstrahy2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Zadejte dotaz pro vytvoření výstrahy2")

### <a name="edit-or-delete-an-existing-alert"></a>Úprava nebo odstranění existující výstrahy

1. Otevřete pracovní prostor Log Analytics z webu Azure Portal.

1. V levé nabídce včásti **Sledování**vyberte **Výstrahy**.

1. Směrem nahoru vyberte **Spravovat pravidla výstrah**.

1. Vyberte výstrahu, kterou chcete upravit nebo odstranit.

1. Máte následující možnosti: **Uložit**, **Zahodit**, **Zakázat**a **Odstranit**.

    ![Výstraha protokolů monitorování HDInsight Azure odstranit úpravy](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

Další informace najdete [v tématu Vytváření, zobrazení a správa upozornění na metriky pomocí Azure Monitoru](../azure-monitor/platform/alerts-metric.md).

## <a name="see-also"></a>Viz také

* [Začínáme s dotazy protokolů v Azure Monitoru](../azure-monitor/log-query/get-started-queries.md)
* [Vytváření vlastních zobrazení pomocí Návrháře zobrazení v Azure Monitoru](../azure-monitor/platform/view-designer.md)
