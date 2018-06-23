---
title: Kopírování dat do nebo ze serveru SQL Server pomocí Azure Data Factory | Microsoft Docs
description: Další informace o tom, jak přesunout data z databáze serveru SQL Server, který je místně nebo v virtuálního počítače Azure pomocí Azure Data Factory.
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
ms.openlocfilehash: cca35d75f6d5560a621d377ae544eeba41434962
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316375"
---
# <a name="copy-data-to-and-from-sql-server-using-azure-data-factory"></a>Kopírování dat z SQL serveru pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](v1/data-factory-sqlserver-connector.md)
> * [Verze 2 – Preview](connector-sql-server.md)

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat z a do databáze serveru SQL Server. Vychází [zkopírujte aktivity přehled](copy-activity-overview.md) článek, který představuje obecný přehled aktivity kopírování.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [konektor serveru SQL Server v V1](v1/data-factory-sqlserver-connector.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Můžete zkopírovat data z/do databáze SQL serveru do úložiště dat žádné podporované podřízený nebo zkopírování dat z jakékoli úložiště podporované zdroje dat do databáze systému SQL Server. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor systému SQL Server podporuje:

- SQL Server verze 2016, 2014, 2012, 2008 R2, 2008 a 2005
- Kopírování dat pomocí **SQL** nebo **Windows** ověřování.
- Jako zdroj načítání dat pomocí dotazu SQL nebo uloženou proceduru.
- Jako jímku přidávání dat do cílové tabulky nebo volání uložené procedury s vlastní logikou během kopírování.

## <a name="prerequisites"></a>Požadavky

Chcete-li použít kopírování dat z databáze serveru SQL Server, který není veřejně přístupný, nastavení modulu Runtime Self-hosted integrace. V tématu [Self-hosted integrace Runtime](create-self-hosted-integration-runtime.md) článku. Modul Runtime integrace poskytuje integrované ovladač databáze systému SQL Server, proto nemusíte ručně nainstalovat všechny ovladače při kopírování dat z/do databáze systému SQL Server.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnosti, které slouží k určení konkrétní entity služby Data Factory ke konektoru databáze systému SQL Server.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro služby SQL serveru propojená se podporují následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavena na: **SqlServer** | Ano |
| připojovací řetězec |Zadejte připojovací řetězec informace potřebné pro připojení k databázi systému SQL Server pomocí ověřování SQL nebo ověřování systému Windows. Naleznete v následujícím příkladu, a přidáte zlepšit komunikaci oddělení zahrnout další vlastnosti AlwaysOn např. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| uživatelské jméno |Zadejte uživatelské jméno, pokud používáte ověřování systému Windows. Příklad: **domainname\\uživatelské jméno**. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). |Ne |
| connectVia | [Integrace Runtime](concepts-integration-runtime.md) který se má použít pro připojení k úložišti. (Pokud je veřejně přístupná data store), můžete použít modul Runtime integrace Self-hosted nebo Runtime integrace Azure. Pokud není zadaný, použije výchozí Runtime integrace Azure. |Ne |

**Příklad 1: pomocí ověřování SQL.**

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

**Příklad 2: pomocí ověřování systému Windows**

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností nepodporuje datové sady SQL Server.

Ke zkopírování dat z/do databáze systému SQL Server, nastavte vlastnost typu datové sady, která **SqlServerTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu datové sady musí být nastavena na: **SqlServerTable** | Ano |
| tableName |Název tabulky nebo zobrazení instance databáze serveru SQL Server, kterou propojená služba odkazuje. | Ano |

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností, které podporuje SQL Server zdroj a jímka.

### <a name="sql-server-as-source"></a>SQL Server jako zdroj

Ke zkopírování dat z SQL serveru, nastavte typ zdroje v aktivitě kopírování do **SqlSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ zdroje kopie aktivity musí být nastavena na: **SqlSource** | Ano |
| sqlReaderQuery |Čtení dat pomocí vlastního dotazu SQL. Příklad: `select * from MyTable`. |Ne |
| sqlReaderStoredProcedureName |Název uložené procedury, který čte data ze zdrojové tabulky. Poslední příkaz jazyka SQL musí být příkaz SELECT v uložené proceduře. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou: páry název/hodnota. Názvy a malá a velká písmena parametry musí odpovídat názvům a malá a velká písmena parametry uložené procedury. |Ne |

**Všimněte si body:**

- Pokud **sqlReaderQuery** je zadán pro SqlSource, aktivitě kopírování spouští tento dotaz zdroje systému SQL Server získat data. Alternativně můžete zadat uložené procedury zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters** (Pokud uložená procedura přebírá parametry).
- Pokud nezadáte "sqlReaderQuery" nebo "sqlReaderStoredProcedureName", sloupce definované v části "struktura" sady dat JSON se používají k vytvoření dotazu (`select column1, column2 from mytable`) ke spouštění na serveru SQL Server. Pokud definice datové sady nemá strukturu"", jsou vybrány všechny sloupce z tabulky.
- Při použití **sqlReaderStoredProcedureName**, stále je třeba zadat fiktivní **tableName** vlastnost v datové sadě JSON.

**Příklad: pomocí jazyka SQL**

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

**Příklad: pomocí uložené procedury**

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

### <a name="sql-server-as-sink"></a>SQL Server jako jímku

Ke zkopírování dat do systému SQL Server, nastavte typ jímky v aktivitě kopírování do **SqlSink**. Následující vlastnosti jsou podporovány v aktivitě kopírování **podřízený** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ jímky kopie aktivity musí nastavena: **SqlSink** | Ano |
| writeBatchSize |Vloží data do tabulky SQL, když velikost vyrovnávací paměti dosáhne writeBatchSize.<br/>Povolené hodnoty jsou: celé číslo (počet řádků). |Ne (výchozí: 10000) |
| writeBatchTimeout |Počkejte, než čas na dokončení předtím, než vyprší časový limit operace dávkové vložení.<br/>Povolené hodnoty jsou: časový interval. Příklad: "00: 30:00" (30 minut). |Ne |
| preCopyScript |Zadejte dotaz SQL pro aktivitu kopírování ke spuštění před zápis dat do systému SQL Server. Pouze vyvolání se jednou za kopie spustit. Tato vlastnost slouží k vyčištění předem načtená data. |Ne |
| sqlWriterStoredProcedureName |Název uložené procedury, která definuje, jak použít zdroj dat do cílové tabulky, například na proveďte upserts nebo transformace pomocí vlastní obchodní logiku. <br/><br/>Tuto uloženou proceduru bude **vyvolat na jednu dávku**. Pokud budete chtít provést operaci, která pouze se spustí jednou a nijak nesouvisí se zdrojovými daty, například odstranit nebo zkrátit proveďte pomocí `preCopyScript` vlastnost. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou: páry název/hodnota. Názvy a malá a velká písmena parametry musí odpovídat názvům a malá a velká písmena parametry uložené procedury. |Ne |
| sqlWriterTableType |Zadejte název typu tabulky má být použit v uložené proceduře. Aktivita kopírování zpřístupní přesouvání dat v dočasné tabulce s tímto typem tabulky. Uložená procedura kód pak sloučit data kopírovány s existujícími daty. |Ne |

> [!TIP]
> Při kopírování dat do systému SQL Server, připojí aktivitě kopírování dat do tabulky jímky ve výchozím nastavení. Chcete-li provedení UPSERT nebo jiné obchodní logiky, použijte uloženou proceduru v SqlSink. Podrobné informace z [volání uložené procedury pro SQL jímky](#invoking-stored-procedure-for-sql-sink).

**Příklad 1: připojování dat**

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

**Příklad 2: volání uložené procedury během kopírování pro upsert**

Podrobné informace z [volání uložené procedury pro SQL jímky](#invoking-stored-procedure-for-sql-sink).

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

**Cílové tabulky:**

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

Všimněte si, že cílová tabulka obsahuje sloupec identity.

**Definice JSON datové sady zdroje**

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

**Cílový definici JSON datové sady**

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

Všimněte si, že jako zdrojové a cílové tabulky jiné schéma (cíl má sloupec s identitou). V tomto scénáři budete muset zadat **struktura** vlastnost v definici datové sady cíl, který neobsahuje sloupec identity.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Volání uložené procedury jímku SQL

Při kopírování dat do databáze serveru SQL, zadaný uživatel uložené procedury může být nakonfigurované a volána s další parametry.

Uložené procedury lze použít, když předdefinované kopie mechanismy neodesílají účel. Používá se obvykle při upsert (insert a update) nebo další zpracování (slučování sloupců, vyhledávání dalších hodnot, vložení do více tabulek, atd.) je třeba provést před posledním vkládání zdrojová data v cílové tabulce.

Následující příklad ukazuje, jak pomocí uložené procedury provádět upsert do tabulky v databázi systému SQL Server. Za předpokladu, že vstupní data a podřízený "Marketing" tabulka má tři sloupce: ProfileID, stavu a kategorie. Proveďte podle sloupce "ProfileID" upsert a platí jen pro určité kategorie.

**Výstupní datové sady**

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

Definujte části SqlSink v aktivitě kopírování následujícím způsobem.

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

V databázi zadejte uložená procedura se stejným názvem jako SqlWriterStoredProcedureName. Zpracovává vstupní data ze zadaného zdroje a sloučení do výstupní tabulky. Všimněte si, že název parametru uložené procedury by měla být stejná jako "tableName" definované v datové sadě.

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

V databázi zadejte typ tabulky se stejným názvem jako sqlWriterTableType. Všimněte si, že schéma typu tabulky musí být stejná jako schéma vrácený vstupní data.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL，
)
```

Uložené procedury funkce využívá [zavolat parametry](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-sql-server"></a>Datový typ mapování pro SQL server

Při kopírování dat z/do systému SQL Server, se používají následující mapování datových typů SQL Server k Azure Data Factory dočasné datové typy. V tématu [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) a zjistěte, jak aktivity kopírování mapuje zdroje schéma a data typ jímky.

| Typ dat systému SQL Server | Typ průběžných dat objektu pro vytváření dat |
|:--- |:--- |
| bigint |Int64 |
| binární |Byte] |
| Bit |Logická hodnota |
| Char |Řetězec, Char] |
| datum |DateTime |
| Datum a čas |DateTime |
| datetime2 |DateTime |
| Datový typ DateTimeOffset |Datový typ DateTimeOffset |
| Decimal |Decimal |
| Atribut FILESTREAM (varbinary(max)) |Byte] |
| Float |Double |
| image |Byte] |
| celá čísla |Int32 |
| peníze |Decimal |
| nchar |Řetězec, Char] |
| ntext |Řetězec, Char] |
| číselné |Decimal |
| nvarchar |Řetězec, Char] |
| skutečné |Jednoduchá |
| ROWVERSION |Byte] |
| smalldatetime |DateTime |
| smallint |Int16 |
| Smallmoney |Decimal |
| SQL_VARIANT |Objekt * |
| text |Řetězec, Char] |
| time |Časový interval |
| časové razítko |Byte] |
| tinyint |Int16 |
| Typ UniqueIdentifier |Guid |
| varbinary |Byte] |
| varchar |Řetězec, Char] |
| xml |XML |

## <a name="troubleshooting-connection-issues"></a>Odstraňování potíží s připojením

1. Konfigurace systému SQL Server tak, aby přijímal vzdálená připojení. Spusťte **SQL Server Management Studio**, klikněte pravým tlačítkem na **server**a klikněte na tlačítko **vlastnosti**. Vyberte **připojení** ze seznamu a zkontrolujte **povolit vzdálená připojení k serveru**.

    ![Povolit vzdálená připojení](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    V tématu [konfigurovat možnosti konfigurace serveru pro vzdálený přístup](https://msdn.microsoft.com/library/ms191464.aspx) podrobné pokyny.

2. Spusťte **Správce konfigurace systému SQL Server**. Rozbalte položku **konfigurace sítě serveru SQL Server** pro instanci a vyberte **protokoly pro MSSQLSERVER**. Měli byste vidět protokolů v pravém podokně. Povolte protokol TCP/IP kliknutím pravým tlačítkem na **TCP/IP** a kliknutím na **povolit**.

    ![Povolte protokol TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    V tématu [povolit nebo zakázat síťový protokol serveru](https://msdn.microsoft.com/library/ms191294.aspx) podrobnosti a alternativní způsoby povolení protokolu TCP/IP.

3. V rámci stejného časového období, klikněte dvakrát na **TCP/IP** spustíte **vlastností protokolu TCP/IP** okno.
4. Přepnout **IP adresy** kartě. Posuňte se dolů a najdete **IPAll** části. Poznamenejte si ** TCP Port ** (výchozí hodnota je **1433**).
5. Vytvoření **pravidlo pro bránu Windows Firewall** na počítači, aby povolit příchozí přenosy pomocí tohoto portu.  
6. **Ověření připojení**: pro připojení k serveru SQL pomocí plně kvalifikovaný název, použijte SQL Server Management Studio z jiný počítač. Například: `"<machine>.<domain>.corp.<company>.com,1433"`.


## <a name="next-steps"></a>Další postup
Seznam úložišť dat jako zdroje a jímky nepodporuje aktivitu kopírování v Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
