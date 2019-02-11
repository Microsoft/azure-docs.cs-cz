---
title: Použití Log Analytics k monitorování clusterů Azure HDInsight
description: Zjistěte, jak monitorovat úlohy spuštěné v clusteru služby HDInsight pomocí Azure Log Analytics.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: hrasheed
ms.openlocfilehash: 2f45b8e5a8fbf06a86a16336b825d185baf4976b
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55959663"
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Monitorování clusterů HDInsight pomocí Azure Log Analytics

Zjistěte, jak povolit Azure Log Analytics k monitorování operací clusteru Hadoop v HDInsight a přidání Hdinsight, řešení pro monitorování.

[Log Analytics](../log-analytics/log-analytics-overview.md) je služba ve službě Azure Monitor, která monitoruje cloudové a místní prostředí s cílem zachovat jejich dostupnost a výkon. Shromažďuje data generovaná prostředky ve vašem cloudovém a místním prostředí a také data z dalších nástrojů pro monitorování a poskytuje analýzy napříč zdroji.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* **Pracovní prostor Log Analytics**. Tento pracovní prostor si můžete představit jako jedinečné prostředí Log Analytics s vlastním úložištěm dat, zdroje dat a řešení. Pokyny najdete v tématu [vytvořit pracovní prostor Log Analytics](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* **Cluster Azure HDInsight**. V současné době můžete Log Analytics s těmito typy clusteru HDInsight:

  * Hadoop
  * HBase
  * Interaktivní dotaz
  * Kafka
  * Spark
  * Storm

  Pokyny o tom, jak vytvořit HDInsight cluster najdete v tématu [Začínáme s Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

> [!NOTE]  
> Doporučujeme umístit HDInsight cluster a pracovní prostor Log Analytics ve stejné oblasti pro zajištění lepšího výkonu. Azure Log Analytics není k dispozici ve všech oblastech Azure.

## <a name="enable-log-analytics-by-using-the-portal"></a>Povolení Log Analytics pomocí portálu

V této části nakonfigurujete existujícího clusteru HDInsight Hadoop pomocí pracovnímu prostoru Azure Log Analytics můžete sledovat úlohy, protokoly ladění atd.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. V nabídce vlevo vyberte **všechny služby**.

3. V části **ANALYTICS**vyberte **clustery HDInsight**.

4. Z levé straně v části **monitorování**vyberte **Operations Management Suite**.

5. Hlavní zobrazení v části **monitorování OMS**vyberte **povolit**.

6. Z **vyberte pracovní prostor** rozevíracího seznamu vyberte existující pracovní prostor Log Analytics.

7. Vyberte **Uložit**.

    ![Zapněte sledování pro clustery HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "zapněte sledování pro clustery HDInsight")

    Trvá několik okamžiků se uložit nastavení.

## <a name="enable-log-analytics-by-using-azure-powershell"></a>Povolení Log Analytics pomocí Azure Powershellu

Můžete povolit Log Analytics pomocí Azure Powershellu. Rutina je:

```powershell
Enable-AzureRmHDInsightOperationsManagementSuite
      [-Name] <CLUSTER NAME>
      [-WorkspaceId] <LOG ANALYTICS WORKSPACE NAME>
      [-PrimaryKey] <LOG ANALYTICS WORKSPACE PRIMARY KEY>
      [-ResourceGroupName] <RESOURCE GROUIP NAME>
```

Zobrazit [povolit AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/Enable-AzureRmHDInsightOperationsManagementSuite?view=azurermps-5.0.0).

Pokud chcete zakázat, rutina je:

```powershell
Disable-AzureRmHDInsightOperationsManagementSuite
       [-Name] <CLUSTER NAME>
       [-ResourceGroupName] <RESOURCE GROUP NAME>
```

Zobrazit [zakázat AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/disable-azurermhdinsightoperationsmanagementsuite?view=azurermps-5.0.0).

## <a name="install-hdinsight-cluster-management-solutions"></a>Nainstalujte řešení pro správu clusteru HDInsight

HDInsight nabízí řešení pro správu specifických pro cluster, které můžete přidat ke službě Azure Log Analytics. [Řešení pro správu](../log-analytics/log-analytics-add-solutions.md) doplňují do Log Analytics, poskytují další data a analytické nástroje. Tato řešení shromažďování metrik výkonu. důležité z vašich clusterů HDInsight a poskytují nástroje pro hledání metriky. Tato řešení také poskytuje vizualizace a řídicí panely pro většinu typů clusteru v HDInsight podporován. Pomocí řešení, která shromáždíte, můžete vytvořit vlastní pravidla monitorování a výstrah.

Toto jsou k dispozici řešení HDInsight:

* Sledování pro HDInsight Hadoop
* Monitorování pro HDInsight HBase
* HDInsight Interactive Query monitorování
* Sledování pro HDInsight Kafka
* Sledování pro HDInsight Spark
* Sledování pro HDInsight Storm

Pokyny k instalaci řešení pro správu najdete v tématu [řešení pro správu v Azure](../azure-monitor/insights/solutions.md#install-a-management-solution). Můžete experimentovat, nainstalujte řešení HDInsight Hadoop Monotiring. Po dokončení, uvidíte **HDInsightHadoop** dlaždici v části **Souhrn**. Vyberte **HDInsightHadoop** dlaždici. Řešení HDInsightHadoop vypadá takto:

![Zobrazení monitorování řešení HDInsight](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Protože clusteru je to úplně nový cluster, nebude se v sestavě zobrazovat žádné aktivity.

## <a name="next-steps"></a>Další postup

* [Dotazování Azure Log Analytics k monitorování clusterů HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
