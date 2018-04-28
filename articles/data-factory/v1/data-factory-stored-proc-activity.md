---
title: Systému SQL Server uložené procedury aktivity
description: Zjistěte, jak můžete pomocí SQL Server aktivity uložené procedury vyvolat uloženou proceduru v databázi SQL Azure nebo Azure SQL Data Warehouse z objektu pro vytváření dat kanál.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: douglasl
robots: noindex
ms.openlocfilehash: a628374841126a750ebf6881f8adec66340c1d29
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="sql-server-stored-procedure-activity"></a>Systému SQL Server uložené procedury aktivity
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Aktivita Hive](data-factory-hive-activity.md) 
> * [Pig aktivity](data-factory-pig-activity.md)
> * [Činnost MapReduce](data-factory-map-reduce.md)
> * [Streamované aktivitě Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Spark aktivity](data-factory-spark.md)
> * [Aktivita Provedení dávky služby Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Aktivita Aktualizace prostředků služby Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Aktivita Uložená procedura](data-factory-stored-proc-activity.md)
> * [Aktivita U-SQL služby Data Lake Analytics](data-factory-usql-activity.md)
> * [Vlastní aktivity rozhraní .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Azure Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [transformace dat pomocí aktivity uložené procedury v datové továrně verze 2](../transform-data-using-stored-procedure.md).

## <a name="overview"></a>Přehled
Pomocí aktivit transformace dat v datové továrně [kanálu](data-factory-create-pipelines.md) k transformaci a zpracování nezpracovaných dat do předpovědi a statistiky. Aktivita uložené procedury je jedním z aktivit transformace, které podporuje služby Data Factory. Tento článek vychází [aktivit transformace dat](data-factory-data-transformation-activities.md) článek, který představuje Obecné přehled o transformaci dat a aktivity podporované transformace v datové továrně.

Aktivita uložené procedury můžete vyvolat uloženou proceduru v jednom z následujících úložiště dat ve vašem podniku nebo na virtuální počítač Azure (VM): 

- Azure SQL Database
- Azure SQL Data Warehouse
- Databáze systému SQL Server.  Pokud používáte systém SQL Server, nainstalujte Brána pro správu dat na stejném počítači, který je hostitelem databáze nebo na samostatný počítač, který má přístup k databázi. Brána pro správu dat je, že komponenty, která se připojuje data způsobem, zabezpečení a správě zdroje na místní nebo na virtuální počítač Azure s cloudovými službami. V tématu [Brána pro správu dat](data-factory-data-management-gateway.md) článku.

> [!IMPORTANT]
> Při kopírování dat do Azure SQL Database nebo SQL Server, můžete nakonfigurovat **SqlSink** v aktivitě kopírování k vyvolání uložené procedury pomocí **sqlWriterStoredProcedureName** vlastnost. Další informace najdete v tématu [vyvolat uloženou proceduru z aktivity kopírování](data-factory-invoke-stored-procedure-from-copy-activity.md). Podrobnosti o vlastnosti, viz následující články konektor: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [systému SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties). Volání uložené procedury při kopírování dat do Azure SQL Data Warehouse pomocí aktivity kopírování není podporována. Ale aktivity uložené procedury můžete vyvolat uloženou proceduru v SQL Data Warehouse. 
>  
> Při kopírování dat z Azure SQL Database nebo SQL Server nebo Azure SQL Data Warehouse, můžete nakonfigurovat **SqlSource** v aktivitě kopírování vyvolat uloženou proceduru čtení dat ze zdrojové databáze pomocí  **sqlReaderStoredProcedureName** vlastnost. Další informace naleznete v následujících článcích konektor: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [systému SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)          


Následující postup používá aktivity uložené procedury v kanálu vyvolat uloženou proceduru v databázi Azure SQL. 

## <a name="walkthrough"></a>Názorný postup
### <a name="sample-table-and-stored-procedure"></a>Ukázkové tabulky a uložené procedury
1. Vytvořte následující **tabulky** ve vaší databázi SQL Azure pomocí SQL Server Management Studio nebo jakýkoli jiný nástroj, který jste obeznámeni s. Sloupec razítko_data_a_času je datum a čas, kdy se vygeneruje odpovídající ID.

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
    ID je jedinečný identifikovat a sloupec razítko_data_a_času je datum a čas, kdy se vygeneruje odpovídající ID.
    
    ![Ukázková data](./media/data-factory-stored-proc-activity/sample-data.png)

    V této ukázce je uloženou proceduru v databázi SQL Azure. Pokud uložená procedura je do Azure SQL Data Warehouse a databáze systému SQL Server, je podobný přístupu. Pro databázi systému SQL Server, musíte nainstalovat [Brána pro správu dat](data-factory-data-management-gateway.md).
2. Vytvořte následující **uložené procedury** který vkládá data v do **sampletable**.

    ```SQL
    CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime)
    END
    ```

   > [!IMPORTANT]
   > **Název** a **velká a malá písmena** parametru (data a času v tomto příkladu) musí odpovídat parametr zadaný v kódu JSON kanálu nebo aktivity. V definici uloženou proceduru, ujistěte se, že **@** slouží jako předpona pro parametr.

### <a name="create-a-data-factory"></a>Vytvoření datové továrny
1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).
2. Klikněte na tlačítko **nový** v levé nabídce klikněte na tlačítko **Intelligence + analýzy**a klikněte na tlačítko **Data Factory**.

    ![Nová datová továrna](media/data-factory-stored-proc-activity/new-data-factory.png)    
3. V **nový objekt pro vytváření dat** okno, zadejte **SProcDF** pro název. Azure Data Factory názvů **globálně jedinečný**. Budete muset předponu názvu objektu pro vytváření dat s názvem, pokud chcete povolit úspěšné vytvoření objektu pro vytváření.

   ![Nová datová továrna](media/data-factory-stored-proc-activity/new-data-factory-blade.png)         
4. Vyberte vaše **předplatné**.
5. V části **Skupina prostředků** proveďte jeden z následujících kroků:
   1. Klikněte na tlačítko **vytvořit nový** a zadejte název pro skupinu prostředků.
   2. Klikněte na tlačítko **použít existující** a vyberte existující skupinu prostředků.  
6. Vyberte **umístění** pro objekt pro vytváření dat.
7. Vyberte **připnout na řídicí panel** , aby mohli zobrazit objektu pro vytváření dat na řídicím panelu při příštím přihlášení.
8. V okně **Nový objekt pro vytváření dat** klikněte na **Vytvořit**.
9. Zobrazí objektu pro vytváření dat vytváří ve **řídicí panel** na portálu Azure. Po úspěšném vytvoření objektu pro vytváření dat se zobrazí stránka s obsahem objektu pro vytváření dat.

   ![Domovská stránka objektu pro vytváření dat](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Vytvoření služby Azure SQL propojené
Po vytvoření objektu pro vytváření dat, Azure SQL vytvoříte propojené služby, který odkazuje na databázi Azure SQL, který obsahuje tabulku sampletable a sp_sample uložené procedury, do data factory.

1. Klikněte na tlačítko **autora a nasazení** na **objekt pro vytváření dat** okně **SProcDF** ke spuštění editoru služby Data Factory.
2. Klikněte na tlačítko **nové datové úložiště** na příkaz panelu a vyberte **Azure SQL Database**. Měli byste vidět skript JSON pro vytvoření služby Azure SQL propojené v editoru.

   ![Nové datové úložiště](media/data-factory-stored-proc-activity/new-data-store.png)
3. Ve skriptu JSON proveďte následující změny:

   1. Nahraďte `<servername>` s názvem serveru Azure SQL Database.
   2. Nahraďte `<databasename>` s databází, ve které jste vytvořili, tabulky a uložené procedury.
   3. Nahraďte `<username@servername>` pomocí uživatelského účtu, který má přístup k databázi.
   4. Nahraďte `<password>` s heslem pro uživatelský účet.

      ![Nové datové úložiště](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. Chcete-li nasadit propojené služby, klikněte na tlačítko **nasadit** na panelu příkazů. Zkontrolujte, jestli AzureSqlLinkedService ve stromovém zobrazení na levé straně.

    ![zobrazení stromu s propojené služby](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Vytvoření výstupní datové sady
I když uloženou proceduru neobsahuje žádná data, je nutné zadat výstupní datovou sadu aktivity uložené procedury. Je to způsobeno je výstupní datovou sadu, která určuje plán aktivity (jak často aktivita spuštěna - hodinové, denní, atd.). Musíte použít výstupní datovou sadu **propojená služba** který odkazuje na databázi SQL Azure nebo Azure SQL Data Warehouse nebo databázi SQL Server, který chcete spustit uloženou proceduru. Výstupní datovou sadu může sloužit jako způsob, jak předat výsledek uložené procedury pro následné zpracování pomocí jiné aktivity ([řetězení aktivity](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) v kanálu. Ale objekt pro vytváření dat nelze zapsat automaticky výstup uložené procedury pro tuto datovou sadu. Je uložené procedury, která zapisuje do tabulky SQL, odkazující na výstupní datovou sadu. V některých případech může být výstupní datovou sadu **fiktivní datovou sadu** (datovou sadu, která odkazuje na tabulku, která skutečně neobsahuje výstup uložené procedury). Tato datová sada fiktivní slouží pouze k určení plánu pro spuštěnou aktivity uložené procedury. 

1. Pokud tlačítko nevidíte, klikněte na panelu nástrojů na **... Další** na panelu nástrojů klikněte na tlačítko **nová datová sada**a klikněte na tlačítko **Azure SQL**. **Nová datová sada** na panelu příkazů a vyberte **Azure SQL**.

    ![zobrazení stromu s propojené služby](media/data-factory-stored-proc-activity/new-dataset.png)
2. Zkopírujte a vložte následující skript JSON v pro JSON editor.

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
3. Chcete-li nasadit datovou sadu, klikněte na tlačítko **nasadit** na panelu příkazů. Zkontrolujte, jestli datové sady ve stromovém zobrazení.

    ![Zobrazení stromu s propojenými službami](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>Vytvoření kanálu s aktivitou SqlServerStoredProcedure
Teď umožňuje vytvoření kanálu s aktivitou uložené procedury. 

Všimněte si následujících vlastností: 

- **Typ** je nastavena na **SqlServerStoredProcedure**. 
- **StoredProcedureName** v typu vlastnosti nastavena na **sp_sample** (název uložené procedury).
- **StoredProcedureParameters** část obsahuje jeden parametr s názvem **data a času**. Název a malá a velká písmena parametr ve formátu JSON musí odpovídat názvu a velká a malá písmena parametru v definici uložené procedury. Pokud je třeba předat hodnotu null pro parametr, použijte syntaxi: `"param1": null` (všechny malá písmena).
 
1. Pokud tlačítko nevidíte, klikněte na panelu nástrojů na **... Další** na panelu příkazů a klikněte na **nový kanál**.
2. Zkopírujte a vložte následující fragment kódu JSON:   

    ```JSON
    {
        "name": "SprocActivitySamplePipeline",
        "properties": {
            "activities": [
                {
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
                        "storedProcedureName": "sp_sample",
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
3. Chcete-li nasadit kanálu, klikněte na tlačítko **nasadit** na panelu nástrojů.  

### <a name="monitor-the-pipeline"></a>Monitorování kanálu
1. Kliknutím na **X** zavřete editor služby Data Factory a vrátíte se zpátky do okna Objekt pro vytváření dat. Tam klikněte na **Diagram**.

    ![Dlaždice diagram](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. V **Zobrazení diagramu** uvidíte přehled kanálů a datové sady použité v tomto kurzu.

    ![Dlaždice diagram](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. V zobrazení diagramu dvakrát klikněte na datovou sadu `sprocsampleout`. Zobrazí řezy ve stavu Připraveno. Měla by existovat pět řezy protože řez se vytvářejí každou hodinu mezi počáteční čas a koncový čas z formátu JSON.

    ![Dlaždice diagram](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. Pokud je řez v **připraven** stavu, spusťte `select * from sampletable` dotaz proti dané databázi Azure SQL, chcete-li ověřit, že data byla vložena v do tabulky pomocí uložené procedury.

   ![Výstupní data](./media/data-factory-stored-proc-activity/output.png)

   V tématu [kanál monitorovat](data-factory-monitor-manage-pipelines.md) podrobné informace o monitorování kanálů služby Azure Data Factory.  


## <a name="specify-an-input-dataset"></a>Zadejte vstupní datové sady
Aktivita uložené procedury v návodu, nemá žádné vstupní datové sady. Pokud zadáte vstupní datové sady, aktivity uložené procedury se nespustí, dokud řezy vstupní datové sady je k dispozici (ve stavu Připraveno). Datová sada může být externí datová sada (který není vyprodukované jinou aktivitu v kanálu stejné) nebo interní datovou sadu, která je vytvořený nadřízenou aktivitou (aktivitu, která se spouští před tato aktivita). Můžete určit více vstupní datové sady aktivity uložené procedury. Pokud tak učiníte, aktivity uložené procedury spustí pouze v případě, že všechny řezy vstupní datové sady jsou k dispozici (ve stavu Připraveno). Vstupní datové sady nelze zpracovat v uložené proceduře jako parametr. Používá se pouze ke kontrole závislost před zahájením aktivity uložené procedury.

## <a name="chaining-with-other-activities"></a>Řetězení s ostatními aktivitami
Pokud chcete zřetězit nadřazeného aktivitu se tato aktivita, zadejte jako vstup této aktivity výstup nadřízené činnosti. Pokud tak učiníte, aktivity uložené procedury se nespustí, dokud dokončení nadřazeného aktivity a výstupní datovou sadu nadřízené činnosti je k dispozici (ve stavu Připraveno). Výstupní datové sady z více činností, nadřazeného můžete zadat jako vstupní datové sady aktivity uložené procedury. Pokud tak učiníte, aktivity uložené procedury spustí pouze v případě, že všechny řezy vstupní datové sady jsou k dispozici.  

V následujícím příkladu je výstup aktivity kopírování: OutputDataset, což je vstup aktivity uložené procedury. Proto aktivity uložené procedury nelze spustit až do dokončení aktivity kopírování a OutputDataset řez je k dispozici (ve stavu Připraveno). Pokud zadáte více vstupní datové sady, aktivity uložené procedury se nespustí, dokud všechny řezy vstupní datové sady jsou k dispozici (ve stavu Připraveno). Vstupní datové sady nelze použít přímo jako parametry pro aktivitu uložené procedury. 

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

Podobně propojení aktivita procedury úložiště s **podřízené aktivity** (aktivity, které spustit po dokončení aktivity uložené procedury), zadejte výstupní datovou sadu aktivity uložené procedury jako vstup podřízené aktivitu v kanálu.

> [!IMPORTANT]
> Při kopírování dat do Azure SQL Database nebo SQL Server, můžete nakonfigurovat **SqlSink** v aktivitě kopírování k vyvolání uložené procedury pomocí **sqlWriterStoredProcedureName** vlastnost. Další informace najdete v tématu [vyvolat uloženou proceduru z aktivity kopírování](data-factory-invoke-stored-procedure-from-copy-activity.md). Podrobnosti o vlastnost, najdete v následujících článcích konektor: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [systému SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties).
>  
> Při kopírování dat z Azure SQL Database nebo SQL Server nebo Azure SQL Data Warehouse, můžete nakonfigurovat **SqlSource** v aktivitě kopírování vyvolat uloženou proceduru čtení dat ze zdrojové databáze pomocí  **sqlReaderStoredProcedureName** vlastnost. Další informace naleznete v následujících článcích konektor: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [systému SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)          

## <a name="json-format"></a>Formát JSON
Tady je formátu JSON pro definování aktivity uložené procedury:

```JSON
{
    "name": "SQLSPROCActivity",
    "description": "description",
    "type": "SqlServerStoredProcedure",
    "inputs":  [ { "name": "inputtable"  } ],
    "outputs":  [ { "name": "outputtable" } ],
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
| description |Text popisující, co se používá aktivitu pro |Ne |
| type | Musí být nastavena na: **SqlServerStoredProcedure** | Ano |
| Vstupy | Volitelné. Pokud zadáte vstupní datové sady, musí být k dispozici (v 'Připravený' stav) se spouští aktivita uložené procedury. Vstupní datové sady nelze zpracovat v uložené proceduře jako parametr. Používá se pouze ke kontrole závislost před zahájením aktivity uložené procedury. |Ne |
| výstupy | Je nutné zadat výstupní datovou sadu aktivity uložené procedury. Určuje výstupní datovou sadu **plán** aktivity uložené procedury (každou hodinu, týdně, měsíčně, atd.). <br/><br/>Musíte použít výstupní datovou sadu **propojená služba** který odkazuje na databázi SQL Azure nebo Azure SQL Data Warehouse nebo databázi SQL Server, který chcete spustit uloženou proceduru. <br/><br/>Výstupní datovou sadu může sloužit jako způsob, jak předat výsledek uložené procedury pro následné zpracování pomocí jiné aktivity ([řetězení aktivity](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) v kanálu. Ale objekt pro vytváření dat nelze zapsat automaticky výstup uložené procedury pro tuto datovou sadu. Je uložené procedury, která zapisuje do tabulky SQL, odkazující na výstupní datovou sadu. <br/><br/>V některých případech může být výstupní datovou sadu **fiktivní datovou sadu**, který slouží pouze k určení plánu pro spuštěnou aktivity uložené procedury. |Ano |
| storedProcedureName |Zadejte název uložené procedury v Azure SQL database nebo databáze Azure SQL Data Warehouse nebo SQL Server, která je reprezentována propojené služby, která používá výstupní tabulka. |Ano |
| storedProcedureParameters |Zadejte hodnoty pro parametry uložené procedury. Pokud potřebujete předat hodnotu null pro parametr, použijte syntaxi: "param1": null (všechny malá písmena). Viz následující ukázka Další informace o používání této vlastnosti. |Ne |

## <a name="passing-a-static-value"></a>Předávání na statické hodnoty
Nyní Pojďme zvažte přidání jiný sloupec s názvem 'Scénář' v tabulce obsahující statická hodnota volána 'Dokumentu ukázka'.

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

**Uložené procedury:**

```SQL
CREATE PROCEDURE sp_sample2 @DateTime nvarchar(127) , @Scenario nvarchar(127)

AS

BEGIN
    INSERT INTO [sampletable2]
    VALUES (newid(), @DateTime, @Scenario)
END
```

Nyní, předat **scénář** parametr a hodnotu z aktivity uložené procedury. **Rámci typeProperties** část v předchozím příkladu vypadá jako následující fragment kódu:

```JSON
"typeProperties":
{
    "storedProcedureName": "sp_sample",
    "storedProcedureParameters":
    {
        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
        "Scenario": "Document sample"
    }
}
```

**Datové sady objektu pro vytváření dat:**

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

**Kanál služby Data Factory**

```JSON
{
    "name": "SprocActivitySamplePipeline2",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_sample2",
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
