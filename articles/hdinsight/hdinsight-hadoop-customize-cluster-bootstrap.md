---
title: Přizpůsobení konfigurace clusteru Azure HDInsight pomocí bootstrapu
description: Zjistěte, jak přizpůsobit konfiguraci clusteru HDInsight programově pomocí rozhraní .net, PowerShell a Resource Manageru šablony.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 7f9100686eaab8c4c75e3d862026b18b6c46ed09
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65203709"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Přizpůsobení clusterů HDInsight pomocí Bootstrap

Spouštěcí skripty umožňují instalaci a konfiguraci součásti v Azure HDInsight prostřednictvím kódu programu. 

Chcete-li nastavit nastavení konfiguračního souboru při vytváření clusteru HDInsight třemi způsoby:

* Použití Azure Powershell
* Použití sady .NET SDK
* Použití šablon Azure Resource Manageru

Například použití těchto metod prostřednictvím kódu programu, můžete nakonfigurovat možnosti v těchto souborech:

* clusterIdentity.xml
* Core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie site.xml
* oozie-env.xml
* Storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml
* Server.Properties (Konfigurace zprostředkovatele kafka)

Informace o instalaci dalších komponent na clusteru HDInsight při vytváření najdete v tématu [HDInsight přizpůsobit clustery pomocí akce skriptu (Linux)](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="prerequisites"></a>Požadavky

* Pokud používáte PowerShell, budete potřebovat [Az modulu](https://docs.microsoft.com/powershell/azure/overview).

## <a name="use-azure-powershell"></a>Použití Azure Powershell

Následující kód Powershellu přizpůsobí [Apache Hive](https://hive.apache.org/) konfigurace:

> [!IMPORTANT]  
> Parametr `Spark2Defaults` možná bude nutné použít s [přidat AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue). Prázdné hodnoty můžete předat parametru, jak je znázorněno v následujícím příkladu kódu.


```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

New-AzHDInsightCluster `
    -ResourceGroupName $existingResourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -Config $config
```

Kompletní funkční skript prostředí PowerShell najdete v [příloha](#appendix-powershell-sample).

**Chcete-li ověřit změny:**

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. V levé nabídce klikněte na tlačítko **clustery HDInsight**. Pokud ho nevidíte, klikněte na tlačítko **všechny služby** první.
3. Klikněte na cluster, který jste právě vytvořili pomocí Powershellového skriptu.
4. Klikněte na tlačítko **řídicí panel** z horní části okna otevřete uživatelské rozhraní Ambari.
5. Klikněte na tlačítko **Hive** v levé nabídce.
6. Klikněte na tlačítko **HiveServer2** z **Souhrn**.
7. Klikněte na tlačítko **Configs** kartu.
8. Klikněte na tlačítko **Hive** v levé nabídce.
9. Klikněte na tlačítko **Upřesnit** kartu.
10. Posuňte se dolů a pak rozbalte **Advanced hive lokality**.
11. Vyhledejte **hive.metastore.client.socket.timeout** v oddílu.

Některé další ukázky k přizpůsobení další konfigurační soubory:

```xml
# hdfs-site.xml configuration
$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

# core-site.xml configuration
$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

# mapred-site.xml configuration
$MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

# oozie-site.xml configuration
$OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120
```

## <a name="use-net-sdk"></a>Použití sady .NET SDK
Zobrazit [clusterů v HDInsight pomocí sady .NET SDK se systémem Linux vytvořit](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-bootstrap).

## <a name="use-resource-manager-template"></a>Šablony Resource Manageru pomocí
V šabloně Resource Manageru můžete spuštění:

```json
"configurations": {
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

![Přizpůsobí bootstrap šablony Azure Resource Manageru clusteru HDInsight Hadoop](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

## <a name="see-also"></a>Další informace najdete v tématech
* [Vytvořte clustery systému Apache Hadoop v HDInsight] [ hdinsight-provision-cluster] pokyny o tom, jak vytvořit HDInsight cluster s použitím jiné možnosti vlastního nastavení.
* [Vývoj skriptových akcí skriptů pro HDInsight][hdinsight-write-script]
* [Nainstalovat a používat Apache Spark v clusterech HDInsight][hdinsight-install-spark]
* [Instalace a využít Apache Giraph u clusterů HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions-linux.md
[hdinsight-provision-cluster]: hdinsight-hadoop-provision-linux-clusters.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Fáze při vytváření clusteru"

## <a name="appendix-powershell-sample"></a>Dodatek: Ukázka PowerShellu

Tento skript Powershellu vytvoří HDInsight cluster a přizpůsobí nastavení Hive. Je potřeba zadat hodnoty pro `$nameToken`, `$httpPassword`, a `$sshPassword`.

> [!IMPORTANT]  
> Hodnoty pro `DefaultStorageAccount`, a `DefaultStorageContainer` nebudou zobrazeny z [Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) při [zabezpečený přenos](../storage/common/storage-require-secure-transfer.md) je povolená v účtu úložiště.

> [!WARNING]  
> Druh účtu úložiště `BlobStorage` nelze použít pro clustery HDInsight.


```powershell
####################################
# Set these variables
####################################
#region - used for creating Azure service names
$nameToken = "<ENTER AN ALIAS>" 
#endregion

#region - cluster user accounts
$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = '<ENTER A PASSWORD>' 

$sshUserName = "sshuser" #HDInsight ssh user name
$sshPassword = '<ENTER A PASSWORD>' 
#endregion

####################################
# Service names and varialbes
####################################
#region - service names
$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

$resourceGroupName = $namePrefix + "rg"
$hdinsightClusterName = $namePrefix + "hdi"
$defaultStorageAccountName = $namePrefix + "store"
$defaultBlobContainerName = $hdinsightClusterName

$location = "East US"
#endregion


####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

#endregion

#region - Create an HDInsight cluster
####################################
# Create dependent components
####################################
Write-Host "Creating a resource group ..." -ForegroundColor Green
New-AzResourceGroup `
    -Name  $resourceGroupName `
    -Location $location

Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey

New-AzStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageContext #use the cluster name as the container name

####################################
# Create a configuration object
####################################
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90"}

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

####################################
# Create an HDInsight cluster
####################################
$httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

$sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
$sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

New-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -Location $location `
    -ClusterSizeInNodes 1 `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -Config $config

####################################
# Verify the cluster
####################################
Get-AzHDInsightCluster `
    -ClusterName $hdinsightClusterName

#endregion
```
