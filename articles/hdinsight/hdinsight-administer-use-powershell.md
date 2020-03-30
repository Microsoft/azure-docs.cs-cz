---
title: Správa clusterů Apache Hadoop pomocí PowerShellu – Azure HDInsight
description: Zjistěte, jak provádět úlohy správy pro clustery Apache Hadoop ve službě HDInsight pomocí Azure PowerShellu.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/13/2020
ms.openlocfilehash: 104975e6424ed96d43434a588997957033c31d93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560350"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Správa clusterů Apache Hadoop v HDInsightu pomocí Azure PowerShellu

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell se dá použít k řízení a automatizaci nasazení a správy vašich úloh v Azure. V tomto článku se dozvíte, jak spravovat clustery [Apache Hadoop](https://hadoop.apache.org/) v Azure HDInsight pomocí modulu Azure PowerShell Az. Seznam rutin prostředí HDInsight PowerShell naleznete v [odkazu Az.HDInsight](https://docs.microsoft.com/powershell/module/az.hdinsight).

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Nainstalován [modul Az prostředí](https://docs.microsoft.com/powershell/azure/overview) PowerShell.

## <a name="create-clusters"></a>Vytváření clusterů

Viz [Vytváření linuxových clusterů ve HDInsightu pomocí Azure PowerShellu](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Seznam clusterů

Pomocí následujícího příkazu zobrazíte seznam všech clusterů v aktuálním předplatném:

```powershell
Get-AzHDInsightCluster
```

## <a name="show-cluster"></a>Zobrazit cluster

Pomocí následujícího příkazu zobrazte podrobnosti o konkrétním clusteru v aktuálním předplatném:

```powershell
Get-AzHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>Odstranění clusterů

K odstranění clusteru použijte následující příkaz:

```powershell
Remove-AzHDInsightCluster -ClusterName <Cluster Name>
```

Cluster můžete odstranit také odebráním skupiny prostředků, která jej obsahuje. Odstraněním skupiny prostředků odstraníte všechny prostředky ve skupině včetně výchozího účtu úložiště.

```powershell
Remove-AzResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>Škálovat clustery

Funkce škálování clusteru umožňuje změnit počet pracovních uzlů používaných clusterem, který běží v Azure HDInsight, aniž byste museli cluster znovu vytvářet. Chcete-li změnit velikost clusteru Hadoop pomocí Azure PowerShellu, spusťte z klientského počítače následující příkaz:

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```

 Další informace o škálování clusterů naleznete v [tématu Škálování clusterů HDInsight](./hdinsight-scaling-best-practices.md).

## <a name="update-http-user-credentials"></a>Aktualizace pověření uživatele PROTOKOLU HTTP

[Set-AzHDInsightGatewayCredential](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightgatewaycredential) nastavuje http přihlašovací údaje brány clusteru Azure HDInsight.

```powershell
$clusterName = "CLUSTERNAME"
$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Set-AzHDInsightGatewayCredential -ClusterName $clusterName -HttpCredential $credential
```

## <a name="find-the-default-storage-account"></a>Vyhledání výchozího účtu úložiště

Následující skript prostředí PowerShell ukazuje, jak získat výchozí název účtu úložiště a související informace:

```powershell
#Connect-AzAccount
$clusterName = "<HDInsight Cluster Name>"

$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$storageInfo = $clusterInfo.DefaultStorageAccount.split('.')
$defaultStoreageType = $storageInfo[1]
$defaultStorageName = $storageInfo[0]

echo "Default Storage account name: $defaultStorageName"
echo "Default Storage account type: $defaultStoreageType"

if ($defaultStoreageType -eq "blob")
{
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey

    echo "Default Blob container name: $defaultBlobContainerName"
    echo "Default Storage account key: $defaultStorageAccountKey"
}
```

## <a name="find-the-resource-group"></a>Vyhledání skupiny prostředků

V režimu Správce prostředků patří každý cluster HDInsight do skupiny prostředků Azure.  Jak najít skupinu prostředků:

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```

## <a name="submit-jobs"></a>Odeslat úlohy

**Odeslání úloh MapReduce**

Viz [Spuštění příkladů MapReduce zahrnutých do hdinsightu](hadoop/apache-hadoop-run-samples-linux.md).

**Odeslání pracovních míst Apache Hive**

Viz [Spuštění dotazů Apache Hive pomocí PowerShellu](hadoop/apache-hadoop-use-hive-powershell.md).

**Odeslání pracovních míst Apache Sqoop**

Viz [Použití Apache Sqoop s HDInsight](hadoop/hdinsight-use-sqoop.md).

**Chcete-li odeslat Apache Oozie pracovní chod**

Viz [Použití Apache Oozie s Apache Hadoop definovat a spustit pracovní postup v HDInsight](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Nahrání dat do úložiště objektů blob Azure

Viz [Nahrání dat do služby HDInsight](hdinsight-upload-data.md).

## <a name="see-also"></a>Viz také

* [Rutiny Az.HDInsight](https://docs.microsoft.com/powershell/module/az.hdinsight/?view=azps-3.1.0#hdinsight)
* [Správa clusterů Apache Hadoop v HDInsightu pomocí portálu Azure](hdinsight-administer-use-portal-linux.md)
* [Správa hdinsightu pomocí rozhraní příkazového řádku](hdinsight-administer-use-command-line.md)
* [Vytváření clusterů HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Odeslat pracovní příležitosti Apache Hadoop programově](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Začínáme se službou Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
