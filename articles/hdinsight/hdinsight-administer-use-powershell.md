---
title: Správa clusterů Apache Hadoop v HDInsight pomocí Powershellu – Azure
description: Zjistěte, jak k provádění úloh správy pro clustery systému Apache Hadoop v HDInsight pomocí Azure Powershellu.
ms.reviewer: tyfox
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: tylerfox
ms.openlocfilehash: a7e129f43b957b271c77f451ab198a9068bb0797
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62096351"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Spravovat clustery systému Apache Hadoop v HDInsight pomocí Azure Powershellu
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Prostředí Azure PowerShell slouží k řízení a automatizaci nasazení a správu vašich úloh v Azure. V tomto článku se dozvíte, jak spravovat [Apache Hadoop](https://hadoop.apache.org/) clustery v Azure HDInsight pomocí modulu Azure PowerShell Az. Seznam rutin Powershellu pro HDInsight najdete v tématu [Az.HDInsight odkaz](https://docs.microsoft.com/powershell/module/az.hdinsight).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* PowerShell [Az modulu](https://docs.microsoft.com/powershell/azure/overview) nainstalované.

## <a name="create-clusters"></a>Vytváření clusterů
Zobrazit [vytvoření linuxových clusterech v HDInsight pomocí Azure Powershellu](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Výpis clusterů
Pomocí následujícího příkazu zobrazíte seznam všech clusterů v rámci aktuálního předplatného:

```powershell
Get-AzHDInsightCluster
```

## <a name="show-cluster"></a>Zobrazení clusteru
Chcete-li zobrazit podrobnosti o konkrétní cluster v aktuálním předplatném, použijte následující příkaz:

```powershell
Get-AzHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>Odstranění clusterů
Pomocí následujícího příkazu odstraňte cluster:

```powershell
Remove-AzHDInsightCluster -ClusterName <Cluster Name>
```

Odstraněním skupiny prostředků, která obsahuje clusteru můžete také odstranit cluster. Odstranění skupiny prostředků odstraní všechny prostředky ve skupině, včetně výchozího účtu úložiště.

```powershell
Remove-AzResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>Škálování clusterů
Funkce škálování clusteru umožňuje změnit počet uzlů pracovního procesu se používá cluster, který běží v Azure HDInsight bez nutnosti nového vytváření clusteru.

Dopad Změna počtu datových uzlů pro každý typ clusteru podporuje HDInsight:

* Apache Hadoop

    Bezproblémově můžete zvýšit počet pracovních uzlů v clusteru Hadoop, na kterém běží bez dopadu na všechny úlohy čekající na vyřízení nebo spuštěné. Nové úlohy můžete odeslat také když probíhá operace. Selhání v rámci operace škálování jsou zpracovány bez výpadku v tak, aby cluster zůstane vždy ve funkčním stavu.

    Pokud je Hadoop cluster je kapacitu vertikálně snížit snížením počtu datových uzlů, jsou restartovat některé ze služeb v clusteru. Restartování služeb způsobí, že všechny spuštěné a čekající úlohy selhání po dokončení operace škálování. Můžete, ale neúspěšné úlohy po dokončení operace.
* Apache HBase

    Bezproblémově můžete přidat nebo odebrat uzly do clusteru HBase během jejího běhu. Oblastní servery jsou automaticky rovnoměrně rozdělen do několika minut od dokončení operace škálování. Můžete však také ručně vyvážit místní servery. přihlášením k hlavnímu uzlu clusteru a pak spusťte následující příkazy z okna příkazového řádku:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

* Apache Storm

    Bezproblémově můžete přidat nebo odebrat datových uzlů do clusteru Storm během jejího běhu. Ale po úspěšném dokončení operace škálování, budete muset vyrovnat topologie.

    Opětovné vyvážení lze provést dvěma způsoby:

  * Webové uživatelské rozhraní Storm
  * Nástroje rozhraní příkazového řádku (CLI)

    Odkazovat [dokumentaci Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) další podrobnosti.

    Webové uživatelské rozhraní Storm je k dispozici v clusteru HDInsight:

    ![Obnovení rovnováhy škálování HDInsight storm](./media/hdinsight-administer-use-powershell/hdinsight.portal.scale.cluster.png)

    Tady je příklad jak obnovit rovnováhu topologie Storm pomocí příkazu rozhraní příkazového řádku:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Chcete-li změnit velikost clusteru Hadoop s použitím prostředí Azure PowerShell, spusťte následující příkaz z klientského počítače:

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```


## <a name="grantrevoke-access"></a>Udělení nebo odvolání přístupu
Clustery HDInsight mají následující webové služby HTTP (mít všechny tyto služby RESTful koncových bodů):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Tyto služby jsou ve výchozím nastavení oprávnění pro přístup. Vám může k nim odvolat/udělit přístup. Chcete-li odebrat:

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
> Udělení nebo odvolání přístupu, obnovíte, clusteru uživatelské jméno a heslo.

Poskytování a odvolání přístupu je možné provést přes portál. Zobrazit [spravovat Apache Hadoop clusterů v HDInsight pomocí webu Azure portal](hdinsight-administer-use-portal-linux.md).

## <a name="update-http-user-credentials"></a>Aktualizace přihlašovacích údajů uživatele HTTP
Je stejným způsobem jako HTTP udělení nebo odvolání přístupu. Pokud cluster má udělen přístup protokolu HTTP, musí se nejprve odvolat.  A potom jim udělit přístup pomocí nových přihlašovacích údajů uživatele HTTP.

## <a name="find-the-default-storage-account"></a>Najít výchozí účet úložiště
Následující skript prostředí PowerShell ukazuje, jak získat výchozí název účtu úložiště a související informace:

> [!IMPORTANT]  
> Hodnoty pro `DefaultStorageAccount`, a `DefaultStorageContainer` nebudou zobrazeny z [Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) při [zabezpečený přenos](../storage/common/storage-require-secure-transfer.md) je povolená v účtu úložiště.


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
V režimu Resource Manager každý cluster HDInsight patří do skupiny prostředků Azure.  Chcete-li najít skupinu prostředků:

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```


## <a name="submit-jobs"></a>Odesílání úloh
**Odesílat úlohy MapReduce**

Zobrazit [spuštění příkladů MapReduce součástí HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**Odesílat úlohy Apache Hive**

Zobrazit [spouštění Apache dotazů Hive pomocí prostředí PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).

**Odesílat úlohy Apache Pig**

Zobrazit [úloh spustit Apache Pig s využitím Powershellu](hadoop/apache-hadoop-use-pig-powershell.md).

**Odesílat úlohy Apache Sqoop**

Zobrazit [použití Apache Sqoop s HDInsight](hadoop/hdinsight-use-sqoop.md).

**Odesílat úlohy Apache Oozie**

Zobrazit [použití Apache Oozie s Hadoopem Apache k definování a spuštění workflowu v HDInsight](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Nahrání dat do úložiště objektů Blob v Azure

Zobrazit [nahrání dat do HDInsight](hdinsight-upload-data.md).

## <a name="see-also"></a>Viz také

* [Referenční dokumentace k rutinám HDInsight](https://msdn.microsoft.com/library/azure/dn479228.aspx)
* [Spravovat clustery systému Apache Hadoop v HDInsight pomocí webu Azure portal](hdinsight-administer-use-portal-linux.md)
* [Správa HDInsight pomocí rozhraní příkazového řádku](hdinsight-administer-use-command-line.md)
* [Vytvoření clusterů HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Odeslání úloh systému Apache Hadoop prostřednictvím kódu programu](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Začínáme s Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
