---
title: SQL Server aktivita uložených procedur
description: Zjistěte, jak můžete pomocí SQL serveru aktivitě uložené procedury k vyvolání uložené procedury do služby Azure SQL Database a Azure SQL Data Warehouse z kanálu Data Factory.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: douglasl
robots: noindex
ms.openlocfilehash: 5604767b49e6234bba5c16179bebe717938170cf
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55753261"
---
# <a name="sql-server-stored-procedure-activity"></a>SQL Server aktivita uložených procedur
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Aktivita hivu](data-factory-hive-activity.md)
> * [Aktivita pig](data-factory-pig-activity.md)
> * [Aktivita MapReduce](data-factory-map-reduce.md)
> * [Streamované aktivitě Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Aktivita Spark](data-factory-spark.md)
> * [Aktivita Provedení dávky služby Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Aktivita Aktualizace prostředků služby Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Aktivita Uložená procedura](data-factory-stored-proc-activity.md)
> * [Aktivita U-SQL služby Data Lake Analytics](data-factory-usql-activity.md)
> * [Vlastní aktivita .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [transformovat data pomocí aktivity uložených procedur ve službě Data Factory](../transform-data-using-stored-procedure.md).

## <a name="overview"></a>Přehled
Použití aktivity transformace dat ve službě Data Factory [kanálu](data-factory-create-pipelines.md) k transformaci a zpracování nezpracovaných dat do předpovědi a přehledy. Aktivita uložená procedura je jedním z aktivity transformace, které služba Data Factory podporuje. Tento článek vychází [aktivity transformace dat](data-factory-data-transformation-activities.md) článek, který nabízí obecný přehled o transformaci dat a aktivity podporované transformace ve službě Data Factory.

Aktivity uložené procedury můžete použít k vyvolání uložené procedury v jedné z následujících úložiště dat ve vaší organizaci nebo na virtuálním počítači Azure (VM):

- Azure SQL Database
- Azure SQL Data Warehouse
- Databáze SQL serveru. Pokud používáte systém SQL Server, nainstalujte bránu správy dat ve stejném počítači, který je hostitelem databáze nebo na samostatném počítači, který má přístup k databázi. Brána správy dat je součást, která se připojí data zdroje v místním prostředí nebo na virtuálním počítači Azure s cloudovými službami zabezpečení a správě způsobem. Zobrazit [brána správy dat](data-factory-data-management-gateway.md) , kde najdete podrobnosti.

> [!IMPORTANT]
> Při kopírování dat do Azure SQL Database nebo SQL Server, můžete nakonfigurovat **SqlSink** v aktivitě kopírování, která se má vyvolat uloženou proceduru s použitím **sqlWriterStoredProcedureName** vlastnost. Další informace najdete v tématu [vyvolat uloženou proceduru z aktivity kopírování](data-factory-invoke-stored-procedure-from-copy-activity.md). Podrobné informace o vlastnosti viz následující články konektoru: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [systému SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties). Volání uložené procedury při kopírování dat do služby Azure SQL Data Warehouse s využitím aktivity kopírování se nepodporuje. Ale můžete použít aktivitu uložené procedury k vyvolání uložené procedury v SQL Data Warehouse.
>
> Při kopírování dat z Azure SQL Database nebo SQL Server nebo Azure SQL Data Warehouse, můžete nakonfigurovat **SqlSource** v aktivitě kopírování, která se má vyvolat uloženou proceduru pro čtení dat ze zdrojové databáze s použitím  **sqlReaderStoredProcedureName** vlastnost. Další informace naleznete v následujících článcích konektoru: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [systému SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

Následující návod používá aktivitu uložená procedura v kanálu vyvolat uloženou proceduru v databázi Azure SQL.

## <a name="walkthrough"></a>Názorný postup
### <a name="sample-table-and-stored-procedure"></a>Ukázkové tabulky a uložené procedury
1. Vytvořte následující **tabulky** v Azure SQL Database přes SQL Server Management Studio nebo jakýkoli jiný nástroj, který znáte. Sloupec razítko_data_a_času je datum a čas, kdy se vygeneruje odpovídající ID.

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
    ID je jedinečné identifikovat a datum a čas, kdy se vygeneruje odpovídající ID je sloupec razítko_data_a_času.
    
    ![Ukázková data](./media/data-factory-stored-proc-activity/sample-data.png)

    V této ukázce uložená procedura je ve službě Azure SQL Database. Pokud uloženou proceduru v databázi systému SQL Server a služby Azure SQL Data Warehouse, přístup se podobá. Pro databáze SQL serveru, je nutné nainstalovat [brána správy dat](data-factory-data-management-gateway.md).
2. Vytvořte následující **uloženou proceduru** , která vloží data v **sampletable**.

    ```SQL
    CREATE PROCEDURE usp_sample @DateTime nvarchar(127)
    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime)
    END
    ```

   > [!IMPORTANT]
   > **Název** a **malých a velkých písmen** parametru (datum a čas v tomto příkladu) se musí shodovat s parametru zadané v kódu JSON kanálu a aktivity. V definici uloženou proceduru, ujistěte se, že **@** slouží jako předpona pro parametr.

### <a name="create-a-data-factory"></a>Vytvoření datové továrny
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Klikněte na tlačítko **nový** v nabídce vlevo klikněte na tlačítko **inteligence a analýza**a klikněte na tlačítko **služby Data Factory**.

    ![Nová datová továrna](media/data-factory-stored-proc-activity/new-data-factory.png)
3. V **nová datová továrna** okně zadejte **SProcDF** pro název. Azure Data Factory názvy jsou **globálně jedinečný**. Je třeba název objektu pro vytváření dat s názvem, aby úspěšném vytvoření objektu pro vytváření.

   ![Nová datová továrna](media/data-factory-stored-proc-activity/new-data-factory-blade.png)
4. Vyberte své **předplatné Azure**.
5. V části **Skupina prostředků** proveďte jeden z následujících kroků:
   1. Klikněte na tlačítko **vytvořit nový** a zadejte název pro skupinu prostředků.
   2. Klikněte na tlačítko **použít existující** a vyberte existující skupinu prostředků.
6. Vyberte **umístění** pro datovou továrnu.
7. Vyberte **připnout na řídicí panel** , kde můžete zobrazit objektu pro vytváření dat na řídicím panelu při příštím přihlášení.
8. V okně **Nový objekt pro vytváření dat** klikněte na **Vytvořit**.
9. Zobrazit objektu pro vytváření dat vytváří **řídicí panel** na webu Azure Portal. Po úspěšném vytvoření objektu pro vytváření dat se zobrazí stránka s obsahem objektu pro vytváření dat.

   ![Domovská stránka datová továrna](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Vytvoření propojené služby Azure SQL
Po vytvoření objektu pro vytváření dat Azure SQL vytvoříte propojené služby, která propojí vaši databázi Azure SQL, která obsahuje sampletable tabulky a usp_sample uloženou proceduru se svou datovou továrnou.

1. Klikněte na tlačítko **Autor a nasadit** na **služby Data Factory** okno **SProcDF** ke spuštění editoru služby Data Factory.
2. Klikněte na tlačítko **nové datové úložiště** na příkaz pruhové a zvolte **Azure SQL Database**. Měli byste vidět skript JSON pro vytvoření služby Azure SQL, které jsou propojeny v editoru.

   ![Nové datové úložiště](media/data-factory-stored-proc-activity/new-data-store.png)
3. Ve skriptu JSON proveďte následující změny:

   1. Nahraďte `<servername>` s názvem vašeho serveru Azure SQL Database.
   2. Nahraďte `<databasename>` s databází, ve které jste vytvořili, tabulky a uložené procedury.
   3. Nahraďte `<username@servername>` pomocí uživatelského účtu, který má přístup k databázi.
   4. Nahraďte `<password>` heslem pro uživatelský účet.

      ![Nové datové úložiště](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. Pokud chcete nasadit tuto propojenou službu, klikněte na tlačítko **nasadit** na panelu příkazů. Ověřte, že AzureSqlLinkedService ve stromovém zobrazení na levé straně.

    ![zobrazení stromu s propojené služby](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Vytvoření výstupní datové sady
Výstupní datovou sadu aktivity uložené procedury je nutné zadat i v případě, že výsledkem bude procedura není žádná data. To je, protože je výstupní datovou sadu, která řídí plán aktivity (jak často aktivita spouští - každou hodinu, každý den atd.). Výstupní datová sada musí používat **propojená služba** , který odkazuje na službu Azure SQL Database nebo Azure SQL Data Warehouse nebo databázi SQL Server, ve kterém chcete spustit uloženou proceduru. Výstupní datová sada může sloužit jako způsob, jak předat výsledek úložnou proceduru pro následné zpracování pomocí další aktivity ([řetězení aktivit](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) v kanálu. Ale Data Factory nezapisuje automaticky výstup z uložené procedury do tohoto objektu dataset. Je uložená procedura, která zapisuje do výstupní datové sady odkazující na tabulku SQL. V některých případech může být výstupní datovou sadu **fiktivní datovou sadu** (datovou sadu, která odkazuje na tabulku, která ve skutečnosti neobsahuje výstup uložené procedury). Tuto fiktivní datovou sadu slouží pouze k určení plánu pro spuštěnou aktivitu uložené procedury.

1. Pokud tlačítko nevidíte, klikněte na panelu nástrojů na **... Další** na panelu nástrojů klikněte na tlačítko **nová datová sada**a klikněte na tlačítko **Azure SQL**. **Nová datová sada** na panelu příkazů a vyberte **Azure SQL**.

    ![zobrazení stromu s propojené služby](media/data-factory-stored-proc-activity/new-dataset.png)
2. Kopírování a vkládání následujícího skriptu JSON v editoru JSON.

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
3. K nasazení datovou sadu, klikněte na tlačítko **nasadit** na panelu příkazů. Potvrďte, že datová sada se zobrazí ve stromovém zobrazení.

    ![Zobrazení stromu s propojenými službami](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>Vytvoření kanálu s aktivitou SqlServerStoredProcedure
Teď vytvoříte kanál s aktivitou uložené procedury.

Všimněte si, že následující vlastnosti:

- **Typ** je nastavena na **SqlServerStoredProcedure**.
- **StoredProcedureName** v typ vlastnosti je nastavený na **usp_sample** (název uložené procedury).
- **StoredProcedureParameters** oddíl obsahuje jeden parametr s názvem **data a času**. Název a použití malých a velkých parametr ve formátu JSON musí odpovídat názvu a použití malých a velkých parametr v definici uloženou proceduru. Pokud je nutné předat pro parametr hodnotu null, použijte syntaxi: `"param1": null` (malými písmeny).

1. Pokud tlačítko nevidíte, klikněte na panelu nástrojů na **... Další** na panelu příkazů a klikněte na **nový kanál**.
2. Kopírování a vkládání následujícím fragmentu kódu JSON:

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
3. Pro nasazení kanálu, klikněte na tlačítko **nasadit** na panelu nástrojů.

### <a name="monitor-the-pipeline"></a>Monitorování kanálu
1. Kliknutím na **X** zavřete editor služby Data Factory a vrátíte se zpátky do okna Objekt pro vytváření dat. Tam klikněte na **Diagram**.

    ![Dlaždice diagram](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. V **Zobrazení diagramu** uvidíte přehled kanálů a datové sady použité v tomto kurzu.

    ![Dlaždice diagram](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. V zobrazení diagramu dvakrát klikněte datovou sadu `sprocsampleout`. Zobrazí se kolekce obsahuje nějaké řezy ve stavu Připraveno. Měla by existovat pět řezy protože řez pro každou hodinu mezi časem spuštění a čas ukončení z JSON.

    ![Dlaždice diagram](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. Pokud je řez ve **připravené** stavu, spusťte `select * from sampletable` dotaz na databázi Azure SQL a ověřte, že data byla vložena do tabulky pomocí uložené procedury.

   ![Výstupní data](./media/data-factory-stored-proc-activity/output.png)

   Zobrazit [monitorování kanálu](data-factory-monitor-manage-pipelines.md) podrobné informace o monitorování kanálů Azure Data Factory.

## <a name="specify-an-input-dataset"></a>Zadejte vstupní datové sady
V tomto návodu aktivita uložená procedura nemá žádné vstupní datové sady. Pokud chcete zadat vstupní datovou sadu, aktivita uložené procedury se nespustí, dokud řez vstupní datové sady není k dispozici (ve stavu Připraveno). Datová sada může být externích datových sad (který není vytvořená jinou aktivitou ve stejném kanálu) nebo interní datové sady, který je vytvořený nadřízenou aktivitou (aktivita, na kterém běží před této aktivity). Můžete zadat více vstupních datových sad pro aktivity uložených procedur. Pokud tak učiníte, aktivita uložená procedura spustí pouze v případě, že všechny řezy vstupní datové sady jsou k dispozici (v připraveném stavu). Vstupní datovou sadu nejde použít v uložené proceduře jako parametr. Používá se pouze ke kontrole závislost před spuštěním aktivity uložených procedur.

## <a name="chaining-with-other-activities"></a>Řetězení s ostatními aktivitami
Pokud chcete pro řetězení s upstreamovou aktivitou s touto aktivitou, zadejte jako vstup této aktivity výstup upstreamovou aktivitou. Pokud tak učiníte, aktivita uložená procedura se nespustí, dokud dokončí upstreamovou aktivitou a výstupní datové sady upstreamovou aktivitou je k dispozici (ve stavu Připraveno). Výstupní datové sady z více aktivit nadřazeného můžete zadat jako vstupní datové sady, které aktivita uložené procedury. Pokud tak učiníte, aktivita uložená procedura spustí pouze v případě, že všechny řezy vstupní datové sady jsou k dispozici.

V následujícím příkladu je výstup aktivity kopírování: OutputDataset, které je vstup aktivity uložené procedury. Aktivita uložená procedura proto nespustí, dokud aktivita kopírování dokončí a OutputDataset řez je dostupný (ve stavu Připraveno). Pokud zadáte více vstupních datových sad, aktivita uložené procedury se nespustí, dokud všechny řezy vstupní datové sady jsou k dispozici (v připraveném stavu). Vstupní datové sady nelze použít přímo jako parametry pro aktivitu uložené procedury.

Další informace o řetězení aktivit najdete v tématu [více aktivit v kanálu](data-factory-create-pipelines.md#multiple-activities-in-a-pipeline)

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

Podobně k propojení aktivity postupu úložiště s **podřízené aktivity** (aktivity, které se spustí po dokončení aktivity uložené procedury), určete výstupní datovou sadu aktivity uložené procedury jako vstup podřízené aktivity v kanálu.

> [!IMPORTANT]
> Při kopírování dat do Azure SQL Database nebo SQL Server, můžete nakonfigurovat **SqlSink** v aktivitě kopírování, která se má vyvolat uloženou proceduru s použitím **sqlWriterStoredProcedureName** vlastnost. Další informace najdete v tématu [vyvolat uloženou proceduru z aktivity kopírování](data-factory-invoke-stored-procedure-from-copy-activity.md). Další informace o vlastnosti naleznete v následujících článcích konektoru: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [systému SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties).
> 
> Při kopírování dat z Azure SQL Database nebo SQL Server nebo Azure SQL Data Warehouse, můžete nakonfigurovat **SqlSource** v aktivitě kopírování, která se má vyvolat uloženou proceduru pro čtení dat ze zdrojové databáze s použitím  **sqlReaderStoredProcedureName** vlastnost. Další informace naleznete v následujících článcích konektoru: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [systému SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

## <a name="json-format"></a>Formát JSON
Tady je formát JSON pro definici aktivity uložené procedury:

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

Následující tabulka popisuje tyto vlastnosti JSON:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| jméno | Název aktivity |Ano |
| description |Text popisující, k čemu aktivita slouží |Ne |
| type | Musí být nastaveno na: **SqlServerStoredProcedure** | Ano |
| vstupy | Volitelné. Pokud chcete zadat vstupní datovou sadu, musí být k dispozici (ve stavu "Připraveno") pro spuštění aktivity uložené procedury. Vstupní datovou sadu nejde použít v uložené proceduře jako parametr. Používá se pouze ke kontrole závislost před spuštěním aktivity uložených procedur. |Ne |
| výstupy | Je nutné zadat výstupní datovou sadu aktivity uložené procedury. Výstupní datová sada Určuje **plán** pro aktivitu uložené procedury (každou hodinu, každý týden, měsíc, atd.). <br/><br/>Výstupní datová sada musí používat **propojená služba** , který odkazuje na službu Azure SQL Database nebo Azure SQL Data Warehouse nebo databázi SQL Server, ve kterém chcete spustit uloženou proceduru. <br/><br/>Výstupní datová sada může sloužit jako způsob, jak předat výsledek úložnou proceduru pro následné zpracování pomocí další aktivity ([řetězení aktivit](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) v kanálu. Ale Data Factory nezapisuje automaticky výstup z uložené procedury do tohoto objektu dataset. Je uložená procedura, která zapisuje do výstupní datové sady odkazující na tabulku SQL. <br/><br/>V některých případech může být výstupní datovou sadu **fiktivní datovou sadu**, který se používá jenom k určení plánu pro spuštěnou aktivitu uložené procedury. |Ano |
| storedProcedureName |Zadejte název uložené procedury v databázi Azure SQL nebo Azure SQL Data Warehouse nebo SQL Server database, která je reprezentována propojené služby, který používá výstupní tabulky. |Ano |
| storedProcedureParameters |Zadejte hodnoty parametrů uložené procedury. Pokud je potřeba pro parametr předat hodnotu null, použijte syntaxi: "param1": null (malými písmeny). Najdete v následující ukázce se dozvíte o použití této vlastnosti. |Ne |

## <a name="passing-a-static-value"></a>Statická hodnota
Teď Pojďme zvážit použití přidání další sloupec s názvem "Scénář" v tabulce obsahující statická hodnota nazývá 'Dokumentu vzorek'.

![Ukázková data 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

**Tabulka:**

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

Nyní, předejte **scénář** parametr a hodnotu z aktivity uložené procedury. **TypeProperties** oddíl v předchozím příkladu vypadá jako následující fragment kódu:

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

**Datovou sadu datové továrny:**

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

**Kanál datové továrny**

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
