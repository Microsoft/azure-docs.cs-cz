---
title: Přizpůsobení clusterů HDInsight pomocí bootstrap - Azure | Microsoft Docs
description: Zjistěte, jak přizpůsobit clusterů HDInsight pomocí bootstrap.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ab2ebf0c-e961-4e95-8151-9724ee22d769
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: 2fdbb8730d350023035038d60d17a5ad12c98bc0
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Přizpůsobení clusterů HDInsight pomocí Bootstrap

V některých případech je vhodné nakonfigurovat konfigurační soubory, které zahrnují:

* clusterIdentity.xml
* Core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* Storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml
* Server.Properties (kafka zprostředkovatele konfigurace)

Chcete-li použít bootstrap třemi způsoby:

* Použití Azure Powershell
* Použití sady .NET SDK
* Použití šablon Azure Resource Manageru

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

Informace o instalaci další součásti v clusteru HDInsight v době vytvoření najdete v tématu:

* [Přizpůsobení clusterů HDInsight pomocí akce skriptu (Linux)](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="use-azure-powershell"></a>Použití Azure Powershell
Následující kód PowerShell upraví konfiguraci Hive:

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }

$config = New-AzureRmHDInsightClusterConfig `
    | Set-AzureRmHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzureRmHDInsightConfigValues `
        -HiveSite $hiveConfigValues 

New-AzureRmHDInsightCluster `
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

Dokončení funkční skript prostředí PowerShell najdete v [příloha](#appendix-powershell-sample).

**Chcete-li ověřit změny:**

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. V levé nabídce klikněte na tlačítko **clustery HDInsight**. Pokud ho nevidíte, klikněte na tlačítko **všechny služby** první.
3. Klikněte na cluster, který jste právě vytvořili pomocí skriptu prostředí PowerShell.
4. Klikněte na tlačítko **řídicí panel** z horní části okna otevřete uživatelské rozhraní Ambari.
5. Klikněte na tlačítko **Hive** v levé nabídce.
6. Klikněte na tlačítko **HiveServer2** z **Souhrn**.
7. Klikněte **konfigurací** kartě.
8. Klikněte na tlačítko **Hive** v levé nabídce.
9. Klikněte **Upřesnit** kartě.
10. Posuňte se dolů a potom rozbalte **rozšířené hive lokality**.
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
Další informace najdete v tématu s názvem blog Azim Uddin [vytváření clusteru HDInsight přizpůsobení](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx).

## <a name="use-net-sdk"></a>Použití sady .NET SDK
V tématu [vytvořit systémem Linux clusterů v HDInsight pomocí sady .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-bootstrap).

## <a name="use-resource-manager-template"></a>Pomocí Správce prostředků šablony
Bootstrap můžete použít v šabloně Resource Manager:

```json
"configurations": {
    …
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

![HDInsight Hadoop přizpůsobí clusteru bootstrap šablony Azure Resource Manageru](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

## <a name="see-also"></a>Další informace najdete v tématech
* [Vytvoření clusterů systému Hadoop v HDInsight] [ hdinsight-provision-cluster] obsahuje pokyny k vytvoření clusteru HDInsight pomocí jiné možnosti vlastního nastavení.
* [Vývoj skriptů akce skriptu pro HDInsight][hdinsight-write-script]
* [Nainstalovat a používat Spark v HDInsight clustery][hdinsight-install-spark]
* [Nainstalovat a používat Solr v clusterech HDInsight](hdinsight-hadoop-solr-install.md).
* [Nainstalovat a používat Giraph v clusterech HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-hadoop-provision-linux-clusters.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Fáze při vytváření clusteru"

## <a name="appendix-powershell-sample"></a>Dodatek: Ukázkový prostředí PowerShell
Tento skript prostředí PowerShell vytvoří HDInsight cluster a přizpůsobí Hive nastavení:

```powershell
####################################
# Set these variables
####################################
#region - used for creating Azure service names
$nameToken = "<ENTER AN ALIAS>" 
#endregion

#region - cluster user accounts
$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"

$sshUserName = "sshuser" #HDInsight ssh user name
$sshPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"
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

$location = "East US 2"
#endregion

# Treat all errors as terminating
$ErrorActionPreference = "Stop"

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzureRmContext}
catch{Connect-AzureRmAccount}
#endregion

#region - Create an HDInsight cluster
####################################
# Create dependent components
####################################
Write-Host "Creating a resource group ..." -ForegroundColor Green
New-AzureRmResourceGroup `
    -Name  $resourceGroupName `
    -Location $location

Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
New-AzureRmStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -Type Standard_GRS

$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value
$defaultStorageContext = New-AzureStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey
New-AzureStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageContext #use the cluster name as the container name

####################################
# Create a configuration object
####################################
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }

$config = New-AzureRmHDInsightClusterConfig `
    | Set-AzureRmHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzureRmHDInsightConfigValues `
        -HiveSite $hiveConfigValues 

####################################
# Create an HDInsight cluster
####################################
$httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

$sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
$sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

New-AzureRmHDInsightCluster `
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
Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName

#endregion
```
