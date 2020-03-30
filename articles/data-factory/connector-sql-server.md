---
title: Kopírování dat do a ze serveru SQL Server
description: Zjistěte, jak přesunout data do a z databáze SQL Serveru, která je místní nebo ve virtuálním počítači Azure pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: e7f91d60a0e181690859da64e491c4c0fa1e1a3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238717"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Kopírování dat do a ze serveru SQL Server pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Azure Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-sqlserver-connector.md)
> * [Aktuální verze](connector-sql-server.md)

Tento článek popisuje, jak použít aktivitu kopírování v Azure Data Factory ke kopírování dat z databáze SQL Serveru a do databáze serveru SQL Server. Vychází z článku [přehledu aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor SQL Server je podporován pro následující aktivity:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)
- [Aktivita getMetadata](control-flow-get-metadata-activity.md)

Data z databáze serveru SQL Server můžete zkopírovat do libovolného podporovaného úložiště dat jímky. Nebo můžete zkopírovat data z libovolného úložiště dat podporovaného zdroje do databáze serveru SQL Server. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivity kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Konkrétně tento konektor SQL Server podporuje:

- Verze serveru SQL Server 2016, 2014, 2012, 2008 R2, 2008 a 2005.
- Kopírování dat pomocí ověřování SQL nebo Windows.
- Jako zdroj načítání dat pomocí dotazu SQL nebo uložené procedury.
- Jako jímka, připojení dat k cílové tabulce nebo vyvolání uložené procedury s vlastní logikou během kopírování.

[SQL Server Express LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-2017) není podporován.

>[!NOTE]
>SQL Server [vždy šifrované](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) není podporována tímto konektorem nyní. Chcete-li obejít, můžete použít [obecný konektor ODBC](connector-odbc.md) a ovladač SQL Server ODBC. Postupujte [podle těchto pokynů](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017) s konfiguracemi stažení ovladače ODBC a připojovacího řetězce.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Data Factory specifických pro konektor databáze serveru SQL Server.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu SQL Server jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na **sqlserver**. | Ano |
| připojovací řetězec |Zadejte informace **connectionString,** které jsou potřebné pro připojení k databázi serveru SQL Server pomocí ověřování SQL nebo ověřování systému Windows. Viz následující ukázky.<br/>Můžete také umístit heslo v Azure Key Vault. Pokud se jedná o ověřování `password` SQL, vyžadujete konfiguraci z připojovacího řetězce. Další informace naleznete v příkladu JSON za tabulkou a [přihlašovacími údaji úložiště v úložišti klíčů Azure](store-credentials-in-key-vault.md). |Ano |
| userName |Pokud používáte ověřování systému Windows, zadejte uživatelské jméno. Příkladem je **uživatelské\\jméno domény**. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Označte toto pole jako **SecureString,** abyste ho bezpečně uložili v Azure Data Factory. Nebo můžete [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). |Ne |
| connectVia | Tento [integrační runtime](concepts-integration-runtime.md) se používá pro připojení k úložišti dat. Další informace naleznete v části [Požadavky.](#prerequisites) Pokud není zadán, použije se výchozí doba runtime integrace Azure. |Ne |

>[!TIP]
>Pokud stisknete chybu s kódem chyby "UserErrorFailedToConnectToSqlServer" a zprávou jako "Limit `Pooling=false` relace pro databázi je XXX a bylo dosaženo," přidejte do připojovacího řetězce a opakujte akci.

**Příklad 1: Použití ověřování SQL**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad 2: Použití ověřování SQL s heslem v trezoru klíčů Azure**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
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

**Příklad 3: Použití ověřování systému Windows**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;",
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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datových sad.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou serveru SQL Server.

Chcete-li kopírovat data z databáze serveru SQL Server a do ní, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena na **sqlservertable**. | Ano |
| Schématu | Název schématu. |Ne pro zdroj, Ano pro umyvadlo  |
| tabulka | Název tabulky/zobrazení. |Ne pro zdroj, Ano pro umyvadlo  |
| tableName | Název tabulky/zobrazení se schématem. Tato vlastnost je podporována pro zpětnou kompatibilitu. Pro nové pracovní `schema` `table`vytížení, použití a . | Ne pro zdroj, Ano pro umyvadlo |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem a jímkou serveru SQL Server.

### <a name="sql-server-as-a-source"></a>SQL Server jako zdroj

Chcete-li kopírovat data ze serveru SQL Server, nastavte typ zdroje v aktivitě kopírování na **SqlSource**. V části zdroje aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena na **sqlsource**. | Ano |
| sqlReaderQuery |Ke čtení dat použijte vlastní dotaz SQL. Příklad: `select * from MyTable`. |Ne |
| sqlReaderStoredProcedureName |Tato vlastnost je název uložené procedury, která čte data ze zdrojové tabulky. Poslední příkaz SQL musí být příkaz SELECT v uložené proceduře. |Ne |
| storedProcedureParameters |Tyto parametry jsou pro uloženou proceduru.<br/>Povolené hodnoty jsou dvojice názvů nebo hodnot. Názvy a písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. |Ne |
| Isolationlevel | Určuje chování uzamčení transakce pro zdroj SQL. Povolené hodnoty jsou: **ReadCommitted** (výchozí), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snímek**. Další podrobnosti naleznete v [tomto dokumentu.](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) | Ne |

**Body k poznámce:**

- Pokud je pro **SqlSource**zadán **příkaz sqlReaderQuery** , spustí aktivita kopírování tento dotaz proti zdroji serveru SQL Server, aby získala data. Můžete také zadat uloženou proceduru zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters,** pokud uložená procedura přebírá parametry.
- Pokud nezadáte **sqlReaderQuery** nebo **sqlReaderStoredProcedureName**, sloupce definované v části "struktura" datové sady JSON se používají k vytvoření dotazu. Dotaz `select column1, column2 from mytable` je spuštěn proti serveru SQL Server. Pokud definice datové sady nemá "strukturu", jsou z tabulky vybrány všechny sloupce.

**Příklad: Použití dotazu SQL**

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

**Příklad: Použití uložené procedury**

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

### <a name="sql-server-as-a-sink"></a>SQL Server jako jímka

> [!TIP]
> Další informace o podporovaných chování zápisu, konfigurace a osvědčené postupy z [osvědčených postupů pro načítání dat do SQL Server](#best-practice-for-loading-data-into-sql-server).

Chcete-li zkopírovat data do serveru SQL Server, nastavte typ jímky v aktivitě kopírování do **aplikace SqlSink**. V části jímka aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type jímky aktivity kopírování musí být nastavena na **sqlsink**. | Ano |
| writeBatchSize |Počet řádků, které mají být vloženy do tabulky SQL *na dávku*.<br/>Povolené hodnoty jsou celá čísla pro počet řádků. Ve výchozím nastavení Azure Data Factory dynamicky určuje příslušnou velikost dávky na základě velikosti řádku. |Ne |
| writeBatchTimeout |Tato vlastnost určuje dobu čekání operace dávkové vložení, která má být dokončena před časovým limitem.<br/>Povolené hodnoty jsou pro časový rozsah. Příkladem je "00:30:00" po dobu 30 minut. Pokud není zadána žádná hodnota, výchozí časový limit je "02:00:00". |Ne |
| preCopyScript |Tato vlastnost určuje dotaz SQL pro aktivitu kopírování, která má být spuštěna před zápisem dat do serveru SQL Server. Je vyvolána pouze jednou za kopírování spustit. Tuto vlastnost můžete použít k vyčištění přednačtených dat. |Ne |
| sqlWriterStoredProcedureName | Název uložené procedury, která definuje, jak použít zdrojová data do cílové tabulky. <br/>Tato uložená procedura je *vyvolána na dávku*. Pro operace, které běží pouze jednou a nemají nic společného se zdrojovými `preCopyScript` daty, například odstranit nebo zkrátit, použijte vlastnost. | Ne |
| storedProcedureTableTypeParameterName |Název parametru typu tabulky zadaný v uložené proceduře.  |Ne |
| sqlWriterTableType |Název typu tabulky, který má být použit v uložené proceduře. Aktivita kopírování zpřístupní data přesunutá v dočasné tabulce s tímto typem tabulky. Uložený kód procedury pak může sloučit data, která se kopírují s existujícími daty. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou dvojice názvů a hodnot. Názvy a písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. | Ne |
| tableOption | Určuje, zda se má automaticky vytvořit tabulka jímky, pokud neexistuje na základě zdrojového schématu. Automatické vytváření tabulek není podporováno, pokud jímka určuje uloženou proceduru nebo fázovaná kopie je nakonfigurována v aktivitě kopírování. Povolené hodnoty `none` jsou: `autoCreate`(výchozí), . |Ne |

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

**Příklad 2: Vyvolání uložené procedury během kopírování**

Další podrobnosti z [vyvolat uloženou proceduru z jímky SQL](#invoke-a-stored-procedure-from-a-sql-sink).

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

## <a name="best-practice-for-loading-data-into-sql-server"></a>Osvědčený postup pro načítání dat do serveru SQL Server

Při kopírování dat do serveru SQL Server může vyžadovat odlišné chování zápisu:

- [Připojit](#append-data): Zdrojová data mají pouze nové záznamy.
- [Upsert](#upsert-data): Moje zdrojová data mají vložit i aktualizace.
- [Přepsat](#overwrite-the-entire-table): Chci pokaždé znovu načíst celou tabulku dimenzí.
- [Napište s vlastní logikou](#write-data-with-custom-logic): Potřebuji další zpracování před konečným vložením do cílové tabulky.

Podívejte se na příslušné části, jak nakonfigurovat v Azure Data Factory a doporučené postupy.

### <a name="append-data"></a>Připojit data

Připojení dat je výchozí chování tohoto konektoru jímky serveru SQL Server. Azure Data Factory provádí hromadné vkládání pro efektivní zápis do vaší tabulky. Můžete nakonfigurovat zdroj a jímky odpovídajícím způsobem v aktivitě kopírování.

### <a name="upsert-data"></a>Upsert dat

**Možnost 1:** Pokud máte velké množství dat ke kopírování, použijte následující přístup k provedení upsert: 

- Nejprve použijte [dočasnou tabulku](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) k hromadnému načtení všech záznamů pomocí aktivity kopírování. Vzhledem k tomu, že operace s dočasnými tabulkami nejsou protokolovány, můžete načíst miliony záznamů během několika sekund.
- Spusťte aktivitu uložené procedury v Azure Data Factory a použijte příkaz [MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) nebo INSERT/UPDATE. Pomocí dočasné tabulky jako zdroje můžete provádět všechny aktualizace nebo vložení jako jednu transakci. Tímto způsobem se sníží počet zpátečních cest a operací protokolu. Na konci aktivity uložené procedury může být tabulka temp zkrácena, aby byla připravena na další cyklus upsert.

Jako příklad v Azure Data Factory můžete vytvořit kanál s **aktivitou Copy** zřetězenou s **aktivitou uložené procedury**. První z nich zkopíruje data ze zdrojového úložiště do dočasné tabulky databáze, například **##UpsertTempTable**, jako název tabulky v datové sadě. Pak druhý vyvolá uloženou proceduru pro sloučení zdrojových dat z dočasné tabulky do cílové tabulky a vyčištění dočasné tabulky.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

V databázi definujte uloženou proceduru s logikou MERGE, jako je následující příklad, na který se ukazuje z předchozí aktivity uložené procedury. Předpokládejme, že cílem je **tabulka Marketing** se třemi sloupci: **ProfileID**, **State**a **Category**. Proveďte upsert na základě sloupce **ProfileID.**

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

**Možnost č. 2:** Můžete také [vyvolat uloženou proceduru v rámci aktivity kopírování](#invoke-a-stored-procedure-from-a-sql-sink). Tento přístup spustí každý řádek ve zdrojové tabulce namísto použití hromadné vložení jako výchozí přístup v aktivitě kopírování, což není vhodné pro velké škálování upsert.

### <a name="overwrite-the-entire-table"></a>Přepsání celé tabulky

Vlastnost **preCopyScript** můžete nakonfigurovat v jímce aktivity kopírování. V tomto případě pro každou aktivitu kopírování, která běží, Azure Data Factory spustí skript jako první. Pak spustí kopii vložit data. Chcete-li například přepsat celou tabulku nejnovějšími daty, zadejte skript, který nejprve odstraní všechny záznamy před hromadným načtením nových dat ze zdroje.

### <a name="write-data-with-custom-logic"></a>Zápis dat pomocí vlastní logiky

Kroky pro zápis dat s vlastní logikou jsou podobné těm, které jsou popsány v části [dat Upsert.](#upsert-data) Pokud potřebujete použít další zpracování před konečným vložením zdrojových dat do cílové tabulky, můžete ve velkém měřítku provést jednu ze dvou věcí: 

- Načtěte do dočasné tabulky a potom vyvolat uloženou proceduru. 
- Vyvolat uloženou proceduru během kopírování.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Vyvolání uložené procedury z jímky SQL

Při kopírování dat do databáze serveru SQL Server můžete také nakonfigurovat a vyvolat uživatelem zadanou uloženou proceduru s dalšími parametry. Funkce uložené procedury využívá [parametry s hodnotou tabulky](https://msdn.microsoft.com/library/bb675163.aspx).

> [!TIP]
> Vyvolání uložené procedury zpracovává data řádek po řádku namísto pomocí hromadné operace, kterou nedoporučujeme pro rozsáhlé kopírování. Další informace naleznete v [článku Doporučené informace o načítání dat do serveru SQL Server](#best-practice-for-loading-data-into-sql-server).

Uloženou proceduru můžete použít, když integrované kopírovací mechanismy neslouží účelu. Příkladem je, když chcete použít další zpracování před konečným vložením zdrojových dat do cílové tabulky. Některé další příklady zpracování jsou, když chcete sloučit sloupce, vyhledat další hodnoty a vložit data do více než jedné tabulky.

Následující ukázka ukazuje, jak použít uloženou proceduru provést upsert do tabulky v databázi SERVERU SQL. Předpokládejme, že vstupní data a tabulka **marketingu** jímky mají každý ze tří sloupců: **ProfileID**, **State**a **Category**. Proveďte upsert na základě sloupce **ProfileID** a použijte jej pouze pro určitou kategorii s názvem "ProductA".

1. V databázi definujte typ tabulky se stejným názvem jako **sqlWriterTableType**. Schéma typu tabulky je stejné jako schéma vrácené vstupními daty.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. V databázi definujte uloženou proceduru se stejným názvem jako **sqlWriterStoredProcedureName**. Zpracovává vstupní data ze zadaného zdroje a slučuje se do výstupní tabulky. Název parametru typu tabulky v uložené proceduře je stejný jako **název_tabulky** definovaný v datové sadě.

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

3. V Azure Data Factory definujte oddíl **jímky SQL** v aktivitě kopírování takto:

    ```json
    "sink": {
        "type": "SqlSink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="data-type-mapping-for-sql-server"></a>Mapování datových typů pro SQL Server

Při kopírování dat z a na SQL Server následující mapování se používají z datových typů SQL Server na dočasné datové typy Azure Data Factory. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ do jímky, naleznete v [tématu Schémata a mapování datových typů](copy-activity-schema-and-type-mapping.md).

| Datový typ serveru SQL Server | Dočasný datový typ Azure Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binární |Bajt[] |
| bitové |Logická hodnota |
| char |Řetězec, Znak[] |
| date |DateTime |
| Datum a čas |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Desetinné číslo |Desetinné číslo |
| ATRIBUT FILESTREAM (varbinary(max)) |Bajt[] |
| Plovoucí desetinná čárka |Double |
| image |Bajt[] |
| int |Int32 |
| Peníze |Desetinné číslo |
| Nchar |Řetězec, Znak[] |
| Ntext |Řetězec, Znak[] |
| numerické |Desetinné číslo |
| nvarchar |Řetězec, Znak[] |
| reálná |Single |
| Rowversion |Bajt[] |
| Smalldatetime |DateTime |
| smallint |Int16 |
| Smallmoney |Desetinné číslo |
| Sql_variant |Objekt |
| text |Řetězec, Znak[] |
| time |TimeSpan |
| časové razítko |Bajt[] |
| tinyint |Int16 |
| uniqueidentifier |Identifikátor GUID |
| Varbinary |Bajt[] |
| varchar |Řetězec, Znak[] |
| xml |XML |

>[!NOTE]
> U datových typů, které se mapují na desítkový prozatímní typ, aktuálně Azure Data Factory podporuje přesnost až 28. Pokud máte data, která vyžaduje přesnost větší než 28, zvažte převod na řetězec v dotazu SQL.

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Vlastnosti aktivity GetMetadata

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="troubleshoot-connection-issues"></a>Řešení potíží s připojením

1. Nakonfigurujte instanci serveru SQL Server tak, aby přijímala vzdálená připojení. Spusťte **sql server Management Studio**, klepněte pravým tlačítkem myši na **server**a vyberte **příkaz Vlastnosti**. V seznamu vyberte **Připojení** a zaškrtněte políčko **Povolit vzdálená připojení k tomuto serveru.**

    ![Povolit vzdálená připojení](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Podrobné kroky naleznete v [tématu Konfigurace konfigurace serveru pro vzdálený přístup](https://msdn.microsoft.com/library/ms191464.aspx).

2. Spusťte **nástroj SQL Server Configuration Manager**. Rozbalte **konfiguraci sítě serveru SQL Server** pro požadovanou instanci a vyberte **protokoly pro mssqlserver**. Protokoly se zobrazí v pravém podokně. Povolte protokol TCP/IP klepnutím pravým tlačítkem myši na **protokol TCP/IP** a výběrem **možnosti Povolit**.

    ![Povolit protokol TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Další informace a alternativní způsoby povolení protokolu TCP/IP naleznete v [tématu Povolení nebo zakázání síťového protokolu serveru](https://msdn.microsoft.com/library/ms191294.aspx).

3. Ve stejném okně poklepejte na **protokol TCP/IP** a spusťte okno **Vlastnosti protokolu TCP/IP.**
4. Přepněte na kartu **IP adresy.** Posuňte se dolů a prohlédněte si sekci **IPAll.** Poznamenejte si **port TCP**. Výchozí hodnota je **1433**.
5. Vytvořte **pravidlo pro bránu Windows Firewall** v počítači, které umožní příchozí přenosy prostřednictvím tohoto portu. 
6. **Ověření připojení**: Chcete-li se připojit k serveru SQL Server pomocí plně kvalifikovaného názvu, použijte aplikaci SQL Server Management Studio z jiného počítače. Příklad: `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu Podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
