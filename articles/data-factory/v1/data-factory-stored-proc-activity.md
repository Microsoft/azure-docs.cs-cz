---
title: Aktivita uložené procedury serveru SQL Server
description: Zjistěte, jak můžete použít aktivitu uložené procedury serveru SQL Server k vyvolání uložené procedury v azure sql database nebo Azure SQL Data Warehouse z kanálu datové továrny.
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
ms.openlocfilehash: 45aa49de51f42b26c653b15e79c865e3f5647c39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931631"
---
# <a name="sql-server-stored-procedure-activity"></a>Aktivita uložené procedury serveru SQL Server
> [!div class="op_single_selector" title1="Transformační aktivity"]
> * [Aktivita úlu](data-factory-hive-activity.md)
> * [Aktivita prasat](data-factory-pig-activity.md)
> * [Aktivita MapReduce](data-factory-map-reduce.md)
> * [Aktivita streamování hadoopu](data-factory-hadoop-streaming-activity.md)
> * [Aktivita jiskry](data-factory-spark.md)
> * [Aktivita Provedení dávky služby Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Aktivita Aktualizace prostředků služby Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Aktivita Uložená procedura](data-factory-stored-proc-activity.md)
> * [Aktivita U-SQL služby Data Lake Analytics](data-factory-usql-activity.md)
> * [Vlastní aktivita rozhraní .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte [si téma transformace dat pomocí uložené aktivity procedury v datové továrně](../transform-data-using-stored-procedure.md).

## <a name="overview"></a>Přehled
Aktivity transformace dat v [kanálu](data-factory-create-pipelines.md) data factory transformovat a zpracovávat nezpracovaná data do předpovědi a přehledy. Aktivita uložené procedury je jednou z transformačních aktivit, které podporuje data factory. Tento článek vychází z článku [aktivit transformace dat,](data-factory-data-transformation-activities.md) který představuje obecný přehled transformace dat a podporovaných transformačních aktivit v Datové továrně.

Aktivita uložené procedury můžete použít k vyvolání uložené procedury v jednom z následujících úložišť dat v podniku nebo na virtuálním počítači Azure(VM):

- Azure SQL Database
- Azure SQL Data Warehouse
- Databáze serveru SQL Server. Pokud používáte SQL Server, nainstalujte bránu pro správu dat do stejného počítače, který je hostitelem databáze, nebo na samostatném počítači, který má přístup k databázi. Brána pro správu dat je komponenta, která propojuje zdroje dat místně/na virtuálním počítači Azure s cloudovými službami bezpečným a spravovaným způsobem. Podrobnosti najdete v článku [Brána pro správu dat.](data-factory-data-management-gateway.md)

> [!IMPORTANT]
> Při kopírování dat do Azure SQL Database nebo SQL Server, můžete nakonfigurovat **SqlSink** v aktivitě kopírování vyvolat uloženou proceduru pomocí **sqlWriterStoredProcedureName** vlastnost. Další informace naleznete v tématu [Invoke stored procedure from copy activity](data-factory-invoke-stored-procedure-from-copy-activity.md). Podrobnosti o vlastnosti naleznete v následujících článcích konektoru: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), SQL [Server](data-factory-sqlserver-connector.md#copy-activity-properties). Vyvolání uložené procedury při kopírování dat do datového skladu Azure SQL pomocí aktivity kopírování není podporováno. Ale můžete použít uloženou aktivitu procedury k vyvolání uložené procedury v datovém skladu SQL.
>
> Při kopírování dat z Azure SQL Database nebo SQL Server nebo Azure SQL Data Warehouse, můžete nakonfigurovat **SqlSource** v aktivitě kopírování vyvolat uloženou proceduru pro čtení dat ze zdrojové databáze pomocí **sqlReaderStoredProcedureName** vlastnost. Další informace naleznete v následujících článcích konektoru: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), SQL [Server](data-factory-sqlserver-connector.md#copy-activity-properties), Azure SQL [Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

Následující návod používá aktivitu uložené procedury v kanálu k vyvolání uložené procedury v databázi Azure SQL.

## <a name="walkthrough"></a>Názorný postup
### <a name="sample-table-and-stored-procedure"></a>Tabulka vzorků a uložená procedura
1. Vytvořte následující **tabulku** v azure sql database pomocí SQL Server Management Studio nebo jakýkoli jiný nástroj, který vám vyhovuje. Sloupec datetimestamp je datum a čas, kdy je generováno odpovídající ID.

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
    ID je jedinečný identifikován a datetimestamp sloupec je datum a čas, kdy je generováno odpovídající ID.
    
    ![Ukázková data](./media/data-factory-stored-proc-activity/sample-data.png)

    V této ukázce je uložená procedura v azure sql databázi. Pokud je uložená procedura v azure SQL datovém skladu a SQL Server databáze, přístup je podobný. Pro databázi serveru SQL Server je nutné nainstalovat [bránu pro správu dat](data-factory-data-management-gateway.md).
2. Vytvořte následující **uloženou proceduru,** která vloží data do **vzorové tabulky**.

    ```SQL
    CREATE PROCEDURE usp_sample @DateTime nvarchar(127)
    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime)
    END
    ```

   > [!IMPORTANT]
   > **Název** a **písmena** parametru (DateTime v tomto příkladu) se musí shodovat s parametrem zadaným v kanálu/aktivitě JSON. V definici uložené procedury se ujistěte, že **\@** se používá jako předpona parametru.

### <a name="create-a-data-factory"></a>Vytvoření datové továrny
1. Přihlaste se na [portál Azure](https://portal.azure.com/).
2. V levé nabídce klikněte na **NOVÝ,** klikněte na **Intelligence + Analytics**a klikněte na **Data Factory**.

    ![Nová datová továrna](media/data-factory-stored-proc-activity/new-data-factory.png)
3. V okně **Nová data factory** zadejte **SProcDF** pro název. Názvy Azure Data Factory jsou **globálně jedinečné**. Chcete-li povolit úspěšné vytvoření továrny, musíte předponu název datové továrny.

   ![Nová datová továrna](media/data-factory-stored-proc-activity/new-data-factory-blade.png)
4. Vyberte své **předplatné Azure**.
5. V části **Skupina prostředků** proveďte jeden z následujících kroků:
   1. Klikněte na **Vytvořit nový** a zadejte název skupiny prostředků.
   2. Klikněte na **Použít existující** a vyberte existující skupinu prostředků.
6. Vyberte **umístění** pro objekt pro vytváření dat.
7. Vyberte **Připnout na řídicí panel,** abyste viděli továrnu dat na řídicím panelu při příštím přihlášení.
8. V okně **Nový objekt pro vytváření dat** klikněte na **Vytvořit**.
9. Zobrazí se vytváření datové továrny na **řídicím panelu** portálu Azure. Po úspěšném vytvoření objektu pro vytváření dat se zobrazí stránka s obsahem objektu pro vytváření dat.

   ![Domovská stránka datové továrny](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Vytvoření propojené služby Azure SQL
Po vytvoření datové továrny vytvoříte propojenou službu Azure SQL, která propojí vaši databázi Azure SQL, která obsahuje ukázkovou tabulku a usp_sample uloženou proceduru, s vaší továrně dat.

1. Klikněte na **Author a deploy** on the Data **Factory** blade for **SProcDF** to launch the Data Factory Editor.
2. Na panelu příkazů klikněte na **Nové úložiště dat** a zvolte Azure SQL **Database**. Měli byste vidět skript JSON pro vytvoření azure SQL propojené služby v editoru.

   ![Nové úložiště dat](media/data-factory-stored-proc-activity/new-data-store.png)
3. Ve skriptu JSON proveďte následující změny:

   1. Nahraďte `<servername>` název serveru Azure SQL Database.
   2. Nahraďte `<databasename>` databází, ve které jste vytvořili tabulku a uloženou proceduru.
   3. Nahraďte `<username@servername>` uživatelským účtem, který má přístup k databázi.
   4. Nahraďte `<password>` heslem pro uživatelský účet.

      ![Nové úložiště dat](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. Pokud chcete nasadit propojenou službu, klikněte na panelu příkazů na **Nasadit.** Potvrďte, že se ve stromovém zobrazení vlevo zobrazí služba AzureSqlLinkedService.

    ![stromové zobrazení s propojenou službou](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Vytvoření výstupní datové sady
Je nutné zadat výstupní datovou sadu pro aktivitu uložené procedury i v případě, že uložená procedura nevytváří žádná data. Je to proto, že je to výstupní datová sada, která řídí plán aktivity (jak často je aktivita spuštěna - každou hodinu, denně atd.). Výstupní datová sada musí používat **propojenou službu,** která odkazuje na Azure SQL Database nebo Azure SQL Data Warehouse nebo sql server databáze, ve kterém chcete, aby uložená procedura spustit. Výstupní datová sada může sloužit jako způsob, jak předat výsledek uložené procedury pro následné zpracování jinou aktivitou ([zřetězení aktivity](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) v kanálu. Data Factory však automaticky nezapisuje výstup uložené procedury do této datové sady. Je uložená procedura, která zapisuje do tabulky SQL, na kterou odkazuje výstupní datová sada. V některých případech může být výstupní datová sada **fiktivní datovou sadou** (datovou sadou, která odkazuje na tabulku, která ve skutečnosti neobsahuje výstup uložené procedury). Tato fiktivní datová sada se používá pouze k určení plánu pro spuštění aktivity uložené procedury.

1. Klikněte na **... Další** informace na panelu nástrojů, klikněte na **Nová datová sada**a klikněte na **Azure SQL**. **Nová datová sada** na panelu příkazů a vyberte **Azure SQL**.

    ![stromové zobrazení s propojenou službou](media/data-factory-stored-proc-activity/new-dataset.png)
2. Zkopírujte/vložte následující skript JSON do editoru JSON.

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
3. Pokud chcete datovou sadu nasadit, klikněte na panelu příkazů na **Nasadit.** Potvrďte, že se datová sada zobrazí ve stromovém zobrazení.

    ![stromové zobrazení s propojenými službami](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>Vytvoření kanálu s aktivitou SqlServerStoredProcedure
Nyní vytvoříme kanál s aktivitou uložené procedury.

Všimněte si následujících vlastností:

- Vlastnost **type** je nastavena na **sqlserverstoredprocedure**.
- **UloženéProcedureName** v type vlastnosti je nastavena na **usp_sample** (název uložené procedury).
- Část **storedProcedureParameters** obsahuje jeden parametr s názvem **DateTime**. Název a písmena parametru v JSON se musí shodovat s názvem a písmenem parametru v definici uložené procedury. Pokud potřebujete předat hodnotu null pro `"param1": null` parametr, použijte syntaxi: (všechna malá písmena).

1. Klikněte na **... Další informace** na panelu příkazů a klepněte na tlačítko **Nový kanál**.
2. Zkopírujte nebo vložte následující úryvek JSON:

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
3. Pokud chcete nasadit kanál, klikněte na panelu nástrojů na **Nasadit.**

### <a name="monitor-the-pipeline"></a>Monitorování kanálu
1. Kliknutím na **X** zavřete editor služby Data Factory a vrátíte se zpátky do okna Objekt pro vytváření dat. Tam klikněte na **Diagram**.

    ![dlaždice diagramu](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. V **zobrazení diagramu**se zobrazí přehled kanálů a datových sad použitých v tomto kurzu.

    ![dlaždice diagramu](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. V zobrazení diagramu poklepejte `sprocsampleout`na datovou sadu . Zobrazí se řezy ve stavu Připraveno. Mělo by být pět řezů, protože řez je vyroben pro každou hodinu mezi časem zahájení a časem ukončení z JSON.

    ![dlaždice diagramu](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. Když je řez ve stavu `select * from sampletable` **Připraveno,** spusťte dotaz v databázi Azure SQL a ověřte, že data byla vložena do tabulky uloženou procedurou.

   ![Výstupní data](./media/data-factory-stored-proc-activity/output.png)

   Podrobné informace o monitorování kanálů Azure Data Factory najdete [v tématu Monitorování kanálu.](data-factory-monitor-manage-pipelines.md)

## <a name="specify-an-input-dataset"></a>Určení vstupní datové sady
V návodu uložená aktivita procedury nemá žádné vstupní datové sady. Pokud zadáte vstupní datovou sadu, aktivita uložené procedury se nespustí, dokud nebude k dispozici řez vstupní datové sady (ve stavu Připraveno). Datová sada může být externí datová sada (která není vytvořena jinou aktivitou ve stejném kanálu) nebo interní datovou sadou, která je vytvořena předcházející aktivitou (aktivitou, která je spuštěna před touto aktivitou). Můžete zadat více vstupních datových sad pro aktivitu uložené procedury. Pokud tak učiníte, uložená aktivita procedury se spustí pouze v případě, že jsou k dispozici všechny řezy vstupní datové sady (ve stavu Připraveno). Vstupní datovou sadu nelze spotřebovat v uložené proceduře jako parametr. Používá se pouze ke kontrole závislosti před spuštěním aktivity uložené procedury.

## <a name="chaining-with-other-activities"></a>Řetězení s jinými aktivitami
Pokud chcete zřetězit upstream aktivity s touto aktivitou, zadejte výstup upstream aktivity jako vstup této aktivity. Pokud tak učiníte, uložená aktivita procedury nespustí, dokud upstream aktivita dokončí a výstupní datová sada upstream aktivity je k dispozici (ve stavu Připraveno). Můžete zadat výstupní datové sady více upstream aktivit jako vstupní datové sady aktivity uložené procedury. Pokud tak učiníte, uložená aktivita procedury se spustí pouze v případě, že jsou k dispozici všechny řezy vstupní datové sady.

V následujícím příkladu je výstup aktivity kopírování: OutputDataset, což je vstup aktivity uložené procedury. Proto uložená aktivita procedury nespustí, dokud se nedokončí aktivita kopírování a výstupDataset řez je k dispozici (ve stavu Připraveno). Pokud zadáte více vstupních datových sad, aktivita uložené procedury se nespustí, dokud nebudou k dispozici všechny řezy vstupní datové sady (ve stavu Připraveno). Vstupní datové sady nelze použít přímo jako parametry aktivity uložené procedury.

Další informace o aktivitách řetězení naleznete [v tématu více aktivit v kanálu](data-factory-create-pipelines.md#multiple-activities-in-a-pipeline)

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

Podobně chcete-li propojit aktivitu procedury úložiště s **navazujícími aktivitami** (aktivity, které se spustí po dokončení aktivity uložené procedury), zadejte výstupní datovou sadu aktivity uložené procedury jako vstup navazující aktivity v kanálu.

> [!IMPORTANT]
> Při kopírování dat do Azure SQL Database nebo SQL Server, můžete nakonfigurovat **SqlSink** v aktivitě kopírování vyvolat uloženou proceduru pomocí **sqlWriterStoredProcedureName** vlastnost. Další informace naleznete v tématu [Invoke stored procedure from copy activity](data-factory-invoke-stored-procedure-from-copy-activity.md). Podrobnosti o vlastnosti naleznete v následujících článcích konektoru: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), SQL [Server](data-factory-sqlserver-connector.md#copy-activity-properties).
> 
> Při kopírování dat z Azure SQL Database nebo SQL Server nebo Azure SQL Data Warehouse, můžete nakonfigurovat **SqlSource** v aktivitě kopírování vyvolat uloženou proceduru pro čtení dat ze zdrojové databáze pomocí **sqlReaderStoredProcedureName** vlastnost. Další informace naleznete v následujících článcích konektoru: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), SQL [Server](data-factory-sqlserver-connector.md#copy-activity-properties), Azure SQL [Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

## <a name="json-format"></a>Formát JSON
Zde je formát JSON pro definování aktivity uložené procedury:

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
| description |Text popisující, k čemu se aktivita používá |Ne |
| type | Musí být nastavena na: **SqlServerStoredProcedure** | Ano |
| Vstupy | Nepovinný parametr. Pokud zadáte vstupní datovou sadu, musí být k dispozici (ve stavu "Připraveno") pro spuštění aktivity uložené procedury. Vstupní datovou sadu nelze spotřebovat v uložené proceduře jako parametr. Používá se pouze ke kontrole závislosti před spuštěním aktivity uložené procedury. |Ne |
| Výstupy | Je nutné zadat výstupní datovou sadu pro aktivitu uložené procedury. Výstupní datová sada určuje **plán** pro aktivitu uložené procedury (hodinové, týdenní, měsíční atd.). <br/><br/>Výstupní datová sada musí používat **propojenou službu,** která odkazuje na Azure SQL Database nebo Azure SQL Data Warehouse nebo sql server databáze, ve kterém chcete, aby uložená procedura spustit. <br/><br/>Výstupní datová sada může sloužit jako způsob, jak předat výsledek uložené procedury pro následné zpracování jinou aktivitou ([zřetězení aktivity](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) v kanálu. Data Factory však automaticky nezapisuje výstup uložené procedury do této datové sady. Je uložená procedura, která zapisuje do tabulky SQL, na kterou odkazuje výstupní datová sada. <br/><br/>V některých případech může být výstupní datovou sadou **fiktivní datová sada**, která se používá pouze k určení plánu pro spuštění aktivity uložené procedury. |Ano |
| storedProcedureName |Zadejte název uložené procedury v databázi Azure SQL nebo azure SQL data warehouse nebo SQL Server databáze, která je reprezentována propojenou službou, kterou používá výstupní tabulka. |Ano |
| storedProcedureParameters |Zadejte hodnoty pro parametry uložené procedury. Pokud potřebujete předat hodnotu null pro parametr, použijte syntaxi: "param1": null (všechna malá písmena). Informace o používání této vlastnosti naleznete v následující ukázce. |Ne |

## <a name="passing-a-static-value"></a>Předání statické hodnoty
Nyní zvažme přidání dalšího sloupce s názvem Scénář v tabulce obsahující statickou hodnotu nazvanou "Vzorek dokumentu".

![Vzorové údaje 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

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

Nyní předaj parametr **Scenario** a hodnotu z aktivity uložené procedury. Oddíl **typeProperties** v předchozí ukázce vypadá tak, že se zobrazuje následující úryvek:

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

**Datová datová sada dat:**

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
