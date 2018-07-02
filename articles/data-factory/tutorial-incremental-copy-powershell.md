---
title: Přírůstkové kopírování tabulky pomocí Azure Data Factory | Microsoft Docs
description: V tomto kurzu vytvoříte kanál Azure Data Factory, který přírůstkově kopíruje data ze služby Azure SQL Database do úložiště Azure Blob Storage.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: yexu
ms.openlocfilehash: d9b1666c1543e16fe5433e05645c232d01e7260c
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054564"
---
# <a name="incrementally-load-data-from-an-azure-sql-database-to-azure-blob-storage"></a>Přírůstkové načtení dat ze služby Azure SQL Database do úložiště Azure Blob Storage
V tomto kurzu vytvoříte službu Azure Data Factory s kanálem, který načítá rozdílová data z tabulky ve službě Azure SQL Database do úložiště Azure Blob Storage. 

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Příprava úložiště dat pro uložení hodnoty meze
> * Vytvoření datové továrny
> * Vytvoření propojených služeb 
> * Vytvoření zdroje, jímky a datových sad mezí
> * Vytvoření kanálu
> * Spuštění kanálu
> * Monitorování spuštění kanálu 

## <a name="overview"></a>Přehled
Tady je souhrnný diagram tohoto řešení: 

![Přírůstkové načtení dat](media\tutorial-Incrementally-copy-powershell\incrementally-load.png)

Tady jsou důležité kroky pro vytvoření tohoto řešení: 

1. **Vyberte sloupec meze**.
    Ve zdrojovém úložišti dat vyberte jeden sloupec, který je možné použít k rozlišení nových nebo aktualizovaných záznamů pro každé spuštění. Data v tomto vybraném sloupci (například čas_poslední_změny nebo ID) se při vytváření nebo aktualizaci řádků obvykle zvyšují. Maximální hodnota v tomto sloupci se používá jako horní mez.

2. **Připravte úložiště dat pro uložení hodnoty meze**.   
    V tomto kurzu uložíte hodnotu meze do databáze SQL.
    
3. **Vytvořte kanál s následujícím pracovním postupem**: 
    
    Kanál v tomto řešení má následující aktivity:
  
    * Vytvořte dvě aktivity vyhledávání. První aktivitu vyhledávání použijte k načtení poslední hodnoty meze. Druhou aktivitu vyhledávání použijte k načtení nové hodnoty meze. Tyto hodnoty meze se předají aktivitě kopírování. 
    * Vytvořte aktivitu kopírování, která ze zdrojového úložiště dat zkopíruje řádky, které ve sloupci horní meze mají hodnotu vyšší, než je stará hodnota meze, a nižší, než je nová hodnota meze. Potom tato rozdílová data zkopíruje ze zdrojového úložiště dat do úložiště objektů blob jako nový soubor. 
    * Vytvořte aktivitu uložené procedury StoredProcedure, která aktualizuje hodnotu meze pro příští spuštění kanálu. 


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
* **Azure SQL Database**. Tuto databázi použijete jako zdrojové úložiště dat. Pokud databázi SQL nemáte, přečtěte si téma [Vytvoření databáze Azure SQL](../sql-database/sql-database-get-started-portal.md), kde najdete kroky pro její vytvoření.
* **Azure Storage** Úložiště objektů blob použijete jako úložiště dat jímky. Pokud nemáte účet úložiště, přečtěte si téma [Vytvoření účtu úložiště](../storage/common/storage-create-storage-account.md#create-a-storage-account), kde najdete kroky pro jeho vytvoření. Vytvořte kontejner s názvem adftutorial. 
* **Azure PowerShell**. Postupujte podle pokynů v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/install-azurerm-ps).

### <a name="create-a-data-source-table-in-your-sql-database"></a>Vytvoření tabulky zdroje dat v databázi SQL
1. Otevřete SQL Server Management Studio. V **Průzkumníku serveru** klikněte pravým tlačítkem na databázi a zvolte **Nový dotaz**.

2. Spuštěním následujícího příkazu SQL na vaší databázi SQL vytvořte tabulku s názvem `data_source_table` jako úložiště zdroje dat: 
    
    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    V tomto kurzu jako sloupec meze použijete LastModifytime. Data v úložišti zdroje dat zobrazuje následující tabulka:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-your-sql-database-to-store-the-high-watermark-value"></a>Vytvoření další tabulky v databázi SQL pro ukládání hodnoty horní meze
1. Spuštěním následujícího příkazu SQL na databázi SQL vytvořte tabulku s názvem `watermarktable` pro uložení hodnoty meze:  
    
    ```sql
    create table watermarktable
    (
    
    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
2. Jako výchozí hodnotu horní meze nastavte název tabulky zdrojového úložiště dat. V tomto kurzu má tato tabulka název data_source_table.

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
3. Zkontrolujte data v tabulce `watermarktable`.
    
    ```sql
    Select * from watermarktable
    ```
    Výstup: 

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-your-sql-database"></a>Vytvoření uložené procedury v databázi SQL 

Spuštěním následujícího příkazu vytvořte v databázi SQL uloženou proceduru:

```sql
CREATE PROCEDURE sp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN
    
    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName
    
END
```

## <a name="create-a-data-factory"></a>Vytvoření datové továrny
1. Definujte proměnnou pro název skupiny prostředků, kterou použijete později v příkazech PowerShellu. Zkopírujte do PowerShellu následující text příkazu, zadejte název [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md) v uvozovkách a pak příkaz spusťte. Příklad: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Pokud již skupina prostředků existuje, nepřepisujte ji. Přiřaďte proměnné `$resourceGroupName` jinou hodnotu a spusťte tento příkaz znovu.

2. Definujte proměnnou pro umístění datové továrny. 

    ```powershell
    $location = "East US"
    ```
3. Pokud chcete vytvořit skupinu prostředků Azure, spusťte následující příkaz: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 
    Pokud již skupina prostředků existuje, nepřepisujte ji. Přiřaďte proměnné `$resourceGroupName` jinou hodnotu a spusťte tento příkaz znovu.

4. Definujte proměnnou název datové továrny. 

    > [!IMPORTANT]
    >  Aktualizujte název datové továrny tak, aby byl globálně jedinečný. Například ADFTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFIncCopyTutorialFactory";
    ```
5. Pokud chcete vytvořit datovou továrnu, spusťte následující rutinu **Set-AzureRmDataFactoryV2**: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

Je třeba počítat s následujícím:

* Název datové továrny musí být globálně jedinečný. Pokud se zobrazí následující chyba, změňte název a zkuste to znovu:

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Pro vytvoření instancí služby Data Factory musí být uživatelský účet, který použijete pro přihlášení k Azure, členem rolí přispěvatel nebo vlastník nebo správcem předplatného Azure.
* Data Factory v současné době umožňuje vytváření datových továren jenom v oblastech Východní USA, Východní USA 2 a Západní Evropa. Úložiště dat (služba Storage, databáze SQL atd.) a výpočetní prostředí (Azure HDInsight atd.) používané datovou továrnou mohou být v jiných oblastech.


## <a name="create-linked-services"></a>Vytvoření propojených služeb
V datové továrně vytvoříte propojené služby, abyste svá úložiště dat a výpočetní služby spojili s datovou továrnou. V této části vytvoříte propojené služby pro účet úložiště a databázi SQL. 

### <a name="create-a-storage-linked-service"></a>Vytvoření propojené služby Storage
1. Ve složce C:\ADF vytvořte soubor JSON s názvem AzureStorageLinkedService.json s následujícím kódem. (Pokud složka ADF ještě neexistuje, vytvořte ji.) Před uložením souboru nahraďte `<accountName>` a `<accountKey>` názvem a klíčem vašeho účtu úložiště.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
2. V PowerShellu přejděte do složky ADF.

3. Spuštěním rutiny **Set-AzureRmDataFactoryV2LinkedService** vytvořte propojenou službu AzureStorageLinkedService. V následujícím příkladu předáte hodnoty pro parametry *ResourceGroupName* a *DataFactoryName*: 

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

### <a name="create-a-sql-database-linked-service"></a>Vytvoření propojené služby databáze SQL
1. Ve složce C:\ADF vytvořte soubor JSON s názvem AzureSQLDatabaseLinkedService.json s následujícím kódem. (Pokud složka ADF ještě neexistuje, vytvořte ji.) Než soubor uložíte, položky &lt;server&gt;, &lt;database&gt;, &lt;user id&gt; a &lt;password&gt; nahraďte názvem vašeho serveru, databází, ID uživatele a heslem. 

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "value": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database>; Persist Security Info=False; User ID=<user> ; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
2. V PowerShellu přejděte do složky ADF.

3. Spuštěním rutiny **Set-AzureRmDataFactoryV2LinkedService** vytvořte propojenou službu AzureSQLDatabaseLinkedService. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Zde je ukázkový výstup:

    ```json
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ProvisioningState :
    ```

## <a name="create-datasets"></a>Vytvoření datových sad
V tomto kroku vytvoříte datové sady, které reprezentují data zdroje a jímky. 

### <a name="create-a-source-dataset"></a>Vytvoření zdrojové datové sady

1. Ve stejné složce vytvořte soubor JSON s názvem SourceDataset.json a s následujícím obsahem: 

    ```json
    {
        "name": "SourceDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "data_source_table"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
   
    ```
    V tomto kurzu se používá tabulka s názvem data_source_table. Pokud používáte tabulku s jiným názvem, nahraďte ho.

2. Spuštěním rutiny **Set-AzureRmDataFactoryV2Dataset** vytvořte datovou sadu SourceDataset.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Tady je ukázkový výstup této rutiny:
    
    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Vytvoření datové sady jímky

1. Ve stejné složce vytvořte soubor JSON s názvem SinkDataset.json a s následujícím obsahem: 

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/incrementalcopy",
                "fileName": "@CONCAT('Incremental-', pipeline().RunId, '.txt')", 
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }   
    ```

    > [!IMPORTANT]
    > Tento fragment kódu předpokládá, že ve svém úložišti objektů blob máte kontejner objektů blob s názvem adftutorial. Pokud tento kontejner neexistuje, vytvořte ho nebo použijte název existujícího kontejneru. Výstupní složka `incrementalcopy` se v kontejneru vytvoří automaticky, pokud ještě neexistuje. V tomto kurzu se název souboru generuje dynamicky pomocí výrazu `@CONCAT('Incremental-', pipeline().RunId, '.txt')`.

2. Spuštěním rutiny **Set-AzureRmDataFactoryV2Dataset** vytvořte datovou sadu SinkDataset.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Tady je ukázkový výstup této rutiny:
    
    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset    
    ```

## <a name="create-a-dataset-for-a-watermark"></a>Vytvoření datové sady pro mez
V tomto kroku vytvoříte datovou sadu pro uložení hodnoty horní meze. 

1. Ve stejné složce vytvořte soubor JSON s názvem WatermarkDataset.json a s následujícím obsahem: 

    ```json
    {
        "name": " WatermarkDataset ",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "watermarktable"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }    
    ```
2.  Spuštěním rutiny **Set-AzureRmDataFactoryV2Dataset** vytvořte datovou sadu WatermarkDataset.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "WatermarkDataset" -File ".\WatermarkDataset.json"
    ```

    Tady je ukázkový výstup této rutiny:
    
    ```json
    DatasetName       : WatermarkDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>Vytvoření kanálu
V tomto kurzu vytvoříte kanál se dvěma aktivitami vyhledávání, jednou aktivitou kopírování a jednou aktivitou uložené procedury, a to všechno zřetězené v jednom kanálu. 


1. Ve stejné složce vytvořte soubor JSON s názvem IncrementalCopyPipeline.json s následujícím obsahem: 

    ```json
    {
        "name": "IncrementalCopyPipeline",
        "properties": {
            "activities": [
                {
                    "name": "LookupOldWaterMarkActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from watermarktable"
                        },
    
                        "dataset": {
                        "referenceName": "WatermarkDataset",
                        "type": "DatasetReference"
                        }
                    }
                },
                {
                    "name": "LookupNewWaterMarkActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select MAX(LastModifytime) as NewWatermarkvalue from data_source_table"
                        },
    
                        "dataset": {
                        "referenceName": "SourceDataset",
                        "type": "DatasetReference"
                        }
                    }
                },
                
                {
                    "name": "IncrementalCopyActivity",
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupNewWaterMarkActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        },
                        {
                            "activity": "LookupOldWaterMarkActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ],
    
                    "inputs": [
                        {
                            "referenceName": "SourceDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "SinkDataset",
                            "type": "DatasetReference"
                        }
                    ]
                },
    
                {
                    "name": "StoredProceduretoWriteWatermarkActivity",
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
    
                        "storedProcedureName": "sp_write_watermark",
                        "storedProcedureParameters": {
                            "LastModifiedtime": {"value": "@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}", "type": "datetime" },
                            "TableName":  { "value":"@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}", "type":"String"}
                        }
                    },
    
                    "linkedServiceName": {
                        "referenceName": "AzureSQLDatabaseLinkedService",
                        "type": "LinkedServiceReference"
                    },
    
                    "dependsOn": [
                        {
                            "activity": "IncrementalCopyActivity",
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
    

2. Spuštěním rutiny **Set-AzureRmDataFactoryV2Pipeline** vytvořte kanál IncrementalCopyPipeline.
    
   ```powershell
   Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ``` 

   Zde je ukázkový výstup: 

   ```json
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Activities        : {LookupOldWaterMarkActivity, LookupNewWaterMarkActivity, IncrementalCopyActivity, StoredProceduretoWriteWatermarkActivity}
    Parameters        :
   ```
 
## <a name="run-the-pipeline"></a>Spuštění kanálu

1. Spusťte kanál IncrementalCopyPipeline pomocí rutiny **Invoke-AzureRmDataFactoryV2Pipeline**. Zástupné znaky nahraďte vlastním názvem skupiny prostředků a názvem datové továrny.

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroupName $resourceGroupName -dataFactoryName $dataFactoryName
    ``` 
2. Kontrolujte stav kanálu spuštěním rutiny **Get-AzureRmDataFactoryV2ActivityRun**, dokud neuvidíte všechny aktivity úspěšně spuštěné. Zástupné znaky nahraďte vlastním odpovídajícím časem pro parametry *RunStartedAfter* a *RunStartedBefore*. V tomto kurzu se používá *-RunStartedAfter "2017/09/14"* a *-RunStartedBefore "2017/09/15"*.

    ```powershell
    Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $RunId -RunStartedAfter "<start time>" -RunStartedBefore "<end time>"
    ```

    Zde je ukázkový výstup:
 
    ```json
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupNewWaterMarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {NewWatermarkvalue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:42:42 AM
    ActivityRunEnd    : 9/14/2017 7:42:50 AM
    DurationInMs      : 7777
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupOldWaterMarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {TableName, WatermarkValue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:42:42 AM
    ActivityRunEnd    : 9/14/2017 7:43:07 AM
    DurationInMs      : 25437
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : IncrementalCopyActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, rowsCopied, copyDuration...}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:43:10 AM
    ActivityRunEnd    : 9/14/2017 7:43:29 AM
    DurationInMs      : 19769
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : StoredProceduretoWriteWatermarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {storedProcedureName, storedProcedureParameters}
    Output            : {}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:43:32 AM
    ActivityRunEnd    : 9/14/2017 7:43:47 AM
    DurationInMs      : 14467
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ```

## <a name="review-the-results"></a>Kontrola výsledků

1. V úložišti objektů blob (úložiště jímky) uvidíte, že se data zkopírovala do souboru definovaného parametrem SinkDataset. V aktuálním kurzu má tento soubor název `Incremental- d4bf3ce2-5d60-43f3-9318-923155f61037.txt`. Otevřete tento soubor. Zobrazí se záznamy, které jsou stejné jako data v databázi SQL.

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ``` 
2. Zkontrolujte nejnovější hodnotu z `watermarktable`. Uvidíte, že hodnota meze byla aktualizována.

    ```sql
    Select * from watermarktable
    ```
    
    Zde je ukázkový výstup:
 
    TableName | WatermarkValue
    --------- | --------------
    data_source_table   2017-09-05  8:06:00.000

### <a name="insert-data-into-the-data-source-store-to-verify-delta-data-loading"></a>Vložení dat do úložiště zdroje dat pro ověření načtení rozdílových dat

1. Do databáze SQL (úložiště zdroje dat) vložte nová data.

    ```sql
    INSERT INTO data_source_table
    VALUES (6, 'newdata','9/6/2017 2:23:00 AM')
    
    INSERT INTO data_source_table
    VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
    ``` 

    Aktualizovaná data v databázi SQL vypadají takto:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    6 | newdata | 2017-09-06 02:23:00.000
    7 | newdata | 2017-09-07 09:01:00.000
    ```
2. Znovu spusťte kanál IncrementalCopyPipeline pomocí rutiny **Invoke-AzureRmDataFactoryV2Pipeline**. Zástupné znaky nahraďte vlastním názvem skupiny prostředků a názvem datové továrny.

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroupName $resourceGroupName -dataFactoryName $dataFactoryName
    ```
3. Kontrolujte stav kanálu spuštěním rutiny **Get-AzureRmDataFactoryV2ActivityRun**, dokud neuvidíte všechny aktivity úspěšně spuštěné. Zástupné znaky nahraďte vlastním odpovídajícím časem pro parametry *RunStartedAfter* a *RunStartedBefore*. V tomto kurzu se používá *-RunStartedAfter "2017/09/14"* a *-RunStartedBefore "2017/09/15"*.

    ```powershell
    Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $RunId -RunStartedAfter "<start time>" -RunStartedBefore "<end time>"
    ```

    Zde je ukázkový výstup:
 
    ```json
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupNewWaterMarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {NewWatermarkvalue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:52:26 AM
    ActivityRunEnd    : 9/14/2017 8:52:58 AM
    DurationInMs      : 31758
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupOldWaterMarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {TableName, WatermarkValue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:52:26 AM
    ActivityRunEnd    : 9/14/2017 8:52:52 AM
    DurationInMs      : 25497
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : IncrementalCopyActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, rowsCopied, copyDuration...}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:53:00 AM
    ActivityRunEnd    : 9/14/2017 8:53:20 AM
    DurationInMs      : 20194
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : StoredProceduretoWriteWatermarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {storedProcedureName, storedProcedureParameters}
    Output            : {}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:53:23 AM
    ActivityRunEnd    : 9/14/2017 8:53:41 AM
    DurationInMs      : 18502
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ```
4. V úložišti objektů blob uvidíte, že byl vytvořen další soubor. V tomto kurzu se tento nový jmenuje `Incremental-2fc90ab8-d42c-4583-aa64-755dba9925d7.txt`. Otevřete tento soubor a uvidíte, že obsahuje dva řádky záznamů.

5. Zkontrolujte nejnovější hodnotu z `watermarktable`. Uvidíte, že hodnota meze byla znovu aktualizována.

    ```sql
    Select * from watermarktable
    ```
    Ukázkový výstup: 
    
    TableName | WatermarkValue
    --------- | ---------------
    data_source_table | 2017-09-07 09:01:00.000

     
## <a name="next-steps"></a>Další kroky
V tomto kurzu jste provedli následující kroky: 

> [!div class="checklist"]
> * Příprava úložiště dat pro uložení hodnoty meze 
> * Vytvoření datové továrny
> * Vytvoření propojených služeb 
> * Vytvoření zdroje, jímky a datových sad mezí
> * Vytvoření kanálu
> * Spuštění kanálu
> * Monitorování spuštění kanálu 

V tomto kurzu kanál zkopíroval data z jedné tabulky v databázi SQL do úložiště objektů blob. Přejděte na následující kurz, abyste se dozvěděli o kopírování dat z více tabulek v místní databázi SQL Serveru do databáze SQL. 

> [!div class="nextstepaction"]
>[Přírůstkové načtení dat z více tabulek v SQL Serveru do Azure SQL Database](tutorial-incremental-copy-multiple-tables-powershell.md)



