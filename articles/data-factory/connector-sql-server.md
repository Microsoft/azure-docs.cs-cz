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
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: jingwang
ms.openlocfilehash: 06535cd86e32d9e5d083bfde5f1c40e345a88640
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "40004615"
---
# <a name="copy-data-to-and-from-sql-server-using-azure-data-factory"></a>Kopírování dat do a z SQL serveru pomocí služby Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-sqlserver-connector.md)
> * [Aktuální verze](connector-sql-server.md)

Tento článek ukazuje, jak použít aktivitu kopírování ke kopírování dat z a do databáze serveru SQL Server ve službě Azure Data Factory. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Můžete kopírovat data z/do databáze SQL serveru do jakékoli podporovaného úložiště dat jímky nebo kopírování dat ze všech podporovaných zdrojů úložišť dat do databáze serveru SQL Server. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor SQL serveru podporuje:

- SQL Server verze 2016, 2014, 2012, 2008 R2, 2008 a 2005
- Kopírování dat pomocí **SQL** nebo **Windows** ověřování.
- Jako zdroj načítání dat pomocí jazyka SQL nebo uloženou proceduru.
- Jako jímku přidávání dat do cílové tabulky nebo volání uložené procedury s vlastní logikou během kopírování.

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít kopírování dat z databáze systému SQL Server, který není veřejně přístupná, musíte nastavit modul Integration Runtime. Zobrazit [modul Integration Runtime](create-self-hosted-integration-runtime.md) , kde najdete podrobnosti. Prostředí Integration Runtime poskytuje integrované ovladač databáze systému SQL Server, proto není nutné ručně nainstalovat všechny ovladače při kopírování dat z/do databáze serveru SQL Server.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní konektor databáze SQL serveru.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu SQL serveru jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **systému SQL Server** | Ano |
| připojovací řetězec |Zadejte připojovací řetězec informace potřebné pro připojení k databázi SQL serveru pomocí ověřování SQL nebo ověřování Windows. Přečtěte si v následujícím příkladu. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| uživatelské jméno |Pokud používáte ověřování Windows, zadejte uživatelské jméno. Příklad: **domainname\\uživatelské jméno**. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). |Ne |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Můžete použít modul Integration Runtime nebo prostředí Azure Integration Runtime (Pokud vaše úložiště dat je veřejně dostupná). Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

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

**Příklad 2: použití ověřování Windows**

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

Pro kopírování dat z/do databáze serveru SQL Server, nastavte vlastnost typ datové sady na **SqlServerTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ datové sady, musí být nastavena na: **SqlServerTable** | Ano |
| tableName |Název tabulky nebo zobrazení v instanci databáze SQL serveru, který propojená služba odkazuje na. | Ano |

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
| type | Vlastnost typu zdroje aktivity kopírování musí být nastavena na: **SqlSource** | Ano |
| sqlReaderQuery |Použijte vlastní dotaz SQL číst data. Příklad: `select * from MyTable`. |Ne |
| sqlReaderStoredProcedureName |Název uložené procedury, která čte data ze zdrojové tabulky. Příkaz SELECT v uložené proceduře musí být poslední příkaz jazyka SQL. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou: páry název/hodnota. Názvy a použití malých a velkých parametry musí odpovídat názvům a použití malých a velkých parametrů uložené procedury. |Ne |

**Odkazuje na mějte na paměti:**

- Pokud **sqlReaderQuery** je určená pro SqlSource, spustí aktivita kopírování tohoto dotazu na zdroji systému SQL Server se mají získat data. Alternativně můžete zadat uložené procedury tak, že zadáte **sqlReaderStoredProcedureName** a **storedProcedureParameters** (Pokud uložená procedura parametry).
- Pokud nezadáte "sqlReaderQuery" nebo "sqlReaderStoredProcedureName" sloupce definované v oddílu "struktura" datové sady JSON se používají k vytvoření dotazu (`select column1, column2 from mytable`) ke spuštění systému SQL Server. Pokud není definice datové sady "struktura", jsou vybrány všechny sloupce z tabulky.
- Při použití **sqlReaderStoredProcedureName**, stále je třeba zadat fiktivní **tableName** vlastnost v datové sadě JSON.

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

Ke zkopírování dat do SQL serveru, nastavte typ jímky v aktivitě kopírování do **SqlSink**. Následující vlastnosti jsou podporovány v aktivitě kopírování **jímky** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Nastavte vlastnost typ jímky aktivity kopírování: **SqlSink** | Ano |
| WriteBatchSize |Vloží data do tabulky SQL writeBatchSize dosáhne velikosti vyrovnávací paměti.<br/>Povolené hodnoty jsou: celé číslo (počet řádků). |Ne (výchozí: 10000) |
| writeBatchTimeout |Čekací doba pro dávkové operace insert dokončit před vypršením časového limitu.<br/>Povolené hodnoty jsou: časový interval. Příklad: "00: 30:00" (30 minut). |Ne |
| preCopyScript |Zadejte dotaz SQL pro aktivitu kopírování ke spuštění před zápis dat do systému SQL Server. To se ji volat pouze jednou za kopírování spustit. Tato vlastnost slouží k vyčištění předem načtená data. |Ne |
| sqlWriterStoredProcedureName |Název uložené procedury, která definuje, jak použít zdroj dat do cílové tabulky, například na upsertuje proveďte nebo transformace pomocí vlastní obchodní logikou. <br/><br/>Mějte na paměti, bude tuto uloženou proceduru **za batch**. Pokud budete chtít provádět operace, která pouze spustí jednou a nemá nic dělat se zdrojovými daty, třeba delete nebo truncate, použijte `preCopyScript` vlastnost. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou: páry název/hodnota. Názvy a použití malých a velkých parametry musí odpovídat názvům a použití malých a velkých parametrů uložené procedury. |Ne |
| sqlWriterTableType |Zadejte název tabulky typu použitého v uložené proceduře. Aktivitu kopírování, která zpřístupňuje data přesouvá do dočasné tabulky s tímto typem tabulky. Uloženou proceduru kód pak sloučit data kopírovaná s existujícími daty. |Ne |

> [!TIP]
> Při kopírování dat systému SQL Server, aktivita kopírování ve výchozím nastavení připojí data do tabulky jímky. K provedení UPSERT nebo další obchodní logiku, použijte uloženou proceduru v SqlSink. Přečtěte si další podrobnosti o [volání uložené procedury pro SQL jímky](#invoking-stored-procedure-for-sql-sink).

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

**Příklad 2: volání uložené procedury během kopírování pro upsertovat**

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

## <a name="identity-columns-in-the-target-database"></a>Sloupce identity v cílové databázi

Tato část poskytuje příklad, který kopíruje data ze zdrojové tabulky s žádný sloupec identity do cílové tabulky se sloupcem identity.

**Zdrojová tabulka:**

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

**Cílová tabulka:**

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

Při kopírování dat do databáze SQL serveru, zadán uživatel, může se konfigurují a vyvolání další parametry uložené procedury.

Uloženou proceduru lze použít při integrovaná funkce kopírování mechanismy neodesílají účel. Používá se obvykle při upsert (insert a update) nebo další zpracování (sloučení sloupců vyhledávání dalších hodnot, vložení do několika tabulek, atd.) je potřeba udělat před posledním vložení zdrojová data v cílové tabulce.

Následující příklad ukazuje způsob použití uloženou proceduru provedete upsert do tabulky v databázi serveru SQL Server. Za předpokladu, že vstupní data a tabulky jímky "Marketing" obsahuje tři sloupce: ID profilu, stavu a kategorie. Provedení na základě sloupce "ProfileID" funkcí upsert a platí jen pro konkrétní kategorie.

**Výstupní datová sada**

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

## <a name="data-type-mapping-for-sql-server"></a>Datový typ mapování pro SQL server

Při kopírování dat z/do systému SQL Server, se používají následující mapování z datových typů SQL serveru do služby Azure Data Factory dočasné datových typů. Zobrazit [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) Další informace o způsobu, jakým aktivitu kopírování, která mapuje typ zdroje schéma a data na jímce.

| Datový typ SQL serveru | Data factory dočasné datový typ |
|:--- |:--- |
| bigint |Int64 |
| Binární |Byte] |
| Bit |Logická hodnota |
| Char |Řetězec, Char] |
| datum |DateTime |
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

## <a name="troubleshooting-connection-issues"></a>Řešení potíží s problémy s připojením

1. Konfigurace SQL serveru tak, aby přijímal vzdálená připojení. Spuštění **SQL Server Management Studio**, klikněte pravým tlačítkem na **server**a klikněte na tlačítko **vlastnosti**. Vyberte **připojení** ze seznamu a kontrola **povolit vzdálená připojení k serveru**.

    ![Povolit vzdálená připojení](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Zobrazit [nakonfigurovat možnosti konfigurace serveru vzdáleného přístupu](https://msdn.microsoft.com/library/ms191464.aspx) podrobné pokyny.

2. Spuštění **Správce konfigurace systému SQL Server**. Rozbalte **síťová konfigurace systému SQL Server** pro instanci a vyberte **protokoly pro MSSQLSERVER**. Měli byste vidět protokoly v pravém podokně. Povolte protokol TCP/IP kliknutím pravým tlačítkem myši **TCP/IP** a kliknete na **povolit**.

    ![Povolte protokol TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Zobrazit [povolení nebo zakázání síťového protokolu serveru](https://msdn.microsoft.com/library/ms191294.aspx) podrobnosti a alternativní způsoby povolení protokolu TCP/IP.

3. Ve stejném okně, dvakrát klikněte na panel **TCP/IP** spustit **vlastností protokolu TCP/IP** okna.
4. Přepněte **IP adresy** kartu. Posuňte se dolů viz **IPAll** oddílu. Poznamenejte si ** TCP Port ** (výchozí hodnota je **1433**).
5. Vytvoření **pravidla pro bránu Windows Firewall** na počítači za účelem povolený příchozí provoz přes tento port.  
6. **Ověření připojení**: pro připojení k SQL serveru pomocí plně kvalifikovaného názvu, použijte SQL Server Management Studio z různých počítačů. Například: `"<machine>.<domain>.corp.<company>.com,1433"`.


## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
