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
ms.openlocfilehash: 3e1978c761c365125ac94a1ecbef5f9ac7375eba
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67338605"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Kopírování dat do a z Azure SQL Database Managed Instance pomocí služby Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory ke zkopírování dat do a z Azure SQL Database Managed Instance. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z Azure SQL Database Managed Instance do jakékoli podporovaného úložiště dat jímky. Můžete také můžete kopírovat data ze všech podporovaných zdrojů úložišť dat do spravované instance. Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor Azure SQL Database Managed Instance podporuje:

- Kopírování dat pomocí ověřování SQL.
- Jako zdroj načítání dat pomocí jazyka SQL nebo uloženou proceduru.
- Jako jímka, připojení dat do cílové tabulky nebo volání uložené procedury s vlastní logikou během kopírování.

>[!NOTE]
>Azure SQL Database Managed Instance [s funkcí Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current) není nyní podporován tímto konektorem. Chcete-li vyřešit, můžete použít [obecný konektor ODBC](connector-odbc.md) a ovladač ODBC systému SQL Server prostřednictvím místního prostředí integration runtime. Postupujte podle [návod](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current) konfigurací ODBC ovladač stahování a připojovací řetězec.

>[!NOTE]
>Ověření identity objektu zabezpečení a spravované služby nejsou v současnosti podporované tímto konektorem. Obejít, vyberte konektor služby Azure SQL Database a ručně zadejte adresu serveru spravované instance.

## <a name="prerequisites"></a>Požadavky

Pro přístup k Azure SQL Database Managed Instance [veřejný koncový bod](../sql-database/sql-database-managed-instance-public-endpoint-securely.md), můžete použít prostředí Azure integration runtime spravované služby Azure Data Factory. Ujistěte se, abyste povolili veřejný koncový bod a také umožňují provoz veřejný koncový bod na skupiny zabezpečení sítě tak, aby Azure Data Factory může připojit k vaší databázi. Další informace najdete v tématu [návod](../sql-database/sql-database-managed-instance-public-endpoint-configure.md).

Pokud chcete získat přístup k Azure SQL Database Managed Instance privátního koncového bodu, nastavte [modulu runtime integrace v místním prostředí](create-self-hosted-integration-runtime.md) , která má přístup do databáze. Pokud zřídíte místní prostředí integration runtime ve stejné virtuální síti jako spravovanou instanci, ujistěte se, že váš počítač modulu runtime integrace je v jiné podsíti, než spravovaná instance. Pokud zřizujete vaše místní prostředí integration runtime v jiné virtuální sítě než spravovaná instance, můžete použít partnerského vztahu virtuálních sítí nebo virtuální sítě pro připojení k virtuální síti. Další informace najdete v tématu [vaši aplikaci do Azure SQL Database Managed Instance připojit](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Azure Data Factory konkrétní konektor Azure SQL Database Managed Instance.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro službu Azure SQL Database Managed Instance propojené se podporují následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na **SqlServer**. | Ano |
| connectionString |Tato vlastnost určuje, **connectionString** informace, které je potřeba k připojení do spravované instance pomocí ověřování SQL. Další informace najdete v tématu v následujících příkladech. <br/>Označte toto pole jako **SecureString** bezpečně uložit ve službě Azure Data Factory. Můžete také vložit hesla ve službě Azure Key Vault. Pokud je ověřování SQL, o přijetí změn `password` konfigurace z připojovacího řetězce. Další informace, podívejte se na příklad JSON pod tabulkou a [Store přihlašovacích údajů ve službě Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| connectVia | To [prostředí integration runtime](concepts-integration-runtime.md) se používá pro připojení k úložišti. Pokud vaše spravovaná instance má veřejný koncový bod a umožňuje Azure Data Factory pro přístup k ní můžete použít místní prostředí integration runtime nebo prostředí Azure integration runtime. Pokud není zadán, je použít výchozí prostředí Azure integration runtime. |Ano |

**Příklad 1: Použít ověřování SQL**

Výchozí port je 1433. Pokud používáte Azure SQL Database Managed Instance pomocí veřejného koncového bodu, explicitně zadejte port 3342.

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
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

Výchozí port je 1433. Pokud používáte Azure SQL Database Managed Instance pomocí veřejného koncového bodu, explicitně zadejte port 3342.

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;"
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
| type | Vlastnost type datové sady, musí být nastavená na **SqlServerTable**. | Ano |
| tableName |Tato vlastnost je název tabulky nebo zobrazení v instanci databáze na propojenou službu. | Ne pro zdroj, Ano pro jímku |

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
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na **SqlSource**. | Ano |
| sqlReaderQuery |Tuto vlastnost používá vlastní dotaz SQL číst data. Příklad: `select * from MyTable`. |Ne |
| sqlReaderStoredProcedureName |Tato vlastnost je název uložené procedury, která čte data ze zdrojové tabulky. Příkaz SELECT v uložené proceduře musí být poslední příkaz jazyka SQL. |Ne |
| storedProcedureParameters |Tyto parametry jsou pro uloženou proceduru.<br/>Povolené hodnoty jsou páry název nebo hodnota. Názvy a použití malých a velkých parametry musí odpovídat názvům a použití malých a velkých parametrů uložené procedury. |Ne |

**Je třeba počítat s následujícím:**

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
> Další informace o podporovaných zápisu chování, konfigurace a osvědčené postupy z [osvědčený postup pro načítání dat do služby Azure SQL Database Managed Instance](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Ke zkopírování dat do Azure SQL Database Managed Instance, nastavte typ jímky v aktivitě kopírování do **SqlSink**. V části jímky aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Nastavte vlastnost typ jímky aktivity kopírování **SqlSink**. | Ano |
| writeBatchSize |Počet řádků k vložení do tabulky SQL *dávce*.<br/>Povolené hodnoty jsou celá čísla pro počet řádků. Ve výchozím nastavení Azure Data Factory dynamicky určuje velikost dávky odpovídající na základě velikosti řádku.  |Ne |
| writeBatchTimeout |Tato vlastnost určuje doba čekání na dokončení před vypršením časového limitu operace insert služby batch.<br/>Povolené jsou hodnoty pro časový interval. Příkladem je "00: 30:00," což je 30 minut. |Ne |
| preCopyScript |Tato vlastnost určuje dotaz SQL pro aktivitu kopírování ke spuštění před zápisem dat do spravované instance. Je vyvolána pouze jednou za kopírování spustit. Tuto vlastnost můžete použít k vyčištění dat předem. |Ne |
| sqlWriterStoredProcedureName |Tento název je pro uloženou proceduru, která definuje, jak použít zdroj dat do cílové tabulky. <br/>Tuto uloženou proceduru se *za batch*. Chcete-li provést operaci, která se spustí pouze jednou a nemá nic se zdrojovými daty, například delete nebo truncate, použijte `preCopyScript` vlastnost. |Ne |
| storedProcedureParameters |Tyto parametry slouží pro uloženou proceduru.<br/>Povolené hodnoty jsou páry název nebo hodnota. Názvy a použití malých a velkých parametry musí odpovídat názvům a použití malých a velkých parametrů uložené procedury. |Ne |
| sqlWriterTableType |Tato vlastnost určuje název typu tabulky má být použit v uložené proceduře. Aktivita kopírování zpřístupňuje data přesouvá do dočasné tabulky s tímto typem tabulky. Uložená procedura kód pak sloučit data, která se kopíruje s existujícími daty. |Ne |

**Příklad 1: Připojení dat**

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

**Příklad 2: Vyvolání uložené procedury během kopírování**

Přečtěte si další podrobnosti o [vyvolat uloženou proceduru z SQL jímky](#invoke-a-stored-procedure-from-a-sql-sink).

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

Při kopírování dat do Azure SQL Database Managed Instance můžete potřebovat chování různých zápisu:

- [Připojit](#append-data): Zdroj data obsahují pouze nové záznamy.
- [Upsert](#upsert-data): Moje zdrojová data mají vkládání a aktualizace.
- [Přepsat](#overwrite-the-entire-table): Chci se pokaždé, když znovu načtěte celý dimenze.
- [Zápis o vlastní logiku](#write-data-with-custom-logic): Potřebuji další zpracování před posledním vkládání do cílové tabulky. 

Naleznete v příslušných částech konfigurace v Azure Data Factory a osvědčené postupy.

### <a name="append-data"></a>Připojení dat

Připojení dat je výchozí chování tohoto konektoru jímky Azure SQL Database Managed Instance. Azure Data Factory nepodporuje příkaz bulk insert efektivně zapisovat do tabulky. Můžete nastavit zdroj a odpovídajícím způsobem jímky v aktivitě kopírování.

### <a name="upsert-data"></a>Upsert dat

**Možnost 1:** Pokud máte velké množství dat. Chcete-li zkopírovat, provést funkcí upsert, použijte následující postup: 

- Nejprve [dočasnou tabulku](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) k hromadné načtení všech záznamů pomocí aktivity kopírování. Protože operace u dočasných tabulek se neprotokolují, můžete načíst milióny záznamů v řádu sekund.
- Spustit uloženou proceduru aktivitu ve službě Azure Data Factory k použití [sloučit](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) nebo příkazu INSERT nebo UPDATE. Použijte dočasné tabulky jako zdroje k provedení všech aktualizaci nebo vložení jako jedna transakce. Tímto způsobem je snížen počet zpátečních cest a operací protokolu. Na konci aktivita uložená procedura můžete až bude připravená na další cyklus upsert oříznutí dočasnou tabulku.

Jako příklad, ve službě Azure Data Factory vytvoříte kanál s **aktivita kopírování** zřetězené s **aktivity uložené procedury**. Nejprve kopíruje data ze zdrojového úložiště do dočasné tabulky, například **##UpsertTempTable**, jako název tabulky v datové sadě. Ten pak volá uloženou proceduru sloučit zdroj dat z dočasné tabulky do cílové tabulky a vyčistit dočasné tabulky.

![Upsertovat](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

V databázi definujte uloženou proceduru s logikou SLOUČENÍ, stejně jako v následujícím příkladu, který ukazuje z předchozí aktivity uložené procedury. Předpokládejme, že cíl je **marketingové** tabulky se třemi sloupci: **ID profilu**, **stavu**, a **kategorie**. Proveďte upsert na základě **ProfileID** sloupce.

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

**Možnost 2:** Je také možné [vyvolat uloženou proceduru v aktivitě kopírování](#invoke-a-stored-procedure-from-a-sql-sink). Tento postup spouští každý řádek ve zdrojové tabulce namísto použití hromadné vložení jako výchozí přístup v aktivitě kopírování, která není vhodné pro velké objemy upsert.

### <a name="overwrite-the-entire-table"></a>Přepsat celou tabulku

Můžete nakonfigurovat **preCopyScript** vlastnost jímky aktivity kopírování. V takovém případě pro každou aktivitu kopírování, na kterém běží, Azure Data Factory spustí skript nejprve. Pak spustí kopírování vložte data. Například pomocí nejnovějších dat přepsat celou tabulku, zadejte skript nejprve odstranit všechny záznamy a teprve potom hromadné načtení nových dat ze zdroje.

### <a name="write-data-with-custom-logic"></a>Zápis dat o vlastní logiku

Postup zápisu dat pomocí vlastní logiky je podobný jako postup popsaný v [Upsert data](#upsert-data) oddílu. Když budete chtít použít zvláštní zpracování před posledním vložení zdrojová data do cílové tabulky pro široké možnosti škálování a máte jeden ze dvou kroků: 

- Načíst do dočasné tabulky a pak vyvolejte uloženou proceduru.
- Vyvolání uložené procedury během kopírování.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Vyvolání uložené procedury z SQL jímky

Při kopírování dat do Azure SQL Database Managed Instance, také můžete nakonfigurovat a volat uloženou proceduru s další parametry zadané uživatelem.

> [!TIP]
> Volání uložené procedury zpracovává data řádek po řádku namísto pomocí hromadné operace, které nedoporučujeme pro kopírování ve velkém měřítku. Další informace z [osvědčený postup pro načítání dat do služby Azure SQL Database Managed Instance](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Uloženou proceduru můžete použít, když integrovaná funkce kopírování mechanismy neslouží účel. Příkladem je, když chcete použít zvláštní zpracování před posledním vložení zdrojová data do cílové tabulky. Některé příklady vysokého jsou, pokud chcete sloučit sloupce, vyhledat další hodnoty a vložit data do více než jednou tabulkou.

Následující příklad ukazuje způsob použití uloženou proceduru provedete upsert do tabulky v databázi serveru SQL Server. Předpokládejme, že vstupní data a jímku **marketingové** tabulka jednotlivých obsahovat tři sloupce: **ID profilu**, **stavu**, a **kategorie**. Proveďte upsert na základě **ProfileID** sloupce a použijte je jenom pro konkrétní kategorie.

**Výstupní datovou sadu:** "tableName" je stejný název parametru typu tabulky v uložené proceduře, jak je znázorněno v následujícím skriptu uložené procedury:

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

Definovat **SQL jímky** části v aktivitě kopírování následujícím způsobem:

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

V databázi, definovat uložená procedura se stejným názvem jako **SqlWriterStoredProcedureName**. Zpracovává vstupní data ze zadaného zdroje a sloučí do výstupní tabulky. Název parametru typu tabulky v uložené proceduře je stejný jako **tableName** definované v datové sadě.

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

V databázi, definujte typ tabulky se stejným názvem jako **sqlWriterTableType**. Schéma typu tabulky je stejný jako schéma vrácené funkcí vstupní data.

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
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> Datové typy, které mapují na typ desetinné dočasné aktuálně Azure Data Factory podporuje přesnost až 28. Pokud máte data, která vyžaduje přesností větší než 28, vezměte v úvahu převodu na řetězec v příkazu jazyka SQL.

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
