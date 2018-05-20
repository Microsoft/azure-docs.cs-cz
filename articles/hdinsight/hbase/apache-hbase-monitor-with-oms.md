---
title: Monitorování HBase s Azure Log Analytics - Azure HDInsight | Microsoft Docs
description: Analýza protokolů Azure slouží k monitorování clusterů HDInsight HBase.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: 12ec60049cdf267834d251c6c927b35e3c363a4e
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="monitor-hbase-with-log-analytics"></a>Monitorování HBase s analýzy protokolů

Analýza protokolů Azure HDInsight HBase monitorování používá ke shromažďování metrik výkonu HDInsight HBase z uzlů clusteru HDInsight. Monitorování poskytuje vizualizace specifické HBase a řídicí panely, nástrojů pro vyhledávání metriky a možnost vytvářet vlastní pravidla monitorování a výstrahy. Metriky pro víc clusterů HDInsight HBase můžete sledovat v rámci více předplatných Azure.

Analýzy protokolů je služba v [Azure](../../operations-management-suite/operations-management-suite-overview.md) , sleduje vaše cloudové a místní prostředí k udržování své dostupnosti a výkonu. Analýzy protokolů shromažďuje data generována prostředky ve vašich cloudových a místních prostředích a z dalších nástrojů monitorování, poskytnout analýzu napříč více zdrojů.

[Protokolu řešení pro správu Analytics](../../log-analytics/log-analytics-add-solutions.md) přidat funkce k analýze protokolů poskytnutí dalších dat a nástrojů pro analýzu. Řešení pro správu protokolu Analytics jsou kolekce logiku, vizualizace a data pořízení pravidel, které poskytují metriky pro konkrétní oblasti. Řešení může také definovat nové typy záznamů, které se mají shromažďovat, a tyto záznamy lze analyzovat pomocí protokolu hledání nebo pomocí nové funkce uživatelského rozhraní.

[Přehledy a analýzy](https://azure.microsoft.com/pricing/details/insight-analytics/) je založený na platformě analýzy protokolů. Můžete rozhodnout použít funkce analýzy protokolů a platit za GB požity ve službě, nebo přepněte pracovního prostoru k vrstvě přehledy a analýzy a platba za uzlu prostřednictvím služby spravovány. Přehledy a analýzy nabízí nadmnožinou možnosti, které nabízí analýzy protokolů. Řešení monitorování HBase je k dispozici s analýzy protokolů nebo přehledy a analýzy.

Při zřizování řešení s HDInsight HBase monitorování, můžete vytvořit pracovní prostor analýzy protokolů. Každý pracovní prostor jako jedinečné prostředí analýzy protokolů s vlastní úložiště dat, zdroje dat a řešení. V rámci vašeho předplatného pro podporu více prostředích, jako je například produkční a testování můžete vytvořit několik pracovních prostorů.

## <a name="provision-hdinsight-hbase-monitoring"></a>Zřízení prostředí HDInsight HBase monitorování

1. Přihlaste se k [portál Azure](https://portal.azure.com) pomocí svého předplatného Azure.
2. V **nový** podokně v části **Marketplace**, vyberte **monitorování + správu**.
3. V **monitorování + správu** podokně, vyberte **zobrazit všechny**.

    ![Monitorování + podokně Správa](./media/apache-hbase-monitor-with-oms/monitoring-management-blade.png)  

4. V seznamu, vyhledejte **řešení pro správu** vzdálené správy. Napravo od **řešení pro správu**, vyberte **Další**.

    ![Monitorování + podokně Správa](./media/apache-hbase-monitor-with-oms/management-solutions.png) 

5. V **řešení pro správu** podokně vyberte monitorování HDInsight HBase řešení pro správu pro přidání do pracovního prostoru.

    ![Podokno řešení pro správu](./media/apache-hbase-monitor-with-oms/hbase-solution.png)  
6. V podokně Správa řešení zkontrolujte informace o řešení pro správu a pak vyberte **vytvořit**. 
7. V *název řešení pro správu* podokně, vyberte existující pracovní prostor chcete přidružit k řešení pro správu, nebo vytvořit nový pracovní prostor analýzy protokolů a pak ho vyberte.
8. Změňte nastavení pracovního prostoru pro předplatné, skupinu prostředků a umístění podle potřeby. 
    ![pracovní prostor řešení](./media/apache-hbase-monitor-with-oms/solution-workspace.png)  
9. Vyberte **Vytvořit**.  
10. Chcete-li používat tento nový řešení pro správu v jeho pracovním prostoru, přejděte na **analýzy protokolů** > ***název pracovního prostoru*** > **řešení**. Položka pro vaše řešení pro správu se zobrazí v seznamu. Vyberte položku a přejděte k řešení.

    ![Řešení pro analýzu protokolu](./media/apache-hbase-monitor-with-oms/log-analytics-solutions.png)  

11. Zobrazí se v podokně pro řešení monitorování HDInsight HBase.

    ![Podokno řešení HDInsight HBase](./media/apache-hbase-monitor-with-oms/hdinsight-hbase-solution.png) 

12. Souhrn dlaždice nezobrazovat žádná data, protože jste zatím nenakonfigurovali cluster HDInsight HBase k odesílání dat k analýze protokolů.

## <a name="connect-hdinsight-hbase-cluster-to-log-analytics"></a>Připojení clusteru HDInsight HBase k analýze protokolů

Chcete-li použít nástroje poskytované subsystémem pro monitorování HDInsight HBase, musíte nakonfigurovat cluster tak, aby se přenáší metriky z jeho serveru oblast, head uzlů a uzly ZooKeeper k analýze protokolů. Tato konfigurace se provádí tak, že spustíte akci skriptu pro váš cluster HDInsight HBase.

### <a name="get-log-analytics-workspace-id-and-workspace-key"></a>Získání ID pracovního prostoru analýzy protokolů a klíč pracovního prostoru

Budete potřebovat vaše ID pracovního prostoru analýzy protokolů a klíč pracovního prostoru povolit uzlů v clusteru k ověření pomocí analýzy protokolů. Pokud chcete získat tyto hodnoty:

1. V podokně vaší HBase monitorování na portálu Azure vyberte Přehled.

    ![Monitorování HBase podokně řešení](./media/apache-hbase-monitor-with-oms/hdinsight-hbase-solution.png) 

2. Vyberte portálu OMS ke spuštění portálu OMS v novou kartu prohlížeče nebo okno.

    ![Vyberte portálu OMS](./media/apache-hbase-monitor-with-oms/select-oms-portal.png) 

3. Na domovské stránce portálu OMS vyberte nastavení.

    ![Portál OMS](./media/apache-hbase-monitor-with-oms/oms-portal-settings.png) 

4. Vyberte připojené zdroje, servery se systémem Linux.

    ![Vyberte připojené zdroje a servery se systémem Linux](./media/apache-hbase-monitor-with-oms/select-linux-servers.png) 

5. Poznámka: hodnoty ID pracovního prostoru a primární klíč zobrazí, jako jsou hodnoty, které jsou potřeba pro akce skriptu.

### <a name="run-the-script-action"></a>Spuštěním akce skriptu

Chcete-li povolit shromažďování dat z clusteru HDInsight HBase, spusťte akci skriptu pro všechny uzly v clusteru:

1. Přejděte do podokna pro váš cluster HDInsight HBase na portálu Azure.
2. Vyberte **skript akce**.

    ![Akce skriptu](./media/apache-hbase-monitor-with-oms/script-actions.png) 

3. Vyberte **odeslání nové**.

    ![Nové odeslání](./media/apache-hbase-monitor-with-oms/script-actions-submit-new.png)  

4. V **odeslat skript** akce, nastavená na typ skriptu `"- Custom"`.
5. Zadejte název pro tento skript.
6. Pro **Bash skriptu URI**, vložte následující identifikátor URI:

        https://raw.githubusercontent.com/hdinsight/HDInsightOMS/master/monitoring/script2.sh 

7. Pro **typy uzlů**, vyberte všechny tři (**Head**, **oblast**, **ZooKeeper**).
8. V **parametry** textového pole zadejte své ID pracovního prostoru a klíč pracovního prostoru, uzavření do každé hodnoty v uvozovkách a oddělené mezerou.

        "<Workspace ID>" "<Workspace Key>"

9. Vyberte **zachovat tuto akci skriptu** znovu spustit akci při přidání nových uzlů do clusteru.

    ![Nastavení akce skriptu](./media/apache-hbase-monitor-with-oms/submit-script-action.png)  

10. Vyberte **Vytvořit**.
11. Akce skriptu trvá několik minut. Můžete sledovat jeho stav v podokně Akce skriptu.

    ![Spuštění akce skriptu](./media/apache-hbase-monitor-with-oms/script-action-running.png)  

12. Po dokončení akce skriptu, měli byste vidět zeleného zaškrtnutí vedle názvu skriptu ve výpisu.

    ![Dokončení akce skriptu](./media/apache-hbase-monitor-with-oms/script-action-done.png)  

## <a name="view-the-hdinsight-hbase-monitoring-solution"></a>Zobrazení řešení monitorování HDInsight HBase

Po dokončení akce skriptu, měli byste vidět dat v řešení pro monitorování během několika minut.

1. V rámci portálu Azure přejděte do podokna řešení HDInsight HBase.
2. Vyberte **přehled** kartě.
3. V části **Souhrn**, zobrazí dlaždice určující počet oblast servery, které jsou monitorovány.

    ![Dlaždice monitorování souhrnu](./media/apache-hbase-monitor-with-oms/monitoring-summary-tile.png)  

4. Vyberte dlaždici s počtem serveru oblast. Zobrazí se hlavní řídicí panel.
5. Tento řídicí panel poskytuje přístup k Statistika oblastech, počet předběžné protokolování (WAL) používá, kolekce hledání analýzy protokolů (například jako oblast serveru protokoly, protokoly Phoenix a výjimkami) a kolekce oblíbených grafy k vizualizaci relevantní metriky. 

    ![Hlavní řídicí panel](./media/apache-hbase-monitor-with-oms/main-dashboard.png)  

6. Výběrem některého z nich bude přejděte do zobrazení hledání protokolů, kde můžete upřesnění dotazu a data podrobněji prozkoumat.

## <a name="next-steps"></a>Další postup

* [Vytváření výstrah v analýzy protokolů](../../log-analytics/log-analytics-alerts-creating.md)
* [Najít data pomocí protokolu hledání v Azure Log Analytics](../../log-analytics/log-analytics-log-searches.md).
