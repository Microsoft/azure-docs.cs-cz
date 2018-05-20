---
title: Správa clusterů systému Hadoop v HDInsight pomocí prostředí PowerShell – Azure | Microsoft Docs
description: Zjistěte, jak k provádění úloh správy pro clusterů systému Hadoop v HDInsight pomocí Azure PowerShell.
services: hdinsight
editor: cgronlun
manager: jhubbard
tags: azure-portal
author: mumian
documentationcenter: ''
ms.assetid: bfdfa754-18e5-4ef9-b0d6-2dbdcebc0283
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: 656ad3673835bb30499931d20fe715e85418b1c3
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="manage-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Správa clusterů systému Hadoop v HDInsight pomocí prostředí Azure PowerShell
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Prostředí Azure PowerShell umožňuje řídit a automatizovat nasazení a správy vašich zatížení v Azure. V tomto článku zjistěte, jak pro správu clusterů systému Hadoop v Azure HDInsight pomocí Azure PowerShell. Seznam rutin prostředí HDInsight PowerShell, naleznete v části [reference k rutině HDInsight][hdinsight-powershell-reference].

**Požadavky**

Před zahájením tohoto článku, musíte mít následující položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="install-azure-powershell"></a>Instalace prostředí Azure PowerShell
[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

Pokud jste nainstalovali Azure PowerShell verze 0,9 x, odinstalujte jej před instalací na novější verzi.

Kontrola verze nainstalované prostředí PowerShell:

```powershell
Get-Module *azure*
```

Chcete-li odinstalovat starší verzi, spusťte programy a funkce v Ovládacích panelech.

## <a name="create-clusters"></a>Vytváření clusterů
V tématu [vytvořit systémem Linux clusterů v HDInsight pomocí Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Seznam clustery
Seznam všech clusterech v aktuálním předplatném použijte následující příkaz:

```powershell
Get-AzureRmHDInsightCluster
```

## <a name="show-cluster"></a>Zobrazit clusteru
Chcete-li zobrazit podrobnosti o konkrétní cluster v aktuálním předplatném použijte následující příkaz:

```powershell
Get-AzureRmHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>Odstranění clusterů
Pokud chcete odstranit cluster použijte následující příkaz:

```powershell
Remove-AzureRmHDInsightCluster -ClusterName <Cluster Name>
```

Můžete také odstranit cluster odebráním skupinu prostředků, která obsahuje clusteru. Odstranění skupiny prostředků se odstraní všechny prostředky ve skupině, včetně výchozí účet úložiště.

```powershell
Remove-AzureRmResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>Škálování clusterů
Funkce škálování clusteru umožňuje změnit počet uzlů pracovního procesu používá cluster, který běží v Azure HDInsight bez nutnosti znovu vytvořit cluster.

> [!NOTE]
> Pouze clustery s HDInsight verze 3.1.3 nebo vyšší nejsou podporovány. Pokud si nejste jistí na verzi vašeho clusteru, můžete zkontrolovat stránku vlastností.  V tématu [seznamu a zobrazit clustery](hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
>
>

Dopad změny v počtu uzlů dat pro každý typ clusteru podporuje HDInsight:

* Hadoop

    Můžete bez problémů zvýšit počet uzlů pracovního procesu v clusteru Hadoop, který běží bez dopadu na všechny úlohy čekající na vyřízení nebo spuštěné. Nové úlohy můžete také odeslány, když probíhá operace. Selhání v rámci operace škálování pohodlné zpracování tak, aby cluster zůstane vždy ve funkčním stavu.

    Pokud se Hadoop cluster měřítko snížením počtu uzlů data, některé služby v clusteru restartovat. Restartování služby způsobí, že všechny spuštěné a čeká se na úlohy selhání po dokončení operace škálování. Můžete, ale odešlete znovu úloh po dokončení operace.
* HBase

    Bezproblémově můžete přidávat nebo odebírat uzly do clusteru HBase, když je spuštěná. Místní servery jsou automaticky vyváženy během několika minut po dokončení operace škálování. Můžete však také ručně vyvážit místní servery přihlášením k headnode clusteru a pak spusťte následující příkazy z okna příkazového řádku:

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

* Storm

    Můžete bezproblémově přidávat nebo odebírat uzly dat do clusteru Storm, když je spuštěná. Ale po úspěšném dokončení operace škálování, budete muset znovu vyvážit topologii.

    Vyrovnává lze dosáhnout dvěma způsoby:

  * Storm webového uživatelského rozhraní
  * Nástroj pro rozhraní příkazového řádku (CLI)

    Odkazovat [dokumentaci Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) další podrobnosti.

    Uživatelské rozhraní Storm webu je k dispozici v clusteru HDInsight:

    ![Obnovte rovnováhu škálování HDInsight storm](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)

    Tady je příklad jak znovu vyvážit topologie Storm pomocí rozhraní příkazového řádku příkaz:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Chcete-li změnit velikost clusteru Hadoop pomocí prostředí Azure PowerShell, spusťte následující příkaz z klientský počítač:

```powershell
Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```


## <a name="grantrevoke-access"></a>Udělení nebo odvolání přístupu
Clustery HDInsight mají následující webové služby HTTP (všechny tyto služby mají RESTful koncových bodů):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Ve výchozím nastavení jsou tyto služby oprávnění pro přístup. Vám může odvolání nebo udělit přístup. K odvolání:

```powershell
Revoke-AzureRmHDInsightHttpServicesAccess -ClusterName <Cluster Name>
```

Udělit:

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
> Udělení nebo odvolání přístupu resetujete, cluster uživatelské jméno a heslo.
>
>

Udělení a odvolání přístupu lze také provést přes portál. V tématu [spravovat HDInsight pomocí portálu Azure][hdinsight-admin-portal].

## <a name="update-http-user-credentials"></a>Aktualizovat pověření uživatele HTTP
Je stejným způsobem jako [HTTP udělení nebo odvolání přístupu](#grant/revoke-access). Pokud cluster byl přidělen přístup protokolu HTTP, musí se nejdřív odvolat.  A pak udělují přístup s novými pověřeními uživatele HTTP.

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
**K odesílání úloh MapReduce**

V tématu [ukázky spustit Hadoop MapReduce v HDInsight se systémem Windows](hdinsight-run-samples.md).

**K odeslání úloh Hive**

V tématu [spouštění dotazů Hive pomocí prostředí PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).

**K odeslání úlohy Pig**

V tématu [úlohy Pig spuštění pomocí prostředí PowerShell](hadoop/apache-hadoop-use-pig-powershell.md).

**K odesílání úloh Sqoop**

V tématu [použití nástroje Sqoop se HDInsight](hadoop/hdinsight-use-sqoop.md).

**K odesílání úloh Oozie**

V tématu [Oozie použití se systémem Hadoop k definování a spuštění workflowu v HDInsight](hdinsight-use-oozie.md).

## <a name="upload-data-to-azure-blob-storage"></a>Nahrání dat do úložiště objektů Blob v Azure
Viz [Nahrání dat do služby HDInsight][hdinsight-upload-data].

## <a name="see-also"></a>Viz také
* [HDInsight rutiny referenční dokumentaci k nástroji][hdinsight-powershell-reference]
* [Spravovat HDInsight pomocí portálu Azure][hdinsight-admin-portal]
* [Spravovat HDInsight pomocí rozhraní příkazového řádku][hdinsight-admin-cli]
* [Vytvoření clusterů HDInsight][hdinsight-provision]
* [Nahrání dat do služby HDInsight][hdinsight-upload-data]
* [Odesílání úloh Hadoop prostřednictvím kódu programu][hdinsight-submit-jobs]
* [Začínáme se službou Azure HDInsight][hdinsight-get-started]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

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
