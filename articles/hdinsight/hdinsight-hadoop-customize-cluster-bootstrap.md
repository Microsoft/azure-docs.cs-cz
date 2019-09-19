---
title: Přizpůsobení konfigurací clusteru Azure HDInsight pomocí Bootstrap
description: Přečtěte si, jak pomocí šablon .NET, PowerShell a Správce prostředků přizpůsobit konfiguraci clusteru HDInsight prostřednictvím kódu programu.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 15d08b14e38f097e8e9c3e0db893efb1d6efe44d
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098672"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Přizpůsobení clusterů HDInsight pomocí Bootstrap

Spouštěcí skripty umožňují programově instalovat a konfigurovat komponenty v Azure HDInsight.

Existují tři přístupy k nastavení konfiguračního souboru při vytváření clusteru HDInsight:

* Použití Azure Powershell
* Použití sady .NET SDK
* Použití šablon Azure Resource Manageru

Pomocí těchto programových metod můžete například nakonfigurovat možnosti v těchto souborech:

* clusterIdentity.xml
* Core-site. XML
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred – lokalita
* Oozie-site. XML
* oozie-env.xml
* Storm-site. XML
* tez-site.xml
* webhcat-site.xml
* YARN-site. XML
* Server. Properties (Kafka-Broker Configuration)

Informace o instalaci dalších komponent v clusteru HDInsight během vytváření najdete v tématu [Přizpůsobení clusterů HDInsight pomocí akce skriptu (Linux)](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="prerequisites"></a>Požadavky

* Pokud používáte PowerShell, budete potřebovat [AZ Module](https://docs.microsoft.com/powershell/azure/overview).

## <a name="use-azure-powershell"></a>Použití Azure Powershell

Následující kód PowerShellu přizpůsobí konfiguraci [Apache Hive](https://hive.apache.org/) :

> [!IMPORTANT]  
> Parametr `Spark2Defaults` může být potřeba použít s [Add-AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue). Do parametru můžete předat prázdné hodnoty, jak je znázorněno v následujícím příkladu kódu.

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

Úplný funkční skript PowerShellu najdete v [dodatku](#appendix-powershell-sample).

**Ověření změny:**

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo klikněte na **clustery HDInsight**. Pokud ho nevidíte, klikněte na **všechny služby** jako první.
3. Klikněte na cluster, který jste právě vytvořili, pomocí skriptu PowerShellu.
4. Kliknutím na **řídicí panel** v horní části okna otevřete uživatelské rozhraní Ambari.
5. V nabídce vlevo klikněte na **podregistr** .
6. Klikněte na **HiveServer2** ze **Shrnutí**.
7. Klikněte na kartu **Konfigurace** .
8. V nabídce vlevo klikněte na **podregistr** .
9. Klikněte na tlačítko **Upřesnit** kartu.
10. Posuňte se dolů a pak rozbalte položku **pokročilý podregistr-site**.
11. V části vyhledejte **podregistr. metastore. Client. Socket. Timeout** .

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
Přečtěte si téma [Vytvoření clusterů se systémem Linux v HDInsight pomocí sady .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-bootstrap).

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

## <a name="see-also"></a>Viz také:

* [Vytváření clusterů Apache Hadoop ve službě HDInsight][hdinsight-provision-cluster] poskytuje pokyny, jak vytvořit cluster HDInsight pomocí dalších vlastních možností.
* [Vývoj skriptů akcí skriptu pro HDInsight][hdinsight-write-script]
* [Instalace a použití Apache Spark v clusterech HDInsight][hdinsight-install-spark]
* [Nainstalujte a použijte Apache Giraph v clusterech HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions-linux.md
[hdinsight-provision-cluster]: hdinsight-hadoop-provision-linux-clusters.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Fáze při vytváření clusteru"

## <a name="appendix-powershell-sample"></a>Obsažen Ukázka PowerShellu

Tento skript PowerShellu vytvoří cluster HDInsight a přizpůsobí nastavení podregistru. Nezapomeňte zadat hodnoty pro `$nameToken`, `$httpPassword`a `$sshPassword`.

> [!WARNING]  
> Druh `BlobStorage` účtu úložiště se nedá použít pro clustery HDInsight.

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
