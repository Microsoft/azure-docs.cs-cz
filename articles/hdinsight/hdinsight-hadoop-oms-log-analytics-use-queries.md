---
title: Dotaz na Azure Log Analytics k monitorování clusterů Azure HDInsight | Microsoft Docs
description: Zjistěte, jak ke spouštění dotazů na Azure Log Analytics ke sledování úloh spuštěných v clusteru služby HDInsight.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 61467d702f3123085fd7e067a8d56c30331c5bc6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31401093"
---
# <a name="query-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Dotaz Azure Log Analytics k monitorování clusterů HDInsight

Další informace o tom, jak pomocí Azure Log Analytics monitorovat Azure HDInsight clustery některé základní scénáře:

* [Analyzujte metriky clustery HDInsight](#analyze-hdinsight-cluster-metrics)
* [Vyhledejte konkrétní protokolu zpráv](#search-for-specific-log-messages)
* [Vytvoření výstrah událostí](#create-alerts-for-tracking-events)

## <a name="prerequisites"></a>Požadavky

* Musíte nakonfigurovat cluster služby HDInsight používat Azure Log Analytics. Pokyny najdete v tématu [použití Azure Log Analytics s clustery HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

* Je třeba přidat řešení správy specifických pro cluster HDInsight [analýzy protokolů](../operations-management-suite/operations-management-suite-overview.md) pracovního prostoru, jak je popsáno v [řešení pro správu clusteru přidat HDInsight k analýze protokolů](hdinsight-hadoop-oms-log-analytics-management-solutions.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>Analyzujte metriky clustery HDInsight

Zjistěte, jak má být vyhledán určité metriky pro váš cluster HDInsight.

1. Otevřete cluster služby HDInsight, který jste přiřadili k analýze protokolů Azure na portálu Azure.
2. Klikněte na tlačítko **monitorování**a potom klikněte na **otevřete řídicí panel OMS**.

    ![Otevřete řídicí panel OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "OMS otevřete řídicí panel")

2. Klikněte na tlačítko **hledání protokolů** v levé nabídce.

    ![Otevřete vyhledávání protokolu](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "otevřete vyhledávání protokolu")

3. Zadejte následující dotaz do vyhledávacího pole pro vyhledávání všechny metriky pro všechny dostupné metriky pro všechny clustery HDInsight nakonfigurován pro používání Azure Log Analytics a potom stiskněte klávesu **ENTER**.

        `search *` 

    ![Vyhledat všechny metriky](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "vyhledat všechny metriky")

    Výstup se vypadat podobně jako:

    ![Vyhledat všechny metriky výstup](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "hledání veškerý výstup metriky")

5. V levém podokně v části **typ**, vyberte metriku, který chcete prozkoumat o a potom klikněte na **použít**. Následující snímek obrazovky ukazuje `metrics_resourcemanager_queue_root_default_CL` je vybrán. 

    > [!NOTE]
    > Je třeba kliknout na **[+] Další** tlačítko Najít metriku, kterou hledáte. Navíc **použít** tlačítko je v dolní části seznamu, musí přejděte dolů k jeho zobrazení.
    > 
    >    

    Všimněte si, že dotaz do textového pole se změní na jednu zobrazené v poli zvýrazněná na následujícím snímku obrazovky:

    ![Vyhledejte konkrétní metriky](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "vyhledejte konkrétní metriky")

6. Prozkoumat podrobněji tato konkrétní metrika. Například můžete upřesnit existující výstup podle průměr prostředky používané v 10minutových intervalech, zařazené do kategorie podle názvu clusteru pomocí následujícího dotazu:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

7. Místo upřesnění podle průměr prostředky využívané, můžete použít následující dotaz k zpřesnění výsledků na základě při maximální prostředky používaly (i 90 a 95. percentil) v okně 10 minut:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>Vyhledejte konkrétní protokolu zpráv

Zjistěte, jak hledat chybové zprávy během konkrétního časového okna. Kroky zde jsou pouze jeden z příkladů na tom, jak můžete dorazit chybovou zprávu můžete zajímá. Můžete použít libovolné vlastnosti, které jsou k dispozici. Vyhledejte chyby, které chcete najít.

1. Otevřete cluster služby HDInsight, který jste přiřadili k analýze protokolů Azure na portálu Azure.
2. Klikněte na tlačítko **monitorování**a potom na **otevřete řídicí panel OMS**.

    ![Otevřete řídicí panel OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "OMS otevřete řídicí panel")

2. Na portálu OMS z domovské obrazovce, klikněte na tlačítko **hledání protokolů**.

    ![Otevřete vyhledávání protokolu](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "otevřete vyhledávání protokolu")

3. Zadejte následující dotaz pro vyhledání všech chybových zpráv pro všechny clustery HDInsight, které jsou nakonfigurované na používání Azure Log Analytics a potom stiskněte klávesu **ENTER**. 

         search "Error"

    Zobrazí se výstup jako následující výstup:

    ![Vyhledat všechny chyby výstupu](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "vyhledat všechny chyby výstupu")

5. V levém podokně v části **typ** vyberte typu chyby, který chcete prozkoumat o, kategorie a pak klikněte na tlačítko **použít**.  Všimněte si, že výsledky jsou vylepšení jenom zobrazit chyba typu, který jste vybrali.
7. Můžete můžete podrobněji tento seznam konkrétních chyb pomocí možnosti dostupné v levém podokně. Například: 

    - Pokud chcete zobrazit chybové zprávy z konkrétního pracovního uzlu:

        ![Vyhledejte konkrétní chyby výstupu](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "vyhledejte konkrétní chyby výstupu")

    - Pokud chcete zobrazit, že v určitém čase došlo k chybě:

        ![Vyhledejte konkrétní chyby výstupu](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "vyhledejte konkrétní chyby výstupu")

9. Pokud chcete zobrazit konkrétní chybu. Můžete kliknout na **[+] zobrazit další** podívat se na skutečné chybové zprávě.

    ![Vyhledejte konkrétní chyby výstupu](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "vyhledejte konkrétní chyby výstupu")

## <a name="create-alerts-for-tracking-events"></a>Vytvořte výstrahy pro sledování událostí

Prvním krokem k vytvoření výstrahy je přicházejí na dotaz, podle kterého se výstraha spustila. Pro jednoduchost použijeme následující dotaz, který obsahuje seznam selhání aplikací běžících na clustery HDInsight.

    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

Můžete použít jakýkoli dotaz, který chcete vytvořit výstrahu.

1. Otevřete cluster služby HDInsight, který jste přiřadili k analýze protokolů Azure na portálu Azure.
2. Klikněte na tlačítko **monitorování**a potom klikněte na **otevřete řídicí panel OMS**.

    ![Otevřete řídicí panel OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "OMS otevřete řídicí panel")

2. Na portálu OMS z domovské obrazovce, klikněte na tlačítko **hledání protokolů**.

    ![Otevřete vyhledávání protokolu](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "otevřete vyhledávání protokolu")

3. Spusťte následující dotaz, na kterém chcete vytvořit výstrahu a stiskněte klávesu **ENTER**.

        metrics_resourcemanager_queue_root-default-CL | where AppsFailed_d > 0

4. Klikněte na tlačítko **výstrahy** horní části stránky.

    ![Zadejte dotaz na vytvoření výstrahy](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "zadejte dotaz na vytvoření výstrahy")

4. V **přidat pravidlo výstrah** okno, zadejte dotaz a další podrobnosti o vytvoření výstrahy, a pak klikněte na **Uložit**.

    ![Zadejte dotaz na vytvoření výstrahy](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "zadejte dotaz na vytvoření výstrahy")

    Na snímku obrazovky je znázorněna konfigurace pro odesílání e-mailové oznámení, když je výstraha dotazu vrátí výstup.

5. Můžete také upravit nebo odstranit existující výstrahy. Uděláte to tak, že z libovolné stránce na portálu OMS klikněte **nastavení** ikonu.

    ![Zadejte dotaz na vytvoření výstrahy](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png "zadejte dotaz na vytvoření výstrahy")

6. Z **nastavení** klikněte na tlačítko **výstrahy** zobrazte výstrahy, které jste vytvořili. Můžete také povolit nebo zakázat výstrahu, upravit nebo odstranit. Další informace najdete v tématu [práce s pravidla výstrah v analýzy protokolů](../log-analytics/log-analytics-alerts-creating.md).

## <a name="see-also"></a>Další informace najdete v tématech

* [Práce s analýzy protokolů](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Vytvořit pravidla výstrah v analýzy protokolů](../log-analytics/log-analytics-alerts-creating.md)
