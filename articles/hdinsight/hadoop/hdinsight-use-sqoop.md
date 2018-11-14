---
title: Spouštět úlohy Apache Sqoop s Azure HDInsight (Hadoop)
description: Zjistěte, jak pomocí Azure Powershellu z pracovní stanice Sqoop import a export mezi clusterem Hadoop a službě Azure SQL database.
ms.reviewer: jasonh
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.openlocfilehash: c02fca7ba1ee5b7a93e54b4898290f0b84d88304
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51622444"
---
# <a name="use-sqoop-with-hadoop-in-hdinsight"></a>Použití nástroje Sqoop se systémem Hadoop v HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Zjistěte, jak použít Sqoop k importu a exportu mezi clusteru HDInsight a Azure SQL database nebo databáze systému SQL Server v HDInsight.

Přestože Hadoop je přirozenou volbou pro zpracování nestrukturovaných a částečně strukturovaných dat, jako jsou protokoly a soubory, může to mít také potřeba ke zpracování strukturovaných dat, která je uložená v relačních databázích.

[Sqoop] [ sqoop-user-guide-1.4.4] je nástroj určený pro přenos dat mezi clustery Hadoop a relačními databázemi. Můžete ho použít pro import dat ze systému správy relačních databází (RDBMS), jako jsou SQL Server, MySQL nebo Oracle do distribuovaného systému souborů Hadoop (HDFS), transformujte data v Hadoop MapReduce nebo Hive a pak exportovat data zpět do relační databázový systém. V tomto kurzu použijete databázi serveru SQL Server pro relační databáze.

Sqoop verze, které jsou podporovány v clusterech HDInsight najdete v tématu [co je nového ve verzích clusterů poskytovaných službou HDInsight?][hdinsight-versions]

## <a name="understand-the-scenario"></a>Vysvětlení scénáře

HDInsight cluster se dodává s ukázkovými daty. Můžete použít následující dvě ukázky:

* Souboru protokolu log4j, které se nacházejí v */example/data/sample.log*. Tyto protokoly jsou extrahovány ze souboru:
  
        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...
* Tabulky Hive s názvem *hivesampletable*, které se odkazuje na datový soubor umístěn v */hive/warehouse/hivesampletable*. Tabulka obsahuje některé data mobilních zařízení. 
  
  | Pole | Typ dat |
  | --- | --- |
  | ID klienta |řetězec |
  | querytime |řetězec |
  | na trhu |řetězec |
  | deviceplatform |řetězec |
  | devicemake |řetězec |
  | devicemodel |řetězec |
  | state |řetězec |
  | Země |řetězec |
  | querydwelltime |double |
  | ID relace |bigint |
  | sessionpagevieworder |bigint |

V tomto kurzu použijete k testování Sqoop import a export těmito dvěma datovými sadami.

## <a name="create-cluster-and-sql-database"></a>Vytvoření clusteru a SQL database
V této části se dozvíte, jak vytvořit cluster, SQL Database a SQL database schémata pro spouštění kurzu pomocí webu Azure portal a šablony Azure Resource Manageru. Šablony lze nalézt v [šablony pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/). Šablony Resource Manageru volá bacpac balíčku pro nasazení schémata tabulek do služby SQL database.  Balíček bacpac se nachází v kontejneru objektů blob veřejný, https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Pokud chcete použít u souborů bacpac kontejner privátní, použijte následující hodnoty v šabloně:
   
```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

Pokud byste radši chtěli použít Azure PowerShell k vytvoření clusteru a službu SQL Database, najdete v článku [příloha A](#appendix-a---a-powershell-sample).

> [!NOTE]
> Import pomocí šablony nebo na webu Azure portal podporuje pouze import souboru BACPAC z úložiště objektů blob v Azure.

**Konfigurace prostředí pomocí šablony resource management**
1. Kliknutím na následující obrázek otevřete šablonu Resource Manageru na webu Azure Portal.         
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
2. Zadejte následující vlastnosti:

    - **Předplatné**: Zadejte předplatné Azure.
    - **Skupina prostředků**: vytvořit novou skupinu prostředků Azure, nebo vyberte existující skupinu prostředků.  Skupina prostředků je pro účely správy.  Je kontejner pro objekty.
    - **Umístění**: Vyberte oblast.
    - **Název clusteru**: Zadejte název pro Hadoop cluster.
    - **Přihlašovací jméno a heslo clusteru**: výchozí přihlašovací jméno je admin.
    - **Uživatelské jméno a heslo SSH**.
    - **Databáze SQL serveru přihlašovací jméno a heslo**.
    - **_artifacts umístění**: použijte výchozí hodnotu, pokud chcete použít vlastní soubor backpac v jiném umístění.
    - **Sas Token umístění _artifacts**: pole ponechte prázdné.
    - **Název souboru Bacpac**: Pokud chcete použít vlastní soubor backpac použijte výchozí hodnotu.
     
        Pevně zakódované v sekci proměnných jsou následující hodnoty:
        
        |Název|Hodnota|
        |----|-----|
        | Název výchozího účtu úložiště | &lt;CluterName > ukládat |
        | Název serveru Azure SQL database | &lt;ClusterName>dbserver |
        | Název databáze SQL Azure | &lt;ClusterName>db |
     
3. Vyberte **vyjadřuji souhlas s podmínkami a ujednáními uvedenými nahoře**.
4. Klikněte na **Koupit**. Zobrazí se nová dlaždice s názvem odeslání nasazení pro šablonu nasazení. Vytvoření clusteru a databáze SQL trvá přibližně 20 minut.

Pokud se rozhodnete použít existující databázi Azure SQL nebo Microsoft SQL Server

* **Azure SQL database**: je nutné nakonfigurovat pravidlo brány firewall pro server databáze Azure SQL umožňující přístup z pracovní stanice. Pokyny týkající se vytváření databáze Azure SQL a konfiguraci brány firewall najdete v tématu [začít používat Azure SQL database][sqldatabase-get-started]. 
  
  > [!NOTE]
  > Ve výchozím nastavení umožňuje službě Azure SQL database připojení ze služeb Azure, jako je například Azure HDInsight. Pokud toto nastavení brány firewall je zakázaná, musíte ji povolit z portálu Azure portal. Pokyny týkající se vytváření databáze Azure SQL a konfigurace pravidla brány firewall naleznete v tématu [vytvoření a konfigurace služby SQL Database][sqldatabase-create-configue].
  > 
  > 
* **SQL Server**: Pokud váš cluster HDInsight je ve stejné virtuální síti v Azure jako SQL Server, můžete použít kroky v tomto článku pro import a export dat do databáze SQL serveru.
  
  > [!NOTE]
  > HDInsight podporuje jen na základě umístění virtuální sítě, a to aktuálně nefunguje s virtuální sítí založených na skupinu vztahů.
  > 
  > 
  
  * Vytvoření a konfigurace virtuální sítě najdete v tématu [vytvořit virtuální síť pomocí webu Azure portal](../../virtual-network/quick-create-portal.md).
    
    * Pokud používáte SQL Server ve vašem datovém centru, je nutné nakonfigurovat virtuální sítě jako *site-to-site* nebo *point-to-site*.
      
      > [!NOTE]
      > Pro **point-to-site** virtuální sítě, SQL Server musí běžet klienta VPN konfigurace aplikace, která je k dispozici **řídicí panel** konfigurace virtuální sítě Azure.
      > 
      > 
    * Při použití systému SQL Server na virtuálním počítači Azure, pokud virtuální počítač, který je hostitelem systému SQL Server je členem stejné virtuální síti jako HDInsight lze použít všechny konfigurace virtuální sítě.
  * Vytvoření clusteru služby HDInsight ve virtuální síti najdete v tématu [vytváření clusterů Hadoop v HDInsight pomocí vlastních možností](../hdinsight-hadoop-provision-linux-clusters.md)
    
    > [!NOTE]
    > SQL Server, musíte také povolit ověřování. Přihlašovací jméno SQL serveru musíte použít k dokončení kroků v tomto článku.
    > 
    > 

**Ověření konfigurace**

1. Otevřete skupinu prostředků na webu Azure Portal. Zobrazí se čtyři prostředky ve skupině:

    - clusteru
    - databázový server
    - databáze
    - Výchozí účet úložiště

2. Otevřete databázi Microsoft SQL Server Management Studio.  Měly by se zobrazit dvě databáze nasazení:

    ![Azure HDInsight Sqoop SQL Management Studio](./media/hdinsight-use-sqoop/hdinsight-sqoop-sql-management-studio.png)


## <a name="run-sqoop-jobs"></a>Spouštění úloh Sqoop
HDInsight můžete spouštět úlohy Sqoop pomocí různých metod. Použijte následující tabulku k rozhodnutí, která metoda je pro vás nejvhodnější a potom na odkaz pro návod.

| **Použít** Pokud chcete... | ...an **interaktivní** prostředí | ...**batch** zpracování | ...při to **clusteru operačního systému** | ...from to **klientský operační systém** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux |Linux, Unix, Mac OS X a Windows |
| [Sada .NET SDK pro Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |? |Linux nebo Windows |Windows (prozatím) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Linux nebo Windows |Windows |

## <a name="limitations"></a>Omezení
* Hromadné export - s Linuxovým systémem HDInsight, Sqoop konektor používaný k exportu dat Microsoft SQL Server nebo Azure SQL Database aktuálně nepodporuje operace hromadného vložení.
* Dávkování – s Linuxovým systémem HDInsight při použití `-batch` přepnout při provádění operace vložení, Sqoop provede několik vložení místo dávkování operace vložení.

## <a name="next-steps"></a>Další postup
Nyní jste se naučili, jak použít Sqoop. Další informace naleznete v tématu:

* [Použití Hivu se službou HDInsight](../hdinsight-use-hive.md)
* [Použití Pigu se službou HDInsight](../hdinsight-use-pig.md)
* [Nahrání dat do HDInsight][hdinsight-upload-data]: Najít další metody pro nahrávání dat do HDInsight nebo Azure Blob storage.

## <a name="appendix-a---a-powershell-sample"></a>Příloha A – ukázku prostředí PowerShell
V ukázce Powershellu provede následující kroky:

1. Připojení k Azure.
2. Vytvořte skupinu prostředků Azure. Další informace najdete v tématu [pomocí Azure Powershellu s Azure Resource Manageru](../../azure-resource-manager/powershell-azure-resource-manager.md)
3. Vytvoření serveru Azure SQL Database, Azure SQL database a dvě tabulky. 
   
    Pokud raději používáte SQL Server, použijte následující příkazy k vytvoření tabulek:
   
        CREATE TABLE [dbo].[log4jlogs](
         [t1] [nvarchar](50),
         [t2] [nvarchar](50),
         [t3] [nvarchar](50),
         [t4] [nvarchar](50),
         [t5] [nvarchar](50),
         [t6] [nvarchar](50),
         [t7] [nvarchar](50))
   
        CREATE TABLE [dbo].[mobiledata](
         [clientid] [nvarchar](50),
         [querytime] [nvarchar](50),
         [market] [nvarchar](50),
         [deviceplatform] [nvarchar](50),
         [devicemake] [nvarchar](50),
         [devicemodel] [nvarchar](50),
         [state] [nvarchar](50),
         [country] [nvarchar](50),
         [querydwelltime] [float],
         [sessionid] [bigint],
         [sessionpagevieworder][bigint])
   
    Použití sady Visual Studio je nejjednodušší způsob, jak prozkoumat databáze a tabulky. Databázový server a databáze se dají prozkoumat pomocí webu Azure portal.
4. Vytvoření clusteru HDInsight.
   
    Prozkoumat clusteru, můžete na webu Azure portal nebo Azure Powershellu.
5. Předběžně zpracovat zdrojového datového souboru.
   
    V tomto kurzu exportu souboru protokolu log4j k (soubor s oddělovači) a tabulku Hive ke službě Azure SQL database. Soubor s oddělovači nazývá */example/data/sample.log*. Dříve v tomto kurzu jste viděli několik ukázky log4j protokolů. V souboru protokolu existují některé prázdné řádky a některé řádky podobné těmto:
   
        java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
            at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)
   
    To je v pořádku pro další příklady, které tato data použít, ale My, musíte odebrat tyto výjimky jsme můžete importovat do Azure SQL database nebo SQL Server. Export Sqoopu selže, pokud je prázdný řetězec ani řádek s nižším element než je počet elementů pole definovaná v tabulce databáze Azure SQL. Tabulka log4jlogs obsahuje sedm řetězec typu pole.
   
    Tento postup vytvoří nový soubor v clusteru: tutorials/usesqoop/data/sample.log. Prohlédněte si soubor upravených dat, můžete na webu Azure portal, nástroj Průzkumník služby Azure Storage nebo Azure Powershellu. [Začínáme s HDInsight] [ hdinsight-get-started] obsahuje ukázku kódu pro použití Azure Powershellu ke stažení souboru a zobrazit obsah souboru.
6. Exportujte datového souboru do služby Azure SQL database.
   
    Zdrojový soubor se tutorials/usesqoop/data/sample.log. Tabulka, ve kterém data se exportují do se nazývá log4jlogs.
   
   > [!NOTE]
   > Kromě informací o připojovacím řetězci by měl fungovat kroky v této části pro službu Azure SQL database nebo SQL Server. Tyto kroky byly testovány s použitím následující konfigurace:
   > 
   > * **Konfigurace point-to-site virtuální síť Azure**: virtuální sítě clusteru HDInsight připojené k serveru SQL Server v privátním datacentru. Zobrazit [konfigurace Point-to-Site VPN na portálu Management Portal](../../vpn-gateway/vpn-gateway-point-to-site-create.md) Další informace.
   > * **Azure HDInsight**: viz [vytváření clusterů Hadoop v HDInsight pomocí vlastních možností](../hdinsight-hadoop-provision-linux-clusters.md) informace o vytvoření clusteru ve virtuální síti.
   > * **SQL Server 2014**: nakonfigurována pro povolení ověřování a jak spustit klienta VPN konfigurační balíček se navázat zabezpečené připojení k virtuální síti.
   > 
   > 
7. Exportujte tabulky Hive ke službě Azure SQL database.
8. Importujte tabulky mobiledata do clusteru HDInsight.
   
    Prohlédněte si soubor upravených dat, můžete na webu Azure portal, nástroj Průzkumník služby Azure Storage nebo Azure Powershellu.  [Začínáme s HDInsight] [ hdinsight-get-started] má o použití Azure Powershellu ke stažení souboru a zobrazit obsah souboru vzorového kódu.

### <a name="the-powershell-sample"></a>V ukázce Powershellu

```powershell
# Prepare an Azure SQL database to be used by the Sqoop tutorial

#region - provide the following values

$subscriptionID = "<Enter your Azure Subscription ID>"

$sqlDatabaseLogin = "<Enter a SQL Database Login name>" #SQL Database server login
$sqlDatabasePassword = "<Enter a Password>"

$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = "<Enter a Password>"

$sshUserName = "sshuser" #HDInsight ssh username
$sshPassword = $httpPassword 

# used for creating Azure service names
$nameToken = "<Enter an alias>" 
$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
#endregion

#region - variables

# Resource group variables
$resourceGroupName = $namePrefix + "rg"
$location = "East US 2" # used by all Azure services defined in this tutorial

# SQL database varialbes
$sqlDatabaseServerName = $namePrefix + "sqldbserver"
$sqlDatabaseName = $namePrefix + "sqldb"
$sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
$sqlDatabaseMaxSizeGB = 10

# Used for retrieving external IP address and creating firewall rules
$ipAddressRestService = "http://bot.whatismyipaddress.com"
$fireWallRuleName = "UseSqoop"

# Used for creating tables and clustered indexes
$cmdCreateLog4jTable = "CREATE TABLE [dbo].[log4jlogs](
    [t1] [nvarchar](50),
    [t2] [nvarchar](50),
    [t3] [nvarchar](50),
    [t4] [nvarchar](50),
    [t5] [nvarchar](50),
    [t6] [nvarchar](50),
    [t7] [nvarchar](50))"

$cmdCreateLog4jClusteredIndex = "CREATE CLUSTERED INDEX log4jlogs_clustered_index on log4jlogs(t1)"

$cmdCreateMobileTable = " CREATE TABLE [dbo].[mobiledata](
[clientid] [nvarchar](50),
[querytime] [nvarchar](50),
[market] [nvarchar](50),
[deviceplatform] [nvarchar](50),
[devicemake] [nvarchar](50),
[devicemodel] [nvarchar](50),
[state] [nvarchar](50),
[country] [nvarchar](50),
[querydwelltime] [float],
[sessionid] [bigint],
[sessionpagevieworder][bigint])"

$cmdCreateMobileDataClusteredIndex = "CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)"

# HDInsight variables
$hdinsightClusterName = $namePrefix + "hdi"
$defaultStorageAccountName = $namePrefix + "store"
$defaultBlobContainerName = $hdinsightClusterName
#endregion

# Treat all errors as terminating
$ErrorActionPreference = "Stop"

#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzureRmContext}
catch{Connect-AzureRmAccount}
#endregion

#region - Create Azure resource group
Write-Host "`nCreating an Azure resource group ..." -ForegroundColor Green
try{
    Get-AzureRmResourceGroup -Name $resourceGroupName
}
catch{
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
}
#endregion

#region - Create Azure SQL database server
Write-Host "`nCreating an Azure SQL Database server ..." -ForegroundColor Green
try{
    Get-AzureRmSqlServer -ServerName $sqlDatabaseServerName -ResourceGroupName $resourceGroupName}
catch{
    Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green

    $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)

    $sqlDatabaseServerName = (New-AzureRmSqlServer `
                                -ResourceGroupName $resourceGroupName `
                                -ServerName $sqlDatabaseServerName `
                                -SqlAdministratorCredentials $credential `
                                -Location $location).ServerName
    Write-Host "`tThe new SQL database server name is $sqlDatabaseServerName." -ForegroundColor Cyan

    Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
    $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
    New-AzureRmSqlServerFirewallRule `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -FirewallRuleName "$fireWallRuleName-workstation" `
        -StartIpAddress $workstationIPAddress `
        -EndIpAddress $workstationIPAddress

    #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. 
    #Note that this allows Azure traffic from any Azure subscription to access the server.
    New-AzureRmSqlServerFirewallRule `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -FirewallRuleName "$fireWallRuleName-Azureservices" `
        -StartIpAddress "0.0.0.0" `
        -EndIpAddress "0.0.0.0"
}

#endregion

#region - Create and validate Azure SQL database
Write-Host "`nCreating an Azure SQL database ..." -ForegroundColor Green

try {
    Get-AzureRmSqlDatabase `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -DatabaseName $sqlDatabaseName
}
catch {
    Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
    New-AzureRMSqlDatabase `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -DatabaseName $sqlDatabaseName `
        -Edition "Standard" `
        -RequestedServiceObjectiveName "S1"
}

#endregion

#region - Create tables
Write-Host "Creating the log4jlogs table and the mobiledata table ..." -ForegroundColor Green

$conn = New-Object System.Data.SqlClient.SqlConnection
$conn.ConnectionString = $sqlDatabaseConnectionString
$conn.Open()

# Create the log4jlogs table and index
$cmd = New-Object System.Data.SqlClient.SqlCommand
$cmd.Connection = $conn
$cmd.CommandText = $cmdCreateLog4jTable
$ret = $cmd.ExecuteNonQuery()
$cmd.CommandText = $cmdCreateLog4jClusteredIndex
$cmd.ExecuteNonQuery()

# Create the mobiledata table and index
$cmd.CommandText = $cmdCreateMobileTable
$cmd.ExecuteNonQuery()
$cmd.CommandText = $cmdCreateMobileDataClusteredIndex
$cmd.ExecuteNonQuery()

$conn.close()

#endregion


#region - Create HDInsight cluster

Write-Host "Creating the HDInsight cluster and the dependent services ..." -ForegroundColor Green

# Create the default storage account
New-AzureRmStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -Type Standard_LRS

# Create the default Blob container
$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value
$defaultStorageAccountContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey 
New-AzureStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageAccountContext 

# Create the HDInsight cluster
$pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

New-AzureRmHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $HDInsightClusterName `
    -Location $location `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version 3.6 `
    -ClusterSizeInNodes 2 `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultStorageContainer $defaultBlobContainerName  

# Validate the cluster
Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName
#endregion

#region - pre-process the source file

Write-Host "Preprocessing the source file ..." -ForegroundColor Green

# This procedure creates a new file with $destBlobName
$sourceBlobName = "example/data/sample.log"
$destBlobName = "tutorials/usesqoop/data/sample.log"

# Define the connection string
$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"

# Create block blob objects referencing the source and destination blob.
$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
$storageClient = $storageAccount.CreateCloudBlobClient();
$storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
$sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)

# Define a MemoryStream and a StreamReader for reading from the source file
$stream = New-Object System.IO.MemoryStream
$stream = $sourceBlob.OpenRead()
$sReader = New-Object System.IO.StreamReader($stream)

# Define a MemoryStream and a StreamWriter for writing into the destination file
$memStream = New-Object System.IO.MemoryStream
$writeStream = New-Object System.IO.StreamWriter $memStream

# Pre-process the source blob
$exString = "java.lang.Exception:"
while(-Not $sReader.EndOfStream){
    $line = $sReader.ReadLine()
    $split = $line.Split(" ")

    # remove the "java.lang.Exception" from the first element of the array
    # for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
    if ($split[0] -eq $exString){
        #create a new ArrayList to remove $split[0]
        $newArray = [System.Collections.ArrayList] $split
        $newArray.Remove($exString)

        # update $split and $line
        $split = $newArray
        $line = $newArray -join(" ")
    }

    # remove the lines that has less than 7 elements
    if ($split.count -ge 7){
        write-host $line
        $writeStream.WriteLine($line)
    }
}

# Write to the destination blob
$writeStream.Flush()
$memStream.Seek(0, "Begin")
$destBlob.UploadFromStream($memStream)

#endregion

#region - export a log file from the cluster to the SQL database

Write-Host "Preprocessing the source file ..." -ForegroundColor Green

$tableName_log4j = "log4jlogs"

# Connection string for Azure SQL Database.
# Comment if using SQL Server
$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
# Connection string for SQL Server.
# Uncomment if using SQL Server.
#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$sqlDatabaseName"

$exportDir_log4j = "/tutorials/usesqoop/data"

# Submit a Sqoop job
$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
    -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
$sqoopJob = Start-AzureRmHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef #-Debug -Verbose
Wait-AzureRmHDInsightJob `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardError
Write-Host "Standard Output" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardOutput

#endregion

#region - export a Hive table

$tableName_mobile = "mobiledata"
$exportDir_mobile = "/hive/warehouse/hivesampletable"

$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
    -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"
$sqoopJob = Start-AzureRmHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef #-Debug -Verbose

Wait-AzureRmHDInsightJob `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardError

Write-Host "Standard Output" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardOutput

#endregion

#region - import a database

$targetDir_mobile = "/tutorials/usesqoop/importeddata/"

$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
    -Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"

$sqoopJob = Start-AzureRmHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef #-Debug -Verbose

Wait-AzureRmHDInsightJob `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardError

Write-Host "Standard Output" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardOutput

#endregion
```


[azure-management-portal]: https://portal.azure.com/

[hdinsight-versions]:  ../hdinsight-component-versioning.md
[hdinsight-provision]: ../hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../../sql-database/sql-database-get-started.md
[sqldatabase-create-configue]: ../../sql-database/sql-database-get-started.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
