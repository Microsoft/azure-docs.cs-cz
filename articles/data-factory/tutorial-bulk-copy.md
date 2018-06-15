---
title: Hromadné kopírování dat pomocí Azure Data Factory | Dokumentace Microsoftu
description: Naučte se používat Azure Data Factory a aktivitu kopírování k hromadnému kopírování dat ze zdrojového úložiště dat do cílového úložiště dat.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: 28128e07b4beb03b1a8ecdd8546d54c19145689f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
ms.locfileid: "31593691"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Hromadné kopírování několika tabulek pomocí Azure Data Factory
Tento kurz představuje **kopírování několika tabulek z Azure SQL Database do služby Azure SQL Data Warehouse**. Stejný vzor můžete využít i u dalších scénářů kopírování. Například při kopírování tabulek z SQL Serveru/Oraclu do služby Azure SQL Database/Data Warehouse/Azure Blob nebo při kopírování různých cest ze služby Blob do tabulek Azure SQL Database.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), prostudujte si [dokumentaci služby Data Factory verze 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Tento kurz zahrnuje následující základní kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření propojených služeb Azure SQL Database, Azure SQL Data Warehouse a Azure Storage
> * Vytvoření datových sad Azure SQL Database a Azure SQL Data Warehouse
> * Vytvoření kanálu pro vyhledání tabulek ke zkopírování a dalšího kanálu pro provedení vlastní operace kopírování 
> * Spuštění kanálu
> * Monitorování spuštění aktivit a kanálu

Tento kurz používá prostředí Azure PowerShell. Další informace o vytvoření datové továrny pomocí jiných nástrojů nebo sad SDK najdete v tématu [Šablony Rychlý start](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Ucelený pracovní postup
V tomto scénáři máme několik tabulek v Azure SQL Database, které chceme zkopírovat do služby SQL Data Warehouse. Tady je logická posloupnost kroků tohoto pracovního postupu, které se provádějí v kanálech:

![Pracovní postup](media/tutorial-bulk-copy/tutorial-copy-multiple-tables.png)

* První kanál vyhledá seznam tabulek, které je potřeba zkopírovat do úložišť dat jímky.  Další možností je udržovat tabulku metadat se seznamem všech tabulek, které je potřeba zkopírovat do úložišť dat jímky. Kanál potom aktivuje jiný kanál, který postupně prochází všechny tabulky v databázi a provádí operaci kopírování dat.
* Tento druhý kanál provádí vlastní kopírování. Jako parametr používá seznam tabulek. Každá tabulka v tomto seznamu se zkopíruje z Azure SQL Database do příslušné tabulky ve službě SQL Data Warehouse pomocí [fázovaného kopírování prostřednictvím Blob Storage a PolyBase](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) pro zajištění nejlepšího výkonu. V tomto příkladu první kanál předá seznam tabulek jako hodnotu parametru. 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* **Azure PowerShell**. Postupujte podle pokynů v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/install-azurerm-ps).
* **Účet služby Azure Storage**. Účet Azure Storage se v operaci hromadného kopírování používá jako pracovní úložiště objektů blob. 
* **Azure SQL Database**. Tato databáze obsahuje zdrojová data. 
* **Azure SQL Data Warehouse**. Tento datový sklad obsahuje data zkopírovaná z SQL Database. 

### <a name="prepare-sql-database-and-sql-data-warehouse"></a>Příprava SQL Database a služby SQL Data Warehouse

**Příprava zdrojové databáze Azure SQL Database**:

Podle postupu v článku [Vytvoření databáze SQL Azure](../sql-database/sql-database-get-started-portal.md) vytvořte Azure SQL Database s ukázkovými daty Adventure Works LT. V tomto kurzu se všechny tabulky z této ukázkové databáze zkopírují do datového skladu SQL.

**Příprava jímky Azure SQL Data Warehouse**:

1. Pokud Azure SQL Data Warehouse nemáte, přečtěte si článek věnovaný [vytvoření služby SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md), kde najdete kroky pro její vytvoření.

2. V SQL Data Warehouse vytvořte odpovídající schémata tabulek. K **migraci schémat** z Azure SQL Database do Azure SQL Data Warehouse můžete využít [nástroj pro migraci](https://www.microsoft.com/download/details.aspx?id=49100) . K migraci/kopírování dat v pozdějším kroku můžete použít Azure Data Factory.

## <a name="azure-services-to-access-sql-server"></a>Služby Azure pro přístup k SQL serveru

Pro SQL Database i SQL Data Warehouse povolte službám Azure přístup k SQL serveru. Ujistěte se, že nastavení **Povolit přístup ke službám Azure** je pro SQL server Azure **zapnuté**. Toto nastavení umožňuje službě Data Factory načítat data z Azure SQL Database a zapisovat data do Azure SQL Data Warehouse. Pokud chcete toto nastavení ověřit a zapnout, proveďte následující kroky:

1. Klikněte na **Všechny služby** na levé straně a pak klikněte na **Servery SQL**.
2. Vyberte svůj server a v části **NASTAVENÍ** klikněte na **Brána firewall**.
3. Na stránce **Nastavení brány firewall** klikněte na **ZAPNUTO** u možnosti **Povolit přístup ke službám Azure**.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Spusťte **PowerShell**. Nechte prostředí Azure PowerShell otevřené až do konce tohoto kurzu. Pokud ho zavřete a znovu otevřete, bude potřeba tyto příkazy spustit znovu.

    Spusťte následující příkaz a zadejte uživatelské jméno a heslo, které používáte k přihlášení na web Azure Portal:
        
    ```powershell
    Connect-AzureRmAccount
    ```
    Spuštěním následujícího příkazu zobrazíte všechna předplatná pro tento účet:

    ```powershell
    Get-AzureRmSubscription
    ```
    Spuštěním následujícího příkazu vyberte předplatné, se kterým chcete pracovat. Místo **SubscriptionId** použijte ID vašeho předplatného Azure:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"
    ```
2. Spusťte rutinu **Set-AzureRmDataFactoryV2** pro vytvoření datové továrny. Před spuštěním tohoto příkazu zástupné znaky nahraďte vlastními hodnotami. 

    ```powershell
    $resourceGroupName = "<your resource group to create the factory>"
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>"
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

    Je třeba počítat s následujícím:

    * Název objektu pro vytváření dat Azure musí být globálně jedinečný. Pokud se zobrazí následující chyba, změňte název a zkuste to znovu.

        ```
        The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
        ```

    * Instance služby Data Factory můžete vytvářet jenom tehdy, když jste přispěvatelem nebo správcem předplatného Azure.
    * Data Factory V2 v současné době umožňuje vytváření datových továren jenom v oblastech Východní USA, Východní USA 2 a Západní Evropa. Úložiště dat (Azure Storage, Azure SQL Database atd.) a výpočetní prostředí (HDInsight atd.) používané datovou továrnou mohou být v jiných oblastech.

## <a name="create-linked-services"></a>Vytvoření propojených služeb

V tomto kurzu vytvoříte tři propojené služby pro zdrojový, objekt blob, objekt blob jímky a pracovní objekt blob, která zahrnují připojení k vašim úložištím dat:

### <a name="create-the-source-azure-sql-database-linked-service"></a>Vytvoření propojené služby Azure SQL Database pro zdroj

1. Vytvořte soubor JSON s názvem **AzureSqlDatabaseLinkedService.json** ve složce **C:\ADFv2TutorialBulkCopy** s následujícím obsahem. (Pokud složka \ADFv2TutorialBulkCopy ještě neexistuje, vytvořte ji.)

    > [!IMPORTANT]
    > Před uložením tohoto souboru položky &lt;servername&gt;, &lt;databasename&gt;, &lt;username&gt;@&lt;servername&gt; a &lt;password&gt; nahraďte odpovídajícími hodnotami pro Azure SQL Database.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                }
            }
        }
    }
    ```

2. V **Azure PowerShellu** přejděte do složky **ADFv2TutorialBulkCopy**.

3. Spuštěním rutiny **Set-AzureRmDataFactoryV2LinkedService** vytvořte propojenou službu **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

    Zde je ukázkový výstup:

    ```json
    LinkedServiceName : AzureSqlDatabaseLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

### <a name="create-the-sink-azure-sql-data-warehouse-linked-service"></a>Vytvoření propojené služby Azure SQL Data Warehouse pro jímku

1. Ve složce **C:\ADFv2TutorialBulkCopy** vytvořte soubor JSON s názvem **AzureSqlDWLinkedService.json** s následujícím obsahem:

    > [!IMPORTANT]
    > Před uložením tohoto souboru položky &lt;servername&gt;, &lt;databasename&gt;, &lt;username&gt;@&lt;servername&gt; a &lt;password&gt; nahraďte odpovídajícími hodnotami pro Azure SQL Database.

    ```json
    {
        "name": "AzureSqlDWLinkedService",
        "properties": {
            "type": "AzureSqlDW",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
            }
        }
    }
    ```

2. Spuštěním rutiny **Set-AzureRmDataFactoryV2LinkedService** vytvořte propojenou službu **AzureSqlDWLinkedService**.

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDWLinkedService" -File ".\AzureSqlDWLinkedService.json"
    ```

    Zde je ukázkový výstup:

    ```json
    LinkedServiceName : AzureSqlDWLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDWLinkedService
    ```

### <a name="create-the-staging-azure-storage-linked-service"></a>Vytvoření pracovní propojené služby Azure Storage

V tomto kurzu použijete Azure Blob Storage jako dočasné pracovní oblast, abyste zajistili lepší výkon kopírování pro funkci PolyBase.

1. Ve složce **C:\ADFv2TutorialBulkCopy** vytvořte soubor JSON s názvem **AzureStorageLinkedService.json** s následujícím obsahem:

    > [!IMPORTANT]
    > Než soubor uložíte, položky &lt;accountName&gt; a &lt;accountKey&gt; nahraďte názvem svého účtu Azure Storage a jeho klíčem.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
                }
            }
        }
    }
    ```

2. Spuštěním rutiny **Set-AzureRmDataFactoryV2LinkedService** vytvořte propojenou službu **AzureStorageLinkedService**.

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    Zde je ukázkový výstup:

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

## <a name="create-datasets"></a>Vytvoření datových sad

V tomto kurzu vytvoříte zdrojovou datovou sadu a datovou sadu jímky, které určují umístění pro uložení dat:

### <a name="create-a-dataset-for-source-sql-database"></a>Vytvoření datové sady pro zdrojovou databázi SQL Database

1. Ve složce **C:\ADFv2TutorialBulkCopy** vytvořte soubor JSON s názvem **AzureSqlDatabaseDataset.json** s následujícím obsahem. TableName je jenom fiktivní, protože později k načtení dat v aktivitě kopírování použijete dotaz SQL.

    ```json
    {
        "name": "AzureSqlDatabaseDataset",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": {
                "referenceName": "AzureSqlDatabaseLinkedService",
                "type": "LinkedServiceReference"
            },
            "typeProperties": {
                "tableName": "dummy"
            }
        }
    }
    ```

2. Spuštěním rutiny **Set-AzureRmDataFactoryV2Dataset** vytvořte datovou sadu **AzureSqlDatabaseDataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDatabaseDataset" -File ".\AzureSqlDatabaseDataset.json"
    ```

    Zde je ukázkový výstup:

    ```json
    DatasetName       : AzureSqlDatabaseDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-sink-sql-data-warehouse"></a>Vytvoření datové sady pro SQL Data Warehouse jímky

1. Ve složce **C:\ADFv2TutorialBulkCopy** vytvořte soubor JSON s názvem **AzureSqlDWDataset.json** s následujícím obsahem. TableName se nastavuje jako parametr. Aktivita kopírování, která odkazuje na tuto datovou sadu, později datové sadě předá skutečnou hodnotu.

    ```json
    {
        "name": "AzureSqlDWDataset",
        "properties": {
            "type": "AzureSqlDWTable",
            "linkedServiceName": {
                "referenceName": "AzureSqlDWLinkedService",
                "type": "LinkedServiceReference"
            },
            "typeProperties": {
                "tableName": {
                    "value": "@{dataset().DWTableName}",
                    "type": "Expression"
                }
            },
            "parameters":{
                "DWTableName":{
                    "type":"String"
                }
            }
        }
    }
    ```

2. Spuštěním rutiny **Set-AzureRmDataFactoryV2Dataset** vytvořte datovou sadu **AzureSqlDWDataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDWDataset" -File ".\AzureSqlDWDataset.json"
    ```

    Zde je ukázkový výstup:

    ```json
    DatasetName       : AzureSqlDWDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDwTableDataset
    ```

## <a name="create-pipelines"></a>Vytvoření kanálů

V tomto kurzu vytvoříte dva kanály:

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Vytvoření kanálu IterateAndCopySQLTables

Tento kanál jako parametr používá seznam tabulek. Data ze všech tabulek v tomto seznamu se zkopírují z Azure SQL Database do služby SQL Data Warehouse pomocí fázovaného kopírování a PolyBase.

1. Ve složce **C:\ADFv2TutorialBulkCopy** vytvořte soubor JSON s názvem **IterateAndCopySQLTables.json** s následujícím obsahem.

    ```json
    {
        "name": "IterateAndCopySQLTables",
        "properties": {
            "activities": [
                {
                    "name": "IterateSQLTables",
                    "type": "ForEach",
                    "typeProperties": {
                        "isSequential": "false",
                        "items": {
                            "value": "@pipeline().parameters.tableList",
                            "type": "Expression"
                        },
                        "activities": [
                            {
                                "name": "CopyData",
                                "description": "Copy data from SQL database to SQL DW",
                                "type": "Copy",
                                "inputs": [
                                    {
                                        "referenceName": "AzureSqlDatabaseDataset",
                                        "type": "DatasetReference"
                                    }
                                ],
                                "outputs": [
                                    {
                                        "referenceName": "AzureSqlDWDataset",
                                        "type": "DatasetReference",
                                        "parameters": {
                                            "DWTableName": "[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                        }
                                    }
                                ],
                                "typeProperties": {
                                    "source": {
                                        "type": "SqlSource",
                                        "sqlReaderQuery": "SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                    },
                                    "sink": {
                                        "type": "SqlDWSink",
                                        "preCopyScript": "TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]",
                                        "allowPolyBase": true
                                    },
                                    "enableStaging": true,
                                    "stagingSettings": {
                                        "linkedServiceName": {
                                            "referenceName": "AzureStorageLinkedService",
                                            "type": "LinkedServiceReference"
                                        }
                                    }
                                }
                            }
                        ]
                    }
                }
            ],
            "parameters": {
                "tableList": {
                    "type": "Object"
                }
            }
        }
    }
    ```

2. Spuštěním rutiny **Set-AzureRmDataFactoryV2Pipeline** vytvořte kanál **IterateAndCopySQLTables**.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IterateAndCopySQLTables" -File ".\IterateAndCopySQLTables.json"
    ```

    Zde je ukázkový výstup:

    ```json
    PipelineName      : IterateAndCopySQLTables
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>Vytvoření kanálu GetTableListAndTriggerCopyData

Tento kanál provádí dva kroky:

* Vyhledá systémové tabulky Azure SQL Database a získá seznam tabulek, které se mají zkopírovat.
* Aktivuje kanál IterateAndCopySQLTables, který provede vlastní kopírování dat.

1. Ve složce **C:\ADFv2TutorialBulkCopy** vytvořte soubor JSON s názvem **GetTableListAndTriggerCopyData.json** s následujícím obsahem.

    ```json
    {
        "name":"GetTableListAndTriggerCopyData",
        "properties":{
            "activities":[
                { 
                    "name": "LookupTableList",
                    "description": "Retrieve the table list from Azure SQL dataabse",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'"
                        },
                        "dataset": {
                            "referenceName": "AzureSqlDatabaseDataset",
                            "type": "DatasetReference"
                        },
                        "firstRowOnly": false
                    }
                },
                {
                    "name": "TriggerCopy",
                    "type": "ExecutePipeline",
                    "typeProperties": {
                        "parameters": {
                            "tableList": {
                                "value": "@activity('LookupTableList').output.value",
                                "type": "Expression"
                            }
                        },
                        "pipeline": {
                            "referenceName": "IterateAndCopySQLTables",
                            "type": "PipelineReference"
                        },
                        "waitOnCompletion": true
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupTableList",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ]
                }
            ]
        }
    }
    ```

2. Spuštěním rutiny **Set-AzureRmDataFactoryV2Pipeline** vytvořte kanál **GetTableListAndTriggerCopyData**.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "GetTableListAndTriggerCopyData" -File ".\GetTableListAndTriggerCopyData.json"
    ```

    Zde je ukázkový výstup:

    ```json
    PipelineName      : GetTableListAndTriggerCopyData
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {LookupTableList, TriggerCopy}
    Parameters        :
    ```

## <a name="start-and-monitor-pipeline-run"></a>Spuštění kanálu a jeho monitorování

1. Zahajte spuštění pro hlavní kanál GetTableListAndTriggerCopyData a zaznamenejte ID spuštění kanálu pro budoucí monitorování. V pozadí se aktivuje spuštění kanálu IterateAndCopySQLTables, jak určuje aktivita ExecutePipeline.

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'GetTableListAndTriggerCopyData'
    ```

2.  Spusťte následující skript pro nepřetržitou kontrolu stavu spuštění kanálu **GetTableListAndTriggerCopyData** a vytiskněte finální výsledek spuštění kanálů a spuštění aktivit.

    ```powershell
    while ($True) {
        $run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                Write-Host "Pipeline run details:" -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 15
    }

    $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result
    ```

    Zde je výstup tohoto ukázkového spuštění:

    ```json
    Pipeline run details:
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    RunId             : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    LastUpdated       : 9/18/2017 4:08:15 PM
    Parameters        : {}
    RunStart          : 9/18/2017 4:06:44 PM
    RunEnd            : 9/18/2017 4:08:15 PM
    DurationInMs      : 90637
    Status            : Succeeded
    Message           : 

    Activity run details:
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : LookupTableList
    PipelineRunId     : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    Input             : {source, dataset, firstRowOnly}
    Output            : {count, value, effectiveIntegrationRuntime}
    LinkedServiceName : 
    ActivityRunStart  : 9/18/2017 4:06:46 PM
    ActivityRunEnd    : 9/18/2017 4:07:09 PM
    DurationInMs      : 22995
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : TriggerCopy
    PipelineRunId     : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    Input             : {pipeline, parameters, waitOnCompletion}
    Output            : {pipelineRunId}
    LinkedServiceName : 
    ActivityRunStart  : 9/18/2017 4:07:11 PM
    ActivityRunEnd    : 9/18/2017 4:08:14 PM
    DurationInMs      : 62581
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. Můžete získat ID spuštění kanálu **IterateAndCopySQLTables** a zkontrolovat podrobné výsledky aktivit spuštění.

    ```powershell
    Write-Host "Pipeline 'IterateAndCopySQLTables' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "TriggerCopy"}).Output.ToString()
    ```

    Zde je výstup tohoto ukázkového spuštění:

    ```json
    {
        "pipelineRunId": "7514d165-14bf-41fb-b5fb-789bea6c9e58"
    }
    ```

    ```powershell
    $result2 = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId <copy above run ID> -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result2
    ```

3. Připojte se ke službě Azure SQL Data Warehouse pro jímku a potvrďte, že se data z Azure SQL Database zkopírovala správně.

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste provedli následující kroky: 

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření propojených služeb Azure SQL Database, Azure SQL Data Warehouse a Azure Storage
> * Vytvoření datových sad Azure SQL Database a Azure SQL Data Warehouse
> * Vytvoření kanálu pro vyhledání tabulek ke zkopírování a dalšího kanálu pro provedení vlastní operace kopírování 
> * Spuštění kanálu
> * Monitorování spuštění aktivit a kanálu

Pokud se chcete dozvědět víc o přírůstkovém kopírování ze zdroje do cíle, přejděte k následujícímu kurzu:
> [!div class="nextstepaction"]
>[Přírůstkové kopírování dat](tutorial-incremental-copy-powershell.md)
