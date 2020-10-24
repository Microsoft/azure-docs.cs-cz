---
title: Použití protokolů Azure Monitor k monitorování clusterů Azure HDInsight
description: Naučte se používat protokoly Azure Monitor k monitorování úloh spuštěných v clusteru HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-azurecli, devx-track-azurepowershell
ms.date: 05/13/2020
ms.openlocfilehash: 5524c7625678d3bacc5fdbe3c295d8392da2280f
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490998"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Monitorování clusterů HDInsight s využitím protokolů služby Azure Monitor

Naučte se, jak povolit protokoly Azure Monitor pro monitorování operací clusteru Hadoop v HDInsight. A postup přidání řešení pro monitorování HDInsight.

[Protokoly Azure monitor](../log-analytics/log-analytics-overview.md) jsou služba Azure monitor, která monitoruje cloudové a místní prostředí. Monitorování je udržování jejich dostupnosti a výkonu. Shromažďuje data vygenerovaná prostředky ve vašem cloudu, v místních prostředích a z jiných monitorovacích nástrojů. Data se používají k zajištění analýzy napříč různými zdroji.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

* Pracovní prostor služby Log Analytics. Tento pracovní prostor si můžete představit jako jedinečné Azure Monitor zaprotokolované prostředí s vlastním úložištěm dat, zdroji dat a řešeními. Pokyny najdete v tématu [Vytvoření pracovního prostoru Log Analytics](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* Cluster Azure HDInsight. V současné době můžete použít protokoly Azure Monitor s následujícími typy clusterů HDInsight:

  * Hadoop
  * HBase
  * Interaktivní dotaz
  * Kafka
  * Spark
  * Storm

  Pokyny k vytvoření clusteru HDInsight najdete v tématu Začínáme [se službou Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* Pokud používáte PowerShell, budete potřebovat [AZ Module](https://docs.microsoft.com/powershell/azure/). Ujistěte se, že máte nejnovější verzi. V případě potřeby spusťte `Update-Module -Name Az` .

* Pokud chcete použít rozhraní příkazového řádku Azure a ještě jste ho nenainstalovali, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli).

> [!NOTE]  
> Pro lepší výkon doporučujeme umístit cluster HDInsight i Log Analytics pracovní prostor do stejné oblasti. Protokoly Azure Monitor nejsou k dispozici ve všech oblastech Azure.

## <a name="enable-azure-monitor-using-the-portal"></a>Povolení Azure Monitor pomocí portálu

V této části nakonfigurujete existující cluster HDInsight Hadoop tak, aby používal pracovní prostor Azure Log Analytics k monitorování úloh, protokolů ladění atd.

1. Z [Azure Portal](https://portal.azure.com/)vyberte svůj cluster. Cluster se otevře na nové stránce portálu.

1. Vlevo v části **monitorování**vyberte **Azure monitor**.

1. V hlavním zobrazení v části **Azure monitor integrace**vyberte **Povolit**.

1. V rozevíracím seznamu **Vyberte pracovní prostor** vyberte existující pracovní prostor Log Analytics.

1. Vyberte **Uložit**.  Uložení nastavení může chvíli trvat.

    ![Povolit monitorování clusterů HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png "Povolit monitorování clusterů HDInsight")

## <a name="enable-azure-monitor-using-azure-powershell"></a>Povolit Azure Monitor pomocí Azure PowerShell

Protokoly Azure Monitor můžete povolit pomocí Azure PowerShell rutinu AZ Module [Enable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightmonitoring) .

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

Pokud ho chcete zakázat, použijte rutinu [Disable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) :

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="enable-azure-monitor-using-azure-cli"></a>Povolení Azure Monitor pomocí rozhraní příkazového řádku Azure

Protokoly Azure Monitor můžete povolit pomocí příkazu Azure CLI `[az hdinsight monitor enable` ] (/CLI/Azure/HDInsight/monitor # AZ-HDInsight-monitor-Enable).

```azurecli
# set variables
export resourceGroup=RESOURCEGROUPNAME
export cluster=CLUSTERNAME
export LAW=LOGANALYTICSWORKSPACENAME

# Enable the Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor enable --name $cluster --resource-group $resourceGroup --workspace $LAW

# Get the status of Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor show --name $cluster --resource-group $resourceGroup
```

K zakázání použijte [`az hdinsight monitor disable`](/cli/azure/hdinsight/monitor#az-hdinsight-monitor-disable) příkaz.

```azurecli
az hdinsight monitor disable --name $cluster --resource-group $resourceGroup
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Instalace řešení pro správu clusteru HDInsight

HDInsight poskytuje řešení pro správu pro konkrétní clustery, která můžete přidat pro protokoly Azure Monitor. [Řešení pro správu](../log-analytics/log-analytics-add-solutions.md) přidávají funkce do protokolů Azure monitor a poskytují další data a analytické nástroje. Tato řešení shromažďují důležité metriky výkonu z clusterů HDInsight. A poskytněte nástroje pro hledání metrik. Tato řešení také poskytují vizualizace a řídicí panely pro většinu typů clusterů podporovaných v HDInsight. Pomocí metrik, které s řešením shromáždíte, můžete vytvořit vlastní pravidla monitorování a výstrahy.

Dostupná řešení HDInsight:

* Monitorování HDInsight Hadoop
* HDInsight HBase Monitoring
* Monitorování interaktivních dotazů HDInsight
* Monitorování HDInsight Kafka
* Monitorování HDInsight Spark
* Sledování v HDInsight

Pokyny k řešení pro správu najdete v tématu [řešení pro správu v Azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Pokud chcete experimentovat, nainstalujte řešení pro monitorování HDInsight Hadoop. Až to bude hotové, zobrazí se pod položkou **summary**dlaždice **HDInsightHadoop** . Vyberte dlaždici **HDInsightHadoop** . Řešení HDInsightHadoop vypadá takto:

![Zobrazení řešení pro monitorování HDInsight](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Vzhledem k tomu, že cluster je úplně nový cluster, sestava nezobrazuje žádné aktivity.

## <a name="configuring-performance-counters"></a>Konfigurace čítačů výkonu

Azure monitor podporuje shromažďování a analýzu metrik výkonu pro uzly ve vašem clusteru. Další informace najdete v tématu [zdroje dat o výkonu pro Linux v Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#linux-performance-counters).

## <a name="cluster-auditing"></a>Auditování clusteru

Služba HDInsight podporuje auditování clusteru s protokoly Azure Monitor importem následujících typů protokolů:

* `log_gateway_audit_CL` – Tato tabulka poskytuje protokoly auditu z uzlů brány clusteru, které zobrazují úspěšné a neúspěšné pokusy o přihlášení.
* `log_auth_CL` – Tato tabulka poskytuje protokoly SSH s úspěšnými a neúspěšnými pokusy o přihlášení.
* `log_ambari_audit_CL` – Tato tabulka poskytuje protokoly auditu z Ambari.
* `log_ranger_audti_CL` – Tato tabulka poskytuje protokoly auditu z Apache Ranger v clusterech ESP.

## <a name="next-steps"></a>Další kroky

* [Dotazování protokolů Azure Monitor pro monitorování clusterů HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
* [Jak monitorovat dostupnost clusteru pomocí protokolů Apache Ambari a Azure Monitor](./hdinsight-cluster-availability.md)
