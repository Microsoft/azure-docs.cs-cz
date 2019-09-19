---
title: Dotazování protokolů Azure Monitor pro monitorování clusterů Azure HDInsight
description: Naučte se spouštět dotazy na protokoly Azure Monitor pro monitorování úloh spuštěných v clusteru HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/05/2018
ms.openlocfilehash: 51344ff7381b6392870b1fd0e331eed38a33915d
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103508"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Dotazování protokolů Azure Monitor pro monitorování clusterů HDInsight

Seznamte se se základními scénáři použití protokolů Azure Monitor k monitorování clusterů Azure HDInsight:

* [Analýza metrik clusteru HDInsight](#analyze-hdinsight-cluster-metrics)
* [Vyhledat konkrétní zprávy protokolu](#search-for-specific-log-messages)
* [Vytváření upozornění na události](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Požadavky

Musíte mít nakonfigurovaný cluster HDInsight, aby používal protokoly Azure Monitor, a přidal do pracovního prostoru řešení monitorování pro konkrétní clustery HDInsight Azure Monitor. Pokyny najdete v tématu [použití protokolů Azure monitor s clustery HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>Analýza metrik clusteru HDInsight

Naučte se hledat konkrétní metriky pro cluster HDInsight.

1. Z Azure Portal otevřete pracovní prostor Log Analytics, který je přidružený k vašemu clusteru HDInsight.
1. Vyberte dlaždici **prohledávání protokolů** .
1. Do vyhledávacího pole zadejte následující dotaz pro vyhledání všech metrik pro všechny dostupné metriky pro všechny clustery HDInsight nakonfigurované pro použití protokolů Azure Monitor a pak vyberte **Spustit**.

        search *

    ![Ambari Analytics – vyhledávání všech metrik](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Prohledat všechny metriky")

    Výstup by měl vypadat takto:

    ![hledání v Log Analytics – všechny metriky](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "Hledat ve výstupu metriky")

1. V levém podokně v části **typ**vyberte metriku, kterou chcete dig hluboko do, a pak vyberte **použít**. Na následujícím snímku obrazovky vidíte `metrics_resourcemanager_queue_root_default_CL` typ vybraný.

    > [!NOTE]  
    > Možná budete muset vybrat tlačítko **[+] Další** a vyhledat metriku, kterou hledáte. Tlačítko **použít** je také v dolní části seznamu, takže je nutné se k jeho zobrazení posunout dolů.

    Všimněte si, že dotaz v textovém poli se změní na jeden, který je zobrazený ve zvýrazněném poli na následujícím snímku obrazovky:

    ![metriky specifické pro hledání v Log Analytics](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Vyhledat konkrétní metriky")

1. Aby se dig hlouběji do této konkrétní metriky. Můžete například Upřesnit stávající výstup na základě průměru prostředků využívaných v intervalu 10 minut, a to podle názvu clusteru pomocí následujícího dotazu:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

1. Místo rafinace na základě průměru použitých prostředků můžete použít následující dotaz k upřesnění výsledků na základě toho, kdy se maximální využití prostředků (a 90 a 95. percentilu) využívalo v období 10 minut:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>Vyhledat konkrétní zprávy protokolu

Naučte se, jak zobrazit chybové zprávy během konkrétního časového období. Tady je jen jeden příklad, jak se můžete dorazit na chybovou zprávu, kterou vás zajímá. Můžete použít libovolnou vlastnost, která je k dispozici pro vyhledání chyb, které se pokoušíte najít.

1. Z Azure Portal otevřete pracovní prostor Log Analytics, který je přidružený k vašemu clusteru HDInsight.
2. Vyberte dlaždici **prohledávání protokolů** .
3. Zadejte následující dotaz, který vyhledá všechny chybové zprávy pro všechny clustery HDInsight nakonfigurované pro použití protokolů Azure Monitor a pak vyberte **Spustit**.

         search "Error"

    Zobrazí se výstup podobný následujícímu výstupu:

    ![Chyby prohledávání protokolu Azure Portal](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "Vyhledat výstup všech chyb")

4. V levém podokně v části kategorie **typu** vyberte typ chyby, který chcete dig do hloubky, a pak vyberte **použít**.  Všimněte si, že výsledky jsou upřesněny tak, aby zobrazovaly jenom chybu typu, který jste vybrali.

5. Můžete Dig hlouběji do tohoto konkrétního seznamu chyb pomocí možností, které jsou k dispozici v levém podokně. Příklad:

    - Zobrazení chybových zpráv z konkrétního pracovního uzlu:

        ![Vyhledat konkrétní chyby output1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "Vyhledat konkrétní chyby output1")

    - K zobrazení chyby došlo v určitou dobu:

        ![Vyhledat konkrétní chyby output2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "Vyhledat konkrétní chyby output2")

6. A zobrazí se konkrétní chyba. Můžete vybrat **[+] zobrazit další** a podívat se na skutečnou chybovou zprávu.

    ![Vyhledat konkrétní chyby output3](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "Vyhledat konkrétní chyby output3")

## <a name="create-alerts-for-tracking-events"></a>Vytváření výstrah pro sledování událostí

Prvním krokem k vytvoření výstrahy je doručení dotazu, na základě kterého se výstraha aktivuje. Můžete použít libovolný dotaz, ve kterém chcete vytvořit výstrahu.

1. Z Azure Portal otevřete pracovní prostor Log Analytics, který je přidružený k vašemu clusteru HDInsight.
2. Vyberte dlaždici **prohledávání protokolů** .
3. Spusťte následující dotaz, na kterém chcete vytvořit výstrahu, a pak vyberte **Spustit**.

        metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

    Dotaz poskytuje seznam neúspěšných aplikací spuštěných v clusterech HDInsight.

4. V horní části stránky vyberte **nové pravidlo výstrahy** .

    ![Zadejte dotaz pro vytvoření alert1] . (./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Zadejte dotaz pro vytvoření alert1") .

5. V okně **vytvořit pravidlo** zadejte dotaz a další podrobnosti pro vytvoření výstrahy a pak vyberte **vytvořit pravidlo upozornění**.

    ![Zadejte dotaz pro vytvoření alert2] . (./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Zadejte dotaz pro vytvoření alert2") .

Postup úpravy nebo odstranění existující výstrahy:

1. Z Azure Portal otevřete Log Analytics pracovní prostor.
2. V nabídce vlevo vyberte možnost **Výstraha**.
3. Vyberte výstrahu, kterou chcete upravit nebo odstranit.
4. Máte následující možnosti: **Uložení**, **zahození**, **zakázání**a **odstranění**.

    ![Protokoly Azure Monitor HDInsight upozornění odstranit úpravu](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

Další informace najdete v tématu [Vytvoření, zobrazení a správa výstrah metrik pomocí Azure monitor](../azure-monitor/platform/alerts-metric.md).

## <a name="see-also"></a>Viz také:

* [Vytváření vlastních zobrazení pomocí návrháře zobrazení v Azure Monitor](../azure-monitor/platform/view-designer.md)
* [Vytváření, zobrazování a správa výstrah metrik pomocí Azure Monitor](../azure-monitor/platform/alerts-metric.md)
