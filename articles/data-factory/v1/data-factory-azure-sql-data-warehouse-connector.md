---
title: Kopírování dat do a z Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data do a z Azure SQL Data Warehouse pomocí Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: d90fa9bd-4b79-458a-8d40-e896835cfd4a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: def99a1b98970c09f28e7bfc7f44084c0f5b3c6e
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54018390"
---
# <a name="copy-data-to-and-from-azure-sql-data-warehouse-using-azure-data-factory"></a>Kopírování dat do a z Azure SQL Data Warehouse pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-azure-sql-data-warehouse-connector.md)
> * [Verze 2 (aktuální verze)](../connector-azure-sql-data-warehouse.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektor Azure SQL Data Warehouse ve verzi V2](../connector-azure-sql-data-warehouse.md).

Tento článek vysvětluje, jak pomocí aktivity kopírování ve službě Azure Data Factory k přesunu dat do a z Azure SQL Data Warehouse. Je nástavbou [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který nabízí obecný přehled o přesun dat pomocí aktivity kopírování.  

> [!TIP]
> K dosažení nejlepšího výkonu dosáhnete, načtení dat do Azure SQL Data Warehouse pomocí PolyBase. [Použití PolyBase k načítání dat do Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) část obsahuje podrobné informace o. Návod s případu použití, naleznete v tématu [načtení 1 TB do Azure SQL Data Warehouse pomocí Azure Data Factory v oblasti 15 minut](data-factory-load-sql-data-warehouse.md).

## <a name="supported-scenarios"></a>Podporované scénáře
Data můžete kopírovat **z Azure SQL Data Warehouse** ukládá následující data:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Může kopírovat data z následujících datových skladů **do služby Azure SQL Data Warehouse**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!TIP]
> Při kopírování dat z Azure SQL Database nebo SQL serveru do Azure SQL Data Warehouse, pokud tabulka neexistuje v cílovém úložišti, služby Data Factory můžete automaticky vytvořit v tabulce ve službě SQL Data Warehouse pomocí schématu tabulky zdroje dat v úložišti. Zobrazit [automatické vytvoření tabulky](#auto-table-creation) podrobnosti.

## <a name="supported-authentication-type"></a>Podporovaný typ ověřování
Azure SQL Data Warehouse konektor podpora základního ověřování.

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, která přesouvání dat do a z Azure SQL Data Warehouse pomocí různých nástrojů a rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, který kopíruje data z Azure SQL Data Warehouse je použití Průvodce kopírováním data. Zobrazit [kurzu: Načtení dat do SQL Data Warehouse pomocí služby Data Factory](../../sql-data-warehouse/sql-data-warehouse-load-with-data-factory.md) rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním data.

Tyto nástroje můžete také použít k vytvoření kanálu: **Azure portal**, **sady Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru**, **rozhraní .NET API**a  **Rozhraní REST API**. Zobrazit [kurz aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování.

Ať už používáte, nástrojů nebo rozhraní API, proveďte následující kroky k vytvoření kanálu pro přesouvání dat ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvoření **služby data factory**. Datová továrna může obsahovat jeden nebo víc kanálů. 
2. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do služby data factory. Například pokud se kopírování dat z Azure blob storage do služby Azure SQL data warehouse, vytvoříte dvě propojené služby k propojení vašeho účtu služby Azure storage a Azure SQL data warehouse se svou datovou továrnou. Vlastnosti propojené služby, které jsou specifické pro Azure SQL Data Warehouse, najdete v části [vlastnostem propojených služeb](#linked-service-properties) oddílu. 
3. Vytvoření **datových sad** k představují vstupní a výstupní data pro operaci kopírování. V příkladu uvedených v posledním kroku vytvoříte datovou sadu, která zadejte kontejner objektů blob a složku obsahující vstupní data. A vytvořte jinou datovou sadu, která určit tabulku ve službě Azure SQL data warehouse, obsahující data zkopírovaná z úložiště objektů blob. Vlastnosti datové sady, které jsou specifické pro Azure SQL Data Warehouse, najdete v části [vlastnosti datové sady](#dataset-properties) oddílu.
4. Vytvoření **kanálu** s aktivitou kopírování, která přijímá jako vstupní datovou sadu a datovou sadu jako výstup. V příkladu již bylo zmíněno dříve pomocí BlobSource jako zdroj a SqlDWSink jako jímka pro aktivitu kopírování. Podobně pokud kopírujete z Azure SQL Data Warehouse do Azure Blob Storage, můžete použít SqlDWSource a BlobSink v aktivitě kopírování. Kopírovat vlastnosti aktivity, které jsou specifické pro Azure SQL Data Warehouse, najdete v části [vlastnosti aktivity kopírování](#copy-activity-properties) oddílu. Podrobnosti o tom, jak používat úložiště dat jako zdroj nebo jímku klikněte na odkaz v předchozí části datového úložiště.

Při použití Průvodce definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál) se automaticky vytvoří za vás. Při použití nástroje a rozhraní API (s výjimkou rozhraní .NET API), můžete definovat tyto entity služby Data Factory ve formátu JSON.  Ukázky s definicemi JSON entit služby Data Factory, které se používají ke kopírování dat do a z Azure SQL Data Warehouse najdete v tématu [JSON příklady](#json-examples-for-copying-data-to-and-from-sql-data-warehouse) části tohoto článku.

Následující části obsahují podrobnosti o vlastnostech JSON, které se používají k definování entit služby Data Factory, které jsou specifické pro Azure SQL Data Warehouse:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro Azure SQL Data Warehouse propojené služby.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost type musí být nastavená na: **AzureSqlDW** |Ano |
| připojovací řetězec |Zadejte informace potřebné pro připojení k instanci Azure SQL Data Warehouse pro vlastnost připojovací řetězec. Je podporován pouze základní ověřování. |Ano |

> [!IMPORTANT]
> Konfigurace [bránu Firewall služby Azure SQL Database](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) a serveru databáze za účelem [povolit službám Azure přístup k serveru](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Kromě toho pokud kopírujete data do služby Azure SQL Data Warehouse z mimo Azure včetně místních zdrojů dat pomocí brány pro objekt pro vytváření dat, nakonfigurujte odpovídající rozsah IP adres pro počítač, který odesílá data do Azure SQL Data Warehouse.

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny datové sady typy (Azure SQL, Azure blob, tabulky Azure, atd.).

V části typeProperties se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti. **TypeProperties** části datové sady typu **AzureSqlDWTable** má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky nebo zobrazení v databázi Azure SQL Data Warehouse, která odkazuje propojenou službu. |Ano |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a zásady jsou k dispozici pro všechny typy aktivit.

> [!NOTE]
> Aktivita kopírování přijímá pouze jeden vstup a vytvoří jenom jeden výstup.

Vzhledem k tomu, vlastnosti v části typeProperties aktivity se liší s jednotlivými typu aktivity. Pro aktivitu kopírování se liší v závislosti na typy zdroje a jímky.

### <a name="sqldwsource"></a>SqlDWSource
Pokud je zdroj typu **SqlDWSource**, následující vlastnosti jsou k dispozici v **typeProperties** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| sqlReaderQuery |Použijte vlastní dotaz číst data. |Řetězec dotazu SQL. Příklad: vybrat * z MyTable. |Ne |
| sqlReaderStoredProcedureName |Název uložené procedury, která čte data ze zdrojové tabulky. |Název uložené procedury. Příkaz SELECT v uložené proceduře musí být poslední příkaz jazyka SQL. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Páry název/hodnota. Názvy a použití malých a velkých parametry musí odpovídat názvům a použití malých a velkých parametrů uložené procedury. |Ne |

Pokud **sqlReaderQuery** je určená pro SqlDWSource, spustí aktivita kopírování tohoto dotazu na zdroji Azure SQL Data Warehouse se mají získat data.

Alternativně můžete zadat uložené procedury tak, že zadáte **sqlReaderStoredProcedureName** a **storedProcedureParameters** (Pokud uložená procedura parametry).

Pokud nezadáte sqlReaderQuery nebo sqlReaderStoredProcedureName sloupce definované v části struktury datové sady JSON se používají k vytvoření dotazu, aby spustil s využitím Azure SQL Data Warehouse. Příklad: `select column1, column2 from mytable`. Pokud struktura nemá žádné definice datové sady, vyberou se všechny sloupce z tabulky.

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
| sqlWriterCleanupScript |Zadejte dotaz pro aktivitu kopírování ke spuštění tak, že po vyčištění dat určitý řez. Podrobnosti najdete v tématu [opakovatelnosti části](#repeatability-during-copy). |Příkaz dotazu. |Ne |
| allowPolyBase |Označuje, zda místo mechanismus hromadné vložení pomocí PolyBase (Pokud se používá). <br/><br/> **Při použití technologie PolyBase je doporučeným způsobem, jak načíst data do SQL Data Warehouse.** Zobrazit [použití PolyBase k načítání dat do Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) části omezení a podrobnosti. |True <br/>False (výchozí) |Ne |
| polyBaseSettings |Skupina vlastností, které může být zadán při **allowPolybase** je nastavena na **true**. |&nbsp; |Ne |
| rejectValue |Určuje číslo nebo procentuální podíl řádků, které mohou být odmítnuty předtím, než se dotaz nezdaří. <br/><br/>Další informace o možnosti odmítnutí PolyBase v **argumenty** část [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) tématu. |0 (výchozí), 1, 2... |Ne |
| rejectType |Určuje, zda je rejectValue možnost zadat hodnotu literálu nebo jako procento. |Hodnota (výchozí), procenta |Ne |
| rejectSampleValue |Určuje počet řádků, načtěte před PolyBase přepočítá procento pozice zamítnutých řádků. |1, 2, … |Ano, pokud **rejectType** je **procento** |
| useTypeDefault |Určuje způsob zpracování chybějící hodnoty v textových souborů s oddělovači, když PolyBase načte data z textového souboru.<br/><br/>Další informace o této vlastnosti v části argumenty [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |Hodnota TRUE, False (výchozí) |Ne |
| WriteBatchSize |Vloží data do tabulky SQL writeBatchSize dosáhne velikosti vyrovnávací paměti |Celé číslo (počet řádků) |Ne (výchozí: 10000) |
| writeBatchTimeout |Čekací doba pro dávkové operace insert dokončit před vypršením časového limitu. |Časový interval<br/><br/> Příklad: "00: 30:00" (30 minut). |Ne |

#### <a name="sqldwsink-example"></a>Příklad SqlDWSink

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Použijte PolyBase k načítání dat do Azure SQL Data Warehouse
Pomocí **[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** je účinný způsob načítání velkých objemů dat do Azure SQL Data Warehouse s vysokou propustností. Velké zvýšení propustnosti můžete zobrazit pomocí PolyBase namísto výchozího mechanismu hromadné vložení. Zobrazit [zkopírujte výkonu referenční číslo](data-factory-copy-activity-performance.md#performance-reference) s podrobné porovnání. Návod s případu použití, naleznete v tématu [načtení 1 TB do Azure SQL Data Warehouse pomocí Azure Data Factory v oblasti 15 minut](data-factory-load-sql-data-warehouse.md).

* Pokud vaše zdrojová data nejsou v **objektů Blob v Azure nebo Azure Data Lake Store**a formát je kompatibilní s technologií PolyBase, můžete zkopírovat přímo do Azure SQL Data Warehouse pomocí PolyBase. Zobrazit **[přímé kopie při použití technologie PolyBase](#direct-copy-using-polybase)** s podrobnostmi.
* Pokud zdrojové úložiště dat a formát není podporován původně PolyBase, můžete použít **[připravené kopírování při použití technologie PolyBase](#staged-copy-using-polybase)** místo toho funkci. Také poskytuje vyšší propustnost automaticky převádí data do formátu kompatibilním PolyBase a ukládání dat ve službě Azure Blob storage. Pak načte data do SQL Data Warehouse.

Nastavte `allowPolyBase` vlastnost **true** jak je znázorněno v následujícím příkladu pro službu Azure Data Factory pro kopírování dat do Azure SQL Data Warehouse pomocí PolyBase. Při allowPolyBase nastavíte na hodnotu true, můžete zadat pomocí konkrétní vlastnosti PolyBase `polyBaseSettings` skupiny vlastností. Zobrazit [SqlDWSink](#SqlDWSink) podrobné informace o vlastnosti, které můžete použít s polyBaseSettings.

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

### <a name="direct-copy-using-polybase"></a>Přímá kopie při použití technologie PolyBase
SQL Data Warehouse PolyBase přímo podporují objektů Blob v Azure a Azure Data Lake Store (s použitím instančního objektu) jako zdroj a s požadavky na formát konkrétní soubor. Pokud vaše zdrojová data splňuje kritéria popsané v této části, můžete přímo zkopírovat ze zdrojového úložiště dat do Azure SQL Data Warehouse pomocí PolyBase. V opačném případě můžete použít [připravené kopírování při použití technologie PolyBase](#staged-copy-using-polybase).

> [!TIP]
> Ke zkopírování dat z Data Lake Store do služby SQL Data Warehouse efektivně, další informace z [Azure Data Factory umožňuje ještě snadněji a pohodlné odhalit další poznatky z dat při použití Data Lake Store s využitím SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Pokud požadavky nejsou splněny, Azure Data Factory zkontroluje nastavení a automaticky přejde zpět k hromadné vložení mechanismus pro přesun dat.

1. **Zdroj propojené služby** je typu: **AzureStorage** nebo **AzureDataLakeStore s ověřování instančních objektů**.  
2. **Vstupní datová sada** je typu: **AzureBlob** nebo **AzureDataLakeStore**a formát, zadejte v části `type` vlastnosti je **OrcFormat**, **ParquetFormat**, nebo **TextFormat** s následující konfigurací:

   1. `rowDelimiter` musí být **\n**.
   2. `nullValue` je nastavena na **prázdný řetězec** (""), nebo `treatEmptyAsNull` je nastavena na **true**.
   3. `encodingName` je nastavena na **utf-8**, což je **výchozí** hodnotu.
   4. `escapeChar`, `quoteChar`, `firstRowAsHeader`, a `skipLineCount` nejsou zadány.
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

3. Neexistuje žádná `skipHeaderLineCount` v nabídce **BlobSource** nebo **AzureDataLakeStore** pro aktivitu kopírování v kanálu.
4. Neexistuje žádná `sliceIdentifierColumnName` v nabídce **SqlDWSink** pro aktivitu kopírování v kanálu. (PolyBase zaručuje, že se aktualizuje všechna data nebo nic je aktualizována najednou. K dosažení **opakovatelnosti**, můžete použít `sqlWriterCleanupScript`).
5. Neexistuje žádná `columnMapping` používán v přidružené kopie aktivity.

### <a name="staged-copy-using-polybase"></a>Fázované kopírování při použití technologie PolyBase
Pokud vaše zdrojová data nesplňuje kritéria představíme v předchozí části, můžete povolit kopírování dat prostřednictvím dočasné pracovní Azure Blob Storage (nemůže být Storage úrovně Premium). V takovém případě služby Azure Data Factory automaticky provede transformace data splňují požadavky na formát dat o PolyBase a pak pomocí PolyBase načtete data do SQL Data Warehouse a v poslední čištění dočasná data z úložiště objektů Blob. V tématu [připravené kopírování](data-factory-copy-activity-performance.md#staged-copy) podrobnosti o tom, jak kopírovat data prostřednictvím pracovního objektu Blob Azure funguje obecně.

> [!NOTE]
> Při kopírování dat z dat místní ukládání do Azure SQL Data Warehouse pomocí PolyBase a Fázování importu, pokud je vaše brána správy dat verze nižší než 2.4, prostředí JRE (Java Runtime Environment) vyžaduje na počítači brány, který slouží k transformaci zdrojových dat do správný formát. Navrhněte že upgradu vaší brány na nejnovější verzi, aby tyto závislosti.
>

Pokud chcete tuto funkci používat, vytvořte [propojenou službu Azure Storage](data-factory-azure-blob-connector.md#azure-storage-linked-service) , který odkazuje na účet úložiště Azure, který má úložiště objektů blob v mezičase, zadejte `enableStaging` a `stagingSettings` vlastnosti pro aktivitu kopírování, jak je znázorněno v Následující kód:

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

## <a name="best-practices-when-using-polybase"></a>Osvědčené postupy při použití technologie PolyBase
Následující části obsahují další osvědčené postupy pro ty, které jsou uvedeny v [osvědčené postupy pro službu Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Oprávnění databáze
Použití technologie PolyBase, vyžaduje má uživatel používá k načtení dat do SQL Data Warehouse [oprávnění "CONTROL"](https://msdn.microsoft.com/library/ms191291.aspx) na cílové databázi. Přidejte tohoto uživatele jako člena role "db_owner" je jeden způsob, jak toho dosáhnout. Zjistěte, jak to provést pomocí následujících [v této části](../../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limitation"></a>Velikost řádku a datový typ omezení
Načítání Polybase jsou omezené na načítání řádků i menší než **1 MB** a nelze načíst do VARCHR(MAX) či NVARCHAR(MAX) nebo VARBINARY(MAX). Odkazovat na [tady](../../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Pokud budete mít zdroj dat s řádky větší než 1 MB, můžete rozdělit zdrojové tabulky svisle na několik malých ty, kde největší velikost řádku každého z nich nepřekračuje limit. Menší tabulky pak lze načíst pomocí PolyBase a sloučeny ve službě Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>Třída prostředků SQL Data Warehouse
K dosažení nejlepší možné propustnost, zvažte možnost používá k načtení dat do SQL Data Warehouse pomocí PolyBase uživateli přiřadit větší třídu prostředků. Zjistěte, jak to provést pomocí následujících [změnit v příkladu třída prostředků uživatele](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).

### <a name="tablename-in-azure-sql-data-warehouse"></a>Název tabulky ve službě Azure SQL Data Warehouse
Následující tabulka obsahuje příklady o tom, jak zadat **tableName** vlastnost v datové sadě JSON pro různé kombinace schématu a název tabulky.

| Schéma databáze | Název tabulky | Vlastnost tableName JSON |
| --- | --- | --- |
| vlastník databáze |Tabulka |Tabulka nebo vlastník databáze. Tabulka nebo [dbo]. [MyTable] |
| dbo1 |Tabulka |dbo1. Tabulka nebo [dbo1]. [MyTable] |
| vlastník databáze |My.Table |[My.Table] nebo [dbo]. [My.Table] |
| dbo1 |My.Table |[dbo1]. [My.Table] |

Pokud se zobrazí následující chyba, můžou být problémy s hodnotou, kterou jste zadali pro vlastnost tableName. Viz tabulka pro správný způsob, jak určit hodnoty pro vlastnost tableName JSON.  

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Sloupce s výchozími hodnotami.
Funkce PolyBase ve službě Data Factory v současné době přijímá pouze stejný počet sloupců jako v cílové tabulce. Řekněme, že máte tabulku s čtyři sloupce a jeden z nich je definované s výchozí hodnotou. Vstupní data stále by měl obsahovat čtyři sloupce. Poskytuje vstupní datovou sadu 3 sloupci produkoval by zpráva podobná následující zpráva:

```
All columns of the table must be specified in the INSERT BULK statement.
```
Hodnota NULL je zvláštní forma výchozí hodnotu. Pokud je sloupec s možnou hodnotou Null, vstupní data (v objektu blob) pro tento sloupec může být prázdný (nelze chybí vstupní datové sady). PolyBase vloží NULL u nich ve službě Azure SQL Data Warehouse.  

## <a name="auto-table-creation"></a>Automatické vytvoření tabulky
Pokud použijete Průvodce kopírováním ke zkopírování dat z Azure SQL Database nebo SQL serveru do Azure SQL Data Warehouse a tabulku, která odpovídá zdrojová tabulka neexistuje v cílovém úložišti, Data Factory automaticky vytvořit tabulky v datovém skladu podle u SING schématu zdrojové tabulky.

Data Factory vytvoří v cílové úložiště se stejným názvem tabulky v úložišti zdroje dat v tabulce. Datové typy sloupců jsou zvolena podle následující mapování typu. V případě potřeby provede převody typů, chcete-li vyřešit jakékoli nekompatibility mezi zdrojovým a cílovým úložištěm. Používá se také tabulky kruhové dotazování na distribuci.

| Typ sloupce zdrojové databáze SQL | Cílový typ sloupce SQL data Warehouse (omezení velikosti) |
| --- | --- |
| Int | Int |
| BigInt | BigInt |
| SmallInt | SmallInt |
| TinyInt | TinyInt |
| Bit | Bit |
| Desítkově | Desítkově |
| Čísla | Desítkově |
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
| DateTimeOffset | DateTimeOffset |
| SmallDateTime | SmallDateTime |
| Text | Varchar (až 8000) |
| NText | NVarChar (až 4000) |
| Image | VarBinary (až 8000) |
| UniqueIdentifier | UniqueIdentifier |
| Char | Char |
| nChar | nChar |
| VarChar | VarChar (až 8000) |
| NVarChar | NVarChar (až 4000) |
| XML | Varchar (až 8000) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../../includes/data-factory-type-repeatability-for-sql-sources.md)]

## <a name="type-mapping-for-azure-sql-data-warehouse"></a>Mapování typu pro službu Azure SQL Data Warehouse
Jak je uvedeno v [aktivity přesunu dat](data-factory-data-movement-activities.md) článku, aktivita kopírování provádí automatické typ převody z typů zdroje do jímky typy s přístupem následující krok 2:

1. Převést na typ formátu .NET typy nativních zdrojů
2. Převést z typu .NET native jímky typu

Při přesouvání dat do a z Azure SQL Data Warehouse, se používají následující mapování z typu SQL pro typ formátu .NET a naopak.

Mapování je stejné jako [mapování datového typu aplikace SQL Server pro technologii ADO.NET](https://msdn.microsoft.com/library/cc716729.aspx).

| Typ databázového stroje SQL serveru | Typ rozhraní .NET framework |
| --- | --- |
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
| tinyint |Bajt |
| UniqueIdentifier |Guid |
| varbinary |Byte] |
| varchar |Řetězec, Char] |
| xml |XML |

Můžete také namapovat sloupce ze zdrojové datové sady na sloupce z datové sady jímky v definici aktivity kopírování. Podrobnosti najdete v tématu [mapování sloupců v datové sadě ve službě Azure Data Factory](data-factory-map-columns.md).

## <a name="json-examples-for-copying-data-to-and-from-sql-data-warehouse"></a>Příklady JSON pro kopírování dat do a ze služby SQL Data Warehouse
Následující příklady popisují ukázkový JSON definice, které můžete použít k vytvoření kanálu pomocí [webu Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) nebo [sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Zobrazí se kopírování dat do a z Azure SQL Data Warehouse a Azure Blob Storage. Nicméně je možné zkopírovat data **přímo** z libovolného zdroje do libovolné jímky uvedeno [tady](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivit kopírování ve službě Azure Data Factory.

### <a name="example-copy-data-from-azure-sql-data-warehouse-to-azure-blob"></a>Příklad: Kopírování dat z Azure SQL Data Warehouse do objektů Blob v Azure
Ukázka definuje následující entity služby Data Factory:

1. Propojené služby typu [AzureSqlDW](#linked-service-properties).
2. Propojené služby typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [AzureSqlDWTable](#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [SqlDWSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data časových řad (hodinovou, denní atd) z tabulky v databázi Azure SQL Data Warehouse do objektu blob každou hodinu. Vlastnostech JSON použitých v tyto ukázky jsou popsány v části podle ukázky.

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
**Azure SQL Data Warehouse vstupní datovou sadu:**

Ukázka předpokládá vytvoříte tabulku "MyTable" ve službě Azure SQL Data Warehouse a obsahuje sloupec s názvem "timestampcolumn" pro data časových řad.

Nastavení "externí": "PRAVDA" informuje služby Data Factory, že datová sada je externí do služby data factory a není vytvořen aktivitou ve službě data factory.

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
**Výstupní datová sada Azure Blob:**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt blob se dynamicky vyhodnocuje na základě doby spuštění řez, který se právě zpracovává. Cesta ke složce používá rok, měsíc, den a části hodin čas spuštění.

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

Kanálu obsahujícího aktivitu kopírování, který je nakonfigurován na použití vstupních a výstupních datových sad a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **SqlDWSource** a **jímky** je typ nastaven na **BlobSink**. Zadaná pro dotaz SQL **SqlReaderQuery** vlastnost vybere data za poslední hodinu pro kopírování.

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
> V tomto příkladu **sqlReaderQuery** je určená pro SqlDWSource. Tento dotaz spustí aktivita kopírování na zdroji Azure SQL Data Warehouse se mají získat data.
>
> Alternativně můžete zadat uložené procedury tak, že zadáte **sqlReaderStoredProcedureName** a **storedProcedureParameters** (Pokud uložená procedura parametry).
>
> Pokud nezadáte sqlReaderQuery nebo sqlReaderStoredProcedureName sloupce definované v části struktury datové sady JSON se používají k vytvoření dotazu (vyberte Sloupec1, Sloupec2 z mytable) ke spuštění na Azure SQL Data Warehouse. Pokud struktura nemá žádné definice datové sady, vyberou se všechny sloupce z tabulky.
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-sql-data-warehouse"></a>Příklad: Kopírování dat z objektů Blob v Azure do Azure SQL Data Warehouse
Ukázka definuje následující entity služby Data Factory:

1. Propojené služby typu [AzureSqlDW](#linked-service-properties).
2. Propojené služby typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureSqlDWTable](#dataset-properties).
5. A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) a [SqlDWSink](#copy-activity-properties).

Ukázka zkopíruje časových řad dat (každou hodinu, každý den atd.) z Azure blob do tabulky ve službě Azure SQL Data Warehouse databáze každou hodinu. Vlastnostech JSON použitých v tyto ukázky jsou popsány v části podle ukázky.

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
**Azure vstupní datovou sadou objektů Blob:**

Data je převzata z nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Název složky a cesta k souboru pro tento objekt blob se dynamicky vyhodnocuje podle času spuštění řezu, který se právě zpracovává. Cesta ke složce používá rok, měsíc a den čas spuštění a název souboru používá hodinová část času zahájení. "externí": "PRAVDA" nastavení informuje služby Data Factory, že tato tabulka je externí do služby data factory a není vytvořen aktivitou ve službě data factory.

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

Ukázce kopíruje data do tabulky s názvem "MyTable" ve službě Azure SQL Data Warehouse. Vytvoření tabulky ve službě Azure SQL Data Warehouse s stejný počet sloupců tak, jak očekáváte objektů Blob CSV soubor obsahovat. Nové řádky do tabulky přidají každou hodinu.

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

Kanálu obsahujícího aktivitu kopírování, který je nakonfigurován na použití vstupních a výstupních datových sad a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **BlobSource** a **jímky** je typ nastaven na **SqlDWSink**.

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
Návod, najdete v části Viz [načtení 1 TB do Azure SQL Data Warehouse pomocí Azure Data Factory v oblasti 15 minut](data-factory-load-sql-data-warehouse.md) a [načtení dat pomocí Azure Data Factory](../../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) článek v dokumentaci k Azure SQL Data Warehouse.

## <a name="performance-and-tuning"></a>Výkon a optimalizace
Zobrazit [výkonem aktivity kopírování & Průvodci optimalizací](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů této ovlivnit výkon přesouvání dat (aktivita kopírování) ve službě Azure Data Factory a různé způsoby, jak optimalizovat.
