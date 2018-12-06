---
title: Přírůstkové kopírování dat pomocí technologie Change Tracking a Azure Data Factory | Dokumentace Microsoftu
description: 'V tomto kurzu vytvoříte kanál Azure Data Factory, který přírůstkově kopíruje rozdílová data z několika tabulek v místní databázi SQL Serveru do databáze Azure SQL. '
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: yexu
ms.openlocfilehash: be08740024e87179a48f3dfd6f8406fa6a2bbca6
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52963517"
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-change-tracking-information"></a>Přírůstkové kopírování dat z Azure SQL Database do Azure Blob Storage s využitím informací sledování změn 
V tomto kurzu vytvoříte datovou továrnu Azure s kanálem, který načítá rozdílová data na základě **sledování změn** ve zdrojové databázi Azure SQL do úložiště objektů blob Azure.  

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Příprava zdrojového úložiště dat
> * Vytvoření datové továrny
> * Vytvoření propojených služeb 
> * Vytvoření datových sad pro zdroj, jímku a sledování změn
> * Vytvoření, spuštění a monitorování kanálu úplného kopírování
> * Přidání nebo aktualizace dat ve zdrojové tabulce
> * Vytvoření, spuštění a monitorování kanálu přírůstkového kopírování

## <a name="overview"></a>Přehled
V řešení integrace dat je přírůstkové načítání dat po počátečním načtení dat často používaný scénář. V některých případech je změněná data ve vašem zdrojovém úložišti dat za určité období snadno rozdělit (například LastModifyTime, CreationTime). V některých případech ale neexistuje žádný explicitní způsob identifikace rozdílových dat od posledního zpracování dat. K identifikaci rozdílových dat je možné využít technologii Change Tracking, kterou podporují úložiště dat, jako je Azure SQL Database a SQL Server.  Tento kurz popisuje využití služby Azure Data Factory s technologií SQL Change Tracking k přírůstkovému načtení rozdílových dat z Azure SQL Database do Azure Blob Storage.  Další konkrétnější informace o technologii SQL Change Tracking najdete v článku věnovaném [sledování změn na SQL Serveru](/sql/relational-databases/track-changes/about-change-tracking-sql-server). 

## <a name="end-to-end-workflow"></a>Ucelený pracovní postup
Tady jsou obvyklé kroky uceleného pracovního postupu pro přírůstkové načtení dat s využitím technologie Change Tracking.

> [!NOTE]
> Azure SQL Database i SQL Server podporují technologii Change Tracking. Tento kurz využívá Azure SQL Database jako zdrojové úložiště dat. Můžete také využít místní SQL Server. 

1. **Počáteční načtení historických dat** (spouští se jednou):
    1. Povolte technologii Change Tracking ve zdrojové databázi Azure SQL.
    2. Získejte počáteční hodnotu SYS_CHANGE_VERSION v databázi Azure SQL jako základ pro zaznamenávání změněných dat.
    3. Načtěte kompletní data z Azure SQL Database do Azure Blob Storage. 
2. **Přírůstkové načítání rozdílových dat podle plánu** (spouští se pravidelně po počátečním načtení dat):
    1. Získejte starou a novou hodnotu SYS_CHANGE_VERSION.
    3. Načtěte rozdílová data připojením primárních klíčů změněných řádků (mezi dvěma hodnotami SYS_CHANGE_VERSION) ze **sys.change_tracking_tables** k datům ve **zdrojové tabulce** a potom přesuňte rozdílová data do cíle.
    4. Aktualizujte SYS_CHANGE_VERSION pro příští rozdílové načtení.

## <a name="high-level-solution"></a>Řešení na nejvyšší úrovni
V tomto kurzu vytvoříte dva kanály, které provádějí následující dvě operace:  

1. **Počáteční načtení:** Vytvoříte kanál s aktivitou kopírování, která zkopíruje všechna data ze zdrojového úložiště dat (Azure SQL Database) do cílového úložiště dat (Azure Blob Storage).

    ![Úplné načtení dat](media/tutorial-incremental-copy-change-tracking-feature-powershell/full-load-flow-diagram.png)
1.  **Přírůstkové načtení:** Vytvoříte kanál s následujícími aktivitami a pravidelně ho budete spouštět. 
    1. Vytvořte **dvě aktivity vyhledávání** pro získání staré a nové hodnoty SYS_CHANGE_VERSION z Azure SQL Database a jejich předání aktivitě kopírování.
    2. Vytvořte **jednu aktivitu kopírování** pro zkopírování vložených/aktualizovaných/odstraněných dat mezi dvěma hodnotami SYS_CHANGE_VERSION z Azure SQL Database do Azure Blob Storage.
    3. Vytvořte **jednu aktivitu uložených procedur** pro aktualizaci hodnoty SYS_CHANGE_VERSION pro další spuštění kanálu.

    ![Diagram toku přírůstkového načtení](media/tutorial-incremental-copy-change-tracking-feature-powershell/incremental-load-flow-diagram.png)


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
* Azure PowerShell Nainstalujte nejnovější moduly Azure PowerShellu podle pokynů v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/install-azurerm-ps).
* **Azure SQL Database**. Tuto databázi použijete jako **zdrojové** úložiště dat. Pokud Azure SQL Database nemáte, přečtěte si článek věnovaný [vytvoření databáze Azure SQL](../sql-database/sql-database-get-started-portal.md), kde najdete kroky pro její vytvoření.
* **Účet služby Azure Storage**. Úložiště objektů blob použijete jako úložiště dat **jímky**. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../storage/common/storage-quickstart-create-account.md), kde najdete kroky pro jeho vytvoření. Vytvořte kontejner s názvem **adftutorial**. 

### <a name="create-a-data-source-table-in-your-azure-sql-database"></a>Vytvoření tabulky zdroje dat v databázi Azure SQL
1. Spusťte **SQL Server Management Studio** a připojte se k serveru SQL Azure. 
2. V **Průzkumníku serveru** klikněte pravým tlačítkem na **databázi** a potom zvolte **Nový dotaz**.
3. Spuštěním následujícího příkazu SQL na vaší databázi Azure SQL vytvořte tabulku s názvem `data_source_table` jako úložiště zdroje dat.  
    
    ```sql
    create table data_source_table
    (
        PersonID int NOT NULL,
        Name varchar(255),
        Age int
        PRIMARY KEY (PersonID)
    );

    INSERT INTO data_source_table
        (PersonID, Name, Age)
    VALUES
        (1, 'aaaa', 21),
        (2, 'bbbb', 24),
        (3, 'cccc', 20),
        (4, 'dddd', 26),
        (5, 'eeee', 22);

    ```
4. Povolte mechanismus **Change Tracking** pro vaši databázi a zdrojovou tabulku (data_source_table) spuštěním následujícího příkazu SQL: 

    > [!NOTE]
    > - Místo &lt;your database name&gt; použijte název databáze Azure SQL, která má data_source_table. 
    > - V uvedeném příkladě se změněná data uchovávají po dobu dvou dnů. Pokud načtete změněná data vždy po třech nebo více dnech, některá změněná data nejsou zahrnuta.  Musíte změnit hodnotu CHANGE_RETENTION a použít větší číslo. Další možností je zajistit, že interval načítání změněných dat spadá do dvou dnů. Další informace najdete v tématu věnovaném [povolení sledování změn pro databázi](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server#enable-change-tracking-for-a-database).
 
    ```sql
    ALTER DATABASE <your database name>
    SET CHANGE_TRACKING = ON  
    (CHANGE_RETENTION = 2 DAYS, AUTO_CLEANUP = ON)  
  
    ALTER TABLE data_source_table
    ENABLE CHANGE_TRACKING  
    WITH (TRACK_COLUMNS_UPDATED = ON)
    ```
5. Vytvořte novou tabulku a uložte ChangeTracking_version s výchozí hodnotou spuštěním následujícího dotazu: 

    ```sql
    create table table_store_ChangeTracking_version
    (
        TableName varchar(255),
        SYS_CHANGE_VERSION BIGINT,
    );

    DECLARE @ChangeTracking_version BIGINT
    SET @ChangeTracking_version = CHANGE_TRACKING_CURRENT_VERSION();  

    INSERT INTO table_store_ChangeTracking_version
    VALUES ('data_source_table', @ChangeTracking_version)
    ```
    
    > [!NOTE]
    > Pokud se data po povolení sledování změn pro SQL Database nezmění, hodnota verze sledování změn je 0.
6. Spuštěním následujícího dotazu ve vaší databázi Azure SQL vytvořte uloženou proceduru. Kanál vyvolá tuto uloženou proceduru pro aktualizaci verze sledování změn v tabulce, kterou jste vytvořili v předchozím kroku. 

    ```sql
    CREATE PROCEDURE Update_ChangeTracking_Version @CurrentTrackingVersion BIGINT, @TableName varchar(50)
    AS
    
    BEGIN
    
        UPDATE table_store_ChangeTracking_version
        SET [SYS_CHANGE_VERSION] = @CurrentTrackingVersion
    WHERE [TableName] = @TableName
    
    END    
    ```

### <a name="azure-powershell"></a>Azure PowerShell
Nainstalujte nejnovější moduly Azure PowerShellu podle pokynů v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/install-azurerm-ps).

## <a name="create-a-data-factory"></a>Vytvoření datové továrny
1. Definujte proměnnou pro název skupiny prostředků, kterou použijete později v příkazech PowerShellu. Zkopírujte do PowerShellu následující text příkazu, zadejte název [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md) v uvozovkách a pak příkaz spusťte. Například: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Pokud již skupina prostředků existuje, nepřepisujte ji. Přiřaďte proměnné `$resourceGroupName` jinou hodnotu a spusťte tento příkaz znovu.
2. Definujte proměnnou pro umístění datové továrny: 

    ```powershell
    $location = "East US"
    ```
3. Pokud chcete vytvořit skupinu prostředků Azure, spusťte následující příkaz: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 
    Pokud již skupina prostředků existuje, nepřepisujte ji. Přiřaďte proměnné `$resourceGroupName` jinou hodnotu a spusťte tento příkaz znovu. 
3. Definujte proměnnou název datové továrny. 

    > [!IMPORTANT]
    >  Aktualizujte název datové továrny tak, aby byl globálně jedinečný.  

    ```powershell
    $dataFactoryName = "IncCopyChgTrackingDF";
    ```
5. Pokud chcete vytvořit datovou továrnu, spusťte následující rutinu **Set-AzureRmDataFactoryV2**: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

Je třeba počítat s následujícím:

* Název objektu pro vytváření dat Azure musí být globálně jedinečný. Pokud se zobrazí následující chyba, změňte název a zkuste to znovu.

    ```
    The specified Data Factory name 'ADFIncCopyChangeTrackingTestFactory' is already in use. Data Factory names must be globally unique.
    ```
* Pro vytvoření instancí Data Factory musí být uživatelský účet, který použijete pro přihlášení k Azure, členem rolí **přispěvatel** nebo **vlastník** nebo **správcem** předplatného Azure.
* Pokud chcete zobrazit seznam oblastí Azure, ve kterých je služba Data Factory aktuálně dostupná, na následující stránce vyberte oblasti, které vás zajímají, pak rozbalte **Analýza** a vyhledejte **Data Factory:** [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/). Úložiště dat (Azure Storage, Azure SQL Database atd.) a výpočetní prostředí (HDInsight atd.) používané datovou továrnou mohou být v jiných oblastech.


## <a name="create-linked-services"></a>Vytvoření propojených služeb
V datové továrně vytvoříte propojené služby, abyste svá úložiště dat a výpočetní služby spojili s datovou továrnou. V této části vytvoříte propojené služby pro účet Azure Storage a databázi Azure SQL. 

### <a name="create-azure-storage-linked-service"></a>Vytvoření propojené služby Azure Storage
V tomto kroku s datovou továrnou propojíte svůj účet služby Azure Storage.

1. Vytvořte soubor JSON s názvem **AzureStorageLinkedService.json** ve složce **C:\ADFTutorials\IncCopyChangeTrackingTutorial** s následujícím obsahem. (Pokud tato složka ještě neexistuje, vytvořte ji.) Než soubor uložíte, položky `<accountName>` a `<accountKey>` nahraďte názvem svého účtu úložiště Azure a jeho klíčem.

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
2. V **Azure PowerShellu** přepněte do složky **C:\ADFTutorials\IncCopyChgTrackingTutorial**.
3. Spuštěním rutiny **Set-AzureRmDataFactoryV2LinkedService** vytvořte propojenou službu **AzureStorageLinkedService**. V následujícím příkladu předáte hodnoty pro parametry **ResourceGroupName** a **DataFactoryName**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    Tady je ukázkový výstup:

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-azure-sql-database-linked-service"></a>Vytvoření propojené služby Azure SQL Database
V tomto kroku propojíte databázi SQL Azure s datovou továrnou.

1. Vytvořte soubor JSON s názvem **AzureSQLDatabaseLinkedService.json** ve složce **C:\ADFTutorials\IncCopyChangeTrackingTutorial** s následujícím obsahem: Místo hodnot **&lt;server&gt; &lt;database name&gt;, &lt;user id&gt; a &lt;password&gt;** použijte název vašeho serveru SQL Azure, název vaší databáze, ID uživatele a heslo a potom soubor uložte. 

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
2. Spuštěním rutiny **Set-AzureRmDataFactoryV2LinkedService** v **Azure PowerShellu** vytvořte propojenou službu **AzureSQLDatabaseLinkedService**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Tady je ukázkový výstup:

    ```json
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

## <a name="create-datasets"></a>Vytvoření datových sad
V tomto kroku vytvoříte datové sady, které reprezentují zdroj a cíl dat a místo pro uložení SYS_CHANGE_VERSION.

### <a name="create-a-source-dataset"></a>Vytvoření zdrojové datové sady
V tomto kroku vytvoříte datovou sadu pro reprezentaci zdrojových dat. 

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

2.  Spuštěním rutiny Set-AzureRmDataFactoryV2Dataset vytvořte datovou sadu SourceDataset.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Tady je ukázkový výstup této rutiny:
    
    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Vytvoření datové sady jímky
V tomto kroku vytvoříte datovou sadu pro reprezentaci dat, která se kopírují ze zdrojového úložiště dat. 

1. Ve stejné složce vytvořte soubor JSON s názvem SinkDataset.json a s následujícím obsahem: 

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/incchgtracking",
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

    Jako součást požadavků ve službě Azure Blob Storage vytvoříte kontejner adftutorial. Pokud tento kontejner neexistuje, vytvořte ho nebo použijte název existujícího kontejneru. V tomto kurzu se název výstupního souboru generuje dynamicky pomocí výrazu @CONCAT('Incremental-', pipeline().RunId, '.txt').
2.  Spuštěním rutiny Set-AzureRmDataFactoryV2Dataset vytvořte datovou sadu SinkDataset.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Tady je ukázkový výstup této rutiny:
    
    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

### <a name="create-a-change-tracking-dataset"></a>Vytvoření datové sady sledování změn
V tomto kroku vytvoříte datovou sadu pro uložení verze sledování změn.  

1. Ve stejné složce vytvořte soubor JSON s názvem ChangeTrackingDataset.json a s následujícím obsahem: 

    ```json
    {
        "name": " ChangeTrackingDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "table_store_ChangeTracking_version"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
    ```

    Jako součást požadavků vytvoříte tabulku table_store_ChangeTracking_version.
2.  Spuštěním rutiny Set-AzureRmDataFactoryV2Dataset vytvořte datovou sadu WatermarkDataset.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "ChangeTrackingDataset" -File ".\ChangeTrackingDataset.json"
    ```

    Tady je ukázkový výstup této rutiny:
    
    ```json
    DatasetName       : ChangeTrackingDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

## <a name="create-a-pipeline-for-the-full-copy"></a>Vytvoření kanálu pro úplné kopírování
V tomto kroku vytvoříte kanál s aktivitou kopírování, která zkopíruje všechna data ze zdrojového úložiště dat (Azure SQL Database) do cílového úložiště dat (Azure Blob Storage).

1. Ve stejné složce vytvořte soubor JSON s názvem FullCopyPipeline.json a s následujícím obsahem: 

    ```json
    {
        "name": "FullCopyPipeline",
        "properties": {
            "activities": [{
                "name": "FullCopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
    
                "inputs": [{
                    "referenceName": "SourceDataset",
                    "type": "DatasetReference"
                }],
                "outputs": [{
                    "referenceName": "SinkDataset",
                    "type": "DatasetReference"
                }]
            }]
        }
    }
    ```
2. Spuštěním rutiny Set-AzureRmDataFactoryV2Pipeline vytvořte kanál FullCopyPipeline.
    
   ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "FullCopyPipeline" -File ".\FullCopyPipeline.json"
   ``` 

   Tady je ukázkový výstup: 

   ```json
    PipelineName      : FullCopyPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Activities        : {FullCopyActivity}
    Parameters        :
   ```
 
### <a name="run-the-full-copy-pipeline"></a>Spuštění kanálu úplného kopírování
Kanál **FullCopyPipeline** spusťte pomocí rutiny **Invoke-AzureRmDataFactoryV2Pipeline**. 

```powershell
Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "FullCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName        
``` 

### <a name="monitor-the-full-copy-pipeline"></a>Monitorování kanálu úplného kopírování

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Klikněte na **Všechny služby**, spusťte hledání pomocí klíčového slova `data factories` a vyberte **Datové továrny**. 

    ![Nabídka Datové továrny](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-data-factories-menu-1.png)
3. V seznamu datových továren vyhledejte **vaši datovou továrnu** a vyberte ji, spustí se stránka Datová továrna. 

    ![Vyhledávání datové továrny](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-search-data-factory-2.png)
4. Na stránce Datové továrny klikněte na dlaždici **Monitorování a správa**. 

    ![Dlaždice Monitorování a správa](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-monitor-manage-tile-3.png)    
5. Na samostatné kartě se spustí **aplikace pro integraci dat**. Zobrazí se všechna **spuštění kanálů** a jejich stavy. Všimněte si, že stav spuštění kanálu v následujícím příkladu je **Úspěšně**. Parametry předané kanálu můžete zkontrolovat kliknutím na sloupec **Parametry**. Pokud došlo k chybě, zobrazí se odkaz ve sloupci **Chyba**. Klikněte na odkaz ve sloupci **Akce**. 

    ![Spuštění kanálu](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-pipeline-runs-4.png)    
6. Po kliknutí na odkaz ve sloupci **Akce** uvidíte následující stránku, která zobrazuje všechna **spuštění aktivit** pro příslušný kanál. 

    ![Spuštění aktivit](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-activity-runs-5.png)
7. Pokud chcete přejít zpátky k zobrazení **Spuštění kanálu**, klikněte na **Kanály**, jak ukazuje obrázek. 


### <a name="review-the-results"></a>Kontrola výsledků
Ve složce `incchgtracking` kontejneru `adftutorial` uvidíte soubor s názvem `incremental-<GUID>.txt`. 

![Výstupní soubor pro úplné kopírování](media/tutorial-incremental-copy-change-tracking-feature-powershell/full-copy-output-file.png)

Tento soubor by měl obsahovat data z databáze SQL Azure:

```
1,aaaa,21
2,bbbb,24
3,cccc,20
4,dddd,26
5,eeee,22
```

## <a name="add-more-data-to-the-source-table"></a>Přidání dalších dat do zdrojové tabulky

Spusťte na databázi Azure SQL následující dotaz pro přidání a aktualizaci řádku. 

```sql
INSERT INTO data_source_table
(PersonID, Name, Age)
VALUES
(6, 'new','50');


UPDATE data_source_table
SET [Age] = '10', [name]='update' where [PersonID] = 1

``` 

## <a name="create-a-pipeline-for-the-delta-copy"></a>Vytvoření kanálu pro rozdílové kopírování
V tomto kroku vytvoříte kanál s následujícími aktivitami a pravidelně ho budete spouštět. **Aktivity vyhledávání** získají starou a novou hodnoty SYS_CHANGE_VERSION z Azure SQL Database a předají je aktivitě kopírování. **Aktivita kopírování** zkopíruje vložená/aktualizovaná/odstraněná data mezi dvěma hodnotami SYS_CHANGE_VERSION z Azure SQL Database do Azure Blob Storage. **Aktivita uložených procedur** aktualizuje hodnotu SYS_CHANGE_VERSION pro další spuštění kanálu.

1. Ve stejné složce vytvořte soubor JSON s názvem IncrementalCopyPipeline.json a s následujícím obsahem: 

    ```json
    {
            "name": "IncrementalCopyPipeline",
            "properties": {
                "activities": [
                {
                        "name": "LookupLastChangeTrackingVersionActivity",
                        "type": "Lookup",
                        "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select * from table_store_ChangeTracking_version"
                            },
        
                            "dataset": {
                            "referenceName": "ChangeTrackingDataset",
                            "type": "DatasetReference"
                            }
                        }
                    },
                    {
                        "name": "LookupCurrentChangeTrackingVersionActivity",
                        "type": "Lookup",
                        "typeProperties": {
                            "source": {
                                "type": "SqlSource",
                                "sqlReaderQuery": "SELECT CHANGE_TRACKING_CURRENT_VERSION() as CurrentChangeTrackingVersion"
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
                                "sqlReaderQuery": "select data_source_table.PersonID,data_source_table.Name,data_source_table.Age, CT.SYS_CHANGE_VERSION, SYS_CHANGE_OPERATION from data_source_table RIGHT OUTER JOIN CHANGETABLE(CHANGES data_source_table, @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.SYS_CHANGE_VERSION}) as CT on data_source_table.PersonID = CT.PersonID where CT.SYS_CHANGE_VERSION <= @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}"
                            },
                            "sink": {
                                "type": "BlobSink"
                            }
                        },
                        "dependsOn": [
                            {
                                "activity": "LookupLastChangeTrackingVersionActivity",
                                "dependencyConditions": [
                                    "Succeeded"
                                ]
                            },
                            {
                                "activity": "LookupCurrentChangeTrackingVersionActivity",
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
                        "name": "StoredProceduretoUpdateChangeTrackingActivity",
                        "type": "SqlServerStoredProcedure",
                        "typeProperties": {
    
                            "storedProcedureName": "Update_ChangeTracking_Version",
                            "storedProcedureParameters": {
                            "CurrentTrackingVersion": {"value": "@{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}", "type": "INT64" },
                                "TableName":  { "value":"@{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.TableName}", "type":"String"}
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
2. Spuštěním rutiny Set-AzureRmDataFactoryV2Pipeline vytvořte kanál FullCopyPipeline.
    
   ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ``` 

   Tady je ukázkový výstup: 

   ```json
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Activities        : {LookupLastChangeTrackingVersionActivity, LookupCurrentChangeTrackingVersionActivity, IncrementalCopyActivity, StoredProceduretoUpdateChangeTrackingActivity}
    Parameters        :
   ```

### <a name="run-the-incremental-copy-pipeline"></a>Spuštění kanálu přírůstkového kopírování
Kanál **IncrementalCopyPipeline** spusťte pomocí rutiny **Invoke-AzureRmDataFactoryV2Pipeline**. 

```powershell
Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName     
``` 


### <a name="monitor-the-incremental-copy-pipeline"></a>Monitorování kanálu přírůstkového kopírování
1. V **aplikaci pro integraci dat** aktualizujte zobrazení **spuštění kanálu**. Zkontrolujte, že se v tomto seznamu zobrazuje IncrementalCopyPipeline. Klikněte na odkaz ve sloupci **Akce**.  

    ![Spuštění kanálu](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-pipeline-runs-6.png)    
2. Po kliknutí na odkaz ve sloupci **Akce** uvidíte následující stránku, která zobrazuje všechna **spuštění aktivit** pro příslušný kanál. 

    ![Spuštění aktivit](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-activity-runs-7.png)
3. Pokud chcete přejít zpátky k zobrazení **Spuštění kanálu**, klikněte na **Kanály**, jak ukazuje obrázek. 

### <a name="review-the-results"></a>Kontrola výsledků
Ve složce `incchgtracking` kontejneru `adftutorial` uvidíte druhý soubor. 

![Výstupní soubor pro přírůstkové kopírování](media/tutorial-incremental-copy-change-tracking-feature-powershell/incremental-copy-output-file.png)

Tento soubor by měl obsahovat jenom rozdílová data z databáze SQL Azure. Záznam s `U` je aktualizovaný řádek v databázi a `I` je přidaný řádek. 

```
1,update,10,2,U
6,new,50,1,I
```
První tři sloupce představují změněná data z data_source_table. Poslední dva sloupce jsou metadata ze systémové tabulky sledování změn. Čtvrtý sloupec je SYS_CHANGE_VERSION pro každý změněný řádek. Pátý řádek představuje operaci: U = aktualizace, I = vložení.  Podrobné informace o sledování změn najdete v tématu [CHANGETABLE](/sql/relational-databases/system-functions/changetable-transact-sql). 

```
==================================================================
PersonID Name    Age    SYS_CHANGE_VERSION    SYS_CHANGE_OPERATION
==================================================================
1        update  10     2                     U
6        new     50     1                     I
```

    
## <a name="next-steps"></a>Další kroky
Pokud se chcete dozvědět víc o transformaci dat pomocí clusteru Spark v Azure, přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Transformace dat pomocí clusteru Spark v cloudu](tutorial-transform-data-spark-powershell.md)



