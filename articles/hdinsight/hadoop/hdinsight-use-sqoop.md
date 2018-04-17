---
title: Spuštění úloh Apache Sqoop s Azure HDInsight (Hadoop) | Microsoft Docs
description: Další informace o použití prostředí Azure PowerShell z pracovní stanice Sqoop import a export mezi clusteru Hadoop a Azure SQL database.
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
ms.assetid: 2fdcc6b7-6ad5-4397-a30b-e7e389b66c7a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/03/2018
ms.author: jgao
ms.openlocfilehash: 2c9d708144ee10a7f55a6ffff33925e865ecd415
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="use-sqoop-with-hadoop-in-hdinsight"></a>Použití nástroje Sqoop se systémem Hadoop v HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Další informace o použití Sqoop v HDInsight k importu a exportu mezi HDInsight cluster a Azure SQL database nebo databáze systému SQL Server.

I když Hadoop je přirozené volbou pro zpracování nestrukturovaných a částečně strukturovaných dat, jako jsou protokoly a soubory, může také být potřeba zpracování strukturovaných dat, která je uložená v relačních databází.

[Sqoop] [ sqoop-user-guide-1.4.4] je nástroj sloužící k přenosu dat mezi clusterů systému Hadoop a relačními databázemi. Můžete ho pro import dat ze systému správy relačních databází (RDBMS), jako je SQL Server, MySQL a Oracle do systému souborů Hadoop distributed (HDFS), transformovat data v Hadoop pomocí MapReduce nebo Hive a poté exportujte data zpět do relační. V tomto kurzu použijete databázi systému SQL Server pro relační databázi.

Sqoop verze, které jsou podporovány v clusterech prostředí HDInsight najdete v tématu [co je nového ve verzích clusterů poskytovaných v HDInsight?][hdinsight-versions]

## <a name="understand-the-scenario"></a>Pochopit scénáře

HDInsight cluster se dodává s ukázková data. Můžete použít následující dva ukázky:

* Soubor protokolu log4j, která se nachází v */example/data/sample.log*. Tyto protokoly jsou extrahovány ze souboru:
  
        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...
* Hive tabulku s názvem *hivesampletable*, který odkazuje na datový soubor nacházející se v */hive/warehouse/hivesampletable*. Tabulka obsahuje některé data mobilních zařízení. 
  
  | Pole | Typ dat |
  | --- | --- |
  | ClientID |řetězec |
  | querytime |řetězec |
  | trh |řetězec |
  | deviceplatform |řetězec |
  | devicemake |řetězec |
  | devicemodel |řetězec |
  | state |řetězec |
  | Země |řetězec |
  | querydwelltime |Double |
  | ID relace |bigint |
  | sessionpagevieworder |bigint |

V tomto kurzu použijete k testování Sqoop import a export tyto dvě datové sady.

## <a name="create-cluster-and-sql-database"></a>Vytvoření clusteru a databáze SQL
V této části se dozvíte, jak vytvořit cluster, databáze SQL a schémata databáze SQL pro spuštění kurz pomocí portálu Azure a šablonu Azure Resource Manager. Šablony lze nalézt v [šablon Azure rychlý Start](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/). Šablony Resource Manageru volá souboru bacpac balíček pro nasazení schémata tabulek do databáze SQL.  Balíček souboru bacpac se nachází v kontejneru veřejného objektu blob https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Pokud chcete použít pro soubory souboru bacpac kontejner privátní, použijte následující hodnoty v šabloně:
   
```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

Pokud chcete používat prostředí Azure PowerShell k vytvoření clusteru a databázi SQL, najdete v části [příloha A](#appendix-a---a-powershell-sample).

> [!NOTE]
> Import pomocí šablony nebo portál Azure podporuje pouze importem souboru souboru BACPAC z Azure blob storage.

**Konfigurace prostředí pomocí šablony správy prostředků**
1. Kliknutím na následující obrázek otevřete šablonu Resource Manageru na portálu Azure.         
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
2. Zadejte následující vlastnosti:

    - **Předplatné**: Zadejte předplatné Azure.
    - **Skupina prostředků**: Vytvořte novou skupinu prostředků Azure, nebo vyberte existující skupinu prostředků.  Skupina prostředků je pro účely správy.  Je kontejner pro objekty.
    - **Umístění**: Vyberte oblast.
    - **Název clusteru**: Zadejte název pro Hadoop cluster.
    - **Přihlašovací jméno a heslo clusteru**: výchozí přihlašovací jméno je admin.
    - **Uživatelské jméno a heslo SSH**.
    - **Databáze SQL serveru přihlašovací jméno a heslo**.
    - **_artifacts umístění**: použijte výchozí hodnotu, pokud chcete používat svůj vlastní soubor backpac v jiném umístění.
    - **_artifacts umístění Sas Token**: ponechat prázdné.
    - **Název souboru souboru Bacpac**: použijte výchozí hodnotu, pokud chcete používat svůj vlastní soubor backpac.
     
        Následující hodnoty jsou pevně kódovaný v části proměnných:
        
        |Název|Hodnota|
        |----|-----|
        | Výchozí název účtu úložiště | &lt;CluterName > Uložit |
        | Název serveru databáze SQL Azure | &lt;ClusterName>dbserver |
        | Název databáze SQL Azure | &lt;ClusterName>db |
     
3. Vyberte **souhlasím s podmínkami a ujednáními výše uvedených**.
4. Klikněte na **Koupit**. Zobrazí se nová dlaždice s názvem odeslání nasazení pro šablonu nasazení. Vytvoření clusteru a databáze SQL trvá přibližně 20 minut.

Pokud chcete použít existující databázi Azure SQL nebo Microsoft SQL Server

* **Databáze SQL Azure**: musíte nakonfigurovat pravidlo brány firewall pro server databáze Azure SQL pro povolení přístupu z pracovní stanice. Pokyny týkající se vytváření databáze Azure SQL a konfiguraci brány firewall najdete v tématu [začít používat Azure SQL database][sqldatabase-get-started]. 
  
  > [!NOTE]
  > Ve výchozím nastavení Azure SQL database umožňuje připojení z Azure služby, jako je Azure HDInsight. Pokud toto nastavení brány firewall je zakázáno, musíte ji povolit z portálu Azure. Pokyny týkající se vytváření databáze Azure SQL a konfigurace pravidel brány firewall, najdete v části [vytvořit a nakonfigurovat databázi SQL][sqldatabase-create-configue].
  > 
  > 
* **SQL Server**: Pokud váš cluster HDInsight je ve stejné virtuální síti v Azure jako systém SQL Server, můžete použít kroky v tomto článku pro import a export dat k databázi systému SQL Server.
  
  > [!NOTE]
  > HDInsight podporuje pouze na základě umístění virtuální sítě a aktuálně nefunguje s virtuálních sítích založených na skupinu vztahů.
  > 
  > 
  
  * Vytvoření a konfigurace virtuální sítě najdete v tématu [vytvoření virtuální sítě pomocí portálu Azure](../../virtual-network/quick-create-portal.md).
    
    * Pokud používáte systém SQL Server ve vašem datovém centru, je nutné nakonfigurovat virtuální síti jako *site-to-site* nebo *point-to-site*.
      
      > [!NOTE]
      > Pro **point-to-site** virtuální sítě, SQL Server musí používat klienta VPN konfigurace aplikace, která je k dispozici z **řídicí panel** konfigurace virtuální sítě Azure.
      > 
      > 
    * Při použití systému SQL Server na virtuální počítač Azure, lze použít žádnou konfiguraci virtuální sítě, pokud je virtuální počítač, který je hostitelem SQL serveru členem stejné virtuální síti jako HDInsight.
  * Vytvoření clusteru HDInsight ve virtuální síti naleznete v tématu [vytvoření Hadoop clusterů v HDInsight pomocí vlastních možností](../hdinsight-hadoop-provision-linux-clusters.md)
    
    > [!NOTE]
    > SQL Server musíte také povolit ověřování. Pokud chcete provést kroky v tomto článku, je nutné použít přihlášení systému SQL Server.
    > 
    > 

**Ověření konfigurace**

1. Otevřete skupinu prostředků na portálu Azure. Zobrazí čtyři prostředky ve skupině:

    - clusteru
    - databázový server
    - databáze
    - Výchozí účet úložiště

2. Otevření databáze Microsoft SQL Server Management Studio.  Měli byste vidět dvě databáze nasazení:

    ![Azure HDInsight Sqoop SQL Management Studio](./media/hdinsight-use-sqoop/hdinsight-sqoop-sql-management-studio.png)


## <a name="run-sqoop-jobs"></a>Spuštění úloh Sqoop
HDInsight Sqoop úlohy můžete spustit pomocí různých metod. Následující tabulku použijte k rozhodování, jakou metodu je pro vás nejvhodnější a potom klepněte na odkaz návod.

| **Použít** Pokud chcete... | ...an **interaktivní** prostředí | ...**batch** zpracování | ...při to **clusteru operačního systému** | ...from to **klientský operační systém** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X nebo systému Windows |
| [Sada .NET SDK pro Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |✔ |Linux nebo Windows |Windows (prozatím) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |✔ |Linux nebo Windows |Windows |

## <a name="limitations"></a>Omezení
* Hromadné export - s Linuxovým systémem HDInsight, Sqoop konektor umožňuje exportovat data do systému Microsoft SQL Server nebo Azure SQL Database v současné době nepodporuje hromadné vložení.
* Dávkování - s HDInsight se systémem Linux, při použití `-batch` přepnout při vložení, Sqoop provádí více vloží místo dávkování operace insert.

## <a name="next-steps"></a>Další postup
Nyní jste se naučili postup použití nástroje Sqoop. Další informace naleznete v tématu:

* [Použití Hivu se službou HDInsight](../hdinsight-use-hive.md)
* [Použití Pigu se službou HDInsight](../hdinsight-use-pig.md)
* [Nahrání dat do HDInsight][hdinsight-upload-data]: Najít další metody pro odesílání dat do HDInsight nebo Azure Blob storage.

## <a name="appendix-a---a-powershell-sample"></a>Příloha A - ukázku prostředí PowerShell
Ukázku v prostředí PowerShell provede následující kroky:

1. Připojte k Azure.
2. Vytvořte skupinu prostředků Azure. Další informace najdete v tématu [použití Azure Powershellu s Azure Resource Manager](../../azure-resource-manager/powershell-azure-resource-manager.md)
3. Vytvoření serveru Azure SQL Database, Azure SQL database a dvě tabulky. 
   
    Pokud místo toho používat SQL Server, použijte následující příkazy k vytvoření tabulky:
   
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
   
    Nejjednodušší způsob, jak podívejte se na databáze a tabulky je pomocí sady Visual Studio. Databázový server a databáze může být prověřen pomocí portálu Azure.
4. Vytvoření clusteru HDInsight.
   
    K prozkoumání clusteru, můžete portál Azure nebo Azure PowerShell.
5. Předběžně zpracovat zdrojového datového souboru.
   
    V tomto kurzu můžete exportovat soubor protokolu log4j (soubor s oddělovači) a tabulku Hive do Azure SQL database. Souboru s oddělovači se nazývá */example/data/sample.log*. V tomto kurzu jste viděli několik ukázky log4j protokolů. V souboru protokolu existují některé prázdné řádky a některé řádky vypadat přibližně takto:
   
        java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
            at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)
   
    To je v pořádku pro další příklady, které používají tato data, ale před jsme můžete importovat do Azure SQL database nebo SQL Server jsme musíte odebrat tyto výjimky. Sqoop export se nezdaří, pokud je prázdný řetězec nebo čáry s méně element než počet elementů pole definovaná v tabulce databáze Azure SQL. Tabulka log4jlogs má sedm řetězec typu pole.
   
    Tento postup vytvoří nový soubor v clusteru: tutorials/usesqoop/data/sample.log. Pro zjištění změny datového souboru, můžete portál Azure, nástroji Průzkumník Azure Storage nebo Azure PowerShell. [Začínáme s HDInsight] [ hdinsight-get-started] má ukázka kódu pro použití Azure PowerShell k stažení souboru a zobrazit obsah souboru.
6. Exportujte soubor dat databáze Azure SQL.
   
    Zdrojový soubor je tutorials/usesqoop/data/sample.log. V tabulce, kde se data se exportují do nazývá log4jlogs.
   
   > [!NOTE]
   > Než informace o připojovacím řetězci by měl pracovní postup v této části pro Azure SQL database nebo SQL Server. Tyto kroky testovali pomocí následující konfigurace:
   > 
   > * **Konfigurace point-to-site virtuální síť Azure**: virtuální sítě připojen k serveru SQL Server v privátním datacentru clusteru HDInsight. V tématu [konfigurace VPN typu Point-to-Site v portálu pro správu](../../vpn-gateway/vpn-gateway-point-to-site-create.md) Další informace.
   > * **Azure HDInsight**: najdete v části [vytvoření Hadoop clusterů v HDInsight pomocí vlastních možností](../hdinsight-hadoop-provision-linux-clusters.md) informace o vytváření clusteru s podporou ve virtuální síti.
   > * **SQL Server 2014**: nakonfigurovaná tak, aby povolit ověřování a spouštění klienta VPN konfigurační balíček se bezpečně připojit k virtuální síti.
   > 
   > 
7. Exportujte tabulky Hive k databázi Azure SQL.
8. Importujte tabulky mobiledata ke clusteru HDInsight.
   
    Pro zjištění změny datového souboru, můžete portál Azure, nástroji Průzkumník Azure Storage nebo Azure PowerShell.  [Začínáme s HDInsight] [ hdinsight-get-started] má ukázka kódu o použití prostředí Azure PowerShell k stažení souboru a zobrazit obsah souboru.

### <a name="the-powershell-sample"></a>Ukázku v prostředí PowerShell

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
catch{Login-AzureRmAccount}
#endregion

#region - Create Azure resouce group
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
