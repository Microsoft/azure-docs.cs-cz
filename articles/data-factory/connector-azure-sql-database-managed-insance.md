---
title: Kopírování dat do a z Azure SQL Database Managed Instance pomocí služby Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak pro přesun dat do a z Azure SQL Database Managed Instance pomocí služby Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: jingwang
ms.openlocfilehash: dc84f6a6a2e10bb7bdbe859eefc71c74769595de
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55215111"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Kopírování dat do a z Azure SQL Database Managed Instance pomocí služby Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory ke zkopírování dat do a z Azure SQL Database Managed Instance. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z Azure SQL Database Managed Instance do jakékoli podporovaného úložiště dat jímky. Můžete také můžete kopírovat data ze všech podporovaných zdrojů úložišť dat do spravované instance. Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor Azure SQL Database Managed Instance podporuje:

- Kopírování dat pomocí ověřování SQL nebo Windows.
- Jako zdroj načítání dat pomocí jazyka SQL nebo uloženou proceduru.
- Jako jímka, připojení dat do cílové tabulky nebo volání uložené procedury s vlastní logikou během kopírování.

SQL Server [s funkcí Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) není nyní podporován. 

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít kopírování dat z Azure SQL Database Managed Instance, který se nachází ve virtuální síti, nastavte místní prostředí integration runtime, které máte přístup k databázi. Další informace najdete v tématu [modulu runtime integrace v místním prostředí](create-self-hosted-integration-runtime.md).

Zřízení vašeho místního prostředí integration runtime ve stejné virtuální síti jako spravovanou instanci, ujistěte se, že váš počítač modulu runtime integrace je v jiné podsíti, než spravovaná instance. Pokud zřizujete vaše místní prostředí integration runtime v jiné virtuální sítě než spravovaná instance, můžete použít buď partnerský vztah virtuální sítě nebo virtuální sítě pro připojení k virtuální síti. Další informace najdete v tématu [vaši aplikaci do Azure SQL Database Managed Instance připojit](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory, které jsou specifické pro konektor Azure SQL Database Managed Instance.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro službu Azure SQL Database Managed Instance propojené se podporují následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na **SqlServer**. | Ano. |
| připojovací řetězec |Tato vlastnost určuje informace připojovací řetězec, který je nezbytný pro připojení do spravované instance pomocí ověřování SQL nebo ověřování Windows. Další informace najdete v tématu v následujících příkladech. Vyberte **SecureString** k bezpečnému ukládání connectionString informace ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). |Ano. |
| uživatelské jméno |Tato vlastnost určuje uživatelské jméno, pokud používáte ověřování Windows. Příkladem je **domainname\\uživatelské jméno**. |Ne. |
| heslo |Tato vlastnost určuje heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Vyberte **SecureString** k bezpečnému ukládání connectionString informace ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). |Ne. |
| connectVia | To [prostředí integration runtime](concepts-integration-runtime.md) se používá pro připojení k úložišti. Zřizování v místním prostředí integration runtime ve stejné virtuální síti jako spravovanou instanci. |Ano. |

>[!TIP]
>Může se zobrazit kód chyby: "UserErrorFailedToConnectToSqlServer" a zobrazí se zpráva jako "limit relace pro databázi je XXX a bylo ho dosaženo." Pokud k této chybě dochází, přidejte `Pooling=false` připojovací řetězec a zkuste to znovu.

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

**Příklad 2: Používat ověřování Windows**

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro použití k definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností, které podporuje datová sada Azure SQL Database Managed Instance.

Pro kopírování dat do a z Azure SQL Database Managed Instance, nastavte vlastnost typ datové sady na **SqlServerTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na **SqlServerTable**. | Ano. |
| tableName |Tato vlastnost je název tabulky nebo zobrazení v instanci databáze na propojenou službu. | Ne pro zdroj. Ano pro jímku. |

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro použití k definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastnosti podporované službou Azure SQL Database Managed Instance zdroje a jímky.

### <a name="azure-sql-database-managed-instance-as-a-source"></a>Azure SQL Database Managed Instance jako zdroj

Ke zkopírování dat z Azure SQL Database Managed Instance, nastavte typ zdroje v aktivitě kopírování do **SqlSource**. V části zdrojová data aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na **SqlSource**. | Ano. |
| sqlReaderQuery |Tuto vlastnost používá vlastní dotaz SQL číst data. Příklad: `select * from MyTable`. |Ne. |
| sqlReaderStoredProcedureName |Tato vlastnost je název uložené procedury, která čte data ze zdrojové tabulky. Příkaz SELECT v uložené proceduře musí být poslední příkaz jazyka SQL. |Ne. |
| storedProcedureParameters |Tyto parametry jsou pro uloženou proceduru.<br/>Povolené hodnoty jsou páry název nebo hodnota. Názvy a použití malých a velkých parametry musí odpovídat názvům a použití malých a velkých parametrů uložené procedury. |Ne. |

Je třeba počítat s následujícím:

- Pokud **sqlReaderQuery** je určená pro **SqlSource**, spustí aktivita kopírování tohoto dotazu na zdroji spravované instance se mají získat data. Můžete také zadat uložené procedury tak, že zadáte **sqlReaderStoredProcedureName** a **storedProcedureParameters** pokud používá parametry uložené procedury.
- Pokud nezadáte buď **sqlReaderQuery** nebo **sqlReaderStoredProcedureName** vlastnost, sloupce definované v oddílu "struktura" datové sady JSON se používají k vytvoření dotazu. Dotaz `select column1, column2 from mytable` spouští pro spravovanou instanci. Pokud není definice datové sady "struktura", jsou vybrány všechny sloupce z tabulky.

**Příklad: Použít dotaz SQL**

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

### <a name="azure-sql-database-managed-instance-as-a-sink"></a>Azure SQL Database Managed Instance jako jímka

Ke zkopírování dat do Azure SQL Database Managed Instance, nastavte typ jímky v aktivitě kopírování do **SqlSink**. V části jímky aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Nastavte vlastnost typ jímky aktivity kopírování **SqlSink**. | Ano. |
| WriteBatchSize |Tato vlastnost vkládá nějaká data do tabulky SQL writeBatchSize dosáhne velikosti vyrovnávací paměti.<br/>Povolené hodnoty jsou celá čísla pro počet řádků. |Ne (výchozí: 10,000). |
| writeBatchTimeout |Tato vlastnost určuje doba čekání na dokončení před vypršením časového limitu operace insert služby batch.<br/>Povolené jsou hodnoty pro časové období. Příkladem je "00: 30:00," což je 30 minut. |Ne. |
| preCopyScript |Tato vlastnost určuje dotaz SQL pro aktivitu kopírování ke spuštění před zápisu dat do spravované instance. Je vyvolána pouze jednou za kopírování spustit. Tuto vlastnost můžete použít k vyčištění dat předem. |Ne. |
| sqlWriterStoredProcedureName |Tento název je pro uloženou proceduru, která definuje, jak použít zdroj dat do cílové tabulky. Příklady postupy jsou to upsertuje nebo transformace pomocí vlastní obchodní logikou. <br/><br/>Tuto uloženou proceduru se *za batch*. Chcete-li provést operaci, která se spustí pouze jednou a nemá nic se zdrojovými daty, například delete nebo truncate, použijte `preCopyScript` vlastnost. |Ne. |
| storedProcedureParameters |Tyto parametry slouží pro uloženou proceduru.<br/>Povolené hodnoty jsou páry název nebo hodnota. Názvy a použití malých a velkých parametry musí odpovídat názvům a použití malých a velkých parametrů uložené procedury. |Ne. |
| sqlWriterTableType |Tato vlastnost určuje název typu tabulky má být použit v uložené proceduře. Aktivita kopírování zpřístupňuje data přesouvá do dočasné tabulky s tímto typem tabulky. Uložená procedura kód pak sloučit data, která se kopíruje s existujícími daty. |Ne. |

> [!TIP]
> Zkopírování dat do Azure SQL Database Managed Instance, aktivita kopírování připojí data do tabulky jímky ve výchozím nastavení. K provedení upsert nebo další obchodní logiku, použijte uloženou proceduru v SqlSink. Další informace najdete v tématu [vyvolat uloženou proceduru z SQL jímky](#invoke-a-stored-procedure-from-a-sql-sink).

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

**Příklad 2: Vyvolání uložené procedury během kopírování pro upsertovat**

Přečtěte si další podrobnosti o [vyvolat uloženou proceduru z SQL jímky](#invoke-a-stored-procedure-from-a-sql-sink).

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

V následujícím příkladu kopíruje data ze zdrojové tabulky se sloupcem bez identity do cílové tabulky se sloupcem identity.

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

Všimněte si, že zdrojové a cílové tabulce mají jiné schéma. Cílová tabulka obsahuje sloupec identity. V tomto scénáři Určete vlastnost "struktura" v cílové definici datové sady, která neobsahuje sloupec identity.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Vyvolání uložené procedury z SQL jímky

Když se data zkopírovala do Azure SQL Database Managed Instance, uložené procedury můžete nakonfigurovat a vyvolání další parametry, které zadáte.

Uloženou proceduru můžete použít, když integrovaná funkce kopírování mechanismy neslouží účel. Používá se obvykle při k upsert (aktualizace + insert) nebo další zpracování je třeba provést před posledním vložení zdrojová data v cílové tabulce. Dodatečné zpracování může obsahovat úlohy, jako je sloučení sloupců vyhledávání další hodnoty a vložení do několika tabulek.

Následující příklad ukazuje, jak provést funkcí upsert do tabulky ve spravované instanci pomocí uložené procedury. Ukázka předpokládá, že vstupní data a "Marketing" tabulky jímky máte tři sloupce: ID profilu, stavu a kategorie. Proveďte upsert podle sloupce pro ID profilu a platí pro konkrétní kategorii.

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

Definujte části SqlSink v aktivitě kopírování takto:

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

V databázi definujte jako SqlWriterStoredProcedureName uložená procedura se stejným názvem. Zpracovává vstupní data ze zadaného zdroje a sloučí je do výstupní tabulky. Název parametru typu tabulky v uložené proceduře je stejný jako "tableName", který je definován v datové sadě.

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

V databázi Definujte typ tabulky se stejným názvem jako sqlWriterTableType. Schéma typu tabulky je stejný jako schéma vrácené funkcí vstupní data.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL，
)
```

Uložená procedura funkce využívá možnosti [parametrů table-valued](https://msdn.microsoft.com/library/bb675163.aspx).

>[!NOTE]
>Pokud zapíšete do **peníze/Smallmoney** datový typ vyvoláním uložené procedury, může být zaokrouhlena hodnoty. Zadejte odpovídající typ dat v parametrů table-valued jako **desítkové** místo **peníze/Smallmoney** ke zmírnění tohoto problému. 

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Mapování datového typu pro Azure SQL Database Managed Instance

Po zkopírování dat do a z Azure SQL Database Managed Instance se používají následující mapování z Azure SQL Database Managed Instance datových typů na Azure Data Factory dočasné datové typy. Informace o tom, jak aktivity kopírování mapuje zdrojový typ schéma a data jímky, najdete v článku [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md).

| Azure SQL Database Managed Instance datového typu | Azure Data Factory dočasné datový typ |
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
| SQL_VARIANT |Objekt |
| text |Řetězec, Char] |
| time |Časový interval |
| časové razítko |Byte] |
| tinyint |Int16 |
| UniqueIdentifier |Guid |
| varbinary |Byte] |
| varchar |Řetězec, Char] |
| xml |XML |

>[!NOTE]
> Datové typy, které mapují na typ desetinné dočasné aktuálně Azure Data Factory podporuje přesnost až 28. Pokud máte data, která vyžaduje přesností větší než 28, vezměte v úvahu převodu na řetězec v příkazu jazyka SQL.

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
