---
title: Kopírování dat do a z SQL Server
description: Přečtěte si, jak přesunout data do a z SQL Server do místní databáze nebo na virtuálním počítači Azure pomocí Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/18/2020
ms.openlocfilehash: 5d35d0434f65f28b58a6d81172ade12aceb02987
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384992"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Kopírování dat do a z SQL Server pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Azure Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-sqlserver-connector.md)
> * [Aktuální verze](connector-sql-server.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z a do databáze SQL Server. Vytvoří se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor SQL Server se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)

Data z databáze SQL Server můžete kopírovat do libovolného podporovaného úložiště dat jímky. Případně můžete kopírovat data z libovolného podporovaného zdrojového úložiště dat do databáze SQL Server. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje nebo jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Konkrétně tento konektor SQL Server podporuje:

- SQL Server verze 2005 a vyšší.
- Kopírování dat pomocí ověřování SQL nebo Windows.
- Jako zdroj načítání dat pomocí dotazu SQL nebo uložené procedury. Můžete také zvolit paralelní kopírování ze zdroje SQL Server, podrobnosti najdete v části [paralelní kopírování z databáze SQL](#parallel-copy-from-sql-database) .
- Pokud v závislosti na zdrojovém schématu neexistuje, vytvoří se jako jímka automaticky vytváření cílové tabulky. připojení dat k tabulce nebo vyvolání uložené procedury s vlastní logikou během kopírování. 

[SQL Server Express LocalDB](/sql/database-engine/configure-windows/sql-server-express-localdb) se nepodporuje.

>[!NOTE]
>Tento konektor teď nepodporuje SQL Server [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) . Pokud chcete tento problém obejít, můžete použít [obecný konektor ODBC](connector-odbc.md) a ovladač SQL Server ODBC. Postupujte [podle pokynů ke](/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver) stažení ovladače ODBC a konfigurací připojovacích řetězců.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které se používají k definování Data Factory entit specifických pro konektor databáze SQL Server.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu SQL Server jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na **SQLServer**. | Yes |
| připojovací řetězec |Zadejte informace **připojovacího řetězce** potřebné pro připojení k databázi SQL Server pomocí ověřování SQL nebo ověřování systému Windows. Přečtěte si následující ukázky.<br/>Heslo můžete také přidat do Azure Key Vault. Pokud se jedná o ověřování SQL, vyžádejte si z `password` připojovacího řetězce konfiguraci. Další informace najdete v příkladech JSON, které následují po tabulce, a [ukládají přihlašovací údaje v Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| userName |Pokud používáte ověřování systému Windows, zadejte uživatelské jméno. Příkladem je **Doména \\ \ uživatelské jméno**. |No |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Označte toto pole jako **SecureString** a bezpečně ho uložte do Azure Data Factory. Nebo můžete [odkazovat na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). |No |
| connectVia | Tento [modul runtime integrace](concepts-integration-runtime.md) se používá pro připojení k úložišti dat. Další informace najdete v části [požadavky](#prerequisites) . Pokud tento parametr nezadáte, použije se výchozí prostředí Azure Integration runtime. |No |

>[!TIP]
>Pokud dojde k chybě s kódem chyby "UserErrorFailedToConnectToSqlServer" a zprávou, jako je "omezení relace pro databázi je XXX a bylo dosaženo," přidejte `Pooling=false` do připojovacího řetězce a zkuste to znovu.

**Příklad 1: použití ověřování SQL**

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

**Příklad 2: použití ověřování SQL s heslem v Azure Key Vault**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad 3: použití ověřování systému Windows**

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných datovou sadou SQL Server.

Chcete-li kopírovat data z a do databáze SQL Server, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type datové sady musí být nastavená na **SQLServer**. | Yes |
| schema | Název schématu. |Ne pro zdroj, Ano pro jímku  |
| tabulka | Název tabulky/zobrazení |Ne pro zdroj, Ano pro jímku  |
| tableName | Název tabulky nebo zobrazení se schématem. Tato vlastnost je podporována z důvodu zpětné kompatibility. Pro nové úlohy použijte `schema` a `table` . | Ne pro zdroj, Ano pro jímku |

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

>[!TIP]
>Pokud chcete data z SQL Server efektivně načíst pomocí dělení dat, přečtěte si další informace z [paralelní kopie z databáze SQL](#parallel-copy-from-sql-database).

Chcete-li kopírovat data z SQL Server, nastavte typ zdroje v aktivitě kopírování na **SqlSource**. V části zdroj aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **SqlSource**. | Yes |
| sqlReaderQuery |Pro čtení dat použijte vlastní dotaz SQL. Příklad: `select * from MyTable`. |No |
| sqlReaderStoredProcedureName |Tato vlastnost je název uložené procedury, která čte data ze zdrojové tabulky. Poslední příkaz SQL musí být příkaz SELECT v uložené proceduře. |No |
| storedProcedureParameters |Tyto parametry jsou pro uloženou proceduru.<br/>Povolené hodnoty jsou páry název-hodnota. Názvy a velikost písmen parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. |No |
| isolationLevel | Určuje chování při zamykání transakcí pro zdroj SQL. Povolené hodnoty jsou: **ReadCommitted**, **READUNCOMMITTED**, **RepeatableRead**, **serializovatelný**, **Snapshot**. Pokud není zadaný, použije se výchozí úroveň izolace databáze. Další podrobnosti najdete v [tomto dokumentu](/dotnet/api/system.data.isolationlevel) . | No |
| partitionOptions | Určuje možnosti dělení dat, které se používají k načtení dat z SQL Server. <br>Povolené hodnoty jsou: **none** (default), **PhysicalPartitionsOfTable** a **DynamicRange**.<br>Pokud je možnost oddílu povolena (tj. ne `None` ), stupeň paralelismu na souběžně načtená data z SQL Server řídí [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) Nastavení aktivity kopírování. | No |
| partitionSettings | Určete skupinu nastavení pro dělení dat. <br>Použijte, pokud možnost partition není `None` . | No |
| ***V části `partitionSettings` :*** | | |
| partitionColumnName | Zadejte název zdrojového sloupce **v typu Integer nebo Date/DateTime** ( `int` , `smallint` , `bigint` , `date` , `smalldatetime` , `datetime` , `datetime2` nebo `datetimeoffset` ), který bude použit pro vytváření oddílů rozsahu pro paralelní kopírování. Pokud není zadaný, index nebo primární klíč tabulky se automaticky zjistí a použije se jako sloupec partition.<br>Použijte, pokud je parametr partition `DynamicRange` . Použijete-li dotaz k načtení zdrojových dat, zapojte  `?AdfDynamicRangePartitionCondition ` v klauzuli WHERE. Příklad naleznete v části [paralelní kopírování z databáze SQL](#parallel-copy-from-sql-database) . | No |
| partitionUpperBound | Maximální hodnota sloupce oddílu pro rozdělení rozsahu oddílu Tato hodnota se používá k určení rozteči oddílu, nikoli pro filtrování řádků v tabulce. Všechny řádky v tabulce nebo výsledku dotazu budou rozděleny na oddíly a zkopírovány. Pokud není zadaný, aktivita kopírování automaticky detekuje hodnotu.  <br>Použijte, pokud je parametr partition `DynamicRange` . Příklad naleznete v části [paralelní kopírování z databáze SQL](#parallel-copy-from-sql-database) . | No |
| partitionLowerBound | Minimální hodnota sloupce oddílu pro rozdělení rozsahu oddílů. Tato hodnota se používá k určení rozteči oddílu, nikoli pro filtrování řádků v tabulce. Všechny řádky v tabulce nebo výsledku dotazu budou rozděleny na oddíly a zkopírovány. Pokud není zadaný, aktivita kopírování automaticky detekuje hodnotu.<br>Použijte, pokud je parametr partition `DynamicRange` . Příklad naleznete v části [paralelní kopírování z databáze SQL](#parallel-copy-from-sql-database) . | No |

**Je třeba počítat s následujícím:**

- Pokud je pro **SqlSource** zadaný **sqlReaderQuery** , aktivita kopírování spustí tento dotaz na zdroj SQL Server, aby se data získala. Uloženou proceduru lze také určit zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters** , pokud uložená procedura přijímá parametry.
- Pokud k načtení dat používáte uloženou proceduru ve zdroji, Všimněte si, že pokud je uložená procedura navržena tak, aby vrátila jiné schéma, pokud je předána jiná hodnota parametru, může dojít k chybě nebo se při importu schématu z uživatelského rozhraní nebo při kopírování dat do SQL Database s automatickým vytvořením tabulky zobrazí neočekávaný výsledek.

**Příklad: použití dotazu SQL**

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

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type jímky aktivity kopírování musí být nastavená na **SqlSink**. | Yes |
| preCopyScript |Tato vlastnost určuje dotaz SQL pro aktivitu kopírování, která se má spustit před zápisem dat do SQL Server. Vyvolá se jenom jednou pro každé spuštění kopírování. Tuto vlastnost můžete použít k vyčištění předem načtených dat. |No |
| tableOption | Určuje, jestli se má [automaticky vytvořit tabulka jímky](copy-activity-overview.md#auto-create-sink-tables) , pokud na základě schématu zdroje neexistuje. Vytvoření automatické tabulky není podporováno, je-li jímka určena pro uloženou proceduru. Povolené hodnoty jsou: `none` (výchozí), `autoCreate` . |No |
| sqlWriterStoredProcedureName | Název uložené procedury definující, jak se mají zdrojová data použít v cílové tabulce. <br/>Tato uložená procedura je *vyvolána pro každou dávku*. Pro operace, které se spouštějí jenom jednou a které nemají nic dělat se zdrojovými daty, například odstranit nebo zkrátit, použijte `preCopyScript` vlastnost.<br>Viz příklad [vyvolání uložené procedury z jímky SQL](#invoke-a-stored-procedure-from-a-sql-sink). | No |
| storedProcedureTableTypeParameterName |Název parametru pro typ tabulky určený v uložené proceduře.  |No |
| sqlWriterTableType |Název typu tabulky, který se má použít v uložené proceduře Aktivita kopírování zpřístupňuje data, která jsou k dispozici v dočasné tabulce s tímto typem tabulky. Uložený kód procedury pak může sloučit data, která jsou kopírována se stávajícími daty. |No |
| storedProcedureParameters |Parametry pro uloženou proceduru.<br/>Povolené hodnoty jsou páry název-hodnota. Názvy a malá písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. | No |
| writeBatchSize |Počet řádků, které mají být vloženy do tabulky SQL *na dávku*.<br/>Povolené hodnoty jsou celá čísla pro počet řádků. Ve výchozím nastavení Azure Data Factory dynamicky určí vhodnou velikost dávky na základě velikosti řádku. |No |
| writeBatchTimeout |Tato vlastnost určuje dobu čekání na dokončení operace dávkového vložení před vypršením časového limitu.<br/>Povolené hodnoty jsou pro časové rozpětí. Příkladem je "00:30:00" po dobu 30 minut. Pokud není zadaná žádná hodnota, použije se jako výchozí časový limit "02:00:00". |No |

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
                "tableOption": "autoCreate",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Příklad 2: vyvolání uložené procedury během kopírování**

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

## <a name="parallel-copy-from-sql-database"></a>Paralelní kopírování z databáze SQL

Konektor SQL Server v aktivitě kopírování poskytuje integrované datové oddíly pro kopírování dat paralelně. Možnosti dělení dat můžete najít na kartě **zdroj** aktivity kopírování.

![Snímek obrazovky s možnostmi oddílů](./media/connector-sql-server/connector-sql-partition-options.png)

Povolíte-li kopírování rozdělené na oddíly, aktivita kopírování spustí paralelní dotazy na zdroj SQL Server a načte data podle oddílů. Paralelní míra je řízena [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) nastavením aktivity kopírování. Pokud jste například nastavili `parallelCopies` na čtyři, Data Factory souběžně generuje a spustí čtyři dotazy na základě zadané možnosti oddílu a nastavení a každý dotaz načte část dat z vašeho SQL Server.

Navrhnete, abyste umožnili paralelní kopírování s vytvářením oddílů dat zvlášť při načítání velkého množství dat z SQL Server. Následují Doporučené konfigurace pro různé scénáře. Při kopírování dat do úložiště dat založeného na souborech se doporučuje zapisovat do složky jako více souborů (zadat jenom název složky). v takovém případě je výkon lepší než zápis do jediného souboru.

| Scenario                                                     | Navrhovaná nastavení                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Úplné načtení z velké tabulky s fyzickými oddíly.        | **Možnost oddílu**: fyzické oddíly tabulky. <br><br/>Během provádění Data Factory automaticky detekuje fyzické oddíly a kopíruje data podle oddílů. <br><br/>Pokud chcete zjistit, jestli má tabulka fyzický oddíl, nebo ne, můžete na [Tento dotaz](#sample-query-to-check-physical-partition)odkazovat. |
| Úplné načtení z velké tabulky bez fyzických oddílů při použití celého čísla nebo sloupce data a času pro dělení dat. | **Možnosti oddílu**: dynamický oddíl rozsahu.<br>**Sloupec oddílu** (volitelné): Zadejte sloupec, který se používá k dělení dat. Pokud není zadaný, použije se sloupec index nebo primární klíč.<br/>**Horní hranice oddílu** a **dolní mez oddílu** (volitelné): Určete, jestli chcete určit rozteč oddílu. Nejedná se o filtrování řádků v tabulce, přičemž všechny řádky v tabulce budou rozděleny na oddíly a zkopírovány. Pokud není zadaný, aktivita kopírování automaticky detekuje hodnoty.<br><br>Například pokud má sloupec oddílu "ID" rozsah hodnot od 1 do 100 a nastavíte dolní mez na hodnotu 20 a horní mez jako 80, s paralelní kopií as 4 Data Factory načte data podle 4 identifikátorů oddílů – ID v rozsahu <= 20, [21, 50], [51, 80] a >= 81, v uvedeném pořadí. |
| Načtěte velké množství dat pomocí vlastního dotazu, bez fyzických oddílů, zatímco se sloupcem typu Integer nebo Date/DateTime pro dělení dat. | **Možnosti oddílu**: dynamický oddíl rozsahu.<br>**Dotaz**: `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>` .<br>**Partition – sloupec**: Určete sloupec, který se používá k dělení dat.<br>**Horní hranice oddílu** a **dolní mez oddílu** (volitelné): Určete, jestli chcete určit rozteč oddílu. Nejedná se o filtrování řádků v tabulce, všechny řádky ve výsledku dotazu budou rozděleny na oddíly a zkopírovány. Pokud není zadaný, aktivita kopírování automaticky detekuje hodnotu.<br><br>Během provádění Data Factory nahradí `?AdfRangePartitionColumnName` skutečným názvem sloupce a rozsahem hodnot pro každý oddíl a odešle do SQL Server. <br>Například pokud má sloupec oddílu "ID" rozsah hodnot od 1 do 100 a nastavíte dolní mez na hodnotu 20 a horní mez jako 80, s paralelní kopií as 4 Data Factory načte data podle 4 identifikátorů oddílů – ID v rozsahu <= 20, [21, 50], [51, 80] a >= 81, v uvedeném pořadí. <br><br>Tady je více ukázkových dotazů pro různé scénáře:<br> 1. dotazování celé tabulky: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2. dotazování z tabulky s výběrem sloupce a dalšími filtry klauzule WHERE: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3. dotazování s poddotazy: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4. dotazování pomocí oddílu v poddotazu: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

Osvědčené postupy načítání dat s možností oddílu:

1. Chcete-li se vyhnout zkosení dat, vyberte sloupec s výrazným označením jako sloupec oddílu (například primární klíč nebo jedinečný klíč). 
2. Pokud tabulka obsahuje vestavěný oddíl, použijte možnost oddíl "fyzické oddíly tabulky", abyste získali lepší výkon.    
3. Pokud ke kopírování dat použijete Azure Integration Runtime, můžete nastavit větší "[jednotky integrace dat (diú)](copy-activity-performance-features.md#data-integration-units)" (>4), abyste využili více výpočetních prostředků. Projděte si příslušné scénáře.
4. "[Stupeň kopírování paralelismus](copy-activity-performance-features.md#parallel-copy)" řídí čísla oddílů a nastavení tohoto počtu je příliš velké, neuškodí výkon, doporučuje se nastavit toto číslo jako (diú nebo počet hostitelských uzlů IR) * (2 až 4).

**Příklad: úplné načtení z velké tabulky s fyzickými oddíly**

```json
"source": {
    "type": "SqlSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Příklad: dotaz s dynamickým oddílem rozsahu**

```json
"source": {
    "type": "SqlSource",
    "query": "SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column (optional) to decide the partition stride, not as data filter>",
        "partitionLowerBound": "<lower_value_of_partition_column (optional) to decide the partition stride, not as data filter>"
    }
}
```

### <a name="sample-query-to-check-physical-partition"></a>Ukázkový dotaz pro kontrolu fyzického oddílu

```sql
SELECT DISTINCT s.name AS SchemaName, t.name AS TableName, pf.name AS PartitionFunctionName, c.name AS ColumnName, iif(pf.name is null, 'no', 'yes') AS HasPartition
FROM sys.tables AS t
LEFT JOIN sys.objects AS o ON t.object_id = o.object_id
LEFT JOIN sys.schemas AS s ON o.schema_id = s.schema_id
LEFT JOIN sys.indexes AS i ON t.object_id = i.object_id 
LEFT JOIN sys.index_columns AS ic ON ic.partition_ordinal > 0 AND ic.index_id = i.index_id AND ic.object_id = t.object_id 
LEFT JOIN sys.columns AS c ON c.object_id = ic.object_id AND c.column_id = ic.column_id 
LEFT JOIN sys.partition_schemes ps ON i.data_space_id = ps.data_space_id 
LEFT JOIN sys.partition_functions pf ON pf.function_id = ps.function_id 
WHERE s.name='[your schema]' AND t.name = '[your table name]'
```

Pokud má tabulka fyzický oddíl, zobrazí se "HasPartition" jako "Ano" podobně jako v následujícím.

![Výsledek dotazu SQL](./media/connector-azure-sql-database/sql-query-result.png)

## <a name="best-practice-for-loading-data-into-sql-server"></a>Osvědčené postupy načítání dat do SQL Server

Při kopírování dat do SQL Server může být nutné vyžadovat jiné chování při zápisu:

- [Připojit](#append-data): zdrojová data obsahují pouze nové záznamy.
- [Upsert](#upsert-data): moje zdrojová data obsahují vložení i aktualizace.
- [Přepsat](#overwrite-the-entire-table): Chci pokaždé, když chcete znovu načíst celou tabulku dimenzí.
- [Zápis pomocí vlastní logiky](#write-data-with-custom-logic): Potřebuji dodatečné zpracování před konečným vložením do cílové tabulky.

V příslušných částech najdete informace o tom, jak nakonfigurovat v Azure Data Factory a osvědčených postupech.

### <a name="append-data"></a>Připojit data

Připojení dat je výchozím chováním tohoto konektoru SQL Server jímky. Azure Data Factory hromadné vložení do tabulky efektivně. Zdroj a jímku můžete v aktivitě kopírování nakonfigurovat odpovídajícím způsobem.

### <a name="upsert-data"></a>Upsert dat

**Možnost 1:** Pokud máte ke kopírování velké množství dat, můžete hromadně načíst všechny záznamy do pracovní tabulky pomocí aktivity kopírování a pak spustit aktivitu uložené procedury a použít příkaz [Sloučit](/sql/t-sql/statements/merge-transact-sql) nebo vložit nebo aktualizovat v jednom snímku. 

Aktivita kopírování aktuálně neposkytuje nativní podporu načítání dat do dočasné tabulky databáze. Existuje pokročilý způsob, jak ho nastavit s kombinací více aktivit, najdete v tématu věnovaném [optimalizaci SQL Database hromadně Upsertch scénářů](https://github.com/scoriani/azuresqlbulkupsert). Níže ukazuje ukázku použití trvalé tabulky jako přípravy.

Jako příklad můžete v Azure Data Factory vytvořit kanál s **aktivitou kopírování** zřetězenou s **aktivitou uložené procedury**. Předchozí kopie dat ze zdrojového úložiště do SQL Server pracovní tabulky, například **UpsertStagingTable**, jako název tabulky v datové sadě. Pak druhá procedura vyvolá uloženou proceduru ke sloučení zdrojových dat z pracovní tabulky do cílové tabulky a vyčištění pracovní tabulky.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

V databázi definujte uloženou proceduru pomocí logiky sloučení, podobně jako v následujícím příkladu, který ukazuje z předchozí aktivity uložené procedury. Předpokládejme, že cílem je **marketingová** tabulka se třemi sloupci: **ProfileID**, **State** a **Category**. Proveďte Upsert na základě sloupce **ProfileID** .

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING UpsertStagingTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE UpsertStagingTable
END
```

**Možnost 2:** Můžete zvolit, aby se [v rámci aktivity kopírování vyvolala uložená procedura](#invoke-a-stored-procedure-from-a-sql-sink). Tento přístup spustí každou dávku (podle `writeBatchSize` Vlastnosti) ve zdrojové tabulce namísto použití možnosti hromadné vložení jako výchozího přístupu v aktivitě kopírování.

### <a name="overwrite-the-entire-table"></a>Přepsat celou tabulku

Vlastnost **preCopyScript** můžete nakonfigurovat v jímky aktivity kopírování. V takovém případě se pro každou aktivitu kopírování, která běží, Azure Data Factory spustí skript jako první. Potom spustí kopii pro vložení dat. Chcete-li například přepsat celou tabulku nejnovějšími daty, zadejte skript, který nejprve odstraní všechny záznamy před hromadnou zátěží nových dat ze zdroje.

### <a name="write-data-with-custom-logic"></a>Zápis dat pomocí vlastní logiky

Postup pro zápis dat pomocí vlastní logiky je podobný těm, které jsou popsané v části [Upsert data](#upsert-data) . Pokud potřebujete použít dodatečné zpracování před konečným vložením zdrojových dat do cílové tabulky, můžete načíst do pracovní tabulky, vyvolat aktivitu uložené procedury nebo vyvolat uloženou proceduru v jímky aktivity kopírování pro uplatnění dat.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Vyvolat uloženou proceduru z jímky SQL

Při kopírování dat do SQL Server databáze můžete také nakonfigurovat a vyvolat uloženou proceduru zadanou uživatelem s dalšími parametry na každé dávce zdrojové tabulky. Funkce uložené procedury využívá [parametry s hodnotou tabulky](/dotnet/framework/data/adonet/sql/table-valued-parameters).

Uloženou proceduru můžete použít, pokud předdefinované mechanismy kopírování neslouží k tomuto účelu. Příkladem je, že chcete použít dodatečné zpracování před konečným vložením zdrojových dat do cílové tabulky. Některé další příklady zpracování jsou, když chcete sloučit sloupce, vyhledat další hodnoty a vložit je do více než jedné tabulky.

Následující příklad ukazuje, jak použít uloženou proceduru k provedení Upsert do tabulky v databázi SQL Server. Předpokládejme, že vstupní data a **marketingová** tabulka jímky mají tři sloupce: **ProfileID**, **State** a **Category**. Proveďte Upsert na základě sloupce **ProfileID** a použijte ho jenom pro konkrétní kategorii s názvem "produkt".

1. V databázi Definujte typ tabulky se stejným názvem jako **sqlWriterTableType**. Schéma typu tabulky je stejné jako schéma vrácené vašimi vstupními daty.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. V databázi definujte uloženou proceduru se stejným názvem jako **sqlWriterStoredProcedureName**. Zpracovává vstupní data ze zadaného zdroje a sloučí je do výstupní tabulky. Název parametru typu tabulky v uložené proceduře je stejný jako **TableName** definovaný v datové sadě.

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

Při kopírování dat z a do SQL Server se z SQL Server datových typů používají následující mapování pro Azure Data Factory dočasných datových typů. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ k jímky, najdete v tématu [mapování schémat a datových typů](copy-activity-schema-and-type-mapping.md).

| SQL Server datový typ | Azure Data Factory pomocný datový typ |
|:--- |:--- |
| bigint |Int64 |
| binární |Byte [] |
| bit |Logická hodnota |
| char |Řetězec, znak [] |
| date |DateTime |
| Datum a čas |DateTime |
| datetime2 |DateTime |
| DateTimeOffset |DateTimeOffset |
| Decimal |Decimal |
| Atribut FILESTREAM (varbinary (max)) |Byte [] |
| Float |dvojité |
| image |Byte [] |
| int |Int32 |
| papír |Decimal |
| nchar |Řetězec, znak [] |
| ntext |Řetězec, znak [] |
| numerické |Decimal |
| nvarchar |Řetězec, znak [] |
| real |Jednoduché |
| rowversion |Byte [] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Objekt |
| text |Řetězec, znak [] |
| time |TimeSpan |
| časové razítko |Byte [] |
| tinyint |Int16 |
| uniqueidentifier |Identifikátor GUID |
| varbinary |Byte [] |
| varchar |Řetězec, znak [] |
| xml |Řetězec |

>[!NOTE]
> Pro datové typy, které jsou mapovány na mezihodnotový průběžný typ, aktuálně aktivita kopírování podporuje přesnost až na 28. Pokud máte data, která vyžadují přesnost větší než 28, zvažte převod na řetězec v dotazu SQL.

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Vlastnosti aktivity GetMetadata

Pokud se chcete dozvědět víc o vlastnostech, podívejte se na [aktivitu GetMetadata](control-flow-get-metadata-activity.md) . 

## <a name="using-always-encrypted"></a>Použití Always Encrypted

Když kopírujete data z/do SQL Server pomocí [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine), použijte [obecný konektor ODBC](connector-odbc.md) a SQL Server ovladač ODBC prostřednictvím místně hostovaného Integration runtime. Tento konektor SQL Server nepodporuje nyní Always Encrypted. 

A konkrétně:

1. Nastavte Integration Runtime v místním prostředí, pokud ho ještě nemáte. Podrobnosti najdete v článku [Integration runtime](create-self-hosted-integration-runtime.md) v místním prostředí.

2. Stáhněte si z [tohoto místa](/sql/connect/odbc/download-odbc-driver-for-sql-server)ovladač ODBC 64 pro SQL Server a nainstalujte ho do Integration runtime počítače. Přečtěte si další informace o tom, jak tento ovladač funguje, [pomocí Always Encrypted s ovladačem ODBC pro SQL Server](/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver#using-the-azure-key-vault-provider).

3. Vytvořte propojenou službu s typem ODBC pro připojení k vaší databázi SQL. Chcete-li použít ověřování SQL, zadejte připojovací řetězec ODBC níže a vyberte **základní** ověřování a nastavte uživatelské jméno a heslo.

    ```
    Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>
    ```

4. Odpovídajícím způsobem Vytvořte datovou sadu a aktivitu kopírování s použitím typu ODBC. Další informace najdete v článku [konektor ODBC](connector-odbc.md) .

## <a name="troubleshoot-connection-issues"></a>Řešení potíží s připojením

1. Nakonfigurujte instanci SQL Server tak, aby přijímala vzdálená připojení. Spusťte **SQL Server Management Studio**, klikněte pravým tlačítkem na **Server** a vyberte **vlastnosti**. V seznamu vyberte **připojení** a zaškrtněte políčko **pro povolení vzdáleného připojení k tomuto serveru** .

    ![Povolit vzdálená připojení](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Podrobný postup najdete v tématu [Konfigurace možnosti konfigurace serveru vzdáleného přístupu](/sql/database-engine/configure-windows/configure-the-remote-access-server-configuration-option).

2. Spustit **SQL Server Configuration Manager** Rozbalte **SQL Server konfigurace sítě** pro požadovanou instanci a vyberte **protokoly pro MSSQLSERVER**. Protokoly se zobrazí v pravém podokně. Povolte protokol TCP/IP tak, že kliknete pravým tlačítkem na **TCP/IP** a vyberete **Povolit**.

    ![Povolit protokol TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Další informace a alternativní způsoby povolení protokolu TCP/IP najdete v tématu [Povolení nebo zakázání síťového protokolu serveru](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol).

3. Ve stejném okně poklikejte na **TCP/IP** a spustí se okno **vlastností protokolu TCP/IP** .
4. Přepněte na kartu **IP adresy** . Posuňte se dolů a zobrazte část **IPAll** . Zapište **port TCP**. Výchozí hodnota je **1433**.
5. Vytvořte **pravidlo pro bránu Windows Firewall** na počítači, aby se povolil příchozí přenos prostřednictvím tohoto portu. 
6. **Ověření připojení**: Chcete-li se připojit k SQL Server pomocí plně kvalifikovaného názvu, použijte SQL Server Management Studio z jiného počítače. Příklad: `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).