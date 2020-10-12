---
title: Přizpůsobení konfigurací clusteru Azure HDInsight pomocí Bootstrap
description: Přečtěte si, jak pomocí šablon .NET, PowerShell a Správce prostředků přizpůsobit konfiguraci clusteru HDInsight prostřednictvím kódu programu.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/01/2020
ms.openlocfilehash: eaddae04c198742007947831046139d80828534b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87006581"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Přizpůsobení clusterů HDInsight pomocí Bootstrap

Spouštěcí skripty umožňují programově instalovat a konfigurovat komponenty v Azure HDInsight.

Existují tři přístupy k nastavení konfiguračního souboru při vytváření clusteru HDInsight:

* Použití Azure Powershell
* Použití sady .NET SDK
* Použití šablony Azure Resource Manager

Pomocí těchto programových metod můžete například nakonfigurovat možnosti v těchto souborech:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred – lokalita
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml
* Server. Properties (Kafka-Broker Configuration)

Informace o instalaci dalších komponent v clusteru HDInsight během vytváření najdete v tématu [Přizpůsobení clusterů HDInsight pomocí akce skriptu (Linux)](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="prerequisites"></a>Požadavky

* Pokud používáte PowerShell, budete potřebovat [AZ Module](https://docs.microsoft.com/powershell/azure/).

## <a name="use-azure-powershell"></a>Použití Azure Powershell

Následující kód PowerShellu přizpůsobí konfiguraci [Apache Hive](https://hive.apache.org/) :

> [!IMPORTANT]  
> Parametr `Spark2Defaults` může být potřeba použít s [Add-AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue). Do parametru můžete předat prázdné hodnoty, jak je znázorněno v následujícím příkladu kódu.

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

Úplný funkční skript PowerShellu najdete v [dodatku](#appendix-powershell-sample).

**Ověření změny:**

1. Přejděte na `https://CLUSTERNAME.azurehdinsight.net/` místo, kde `CLUSTERNAME` je název vašeho clusteru.
1. V nabídce vlevo přejděte na konfigurace **podregistru**  >  **Configs**  >  **Upřesnit**.
1. Rozbalte položku **pokročilý podregistr – lokalita**.
1. Vyhledejte **podregistr. metastore. Client. Socket. Timeout** a potvrďte, že hodnota je **90**.

Další ukázky přizpůsobení dalších konfiguračních souborů:

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

Můžete použít Bootstrap v šabloně Správce prostředků:

```json
"configurations": {
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

![Hadoop přizpůsobí šablonu Azure Resource Manager Bootstrap clusteru.](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

Ukázka fragmentu šablony Správce prostředků pro přepínání konfigurace v spark2 – ve výchozím nastavení se pravidelně čistí protokoly událostí ze služby Storage.  

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

* [Vytváření clusterů Apache Hadoop ve službě HDInsight](hdinsight-hadoop-provision-linux-clusters.md) poskytuje pokyny, jak vytvořit cluster HDInsight pomocí dalších vlastních možností.
* [Vývoj skriptů akcí skriptu pro HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Instalace a použití Apache Spark v clusterech HDInsight](spark/apache-spark-jupyter-spark-sql-use-portal.md)
* [Nainstalujte a použijte Apache Giraph v clusterech HDInsight](hdinsight-hadoop-giraph-install.md).

## <a name="appendix-powershell-sample"></a>Příloha: Ukázka PowerShellu

Tento skript PowerShellu vytvoří cluster HDInsight a přizpůsobí nastavení podregistru. Nezapomeňte zadat hodnoty pro `$nameToken` , `$httpPassword` a `$sshPassword` .

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
