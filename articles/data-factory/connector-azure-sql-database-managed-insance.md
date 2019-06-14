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
ms.date: 06/13/2019
ms.author: jingwang
ms.openlocfilehash: e68b522d5a0fe7c359d83fc436aa7a1fd2159198
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67048590"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Kopírování dat do a z Azure SQL Database Managed Instance pomocí služby Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory ke zkopírování dat do a z Azure SQL Database Managed Instance. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z Azure SQL Database Managed Instance do jakékoli podporovaného úložiště dat jímky. Můžete také můžete kopírovat data ze všech podporovaných zdrojů úložišť dat do spravované instance. Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor Azure SQL Database Managed Instance podporuje:

- Kopírování dat pomocí ověřování SQL.
- Jako zdroj načítání dat pomocí jazyka SQL nebo uloženou proceduru.
- Jako jímka, připojení dat do cílové tabulky nebo volání uložené procedury s vlastní logikou během kopírování.

SQL Server [s funkcí Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) není nyní podporován. 

## <a name="prerequisites"></a>Požadavky

Pro přístup k Azure SQL Database Managed Instance  **[veřejný koncový bod](../sql-database/sql-database-managed-instance-public-endpoint-securely.md)** , můžete použít Azure IR ADF spravované Ujistěte se, že nejen povolit koncový bod veřejné, ale také umožňují provoz veřejného koncového bodu skupiny zabezpečení sítě, chcete-li se připojit k databázi, pomocí následujících ADF [návod](../sql-database/sql-database-managed-instance-public-endpoint-configure.md).

Pro přístup k Azure SQL Database Managed Instance **privátního koncového bodu**, nastavení [modulu runtime integrace v místním prostředí](create-self-hosted-integration-runtime.md) , která má přístup do databáze. Pokud zřídíte místní prostředí integration runtime ve stejné virtuální síti jako spravovanou instanci, ujistěte se, že váš počítač modulu runtime integrace je v jiné podsíti, než spravovaná instance. Pokud zřizujete vaše místní prostředí integration runtime v jiné virtuální sítě než spravovaná instance, můžete použít buď partnerský vztah virtuální sítě nebo virtuální sítě pro připojení k virtuální síti. Další informace najdete v tématu [vaši aplikaci do Azure SQL Database Managed Instance připojit](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory, které jsou specifické pro konektor Azure SQL Database Managed Instance.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro službu Azure SQL Database Managed Instance propojené se podporují následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na **SqlServer**. | Ano. |
| connectionString |Tato vlastnost určuje informace připojovací řetězec, který je nezbytný pro připojení do spravované instance pomocí ověřování SQL. Další informace najdete v tématu v následujících příkladech. <br/>Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory. Můžete také umístit hesla ve službě Azure Key Vault, a pokud se jedná o přijetí změn ověřování SQL `password` konfigurace z připojovacího řetězce. Podívejte se na příklad JSON pod tabulkou a [Store přihlašovacích údajů ve službě Azure Key Vault](store-credentials-in-key-vault.md) článku s dalšími podrobnostmi. |Ano. |
| connectVia | To [prostředí integration runtime](concepts-integration-runtime.md) se používá pro připojení k úložišti. (Pokud spravované instance má veřejný koncový bod a umožňují pro přístup k ADF), můžete použít modul Integration Runtime nebo prostředí Azure Integration Runtime. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ano. |

**Příklad 1: Použít ověřování SQL**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername:port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad 2: Použít ověřování SQL pomocí hesla ve službě Azure Key Vault**

```json
{
    "name": "AzureSqlMILinkedService",
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

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro použití k definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností, které podporuje datová sada Azure SQL Database Managed Instance.

Pro kopírování dat do a z Azure SQL Database Managed Instance, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na **SqlServerTable**. | Ano. |
| tableName |Tato vlastnost je název tabulky nebo zobrazení v instanci databáze na propojenou službu. | Ne pro zdroj. Ano pro jímku. |

**Příklad**

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
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
        "name": "CopyFromAzureSqlMI",
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
        "name": "CopyFromAzureSqlMI",
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

> [!TIP]
> Další informace o podporovaných zápisu chování, konfigurace a osvědčených postupů z [osvědčený postup pro načítání dat do služby Azure SQL Database Managed Instance](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Ke zkopírování dat do Azure SQL Database Managed Instance, nastavte typ jímky v aktivitě kopírování do **SqlSink**. V části jímky aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Nastavte vlastnost typ jímky aktivity kopírování **SqlSink**. | Ano. |
| writeBatchSize |Počet řádků, která se vloží do tabulky SQL **dávce**.<br/>Povolené hodnoty jsou celá čísla pro počet řádků. Ve výchozím nastavení služby Data Factory dynamicky určí příslušné batch velikost podle velikosti řádku.  |Ne |
| writeBatchTimeout |Tato vlastnost určuje doba čekání na dokončení před vypršením časového limitu operace insert služby batch.<br/>Povolené jsou hodnoty pro časové období. Příkladem je "00: 30:00," což je 30 minut. |Ne. |
| preCopyScript |Tato vlastnost určuje dotaz SQL pro aktivitu kopírování ke spuštění před zápisu dat do spravované instance. Je vyvolána pouze jednou za kopírování spustit. Tuto vlastnost můžete použít k vyčištění dat předem. |Ne. |
| sqlWriterStoredProcedureName |Tento název je pro uloženou proceduru, která definuje, jak použít zdroj dat do cílové tabulky. <br/>Tuto uloženou proceduru se *za batch*. Chcete-li provést operaci, která se spustí pouze jednou a nemá nic se zdrojovými daty, například delete nebo truncate, použijte `preCopyScript` vlastnost. |Ne. |
| storedProcedureParameters |Tyto parametry slouží pro uloženou proceduru.<br/>Povolené hodnoty jsou páry název nebo hodnota. Názvy a použití malých a velkých parametry musí odpovídat názvům a použití malých a velkých parametrů uložené procedury. |Ne. |
| sqlWriterTableType |Tato vlastnost určuje název typu tabulky má být použit v uložené proceduře. Aktivita kopírování zpřístupňuje data přesouvá do dočasné tabulky s tímto typem tabulky. Uložená procedura kód pak sloučit data, která se kopíruje s existujícími daty. |Ne. |

**Příklad 1: připojení dat**

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
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

**Příklad 2: volání uložené procedury během kopírování**

Přečtěte si další podrobnosti o [vyvolat uloženou proceduru z SQL jímky](#invoking-stored-procedure-for-sql-sink).

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database-managed-instance"></a>Osvědčeným postupem pro načítání dat do služby Azure SQL Database Managed Instance

Při kopírování dat do Azure SQL Database Managed Instance, můžete vyžadovat chování různých zápisu:

- **[Připojit](#append-data)** : zdroj data obsahují pouze nové záznamy;
- **[Upsert](#upsert-data)** : Moje zdrojová data mají vkládání a aktualizace.
- **[Přepsat](#overwrite-entire-table)** : Chcete znovu načíst tabulku celé dimenze pokaždé, když;
- **[Zápis o vlastní logiku](#write-data-with-custom-logic)** : Potřebuji další zpracování před posledním vkládání do cílové tabulky.

Odkazovat uvedeném pořadí oddíly o tom, jak nakonfigurovat v ADF a osvědčené postupy.

### <a name="append-data"></a>Připojení dat

Toto je výchozí chování tohoto konektoru jímky Azure SQL Database Managed Instance a dělat ADF **hromadné vložení** efektivně zapisovat do tabulky. Můžete jednoduše nakonfigurovat zdroj a odpovídajícím způsobem jímky v aktivitě kopírování.

### <a name="upsert-data"></a>Upsert dat

**Možnost mám** (navrhované zejména v případě, že máte ke kopírování velkých objemů dat): **přístup většina výkonné** provedete upsert je následující: 

- Za prvé, využívat [dočasnou tabulku](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) hromadné načtení všech záznamů pomocí aktivit kopírování. Jak operace u dočasných tabulek se neprotokolují, můžete načíst milióny záznamů v řádu sekund.
- Aktivita uložená procedura spuštění ve službě ADF použít [sloučit](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) (nebo INSERT/UPDATE) prohlášení a dočasné tabulky jako zdroje k provedení všech aktualizaci nebo vložení jako jedna transakce, snižuje množství výměn dat a protokolovat operace. Na konci aktivity uložené procedury lze až bude připravená na další cyklus upsert oříznutí dočasné tabulky. 

Jako příklad, ve službě Azure Data Factory vytvoříte kanál s **aktivita kopírování** zřetězené s **aktivity uložené procedury** v případě úspěchu. Nejprve kopíruje data ze zdrojového úložiště do dočasné tabulky, třeba " **##UpsertTempTable**" jako název tabulky v datové sadě, pak ten volá uloženou proceduru sloučit zdroj dat z dočasné tabulky do cílové tabulky a vyčištění dočasné tabulky.

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

## <a name="invoking-stored-procedure-for-sql-sink"></a> Vyvolání uložené procedury z SQL jímky

Při kopírování dat do Azure SQL Database Managed Instance, můžete také nakonfigurovat a vyvolat uloženou proceduru s další parametry zadané uživatelem.

> [!TIP]
> Volání uložené procedury zpracovává na data řádek po řádku namísto hromadné operace, která není určeno pro kopírování velkého rozsahu. Další informace z [osvědčený postup pro načítání dat do služby Azure SQL Database Managed Instance](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Uloženou proceduru můžete použít, když integrovaná funkce kopírování mechanismy neslouží účel, například použít zvláštní zpracování před posledním vložení zdrojová data do cílové tabulky. Některé příklady vysokého jsou sloučení sloupců, vyhledat další hodnoty a vložení do více než jednou tabulkou.

Následující příklad ukazuje způsob použití uloženou proceduru provedete upsert do tabulky v databázi serveru SQL Server. Předpokládejme, který vstupní data a jímku **marketingové** tabulka jednotlivých obsahovat tři sloupce: **ID profilu**, **stavu**, a **kategorie**. Proveďte upsert na základě **ProfileID** sloupce a použijte je jenom pro konkrétní kategorie.

**Výstupní datová sada:** "tableName" by měl být stejný název parametru typu tabulky v uložené proceduře (viz níže uvedený skript uložené procedury).

```json
{
    "name": "AzureSqlMIDataset",
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

V databázi Definujte typ tabulky se stejným názvem jako sqlWriterTableType. Schéma typu tabulky je stejný jako schéma vrácené funkcí vstupní data.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL
)
```

Uložená procedura funkce využívá možnosti [parametrů table-valued](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Mapování datového typu pro Azure SQL Database Managed Instance

Po zkopírování dat do a z Azure SQL Database Managed Instance se používají následující mapování z Azure SQL Database Managed Instance datových typů na Azure Data Factory dočasné datové typy. Informace o tom, jak aktivity kopírování mapuje zdrojový typ schéma a data jímky, najdete v článku [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md).

| Azure SQL Database Managed Instance datového typu | Azure Data Factory dočasné datový typ |
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
> Datové typy, které mapují na typ desetinné dočasné aktuálně Azure Data Factory podporuje přesnost až 28. Pokud máte data, která vyžaduje přesností větší než 28, vezměte v úvahu převodu na řetězec v příkazu jazyka SQL.

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
