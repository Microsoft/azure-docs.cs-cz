---
title: Správa clusterů Apache Hadoop v HDInsight pomocí Powershellu – Azure
description: Zjistěte, jak k provádění úloh správy pro clustery systému Apache Hadoop v HDInsight pomocí Azure Powershellu.
services: hdinsight
ms.reviewer: tyfox
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: tylerfox
ms.openlocfilehash: 8454a22b02f28ece45ce04464f8f2658f745b53e
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53163340"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Spravovat clustery systému Apache Hadoop v HDInsight pomocí Azure Powershellu
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Prostředí Azure PowerShell slouží k řízení a automatizaci nasazení a správu vašich úloh v Azure. V tomto článku se dozvíte, jak spravovat [Apache Hadoop](https://hadoop.apache.org/) clustery v Azure HDInsight pomocí Azure Powershellu. Seznam rutin Powershellu pro HDInsight najdete v tématu [Reference k rutinám HDInsight](https://msdn.microsoft.com/library/azure/dn479228.aspx).

**Požadavky**

Před zahájením tohoto článku, musíte mít následující položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="install-azure-powershell"></a>Instalace prostředí Azure PowerShell
[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

Pokud jste nainstalovali Azure PowerShell verze 0.9 x, musíte ho odinstalovat před instalací novější verze.

Pokud chcete zkontrolovat verzi nainstalovaného powershellu:

```powershell
Get-Module *azure*
```

Odinstalovat starší verze, spusťte v ovládacím panelu Programy a funkce.

## <a name="create-clusters"></a>Vytváření clusterů
Zobrazit [vytvoření linuxových clusterech v HDInsight pomocí Azure Powershellu](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Výpis clusterů
Pomocí následujícího příkazu zobrazíte seznam všech clusterů v rámci aktuálního předplatného:

```powershell
Get-AzureRmHDInsightCluster
```

## <a name="show-cluster"></a>Zobrazení clusteru
Chcete-li zobrazit podrobnosti o konkrétní cluster v aktuálním předplatném, použijte následující příkaz:

```powershell
Get-AzureRmHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>Odstranění clusterů
Pomocí následujícího příkazu odstraňte cluster:

```powershell
Remove-AzureRmHDInsightCluster -ClusterName <Cluster Name>
```

Odstraněním skupiny prostředků, která obsahuje clusteru můžete také odstranit cluster. Odstranění skupiny prostředků odstraní všechny prostředky ve skupině, včetně výchozího účtu úložiště.

```powershell
Remove-AzureRmResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>Škálování clusterů
Funkce škálování clusteru umožňuje změnit počet uzlů pracovního procesu se používá cluster, který běží v Azure HDInsight bez nutnosti nového vytváření clusteru.

> [!NOTE]
> Pouze clustery HDInsight verze 3.1.3 nebo vyšší nejsou podporovány. Pokud si nejste jistí verze vašeho clusteru, můžete zkontrolovat na stránce Vlastnosti.  Zobrazit [výpisu a zobrazení clusterů](hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
>
>

Dopad Změna počtu datových uzlů pro každý typ clusteru podporuje HDInsight:

* Apache Hadoop

    Bezproblémově můžete zvýšit počet pracovních uzlů v clusteru Hadoop, na kterém běží bez dopadu na všechny úlohy čekající na vyřízení nebo spuštěné. Nové úlohy můžete odeslat také když probíhá operace. Selhání v rámci operace škálování jsou zpracovány bez výpadku v tak, aby cluster zůstane vždy ve funkčním stavu.

    Pokud je Hadoop cluster je kapacitu vertikálně snížit snížením počtu datových uzlů, jsou restartovat některé ze služeb v clusteru. Restartování služeb způsobí, že všechny spuštěné a čekající úlohy selhání po dokončení operace škálování. Můžete, ale neúspěšné úlohy po dokončení operace.
* Apache HBase

    Bezproblémově můžete přidat nebo odebrat uzly do clusteru HBase během jejího běhu. Oblastní servery jsou automaticky rovnoměrně rozdělen do několika minut od dokončení operace škálování. Můžete však také ručně vyvážit místní servery. přihlášením k hlavnímu uzlu clusteru a pak spusťte následující příkazy z okna příkazového řádku:

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

* Apache Storm

    Bezproblémově můžete přidat nebo odebrat datových uzlů do clusteru Storm během jejího běhu. Ale po úspěšném dokončení operace škálování, budete muset vyrovnat topologie.

    Opětovné vyvážení lze provést dvěma způsoby:

  * Webové uživatelské rozhraní Storm
  * Nástroje rozhraní příkazového řádku (CLI)

    Odkazovat [dokumentaci Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) další podrobnosti.

    Webové uživatelské rozhraní Storm je k dispozici v clusteru HDInsight:

    ![Obnovení rovnováhy škálování HDInsight storm](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)

    Tady je příklad jak obnovit rovnováhu topologie Storm pomocí příkazu rozhraní příkazového řádku:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Chcete-li změnit velikost clusteru Hadoop s použitím prostředí Azure PowerShell, spusťte následující příkaz z klientského počítače:

```powershell
Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
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
Revoke-AzureRmHDInsightHttpServicesAccess -ClusterName <Cluster Name>
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

Grant-AzureRmHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential
```

> [!NOTE]
> Udělení nebo odvolání přístupu, obnovíte, clusteru uživatelské jméno a heslo.
>
>

Poskytování a odvolání přístupu je možné provést přes portál. Zobrazit [Správa HDInsight pomocí webu Azure portal][hdinsight-admin-portal].

## <a name="update-http-user-credentials"></a>Aktualizace přihlašovacích údajů uživatele HTTP
Je stejným způsobem jako [HTTP udělení nebo odvolání přístupu](#grant/revoke-access). Pokud cluster má udělen přístup protokolu HTTP, musí se nejprve odvolat.  A potom jim udělit přístup pomocí nových přihlašovacích údajů uživatele HTTP.

## <a name="find-the-default-storage-account"></a>Najít výchozí účet úložiště
Následující skript prostředí PowerShell ukazuje, jak získat výchozí název účtu úložiště a související informace:

```powershell
#Connect-AzureRmAccount
$clusterName = "<HDInsight Cluster Name>"

$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$storageInfo = $clusterInfo.DefaultStorageAccount.split('.')
$defaultStoreageType = $storageInfo[1]
$defaultStorageName = $storageInfo[0]

echo "Default Storage account name: $defaultStorageName"
echo "Default Storage account type: $defaultStoreageType"

if ($defaultStoreageType -eq "blob")
{
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey

    echo "Default Blob container name: $defaultBlobContainerName"
    echo "Default Storage account key: $defaultStorageAccountKey"
}
```


## <a name="find-the-resource-group"></a>Najít skupinu prostředků
V režimu Resource Manager každý cluster HDInsight patří do skupiny prostředků Azure.  Chcete-li najít skupinu prostředků:

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```


## <a name="submit-jobs"></a>Odesílání úloh
**Odesílat úlohy Apache Hadoop MapReduce**

Zobrazit [spuštění příkladů Apache Hadoop MapReduce součástí HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**Odesílat úlohy Apache Hive**

Zobrazit [spouštění Apache dotazů Hive pomocí prostředí PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).

**Odesílat úlohy Apache Pig**

Zobrazit [úloh spustit Apache Pig s využitím Powershellu](hadoop/apache-hadoop-use-pig-powershell.md).

**Odesílat úlohy Apache Sqoop**

Zobrazit [použití Apache Sqoop s HDInsight](hadoop/hdinsight-use-sqoop.md).

**Odesílat úlohy Apache Oozie**

Zobrazit [použití Apache Oozie s Hadoopem Apache k definování a spuštění workflowu v HDInsight](hdinsight-use-oozie.md).

## <a name="upload-data-to-azure-blob-storage"></a>Nahrání dat do úložiště objektů Blob v Azure
Viz [Nahrání dat do služby HDInsight][hdinsight-upload-data].

## <a name="see-also"></a>Viz také
* [Referenční dokumentace k rutinám HDInsight](https://msdn.microsoft.com/library/azure/dn479228.aspx)
* [Správa HDInsight pomocí webu Azure portal][hdinsight-admin-portal]
* [Správa HDInsight pomocí rozhraní příkazového řádku][hdinsight-admin-cli]
* [Vytvoření clusterů HDInsight][hdinsight-provision]
* [Nahrání dat do služby HDInsight][hdinsight-upload-data]
* [Odeslání úloh systému Apache Hadoop prostřednictvím kódu programu][hdinsight-submit-jobs]
* [Začínáme se službou Azure HDInsight][hdinsight-get-started]

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-provision-custom-options]: hdinsight-hadoop-provision-linux-clusters.md#configuration
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png
