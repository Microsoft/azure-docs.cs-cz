---
title: Přírůstkové kopírování více tabulek pomocí Azure Data Factory | Microsoft Docs
description: V tomto kurzu vytvoříte kanál Azure Data Factory, který přírůstkově kopíruje rozdílová data z několika tabulek v místní databázi SQL Serveru do databáze Azure SQL.
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
ms.openlocfilehash: 377cb9df8ce09581a5fd5d9d7a7d55368daf44ca
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082440"
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-an-azure-sql-database"></a>Přírůstkové načtení dat z více tabulek v SQL Serveru do databáze Azure SQL
V tomto kurzu vytvoříte Azure Data Factory s kanálem, který načítá rozdílová data z několika tabulek v místním SQL Serveru do databáze Azure SQL.    

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Příprava zdrojového a cílového datového úložiště
> * Vytvoření datové továrny
> * Vytvořte místní prostředí Integration Runtime.
> * Instalace prostředí Integration Runtime 
> * Vytvoření propojených služeb 
> * Vytvoření zdroje, jímky a datových sad mezí
> * Vytvoření a spuštění kanálu a jeho monitorování
> * Kontrola výsledků
> * Přidání nebo aktualizace dat ve zdrojových tabulkách
> * Opakované spuštění kanálu a jeho monitorování
> * Kontrola konečných výsledků

## <a name="overview"></a>Přehled
Tady jsou důležité kroky pro vytvoření tohoto řešení: 

1. **Vyberte sloupec meze**.
    Ve zdrojovém úložišti dat vyberte pro každou tabulku jeden sloupec, který je možné použít k identifikaci nových nebo aktualizovaných záznamů pro každé spuštění. Data v tomto vybraném sloupci (například čas_poslední_změny nebo ID) se při vytváření nebo aktualizaci řádků obvykle zvyšují. Maximální hodnota v tomto sloupci se používá jako horní mez.

2. **Připravte úložiště dat pro uložení hodnoty meze**.   
    V tomto kurzu uložíte hodnotu meze do databáze SQL.

3. **Vytvořte kanál s následujícími aktivitami**: 
    
    a. Vytvořte aktivitu ForEach, která prochází seznam názvů zdrojových tabulek, který je předaný kanálu jako parametr. Pro každou zdrojovou tabulku vyvolá následující aktivity, aby pro tabulku provedl rozdílové načtení.

    b. Vytvořte dvě aktivity vyhledávání. První aktivitu vyhledávání použijte k načtení poslední hodnoty meze. Druhou aktivitu vyhledávání použijte k načtení nové hodnoty meze. Tyto hodnoty meze se předají aktivitě kopírování.

    c. Vytvořte aktivitu kopírování, která ze zdrojového úložiště dat zkopíruje řádky, které ve sloupci horní meze mají hodnotu vyšší, než je stará hodnota meze, a nižší, než je nová hodnota meze. Potom tato rozdílová data zkopíruje ze zdrojového úložiště dat do úložiště Azure Blob Storage jako nový soubor.

    d. Vytvořte aktivitu uložené procedury StoredProcedure, která aktualizuje hodnotu meze pro příští spuštění kanálu. 

    Tady je souhrnný diagram tohoto řešení: 

    ![Přírůstkové načtení dat](media\tutorial-incremental-copy-multiple-tables-powershell\high-level-solution-diagram.png)


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
* **SQL Server**. V tomto kurzu použijete místní databázi SQL Serveru jako zdrojové úložiště dat. 
* **Azure SQL Database**. Použijete databázi SQL jako úložiště dat jímky. Pokud databázi SQL nemáte, přečtěte si téma [Vytvoření databáze Azure SQL](../sql-database/sql-database-get-started-portal.md), kde najdete kroky pro její vytvoření. 

### <a name="create-source-tables-in-your-sql-server-database"></a>Vytvoření zdrojových tabulek v databázi SQL Serveru

1. Otevřete SQL Server Management Studio a připojte se k místní databázi SQL Serveru.

2. V **Průzkumníku serveru** klikněte pravým tlačítkem na databázi a zvolte **Nový dotaz**.

3. Spusťte na databázi následující příkaz SQL, aby se vytvořily tabulky s názvem `customer_table` a `project_table`:

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    
    ```

### <a name="create-destination-tables-in-your-azure-sql-database"></a>Vytvoření cílových tabulek v databázi SQL Azure
1. Otevřete SQL Server Management Studio a připojte se k databázi SQL Serveru.

2. V **Průzkumníku serveru** klikněte pravým tlačítkem na databázi a zvolte **Nový dotaz**.

3. Spusťte na databázi SQL následující příkaz SQL, aby se vytvořily tabulky s názvem `customer_table` a `project_table`:  
    
    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );

    ```

### <a name="create-another-table-in-the-azure-sql-database-to-store-the-high-watermark-value"></a>Vytvoření další tabulky v databázi SQL Azure pro ukládání hodnoty horní meze
1. Spuštěním následujícího příkazu SQL na databázi SQL vytvořte tabulku s názvem `watermarktable` pro uložení hodnoty meze: 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
2. Do tabulky mezí vložte hodnoty počátečních mezí pro obě zdrojové tabulky.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-the-azure-sql-database"></a>Vytvoření uložené procedury v databázi SQL Azure 

Spuštěním následujícího příkazu vytvořte v databázi SQL uloženou proceduru. Tato uložená procedura aktualizuje hodnotu meze po každém spuštění kanálu. 

```sql
CREATE PROCEDURE sp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-the-azure-sql-database"></a>Vytvoření datových typů a dalších uložených procedur v databázi SQL Azure
Spuštěním následujícího dotazu vytvořte v databázi SQL dvě uložené procedury a dva datové typy. Slouží ke slučování dat ze zdrojových tabulek do cílových tabulek.

```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE sp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE sp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END

```

### <a name="azure-powershell"></a>Azure PowerShell
Nainstalujte nejnovější moduly Azure PowerShellu podle pokynů v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/install-azurerm-ps).

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
    >  Aktualizujte název datové továrny tak, aby byl globálně jedinečný. Příklad: ADFIncMultiCopyTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFIncMultiCopyTutorialFactory";
    ```
5. Pokud chcete vytvořit datovou továrnu, spusťte následující rutinu **Set-AzureRmDataFactoryV2**: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

Je třeba počítat s následujícím:

* Název datové továrny musí být globálně jedinečný. Pokud se zobrazí následující chyba, změňte název a zkuste to znovu:

    ```
    The specified Data Factory name 'ADFIncMultiCopyTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Pro vytvoření instancí služby Data Factory musí být uživatelský účet, který použijete pro přihlášení k Azure, členem rolí přispěvatel nebo vlastník nebo správcem předplatného Azure.
* Pokud chcete zobrazit seznam oblastí Azure, ve kterých je služba Data Factory aktuálně dostupná, na následující stránce vyberte oblasti, které vás zajímají, pak rozbalte **Analýza** a vyhledejte **Data Factory:** [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/). Úložiště dat (Azure Storage, databáze SQL atd.) a výpočetní prostředí (Azure HDInsight atd.) používané datovou továrnou mohou být v jiných oblastech.

[!INCLUDE [data-factory-create-install-integration-runtime](../../includes/data-factory-create-install-integration-runtime.md)]



## <a name="create-linked-services"></a>Vytvoření propojených služeb
V datové továrně vytvoříte propojené služby, abyste svá úložiště dat a výpočetní služby spojili s datovou továrnou. V této části vytvoříte propojené služby pro místní databázi SQL Serveru a databázi SQL. 

### <a name="create-the-sql-server-linked-service"></a>Vytvoření propojené služby SQL Serveru
V tomto kroku s datovou továrnou propojíte místní databázi SQL Serveru.

1. Vytvořte soubor JSON s názvem SqlServerLinkedService.json ve složce C:\ADFTutorials\IncCopyMultiTableTutorial s následujícím obsahem. Vyberte správnou část na základě ověřování, které požíváte pro připojení k SQL Serveru. Pokud tyto místní složky ještě neexistují, vytvořte je. 

    > [!IMPORTANT]
    > Vyberte správnou část na základě ověřování, které požíváte pro připojení k SQL Serveru.

    Pokud používáte ověřování SQL, zkopírujte následující definici JSON:

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }
   ```    
    Pokud používáte ověřování Windows, zkopírujte následující definici JSON:

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<server>;Database=<database>;Integrated Security=True"
                },
                "userName": "<user> or <domain>\\<user>",
                "password": {
                    "type": "SecureString",
                    "value": "<password>"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }    
    ```
    > [!IMPORTANT]
    > - Vyberte správnou část na základě ověřování, které požíváte pro připojení k SQL Serveru.
    > - Položku &lt;integration runtime name> nahraďte názvem vašeho prostředí Integration Runtime.
    > - Než soubor uložíte, položky &lt;servername>, &lt;databasename>, &lt;username> a &lt;password> nahraďte odpovídajícími hodnotami pro vaši databázi SQL Serveru.
    > - Pokud v názvu uživatelského účtu nebo serveru potřebujete použít znak lomítko (`\`), použijte řídicí znak (`\`). Příklad: `mydomain\\myuser`.

2. V PowerShellu přepněte do složky C:\ADFTutorials\IncCopyMultiTableTutorial.

3. Spuštěním rutiny **Set-AzureRmDataFactoryV2LinkedService** vytvořte propojenou službu AzureStorageLinkedService. V následujícím příkladu předáte hodnoty pro parametry *ResourceGroupName* a *DataFactoryName*: 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerLinkedService" -File ".\SqlServerLinkedService.json"
    ```

    Tady je ukázkový výstup:

    ```json
    LinkedServiceName : SqlServerLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerLinkedService
    ```

### <a name="create-the-sql-database-linked-service"></a>Vytvoření propojené služby databáze SQL
1. Vytvořte soubor JSON s názvem AzureSQLDatabaseLinkedService.json ve složce C:\ADFTutorials\IncCopyMultiTableTutorial s následujícím obsahem. (Pokud složka ADF ještě neexistuje, vytvořte ji.) Než soubor uložíte, položky &lt;server&gt;, &lt;database name&gt;, &lt;user id&gt; a &lt;password&gt; nahraďte názvem vaší databáze SQL Serveru, názvem databáze, ID uživatele a heslem. 

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "value": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database name>; Persist Security Info=False; User ID=<user name>; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
2. Spuštěním rutiny **Set-AzureRmDataFactoryV2LinkedService** v PowerShellu vytvořte propojenou službu AzureSQLDatabaseLinkedService. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Tady je ukázkový výstup:

    ```json
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

## <a name="create-datasets"></a>Vytvoření datových sad
V tomto kroku vytvoříte datové sady, které představují zdroj dat, cíl dat a místo pro uložení hodnoty meze.

### <a name="create-a-source-dataset"></a>Vytvoření zdrojové datové sady

1. Ve stejné složce vytvořte soubor JSON s názvem SourceDataset.json a s následujícím obsahem: 

    ```json
    {
        "name": "SourceDataset",
        "properties": {
            "type": "SqlServerTable",
            "typeProperties": {
                "tableName": "dummyName"
            },
            "linkedServiceName": {
                "referenceName": "SqlServerLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
   
    ```

    Název tabulky je fiktivní název. Aktivita kopírování v kanálu používá místo načtení celé tabulky dotaz SQL pro načtení dat.

2. Spuštěním rutiny **Set-AzureRmDataFactoryV2Dataset** vytvořte datovou sadu SourceDataset.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Tady je ukázkový výstup této rutiny:
    
    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Vytvoření datové sady jímky

1. Ve stejné složce vytvořte soubor JSON s názvem SinkDataset.json s následujícím obsahem. Element tableName je nastaven kanálem dynamicky za běhu. Aktivita ForEach v kanálu prochází seznam názvů tabulek a při každé iteraci předává název tabulky této datové sadě. 

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": {
                    "value": "@{dataset().SinkTableName}",
                    "type": "Expression"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            },
            "parameters": {
                "SinkTableName": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. Spuštěním rutiny **Set-AzureRmDataFactoryV2Dataset** vytvořte datovou sadu SinkDataset.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Tady je ukázkový výstup této rutiny:
    
    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-a-watermark"></a>Vytvoření datové sady pro mez
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
2. Spuštěním rutiny **Set-AzureRmDataFactoryV2Dataset** vytvořte datovou sadu WatermarkDataset.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "WatermarkDataset" -File ".\WatermarkDataset.json"
    ```

    Tady je ukázkový výstup této rutiny:
    
    ```json
    DatasetName       : WatermarkDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : <data factory name>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>Vytvoření kanálu
Tento kanál dostává jako parametr seznam tabulek. Aktivita ForEach prochází seznam názvů tabulek a provádí následující operace: 

1. Použije aktivitu vyhledávání k načtení původní hodnoty meze (počáteční hodnota nebo hodnota použitá v poslední iteraci).

2. Použije aktivitu vyhledávání k načtení nové hodnoty meze (maximální hodnota sloupce mezí ve zdrojové tabulce).

3. Použije aktivitu kopírování ke kopírování dat, která leží mezi těmito dvěma hodnotami mezí, ze zdrojové databáze do cílové databáze.

4. Použije aktivitu uložené procedury StoredProcedure k aktualizaci staré hodnoty meze, která se má použít v prvním kroku další iterace. 

### <a name="create-the-pipeline"></a>Vytvoření kanálu
1. Ve stejné složce vytvořte soubor JSON s názvem IncrementalCopyPipeline.json s následujícím obsahem: 

    ```json
    {
        "name": "IncrementalCopyPipeline",
        "properties": {
            "activities": [{
    
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
                            "name": "LookupOldWaterMarkActivity",
                            "type": "Lookup",
                            "typeProperties": {
                                "source": {
                                    "type": "SqlSource",
                                    "sqlReaderQuery": "select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'"
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
                                    "sqlReaderQuery": "select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}"
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
                                    "sqlReaderQuery": "select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'"
                                },
                                "sink": {
                                    "type": "SqlSink",
                                    "SqlWriterTableType": "@{item().TableType}",
                                    "SqlWriterStoredProcedureName": "@{item().StoredProcedureNameForMergeOperation}"
                                }
                            },
                            "dependsOn": [{
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
    
                            "inputs": [{
                                "referenceName": "SourceDataset",
                                "type": "DatasetReference"
                            }],
                            "outputs": [{
                                "referenceName": "SinkDataset",
                                "type": "DatasetReference",
                                "parameters": {
                                    "SinkTableName": "@{item().TABLE_NAME}"
                                }
                            }]
                        },
    
                        {
                            "name": "StoredProceduretoWriteWatermarkActivity",
                            "type": "SqlServerStoredProcedure",
                            "typeProperties": {
    
                                "storedProcedureName": "sp_write_watermark",
                                "storedProcedureParameters": {
                                    "LastModifiedtime": {
                                        "value": "@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}",
                                        "type": "datetime"
                                    },
                                    "TableName": {
                                        "value": "@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}",
                                        "type": "String"
                                    }
                                }
                            },
    
                            "linkedServiceName": {
                                "referenceName": "AzureSQLDatabaseLinkedService",
                                "type": "LinkedServiceReference"
                            },
    
                            "dependsOn": [{
                                "activity": "IncrementalCopyActivity",
                                "dependencyConditions": [
                                    "Succeeded"
                                ]
                            }]
                        }
    
                    ]
    
                }
            }],
    
            "parameters": {
                "tableList": {
                    "type": "Object"
                }
            }
        }
    }
    ```
2. Spuštěním rutiny **Set-AzureRmDataFactoryV2Pipeline** vytvořte kanál IncrementalCopyPipeline.
    
   ```powershell
   Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ``` 

   Tady je ukázkový výstup: 

   ```json
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```
 
## <a name="run-the-pipeline"></a>Spuštění kanálu

1. Ve stejné složce vytvořte soubor parametrů s názvem Parameters.json s následujícím obsahem:

    ```json
    {
        "tableList": 
        [
            {
                "TABLE_NAME": "customer_table",
                "WaterMark_Column": "LastModifytime",
                "TableType": "DataTypeforCustomerTable",
                "StoredProcedureNameForMergeOperation": "sp_upsert_customer_table"
            },
            {
                "TABLE_NAME": "project_table",
                "WaterMark_Column": "Creationtime",
                "TableType": "DataTypeforProjectTable",
                "StoredProcedureNameForMergeOperation": "sp_upsert_project_table"
            }
        ]
    }
    ```
2. Spusťte kanál IncrementalCopyPipeline pomocí rutiny **Invoke-AzureRmDataFactoryV2Pipeline**. Zástupné znaky nahraďte vlastním názvem skupiny prostředků a názvem datové továrny.

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"        
    ``` 

## <a name="monitor-the-pipeline"></a>Monitorování kanálu

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyberte **Všechny služby**, spusťte hledání pomocí klíčového slova *Datové továrny* a vyberte **Datové továrny**. 

    ![Nabídka Datové továrny](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-data-factories-menu-1.png)

3. V seznamu datových továren vyhledejte vaši datovou továrnu a vyberte ji. Otevře se stránka **Datová továrna**. 

    ![Vyhledávání datové továrny](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-search-data-factory-2.png)

4. Na stránce **Datová továrna** vyberte **Monitorování a správa**. 

    ![Dlaždice Monitorování a správa](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-monitor-manage-tile-3.png)

5. Na samostatné kartě se otevře **aplikace pro integraci dat**. Zobrazí se všechna spuštění kanálů a jejich stavy. Všimněte si, že stav spuštění kanálu v následujícím příkladu je **Úspěšně**. Parametry předané kanálu můžete zkontrolovat kliknutím na odkaz ve sloupci **Parametry**. Pokud došlo k chybě, uvidíte odkaz ve sloupci **Chyba**. Klikněte na odkaz ve sloupci **Akce**. 

    ![Spuštění kanálu](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-pipeline-runs-4.png)    
6. Po kliknutí na odkaz ve sloupci **Akce** uvidíte následující stránku, která zobrazuje všechna spuštění aktivit pro příslušný kanál: 

    ![Spuštění aktivit](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-activity-runs-5.png)

7. Pokud chcete přejít zpátky k zobrazení **Spuštění kanálu**, vyberte **Kanály**, jak ukazuje obrázek. 

## <a name="review-the-results"></a>Kontrola výsledků
V SQL Server Management Studiu spusťte následující dotazy na cílovou databázi SQL a ověřte, že data byla ze zdrojových tabulek zkopírována do cílových tabulek: 

**Dotaz** 
```sql
select * from customer_table
```

**Výstup**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**Dotaz**

```sql
select * from project_table
```

**Výstup**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**Dotaz**

```sql
select * from watermarktable
```

**Výstup**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

Všimněte si, že hodnoty mezí pro obě tabulky byly aktualizovány. 

## <a name="add-more-data-to-the-source-tables"></a>Přidání dalších dat do zdrojových tabulek

Spusťte následující dotaz na zdrojovou databázi SQL Serveru, aby se aktualizoval stávající řádek v tabulce customer_table. Vložte nový řádek do tabulky project_table. 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>Opětovné spuštění kanálu

1. Nyní spusťte znovu kanálu provedením následujícího příkazu Powershellu:

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupname -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"
    ```
2. Monitorujte spuštění kanálu podle pokynů v části [Monitorování kanálu](#monitor-the-pipeline). Protože stav kanálu je **Probíhá**, najdete v části **Akce** další odkaz akce pro zrušení běhu kanálu. 

    ![Spuštění kanálu se stavem Probíhá](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-pipeline-runs-6.png)

3. Kliknutím na **Aktualizovat** můžete aktualizovat seznam, dokud nebude spuštění kanálu úspěšné. 

    ![Aktualizace spuštění kanálu](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-pipeline-runs-succeded-7.png)

4. Volitelně můžete v části **Akce** vybrat odkaz **Zobrazit spuštění aktivit** a zobrazit si všechna spuštění aktivit související s tímto spuštěním kanálu. 

## <a name="review-the-final-results"></a>Kontrola konečných výsledků
V SQL Server Management Studiu spusťte následující dotazy na cílovou databázi a ověřte, že aktualizovaná/nová data byla ze zdrojových tabulek zkopírována do cílových tabulek. 

**Dotaz** 
```sql
select * from customer_table
```

**Výstup**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

Všimněte si nových hodnot položek **Name** a **LastModifytime** pro **PersonID** pro číslo 3. 

**Dotaz**

```sql
select * from project_table
```

**Výstup**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

Všimněte si, že do tabulky project_table byla přidána položka **NewProject**. 

**Dotaz**

```sql
select * from watermarktable
```

**Výstup**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

Všimněte si, že hodnoty mezí pro obě tabulky byly aktualizovány.
     
## <a name="next-steps"></a>Další kroky
V tomto kurzu jste provedli následující kroky: 

> [!div class="checklist"]
> * Příprava zdrojového a cílového datového úložiště
> * Vytvoření datové továrny
> * Vytvoření místního prostředí Integration Runtime (IR)
> * Instalace prostředí Integration Runtime
> * Vytvoření propojených služeb 
> * Vytvoření zdroje, jímky a datových sad mezí
> * Vytvoření a spuštění kanálu a jeho monitorování
> * Kontrola výsledků
> * Přidání nebo aktualizace dat ve zdrojových tabulkách
> * Opakované spuštění kanálu a jeho monitorování
> * Kontrola konečných výsledků

Pokud se chcete dozvědět víc o transformaci dat pomocí clusteru Spark v Azure, přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Přírůstkové načtení dat ze služby Azure SQL Database do úložiště Azure Blob Storage pomocí technologie Change Tracking](tutorial-incremental-copy-change-tracking-feature-powershell.md)


