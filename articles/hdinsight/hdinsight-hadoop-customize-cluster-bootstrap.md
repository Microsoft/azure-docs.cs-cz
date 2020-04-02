---
title: Přizpůsobení konfigurací clusteru Azure HDInsight pomocí zaváděcí pasti
description: Zjistěte, jak programově přizpůsobit konfiguraci clusteru HDInsight pomocí šablon .NET, PowerShell a Resource Manager.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/01/2020
ms.openlocfilehash: 796dbc53d1adf310028e06dea319b9a60d5cf54b
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529351"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Přizpůsobení clusterů HDInsight pomocí Bootstrapu

Skripty bootstrapu umožňují programově instalovat a konfigurovat součásti v Azure HDInsight.

Existují tři přístupy k nastavení konfiguračního souboru při vytváření clusteru HDInsight:

* Použití Azure Powershell
* Použití sady .NET SDK
* Použití šablony Azure Resource Manager

Pomocí těchto programových metod můžete například konfigurovat možnosti v těchto souborech:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml
* server.properties (konfigurace kafka-broker)

Informace o instalaci dalších součástí v clusteru HDInsight během doby vytváření naleznete v [tématu Customize HDInsight clusters using Script Action (Linux).](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="prerequisites"></a>Požadavky

* Pokud používáte Prostředí PowerShell, budete potřebovat [modul Az](https://docs.microsoft.com/powershell/azure/overview).

## <a name="use-azure-powershell"></a>Použití Azure Powershell

Následující kód Prostředí PowerShell přizpůsobuje konfiguraci [Apache Hive:](https://hive.apache.org/)

> [!IMPORTANT]  
> Parametr `Spark2Defaults` může být nutné použít s [Add-AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue). Do parametru můžete předat prázdné hodnoty, jak je znázorněno v příkladu kódu níže.

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90s" }

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

Kompletní pracovní PowerShell skript lze nalézt v [dodatku](#appendix-powershell-sample).

**Změna ověřujete takto:**

1. Přejděte `https://CLUSTERNAME.azurehdinsight.net/` `CLUSTERNAME` na místo, kde je název clusteru.
1. V levé nabídce přejděte na **hive** > **configs** > **Advanced**.
1. Rozbalte **rozšířený podregistrový web**.
1. Vyhledejte **hive.metastore.client.socket.timeout** a potvrďte, že hodnota je **90s**.

Některé další ukázky přizpůsobení jiných konfiguračních souborů:

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

Viz [Azure HDInsight SDK pro .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet).

## <a name="use-resource-manager-template"></a>Použití šablony Resource Manageru

Zaváděcí past můžete použít v šabloně Správce prostředků:

```json
"configurations": {
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

![Hadoop přizpůsobuje zaváděcí šablonu Azure Resource Manageru clusteru](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

Ukázkový fragment šablony Správce prostředků pro přepnutí konfigurace ve výchozím nastavení spark2, aby se pravidelně čistily protokoly událostí z úložiště.  

```json
"configurations": {
    "spark2-defaults": {
        "spark.history.fs.cleaner.enabled": "true",
        "spark.history.fs.cleaner.interval": "7d",
        "spark.history.fs.cleaner.maxAge": "90d"
    }
}
```

## <a name="see-also"></a>Viz také

* [Vytvoření clusterů Apache Hadoop v HDInsight](hdinsight-hadoop-provision-linux-clusters.md) poskytuje pokyny, jak vytvořit cluster HDInsight pomocí jiných vlastních možností.
* [Vývoj skriptů pro HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Instalace a používání Apache Spark v clusterech HDInsight](spark/apache-spark-jupyter-spark-sql-use-portal.md)
* [Nainstalujte a používejte Apache Giraph na clusterech HDInsight](hdinsight-hadoop-giraph-install.md).

## <a name="appendix-powershell-sample"></a>Dodatek: Ukázka prostředí PowerShell

Tento skript prostředí PowerShell vytvoří cluster HDInsight a přizpůsobí nastavení Hive. Nezapomeňte zadat hodnoty `$nameToken`pro `$httpPassword`, `$sshPassword`a .

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

# Note: Storage account kind BlobStorage cannot be used as primary storage.

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
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90s"}

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
