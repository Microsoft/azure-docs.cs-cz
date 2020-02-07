---
title: Použití protokolů Azure Monitor k monitorování clusterů Azure HDInsight
description: Naučte se používat protokoly Azure Monitor k monitorování úloh spuštěných v clusteru HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: 980569edf8322c6c22a4357a5b946ded85f0ebe4
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063729"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Použití protokolů Azure Monitor k monitorování clusterů HDInsight

Naučte se, jak povolit protokoly Azure Monitor pro monitorování operací clusteru Hadoop ve službě HDInsight a přidání řešení pro monitorování HDInsight.

[Protokoly Azure monitor](../log-analytics/log-analytics-overview.md) jsou služba v Azure monitor, která monitoruje cloudové a místní prostředí a udržuje jejich dostupnost a výkon. Shromažďuje data generovaná prostředky ve vašem cloudovém a místním prostředí a také data z dalších nástrojů pro monitorování a poskytuje analýzy napříč zdroji.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor Log Analytics. Tento pracovní prostor si můžete představit jako jedinečné Azure Monitor zaprotokolované prostředí s vlastním úložištěm dat, zdroji dat a řešeními. Pokyny najdete v tématu [Vytvoření pracovního prostoru Log Analytics](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* Cluster Azure HDInsight. V současné době můžete použít protokoly Azure Monitor s následujícími typy clusterů HDInsight:

  * Hadoop
  * HBase
  * Interaktivní dotaz
  * Kafka
  * Spark
  * Storm

  Pokyny k vytvoření clusteru HDInsight najdete v tématu Začínáme [se službou Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* Azure PowerShell AZ Module.  Další informace najdete v tématu [Úvod do nového Azure PowerShell AZ Module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

> [!NOTE]  
> Doporučujeme umístit HDInsight cluster a pracovní prostor Log Analytics ve stejné oblasti pro zajištění lepšího výkonu. Protokoly Azure Monitor nejsou k dispozici ve všech oblastech Azure.

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>Povolení protokolů Azure Monitor pomocí portálu

V této části nakonfigurujete existujícího clusteru HDInsight Hadoop pomocí pracovnímu prostoru Azure Log Analytics můžete sledovat úlohy, protokoly ladění atd.

1. Z [Azure Portal](https://portal.azure.com/)vyberte svůj cluster.  Pokyny najdete v tématu [seznam a zobrazení clusterů](./hdinsight-administer-use-portal-linux.md#showClusters) . Cluster se otevře na nové stránce portálu.

1. Vlevo v části **monitorování**vyberte **Azure monitor**.

1. V hlavním zobrazení v části **Azure monitor integrace**vyberte **Povolit**.

1. V rozevíracím seznamu **Vyberte pracovní prostor** vyberte existující pracovní prostor Log Analytics.

1. Vyberte **Save** (Uložit).  Trvá několik okamžiků se uložit nastavení.

    ![Povolit monitorování clusterů HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png "Povolit monitorování clusterů HDInsight")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Povolit protokoly Azure Monitor pomocí Azure PowerShell

Protokoly Azure Monitor můžete povolit pomocí Azure PowerShell rutinu AZ Module [Enable-AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightoperationsmanagementsuite) .

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace -ResourceGroupName $resourceGroup -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace -ResourceGroupName $resourceGroup -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables Operations Management Suite
Enable-AzHDInsightOperationsManagementSuite -ResourceGroupName $resourceGroup -Name $cluster -WorkspaceId $WorkspaceId -PrimaryKey $PrimaryKey
```

Pokud ho chcete zakázat, použijte rutinu [Disable-AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightoperationsmanagementsuite) :

```powershell
Disable-AzHDInsightOperationsManagementSuite -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Nainstalujte řešení pro správu clusteru HDInsight

HDInsight poskytuje řešení pro správu pro konkrétní clustery, která můžete přidat pro protokoly Azure Monitor. [Řešení pro správu](../log-analytics/log-analytics-add-solutions.md) přidávají funkce do protokolů Azure monitor a poskytují další data a analytické nástroje. Tato řešení shromažďování metrik výkonu. důležité z vašich clusterů HDInsight a poskytují nástroje pro hledání metriky. Tato řešení také poskytuje vizualizace a řídicí panely pro většinu typů clusteru v HDInsight podporován. Pomocí řešení, která shromáždíte, můžete vytvořit vlastní pravidla monitorování a výstrah.

Toto jsou k dispozici řešení HDInsight:

* Sledování pro HDInsight Hadoop
* Monitorování pro HDInsight HBase
* HDInsight Interactive Query monitorování
* Sledování pro HDInsight Kafka
* Sledování pro HDInsight Spark
* Sledování pro HDInsight Storm

Pokyny k instalaci řešení pro správu najdete v tématu [řešení pro správu v Azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Pokud chcete experimentovat, nainstalujte řešení pro monitorování HDInsight Hadoop. Až to bude hotové, zobrazí se pod položkou **summary**dlaždice **HDInsightHadoop** . Vyberte dlaždici **HDInsightHadoop** . Řešení HDInsightHadoop vypadá takto:

![Zobrazení monitorování řešení HDInsight](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Protože clusteru je to úplně nový cluster, nebude se v sestavě zobrazovat žádné aktivity.

## <a name="configuring-performance-counters"></a>Konfigurace čítačů výkonu

Azure monitor také podporuje shromažďování a analýzu metrik výkonu pro uzly ve vašem clusteru. Další informace o povolení a konfiguraci této funkce najdete v tématu [zdroje dat o výkonu pro Linux v Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#linux-performance-counters).

## <a name="cluster-auditing"></a>Auditování clusteru

Služba HDInsight podporuje auditování clusteru s protokoly Azure Monitor importem následujících typů protokolů:

* `log_gateway_audit_CL` – Tato tabulka poskytuje protokoly auditu z uzlů brány clusteru, které zobrazují úspěšné a neúspěšné pokusy o přihlášení.
* `log_auth_CL` – Tato tabulka poskytuje protokoly SSH s úspěšnými a neúspěšnými pokusy o přihlášení.
* `log_ambari_audit_CL` – Tato tabulka poskytuje protokoly auditu z Ambari.
* `log_ranger_audti_CL` – Tato tabulka poskytuje protokoly auditu z Apache Ranger v clusterech ESP.

## <a name="next-steps"></a>Další kroky

* [Dotazování protokolů Azure Monitor pro monitorování clusterů HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
