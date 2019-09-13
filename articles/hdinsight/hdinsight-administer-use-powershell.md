---
title: Správa clusterů Apache Hadoop ve službě HDInsight pomocí PowerShellu – Azure
description: Naučte se provádět úlohy správy pro Apache Hadoop clustery v HDInsight pomocí Azure PowerShell.
ms.reviewer: tyfox
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: tyfox
ms.openlocfilehash: 3f4ccd8de1f26ea898b0e7ec4bb57aa20b1be209
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70885368"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Správa clusterů Apache Hadoop ve službě HDInsight pomocí Azure PowerShell
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell lze použít k řízení a automatizaci nasazení a správy úloh v Azure. V tomto článku se naučíte spravovat [Apache Hadoop](https://hadoop.apache.org/) clustery ve službě Azure HDInsight pomocí Azure PowerShell AZ Module. Seznam rutin prostředí PowerShell pro HDInsight najdete v tématu [AZ. HDInsight reference](https://docs.microsoft.com/powershell/module/az.hdinsight).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Prostředí PowerShell [AZ Module](https://docs.microsoft.com/powershell/azure/overview) installed.

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
Funkce škálování clusteru umožňuje změnit počet pracovních uzlů používaných clusterem, který běží ve službě Azure HDInsight, aniž by bylo nutné cluster znovu vytvořit.

Dopad změny počtu datových uzlů pro každý typ clusteru podporovaný službou HDInsight:

* Apache Hadoop

    Můžete bez problémů zvýšit počet pracovních uzlů v clusteru Hadoop, který je spuštěný, aniž by to ovlivnilo nedokončené nebo spuštěné úlohy. Nové úlohy je možné odeslat i v průběhu operace. Selhání operace škálování se řádným způsobem zpracovávají, aby byl cluster vždycky ponechán ve funkčním stavu.

    Pokud je cluster Hadoop škálované dolů snížením počtu datových uzlů, některé ze služeb v clusteru se restartují. Restartování služeb způsobí, že všechny spuštěné a nedokončené úlohy selžou při dokončení operace škálování. Po dokončení operace ale můžete úlohy znovu odeslat.
* Apache HBase

    Bez problémů můžete přidat nebo odebrat uzly do clusteru HBA v době, kdy je spuštěný. Regionální servery se automaticky vyrovnávají během několika minut od dokončení operace škálování. Můžete ale také ručně vyrovnávat regionální servery přihlášením k hlavnímu uzlu clusteru a pak z okna příkazového řádku spustit následující příkazy:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

* Apache Storm

    Můžete hladce přidávat nebo odebírat datové uzly do clusteru s více podsystémy, když je spuštěný. Po úspěšném dokončení operace škálování ale budete muset topologii znovu vyvážit.

    Nové Vyrovnávání je možné dosáhnout dvěma způsoby:

  * Webové uživatelské rozhraní pro vyplavení
  * Nástroj rozhraní příkazového řádku (CLI)

    Další podrobnosti najdete v [dokumentaci k Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) .

    Webové uživatelské rozhraní pro zaplavení je k dispozici v clusteru HDInsight:

    ![Rebilance škálování ve službě HDInsight](./media/hdinsight-administer-use-powershell/portal-scale-cluster.png)

    Tady je příklad, jak pomocí příkazu CLI znovu vyrovnávat topologii zatížení:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Chcete-li změnit velikost clusteru Hadoop pomocí Azure PowerShell, spusťte z klientského počítače následující příkaz:

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```


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
$hadoopUserPassword = "<Enter the Password>"
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
Stejný postup je stejný jako u udělení nebo odvolání přístupu HTTP. Pokud byl clusteru udělen přístup HTTP, je nutné jej nejprve odvolat.  A pak udělte přístup pomocí nových přihlašovacích údajů uživatele HTTP.

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

* [Referenční dokumentace k rutinám HDInsight](https://msdn.microsoft.com/library/azure/dn479228.aspx)
* [Správa clusterů Apache Hadoop ve službě HDInsight pomocí Azure Portal](hdinsight-administer-use-portal-linux.md)
* [Správa HDInsight pomocí rozhraní příkazového řádku](hdinsight-administer-use-command-line.md)
* [Vytváření clusterů HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Programové odeslání Apache Hadoop úloh](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Začínáme se službou Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
