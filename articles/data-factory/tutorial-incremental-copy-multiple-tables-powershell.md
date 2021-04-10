---
title: Přírůstkové kopírování více tabulek pomocí PowerShellu
description: V tomto kurzu vytvoříte Azure Data Factory s kanálem, který načte rozdílová data z několika tabulek v databázi databáze SQL Server do Azure SQL Database.
ms.author: yexu
author: dearandyxu
ms.reviewer: douglasl, jburchel
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 231f4033b90b152a5893cff93584d9c9bb794321
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783179"
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-azure-sql-database-using-powershell"></a>Přírůstkové načtení dat z více tabulek v SQL Server do Azure SQL Database pomocí prostředí PowerShell

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

V tomto kurzu vytvoříte Azure Data Factory s kanálem, který načte rozdílová data z několika tabulek v databázi databáze SQL Server do Azure SQL Database.    

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Příprava zdrojového a cílového datového úložiště
> * Vytvoření datové továrny
> * Vytvořte místní prostředí Integration Runtime.
> * Instalace prostředí Integration Runtime 
> * Vytvoření propojených služeb 
> * Vytvoření zdroje, jímky a datových sad mezí
> * Vytvoření a spuštění kanálu a jeho monitorování
> * Prohlédněte si výsledky.
> * Přidání nebo aktualizace dat ve zdrojových tabulkách
> * Opakované spuštění kanálu a jeho monitorování
> * Kontrola konečných výsledků

## <a name="overview"></a>Přehled
Tady jsou důležité kroky pro vytvoření tohoto řešení: 

1. **Vyberte sloupec meze**.

    Vyberte jeden sloupec pro každou tabulku ve zdrojovém úložišti dat, ve kterém můžete identifikovat nové nebo aktualizované záznamy pro každé spuštění. Data v tomto vybraném sloupci (například čas_poslední_změny nebo ID) se při vytváření nebo aktualizaci řádků obvykle zvyšují. Maximální hodnota v tomto sloupci se používá jako horní mez.

2. **Připravte úložiště dat pro uložení hodnoty meze**.

    V tomto kurzu uložíte hodnotu meze do databáze SQL.

3. **Vytvořte kanál s následujícími aktivitami**:
    
    a. Vytvořte aktivitu ForEach, která prochází seznam názvů zdrojových tabulek, který je předaný kanálu jako parametr. Pro každou zdrojovou tabulku vyvolá následující aktivity, aby pro tabulku provedl rozdílové načtení.

    b. Vytvořte dvě aktivity vyhledávání. První aktivitu vyhledávání použijte k načtení poslední hodnoty meze. Druhou aktivitu vyhledávání použijte k načtení nové hodnoty meze. Tyto hodnoty meze se předají aktivitě kopírování.

    c. Vytvořte aktivitu kopírování, která ze zdrojového úložiště dat zkopíruje řádky, které ve sloupci horní meze mají hodnotu vyšší, než je stará hodnota meze, a nižší, než je nová hodnota meze. Potom tato rozdílová data zkopíruje ze zdrojového úložiště dat do úložiště Azure Blob Storage jako nový soubor.

    d. Vytvořte aktivitu uložené procedury StoredProcedure, která aktualizuje hodnotu meze pro příští spuštění kanálu. 

    Tady je souhrnný diagram tohoto řešení: 

    ![Přírůstkové načtení dat](media/tutorial-incremental-copy-multiple-tables-powershell/high-level-solution-diagram.png)


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* **SQL Server**. V tomto kurzu použijete databázi SQL Server jako zdrojové úložiště dat. 
* **Azure SQL Database**. Jako úložiště dat jímky použijete databázi v Azure SQL Database. Pokud databázi SQL nemáte, přečtěte si téma [Vytvoření databáze v tématu Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md) , kde najdete kroky pro její vytvoření. 

### <a name="create-source-tables-in-your-sql-server-database"></a>Vytvoření zdrojových tabulek v databázi SQL Serveru

1. Otevřete [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) nebo [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)a připojte se k SQL Server databázi.

2. V **Průzkumník serveru (SSMS)** nebo v **podokně připojení (Azure Data Studio)** klikněte pravým tlačítkem myši na databázi a vyberte možnost **Nový dotaz**.

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

### <a name="create-destination-tables-in-your-azure-sql-database"></a>Vytvoření cílových tabulek v Azure SQL Database

1. Otevřete [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) nebo [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)a připojte se k SQL Server databázi.

2. V **Průzkumník serveru (SSMS)** nebo v **podokně připojení (Azure Data Studio)** klikněte pravým tlačítkem myši na databázi a vyberte možnost **Nový dotaz**.

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
    ```

### <a name="create-another-table-in-azure-sql-database-to-store-the-high-watermark-value"></a>Vytvoření další tabulky v Azure SQL Database k uložení hodnoty horní meze

1. Spusťte následující příkaz SQL pro vaši databázi a vytvořte tabulku s názvem `watermarktable` pro uložení hodnoty meze: 
    
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

### <a name="create-a-stored-procedure-in-the-azure-sql-database"></a>Vytvořit uloženou proceduru v Azure SQL Database 

Spuštěním následujícího příkazu vytvořte v databázi uloženou proceduru. Tato uložená procedura aktualizuje hodnotu meze po každém spuštění kanálu. 

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

UPDATE watermarktable
SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-azure-sql-database"></a>Vytvoření datových typů a dalších uložených procedur v Azure SQL Database

Spusťte následující dotaz pro vytvoření dvou uložených procedur a dvou datových typů ve vaší databázi. Slouží ke slučování dat ze zdrojových tabulek do cílových tabulek. 

Aby bylo možné cestu snadno začít používat, přímo tyto uložené procedury předají rozdílová data v rámci přes proměnnou tabulky a pak je sloučí do cílového úložiště. Buďte opatrní, neočekává se, že "velký" počet rozdílových řádků (více než 100) se uloží do proměnné tabulky.  

Pokud potřebujete sloučit velký počet rozdílových řádků do cílového úložiště, doporučujeme použít aktivitu kopírování ke zkopírování všech rozdílových dat do dočasné tabulky "fázování" v cílovém úložišti a pak vytvořit vlastní uloženou proceduru bez použití proměnné tabulky pro jejich sloučení z "pracovní" tabulky do "konečné" tabulky. 


```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE usp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
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

CREATE PROCEDURE usp_upsert_project_table @project_table DataTypeforProjectTable READONLY
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

Nainstalujte nejnovější moduly Azure PowerShellu podle pokynů v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/azurerm/install-azurerm-ps).

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Definujte proměnnou pro název skupiny prostředků, kterou použijete později v příkazech PowerShellu. Zkopírujte do PowerShellu následující text příkazu, zadejte název [skupiny prostředků Azure](../azure-resource-manager/management/overview.md) v uvozovkách a pak příkaz spusťte. Příklad: `"adfrg"`.

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
    New-AzResourceGroup $resourceGroupName $location
    ``` 
    Pokud již skupina prostředků existuje, nepřepisujte ji. Přiřaďte proměnné `$resourceGroupName` jinou hodnotu a spusťte tento příkaz znovu.

4. Definujte proměnnou název datové továrny. 

    > [!IMPORTANT]
    >  Aktualizujte název datové továrny tak, aby byl globálně jedinečný. Příklad: ADFIncMultiCopyTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFIncMultiCopyTutorialFactory";
    ```
5. Pokud chcete vytvořit datovou továrnu, spusťte následující rutinu **set-AzDataFactoryV2** : 
    
    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

Je třeba počítat s následujícím:

* Název datové továrny musí být globálně jedinečný. Pokud se zobrazí následující chyba, změňte název a zkuste to znovu:

    ```powershell
    Set-AzDataFactoryV2 : HTTP Status Code: Conflict
    Error Code: DataFactoryNameInUse
    Error Message: The specified resource name 'ADFIncMultiCopyTutorialFactory' is already in use. Resource names must be globally unique.
    ```

* Pro vytvoření instancí služby Data Factory musí být uživatelský účet, který použijete pro přihlášení k Azure, členem rolí přispěvatel nebo vlastník nebo správcem předplatného Azure.

* Pokud chcete zobrazit seznam oblastí Azure, ve kterých je služba Data Factory aktuálně dostupná, na následující stránce vyberte oblasti, které vás zajímají, pak rozbalte **Analýza** a vyhledejte **Data Factory:**[Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/). Úložiště dat (Azure Storage, SQL Database, spravovaná instance SQL atd.) a výpočetní prostředí (Azure HDInsight atd.) používané datovou továrnou mohou být v jiných oblastech.

[!INCLUDE [data-factory-create-install-integration-runtime](../../includes/data-factory-create-install-integration-runtime.md)]

## <a name="create-linked-services"></a>Vytvoření propojených služeb

V datové továrně vytvoříte propojené služby, abyste svá úložiště dat a výpočetní služby spojili s datovou továrnou. V této části vytvoříte propojené služby pro SQL Server databázi a databázi v Azure SQL Database. 

### <a name="create-the-sql-server-linked-service"></a>Vytvoření propojené služby SQL Serveru

V tomto kroku propojíte databázi SQL Server s datovou továrnou.

1. Ve složce ve c:\adftutorials\inccopymultitabletutorial vytvořte soubor JSON s názvem **SqlServerLinkedService.js** (vytvořte místní složky, pokud ještě neexistují) s následujícím obsahem. Vyberte správnou část na základě ověřování, které požíváte pro připojení k SQL Serveru.  

    > [!IMPORTANT]
    > Vyberte správnou část na základě ověřování, které požíváte pro připojení k SQL Serveru.

    Pokud používáte ověřování SQL, zkopírujte následující definici JSON:

    ```json
    {  
        "name":"SqlServerLinkedService",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=False;data source=<servername>;initial catalog=<database name>;user id=<username>;Password=<password>"
            },
            "connectVia":{  
                "referenceName":"<integration runtime name>",
                "type":"IntegrationRuntimeReference"
            }
        }
    } 
   ```    
    Pokud používáte ověřování Windows, zkopírujte následující definici JSON:

    ```json
    {  
        "name":"SqlServerLinkedService",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=True;data source=<servername>;initial catalog=<database name>",
                "userName":"<username> or <domain>\\<username>",
                "password":{  
                    "type":"SecureString",
                    "value":"<password>"
                }
            },
            "connectVia":{  
                "referenceName":"<integration runtime name>",
                "type":"IntegrationRuntimeReference"
            }
        }
    }
    ```
    > [!IMPORTANT]
    > - Vyberte správnou část na základě ověřování, které požíváte pro připojení k SQL Serveru.
    > - Položku &lt;integration runtime name> nahraďte názvem vašeho prostředí Integration Runtime.
    > - Než soubor uložíte, položky &lt;servername>, &lt;databasename>, &lt;username> a &lt;password> nahraďte odpovídajícími hodnotami pro vaši databázi SQL Serveru.
    > - Pokud v názvu uživatelského účtu nebo serveru potřebujete použít znak lomítko (`\`), použijte řídicí znak (`\`). Příklad: `mydomain\\myuser`.

2. V prostředí PowerShell spusťte následující rutinu, která přepne do složky ve c:\adftutorials\inccopymultitabletutorial.

    ```powershell
    Set-Location 'C:\ADFTutorials\IncCopyMultiTableTutorial'
    ```

3. Spuštěním rutiny **set-AzDataFactoryV2LinkedService** vytvořte AzureStorageLinkedService propojené služby. V následujícím příkladu předáte hodnoty pro parametry *ResourceGroupName* a *DataFactoryName*: 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerLinkedService" -File ".\SqlServerLinkedService.json"
    ```

    Zde je ukázkový výstup:

    ```console
    LinkedServiceName : SqlServerLinkedService
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerLinkedService
    ```

### <a name="create-the-sql-database-linked-service"></a>Vytvoření propojené služby SQL Database

1. Ve složce ve c:\adftutorials\inccopymultitabletutorial vytvořte soubor JSON s názvem **AzureSQLDatabaseLinkedService.js** s následujícím obsahem. (Pokud ještě neexistuje, vytvořte si ADF složky.) Než soubor uložíte, nahraďte název &lt; serveru &gt; , název &lt; databáze &gt; , &lt; uživatelské jméno &gt; a &lt; heslo &gt; názvem vaší databáze SQL Server, názvem databáze, uživatelským jménem a heslem. 

    ```json
    {  
        "name":"AzureSQLDatabaseLinkedService",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"AzureSqlDatabase",
            "typeProperties":{  
                "connectionString":"integrated security=False;encrypt=True;connection timeout=30;data source=<servername>.database.windows.net;initial catalog=<database name>;user id=<user name>;Password=<password>;"
            }
        }
    }
    ```
2. V PowerShellu spusťte rutinu **set-AzDataFactoryV2LinkedService** , která vytvoří propojenou službu AzureSQLDatabaseLinkedService. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Zde je ukázkový výstup:

    ```console
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

## <a name="create-datasets"></a>Vytvoření datových sad

V tomto kroku vytvoříte datové sady, které představují zdroj dat, cíl dat a místo pro uložení hodnoty meze.

### <a name="create-a-source-dataset"></a>Vytvoření zdrojové datové sady

1. Ve stejné složce vytvořte soubor JSON s názvem **SourceDataset.js** s následujícím obsahem: 

    ```json
    {  
        "name":"SourceDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"SqlServerLinkedService",
                "type":"LinkedServiceReference"
            },
            "annotations":[  
    
            ],
            "type":"SqlServerTable",
            "schema":[  
    
            ]
        }
    }
   
    ```

    Aktivita kopírování v kanálu používá místo načtení celé tabulky dotaz SQL pro načtení dat.

2. Spusťte rutinu **set-AzDataFactoryV2Dataset** , která vytvoří datovou sadu SourceDataset.
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Tady je ukázkový výstup této rutiny:
    
    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Vytvoření datové sady jímky

1. Vytvořte soubor JSON s názvem **SinkDataset.js** ve stejné složce s následujícím obsahem. Element tableName je nastaven kanálem dynamicky za běhu. Aktivita ForEach v kanálu prochází seznam názvů tabulek a při každé iteraci předává název tabulky této datové sadě. 

    ```json
    {  
        "name":"SinkDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"AzureSQLDatabaseLinkedService",
                "type":"LinkedServiceReference"
            },
            "parameters":{  
                "SinkTableName":{  
                    "type":"String"
                }
            },
            "annotations":[  
    
            ],
            "type":"AzureSqlTable",
            "typeProperties":{  
                "tableName":{  
                    "value":"@dataset().SinkTableName",
                    "type":"Expression"
                }
            }
        }
    }
    ```

2. Spusťte rutinu **set-AzDataFactoryV2Dataset** , která vytvoří datovou sadu SinkDataset.
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Tady je ukázkový výstup této rutiny:
    
    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-a-watermark"></a>Vytvoření datové sady pro mez

V tomto kroku vytvoříte datovou sadu pro uložení hodnoty horní meze. 

1. Ve stejné složce vytvořte soubor JSON s názvem **WatermarkDataset.js** s následujícím obsahem: 

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
2. Spusťte rutinu **set-AzDataFactoryV2Dataset** , která vytvoří datovou sadu WatermarkDataset.
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "WatermarkDataset" -File ".\WatermarkDataset.json"
    ```

    Tady je ukázkový výstup této rutiny:
    
    ```json
    DatasetName       : WatermarkDataset
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>Vytvoření kanálu

Tento kanál dostává jako parametr seznam tabulek. **Aktivita foreach** prochází seznam názvů tabulek a provádí následující operace: 

1. **Aktivitu vyhledávání** použijte k načtení původní hodnoty meze (počáteční hodnota nebo ta, která byla použita v poslední iteraci).

2. **Aktivitu vyhledávání** použijte k načtení nové hodnoty meze (maximální hodnota sloupce meze ve zdrojové tabulce).

3. **Aktivitu kopírování** použijte ke kopírování dat mezi těmito dvěma hodnotami meze ze zdrojové databáze do cílové databáze.

4. **Aktivitu StoredProcedure** použijte k aktualizaci staré hodnoty meze, která se má použít v prvním kroku další iterace. 

### <a name="create-the-pipeline"></a>Vytvoření kanálu

1. Ve stejné složce vytvořte soubor JSON s názvem **IncrementalCopyPipeline.js** s následujícím obsahem: 

    ```json
    {  
        "name":"IncrementalCopyPipeline",
        "properties":{  
            "activities":[  
                {  
                    "name":"IterateSQLTables",
                    "type":"ForEach",
                    "dependsOn":[  
    
                    ],
                    "userProperties":[  
    
                    ],
                    "typeProperties":{  
                        "items":{  
                            "value":"@pipeline().parameters.tableList",
                            "type":"Expression"
                        },
                        "isSequential":false,
                        "activities":[  
                            {  
                                "name":"LookupOldWaterMarkActivity",
                                "type":"Lookup",
                                "dependsOn":[  
    
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "source":{  
                                        "type":"AzureSqlSource",
                                        "sqlReaderQuery":{  
                                            "value":"select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'",
                                            "type":"Expression"
                                        }
                                    },
                                    "dataset":{  
                                        "referenceName":"WatermarkDataset",
                                        "type":"DatasetReference"
                                    }
                                }
                            },
                            {  
                                "name":"LookupNewWaterMarkActivity",
                                "type":"Lookup",
                                "dependsOn":[  
    
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "source":{  
                                        "type":"SqlServerSource",
                                        "sqlReaderQuery":{  
                                            "value":"select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}",
                                            "type":"Expression"
                                        }
                                    },
                                    "dataset":{  
                                        "referenceName":"SourceDataset",
                                        "type":"DatasetReference"
                                    },
                                    "firstRowOnly":true
                                }
                            },
                            {  
                                "name":"IncrementalCopyActivity",
                                "type":"Copy",
                                "dependsOn":[  
                                    {  
                                        "activity":"LookupOldWaterMarkActivity",
                                        "dependencyConditions":[  
                                            "Succeeded"
                                        ]
                                    },
                                    {  
                                        "activity":"LookupNewWaterMarkActivity",
                                        "dependencyConditions":[  
                                            "Succeeded"
                                        ]
                                    }
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "source":{  
                                        "type":"SqlServerSource",
                                        "sqlReaderQuery":{  
                                            "value":"select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'",
                                            "type":"Expression"
                                        }
                                    },
                                    "sink":{  
                                        "type":"AzureSqlSink",
                                        "sqlWriterStoredProcedureName":{  
                                            "value":"@{item().StoredProcedureNameForMergeOperation}",
                                            "type":"Expression"
                                        },
                                        "sqlWriterTableType":{  
                                            "value":"@{item().TableType}",
                                            "type":"Expression"
                                        },
                                        "storedProcedureTableTypeParameterName":{  
                                            "value":"@{item().TABLE_NAME}",
                                            "type":"Expression"
                                        },
                                        "disableMetricsCollection":false
                                    },
                                    "enableStaging":false
                                },
                                "inputs":[  
                                    {  
                                        "referenceName":"SourceDataset",
                                        "type":"DatasetReference"
                                    }
                                ],
                                "outputs":[  
                                    {  
                                        "referenceName":"SinkDataset",
                                        "type":"DatasetReference",
                                        "parameters":{  
                                            "SinkTableName":{  
                                                "value":"@{item().TABLE_NAME}",
                                                "type":"Expression"
                                            }
                                        }
                                    }
                                ]
                            },
                            {  
                                "name":"StoredProceduretoWriteWatermarkActivity",
                                "type":"SqlServerStoredProcedure",
                                "dependsOn":[  
                                    {  
                                        "activity":"IncrementalCopyActivity",
                                        "dependencyConditions":[  
                                            "Succeeded"
                                        ]
                                    }
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "storedProcedureName":"[dbo].[usp_write_watermark]",
                                    "storedProcedureParameters":{  
                                        "LastModifiedtime":{  
                                            "value":{  
                                                "value":"@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}",
                                                "type":"Expression"
                                            },
                                            "type":"DateTime"
                                        },
                                        "TableName":{  
                                            "value":{  
                                                "value":"@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}",
                                                "type":"Expression"
                                            },
                                            "type":"String"
                                        }
                                    }
                                },
                                "linkedServiceName":{  
                                    "referenceName":"AzureSQLDatabaseLinkedService",
                                    "type":"LinkedServiceReference"
                                }
                            }
                        ]
                    }
                }
            ],
            "parameters":{  
                "tableList":{  
                    "type":"array"
                }
            },
            "annotations":[  
    
            ]
        }
    }
    ```
2. Spuštěním rutiny **set-AzDataFactoryV2Pipeline** vytvořte IncrementalCopyPipeline kanálu.
    
   ```powershell
   Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ``` 

   Zde je ukázkový výstup: 

   ```console
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```
 
## <a name="run-the-pipeline"></a>Spuštění kanálu

1. Ve stejné složce vytvořte soubor parametrů s názvem **Parameters.js** s následujícím obsahem:

    ```json
    {
        "tableList": 
        [
            {
                "TABLE_NAME": "customer_table",
                "WaterMark_Column": "LastModifytime",
                "TableType": "DataTypeforCustomerTable",
                "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
            },
            {
                "TABLE_NAME": "project_table",
                "WaterMark_Column": "Creationtime",
                "TableType": "DataTypeforProjectTable",
                "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
            }
        ]
    }
    ```
2. Spusťte IncrementalCopyPipeline kanálu pomocí rutiny **Invoke-AzDataFactoryV2Pipeline** . Zástupné znaky nahraďte vlastním názvem skupiny prostředků a názvem datové továrny.

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"        
    ``` 

## <a name="monitor-the-pipeline"></a>Monitorování kanálu

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

2. Vyberte **Všechny služby**, spusťte hledání pomocí klíčového slova *Datové továrny* a vyberte **Datové továrny**. 

3. V seznamu datových továren vyhledejte vaši datovou továrnu a vyberte ji. Otevře se stránka **Datová továrna**. 

4. Na stránce **Datová továrna** vyberte **vytvořit & monitorování** a spusťte Azure Data Factory na samostatné kartě.

5. Na stránce **Začínáme** vyberte **monitor** na levé straně. 
![Snímek obrazovky se stránkou Začínáme pro Azure Data Factory.](media/doc-common-process/get-started-page-monitor-button.png)    

6. Zobrazí se všechna spuštění kanálů a jejich stavy. Všimněte si, že stav spuštění kanálu v následujícím příkladu je **Úspěšně**. Parametry předané kanálu můžete zkontrolovat kliknutím na odkaz ve sloupci **Parametry**. Pokud došlo k chybě, uvidíte odkaz ve sloupci **Chyba**.

    ![Snímek obrazovky ukazuje spuštění kanálu pro datovou továrnu, včetně vašeho kanálu.](media/tutorial-incremental-copy-multiple-tables-powershell/monitor-pipeline-runs-4.png)    
7. Když vyberete odkaz ve sloupci **Akce** , zobrazí se všechna spuštění aktivit pro daný kanál. 

8. Pokud se chcete vrátit do zobrazení **spuštění kanálu** , vyberte **všechna spuštění kanálu**. 

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
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupname -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"
    ```
2. Monitorujte spuštění kanálu podle pokynů v části [Monitorování kanálu](#monitor-the-pipeline). Když stav kanálu **probíhá, zobrazí se v části** **Akce** další odkaz akce pro zrušení běhu kanálu. 

3. Kliknutím na **Aktualizovat** můžete aktualizovat seznam, dokud nebude spuštění kanálu úspěšné. 

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
> * Prohlédněte si výsledky.
> * Přidání nebo aktualizace dat ve zdrojových tabulkách
> * Opakované spuštění kanálu a jeho monitorování
> * Kontrola konečných výsledků

Pokud se chcete dozvědět víc o transformaci dat pomocí clusteru Spark v Azure, přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Přírůstkové načtení dat ze služby Azure SQL Database do úložiště Azure Blob Storage pomocí technologie Change Tracking](tutorial-incremental-copy-change-tracking-feature-powershell.md)
