---
title: Dotazování protokolů Azure Monitor pro monitorování clusterů Azure HDInsight
description: Naučte se spouštět dotazy na protokoly Azure Monitor pro monitorování úloh spuštěných v clusteru HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/02/2019
ms.openlocfilehash: 3cf97039983ecec44a7c3a32e178fdcf9f9c45ff
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104872179"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Dotazování protokolů Azure Monitor pro monitorování clusterů HDInsight

Seznamte se se základními scénáři použití protokolů Azure Monitor k monitorování clusterů Azure HDInsight:

* [Analýza metrik clusteru HDInsight](#analyze-hdinsight-cluster-metrics)
* [Vytváření upozornění na události](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Předpoklady

Musíte mít nakonfigurovaný cluster HDInsight, aby používal protokoly Azure Monitor, a přidal do pracovního prostoru řešení monitorování pro konkrétní clustery HDInsight Azure Monitor. Pokyny najdete v tématu [použití protokolů Azure monitor s clustery HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>Analýza metrik clusteru HDInsight

Naučte se hledat konkrétní metriky pro cluster HDInsight.

1. Z Azure Portal otevřete pracovní prostor Log Analytics, který je přidružený k vašemu clusteru HDInsight.
1. V části **Obecné** vyberte **Protokoly**.
1. Do vyhledávacího pole zadejte následující dotaz pro vyhledání všech metrik pro všechny dostupné metriky pro všechny clustery HDInsight nakonfigurované pro použití protokolů Azure Monitor a pak vyberte **Spustit**. Prohlédněte si výsledky.

    ```kusto
    search *
    ```

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png" alt-text="Ambari Analytics – vyhledávání všech metrik":::

1. V nabídce vlevo vyberte kartu **Filtr** .

1. V části **typ** vyberte **prezenční signál**. Pak vyberte **použít & spustit**.

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png" alt-text="metriky specifické pro hledání v Log Analytics":::

1. Všimněte si, že dotaz v textovém poli se změní na:

    ```kusto
    search *
    | where Type == "Heartbeat"
    ```

1. Můžete Dig hlouběji pomocí možností dostupných v levé nabídce. Například:

   - Postup zobrazení protokolů z konkrétního uzlu:

     :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-node.png" alt-text="Vyhledat konkrétní chyby output1":::

   - Postup zobrazení protokolů v určitých časech:

     :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-time.png" alt-text="Vyhledat konkrétní chyby output2":::

1. Vyberte **použít & spustit** a zkontrolujte výsledky. Všimněte si také, že dotaz byl aktualizován na:

    ```kusto
    search *
    | where Type == "Heartbeat"
    | where (Computer == "zk2-myhado") and (TimeGenerated == "2019-12-02T23:15:02.69Z" or TimeGenerated == "2019-12-02T23:15:08.07Z" or TimeGenerated == "2019-12-02T21:09:34.787Z")
    ```

### <a name="additional-sample-queries"></a>Další ukázkové dotazy

Vzorový dotaz založený na průměru prostředků použitých v intervalu 10 minut, v členění podle názvu clusteru:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)
```

Místo rafinace na základě průměru použitých prostředků můžete použít následující dotaz k upřesnění výsledků na základě toho, kdy se maximální využití prostředků (a 90 a 95. percentilu) využívalo v období 10 minut:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)
```

## <a name="create-alerts-for-tracking-events"></a>Vytváření výstrah pro sledování událostí

Prvním krokem k vytvoření výstrahy je doručení dotazu, na základě kterého se výstraha aktivuje. Můžete použít libovolný dotaz, ve kterém chcete vytvořit výstrahu.

1. Z Azure Portal otevřete pracovní prostor Log Analytics, který je přidružený k vašemu clusteru HDInsight.
1. V části **Obecné** vyberte **Protokoly**.
1. Spusťte následující dotaz, na kterém chcete vytvořit výstrahu, a pak vyberte **Spustit**.

    ```kusto
    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0
    ```

    Dotaz poskytuje seznam neúspěšných aplikací spuštěných v clusterech HDInsight.

1. V horní části stránky vyberte **nové pravidlo výstrahy** .

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png" alt-text="Nové pravidlo výstrahy":::

1. V okně **vytvořit pravidlo** zadejte dotaz a další podrobnosti pro vytvoření výstrahy a pak vyberte **vytvořit pravidlo upozornění**.

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png" alt-text="Definujte podmínku upozornění.":::

### <a name="edit-or-delete-an-existing-alert"></a>Úprava nebo odstranění existující výstrahy

1. Z Azure Portal otevřete Log Analytics pracovní prostor.

1. V nabídce vlevo v části **monitorování** vyberte **výstrahy**.

1. V horní části vyberte **Spravovat pravidla výstrah**.

1. Vyberte výstrahu, kterou chcete upravit nebo odstranit.

1. Máte následující možnosti: **Uložit**, **Zahodit**, **Zakázat** a **Odstranit**.

    :::image type="content" source="media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png" alt-text="Protokoly Azure Monitor HDInsight upozornění odstranit úpravu":::

Další informace najdete v tématu [Vytvoření, zobrazení a správa výstrah metrik pomocí Azure monitor](../azure-monitor/alerts/alerts-metric.md).

## <a name="see-also"></a>Viz také

* [Začínáme s dotazy protokolů v Azure Monitoru](../azure-monitor/logs/get-started-queries.md)
* [Vytváření vlastních zobrazení pomocí návrháře zobrazení v Azure Monitor](../azure-monitor/visualize/view-designer.md)
