---
title: Použití protokolů Azure Monitor ke sledování clusterů Azure HDInsight
description: Zjistěte, jak pomocí protokolů Azure Monitoru monitorovat monitoru monitoru spouštěné v clusteru HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: e4b33e132e660fba7d06ff33c7db06c7727dd26c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162782"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Monitorování clusterů HDInsight s využitím protokolů služby Azure Monitor

Zjistěte, jak povolit protokolům Azure Monitoru monitorke monitorke ke sledování operací clusteru Hadoop v HDInsightu a jak přidat řešení monitorování HDInsight.

[Protokoly Azure Monitoru](../log-analytics/log-analytics-overview.md) je služba ve službě Azure Monitor, která monitoruje vaše cloudová a místní prostředí, aby byla zachována jejich dostupnost a výkon. Shromažďuje data generovaná prostředky ve vašem cloudovém a místním prostředí a také data z dalších nástrojů pro monitorování a poskytuje analýzy napříč zdroji.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Log Analytics. Tento pracovní prostor si můžete myslet jako jedinečné prostředí protokolů Azure Monitor s vlastním úložištěm dat, zdroji dat a řešeními. Pokyny naleznete v [tématu Vytvoření pracovního prostoru Analýzy protokolů](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* Cluster Azure HDInsight. V současné době můžete používat protokoly Azure Monitor s následujícími typy clusterů HDInsight:

  * Hadoop
  * HBase
  * Interaktivní dotaz
  * Kafka
  * Spark
  * Storm

  Pokyny k vytvoření clusteru HDInsight najdete v tématu [Začínáme s Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* Modul Azure PowerShell Az.  Viz [Představení nového modulu Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az). Ujistěte se, že máte nejnovější verzi. V případě `Update-Module -Name Az`potřeby spusťte .

> [!NOTE]  
> Doporučujeme umístit cluster HDInsight i pracovní prostor Log Analytics do stejné oblasti pro lepší výkon. Protokoly Azure Monitor není k dispozici ve všech oblastech Azure.

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>Povolení protokolů Azure Monitor pomocí portálu

V této části nakonfigurujete existující cluster HDInsight Hadoop tak, aby používal pracovní prostor Azure Log Analytics ke sledování úloh, ladění protokolů atd.

1. Na [portálu Azure](https://portal.azure.com/)vyberte svůj cluster.  Pokyny naleznete [v tématu Seznam a zobrazení clusterů.](./hdinsight-administer-use-portal-linux.md#showClusters) Cluster se otevře na nové stránce portálu.

1. Zleva v části **Monitorování**vyberte **Azure Monitor**.

1. V hlavním zobrazení vyberte v části **Integrace monitorování Azure**možnost **Povolit**.

1. V rozevíracím seznamu **Vyberte pracovní prostor** vyberte existující pracovní prostor Analýzy protokolů.

1. Vyberte **Uložit**.  Uložení nastavení trvá několik okamžiků.

    ![Povolení monitorování clusterů HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png "Povolení monitorování clusterů HDInsight")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Povolení protokolů Azure Monitor pomocí Azure PowerShellu

Protokoly Azure Monitoru můžete povolit pomocí rutiny Modul Az Azure PowerShell [povolit AzHDInsightMonitoring.](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightmonitoring)

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables monitoring and relevant logs will be sent to the specified workspace.
Enable-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster `
    -WorkspaceId $WorkspaceId `
    -PrimaryKey $PrimaryKey

# Gets the status of monitoring installation on the cluster.
Get-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster
```

Chcete-li zakázat, použijte [rutinu Disable-AzHDInsightMonitoring:](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring)

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Instalace řešení pro správu clusteru HDInsight

HDInsight poskytuje řešení pro správu specifická pro cluster, která můžete přidat pro protokoly Azure Monitor. [Řešení pro správu](../log-analytics/log-analytics-add-solutions.md) přidávají do protokolů Azure Monitoru funkce a poskytují další data a analytické nástroje. Tato řešení shromažďují důležité metriky výkonu z clusterů HDInsight a poskytují nástroje pro vyhledávání metrik. Tato řešení také poskytují vizualizace a řídicí panely pro většinu typů clusterů podporovaných v HDInsight. Pomocí metriky, které shromažďujete s řešením, můžete vytvořit vlastní pravidla monitorování a výstrahy.

Toto jsou dostupná řešení HDInsight:

* Monitorování hdinsight hadoopu
* Monitorování hbase HDInsight
* Interaktivní monitorování dotazů HDInsight
* HDInsight Kafka Monitoring
* Monitorování hdinsight spark
* Monitorování bouře HDInsight

Pokyny k instalaci řešení pro správu najdete [v tématu Řešení pro správu v Azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Chcete-li experimentovat, nainstalujte řešení monitorování hadoopu HDInsight. Po dokončení se v části **Souhrn**zobrazí dlaždice **HDInsightHadoop** . Vyberte dlaždici **HDInsightHadoop.** Řešení HDInsightHadoop vypadá takto:

![Zobrazení řešení monitorování HDInsight](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Vzhledem k tomu, že cluster je zcela nový cluster, sestava nezobrazuje žádné aktivity.

## <a name="configuring-performance-counters"></a>Konfigurace čítačů výkonu

Azure Monitor také podporuje shromažďování a analýzu metrik výkonu pro uzly ve vašem clusteru. Další informace o povolení a konfiguraci této funkce najdete [v tématu Zdroje dat o výkonu Linuxu v Azure Monitoru](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#linux-performance-counters).

## <a name="cluster-auditing"></a>Auditování clusteru

HDInsight podporuje auditování clusteru pomocí protokolů Azure Monitor importem následujících typů protokolů:

* `log_gateway_audit_CL`- Tato tabulka obsahuje protokoly auditu z uzlů brány clusteru, které zobrazují úspěšné a neúspěšné pokusy o přihlášení.
* `log_auth_CL`- tato tabulka poskytuje protokoly SSH s úspěšnými a neúspěšnými pokusy o přihlášení.
* `log_ambari_audit_CL`- tato tabulka obsahuje protokoly auditu z Ambari.
* `log_ranger_audti_CL`- tato tabulka obsahuje protokoly auditu od Apache Ranger na CLUSTEREch ESP.

## <a name="next-steps"></a>Další kroky

* [Dotaz na protokoly Azure Monitor pro monitorování clusterů HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
