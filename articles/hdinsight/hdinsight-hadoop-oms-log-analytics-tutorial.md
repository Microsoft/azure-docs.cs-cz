---
title: Použití Azure monitoru zaznamená do monitorování clusterů Azure HDInsight
description: Zjistěte, jak pomocí protokolů Azure Monitor monitorovat úlohy spuštěné v clusteru služby HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: hrasheed
ms.openlocfilehash: 610843d325744aec8ad944075f06c63c90b6fe4d
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65203682"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Použití Azure monitoru zaznamená do monitorování clusterů HDInsight

Zjistěte, jak povolit protokoly Azure Monitor k monitorování operací clusteru Hadoop v HDInsight a přidání HDInsight, řešení pro monitorování.

[Protokoly Azure monitoru](../log-analytics/log-analytics-overview.md) je služba ve službě Azure Monitor, která monitoruje cloudové a místní prostředí s cílem zachovat jejich dostupnost a výkon. Shromažďuje data generovaná prostředky ve vašem cloudovém a místním prostředí a také data z dalších nástrojů pro monitorování a poskytuje analýzy napříč zdroji.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* **Pracovní prostor Log Analytics**. Tento pracovní prostor si můžete představit jako jedinečné prostředí protokoly Azure monitoru s vlastním úložištěm dat, zdroje dat a řešení. Pokyny najdete v tématu [vytvořit pracovní prostor Log Analytics](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* **Cluster Azure HDInsight**. V současné době můžete protokoly Azure monitoru s následujícími typy clusteru HDInsight:

  * Hadoop
  * HBase
  * Interaktivní dotaz
  * Kafka
  * Spark
  * Storm

  Pokyny o tom, jak vytvořit HDInsight cluster najdete v tématu [Začínáme s Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* **Modul Azure PowerShell Az**.  Zobrazit [představení nového modulu Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

> [!NOTE]  
> Doporučujeme umístit HDInsight cluster a pracovní prostor Log Analytics ve stejné oblasti pro zajištění lepšího výkonu. Protokoly služby Azure Monitor není k dispozici ve všech oblastech Azure.

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>Povolit protokoly Azure monitoru s využitím portálu

V této části nakonfigurujete existujícího clusteru HDInsight Hadoop pomocí pracovnímu prostoru Azure Log Analytics můžete sledovat úlohy, protokoly ladění atd.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. V nabídce vlevo vyberte **všechny služby**.

3. V části **ANALYTICS**vyberte **clustery HDInsight**.

4. Ze seznamu vyberte svůj cluster.

5. Z levé straně v části **monitorování**vyberte **Operations Management Suite**.

6. Hlavní zobrazení v části **monitorování OMS**vyberte **povolit**.

7. Z **vyberte pracovní prostor** rozevíracího seznamu vyberte existující pracovní prostor Log Analytics.

8. Vyberte **Uložit**.  Trvá několik okamžiků se uložit nastavení.

    ![Zapněte sledování pro clustery HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "zapněte sledování pro clustery HDInsight")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Povolit protokoly Azure monitoru s využitím Azure Powershellu

Můžete povolit protokoly Azure monitoru pomocí modulu Azure PowerShell Az [povolit AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightoperationsmanagementsuite) rutiny.

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

Chcete zakázat, použití [zakázat AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightoperationsmanagementsuite) rutiny:

```powershell
Disable-AzHDInsightOperationsManagementSuite -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Nainstalujte řešení pro správu clusteru HDInsight

HDInsight nabízí řešení pro správu specifických pro cluster, které můžete přidat pro protokoly Azure monitoru. [Řešení pro správu](../log-analytics/log-analytics-add-solutions.md) přidávají funkce do protokolů Azure Monitor, poskytují další data a analytické nástroje. Tato řešení shromažďování metrik výkonu. důležité z vašich clusterů HDInsight a poskytují nástroje pro hledání metriky. Tato řešení také poskytuje vizualizace a řídicí panely pro většinu typů clusteru v HDInsight podporován. Pomocí řešení, která shromáždíte, můžete vytvořit vlastní pravidla monitorování a výstrah.

Toto jsou k dispozici řešení HDInsight:

* Sledování pro HDInsight Hadoop
* Monitorování pro HDInsight HBase
* HDInsight Interactive Query monitorování
* Sledování pro HDInsight Kafka
* Sledování pro HDInsight Spark
* Sledování pro HDInsight Storm

Pokyny k instalaci řešení pro správu najdete v tématu [řešení pro správu v Azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Můžete experimentovat, nainstalujte řešení HDInsight Hadoop monitorování. Po dokončení, uvidíte **HDInsightHadoop** dlaždici v části **Souhrn**. Vyberte **HDInsightHadoop** dlaždici. Řešení HDInsightHadoop vypadá takto:

![Zobrazení monitorování řešení HDInsight](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Protože clusteru je to úplně nový cluster, nebude se v sestavě zobrazovat žádné aktivity.

## <a name="next-steps"></a>Další postup

* [Zaznamená dotaz Azure Monitor k monitorování clusterů HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
