---
title: Aktivita uložené procedury SQL Server
description: Přečtěte si, jak můžete pomocí aktivity SQL Server uložená procedura vyvolat uloženou proceduru v Azure SQL Database nebo v Azure synapse Analytics z kanálu Data Factory.
services: data-factory
documentationcenter: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
manager: anandsub
robots: noindex
ms.openlocfilehash: 55c884375372b3fea2ff3153aa936893cf668903
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92359981"
---
# <a name="sql-server-stored-procedure-activity"></a>Aktivita uložené procedury SQL Server
> [!div class="op_single_selector" title1="Aktivity transformace"]
> * [Aktivita v podregistru](data-factory-hive-activity.md)
> * [Aktivita prasete](data-factory-pig-activity.md)
> * [Aktivita MapReduce](data-factory-map-reduce.md)
> * [Aktivita streamování Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Aktivita Sparku](data-factory-spark.md)
> * [Aktivita spuštění dávky Azure Machine Learning Studio (Classic)](data-factory-azure-ml-batch-execution-activity.md)
> * [Aktivita aktualizace prostředku Azure Machine Learning Studio (Classic)](data-factory-azure-ml-update-resource-activity.md)
> * [Aktivita Uložená procedura](data-factory-stored-proc-activity.md)
> * [Aktivita U-SQL služby Data Lake Analytics](data-factory-usql-activity.md)
> * [Vlastní aktivita rozhraní .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [transformace dat pomocí aktivity uložená procedura v Data Factory](../transform-data-using-stored-procedure.md).

## <a name="overview"></a>Přehled
Aktivity transformace dat v [kanálu](data-factory-create-pipelines.md) Data Factory slouží k transformaci a zpracování nezpracovaných dat do předpovědi a přehledů. Aktivita uložená procedura je jednou z aktivit transformace, které Data Factory podporuje. Tento článek se sestavuje na článku [aktivity transformace dat](data-factory-data-transformation-activities.md) , který představuje obecný přehled transformace dat a podporovaných transformačních aktivit v Data Factory.

Aktivitu uložené procedury můžete použít k vyvolání uložené procedury v jednom z následujících úložišť dat v podniku nebo na virtuálním počítači Azure (VM):

- Azure SQL Database
- Azure Synapse Analytics (dříve SQL Data Warehouse)
- SQL Server databázi. Pokud používáte SQL Server, nainstalujte Správa dat bránu na stejném počítači, který hostuje databázi, nebo na samostatném počítači, který má přístup k databázi. Správa dat Gateway je komponenta, která připojuje zdroje dat místně nebo na virtuálním počítači Azure s Cloud Services zabezpečeným a spravovaným způsobem. Podrobnosti najdete v článku o [Správa dat brány](data-factory-data-management-gateway.md) .

> [!IMPORTANT]
> Při kopírování dat do Azure SQL Database nebo SQL Server můžete nakonfigurovat **SqlSink** v aktivitě kopírování a vyvolat uloženou proceduru pomocí vlastnosti **sqlWriterStoredProcedureName** . Další informace naleznete v tématu [vyvolání uložené procedury z aktivity kopírování](data-factory-invoke-stored-procedure-from-copy-activity.md). Podrobnosti o této vlastnosti naleznete v následujících článcích konektoru: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties). Volání uložené procedury při kopírování dat do služby Azure synapse Analytics pomocí aktivity kopírování se nepodporuje. Můžete ale použít aktivitu uložená procedura k vyvolání uložené procedury ve službě Azure synapse Analytics.
>
> Při kopírování dat z Azure SQL Database nebo SQL Server nebo Azure synapse Analytics můžete nakonfigurovat **SqlSource** v aktivitě kopírování, aby se vyvolala uložená procedura pro čtení dat ze zdrojové databáze pomocí vlastnosti **sqlReaderStoredProcedureName** . Další informace najdete v následujících článcích konektoru: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties)a [Azure synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) .

Následující návod používá aktivitu uložené procedury v kanálu k vyvolání uložené procedury v Azure SQL Database.

## <a name="walkthrough"></a>Názorný postup
### <a name="sample-table-and-stored-procedure"></a>Ukázková tabulka a uložená procedura
1. Vytvořte v Azure SQL Database následující **tabulku** pomocí SQL Server Management Studio nebo jakéhokoli jiného nástroje, se kterým máte v pohodlí. Sloupec DateTimeStamp je datum a čas, kdy je vygenerováno odpovídající ID.

    ```SQL
    CREATE TABLE dbo.sampletable
    (
        Id uniqueidentifier,
        datetimestamp nvarchar(127)
    )
    GO

    CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
    GO
    ```
    ID je jedinečný identifikovaný a sloupec DateTimeStamp je datum a čas, kdy je vygenerováno odpovídající ID.
    
    ![Ukázková data](./media/data-factory-stored-proc-activity/sample-data.png)

    V této ukázce je uložená procedura v Azure SQL Database. Pokud je uložená procedura ve službě Azure synapse Analytics a v databázi SQL Server, je přístup podobný. U SQL Server databáze je nutné nainstalovat [Správa dat bránu](data-factory-data-management-gateway.md).
2. Vytvořte následující **uloženou proceduru** , která do **vzorové**sady vloží data.

    ```SQL
    CREATE PROCEDURE usp_sample @DateTime nvarchar(127)
    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime)
    END
    ```

   > [!IMPORTANT]
   > **Název** a **velikost písmen** v parametru (DateTime v tomto příkladu) se musí shodovat s parametrem zadaným v kanálu JSON kanálu nebo aktivity. V definici uložené procedury se ujistěte, že **\@** se používá jako předpona pro parametr.

### <a name="create-a-data-factory"></a>Vytvoření datové továrny
1. Přihlaste se k [Azure Portal](https://portal.azure.com/).
2. V nabídce vlevo klikněte na **Nový** , pak na **Intelligence + Analytics**a pak klikněte na **Data Factory**.

    ![Nová datová továrna 1](media/data-factory-stored-proc-activity/new-data-factory.png)
3. V okně **Nová datová továrna** jako název zadejte **SProcDF** . Názvy Azure Data Factory jsou **globálně jedinečné**. Chcete-li povolit úspěšné vytvoření objektu pro vytváření, je třeba vytvořit předponu názvu objektu pro vytváření dat s vaším jménem.

   ![Nová datová továrna 2](media/data-factory-stored-proc-activity/new-data-factory-blade.png)
4. Vyberte své **předplatné Azure**.
5. V části **Skupina prostředků** proveďte jeden z následujících kroků:
   1. Klikněte na **vytvořit nový** a zadejte název skupiny prostředků.
   2. Klikněte na **použít existující** a vyberte existující skupinu prostředků.
6. Vyberte **umístění** pro objekt pro vytváření dat.
7. Vyberte **Připnout na řídicí panel** , abyste mohli datovou továrnu zobrazit na řídicím panelu při příštím přihlášení.
8. V okně **Nový objekt pro vytváření dat** klikněte na **Vytvořit**.
9. Na **řídicím panelu** Azure Portal se zobrazí objekt pro vytváření dat. Po úspěšném vytvoření objektu pro vytváření dat se zobrazí stránka s obsahem objektu pro vytváření dat.

   ![Data Factory domovskou stránku](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Vytvoření propojené služby Azure SQL
Po vytvoření datové továrny vytvoříte propojenou službu Azure SQL, která propojuje vaši databázi v Azure SQL Database, která obsahuje tabulku vzorků a usp_sample uloženou proceduru, do vaší datové továrny.

1. Kliknutím na **vytvořit a nasadit** v okně **Data Factory** pro **SProcDF** spusťte Editor Data Factory.
2. Na panelu příkazů klikněte na **nové úložiště dat** a vyberte **Azure SQL Database**. V editoru by se měl zobrazit skript JSON pro vytvoření propojené služby Azure SQL.

   ![Nové úložiště dat 1](media/data-factory-stored-proc-activity/new-data-store.png)
3. Ve skriptu JSON proveďte následující změny:

   1. Nahraďte `<servername>` názvem vašeho serveru.
   2. Nahraďte `<databasename>` databází, ve které jste vytvořili tabulku a uloženou proceduru.
   3. Nahraďte `<username@servername>` uživatelským účtem, který má přístup k databázi.
   4. Nahraďte `<password>` heslem pro uživatelský účet.

      ![Nové úložiště dat 2](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. Pokud chcete nasazovat propojenou službu, klikněte na panelu příkazů na **nasadit** . Ověřte, že se v zobrazení stromu na levé straně zobrazuje AzureSqlLinkedService.

    ![stromové zobrazení s propojenou službou 1](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Vytvoření výstupní datové sady
Výstupní datovou sadu pro aktivitu uložené procedury musíte zadat i v případě, že uložená procedura nevytváří žádná data. Důvodem je to, že se jedná o výstupní datovou sadu, která řídí plán aktivity (jak často se aktivita spouští – každou hodinu, každý den atd.). Výstupní datová sada musí používat **propojenou službu** , která odkazuje na Azure SQL Database nebo Azure synapse Analytics nebo databáze SQL Server, ve které chcete uloženou proceduru spustit. Výstupní datová sada může sloužit jako způsob, jak předat výsledek uložené procedury pro následné zpracování jinou aktivitou ([řetězení aktivit](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) v kanálu. Data Factory však do této datové sady automaticky nezapisuje výstup uložené procedury. Jedná se o uloženou proceduru, která zapisuje do tabulky SQL, na kterou odkazuje výstupní datová sada. V některých případech může být výstupní datová sada **fiktivní datovou sadu** (datovou sadu, která odkazuje na tabulku, která ve skutečnosti nedrží výstup uložené procedury). Tato fiktivní datová sada se používá pouze k zadání plánu pro spuštění aktivity uložené procedury.

1. Klikněte na **... Na panelu** nástrojů klikněte na **Nová datová sada**a pak na **Azure SQL**. Na panelu příkazů **Nová datová sada** a vyberte **Azure SQL**.

    ![stromové zobrazení s propojenou službou 2](media/data-factory-stored-proc-activity/new-dataset.png)
2. Zkopírujte následující skript JSON do editoru JSON a vložte ho do něj.

    ```JSON
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "sampletable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
3. Pokud chcete nasadit datovou sadu, klikněte na panelu příkazů na **nasadit** . Ověřte, že se ve stromovém zobrazení zobrazuje datová sada.

    ![stromové zobrazení s propojenými službami](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>Vytvoření kanálu s aktivitou SqlServerStoredProcedure
Teď vytvoříme kanál s aktivitou uložené procedury.

Všimněte si následujících vlastností:

- Vlastnost **Type** je nastavená na **SqlServerStoredProcedure**.
- **StoredProcedureName** ve vlastnostech typu je nastaveno na **usp_sample** (název uložené procedury).
- Oddíl **storedProcedureParameters** obsahuje jeden parametr s názvem **DateTime**. Název a velikost písmen v parametru JSON se musí shodovat s názvem a velikostí parametru v definici uložené procedury. Pokud pro parametr potřebujete předat hodnotu null, použijte syntaxi: `"param1": null` (všechna malá písmena).

1. Klikněte na **... Další** na panelu příkazů a klikněte na **Nový kanál**.
2. Zkopírujte/vložte následující fragment kódu JSON:

    ```JSON
    {
        "name": "SprocActivitySamplePipeline",
        "properties": {
            "activities": [
                {
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
                        "storedProcedureName": "usp_sample",
                        "storedProcedureParameters": {
                            "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                        }
                    },
                    "outputs": [
                        {
                            "name": "sprocsampleout"
                        }
                    ],
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "SprocActivitySample"
                }
            ],
            "start": "2017-04-02T00:00:00Z",
            "end": "2017-04-02T05:00:00Z",
            "isPaused": false
        }
    }
    ```
3. Kanál nasadíte kliknutím na **nasadit** na panelu nástrojů.

### <a name="monitor-the-pipeline"></a>Monitorování kanálu
1. Kliknutím na **X** zavřete editor služby Data Factory a vrátíte se zpátky do okna Objekt pro vytváření dat. Tam klikněte na **Diagram**.

    ![dlaždice diagramu 1](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. V **zobrazení diagramu**uvidíte přehled kanálů a datové sady použité v tomto kurzu.

    ![dlaždice diagramu 2](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. V zobrazení diagramu dvakrát klikněte na datovou sadu `sprocsampleout` . Řezy se zobrazí ve stavu připraveno. Mělo by existovat pět řezů, protože řez se vytvoří pro každou hodinu mezi počátečním a koncovým časem z formátu JSON.

    ![dlaždice diagramu 3](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. Pokud je řez ve stavu **připraveno** , spusťte `select * from sampletable` dotaz na databázi a ověřte, zda byla data do tabulky vložena v rámci uložené procedury.

   ![Výstupní data](./media/data-factory-stored-proc-activity/output.png)

   Podrobné informace o monitorování Azure Data Factorych kanálech najdete v tématu [monitorování kanálu](data-factory-monitor-manage-pipelines.md) .

## <a name="specify-an-input-dataset"></a>Zadat vstupní datovou sadu
V tomto návodu aktivita uložených procedur nemá žádné vstupní datové sady. Pokud zadáte vstupní datovou sadu, aktivita uložené procedury se nespustí, dokud není dostupný řez vstupní datové sady (v připraveném stavu). Datová sada může být externí datová sada (která není vytvořená jinou aktivitou ve stejném kanálu) nebo interní datovou sadu vytvořenou nadřazeným aktivitou (aktivita, která se spouští před touto aktivitou). Pro aktivitu uložené procedury lze zadat více vstupních datových sad. V takovém případě se aktivita uložené procedury spustí pouze v případě, že jsou k dispozici všechny vstupní řezy datové sady (ve stavu připraveno). Vstupní datovou sadu nelze v uložené proceduře jako parametr spotřebovat. Slouží pouze ke kontrole závislosti před spuštěním aktivity uložené procedury.

## <a name="chaining-with-other-activities"></a>Řetězení s ostatními aktivitami
Pokud chcete zřetězit aktivitu typu nadřazený s touto aktivitou, zadejte výstup nadřazené aktivity jako vstup této aktivity. Když to uděláte, aktivita uložená procedura se nespustí, dokud není dokončená aktivita nadřazeného objektu a je dostupná výstupní datová sada nadřazené aktivity (ve stavu připraveno). Můžete zadat výstupní datové sady více nadřazených aktivit jako vstupní datové sady aktivity uložené procedury. V takovém případě se aktivita uložené procedury spustí pouze v případě, že jsou k dispozici všechny vstupní datové sady Slice.

V následujícím příkladu je výstupem aktivity kopírování: OutputDataset, což je vstup aktivity uložené procedury. Proto se aktivita uložené procedury nespustí, dokud se nedokončí aktivita kopírování a k dispozici je OutputDataset řez (ve stavu připraveno). Pokud zadáte více vstupních datových sad, aktivita uložená procedura nebude spuštěna, dokud nebudou k dispozici všechny vstupní datové sady Slice (ve stavu připraveno). Vstupní datové sady nelze použít přímo jako parametry aktivity uložená procedura.

Další informace o řetězení aktivit najdete v tématu [více aktivit v kanálu](data-factory-create-pipelines.md#multiple-activities-in-a-pipeline) .

```json
{
    "name": "ADFTutorialPipeline",
    "properties": {
        "description": "Copy data from a blob to blob",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [ { "name": "InputDataset" } ],
                "outputs": [ { "name": "OutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst"
                },
                "name": "CopyFromBlobToSQL"
            },
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "SPSproc"
                },
                "inputs": [ { "name": "OutputDataset" } ],
                "outputs": [ { "name": "SQLOutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunStoredProcedure"
            }
        ],
        "start": "2017-04-12T00:00:00Z",
        "end": "2017-04-13T00:00:00Z",
        "isPaused": false,
    }
}
```

Podobně pokud chcete propojit aktivitu procedury úložiště s **podřízenými aktivitami** (aktivity, které se spouštějí po dokončení aktivity uložené procedury), zadejte výstupní datovou sadu aktivity uložené procedury jako vstup aktivity pro příjem dat v kanálu.

> [!IMPORTANT]
> Při kopírování dat do Azure SQL Database nebo SQL Server můžete nakonfigurovat **SqlSink** v aktivitě kopírování a vyvolat uloženou proceduru pomocí vlastnosti **sqlWriterStoredProcedureName** . Další informace naleznete v tématu [vyvolání uložené procedury z aktivity kopírování](data-factory-invoke-stored-procedure-from-copy-activity.md). Podrobnosti o této vlastnosti naleznete v následujících článcích konektoru: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties).
> 
> Při kopírování dat z Azure SQL Database nebo SQL Server nebo Azure synapse Analytics můžete nakonfigurovat **SqlSource** v aktivitě kopírování, aby se vyvolala uložená procedura pro čtení dat ze zdrojové databáze pomocí vlastnosti **sqlReaderStoredProcedureName** . Další informace najdete v následujících článcích konektoru: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties)a [Azure synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) .

## <a name="json-format"></a>Formát JSON
Tady je formát JSON pro definování aktivity uložené procedury:

```JSON
{
    "name": "SQLSPROCActivity",
    "description": "description",
    "type": "SqlServerStoredProcedure",
    "inputs": [ { "name": "inputtable" } ],
    "outputs": [ { "name": "outputtable" } ],
    "typeProperties":
    {
        "storedProcedureName": "<name of the stored procedure>",
        "storedProcedureParameters":
        {
            "param1": "param1Value"
            …
        }
    }
}
```

Tyto vlastnosti JSON jsou popsány v následující tabulce:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| name | Název aktivity |Ano |
| Popis |Text popisující, k čemu se aktivita používá |Ne |
| typ | Musí být nastavené na: **SqlServerStoredProcedure** | Ano |
| vztahují | Nepovinný parametr. Pokud zadáte vstupní datovou sadu, musí být k dispozici (ve stavu "připraveno") pro spuštění aktivity uložená procedura. Vstupní datovou sadu nelze v uložené proceduře jako parametr spotřebovat. Slouží pouze ke kontrole závislosti před spuštěním aktivity uložené procedury. |Ne |
| činnosti | Pro aktivitu uložené procedury musíte zadat výstupní datovou sadu. Výstupní datová sada určuje **plán** aktivity uložené procedury (každou hodinu, týdně, měsíčně atd.). <br/><br/>Výstupní datová sada musí používat **propojenou službu** , která odkazuje na Azure SQL Database nebo Azure synapse Analytics nebo databáze SQL Server, ve které chcete uloženou proceduru spustit. <br/><br/>Výstupní datová sada může sloužit jako způsob, jak předat výsledek uložené procedury pro následné zpracování jinou aktivitou ([řetězení aktivit](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) v kanálu. Data Factory však do této datové sady automaticky nezapisuje výstup uložené procedury. Jedná se o uloženou proceduru, která zapisuje do tabulky SQL, na kterou odkazuje výstupní datová sada. <br/><br/>V některých případech může být výstupní datovou sadou **fiktivní datová**sada, která se používá pouze k zadání plánu pro spuštění aktivity uložené procedury. |Ano |
| storedProcedureName |Zadejte název uložené procedury v Azure SQL Database, Azure synapse Analytics nebo SQL Server reprezentované propojenou službou, kterou používá výstupní tabulka. |Ano |
| storedProcedureParameters |Zadejte hodnoty pro parametry uložené procedury. Pokud pro parametr potřebujete předat hodnotu null, použijte syntaxi: "param1": null (bez velkých malých písmen). V následující ukázce se dozvíte, jak tuto vlastnost použít. |Ne |

## <a name="passing-a-static-value"></a>Předání statické hodnoty
Teď zvažte přidání dalšího sloupce s názvem "scénář" v tabulce, která obsahuje statickou hodnotu s názvem "dokument Sample".

![Ukázková data 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

**Stolní**

```SQL
CREATE TABLE dbo.sampletable2
(
    Id uniqueidentifier,
    datetimestamp nvarchar(127),
    scenario nvarchar(127)
)
GO

CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable2(Id);
```

**Uložená procedura:**

```SQL
CREATE PROCEDURE usp_sample2 @DateTime nvarchar(127) , @Scenario nvarchar(127)

AS

BEGIN
    INSERT INTO [sampletable2]
    VALUES (newid(), @DateTime, @Scenario)
END
```

Nyní předejte parametr **scénáře** a hodnotu z aktivity uložená procedura. Část **typeProperties** v předchozím příkladu vypadá jako následující fragment kódu:

```JSON
"typeProperties":
{
    "storedProcedureName": "usp_sample",
    "storedProcedureParameters":
    {
        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
        "Scenario": "Document sample"
    }
}
```

**Data Factory datová sada:**

```JSON
{
    "name": "sprocsampleout2",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "sampletable2"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Kanál Data Factory**

```JSON
{
    "name": "SprocActivitySamplePipeline2",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample2",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
                        "Scenario": "Document sample"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout2"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
        "start": "2016-10-02T00:00:00Z",
        "end": "2016-10-02T05:00:00Z"
    }
}
```
