---
title: Kopírování dat do a z SQL Server pomocí Azure Data Factory | Microsoft Docs
description: Přečtěte si, jak přesunout data do a z SQL Server do místní databáze nebo na virtuálním počítači Azure pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: jingwang
ms.openlocfilehash: 7b266a21aabf37765de4f4f94cd3939cec697585
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058516"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Kopírování dat do a z SQL Server pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Azure Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-sqlserver-connector.md)
> * [Aktuální verze](connector-sql-server.md)

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z a do databáze SQL Server. Vytvoří se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor SQL Server se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)

Data z databáze SQL Server můžete kopírovat do libovolného podporovaného úložiště dat jímky. Případně můžete kopírovat data z libovolného podporovaného zdrojového úložiště dat do databáze SQL Server. Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor SQL Server podporuje:

- SQL Server verze 2016, 2014, 2012, 2008 R2, 2008 a 2005.
- Kopírování dat pomocí ověřování SQL nebo Windows.
- Jako zdroj načítání dat pomocí dotazu SQL nebo uložené procedury.
- Jako jímky, připojení dat do cílové tabulky nebo vyvolání uložené procedury s vlastní logikou během kopírování.

[SQL Server Express LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-2017) se nepodporuje.

>[!NOTE]
>Tento konektor teď nepodporuje SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) . Pokud chcete tento problém obejít, můžete použít [obecný konektor ODBC](connector-odbc.md) a ovladač SQL Server ODBC. Postupujte [podle pokynů ke](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017) stažení ovladače ODBC a konfigurací připojovacích řetězců.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které se používají k definování Data Factory entit specifických pro konektor databáze SQL Server.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu SQL Server jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na **SQLServer**. | Ano |
| connectionString |Zadejte informace **připojovacího řetězce** potřebné pro připojení k databázi SQL Server pomocí ověřování SQL nebo ověřování systému Windows. Přečtěte si následující ukázky.<br/>Označte toto pole jako **SecureString** a bezpečně ho uložte do Azure Data Factory. Heslo můžete také přidat do Azure Key Vault. Pokud se jedná o ověřování SQL, vyžádejte si `password` z připojovacího řetězce konfiguraci. Další informace najdete v příkladech JSON, které následují po tabulce, a [ukládají přihlašovací údaje v Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| userName |Pokud používáte ověřování systému Windows, zadejte uživatelské jméno. Příkladem je **domainname\\username**. |Ne |
| password |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Označte toto pole jako **SecureString** a bezpečně ho uložte do Azure Data Factory. Nebo můžete [odkazovat na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). |Ne |
| connectVia | Tento [modul runtime integrace](concepts-integration-runtime.md) se používá pro připojení k úložišti dat. Další informace najdete v části [požadavky](#prerequisites) . Pokud tento parametr nezadáte, použije se výchozí prostředí Azure Integration runtime. |Ne |

>[!TIP]
>Pokud dojde k chybě s kódem chyby "UserErrorFailedToConnectToSqlServer" a zprávou, jako je "omezení relace pro databázi je xxx a bylo dosaženo," přidejte `Pooling=false` do připojovacího řetězce a zkuste to znovu.

**Příklad 1: Použít ověřování SQL**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad 2: Použijte ověřování SQL s heslem v Azure Key Vault**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad 3: Použít ověřování systému Windows**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;"
            },
            "userName": "<domain\\username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
     }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. V této části najdete seznam vlastností podporovaných datovou sadou SQL Server.

Chcete-li kopírovat data z a do databáze SQL Server, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavená na **SQLServer**. | Ano |
| schema | Název schématu. |Ne pro zdroj, Ano pro jímku  |
| table | Název tabulky/zobrazení |Ne pro zdroj, Ano pro jímku  |
| tableName | Název tabulky nebo zobrazení se schématem. Tato vlastnost je podporována z důvodu zpětné kompatibility. Pro nové úlohy použijte `schema` a. `table` | Ne pro zdroj, Ano pro jímku |

**Příklad**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro použití k definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných zdrojem a jímkou SQL Server.

### <a name="sql-server-as-a-source"></a>SQL Server jako zdroj

Chcete-li kopírovat data z SQL Server, nastavte typ zdroje v aktivitě kopírování na **SqlSource**. V části zdroj aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **SqlSource**. | Ano |
| sqlReaderQuery |Použijte vlastní dotaz SQL číst data. Příklad: `select * from MyTable`. |Ne |
| sqlReaderStoredProcedureName |Tato vlastnost je název uložené procedury, která čte data ze zdrojové tabulky. Příkaz SELECT v uložené proceduře musí být poslední příkaz jazyka SQL. |Ne |
| storedProcedureParameters |Tyto parametry jsou pro uloženou proceduru.<br/>Povolené hodnoty jsou páry název nebo hodnota. Názvy a velikost písmen parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. |Ne |

**Ukazuje na poznámku:**

- Pokud je pro **SqlSource**zadaný **sqlReaderQuery** , aktivita kopírování spustí tento dotaz na zdroj SQL Server, aby se data získala. Uloženou proceduru lze také určit zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters** , pokud uložená procedura přijímá parametry.
- Pokud nezadáte buď **sqlReaderQuery** nebo **sqlReaderStoredProcedureName**, budou použity sloupce definované v oddílu Structure pro datovou sadu JSON pro vytvoření dotazu. Dotaz `select column1, column2 from mytable` se spustí na SQL Server. Pokud definice datové sady nemá "strukturu", všechny sloupce jsou vybrány z tabulky.

**Příklad: Použít dotaz SQL**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Příklad: Použít uloženou proceduru**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Definice uložené procedury**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sql-server-as-a-sink"></a>SQL Server jako jímky

> [!TIP]
> Přečtěte si další informace o podporovaných chováních, konfiguracích a osvědčených postupech pro zápis z [osvědčeného postupu pro načítání dat do SQL Server](#best-practice-for-loading-data-into-sql-server).

Chcete-li kopírovat data do SQL Server, nastavte typ jímky v aktivitě kopírování na **SqlSink**. V části jímka aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type jímky aktivity kopírování musí být nastavená na **SqlSink**. | Ano |
| writeBatchSize |Počet řádků, které mají být vloženy do tabulky SQL *na dávku*.<br/>Povolené hodnoty jsou celá čísla pro počet řádků. Ve výchozím nastavení Azure Data Factory dynamicky určí vhodnou velikost dávky na základě velikosti řádku. |Ne |
| writeBatchTimeout |Tato vlastnost určuje dobu čekání na dokončení operace dávkového vložení před vypršením časového limitu.<br/>Povolené hodnoty jsou pro časové rozpětí. Příkladem je "00:30:00" po dobu 30 minut. |Ne |
| preCopyScript |Tato vlastnost určuje dotaz SQL pro aktivitu kopírování, která se má spustit před zápisem dat do SQL Server. Vyvolá se jenom jednou pro každé spuštění kopírování. Tuto vlastnost můžete použít k vyčištění předem načtených dat. |Ne |
| sqlWriterStoredProcedureName | Název uložené procedury definující, jak se mají zdrojová data použít v cílové tabulce. <br/>Tato uložená procedura je *vyvolána pro každou dávku*. Pro operace, které se spouštějí jenom jednou a které nemají nic dělat se zdrojovými daty, například odstranit nebo zkrátit, použijte `preCopyScript` vlastnost. | Ne |
| storedProcedureTableTypeParameterName |Název parametru pro typ tabulky určený v uložené proceduře.  |Ne |
| sqlWriterTableType |Název typu tabulky, který se má použít v uložené proceduře Aktivita kopírování zpřístupňuje data, která jsou k dispozici v dočasné tabulce s tímto typem tabulky. Uložený kód procedury pak může sloučit data, která jsou kopírována se stávajícími daty. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou páry název-hodnota. Názvy a použití malých a velkých parametry musí odpovídat názvům a použití malých a velkých parametrů uložené procedury. | Ne |
| tableOption | Určuje, jestli se má automaticky vytvořit tabulka jímky, pokud na základě schématu zdroje neexistuje. Automatické vytváření tabulek není podporované, pokud jímka určuje uloženou proceduru nebo připravenou kopii nakonfigurovanou v aktivitě kopírování. Povolené hodnoty jsou: `none` (výchozí), `autoCreate`. |Ne |

**Příklad 1: Připojit data**

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
            }
        }
    }
]
```

**Příklad 2: Vyvolat uloženou proceduru během kopírování**

Další informace o [vyvolání uložené procedury z jímky SQL](#invoke-a-stored-procedure-from-a-sql-sink).

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="best-practice-for-loading-data-into-sql-server"></a>Osvědčené postupy načítání dat do SQL Server

Při kopírování dat do SQL Server může být nutné vyžadovat jiné chování při zápisu:

- [Připojit](#append-data): Moje zdrojová data obsahují pouze nové záznamy.
- [Upsert](#upsert-data): Moje zdrojová data obsahují jak vložené, tak i aktualizace.
- [Přepsat](#overwrite-the-entire-table): Chci pokaždé, když se pokaždé znovu načte celá tabulka dimenze.
- [Psaní pomocí vlastní logiky](#write-data-with-custom-logic): Potřebuji dodatečné zpracování před konečným vložením do cílové tabulky.

V příslušných částech najdete informace o tom, jak nakonfigurovat v Azure Data Factory a osvědčených postupech.

### <a name="append-data"></a>Připojit data

Připojení dat je výchozím chováním tohoto konektoru SQL Server jímky. Azure Data Factory hromadné vložení do tabulky efektivně. Zdroj a jímku můžete v aktivitě kopírování nakonfigurovat odpovídajícím způsobem.

### <a name="upsert-data"></a>Upsert dat

**Možnost 1:** Pokud máte ke kopírování velké množství dat, použijte následující postup k Upsert: 

- Nejdřív použijte [dočasnou tabulku](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) k hromadnému načtení všech záznamů pomocí aktivity kopírování. Vzhledem k tomu, že operace s dočasnými tabulkami nejsou protokolovány, můžete načíst miliony záznamů během několika sekund.
- Spuštěním aktivity uložené procedury v Azure Data Factory použijte příkaz [Merge](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) nebo INSERT/Update. Použijte dočasnou tabulku jako zdroj k provedení všech aktualizací nebo vložení jako jedné transakce. Tímto způsobem se sníží počet operací Round Trip a log. Na konci aktivity uložená procedura můžete dočasnou tabulku zkrátit, aby byla připravená na další Upsert cyklus.

Jako příklad můžete v Azure Data Factory vytvořit kanál s **aktivitou kopírování** zřetězenou s **aktivitou uložené procedury**. Předchozí kopie dat ze zdrojového úložiště do dočasné tabulky databáze, například **# #UpsertTempTable**, jako název tabulky v datové sadě. Potom druhá potom vyvolá uloženou proceduru ke sloučení zdrojových dat z dočasné tabulky do cílové tabulky a vyčištění dočasné tabulky.

![Upsertovat](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

V databázi definujte uloženou proceduru pomocí logiky sloučení, podobně jako v následujícím příkladu, který ukazuje z předchozí aktivity uložené procedury. Předpokládejme, že cílem je **marketingová** tabulka se třemi sloupci: **ProfileID**, **State**a **Category**. Proveďte Upsert na základě sloupce **ProfileID** .

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING ##UpsertTempTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE ##UpsertTempTable
END
```

**Možnost 2:** Také se můžete rozhodnout [vyvolat uloženou proceduru v rámci aktivity kopírování](#invoke-a-stored-procedure-from-a-sql-sink). Tento přístup spustí každý řádek ve zdrojové tabulce namísto použití hromadného vkládání jako výchozího přístupu v aktivitě kopírování, což není vhodné pro velké Upsert.

### <a name="overwrite-the-entire-table"></a>Přepsat celou tabulku

Vlastnost **preCopyScript** můžete nakonfigurovat v jímky aktivity kopírování. V takovém případě se pro každou aktivitu kopírování, která běží, Azure Data Factory spustí skript jako první. Potom spustí kopii pro vložení dat. Chcete-li například přepsat celou tabulku nejnovějšími daty, zadejte skript, který nejprve odstraní všechny záznamy před hromadnou zátěží nových dat ze zdroje.

### <a name="write-data-with-custom-logic"></a>Zápis dat pomocí vlastní logiky

Postup pro zápis dat pomocí vlastní logiky je podobný těm, které jsou popsané v části [Upsert data](#upsert-data) . Pokud potřebujete použít dodatečné zpracování před konečným vložením zdrojových dat do cílové tabulky, můžete pro velkou škálu provést jednu z následujících akcí: 

- Načíst do dočasné tabulky a poté vyvolat uloženou proceduru. 
- Vyvolat uloženou proceduru během kopírování.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Vyvolat uloženou proceduru z jímky SQL

Když kopírujete data do databáze SQL Server, můžete také nakonfigurovat a vyvolat uloženou proceduru zadanou uživatelem s dalšími parametry. Funkce uložené procedury využívá [parametry s hodnotou tabulky](https://msdn.microsoft.com/library/bb675163.aspx).

> [!TIP]
> Vyvolání uložené procedury zpracuje řádek data na řádku místo pomocí hromadné operace, kterou nedoporučujeme pro velké kopírování. Další informace z [osvědčeného postupu pro načítání dat do SQL Server](#best-practice-for-loading-data-into-sql-server).

Uloženou proceduru můžete použít, pokud předdefinované mechanismy kopírování neslouží k tomuto účelu. Příkladem je, že chcete použít dodatečné zpracování před konečným vložením zdrojových dat do cílové tabulky. Některé další příklady zpracování jsou, když chcete sloučit sloupce, vyhledat další hodnoty a vložit data do více než jedné tabulky.

Následující příklad ukazuje, jak použít uloženou proceduru k provedení Upsert do tabulky v databázi SQL Server. Předpokládejme, že vstupní data a **marketingová** tabulka jímky mají tři sloupce: **ProfileID**, **State**a **Category**. Proveďte Upsert na základě sloupce **ProfileID** a použijte ho jenom pro konkrétní kategorii s názvem "produkt".

1. V databázi Definujte typ tabulky se stejným názvem jako **sqlWriterTableType**. Schéma typu tabulky je stejné jako schéma vrácené vašimi vstupními daty.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL，
        [Category] [varchar](256) NOT NULL
    )
    ```

2. V databázi definujte uloženou proceduru se stejným názvem jako **SqlWriterStoredProcedureName**. Zpracovává vstupní data ze zadaného zdroje a sloučí je do výstupní tabulky. Název parametru typu tabulky v uložené proceduře je stejný jako **TableName** definovaný v datové sadě.

    ```sql
    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
    AS
    BEGIN
    MERGE [dbo].[Marketing] AS target
    USING @Marketing AS source
    ON (target.ProfileID = source.ProfileID and target.Category = @category)
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT MATCHED THEN
        INSERT (ProfileID, State, Category)
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. V Azure Data Factory v aktivitě kopírování definujte oddíl **jímky SQL** následujícím způsobem:

    ```json
    "sink": {
        "type": "SqlSink",
        "SqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "SqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="data-type-mapping-for-sql-server"></a>Mapování datových typů pro SQL Server

Při kopírování dat z a do SQL Server se z SQL Server datových typů používají následující mapování pro Azure Data Factory dočasných datových typů. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ k jímky, najdete v tématu [mapování schémat a datových typů](copy-activity-schema-and-type-mapping.md).

| SQL Server datový typ | Azure Data Factory pomocný datový typ |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |Datetimeoffset |
| Decimal |Decimal |
| Atribut FILESTREAM (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object |
| text |String, Char[] |
| time |TimeSpan |
| časové razítko |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| Varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> Pro datové typy, které jsou mapovány na mezihodnotový průběžný typ, aktuálně Azure Data Factory podporuje přesnost až 28. Pokud máte data, která vyžadují přesnost větší než 28, zvažte převod na řetězec v dotazu SQL.

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Vlastnosti aktivity GetMetadata

Pokud se chcete dozvědět víc o vlastnostech, podívejte se na [aktivitu GetMetadata](control-flow-get-metadata-activity.md) . 

## <a name="troubleshoot-connection-issues"></a>Řešení potíží s připojením

1. Nakonfigurujte instanci SQL Server tak, aby přijímala vzdálená připojení. Spusťte **SQL Server Management Studio**, klikněte pravým tlačítkem na **Server**a vyberte **vlastnosti**. V seznamu vyberte **připojení** a zaškrtněte políčko **pro povolení vzdáleného připojení k tomuto serveru** .

    ![Povolit vzdálená připojení](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Podrobný postup najdete v tématu [Konfigurace možnosti konfigurace serveru vzdáleného přístupu](https://msdn.microsoft.com/library/ms191464.aspx).

2. Spustit **SQL Server Configuration Manager** Rozbalte **SQL Server konfigurace sítě** pro požadovanou instanci a vyberte **protokoly pro MSSQLSERVER**. Protokoly se zobrazí v pravém podokně. Povolte protokol TCP/IP tak, že kliknete pravým tlačítkem na **TCP/IP** a vyberete **Povolit**.

    ![Povolit protokol TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Další informace a alternativní způsoby povolení protokolu TCP/IP najdete v tématu [Povolení nebo zakázání síťového protokolu serveru](https://msdn.microsoft.com/library/ms191294.aspx).

3. Ve stejném okně poklikejte na **TCP/IP** a spustí se okno **vlastností protokolu TCP/IP** .
4. Přepněte na kartu **IP adresy** . Posuňte se dolů a zobrazte část **IPAll** . Zapište **port TCP**. Výchozí hodnota je **1433**.
5. Vytvořte **pravidlo pro bránu Windows Firewall** na počítači, aby se povolil příchozí přenos prostřednictvím tohoto portu. 
6. **Ověřit připojení**: Pokud se chcete připojit k SQL Server pomocí plně kvalifikovaného názvu, použijte SQL Server Management Studio z jiného počítače. Příklad: `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md##supported-data-stores-and-formats).
