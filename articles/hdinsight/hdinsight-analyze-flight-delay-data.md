---
title: Analýza dat zpoždění letu s Hadoop v HDInsight - Azure | Microsoft Docs
description: Naučte se používat jeden skript prostředí Windows PowerShell k vytvoření clusteru HDInsight, spouštět úlohy Hive, Sqoop úlohu spusťte a odstranění clusteru.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 00e26aa9-82fb-4dbe-b87d-ffe8e39a5412
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: eec5d0eb3c9cb0ae6e3e7f4eadfc58c4ab039cfd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="analyze-flight-delay-data-by-using-hive-in-hdinsight"></a>Analýza dat zpoždění letu pomocí Hive v HDInsight
Hive zajišťuje spuštěných úloh Hadoop MapReduce prostřednictvím SQL jako skriptovacího jazyka nazvaného  *[HiveQL][hadoop-hiveql]*, který je možné použít ke shrnutí, dotazování, a analýze velkých objemů dat.

> [!IMPORTANT]
> Kroky v tomto dokumentu vyžadují cluster HDInsight se systémem Windows. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Kroky, které pracují s clusterem se systémem Linux najdete v tématu [analyzovat data zpoždění letu pomocí Hive v HDInsight (Linux)](hdinsight-analyze-flight-delay-data-linux.md).

Jeden z největších výhod Azure HDInsight je oddělení dat úložiště a výpočty. HDInsight používá úložiště objektů Blob v Azure pro úložiště dat. Typické úlohy sestává ze tří částí:

1. **Ukládání dat v úložišti objektů Blob Azure.**  Například informace o počasí dat, data snímačů, webových protokolů a v takovém případě letu zpoždění data se uloží do úložiště objektů Blob v Azure.
2. **Spuštění úlohy.** Pokud bude čas zpracování dat, spusťte skript prostředí Windows PowerShell (nebo klientské aplikace) k vytvoření clusteru HDInsight, spouštění úloh a odstranění clusteru. Úlohy uložit výstupní data do úložiště objektů Blob v Azure. Výstupní data se uchovávají i po odstranění clusteru. Tímto způsobem platíte jenom to, na co mají použít.
3. **Načíst výstup z Azure Blob storage**, nebo v tomto kurzu exportovat data do Azure SQL database.

Následující diagram znázorňuje tento scénář a struktura v tomto kurzu:

![HDI. FlightDelays.flow][img-hdi-flightdelays-flow]

Všimněte si, že čísla v diagramu odpovídají názvů oddílů. **M** znamená hlavní proces. **A** znamená pro obsah v příloze.

Hlavní část kurzu se dozvíte, jak používat jeden skript prostředí Windows PowerShell k provádění následujících úloh:

* Vytvoření clusteru HDInsight.
* Spuštění úlohy Hive v clusteru pro výpočet průměrné zpoždění na letištích. Data pohybující se zpoždění uložený v účtu úložiště objektů Blob v Azure.
* Spustíte úlohu Sqoop výstup úlohy Hive export do Azure SQL database.
* Odstranění clusteru HDInsight.

V dodatky najdete pokyny k nahrávání letu zpoždění dat, vytváření nebo odeslání řetězec dotazu Hive a příprava úlohy Sqoop Azure SQL database.

> [!NOTE]
> Kroky v tomto dokumentu jsou specifická pro clustery HDInsight se systémem Windows. Kroky, které pracují s clusterem se systémem Linux najdete v tématu [analyzovat data zpoždění letu používání Hive v HDInsight (Linux)](hdinsight-analyze-flight-delay-data-linux.md)

### <a name="prerequisites"></a>Požadavky
Před zahájením tohoto kurzu musíte mít tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Pracovní stanice s prostředím Azure PowerShell**.

    > [!IMPORTANT]
    > Podpora prostředí Azure PowerShell pro správu prostředků služby HDInsight pomocí Azure Service Manageru je **zastaralá** a k 1. lednu 2017 jsme ji odebrali. Kroky v tomto dokumentu používají nové rutiny služby HDInsight, které pracují s Azure Resource Managerem.
    >
    > Podle postupu v tématu [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azureps-cmdlets-docs) si nainstalujte nejnovější verzi prostředí Azure PowerShell. Pokud máte skripty, které je potřeba upravit tak, aby používaly nové rutiny, které pracují s nástrojem Azure Resource Manager, najdete další informace v tématu [Migrace na vývojové nástroje založené na Azure Resource Manageru pro clustery služby HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md).

**Soubory používané v tomto kurzu**

Tento kurz používá na časový výkon letecká společnost letu data z [výzkum a inovativní technologie správy, úřad Transport statistických nebo RITĚ][rita-website].
Kopírování dat byl odeslán do kontejner úložiště objektů Blob v Azure s oprávněním přístup veřejného objektu Blob.
Součástí vašeho skriptu prostředí PowerShell zkopíruje data z kontejneru veřejného objektu blob na výchozí kontejner objektu blob ve vašem clusteru. Skript HiveQL je také zkopírován do stejné kontejneru objektů Blob.
Pokud chcete zjistit, jak get/nahrávání dat do účtu úložiště a jak vytvořit nebo nahráváte soubor skriptu HiveQL, najdete v článku [příloha A](#appendix-a) a [příloha B](#appendix-b).

Následující tabulka uvádí soubory používané v tomto kurzu:

<table border="1">
<tr><th>Soubory</th><th>Popis</th></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql</td><td>Soubor skriptu HiveQL používané úlohy Hive. Tento skript byl odeslán do účtu úložiště objektů Blob v Azure s veřejný přístup. <a href="#appendix-b">Dodatek B</a> obsahuje pokyny k přípravě a pak tento soubor nahrát na svůj vlastní účet úložiště objektů Blob v Azure.</td></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data</td><td>Vstupní data pro úlohy Hive. Data byla uložena do účtu úložiště objektů Blob v Azure s veřejný přístup. <a href="#appendix-a">Příloha A</a> obsahuje informace o získání dat a odesílání dat na svůj vlastní účet úložiště objektů Blob v Azure.</td></tr>
<tr><td>\tutorials\flightdelays\output</td><td>Výstupní cesta pro úlohy Hive. Výchozí kontejner se používá pro ukládání výstupní data.</td></tr>
<tr><td>\tutorials\flightdelays\jobstatus</td><td>Hive složka stav úlohy na výchozí kontejner.</td></tr>
</table>

## <a name="create-cluster-and-run-hivesqoop-jobs"></a>Vytvoření clusteru a spouštění úloh Hive nebo Sqoop
Hadoop MapReduce je dávkové zpracování. Nákladově nejefektivnější způsob spuštění úlohy Hive je vytvoření clusteru s podporou pro úlohu a odstranit tuto úlohu po dokončení úlohy. Následující skript obsahuje celého procesu.
Další informace o vytváření clusteru služby HDInsight a spuštění úloh Hive naleznete v tématu [vytvoření Hadoop clusterů v HDInsight] [ hdinsight-provision] a [používání Hive s HDInsight] [hdinsight-use-hive].

**Ke spouštění dotazů Hive pomocí prostředí Azure PowerShell**

1. Vytvoření databáze Azure SQL a v tabulce pro výstup úlohy Sqoop pomocí pokynů uvedených v [příloha C](#appendix-c).
2. Otevřete Windows PowerShell ISE a spusťte následující skript:

    ```powershell
    $subscriptionID = "<Azure Subscription ID>"
    $nameToken = "<Enter an Alias>"

    ###########################################
    # You must configure the follwing variables
    # for an existing Azure SQL Database
    ###########################################
    $existingSqlDatabaseServerName = "<Azure SQL Database Server>"
    $existingSqlDatabaseLogin = "<Azure SQL Database Server Login>"
    $existingSqlDatabasePassword = "<Azure SQL Database Server login password>"
    $existingSqlDatabaseName = "<Azure SQL Database name>"

    $localFolder = "E:\Tutorials\Downloads\" # A temp location for copying files.
    $azcopyPath = "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy" # depends on the version, the folder can be different

    ###########################################
    # (Optional) configure the following variables
    ###########################################

    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $location = "EAST US 2"

    $HDInsightClusterName = $namePrefix + "hdi"
    $httpUserName = "admin"
    $httpPassword = "<Enter the Password>"

    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $HDInsightClusterName # use the cluster name

    $existingSqlDatabaseTableName = "AvgDelays"
    $sqlDatabaseConnectionString = "jdbc:sqlserver://$existingSqlDatabaseServerName.database.windows.net;user=$existingSqlDatabaseLogin@$existingSqlDatabaseServerName;password=$existingSqlDatabaseLogin;database=$existingSqlDatabaseName"

    $hqlScriptFile = "/tutorials/flightdelays/flightdelays.hql"

    $jobStatusFolder = "/tutorials/flightdelays/jobstatus"

    ###########################################
    # Login
    ###########################################
    try{
        $acct = Get-AzureRmSubscription
    }
    catch{
        Connect-AzureRmAccount
    }
    Select-AzureRmSubscription -SubscriptionID $subscriptionID

    ###########################################
    # Create a new HDInsight cluster
    ###########################################

    # Create ARM group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create the default storage account
    New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName -Location $location -Type Standard_LRS

    # Create the default Blob container
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer -Name $defaultBlobContainerName -Context $defaultStorageAccountContext

    # Create the HDInsight cluster
    $pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $HDInsightClusterName `
        -Location $location `
        -ClusterType Hadoop `
        -OSType Windows `
        -ClusterSizeInNodes 2 `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $existingDefaultBlobContainerName

    ###########################################
    # Prepare the HiveQL script and source data
    ###########################################

    # Create the temp location
    New-Item -Path $localFolder -ItemType Directory -Force

    # Download the sample file from Azure Blob storage
    $context = New-AzureStorageContext -StorageAccountName "hditutorialdata" -Anonymous
    $blobs = Get-AzureStorageBlob -Container "flightdelay" -Context $context
    #$blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

    # Upload data to default container

    $azcopycmd = "cmd.exe /C '$azcopyPath\azcopy.exe' /S /Source:'$localFolder' /Dest:'https://$defaultStorageAccountName.blob.core.windows.net/$defaultBlobContainerName/tutorials/flightdelays' /DestKey:$defaultStorageAccountKey"

    Invoke-Expression -Command:$azcopycmd

    ###########################################
    # Submit the Hive job
    ###########################################
    Use-AzureRmHDInsightCluster -ClusterName $HDInsightClusterName -HttpCredential $httpCredential
    $response = Invoke-AzureRmHDInsightHiveJob `
                    -Files $hqlScriptFile `
                    -DefaultContainer $defaultBlobContainerName `
                    -DefaultStorageAccountName $defaultStorageAccountName `
                    -DefaultStorageAccountKey $defaultStorageAccountKey `
                    -StatusFolder $jobStatusFolder

    write-Host $response

    ###########################################
    # Submit the Sqoop job
    ###########################################
    $exportDir = "wasb://$defaultBlobContainerName@$defaultStorageAccountName.blob.core.windows.net/tutorials/flightdelays/output"

    $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
                    -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "
    $sqoopJob = Start-AzureRmHDInsightJob `
                    -ResourceGroupName $resourceGroupName `
                    -ClusterName $hdinsightClusterName `
                    -HttpCredential $httpCredential `
                    -JobDefinition $sqoopDef #-Debug -Verbose

    Wait-AzureRmHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $HDInsightClusterName `
        -HttpCredential $httpCredential `
        -WaitTimeoutInSeconds 3600 `
        -Job $sqoopJob.JobId

    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -DefaultContainer $existingDefaultBlobContainerName `
        -DefaultStorageAccountName $defaultStorageAccountName `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardError

    ###########################################
    # Delete the cluster
    ###########################################
    Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName
    ```
3. Připojení k vaší databázi SQL a najdete v části zpoždění letů průměrná podle města v tabulce AvgDelays:

    ![HDI. FlightDelays.AvgDelays.Dataset][image-hdi-flightdelays-avgdelays-dataset]

- - -

## <a id="appendix-a"></a>Příloha A - nahrávání letu zpoždění data do úložiště objektů Blob v Azure
Odesílání datového souboru a soubory skript HiveQL (viz [příloha B](#appendix-b)) vyžaduje některé plánování. Cílem je, a ukládat soubory dat a soubor HiveQL předtím, než vytváření clusteru služby HDInsight a spuštění úlohy Hive. Máte dvě možnosti:

* **Použijte stejný účet úložiště Azure, který se použije jako výchozí systém souborů v clusteru HDInsight.** Protože přístupový klíč účtu úložiště bude mít HDInsight cluster, nemusíte provádět žádné další změny.
* **Použijte jiný účet úložiště Azure z clusteru HDInsight výchozí systém souborů.** Pokud je to tento případ, musíte upravit vytvoření součástí prostředí Windows PowerShell skriptu v nalezen [clusteru HDInsight se vytvoření a spuštění úlohy Hive nebo Sqoop](#runjob) propojení účtu úložiště jako další účet úložiště. Pokyny najdete v tématu [vytvoření Hadoop clusterů v HDInsight][hdinsight-provision]. HDInsight cluster pak zná přístupový klíč pro účet úložiště.

> [!NOTE]
> Cesta k úložišti objektů Blob pro datový soubor je pevný programového v souboru skriptu HiveQL. Je třeba jej aktualizovat odpovídajícím způsobem.

**Ke stahování dat letu**

1. Přejděte do [výzkum a inovativní technologie správy, statistický úřad Transport][rita-website].
2. Na stránce vyberte následující hodnoty:

    <table border="1">
    <tr><th>Název</th><th>Hodnota</th></tr>
    <tr><td>Filtr roku</td><td>2013 </td></tr>
    <tr><td>Filtrovat období</td><td>Leden</td></tr>
    <tr><td>Pole</td><td>*Rok*, *FlightDate*, *UniqueCarrier*, *poskytovatel*, *FlightNum*, *OriginAirportID*, *Původu*, *OriginCityName*, *OriginState*, *DestAirportID*, *cíle*, *DestCityName*, *DestState*, *DepDelayMinutes*, *ArrDelay*,  *ArrDelayMinutes*, *CarrierDelay*, *WeatherDelay*, *NASDelay*, *SecurityDelay*,  *LateAircraftDelay* (zrušte zaškrtnutí všech ostatních polí)</td></tr>
    </table>

3. Klikněte na **Stáhnout**.
4. Rozbalte soubor **C:\Tutorials\FlightDelay\2013Data** složky. Každý soubor je soubor CSV a je přibližně 60GB.
5. Přejmenujte soubor na název v měsíci, který obsahuje data. Například by s názvem souboru, který obsahuje data leden *January.csv*.
6. Opakujte kroky 2 a 5 na stažení souboru pro každou dobu 12 měsíců v 2013. Budete potřebovat minimálně jeden soubor ke spuštění tohoto kurzu.

**Odeslání dat zpoždění letu do úložiště objektů Blob v Azure**

1. Připravte parametry:

    <table border="1">
    <tr><th>Název proměnné</th><th>Poznámky</th></tr>
    <tr><td>$storageAccountName</td><td>Kam chcete nahrát data do účtu úložiště Azure.</td></tr>
    <tr><td>$blobContainerName</td><td>Kam chcete nahrát data do kontejneru objektů Blob.</td></tr>
    </table>
    
2. Otevřete Azure PowerShell ISE.
3. Vložte následující skript do podokna skriptu:

    ```powershell
    [CmdletBinding()]
    Param(

        [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
        [String]$storageAccountName,

        [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
        [String]$blobContainerName
    )

    #Region - Variables
    $localFolder = "C:\Tutorials\FlightDelay\2013Data"  # The source folder
    $destFolder = "tutorials/flightdelay/2013data"     #The blob name prefix for the files to be uploaded
    #EndRegion

    #Region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Connect-AzureRmAccount}
    #EndRegion

    #Region - Validate user input
    Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
    # Validate the Storage account
    if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
    {
        Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
        exit
    }
    else{
        $resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
    }

    # Validate the container
    $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

    if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
    {
        Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
        Exit
    }
    #EngRegion

    #Region - Copy the file from local workstation to Azure Blob storage
    if (test-path -Path $localFolder)
    {
        foreach ($item in Get-ChildItem -Path $localFolder){
            $fileName = "$localFolder\$item"
            $blobName = "$destFolder/$item"

            Write-Host "Copying $fileName to $blobName" -ForegroundColor Green

            Set-AzureStorageBlobContent -File $fileName -Container $blobContainerName -Blob $blobName -Context $storageContext
        }
    }
    else
    {
        Write-Host "The source folder on the workstation doesn't exist" -ForegroundColor Red
    }

    # List the uploaded files on HDInsight
    Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
    #EndRegion
    ```
4. Stisknutím klávesy **F5** spusťte skript.

Pokud chcete použít jinou metodu pro nahrávání souborů, Zkontrolujte prosím, že je cesta k souboru kurzy/flightdelay nebo data. Syntaxe pro přístup k souborům je:

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelay/data

Cesta flightdelay/kurzy nebo dat je virtuální složky, kterou jste vytvořili, když jste odeslali soubory. Ověřte, jestli jsou 12 soubory, jeden pro každý měsíc.

> [!NOTE]
> Je třeba aktualizovat dotaz Hive ke čtení z nového umístění.
>
> Buď je nutné nakonfigurovat oprávnění ke kontejneru přístupu veřejné nebo vytvořit vazbu na účet úložiště na clusteru HDInsight. Řetězec dotazu Hive, jinak nebudou mít přístup k datové soubory.

- - -

## <a id="appendix-b"></a>Dodatek B – vytvořit a odeslat skript HiveQL
Pomocí Azure PowerShell, můžete současně spustit více příkazy HiveQL jeden nebo balíček příkaz HiveQL do souboru skriptu. V této části se dozvíte, jak vytvořit skript HiveQL a odeslat skript do úložiště objektů Blob v Azure pomocí Azure PowerShell. Hive vyžaduje HiveQL skriptů k uložení do úložiště objektů Blob v Azure.

Skript HiveQL provede následující:

1. **Odpojit tabulku delays_raw**, v případě, že v tabulce již existuje.
2. **Vytvoří externí tabulku Hive delays_raw** odkazující na umístění úložiště objektů Blob v souborech zpoždění letu. Tento dotaz Určuje, že pole jsou oddělená "," a že řádky se ukončila příkazem "\n". To představuje problém, když hodnoty polí obsahovat čárky, protože podregistr nelze rozlišit mezi čárkami, který je oddělovačem polí a ten, který je součástí hodnotu pole (což je případ hodnoty v polích pro POČÁTEK\_MĚSTA\_název a cíl\_ MĚSTA\_název). Chcete-li to vyřešit, vytvoří dotaz dočasné sloupce k ukládání dat, která je nesprávně rozdělená do sloupce.
3. **Odpojit tabulku zpoždění**, v případě, že v tabulce již existuje.
4. **Vytvoření tabulky zpoždění**. Je vhodné vyčistit data před další zpracování. Tento dotaz vytvoří novou tabulku, *zpoždění*, z tabulky delays_raw. Všimněte si, že nejsou zkopírovány dočasné sloupce (jak je uvedeno nahoře) a že **substring** funkce slouží k odebrání dat uvozovky.
5. **Výpočetní průměrná počasí zpoždění a skupiny výsledky podle název města.** Je také výstup výsledků do úložiště objektů Blob. Poznámka: aby dotaz dojde k odebrání apostrofy z dat a vyloučí řádků, kde hodnota **weather_delay** má hodnotu null. To je nezbytné, protože Sqoop, použít později v tomto kurzu, nemůže pracovat s těmito hodnotami řádně ve výchozím nastavení.

Úplný seznam příkazy HiveQL, najdete v části [Hive Data Definition Language][hadoop-hiveql]. Každý příkaz HiveQL musí ukončit středníkem.

**K vytvoření souboru skriptu HiveQL**

1. Připravte parametry:

    <table border="1">
    <tr><th>Název proměnné</th><th>Poznámky</th></tr>
    <tr><td>$storageAccountName</td><td>Pokud chcete odeslat skript HiveQL k účtu úložiště Azure.</td></tr>
    <tr><td>$blobContainerName</td><td>Kontejner objektů Blob, kde chcete odeslat skript HiveQL k.</td></tr>
    </table>
    
2. Otevřete Azure PowerShell ISE.  

3. Zkopírujte a vložte následující skript do podokna skriptu:  

    ```powershell
    [CmdletBinding()]
    Param(

        # Azure Blob storage variables
        [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
        [String]$storageAccountName,

        [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
        [String]$blobContainerName
    )

    #region - Define variables
    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    # The HiveQL script file is exported as this file before it's uploaded to Blob storage
    $hqlLocalFileName = "e:\tutorials\flightdelay\flightdelays.hql"

    # The HiveQL script file will be uploaded to Blob storage as this blob name
    $hqlBlobName = "tutorials/flightdelay/flightdelays.hql"

    # These two constants are used by the HiveQL script file
    #$srcDataFolder = "tutorials/flightdelay/data"
    $dstDataFolder = "/tutorials/flightdelay/output"
    #endregion

    #Region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Connect-AzureRmAccount}
    #EndRegion

    #Region - Validate user input
    Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
    # Validate the Storage account
    if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
    {
        Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
        exit
    }
    else{
        $resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
    }

    # Validate the container
    $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

    if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
    {
        Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
        Exit
    }
    #EngRegion

    #region - Validate the file and file path

    # Check if a file with the same file name already exists on the workstation
    Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
    if (test-path $hqlLocalFileName){

        $isDelete = Read-Host 'The file, ' $hqlLocalFileName ', exists.  Do you want to overwirte it? (Y/N)'

        if ($isDelete.ToLower() -ne "y")
        {
            Exit
        }
    }

    # Create the folder if it doesn't exist
    $folder = split-path $hqlLocalFileName
    if (-not (test-path $folder))
    {
        Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green

        new-item $folder -ItemType directory
    }
    #end region

    #region - Write the Hive script into a local file
    Write-Host "`nWriting the Hive script into a file on your workstation ..." `
                -ForegroundColor Green

    $hqlDropDelaysRaw = "DROP TABLE delays_raw;"

    $hqlCreateDelaysRaw = "CREATE EXTERNAL TABLE delays_raw (" +
            "YEAR string, " +
            "FL_DATE string, " +
            "UNIQUE_CARRIER string, " +
            "CARRIER string, " +
            "FL_NUM string, " +
            "ORIGIN_AIRPORT_ID string, " +
            "ORIGIN string, " +
            "ORIGIN_CITY_NAME string, " +
            "ORIGIN_CITY_NAME_TEMP string, " +
            "ORIGIN_STATE_ABR string, " +
            "DEST_AIRPORT_ID string, " +
            "DEST string, " +
            "DEST_CITY_NAME string, " +
            "DEST_CITY_NAME_TEMP string, " +
            "DEST_STATE_ABR string, " +
            "DEP_DELAY_NEW float, " +
            "ARR_DELAY_NEW float, " +
            "CARRIER_DELAY float, " +
            "WEATHER_DELAY float, " +
            "NAS_DELAY float, " +
            "SECURITY_DELAY float, " +
            "LATE_AIRCRAFT_DELAY float) " +
        "ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' " +
        "LINES TERMINATED BY '\n' " +
        "STORED AS TEXTFILE " +
        "LOCATION 'wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data';"

    $hqlDropDelays = "DROP TABLE delays;"

    $hqlCreateDelays = "CREATE TABLE delays AS " +
        "SELECT YEAR AS year, " +
            "FL_DATE AS flight_date, " +
            "substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier, " +
            "substring(CARRIER, 2, length(CARRIER) -1) AS carrier, " +
            "substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num, " +
            "ORIGIN_AIRPORT_ID AS origin_airport_id, " +
            "substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code, " +
            "substring(ORIGIN_CITY_NAME, 2) AS origin_city_name, " +
            "substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr, " +
            "DEST_AIRPORT_ID AS dest_airport_id, " +
            "substring(DEST, 2, length(DEST) -1) AS dest_airport_code, " +
            "substring(DEST_CITY_NAME,2) AS dest_city_name, " +
            "substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr, " +
            "DEP_DELAY_NEW AS dep_delay_new, " +
            "ARR_DELAY_NEW AS arr_delay_new, " +
            "CARRIER_DELAY AS carrier_delay, " +
            "WEATHER_DELAY AS weather_delay, " +
            "NAS_DELAY AS nas_delay, " +
            "SECURITY_DELAY AS security_delay, " +
            "LATE_AIRCRAFT_DELAY AS late_aircraft_delay " +
        "FROM delays_raw;"

    $hqlInsertLocal = "INSERT OVERWRITE DIRECTORY '$dstDataFolder' " +
        "SELECT regexp_replace(origin_city_name, '''', ''), " +
            "avg(weather_delay) " +
        "FROM delays " +
        "WHERE weather_delay IS NOT NULL " +
        "GROUP BY origin_city_name;"

    $hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal

    $hqlScript | Out-File $hqlLocalFileName -Encoding ascii -Force
    #endregion

    #region - Upload the Hive script to the default Blob container
    Write-Host "`nUploading the Hive script to the default Blob container ..." -ForegroundColor Green

    # Create a storage context object
    $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

    # Upload the file from local workstation to Blob storage
    Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $blobContainerName -Blob $hqlBlobName -Context $destContext
    #endregion

    Write-host "`nEnd of the PowerShell script" -ForegroundColor Green
    ```

    Zde jsou proměnné používané ve skriptu:

   * **$hqlLocalFileName** -skript místně uloží soubor skriptu HiveQL před nahráním do úložiště objektů Blob. Toto je název souboru. Výchozí hodnota je <u>C:\tutorials\flightdelay\flightdelays.hql</u>.
   * **$hqlBlobName** -Toto je název objektu blob souboru skript HiveQL použít ve službě Azure Blob storage. Výchozí hodnota je tutorials/flightdelay/flightdelays.hql. Protože soubor bude zapisovat přímo do úložiště objektů Azure Blob, není "/" na začátku názvu objektu blob. Pokud chcete získat přístup k souboru z úložiště objektů Blob, musíte přidat "/" na začátku názvu souboru.
   * **$srcDataFolder** a **$dstDataFolder** -= "kurzy/flightdelay nebo data" = "kurzy a flightdelay nebo výstupní"

- - -
## <a id="appendix-c"></a>Příloha C – Příprava Azure SQL database pro výstup úlohy Sqoop
**Příprava databáze SQL (sloučení to pomocí skriptu Sqoop)**

1. Připravte parametry:

    <table border="1">
    <tr><th>Název proměnné</th><th>Poznámky</th></tr>
    <tr><td>$sqlDatabaseServerName</td><td>Název databáze serveru Azure SQL. Zadejte, co vytvořit nový server.</td></tr>
    <tr><td>$sqlDatabaseUsername</td><td>Přihlašovací jméno pro server databáze Azure SQL. Pokud $sqlDatabaseServerName stávajícího serveru, se používají přihlašovací jméno a heslo pro přihlášení k ověření serveru. V opačném případě se používají k vytvoření nového serveru.</td></tr>
    <tr><td>$sqlDatabasePassword</td><td>Heslo pro přihlášení pro server databáze Azure SQL.</td></tr>
    <tr><td>$sqlDatabaseLocation</td><td>Tato hodnota se používá jenom v případě, že vytváříte nový server databáze Azure.</td></tr>
    <tr><td>$sqlDatabaseName</td><td>Databázi SQL používanou k vytvoření tabulky AvgDelays Sqoop úlohy. Ponechat prázdné vytvoří databázi s názvem HDISqoop. Název tabulky pro výstup úlohy Sqoop je AvgDelays. </td></tr>
    </table>
    
2. Otevřete Azure PowerShell ISE.

3. Zkopírujte a vložte následující skript do podokna skriptu:  

    ```powershell
    [CmdletBinding()]
    Param(

        # Azure Resource group variables
        [Parameter(Mandatory=$True,
                HelpMessage="Enter the Azure resource group name. It will be created if it doesn't exist.")]
        [String]$resourceGroupName,

        # SQL database server variables
        [Parameter(Mandatory=$True,
                HelpMessage="Enter the Azure SQL Database Server Name. It will be created if it doesn't exist.")]
        [String]$sqlDatabaseServer,

        [Parameter(Mandatory=$True,
                HelpMessage="Enter the Azure SQL Database admin user.")]
        [String]$sqlDatabaseLogin,

        [Parameter(Mandatory=$True,
                HelpMessage="Enter the Azure SQL Database admin user password.")]
        [String]$sqlDatabasePassword,

        [Parameter(Mandatory=$True,
                HelpMessage="Enter the region to create the Database in.")]
        [String]$sqlDatabaseLocation,   #For example, West US.

        # SQL database variables
        [Parameter(Mandatory=$True,
                HelpMessage="Enter the database name. It will be created if it doesn't exist.")]
        [String]$sqlDatabaseName # specify the database name if you have one created. Otherwise use "" to have the script create one for you.
    )

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    #region - Constants and variables

    # IP address REST service used for retrieving external IP address and creating firewall rules
    [String]$ipAddressRestService = "http://bot.whatismyipaddress.com"
    [String]$fireWallRuleName = "FlightDelay"

    # SQL database variables
    [String]$sqlDatabaseMaxSizeGB = 10

    #SQL query string for creating AvgDelays table
    [String]$sqlDatabaseTableName = "AvgDelays"
    [String]$sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                [origin_city_name] [nvarchar](50) NOT NULL,
                [weather_delay] float,
            CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
            (
                [origin_city_name] ASC
            )
            )"
    #endregion

    #Region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Connect-AzureRmAccount}
    #EndRegion

    #region - Create and validate Azure resouce group
    try{
        Get-AzureRmResourceGroup -Name $resourceGroupName
    }
    catch{
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $sqlDatabaseLocation
    }

    #EndRegion

    #region - Create and validate Azure SQL database server
    try{
        Get-AzureRmSqlServer -ServerName $sqlDatabaseServer -ResourceGroupName $resourceGroupName}
    catch{
        Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green

        $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
        $credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)

        $sqlDatabaseServer = (New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -SqlAdministratorCredentials $credential -Location $sqlDatabaseLocation).ServerName
        Write-Host "`tThe new SQL database server name is $sqlDatabaseServer." -ForegroundColor Cyan

        Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
        $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
        New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-workstation" -StartIpAddress $workstationIPAddress -EndIpAddress $workstationIPAddress

        #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. Note that this allows Azure traffic from any Azure subscription to access the server.
        New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-Azureservices" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
    }

    #endregion

    #region - Create and validate Azure SQL database

    try {
        Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName
    }
    catch {
        Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
        New-AzureRMSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName -Edition "Standard" -RequestedServiceObjectiveName "S1"
    }

    #endregion

    #region -  Execute an SQL command to create the AvgDelays table

    Write-Host "`nCreating SQL Database table ..."  -ForegroundColor Green
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
    $conn.open()
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.connection = $conn
    $cmd.commandtext = $sqlCreateAvgDelaysTable
    $cmd.executenonquery()

    $conn.close()

    Write-host "`nEnd of the PowerShell script" -ForegroundColor Green
    ```

   > [!NOTE]
   > Tento skript využívá službu representational stavu transfer (REST), http://bot.whatismyipaddress.com, načíst externí IP adresu. IP adresa se používá pro vytvoření pravidla brány firewall pro server databáze SQL.

    Zde jsou některé proměnné používané ve skriptu:

   * **$ipAddressRestService** – výchozí hodnota je http://bot.whatismyipaddress.com. Je veřejnou IP adresu služby REST pro získání externí IP adresu. Pokud chcete, můžete použít jiné služby. Externí IP adresu získat pomocí služby se použije k vytvoření pravidla brány firewall pro server databáze Azure SQL, takže budete mít přístup k databázi z pracovní stanice (pomocí skriptu prostředí Windows PowerShell).
   * **$fireWallRuleName** -Toto je název pravidla brány firewall pro server databáze Azure SQL. Výchozí název je <u>FlightDelay</u>. Pokud chcete, můžete ho změnit.
   * **$sqlDatabaseMaxSizeGB** – tato hodnota se používá jenom v případě, že vytváříte nový server databáze Azure SQL. Výchozí hodnota je 10GB. 10GB je dostačující pro účely tohoto kurzu.
   * **$sqlDatabaseName** – tato hodnota se používá jenom v případě, že vytváříte novou databázi Azure SQL. Výchozí hodnota je HDISqoop. Pokud přejmenujete, je nutné aktualizovat Sqoop Windows PowerShell skriptu odpovídajícím způsobem.
4. Stisknutím klávesy **F5** spusťte skript.
5. Ověření výstup skriptu. Ujistěte se, že skript proběhla úspěšně.

## <a id="nextsteps"></a> Další kroky
Teď víte, jak nahrát soubor do úložiště objektů Blob v Azure, jak naplnit tabulku Hive pomocí dat z Azure Blob storage, jak spouštět dotazy Hive a postup použití nástroje Sqoop exportovat data z HDFS do Azure SQL database. Další informace naleznete v následujících článcích:

* [Začínáme s HDInsight][hdinsight-get-started]
* [Použití Hivu se službou HDInsight][hdinsight-use-hive]
* [Použijte Oozie s HDInsight][hdinsight-use-oozie]
* [Použití nástroje Sqoop s HDInsight][hdinsight-use-sqoop]
* [Použití Pigu se službou HDInsight][hdinsight-use-pig]
* [Vývoj aplikací Java MapReduce pro HDInsight][hdinsight-develop-mapreduce]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-develop-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
[hadoop-shell-commands]: http://hadoop.apache.org/docs/r0.18.3/hdfs_shell.html

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

[image-hdi-flightdelays-avgdelays-dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
[img-hdi-flightdelays-run-hive-job-output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
[img-hdi-flightdelays-flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png
