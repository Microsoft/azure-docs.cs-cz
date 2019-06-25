---
title: Kopírování dat do a z SQL serveru pomocí služby Azure Data Factory | Dokumentace Microsoftu
description: Další informace o tom, jak přesunout data z databáze serveru SQL Server, který je místně nebo ve Virtuálním počítači Azure pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: jingwang
ms.openlocfilehash: 62845557f33fd9c4f3c2ec4e239213c75101955d
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275978"
---
# <a name="copy-data-to-and-from-sql-server-using-azure-data-factory"></a>Kopírování dat do a z SQL serveru pomocí služby Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, který používáte:"]
> * [Verze 1](v1/data-factory-sqlserver-connector.md)
> * [Aktuální verze](connector-sql-server.md)

Tento článek ukazuje, jak použít aktivitu kopírování ke kopírování dat z a do databáze serveru SQL Server ve službě Azure Data Factory. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Můžete kopírovat data z/do databáze SQL serveru do jakékoli podporovaného úložiště dat jímky nebo kopírování dat ze všech podporovaných zdrojů úložišť dat do databáze serveru SQL Server. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor SQL serveru podporuje:

- SQL Server version 2016, 2014, 2012, 2008 R2, 2008, and 2005
- Kopírování dat pomocí **SQL** nebo **Windows** ověřování.
- Jako zdroj načítání dat pomocí jazyka SQL nebo uloženou proceduru.
- Jako jímku přidávání dat do cílové tabulky nebo volání uložené procedury s vlastní logikou během kopírování.

>[!NOTE]
>SQL Server **[s funkcí Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)** není nyní podporován tímto konektorem. Chcete-li vyřešit, můžete použít [obecný konektor ODBC](connector-odbc.md) a ovladač ODBC systému SQL Server. Postupujte podle [návod](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017) konfigurací ODBC ovladač stahování a připojovací řetězec.

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít kopírování dat z databáze systému SQL Server, který není veřejně přístupná, musíte nastavit modul Integration Runtime. Zobrazit [modul Integration Runtime](create-self-hosted-integration-runtime.md) , kde najdete podrobnosti. Prostředí Integration Runtime poskytuje integrované ovladač databáze systému SQL Server, proto není nutné ručně nainstalovat všechny ovladače při kopírování dat z/do databáze serveru SQL Server.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní konektor databáze SQL serveru.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu SQL serveru jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **SqlServer** | Ano |
| connectionString |Zadejte připojovací řetězec informace potřebné pro připojení k databázi SQL serveru pomocí ověřování SQL nebo ověřování Windows. Podívejte se na následující ukázky.<br/>Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory. Můžete také umístit hesla ve službě Azure Key Vault, a pokud se jedná o přijetí změn ověřování SQL `password` konfigurace z připojovacího řetězce. Podívejte se na příklad JSON pod tabulkou a [Store přihlašovacích údajů ve službě Azure Key Vault](store-credentials-in-key-vault.md) článku s dalšími podrobnostmi. |Ano |
| userName |Pokud používáte ověřování Windows, zadejte uživatelské jméno. Příklad: **domainname\\uživatelské jméno**. |Ne |
| password |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). |Ne |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Můžete použít modul Integration Runtime nebo prostředí Azure Integration Runtime (Pokud vaše úložiště dat je veřejně dostupná). Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

>[!TIP]
>Pokud spuštění chybě s kódem chyby jako "UserErrorFailedToConnectToSqlServer" a zpráva jako "limit relace pro databázi je XXX a bylo ho dosaženo.", přidejte `Pooling=false` připojovací řetězec a zkuste to znovu.

**Příklad 1: použití ověřování SQL**

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

**Příklad 2: použití ověřování SQL pomocí hesla ve službě Azure Key Vault**

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

**Příklad 3: použití ověřování Windows**

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností, které podporuje datové sady SQL Server.

Pro kopírování dat z/do databáze SQL serveru, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na: **SqlServerTable** | Ano |
| tableName |Název tabulky nebo zobrazení v instanci databáze SQL serveru, který propojená služba odkazuje na. | Ne pro zdroj, Ano pro jímku |

**Příklad:**

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
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností systému SQL Server zdroje a jímky podporované.

### <a name="sql-server-as-source"></a>SQL Server jako zdroj

Pro kopírování dat z SQL serveru, nastavte typ zdroje v aktivitě kopírování do **SqlSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na: **SqlSource** | Ano |
| sqlReaderQuery |Použijte vlastní dotaz SQL číst data. Příklad: `select * from MyTable`. |Ne |
| sqlReaderStoredProcedureName |Název uložené procedury, která čte data ze zdrojové tabulky. Příkaz SELECT v uložené proceduře musí být poslední příkaz jazyka SQL. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou: páry název/hodnota. Názvy a použití malých a velkých parametry musí odpovídat názvům a použití malých a velkých parametrů uložené procedury. |Ne |

**Odkazuje na mějte na paměti:**

- Pokud **sqlReaderQuery** je určená pro SqlSource, spustí aktivita kopírování tohoto dotazu na zdroji systému SQL Server se mají získat data. Alternativně můžete zadat uložené procedury tak, že zadáte **sqlReaderStoredProcedureName** a **storedProcedureParameters** (Pokud uložená procedura parametry).
- Pokud nezadáte "sqlReaderQuery" nebo "sqlReaderStoredProcedureName" sloupce definované v oddílu "struktura" datové sady JSON se používají k vytvoření dotazu (`select column1, column2 from mytable`) ke spuštění systému SQL Server. Pokud není definice datové sady "struktura", jsou vybrány všechny sloupce z tabulky.

**Příklad: použití jazyka SQL**

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

**Příklad: použití uložené procedury**

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

**Uložená procedura definice:**

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

### <a name="sql-server-as-sink"></a>SQL Server jako jímka

> [!TIP]
> Další informace o podporovaných zápisu chování, konfigurace a osvědčených postupů z [osvědčený postup pro načítání dat do služby SQL Server](#best-practice-for-loading-data-into-sql-server).

Ke zkopírování dat do SQL serveru, nastavte typ jímky v aktivitě kopírování do **SqlSink**. Následující vlastnosti jsou podporovány v aktivitě kopírování **jímky** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ jímky aktivity kopírování musí být nastavena: **SqlSink** | Ano |
| writeBatchSize |Počet řádků, která se vloží do tabulky SQL **dávce**.<br/>Povolené hodnoty jsou: celé číslo (počet řádků). Ve výchozím nastavení služby Data Factory dynamicky určí příslušné batch velikost podle velikosti řádku. |Ne |
| writeBatchTimeout |Čekací doba pro dávkové operace insert dokončit před vypršením časového limitu.<br/>Povolené hodnoty jsou: časový interval. Příklad: "00: 30:00" (30 minut). |Ne |
| preCopyScript |Zadejte dotaz SQL pro aktivitu kopírování ke spuštění před zápis dat do systému SQL Server. To se ji volat pouze jednou za kopírování spustit. Tato vlastnost slouží k vyčištění předem načtená data. |Ne |
| sqlWriterStoredProcedureName |Název uložené procedury, která definuje, jak použít zdroj dat do cílové tabulky.<br/>Mějte na paměti, bude tuto uloženou proceduru **za batch**. Pokud budete chtít provádět operace, která pouze spustí jednou a nemá nic dělat se zdrojovými daty, třeba delete nebo truncate, použijte `preCopyScript` vlastnost. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou: páry název/hodnota. Názvy a použití malých a velkých parametry musí odpovídat názvům a použití malých a velkých parametrů uložené procedury. |Ne |
| sqlWriterTableType |Zadejte název tabulky typu použitého v uložené proceduře. Aktivitu kopírování, která zpřístupňuje data přesouvá do dočasné tabulky s tímto typem tabulky. Uloženou proceduru kód pak sloučit data kopírovaná s existujícími daty. |Ne |

**Příklad 1: připojení dat**

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
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Příklad 2: volání uložené procedury během kopírování**

Přečtěte si další podrobnosti o [volání uložené procedury pro SQL jímky](#invoking-stored-procedure-for-sql-sink).

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
                "sqlWriterTableType": "CopyTestTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="best-practice-for-loading-data-into-sql-server"></a>Osvědčeným postupem pro načítání dat do SQL serveru

Při kopírování dat do SQL serveru může vyžadovat chování různých zápisu:

- **[Připojit](#append-data)** : zdroj data obsahují pouze nové záznamy;
- **[Upsert](#upsert-data)** : Moje zdrojová data mají vkládání a aktualizace.
- **[Přepsat](#overwrite-entire-table)** : Chcete znovu načíst tabulku celé dimenze pokaždé, když;
- **[Zápis o vlastní logiku](#write-data-with-custom-logic)** : Potřebuji další zpracování před posledním vkládání do cílové tabulky.

Odkazovat uvedeném pořadí oddíly o tom, jak nakonfigurovat v ADF a osvědčené postupy.

### <a name="append-data"></a>Připojení dat

Toto je výchozí chování tohoto konektoru jímky SQL Server a proveďte ADF **hromadné vložení** efektivně zapisovat do tabulky. Můžete jednoduše nakonfigurovat zdroj a odpovídajícím způsobem jímky v aktivitě kopírování.

### <a name="upsert-data"></a>Upsert dat

**Možnost mám** (navrhované zejména v případě, že máte ke kopírování velkých objemů dat): **přístup většina výkonné** provedete upsert je následující: 

- Za prvé, využívat [dočasnou tabulku](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) hromadné načtení všech záznamů pomocí aktivit kopírování. Jak operace u dočasných tabulek se neprotokolují, můžete načíst milióny záznamů v řádu sekund.
- Aktivita uložená procedura spuštění ve službě ADF použít [sloučit](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) (nebo INSERT/UPDATE) prohlášení a dočasné tabulky jako zdroje k provedení všech aktualizaci nebo vložení jako jedna transakce, snižuje množství výměn dat a protokolovat operace. Na konci aktivity uložené procedury lze až bude připravená na další cyklus upsert oříznutí dočasné tabulky. 

Jako příklad, ve službě Azure Data Factory vytvoříte kanál s **aktivita kopírování** zřetězené s **aktivity uložené procedury** v případě úspěchu. Nejprve kopíruje data ze zdrojového úložiště do dočasné tabulky databáze, třeba " **##UpsertTempTable**" jako název tabulky v datové sadě, pak ten volá uloženou proceduru ke sloučení do cílové tabulky zdroje dat z dočasné tabulky a vyčistit dočasné tabulky.

![Upsertovat](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

V databázi definujte uložená procedura s logikou SLOUČENÍ, podobně jako následující text, který ukazuje z výše uvedených aktivity uložené procedury. Za předpokladu, že cíl **marketingové** tabulky se třemi sloupci: **ID profilu**, **stavu**, a **kategorie**, a proveďte upsert na základě **ProfileID** sloupce.

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

**Možnost II:** Alternativně můžete se rozhodnout [vyvolat uloženou proceduru v aktivitě kopírování](#invoking-stored-procedure-for-sql-sink), při Poznámka: Tento přístup je provést pro každý řádek ve zdrojové tabulce místo využívání hromadné vložení jako výchozí přístup v aktivitě kopírování proto se nevejde pro upsert velkého rozsahu.

### <a name="overwrite-entire-table"></a>Přepsat celou tabulku

Můžete nakonfigurovat **preCopyScript** vlastnost v aktivitě kopírování jímky, v takovém případě pro každé spuštění aktivity kopírování ADF spustí skript nejprve spusťte kopírování vložte data. Například pokud chcete přepsat celou tabulku s nejnovější data, můžete určit skript, který nejprve odstranit všechny záznamy před hromadného načtení nová data ze zdroje.

### <a name="write-data-with-custom-logic"></a>Zápis dat o vlastní logiku

Podobné, jak je popsáno v [Upsert data](#upsert-data) oddílu, když potřebujete provést další zpracování před posledním vložení zdrojová data do cílové tabulky, můžete) pro široké možnosti škálování a načíst do dočasné tabulky a vyvoláte uloženého procedura, nebo b) vyvolání uložené procedury během kopírování.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Vyvolání uložené procedury SQL jímky

Při kopírování dat do databáze SQL serveru, můžete také nakonfigurovat a vyvolat uloženou proceduru s další parametry zadané uživatelem.

> [!TIP]
> Volání uložené procedury zpracovává na data řádek po řádku namísto hromadné operace, která není určeno pro kopírování velkého rozsahu. Další informace z [osvědčený postup pro načítání dat do služby SQL Server](#best-practice-for-loading-data-into-sql-server).

Uloženou proceduru můžete použít, když integrovaná funkce kopírování mechanismy neslouží účel, například použít zvláštní zpracování před posledním vložení zdrojová data do cílové tabulky. Některé příklady vysokého jsou sloučení sloupců, vyhledat další hodnoty a vložení do více než jednou tabulkou.

Následující příklad ukazuje způsob použití uloženou proceduru provedete upsert do tabulky v databázi serveru SQL Server. Předpokládejme, který vstupní data a jímku **marketingové** tabulka jednotlivých obsahovat tři sloupce: **ID profilu**, **stavu**, a **kategorie**. Proveďte upsert na základě **ProfileID** sloupce a použijte je jenom pro konkrétní kategorie.

**Výstupní datová sada:** "tableName" by měl být stejný název parametru typu tabulky v uložené proceduře (viz níže uvedený skript uložené procedury).

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
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

Definovat **SQL jímky** následující části v aktivitě kopírování.

```json
"sink": {
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing",
    "storedProcedureParameters": {
        "category": {
            "value": "ProductA"
        }
    }
}
```

V databázi, definovat uložená procedura se stejným názvem jako **SqlWriterStoredProcedureName**. Zpracovává vstupní data ze zadaného zdroje a sloučí do výstupní tabulky. Název parametru typu tabulky v uložené proceduře by měl být stejný jako **tableName** definované v datové sadě.

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

V databázi Definujte typ tabulky se stejným názvem jako sqlWriterTableType. Všimněte si, že schéma typu tabulky musí být stejná jako schéma vrácené funkcí vstupní data.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL
)
```

Uložená procedura funkce využívá možnosti [Table-Valued parametry](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-sql-server"></a>Datový typ mapování pro SQL server

Při kopírování dat z/do systému SQL Server, se používají následující mapování z datových typů SQL serveru do služby Azure Data Factory dočasné datových typů. Zobrazit [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) Další informace o způsobu, jakým aktivitu kopírování, která mapuje typ zdroje schéma a data na jímce.

| Datový typ SQL serveru | Data factory dočasné datový typ |
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
| ROWVERSION |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object |
| text |String, Char[] |
| time |TimeSpan |
| časové razítko |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> Pro mapování typů dat na typ desetinné dočasné aktuálně ADF podporují přesnost až 28. Pokud máte data s přesností větší než 28, zvažte možnost převést na řetězec v dotazu SQL.

## <a name="troubleshooting-connection-issues"></a>Řešení potíží s připojením

1. Konfigurace SQL serveru tak, aby přijímal vzdálená připojení. Spuštění **SQL Server Management Studio**, klikněte pravým tlačítkem na **server**a klikněte na tlačítko **vlastnosti**. Vyberte **připojení** ze seznamu a kontrola **povolit vzdálená připojení k serveru**.

    ![Povolit vzdálená připojení](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Zobrazit [nakonfigurovat možnosti konfigurace serveru vzdáleného přístupu](https://msdn.microsoft.com/library/ms191464.aspx) podrobné pokyny.

2. Spuštění **Správce konfigurace systému SQL Server**. Rozbalte **síťová konfigurace systému SQL Server** pro instanci a vyberte **protokoly pro MSSQLSERVER**. Měli byste vidět protokoly v pravém podokně. Povolte protokol TCP/IP kliknutím pravým tlačítkem myši **TCP/IP** a kliknete na **povolit**.

    ![Povolte protokol TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Zobrazit [povolení nebo zakázání síťového protokolu serveru](https://msdn.microsoft.com/library/ms191294.aspx) podrobnosti a alternativní způsoby povolení protokolu TCP/IP.

3. Ve stejném okně, dvakrát klikněte na panel **TCP/IP** spustit **vlastností protokolu TCP/IP** okna.
4. Přepněte **IP adresy** kartu. Posuňte se dolů viz **IPAll** oddílu. Poznamenejte si **TCP Port** (výchozí hodnota je **1433**).
5. Vytvoření **pravidla pro bránu Windows Firewall** na počítači za účelem povolený příchozí provoz přes tento port.  
6. **Ověření připojení**: Pro připojení k SQL serveru pomocí plně kvalifikovaného názvu, použijte SQL Server Management Studio z různých počítačů. Například: `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
