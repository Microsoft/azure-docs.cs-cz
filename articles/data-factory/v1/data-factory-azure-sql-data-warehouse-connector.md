---
title: Kopírování dat do/z Azure SQL Data Warehouse | Microsoft Docs
description: Zjistěte, jak ke zkopírování dat z Azure SQL Data Warehouse pomocí Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: d90fa9bd-4b79-458a-8d40-e896835cfd4a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 678913796edafe86e19d8907e3a2e29ec15ffa90
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37047073"
---
# <a name="copy-data-to-and-from-azure-sql-data-warehouse-using-azure-data-factory"></a>Kopírování dat do a z Azure SQL Data Warehouse pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-azure-sql-data-warehouse-connector.md)
> * [Verze 2 (aktuální verze)](../connector-azure-sql-data-warehouse.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory. Pokud používáte aktuální verze služby Data Factory, přečtěte si téma [konektor Azure SQL Data Warehouse v V2](../connector-azure-sql-data-warehouse.md).

Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory pro přesun dat z Azure SQL Data Warehouse. Vychází [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který představuje obecný přehled přesun dat s aktivitou kopírování.  

> [!TIP]
> K dosažení nejlepšího výkonu, použijte k načtení dat do Azure SQL Data Warehouse PolyBase. [PolyBase používá k načtení dat do Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) obsahuje podrobnosti. Návod s případu použití najdete v tématu [načíst 1 TB do Azure SQL Data Warehouse pomocí Azure Data Factory v části 15 minut](data-factory-load-sql-data-warehouse.md).

## <a name="supported-scenarios"></a>Podporované scénáře
Může kopírovat data **z Azure SQL Data Warehouse** ukládá do následující data:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Může kopírovat data z následujících datových úložišť **do Azure SQL Data Warehouse**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!TIP]
> Při kopírování dat z SQL serveru nebo databáze SQL Azure do Azure SQL Data Warehouse, pokud tabulka neexistuje v cílové úložiště, Data Factory můžete automaticky vytvořit v tabulce v SQL Data Warehouse pomocí schématu tabulky v úložišti dat zdroje. V tématu [automatické vytvoření tabulky](#auto-table-creation) podrobnosti.

## <a name="supported-authentication-type"></a>Podporované typ ověřování
Azure SQL Data Warehouse konektor podporu základní ověřování.

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z Azure SQL Data Warehouse pomocí různých nástrojů nebo rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, který kopíruje data z Azure SQL Data Warehouse je pomocí Průvodce kopírováním data. V tématu [kurz: načtení dat do SQL Data Warehouse pomocí služby Data Factory](../../sql-data-warehouse/sql-data-warehouse-load-with-data-factory.md) podrobný rychlé vytvoření kanálu pomocí Průvodce kopírováním data.

Tyto nástroje můžete také použít k vytvoření kanálu: **portál Azure**, **Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru** , **.NET API**, a **rozhraní REST API**. V tématu [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování.

Jestli používáte nástroje nebo rozhraní API, je třeba provést následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat podřízený:

1. Vytvoření **objekt pro vytváření dat**. Objekt pro vytváření dat může obsahovat jeden nebo víc kanálů. 
2. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do data factory. Pokud jsou kopírování dat z Azure blob storage do služby Azure SQL data warehouse, například vytvoříte dvě propojené služby pro vytváření dat. propojte vašeho účtu úložiště Azure a Azure SQL data warehouse. Vlastnosti propojené služby, které jsou specifické pro Azure SQL Data Warehouse, najdete v části [propojené vlastnosti služby](#linked-service-properties) části. 
3. Vytvoření **datové sady** představují vstupní a výstupní data pro kopírování. V příkladu uvedených v posledním kroku vytvoříte datové sady a zadat kontejner objektů blob a složky, která obsahuje vstupní data. A vytvořte jinou datovou sadu, která zadejte v tabulce v datovém skladu Azure SQL, který obsahuje data zkopírovat z úložiště objektů blob. Vlastnosti datové sady, které jsou specifické pro Azure SQL Data Warehouse, najdete v části [vlastnosti datové sady](#dataset-properties) části.
4. Vytvoření **kanálu** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. V příkladu již bylo zmíněno dříve použijete BlobSource jako zdroj a SqlDWSink jako jímku pro aktivitu kopírování. Podobně pokud kopírujete z Azure SQL Data Warehouse do úložiště objektů Blob Azure, můžete použít SqlDWSource a BlobSink v aktivitě kopírování. Kopírovat vlastnosti aktivity, které jsou specifické pro Azure SQL Data Warehouse, najdete v části [zkopírovat vlastnosti aktivity](#copy-activity-properties) části. Podrobnosti o tom, jak používat úložiště dat jako zdroj nebo jímka klikněte na odkaz v předchozí části pro data store.

Když použijete průvodce, jsou automaticky vytvoří definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál). Při použití nástroje nebo rozhraní API (s výjimkou .NET API), definujete tyto entity služby Data Factory pomocí formátu JSON.  Ukázky s definicemi JSON entit služby Data Factory, které se používají ke zkopírování dat do/z Azure SQL Data Warehouse, najdete v části [JSON příklady](#json-examples-for-copying-data-to-and-from-sql-data-warehouse) tohoto článku.

Následující části obsahují podrobnosti o vlastnostech formátu JSON, které slouží k definování konkrétní entity služby Data Factory pro Azure SQL Data Warehouse:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka obsahuje popis specifické pro službu Azure SQL Data Warehouse propojené elementy JSON.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost typu musí být nastavena na: **AzureSqlDW** |Ano |
| připojovací řetězec |Zadejte informace potřebné pro připojení k Azure SQL Data Warehouse instance pro vlastnost connectionString. Podporováno je pouze základní ověřování. |Ano |

> [!IMPORTANT]
> Konfigurace [Firewall databáze Azure SQL](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) a databázový server, který [povolit službám Azure přístup k serveru](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Kromě toho pokud data kopírujete do Azure SQL Data Warehouse z mimo Azure včetně z místního zdroje dat se brána objekt pro vytváření dat, nakonfigurujte příslušné rozsah IP adres pro počítač, který odesílá data do Azure SQL Data Warehouse.

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny typy datovou sadu (Azure SQL Azure blob, tabulky Azure, atd.).

V rámci typeProperties části se liší pro jednotlivé typy datovou sadu a poskytuje informace o umístění dat v úložišti. **Rámci typeProperties** části datové sady typu **AzureSqlDWTable** má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky nebo zobrazení v databázi Azure SQL Data Warehouse, která propojená služba odkazuje. |Ano |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, například název, popis, vstupní a výstupní tabulky a zásad jsou dostupné pro všechny typy aktivit.

> [!NOTE]
> Aktivita kopírování přijímá pouze jeden vstup a vytváří jenom jeden výstup.

Vzhledem k tomu, vlastnosti dostupné v rámci typeProperties části aktivity se liší podle každý typ aktivity. Pro aktivitu kopírování budou lišit v závislosti na typech zdrojů a jímky.

### <a name="sqldwsource"></a>SqlDWSource
Pokud je zdroj typu **SqlDWSource**, následující vlastnosti jsou k dispozici v **rámci typeProperties** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| sqlReaderQuery |Čtení dat pomocí vlastního dotazu. |Řetězec dotazu SQL. Příklad: vybrat * z MyTable. |Ne |
| sqlReaderStoredProcedureName |Název uložené procedury, který čte data ze zdrojové tabulky. |Název uložené procedury. Poslední příkaz jazyka SQL musí být příkaz SELECT v uložené proceduře. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Páry název/hodnota. Názvy a malá a velká písmena parametry musí odpovídat názvům a malá a velká písmena parametry uložené procedury. |Ne |

Pokud **sqlReaderQuery** je zadán pro SqlDWSource, spuštění aktivity kopírování tohoto dotazu na zdroji Azure SQL Data Warehouse se získat data.

Alternativně můžete zadat uložené procedury zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters** (Pokud uložená procedura přebírá parametry).

Pokud nezadáte sqlReaderQuery nebo sqlReaderStoredProcedureName, sloupce definované v části Struktura sady dat JSON se používají k vytvoření dotazu ke spouštění na Azure SQL Data Warehouse. Příklad: `select column1, column2 from mytable`. Pokud definice datové sady nemá strukturu, jsou vybrány všechny sloupce z tabulky.

#### <a name="sqldwsource-example"></a>Příklad SqlDWSource

```JSON
"source": {
    "type": "SqlDWSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```
**Uložená procedura definice:**

```SQL
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

### <a name="sqldwsink"></a>SqlDWSink
**SqlDWSink** podporuje následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Zadejte dotaz pro aktivitu kopírování provést tak, aby se vyčistit data určitý řez. Podrobnosti najdete v tématu [opakovatelnosti části](#repeatability-during-copy). |Příkaz dotazu. |Ne |
| allowPolyBase |Označuje, zda místo hromadné vložení mechanismus použít PolyBase (v případě potřeby). <br/><br/> **Pomocí PolyBase je doporučeným způsobem, jak načíst data do SQL Data Warehouse.** V tématu [PolyBase používá k načtení dat do Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) části omezení a podrobnosti. |True <br/>NEPRAVDA (výchozí) |Ne |
| polyBaseSettings |Skupinu vlastností, které se dají zadat při **allowPolybase** je nastavena na **true**. |&nbsp; |Ne |
| rejectValue |Určuje číslo nebo podíl řádků, které může být odmítnutá předtím, než se dotaz nezdaří. <br/><br/>Další informace o možnostech odmítněte používání funkce PolyBase v **argumenty** části [vytvořit EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) tématu. |0 (výchozí), 1, 2... |Ne |
| rejectType |Určuje, zda je hodnota literálu nebo jako procento zadána možnost rejectValue. |Hodnota (výchozí), procento |Ne |
| rejectSampleValue |Určuje počet řádků k načtení předtím, než PolyBase přepočítá procento odmítnutých řádků. |1, 2, … |Ano, pokud **rejectType** je **procento** |
| useTypeDefault |Určuje způsob zpracování chybějící hodnoty v textových souborů s oddělovači, když PolyBase načítá data z textového souboru.<br/><br/>Další informace o této vlastnosti v části argumenty [vytvořit EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |Hodnota TRUE, False (výchozí) |Ne |
| writeBatchSize |Vloží data do tabulky SQL, když velikost vyrovnávací paměti dosáhne writeBatchSize |Celé číslo (počet řádků) |Ne (výchozí: 10000) |
| writeBatchTimeout |Počkejte, než čas na dokončení předtím, než vyprší časový limit operace dávkové vložení. |časový interval<br/><br/> Příklad: "00: 30:00" (30 minut). |Ne |

#### <a name="sqldwsink-example"></a>Příklad SqlDWSink

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Použijte PolyBase k načtení dat do Azure SQL Data Warehouse
Pomocí **[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** je účinný způsob načítání velké množství dat do Azure SQL Data Warehouse s vysokou propustností. Velký nárůst v propustnost můžete zobrazit pomocí PolyBase místo výchozího mechanismu hromadné vložení. V tématu [zkopírujte výkonu referenční číslo](data-factory-copy-activity-performance.md#performance-reference) s podrobné porovnání. Návod s případu použití najdete v tématu [načíst 1 TB do Azure SQL Data Warehouse pomocí Azure Data Factory v části 15 minut](data-factory-load-sql-data-warehouse.md).

* Pokud je zdroj dat v **objektů Blob v Azure nebo Azure Data Lake Store**a formát je kompatibilní s funkcí PolyBase, můžete zkopírovat přímo do Azure SQL Data Warehouse pomocí PolyBase. V tématu **[přímé kopírování pomocí PolyBase](#direct-copy-using-polybase)** s podrobnostmi.
* Pokud vaše zdrojového úložiště dat a formát není podporován původně polybase, můžete použít **[připravený kopírování pomocí PolyBase](#staged-copy-using-polybase)** místo toho funkci. Také poskytuje lepší propustnosti automaticky převod dat do formátu kompatibilní s funkcí PolyBase a ukládání dat v úložišti objektů Blob v Azure. Potom načte data do SQL Data Warehouse.

Nastavte `allowPolyBase` vlastnost **true** jak je znázorněno v následujícím příkladu pro Azure Data Factory ke zkopírování dat do Azure SQL Data Warehouse pomocí funkce PolyBase. Když nastavíte allowPolyBase na hodnotu true, můžete zadat konkrétní vlastnosti PolyBase pomocí `polyBaseSettings` skupina vlastností. najdete v článku [SqlDWSink](#SqlDWSink) část Podrobnosti o vlastnosti, které můžete použít s polyBaseSettings.

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

### <a name="direct-copy-using-polybase"></a>Přímé kopírování pomocí PolyBase
SQL Data Warehouse PolyBase přímo podporují objektů Blob v Azure a Azure Data Lake Store (pomocí instanční objekt) jako zdroj a s požadavky na konkrétní soubor formátu. Pokud vaše zdrojová data splňuje kritéria popsaných v této části, můžete přímo zkopírovat ze zdrojového úložiště dat do Azure SQL Data Warehouse pomocí PolyBase. Jinak můžete použít [připravený kopírování pomocí PolyBase](#staged-copy-using-polybase).

> [!TIP]
> Ke zkopírování dat z Data Lake Store k SQL Data Warehouse efektivně, další informace z [Azure Data Factory usnadňuje i a pohodlný k odhalení přehled o datech, při použití Data Lake Store s SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Pokud požadavky nejsou splněny, zkontroluje nastavení Azure Data Factory a automaticky se vrátí k hromadné vložení mechanismus pro přesun dat.

1. **Zdroj propojené služby** je typu: **azurestorage** nebo **AzureDataLakeStore s objekt zabezpečení ověřování služby**.  
2. **Vstupní datové sady** je typu: **AzureBlob** nebo **AzureDataLakeStore**a zadejte v části formát `type` vlastnosti je **OrcFormat**, **ParquetFormat**, nebo **TextFormat** s následující konfigurace:

   1. `rowDelimiter` musí být **\n**.
   2. `nullValue` je nastavena na **prázdný řetězec** (""), nebo `treatEmptyAsNull` je nastaven na **true**.
   3. `encodingName` je nastavena na **znakové sady utf-8**, což je **výchozí** hodnotu.
   4. `escapeChar`, `quoteChar`, `firstRowAsHeader`, a `skipLineCount` nejsou zadané.
   5. `compression` může být **bez komprese**, **GZip**, nebo **Deflate**.

    ```JSON
    "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",     
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",       
           "nullValue": "",           
           "encodingName": "utf-8"    
       },
       "compression": {  
           "type": "GZip",  
           "level": "Optimal"  
       }  
    },
    ```

3. Neexistuje žádné `skipHeaderLineCount` nastavení v části **BlobSource** nebo **AzureDataLakeStore** pro aktivitu kopírování v kanálu.
4. Neexistuje žádné `sliceIdentifierColumnName` nastavení v části **SqlDWSink** pro aktivitu kopírování v kanálu. (PolyBase zaručuje, že se aktualizuje všechna data nebo nic se aktualizuje v jednom spustit. K dosažení **opakovatelnosti**, můžete použít `sqlWriterCleanupScript`).
5. Neexistuje žádné `columnMapping` použitá v přidružené v kopie aktivity.

### <a name="staged-copy-using-polybase"></a>Dvoufázové instalace kopírování pomocí PolyBase
Pokud vaše zdrojová data nesplňuje kritéria byla zavedená v předchozí části, můžete povolit kopírování dat prostřednictvím dočasné pracovní Azure Blob Storage (úložiště úrovně Premium nemůže být). V takovém případě Azure Data Factory automaticky provede transformace na data, která mají požadavkům data formátu PolyBase, potom použijte PolyBase k načtení dat do SQL Data Warehouse a v poslední čištění dočasná data z úložiště objektů Blob. V tématu [připravený kopie](data-factory-copy-activity-performance.md#staged-copy) informace o tom, jak kopírování dat prostřednictvím pracovní objektů Blob v Azure funguje obecně.

> [!NOTE]
> Při kopírování dat z data místní ukládání do Azure SQL Data Warehouse pomocí PolyBase a pracovní, pokud vaše verze brány pro správu dat je menší než 2.4, prostředí JRE (prostředí Java Runtime) se vyžaduje na počítači brány, který se používá k transformaci zdrojová data do správný formát. Naznačují, že upgrade brány na nejnovější předejdete této závislosti.
>

Chcete-li tuto funkci používat, vytvořte [propojená služba Azure Storage](data-factory-azure-blob-connector.md#azure-storage-linked-service) který odkazuje na účet úložiště Azure, který má dočasné objektu blob úložiště, zadejte `enableStaging` a `stagingSettings` vlastnosti pro aktivitu kopírování, jak je znázorněno v Následující kód:

```json
"activities":[  
{
    "name": "Sample copy activity from SQL Server to SQL Data Warehouse via PolyBase",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDWOutput" }],
    "typeProperties": {
        "source": {
            "type": "SqlSource",
        },
        "sink": {
            "type": "SqlDwSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyStagingBlob"
        }
    }
}
]
```

## <a name="best-practices-when-using-polybase"></a>Osvědčené postupy při používání funkce PolyBase
Následující části obsahují další osvědčené postupy pro ty, které jsou uvedené v [osvědčené postupy pro Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Oprávnění požadovaná databáze
Pokud chcete používat funkce PolyBase, vyžaduje má uživatel používaný k načtení dat do SQL Data Warehouse [oprávnění "Řízení"](https://msdn.microsoft.com/library/ms191291.aspx) na cílovou databázi. Jeden způsob, jak dosáhnout, který je přidejte tohoto uživatele jako člen role "db_owner". Zjistěte, jak to provést pomocí následujících [v této části](../../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limitation"></a>Velikost řádku a datový typ omezení
Polybase zatížení jsou omezeny na obou načítání řádků menší než **1 MB** a nelze načíst VARCHR(MAX), NVARCHAR(MAX) nebo VARBINARY(MAX). Odkazovat na [zde](../../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Pokud máte zdroj dat s řádky o velikosti větší než 1 MB, můžete rozdělit zdrojové tabulky svisle na několik malých sítí, kde největší velikost řádku každého z nich není delší než limit. Menší tabulky pak lze načíst pomocí PolyBase a sloučí v Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>Třída prostředků SQL Data Warehouse
K dosažení nejlepší možné propustnost, zvažte, pro větší Třída prostředků přiřadit uživatele používaný k načtení dat do SQL Data Warehouse pomocí PolyBase. Zjistěte, jak to provést pomocí následujících [změnit v příkladu třída prostředků uživatele](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).

### <a name="tablename-in-azure-sql-data-warehouse"></a>Název tabulky v Azure SQL Data Warehouse
Následující tabulka obsahuje příklady, jak můžete zadat **tableName** vlastnost v datové sadě JSON pro různé kombinace názvu schéma a tabulku.

| Schéma databáze | Název tabulky | hodnotu vlastnosti tableName JSON |
| --- | --- | --- |
| dbo |MyTable |MyTable nebo dbo. MyTable nebo [dbo]. [Tabulka] |
| dbo1 |MyTable |dbo1. MyTable nebo [dbo1]. [Tabulka] |
| dbo |My.Table |[My.Table] nebo [dbo]. [My.Table] |
| dbo1 |My.Table |[dbo1]. [My.Table] |

Pokud se zobrazí následující chyba, může to být problém s hodnotou, kterou jste zadali pro vlastnost tableName. Najdete v tabulce pro správný způsob, jak určit hodnoty pro vlastnost tableName JSON.  

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Sloupce s výchozími hodnotami
V současné době funkce PolyBase v datové továrně přijímá pouze stejný počet sloupců jako v cílové tabulce. Řekněme, máte tabulku s čtyři sloupce a jeden z nich je definovaná pomocí výchozí hodnotu. Vstupní data stále by mělo obsahovat čtyři sloupce. Poskytnutí 3sloupce vstupní datové sady, které by poskytují chyba podobná následující zpráva:

```
All columns of the table must be specified in the INSERT BULK statement.
```
Hodnota NULL je speciální forma výchozí hodnotu. Pokud je sloupec s možnou hodnotou Null, vstupní data (v objektu blob) pro tento sloupec může být prázdná (nelze chybí vstupní datové sady). PolyBase vloží NULL u nich v Azure SQL Data Warehouse.  

## <a name="auto-table-creation"></a>Automaticky vytvářené tabulky
Pokud použijete Průvodce kopírováním ke zkopírování dat z SQL serveru nebo databáze SQL Azure do Azure SQL Data Warehouse a tabulky, která odpovídá zdrojová tabulka neexistuje v cílové úložiště, Data Factory můžete automaticky vytvořit v tabulce v datovém skladu u SING – schéma zdrojové tabulky.

Data Factory vytvoří v cílové úložiště se stejným názvem tabulky v zdrojového úložiště dat v tabulce. Datové typy pro sloupce jsou zvolen v závislosti na následující mapování typu. V případě potřeby provede převody typů opravit všechny problémům s kompatibilitou mezi zdrojovým a cílovým úložišti. Používá také distribuce kruhové dotazování tabulky.

| Typ sloupce zdrojové databáze SQL | Cílový typ sloupce SQL DW (omezení velikosti) |
| --- | --- |
| Int | Int |
| BigInt | BigInt |
| SmallInt | SmallInt |
| TinyInt | TinyInt |
| Bit | Bit |
| Decimal | Decimal |
| Čísla | Decimal |
| Float | Float |
| peníze | peníze |
| Real | Real |
| SmallMoney | SmallMoney |
| Binární hodnota | Binární hodnota |
| varbinary | Varbinary (až 8000) |
| Datum | Datum |
| DateTime | DateTime |
| DateTime2 | DateTime2 |
| Čas | Čas |
| Datový typ DateTimeOffset | Datový typ DateTimeOffset |
| SmallDateTime | SmallDateTime |
| Text | Varchar (až 8000) |
| NText | NVarChar (až 4000) |
| Image | VarBinary (až 8000) |
| Typ UniqueIdentifier | Typ UniqueIdentifier |
| Char | Char |
| NChar | NChar |
| VarChar | VarChar (až 8000) |
| NVarChar | NVarChar (až 4000) |
| XML | Varchar (až 8000) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../../includes/data-factory-type-repeatability-for-sql-sources.md)]

## <a name="type-mapping-for-azure-sql-data-warehouse"></a>Mapování typu pro Azure SQL Data Warehouse
Jak je uvedeno v [aktivity přesunu dat](data-factory-data-movement-activities.md) článku aktivita kopírování provádí automatické typ převody z typů zdroje do jímky typů s následující postup krok 2:

1. Převést na typ .NET typy nativní zdrojů
2. Převést na typ jímky nativní typ formátu .NET

Při přesunu dat do a z Azure SQL Data Warehouse, se používají následující mapování z typu SQL na typ .NET a naopak.

Mapování je stejné jako [mapování datového typu aplikace SQL Server pro technologii ADO.NET](https://msdn.microsoft.com/library/cc716729.aspx).

| Typ databázového stroje SQL Server | Typ rozhraní .NET framework |
| --- | --- |
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
| tinyint |Bajt |
| Typ UniqueIdentifier |Guid |
| varbinary |Byte] |
| varchar |Řetězec, Char] |
| xml |XML |

Můžete také mapovat sloupců z datové sady zdroje na sloupce ze sady jímku dat v definici aktivity kopírování. Podrobnosti najdete v tématu [mapování sloupců datovou sadu v Azure Data Factory](data-factory-map-columns.md).

## <a name="json-examples-for-copying-data-to-and-from-sql-data-warehouse"></a>Příklady JSON pro kopírování dat do a z SQL Data Warehouse
Následující příklady poskytují ukázka JSON definice, které můžete použít k vytvoření kanálu pomocí [portál Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) nebo [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Se ukazují, jak ke zkopírování dat do a z Azure SQL Data Warehouse a Azure Blob Storage. Nicméně je možné zkopírovat data **přímo** ze všech zdrojů do jakéhokoli z jímky uvádí [sem](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.

### <a name="example-copy-data-from-azure-sql-data-warehouse-to-azure-blob"></a>Příklad: Kopírování dat z Azure SQL Data Warehouse do objektů Blob v Azure
Ukázka definuje následující entity služby Data Factory:

1. Propojené služby typu [AzureSqlDW](#linked-service-properties).
2. Propojené služby typu [azurestorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstup [datovou sadu](data-factory-create-datasets.md) typu [AzureSqlDWTable](#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [SqlDWSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data časové řady (hodinové, denní, atd.) z tabulky v databázi Azure SQL Data Warehouse do objektu blob každou hodinu. Vlastnostech JSON použitých ve tyto ukázky jsou popsané v části následující ukázky.

**Azure SQL Data Warehouse propojené služby:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Propojená služba Azure Blob storage:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure SQL Data Warehouse vstupní datové sady:**

Příkladu se předpokládá, jste vytvořili tabulku "MyTable" v Azure SQL Data Warehouse a obsahuje sloupec s názvem "timestampcolumn" pro data časové řady.

Nastavení "externí": "PRAVDA" informuje služba Data Factory, datová sada je externí k objektu pro vytváření dat a není vyprodukované aktivitu v datové továrně.

```JSON
{
  "name": "AzureSqlDWInput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Azure Blob výstupní datovou sadu:**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodiny, interval: 1). Cesta ke složce pro tento objekt blob je vyhodnocován dynamicky podle času zahájení řezu, které jsou zpracovávány. Cesta ke složce používá rok, měsíc, den a čas částí čas spuštění.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Aktivita kopírování v kanálu s SqlDWSource a BlobSink:**

Kanál obsahuje aktivitu kopírování, který je nakonfigurovaný na použití vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **SqlDWSource** a **podřízený** je typ nastaven na **BlobSink**. Zadané pro dotaz SQL **SqlReaderQuery** vlastnost vybere data za poslední hodinu pro kopírování.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[  
      {
        "name": "AzureSQLDWtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSqlDWInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlDWSource",
            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
     ]
   }
}
```
> [!NOTE]
> V příkladu **sqlReaderQuery** je zadán pro SqlDWSource. Aktivita kopírování spustí tohoto dotazu na zdroji Azure SQL Data Warehouse se získat data.
>
> Alternativně můžete zadat uložené procedury zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters** (Pokud uložená procedura přebírá parametry).
>
> Pokud nezadáte sqlReaderQuery nebo sqlReaderStoredProcedureName, sloupce definované v části Struktura sady dat JSON se používají k vytvoření dotazu (vyberte Sloupec1, Sloupec2 z mytable) ke spouštění na Azure SQL Data Warehouse. Pokud definice datové sady nemá strukturu, jsou vybrány všechny sloupce z tabulky.
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-sql-data-warehouse"></a>Příklad: Kopírování dat z objektu Blob Azure do Azure SQL Data Warehouse
Ukázka definuje následující entity služby Data Factory:

1. Propojené služby typu [AzureSqlDW](#linked-service-properties).
2. Propojené služby typu [azurestorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureSqlDWTable](#dataset-properties).
5. A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) a [SqlDWSink](#copy-activity-properties).

Kopie ukázka časové řady dat (hodinový, denní atd.) z Azure blob do tabulky v Azure SQL Data Warehouse databáze každou hodinu. Vlastnostech JSON použitých ve tyto ukázky jsou popsané v části následující ukázky.

**Azure SQL Data Warehouse propojené služby:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Propojená služba Azure Blob storage:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure vstupní datovou sadu objektu Blob:**

Data je převzata z nového objektu blob každou hodinu (frekvence: hodiny, interval: 1). Název složky a cesta k souboru pro tento objekt blob se vyhodnocují dynamicky podle času zahájení řezu, které jsou zpracovávány. Cesta ke složce používá rok, měsíc a den součástí čas spuštění a název souboru používá hodinu součástí čas spuštění. "externí": "PRAVDA" nastavení informuje služba Data Factory, že tato tabulka je externí k objektu pro vytváření dat a není vyprodukované aktivitu v datové továrně.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Azure SQL Data Warehouse výstupní datovou sadu:**

Ukázka zkopíruje data na tabulku s názvem "MyTable" v Azure SQL Data Warehouse. Vytvořte v tabulce v Azure SQL Data Warehouse s stejný počet sloupců, podle očekávání souboru CSV objektů Blob tak, aby obsahovala. Nové záznamy se přidají do tabulky každou hodinu.

```JSON
{
  "name": "AzureSqlDWOutput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Aktivita kopírování v kanálu s BlobSource a SqlDWSink:**

Kanál obsahuje aktivitu kopírování, který je nakonfigurovaný na použití vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **BlobSource** a **podřízený** je typ nastaven na **SqlDWSink**.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "AzureBlobtoSQLDW",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlDWOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```
Podrobný postup najdete [načíst 1 TB do Azure SQL Data Warehouse pomocí Azure Data Factory v části 15 minut](data-factory-load-sql-data-warehouse.md) a [načtení dat pomocí Azure Data Factory](../../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) článek v dokumentaci k Azure SQL Data Warehouse.

## <a name="performance-and-tuning"></a>Výkon a ladění
V tématu [výkonu kopie aktivity & ladění průvodce](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů, že dopad výkon přesun dat (aktivita kopírování) v Azure Data Factory a různé způsoby, jak optimalizovat ho.
