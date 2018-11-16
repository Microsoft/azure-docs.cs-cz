---
title: Kopírování dat do a z Azure SQL Database Managed Instance pomocí služby Azure Data Factory | Dokumentace Microsoftu
description: Další informace o tom, jak přesunout data z Azure SQL Database Managed Instance pomocí služby Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: jingwang
ms.openlocfilehash: 561e672436c38cd0b3e637b794662483fc630676
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51706717"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-using-azure-data-factory"></a>Kopírování dat do a z Azure SQL Database Managed Instance pomocí služby Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory pro kopírování dat z a do Azure SQL Database Managed Instance. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Můžete kopírovat data z Azure SQL Database Managed Instance pro jakékoli podporovaného úložiště dat jímky nebo kopírování dat ze všech podporovaných zdrojů úložišť dat do Managed Instance. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor Azure SQL Database Managed Instance podporuje:

- Kopírování dat pomocí **SQL** nebo **Windows** ověřování.
- Jako zdroj načítání dat pomocí jazyka SQL nebo uloženou proceduru.
- Jako jímku přidávání dat do cílové tabulky nebo volání uložené procedury s vlastní logikou během kopírování.

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít kopírování dat z Azure SQL Database Managed Instance, který se nachází ve virtuální síti, musíte nastavit modul Integration Runtime ve stejné virtuální síti s přístupem k databázi. Zobrazit [modul Integration Runtime](create-self-hosted-integration-runtime.md) , kde najdete podrobnosti.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory, které jsou specifické pro konektor Azure SQL Database Managed Instance.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro Azure SQL Database Managed Instance propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **systému SQL Server** | Ano |
| připojovací řetězec |Zadejte připojovací řetězec informace potřebné pro připojení k Managed Instance pomocí ověřování SQL nebo ověřování Windows. Přečtěte si v následujícím příkladu. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| uživatelské jméno |Pokud používáte ověřování Windows, zadejte uživatelské jméno. Příklad: **domainname\\uživatelské jméno**. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). |Ne |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Zřízení modul Integration Runtime ve stejné virtuální síti jako Managed Instance. |Ano |

>[!TIP]
>Pokud spuštění chybě s kódem chyby jako "UserErrorFailedToConnectToSqlServer" a zpráva jako "limit relace pro databázi je XXX a bylo ho dosaženo.", přidejte `Pooling=false` připojovací řetězec a zkuste to znovu.

**Příklad 1: Použití ověřování SQL**

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

**Příklad 2: Použití ověřování Windows**

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností, které podporuje datová sada Azure SQL Database Managed Instance.

Pro kopírování dat z/do Azure SQL Database Managed Instance, nastavte vlastnost typ datové sady na **SqlServerTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ datové sady, musí být nastavena na: **SqlServerTable** | Ano |
| tableName |Název tabulky nebo zobrazení v instanci databáze na propojenou službu. | Ne pro zdroj, Ano pro jímku |

**Příklad**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastnosti podporované službou Azure SQL Database Managed Instance zdroje a jímky.

### <a name="azure-sql-database-managed-instance-as-source"></a>Azure SQL Database Managed Instance jako zdroj

Ke zkopírování dat z Azure SQL Database Managed Instance, nastavte typ zdroje v aktivitě kopírování do **SqlSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu zdroje aktivity kopírování musí být nastavena na: **SqlSource** | Ano |
| sqlReaderQuery |Použijte vlastní dotaz SQL číst data. Příklad: `select * from MyTable`. |Ne |
| sqlReaderStoredProcedureName |Název uložené procedury, která čte data ze zdrojové tabulky. Příkaz SELECT v uložené proceduře musí být poslední příkaz jazyka SQL. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou: páry název/hodnota. Názvy a použití malých a velkých parametry musí odpovídat názvům a použití malých a velkých parametrů uložené procedury. |Ne |

**Odkazuje na mějte na paměti**

- Pokud **sqlReaderQuery** je určená pro SqlSource, spustí aktivita kopírování tohoto dotazu na zdroji Managed Instance se mají získat data. Alternativně můžete zadat uložené procedury tak, že zadáte **sqlReaderStoredProcedureName** a **storedProcedureParameters** (Pokud uložená procedura parametry).
- Pokud nezadáte vlastnost "sqlReaderQuery" nebo "sqlReaderStoredProcedureName", sloupce definované v oddílu "struktura" datové sady JSON se používají k vytvoření dotazu (`select column1, column2 from mytable`) ke spuštění Managed Instance. Pokud není definice datové sady "struktura", jsou vybrány všechny sloupce z tabulky.

**Příklad: Použití jazyka SQL**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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
                "referenceName": "<Managed Instance input dataset name>",
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

### <a name="azure-sql-database-managed-instance-as-sink"></a>Azure SQL Database Managed Instance jako jímka

Ke zkopírování dat do Azure SQL Database Managed Instance, nastavte typ jímky v aktivitě kopírování do **SqlSink**. Následující vlastnosti jsou podporovány v aktivitě kopírování **jímky** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Nastavte vlastnost typ jímky aktivity kopírování: **SqlSink** | Ano |
| WriteBatchSize |Vloží data do tabulky SQL writeBatchSize dosáhne velikosti vyrovnávací paměti.<br/>Povolené hodnoty jsou: celé číslo (počet řádků). |Ne (výchozí: 10000) |
| writeBatchTimeout |Čekací doba pro dávkové operace insert dokončit před vypršením časového limitu.<br/>Povolené hodnoty jsou: časový interval. Příklad: "00: 30:00" (30 minut). |Ne |
| preCopyScript |Zadejte dotaz SQL pro aktivitu kopírování ke spuštění před zápisu dat do Managed Instance. To se ji volat pouze jednou za kopírování spustit. Tato vlastnost slouží k vyčištění předem načtená data. |Ne |
| sqlWriterStoredProcedureName |Název uložené procedury, která definuje, jak použít zdroj dat do cílové tabulky, například na upsertuje proveďte nebo transformace pomocí vlastní obchodní logikou. <br/><br/>Mějte na paměti, bude tuto uloženou proceduru **za batch**. Pokud budete chtít provádět operace, která pouze spustí jednou a nemá nic dělat se zdrojovými daty, třeba delete nebo truncate, použijte `preCopyScript` vlastnost. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou: páry název/hodnota. Názvy a použití malých a velkých parametry musí odpovídat názvům a použití malých a velkých parametrů uložené procedury. |Ne |
| sqlWriterTableType |Zadejte název tabulky typu použitého v uložené proceduře. Aktivitu kopírování, která zpřístupňuje data přesouvá do dočasné tabulky s tímto typem tabulky. Uloženou proceduru kód pak sloučit data kopírovaná s existujícími daty. |Ne |

> [!TIP]
> Při kopírování dat do Azure SQL Database Managed Instance, aktivita kopírování ve výchozím nastavení připojí data do tabulky jímky. K provedení UPSERT nebo další obchodní logiku, použijte uloženou proceduru v SqlSink. Přečtěte si další podrobnosti o [volání uložené procedury pro SQL jímky](#invoking-stored-procedure-for-sql-sink).

**Příklad 1: Připojení dat**

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
                "referenceName": "<Managed Instance output dataset name>",
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

**Příklad 2: Volání uložené procedury během kopírování pro upsertovat**

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
                "referenceName": "<Managed Instance output dataset name>",
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

## <a name="identity-columns-in-the-target-database"></a>Sloupce identity v cílové databázi

Tato část poskytuje příklad, který kopíruje data ze zdrojové tabulky s žádný sloupec identity do cílové tabulky se sloupcem identity.

**Zdrojová tabulka**

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

**Cílová tabulka**

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

Všimněte si, že cílová tabulka obsahuje sloupec identity.

**Zdroj definice JSON datové sady**

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " SqlServerTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTbl"
        }
    }
}
```

**Určení definice JSON datové sady**

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "TargetTbl"
        }
    }
}
```

Všimněte si, že jako zdrojové a cílové tabulce mají jiné schéma (cílový má další sloupec s identitou). V tomto scénáři budete muset zadat **struktura** vlastnost v definici datové sady target, která neobsahuje sloupec identity.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Vyvolání uložené procedury SQL jímky

Při kopírování dat do Azure SQL Database Managed Instance, uživatelem zadaný uložené procedury může nakonfigurovat a volána s další parametry.

Uloženou proceduru lze použít při integrovaná funkce kopírování mechanismy neodesílají účel. Používá se obvykle při upsert (insert a update) nebo další zpracování (sloučení sloupců vyhledávání dalších hodnot, vložení do několika tabulek, atd.) je potřeba udělat před posledním vložení zdrojová data v cílové tabulce.

Následující příklad ukazuje, jak provést funkcí upsert do tabulky ve spravované instanci pomocí uložené procedury. Za předpokladu, že vstupní data a tabulky jímky "Marketing" obsahuje tři sloupce: ID profilu, stavu a kategorie. Provedení na základě sloupce "ProfileID" funkcí upsert a platí jen pro konkrétní kategorie.

**Výstupní datová sada**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

V části SqlSink definujte takto v aktivitě kopírování.

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

V databázi definujte jako SqlWriterStoredProcedureName uložená procedura se stejným názvem. Zpracovává vstupní data ze zadaného zdroje a sloučení do výstupní tabulky. Název parametru typu tabulky v uložené proceduře by měl být stejný jako "tableName" definovaný v datové sadě.

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
      VALUES (source.ProfileID, source.State, source.Category)
END
```

V databázi Definujte typ tabulky se stejným názvem jako sqlWriterTableType. Všimněte si, že schéma typu tabulky musí být stejná jako schéma vrácené funkcí vstupní data.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL，
)
```

Uložená procedura funkce využívá možnosti [Table-Valued parametry](https://msdn.microsoft.com/library/bb675163.aspx).

>[!NOTE]
>Při zápisu na datový typ Money/Smallmoney ve vyvolání uložené procedury, může se zaokrouhlí hodnoty. Zadejte odpovídající typ dat v TVP jako desetinné místo peníze/Smallmoney zmírnit. 

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Mapování datového typu pro Azure SQL Database Managed Instance

Při kopírování dat z/do Azure SQL Database Managed Instance, se používají následující mapování z datových typů Managed Instance na Azure Data Factory dočasné datové typy. Zobrazit [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) Další informace o způsobu, jakým aktivitu kopírování, která mapuje typ zdroje schéma a data na jímce.

| Azure SQL Database Managed Instance datového typu | Data factory dočasné datový typ |
|:--- |:--- |
| bigint |Int64 |
| Binární |Byte] |
| Bit |Logická hodnota |
| Char |Řetězec, Char] |
| date |DateTime |
| Datum a čas |DateTime |
| datetime2 |DateTime |
| DateTimeOffset |DateTimeOffset |
| Decimal |Decimal |
| Atribut FILESTREAM (varbinary(max)) |Byte] |
| Float |Double |
| image |Byte] |
| int |Datový typ Int32 |
| peníze |Decimal |
| nchar |Řetězec, Char] |
| ntext |Řetězec, Char] |
| Číselné |Decimal |
| nvarchar |Řetězec, Char] |
| Real |Jednoduchá |
| ROWVERSION |Byte] |
| smalldatetime |DateTime |
| smallint |Int16 |
| Smallmoney |Decimal |
| SQL_VARIANT |Objekt * |
| text |Řetězec, Char] |
| time |Časový interval |
| časové razítko |Byte] |
| tinyint |Int16 |
| UniqueIdentifier |Guid |
| varbinary |Byte] |
| varchar |Řetězec, Char] |
| xml |XML |

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
