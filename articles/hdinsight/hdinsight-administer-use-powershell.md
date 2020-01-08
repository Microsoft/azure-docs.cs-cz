---
title: Správa clusterů Apache Hadoop pomocí prostředí PowerShell – Azure HDInsight
description: Naučte se provádět úlohy správy pro Apache Hadoop clustery v HDInsight pomocí Azure PowerShell.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/09/2019
ms.openlocfilehash: e37571b0078b4966aab9f505ddf88c2edb353197
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435626"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Správa clusterů Apache Hadoop ve službě HDInsight pomocí Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell lze použít k řízení a automatizaci nasazení a správy úloh v Azure. V tomto článku se naučíte spravovat [Apache Hadoop](https://hadoop.apache.org/) clustery ve službě Azure HDInsight pomocí Azure PowerShell AZ Module. Seznam rutin prostředí PowerShell pro HDInsight najdete v tématu [AZ. HDInsight reference](https://docs.microsoft.com/powershell/module/az.hdinsight).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Prostředí PowerShell [AZ Module](https://docs.microsoft.com/powershell/azure/overview) installed.

## <a name="create-clusters"></a>Vytváření clusterů

Další informace najdete [v tématu Vytvoření clusterů se systémem Linux v HDInsight pomocí Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Výpis clusterů

K vypsání všech clusterů v aktuálním předplatném použijte následující příkaz:

```powershell
Get-AzHDInsightCluster
```

## <a name="show-cluster"></a>Zobrazit cluster

Pomocí následujícího příkazu zobrazíte podrobnosti konkrétního clusteru v aktuálním předplatném:

```powershell
Get-AzHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>Odstranění clusterů

Pomocí následujícího příkazu odstraňte cluster:

```powershell
Remove-AzHDInsightCluster -ClusterName <Cluster Name>
```

Cluster můžete odstranit i tak, že odeberete skupinu prostředků, která obsahuje cluster. Odstraněním skupiny prostředků se odstraní všechny prostředky ve skupině, včetně výchozího účtu úložiště.

```powershell
Remove-AzResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>Škálování clusterů

Funkce škálování clusteru umožňuje změnit počet pracovních uzlů používaných clusterem, který běží ve službě Azure HDInsight, aniž by bylo nutné cluster znovu vytvořit. Chcete-li změnit velikost clusteru Hadoop pomocí Azure PowerShell, spusťte z klientského počítače následující příkaz:

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```

 Další informace o škálování clusterů najdete v tématu [škálování clusterů HDInsight](./hdinsight-scaling-best-practices.md).

## <a name="grantrevoke-access"></a>Udělit nebo odvolat přístup

Clustery HDInsight mají následující webové služby HTTP (všechny tyto služby mají koncové body RESTful):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Ve výchozím nastavení jsou tyto služby uděleny pro přístup. Přístup můžete odvolat nebo udělit. Odvolat:

```powershell
Revoke-AzHDInsightHttpServicesAccess -ClusterName <Cluster Name>
```

Chcete-li udělit:

```powershell
$clusterName = "<HDInsight Cluster Name>"

# Credential option 1
$hadoopUserName = "admin"
$hadoopUserPassword = '<Enter the Password>'
$hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

# Credential option 2
#$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Grant-AzHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential
```

> [!NOTE]  
> Tím, že udělíte nebo odvoláváte přístup, resetujete uživatelské jméno a heslo clusteru.

Přístup k udělení a odvolání je možné provést taky prostřednictvím portálu. Další informace najdete v tématu [správa Apache Hadoop clusterů ve službě HDInsight pomocí Azure Portal](hdinsight-administer-use-portal-linux.md).

## <a name="update-http-user-credentials"></a>Aktualizovat přihlašovací údaje uživatele HTTP

Je to stejný postup jako udělení nebo odvolání přístupu HTTP. Pokud byl clusteru udělen přístup HTTP, je nutné jej nejprve odvolat.  A pak udělte přístup pomocí nových přihlašovacích údajů uživatele HTTP.

## <a name="find-the-default-storage-account"></a>Najít výchozí účet úložiště

Následující skript PowerShellu ukazuje, jak získat výchozí název účtu úložiště a související informace:

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

## <a name="find-the-resource-group"></a>Najít skupinu prostředků

V režimu Správce prostředků každý cluster HDInsight patří do skupiny prostředků Azure.  Postup vyhledání skupiny prostředků:

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```

## <a name="submit-jobs"></a>Odeslat úlohy

**Odeslání úloh MapReduce**

Podívejte [se na příklady spuštění MapReduce obsažených v HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**Odeslání Apache Hivech úloh**

Přečtěte si téma [spuštění Apache Hive dotazů pomocí prostředí PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).

**Odeslání úloh Apache Sqoop**

Viz [použití Apache Sqoop se službou HDInsight](hadoop/hdinsight-use-sqoop.md).

**Odeslání úloh Apache Oozie**

Další informace najdete [v tématu použití Apache Oozie s Apache Hadoop k definování a spuštění pracovního postupu v HDInsight](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Nahrání dat do služby Azure Blob Storage

Viz [nahrání dat do HDInsight](hdinsight-upload-data.md).

## <a name="see-also"></a>Viz také

* [AZ. HDInsight – rutiny](https://docs.microsoft.com/powershell/module/az.hdinsight/?view=azps-3.1.0#hdinsight)
* [Správa clusterů Apache Hadoop ve službě HDInsight pomocí Azure Portal](hdinsight-administer-use-portal-linux.md)
* [Správa HDInsight pomocí rozhraní příkazového řádku](hdinsight-administer-use-command-line.md)
* [Vytváření clusterů HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Programové odeslání Apache Hadoop úloh](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Začínáme se službou Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
