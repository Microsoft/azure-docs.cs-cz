---
title: Použít k monitorování clusterů Azure HDInsight Log Analytics | Microsoft Docs
description: Naučte se používat Azure Log Analytics ke sledování úloh spuštěných v clusteru služby HDInsight.
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
ms.openlocfilehash: e11c9672e2e96f3370c69b33f57a007f7d63ccf2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Monitorování clusterů HDInsight pomocí Azure Log Analytics

Naučte se používat Azure Log Analytics ke sledování operací clusteru Hadoop v HDInsight.

[Analýza protokolu](../log-analytics/log-analytics-overview.md) je služba, která monitoruje své cloudové a místní prostředí k udržování své dostupnosti a výkonu. Shromažďuje data generovaná prostředky ve vašem cloudovém a místním prostředí a také data z dalších nástrojů pro monitorování a poskytuje analýzy napříč zdroji. 

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Než začnete tento kurz, musíte mít předplatné Azure. Přečtěte si téma [Bezplatné vytvoření účtu Microsoft Azure ještě dnes](https://azure.microsoft.com/free).

* **Cluster Azure HDInsight**. V současné době můžete provádět analýzy protokolů s těmito typy clusteru HDInsight:

    * Hadoop
    * HBase
    * Interaktivní dotaz
    * Kafka
    * Spark
    * Storm

    Pokyny k vytvoření clusteru HDInsight naleznete v tématu [Začínáme s Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Pracovní prostor analýzy protokolů**. Tento pracovní prostor si můžete představit jako jedinečné prostředí analýzy protokolů s vlastní úložiště dat, zdroje dat a řešení. Musí mít jeden takový prostoru už vytvořený, můžete přidružit Azure HDInsight clustery. Pokyny najdete v tématu [vytvořit pracovní prostor analýzy protokolů](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace).

## <a name="enable-log-analytics-by-using-the-portal"></a>Povolit pomocí portálu analýzy protokolů

V této části nakonfigurujete stávajícího clusteru HDInsight Hadoop použití k pracovnímu prostoru analýzy protokolů Azure k monitorování úlohy, protokoly ladění atd.

1. Otevřete cluster služby HDInsight v portálu Azure.
2. V levém podokně klikněte na **monitorování**.
3. V pravém podokně klikněte na **povolit**, vyberte existující pracovní prostor analýzy protokolů a pak klikněte na tlačítko **Uložit**.

    ![Povolit monitorování pro clustery služby HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "povolit monitorování pro clustery služby HDInsight")

    Jak dlouho trvá chvíli se uložit nastavení.  Až se to dokončí, zobrazí se **otevřete řídicí panel OMS** tlačítko nahoře. 

    ![Otevřete řídicí panel služby Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-open-workspace.png "OMS otevřete řídicí panel")

5. Klikněte na tlačítko **řídicí panel otevřete OMS**.
6. Pokud se zobrazí výzva, zadejte přihlašovací údaje Azure.

    ![Portál Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-oms-portal.png "portál Operations Management Suite")

## <a name="enable-log-analytics-by-using-azure-powershell"></a>Povolení analýzy protokolů pomocí Azure PowerShell

Můžete povolit analýzy protokolů pomocí Azure PowerShell. Rutina je:

```powershell
Enable-AzureRmHDInsightOperationsManagementSuite
```

V tématu [povolit AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/Enable-AzureRmHDInsightOperationsManagementSuite?view=azurermps-5.0.0).

Pokud chcete zakázat, je rutina 

```powershell
Disable-AzureRmHDInsightOperationsManagementSuite
```

V tématu [zakázat AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/disable-azurermhdinsightoperationsmanagementsuite?view=azurermps-5.0.0).


## <a name="next-steps"></a>Další postup
* [Přidat řešení pro správu clusteru HDInsight k analýze protokolů](hdinsight-hadoop-oms-log-analytics-management-solutions.md)
