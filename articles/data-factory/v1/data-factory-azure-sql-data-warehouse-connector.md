---
title: Kopírování dat do datového skladu Azure SQL nebo z nich
description: Zjistěte, jak kopírovat data do nebo z Azure SQL Data Warehouse pomocí Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: d90fa9bd-4b79-458a-8d40-e896835cfd4a
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 2df49e65603573e4a3adcdda0635982252e70b18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80130822"
---
# <a name="copy-data-to-and-from-azure-sql-data-warehouse-using-azure-data-factory"></a>Kopírování dat do a z Azure SQL Data Warehouse pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-azure-sql-data-warehouse-connector.md)
> * [Verze 2 (aktuální verze)](../connector-azure-sql-data-warehouse.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [Konektor Datového skladu Azure SQL ve Verzi 2](../connector-azure-sql-data-warehouse.md).

Tento článek vysvětluje, jak použít aktivitu kopírování v Azure Data Factory k přesunutí dat do nebo z Azure SQL Data Warehouse. Vychází z článku [Aktivity přesunu dat,](data-factory-data-movement-activities.md) který představuje obecný přehled přesunu dat s aktivitou kopírování.

> [!TIP]
> Chcete-li dosáhnout nejlepšího výkonu, použijte PolyBase k načtení dat do Azure SQL Data Warehouse. [Použití PolyBase k načtení dat do](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) části Azure SQL Data Warehouse obsahuje podrobnosti. Návod k případu použití najdete v [tématu načtení 1 TB do Azure SQL Data Warehouse do 15 minut s Azure Data Factory](data-factory-load-sql-data-warehouse.md).

## <a name="supported-scenarios"></a>Podporované scénáře
Data z **Azure SQL Data Warehouse** můžete zkopírovat do následujících úložišť dat:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Data z následujících úložišť dat můžete zkopírovat **do datového skladu Azure SQL**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!TIP]
> Při kopírování dat z SQL Server nebo Azure SQL Database do Azure SQL Data Warehouse, pokud tabulka neexistuje v cílovém úložišti, Data Factory můžete automaticky vytvořit tabulku v SQL Data Warehouse pomocí schématu tabulky ve zdrojovém úložišti dat. Podrobnosti najdete [v tématu Automatické vytváření tabulek.](#auto-table-creation)

## <a name="supported-authentication-type"></a>Podporovaný typ ověřování
Konektor Azure SQL Data Warehouse podporuje základní ověřování.

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data do nebo z datového skladu Azure SQL pomocí různých nástrojů nebo api.

Nejjednodušší způsob, jak vytvořit kanál, který kopíruje data do nebo z Azure SQL Data Warehouse, je použít Průvodce kopírováním dat. Viz [Kurz: Načtení dat do datového skladu SQL s továrně dat](../../sql-data-warehouse/sql-data-warehouse-load-with-data-factory.md) pro rychlý návod na vytvoření kanálu pomocí Průvodce kopírováním dat.

K vytvoření kanálu můžete taky použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Šablona Azure Resource Manager**, Rozhraní **.NET API**a REST **API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v tématu [Kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aktivity.

Bez ohledu na to, zda používáte nástroje nebo api, provedete následující kroky k vytvoření kanálu, který přesune data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **datovou továrnu**. Továrna dat může obsahovat jeden nebo více kanálů. 
2. Vytvořte **propojené služby** pro propojení vstupních a výstupních úložišť dat s vaší továrně dat. Pokud například kopírujete data z úložiště objektů blob Azure do datového skladu Azure SQL, vytvoříte dvě propojené služby, které propojí váš účet úložiště Azure a datový sklad Azure SQL s vaší továrně dat. Vlastnosti propojené služby, které jsou specifické pro Azure SQL Data Warehouse, najdete v článku [vlastnosti propojené služby.](#linked-service-properties) 
3. Vytvořte **datové sady** představující vstupní a výstupní data pro operaci kopírování. V příkladu uvedeném v posledním kroku vytvoříte datovou sadu, která určí kontejner objektů blob a složku, která obsahuje vstupní data. A vytvoříte další datovou sadu k určení tabulky v datovém skladu Azure SQL, který obsahuje data zkopírovaná z úložiště objektů blob. Vlastnosti datové sady, které jsou specifické pro Azure SQL Data Warehouse, najdete v tématu [vlastnosti datové sady](#dataset-properties) části.
4. Vytvořte **kanál** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. V příkladu uvedeném výše použijete Objekt blobSource jako zdroj a SqlDWSink jako jímku pro aktivitu kopírování. Podobně pokud kopírujete z Azure SQL Data Warehouse do úložiště objektů blob Azure, použijete sqldwsource a blobsink v aktivitě kopírování. Informace o vlastnostech aktivity kopírování, které jsou specifické pro Azure SQL Data Warehouse, najdete v tématu [kopírování vlastností aktivity.](#copy-activity-properties) Podrobnosti o tom, jak používat úložiště dat jako zdroj nebo jímku, klikněte na odkaz v předchozí části úložiště dat.

Při použití průvodce jsou automaticky vytvořeny definice JSON pro tyto entity Data Factory (propojené služby, datové sady a kanál). Při použití nástrojů nebo rozhraní API (s výjimkou rozhraní .NET API) definujete tyto entity Data Factory pomocí formátu JSON. Ukázky s definicemi JSON pro entity Data Factory, které se používají ke kopírování dat do/z datového skladu Azure SQL, najdete v části [Příklady JSON](#json-examples-for-copying-data-to-and-from-sql-data-warehouse) v tomto článku.

V následujících částech jsou uvedeny podrobnosti o vlastnostech JSON, které se používají k definování entit Datové továrny specifické pro Datový sklad Azure SQL:

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb
Následující tabulka obsahuje popis prvků JSON specifických pro propojenou službu Azure SQL Data Warehouse.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost type musí být nastavena na: **AzureSqlDW** |Ano |
| připojovací řetězec |Zadejte informace potřebné pro připojení k instanci datového skladu Azure SQL pro vlastnost connectionString. Je podporováno pouze základní ověřování. |Ano |

> [!IMPORTANT]
> Nakonfigurujte [bránu Azure SQL Database Firewall](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) a databázový server tak, aby [umožňovaly přístup ke serveru službám Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Navíc pokud kopírujete data do Azure SQL Data Warehouse z mimo Azure, včetně místních zdrojů dat s bránou pro datové továrny, nakonfigurujte příslušný rozsah IP adres pro počítač, který odesílá data do Azure SQL Data Warehouse.

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datových sad, naleznete v článku [Vytváření datových sad.](data-factory-create-datasets.md) Oddíly, jako je struktura, dostupnost a zásady datové sady JSON, jsou podobné pro všechny typy datových sad (Azure SQL, Azure blob, Tabulka Azure atd.).

Sekce typeProperties se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Část **typeProperties** pro datovou sadu typu **AzureSqlDWTable** má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky nebo zobrazení v databázi Azure SQL Data Warehouse, na kterou propojená služba odkazuje. |Ano |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit, naleznete v článku [Vytváření kanálů.](data-factory-create-pipelines.md) Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a zásady jsou k dispozici pro všechny typy aktivit.

> [!NOTE]
> Aktivita kopírování trvá pouze jeden vstup a vytváří pouze jeden výstup.

Vzhledem k tomu, vlastnosti, které jsou k dispozici v typeProperties části aktivity se liší s každým typem aktivity. U aktivity kopírování se liší v závislosti na typech zdrojů a propadů.

### <a name="sqldwsource"></a>Zdroj SQLDW
Pokud je zdroj typu **SqlDWSource**, jsou v části **typeProperties** k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| sqlReaderQuery |Ke čtení dat použijte vlastní dotaz. |Řetězec dotazu SQL. Příklad: vyberte * z MyTable. |Ne |
| sqlReaderStoredProcedureName |Název uložené procedury, která čte data ze zdrojové tabulky. |Název uložené procedury. Poslední příkaz SQL musí být příkaz SELECT v uložené proceduře. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Dvojice název/hodnota. Názvy a písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. |Ne |

Pokud **sqlReaderQuery** je zadán pro SqlDWSource, aktivita kopírování spustí tento dotaz proti zdroji Azure SQL Data Warehouse získat data.

Alternativně můžete zadat uloženou proceduru zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters** (pokud uložená procedura přebírá parametry).

Pokud nezadáte sqlReaderQuery nebo sqlReaderStoredProcedureName, sloupce definované v části struktura datové sady JSON se používají k vytvoření dotazu ke spuštění proti Azure SQL Data Warehouse. Příklad: `select column1, column2 from mytable`. Pokud definice datové sady nemá strukturu, jsou z tabulky vybrány všechny sloupce.

#### <a name="sqldwsource-example"></a>Příklad zdroje SqlDW

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
**Definice uložené procedury:**

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

### <a name="sqldwsink"></a>Modul SQLDWSink
**SqlDWSink** podporuje následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Zadejte dotaz pro aktivitu kopírování, který má být proveden tak, aby byla vyčištěna data určitého řezu. Podrobnosti viz [část opakovatelnost](#repeatability-during-copy). |Příkaz dotazu. |Ne |
| allowPolyBase |Označuje, zda použít PolyBase (pokud je to možné) namísto bulkinsert mechanismu. <br/><br/> **Použití PolyBase je doporučený způsob, jak načíst data do datového skladu SQL.** Najdete [v článku Použití PolyBase k načtení dat do](#use-polybase-to-load-data-into-azure-sql-data-warehouse) oddílu Azure SQL Data Warehouse pro omezení a podrobnosti. |True <br/>False (výchozí) |Ne |
| polyBaseSettings |Skupina vlastností, které lze zadat při **allowPolybase** vlastnost je nastavena na **hodnotu true**. |&nbsp; |Ne |
| rejectValue |Určuje počet nebo procento řádků, které lze odmítnout před selháním dotazu. <br/><br/>Další informace o možnostech odmítnutí základny PolyBase naleznete v části **Argumenty** v tématu [CREATE EXTERNAL TABLE (Transact-SQL).](https://msdn.microsoft.com/library/dn935021.aspx) |0 (výchozí), 1, 2, ... |Ne |
| rejectType |Určuje, zda je možnost rejectValue zadána jako hodnota literálu nebo procento. |Hodnota (výchozí), procento |Ne |
| rejectSampleValue |Určuje počet řádků, které mají být načteny před přepočtem PolyBase procento odmítnutých řádků. |1, 2, ... |Ano, pokud **rejectType** je **procento** |
| useTypeDefault |Určuje, jak zpracovat chybějící hodnoty v oddělených textových souborech, když PolyBase načte data z textového souboru.<br/><br/>Další informace o této vlastnosti naleznete v části Argumenty v [části CREATE EXTERNAL FILE FORMAT (Transact-SQL).](https://msdn.microsoft.com/library/dn935026.aspx) |Pravda, Nepravda (výchozí) |Ne |
| writeBatchSize |Vloží data do tabulky SQL, když velikost vyrovnávací paměti dosáhne writeBatchSize |Celé číslo (počet řádků) |Ne (výchozí: 10000) |
| writeBatchTimeout |Počkejte čas pro operaci dávkové vložení k dokončení před časovým výpadkem. |Timespan<br/><br/> Příklad: "00:30:00" (30 minut). |Ne |

#### <a name="sqldwsink-example"></a>Příklad funkce SqlDWSink

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Použití PolyBase k načtení dat do datového skladu Azure SQL
Použití **[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** je efektivní způsob načítání velkého množství dat do Azure SQL Data Warehouse s vysokou propustností. Můžete vidět velký nárůst propustnost pomocí PolyBase namísto výchozího mechanismu BULKINSERT. Viz [referenční číslo výkonu kopírování](data-factory-copy-activity-performance.md#performance-reference) s podrobným porovnáním. Návod k případu použití najdete v [tématu načtení 1 TB do Azure SQL Data Warehouse do 15 minut s Azure Data Factory](data-factory-load-sql-data-warehouse.md).

* Pokud jsou vaše zdrojová data v **Azure Blob nebo Azure Data Lake Store**a formát je kompatibilní s PolyBase, můžete přímo kopírovat do Azure SQL Data Warehouse pomocí PolyBase. Viz **[Přímá kopie pomocí PolyBase](#direct-copy-using-polybase)** s podrobnostmi.
* Pokud úložiště a formát zdrojových dat není původně podporováno službou PolyBase, můžete místo toho použít funkci Fázované kopírování pomocí funkce **[PolyBase.](#staged-copy-using-polybase)** Poskytuje také lepší propustnost automatickým převodem dat do formátu kompatibilního s PolyBase a ukládáním dat do úložiště objektů blob Azure. Potom načte data do datového skladu SQL.

Nastavte `allowPolyBase` vlastnost na **hodnotu true,** jak je znázorněno v následujícím příkladu pro Azure Data Factory, abyste pomocí PolyBase zkopírovali data do Azure SQL Data Warehouse. Pokud nastavíte allowPolyBase na true, můžete pomocí `polyBaseSettings` skupiny vlastností určit specifické vlastnosti PolyBase. Podrobnosti o vlastnostech, které můžete použít s polyBaseSettings, naleznete v části [SqlDWSink.](#sqldwsink)

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
SQL Data Warehouse PolyBase přímo podporuje Azure Blob a Azure Data Lake Store (pomocí instančního objektu) jako zdroj a specifické požadavky na formát souboru. Pokud vaše zdrojová data splňují kritéria popsaná v této části, můžete přímo kopírovat z úložiště zdrojových dat do Azure SQL Data Warehouse pomocí PolyBase. V opačném případě můžete použít [fázovanou kopii pomocí polybase](#staged-copy-using-polybase).

> [!TIP]
> Chcete-li efektivně kopírovat data z úložiště data lake do sql data skladu, další informace z [Azure Data Factory umožňuje ještě jednodušší a pohodlnější odkrýt poznatky z dat při použití Úložiště dat s SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Pokud nejsou splněny požadavky, Azure Data Factory zkontroluje nastavení a automaticky přejde zpět do bulkinsert mechanismus pro přesun dat.

1. **Zdrojpropojené služby** je typu: **AzureStorage** nebo **AzureDataLakeStore s ověřováním instančního objektu**.
2. **Vstupní datová sada** je typu: **AzureBlob** nebo **AzureDataLakeStore**a typ formátu `type` pod vlastnostmi je **OrcFormat**, **ParquetFormat**nebo **TextFormat** s následujícími konfiguracemi:

   1. `rowDelimiter`musí být **\n**.
   2. `nullValue`je nastavena na **prázdný** `treatEmptyAsNull` řetězec (""), nebo je nastavena na **hodnotu true**.
   3. `encodingName`je nastavena na **utf-8**, což je **výchozí** hodnota.
   4. `escapeChar`, `quoteChar` `firstRowAsHeader`, `skipLineCount` , a nejsou zadány.
   5. `compression`nemůže být **žádná komprese**, **GZip**nebo **Deflate**.

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

3. V části `skipHeaderLineCount` **BlobSource** nebo **AzureDataLakeStore** neexistuje žádné nastavení pro aktivitu Copy v kanálu.
4. V části `sliceIdentifierColumnName` **SqlDWSink** není žádné nastavení pro aktivitu Kopírování v kanálu. (PolyBase zaručuje, že všechna data jsou aktualizována nebo nic je aktualizován v jednom spuštění. Chcete-li dosáhnout **opakovatelnosti**, můžete použít `sqlWriterCleanupScript`).
5. V přidružené `columnMapping` aktivitě Copy není použito žádné použití.

### <a name="staged-copy-using-polybase"></a>Fázovaná kopie pomocí polybase
Pokud vaše zdrojová data nesplňují kritéria zavedená v předchozí části, můžete povolit kopírování dat prostřednictvím dočasného pracovního úložiště objektů blob Azure (nemůže být úložiště Premium). V takovém případě Azure Data Factory automaticky provádí transformace na data ke splnění požadavků na formát dat PolyBase, pak použijte PolyBase k načtení dat do datového skladu SQL a konečně vyčištění temp data z úložiště objektů blob. Podrobnosti o tom, jak kopírování dat prostřednictvím pracovního objektu blob Azure funguje obecně, najdete v tématu [Fázované kopírování.](data-factory-copy-activity-performance.md#staged-copy)

> [!NOTE]
> Při kopírování dat z místního úložiště dat do Azure SQL Data Warehouse pomocí PolyBase a staging, pokud vaše verze brány pro správu dat je nižší než 2.4, JRE (Java Runtime Environment) je vyžadováno na počítači brány, který se používá k transformaci zdroje do správného formátu. Navrhněte upgradovat bránu na nejnovější, abyste se vyhnuli takové závislosti.
>

Chcete-li použít tuto funkci, vytvořte [propojenou službu Azure Storage,](data-factory-azure-blob-connector.md#azure-storage-linked-service) která odkazuje na `enableStaging` `stagingSettings` účet úložiště Azure, který má dočasné úložiště objektů blob, a pak zadejte vlastnosti a vlastnosti aktivity kopírování, jak je znázorněno v následujícím kódu:

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

## <a name="best-practices-when-using-polybase"></a>Doporučené postupy při používání polybase
Následující části obsahují další osvědčené postupy pro ty, které jsou uvedeny v [doporučené postupy pro Azure SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Požadované oprávnění k databázi
Chcete-li použít PolyBase, vyžaduje, aby uživatel používaný k načtení dat do datového skladu SQL má [oprávnění "CONTROL"](https://msdn.microsoft.com/library/ms191291.aspx) v cílové databázi. Jedním ze způsobů, jak toho dosáhnout, je přidat tohoto uživatele jako člena role "db_owner". Postup se dozvíte v [této části](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limitation"></a>Omezení velikosti řádků a datového typu
Polybase zatížení jsou omezeny na načítání řádků menší než **1 MB** a nelze načíst VARCHR(MAX), NVARCHAR(MAX) nebo VARBINARY(MAX). Viz [zde](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Pokud máte zdrojová data s řádky o velikosti větší než 1 MB, můžete rozdělit zdrojové tabulky svisle do několika malých, kde největší velikost řádku každého z nich nepřekračuje limit. Menší tabulky pak lze načíst pomocí PolyBase a sloučeny v Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>Třída prostředků datového skladu SQL
Chcete-li dosáhnout co nejlepší propustnost, zvažte přiřazení větší třídy prostředků uživateli, který se používá k načtení dat do datového skladu SQL prostřednictvím PolyBase. Zjistěte, jak to udělat v následujících [chcete-li změnit třídu prostředků uživatele příklad](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).

### <a name="tablename-in-azure-sql-data-warehouse"></a>tableName v datovém skladu Azure SQL
Následující tabulka obsahuje příklady, jak zadat vlastnost **tableName** v datové sadě JSON pro různé kombinace schématu a názvu tabulky.

| Schéma DB | Název tabulky | vlastnost tableName JSON |
| --- | --- | --- |
| Dbo |Mytable |MyTable nebo dbo. MyTable nebo [dbo]. [MyTable] |
| dbo1 |Mytable |dbo1. MyTable nebo [dbo1]. [MyTable] |
| Dbo |My.Table |[My.Table] nebo [dbo]. [My.Table] |
| dbo1 |My.Table |[dbo1]. [My.Table] |

Pokud se zobrazí následující chyba, může to být problém s hodnotou, kterou jste zadali pro vlastnost tableName. Správný způsob zadání hodnot pro vlastnost tableName JSON naleznete v tabulce.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Sloupce s výchozími hodnotami
V současné době funkce PolyBase v datové továrně přijímá pouze stejný počet sloupců jako v cílové tabulce. Řekněme, že máte tabulku se čtyřmi sloupci a jeden z nich je definován s výchozí hodnotou. Vstupní data by měla stále obsahovat čtyři sloupce. Poskytnutí vstupní datové sady ve 3 sloupci by přineslo chybu podobnou následující zprávě:

```
All columns of the table must be specified in the INSERT BULK statement.
```
Hodnota NULL je zvláštní forma výchozí hodnoty. Pokud je sloupec nullable, vstupní data (v objektu blob) pro tento sloupec může být prázdný (nemůže chybět ze vstupní datové sady). PolyBase vloží hodnotu NULL pro ně v datovém skladu Azure SQL.

## <a name="auto-table-creation"></a>Automatické vytváření tabulek
Pokud používáte Průvodce kopírováním ke kopírování dat z SQL Serveru nebo Azure SQL Database do datového skladu Azure SQL a tabulka, která odpovídá zdrojové tabulce, v cílovém úložišti neexistuje, může Data Factory automaticky vytvořit tabulku v datovém skladu podle pomocí schématu zdrojové tabulky.

Data Factory vytvoří tabulku v cílovém úložišti se stejným názvem tabulky v úložišti zdrojových dat. Datové typy pro sloupce jsou vybrány na základě následujícího mapování typů. V případě potřeby provádí převody typů opravit všechny nekompatibility mezi zdrojové a cílové úložiště. Používá také rozložení tabulky kruhového dotazování.

| Typ sloupce zdrojové databáze SQL | Typ sloupce cílového sql DW (omezení velikosti) |
| --- | --- |
| Int | Int |
| Bigint | Bigint |
| Smallint | Smallint |
| Tinyint | Tinyint |
| Bit | Bit |
| Desetinné číslo | Desetinné číslo |
| Numeric | Desetinné číslo |
| Plovoucí desetinná čárka | Plovoucí desetinná čárka |
| Peněžní částka | Peněžní částka |
| Skutečné | Skutečné |
| Smallmoney | Smallmoney |
| binární | binární |
| Varbinary | Varbinary (až 8000) |
| Datum | Datum |
| DateTime | DateTime |
| DatumČas2 | DatumČas2 |
| Time | Time |
| DateTimeOffset | DateTimeOffset |
| Smalldatetime | Smalldatetime |
| Text | Varchar (až 8000) |
| Ntext | NVarChar (až 4000) |
| Image | VarBinary (až 8000) |
| Uniqueidentifier | Uniqueidentifier |
| Char | Char |
| Nchar | Nchar |
| Varchar | VarChar (až 8000) |
| Nvarchar | NVarChar (až 4000) |
| XML | Varchar (až 8000) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../../includes/data-factory-type-repeatability-for-sql-sources.md)]

## <a name="type-mapping-for-azure-sql-data-warehouse"></a>Mapování typů pro datový sklad Azure SQL
Jak je uvedeno v článku [aktivity přesunu dat,](data-factory-data-movement-activities.md) copy aktivita provádí převody automatického typu z typů zdrojů na typy jímek s následujícím přístupem 2 kroky:

1. Převod z nativních typů zdrojů na typ .NET
2. Převod z typu .NET na nativní typ jímky

Při přesouvání dat do & z Azure SQL Data Warehouse se používají následující mapování z typu SQL na typ .NET a naopak.

Mapování je stejné jako [mapování datového typu serveru SQL Server pro ADO.NET](https://msdn.microsoft.com/library/cc716729.aspx).

| Typ databázového stroje serveru SQL Server | Typ rozhraní .NET Framework |
| --- | --- |
| bigint |Int64 |
| binární |Bajt[] |
| bitové |Logická hodnota |
| char |Řetězec, Znak[] |
| date |DateTime |
| Datum a čas |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Desetinné číslo |Desetinné číslo |
| ATRIBUT FILESTREAM (varbinary(max)) |Bajt[] |
| Plovoucí desetinná čárka |Double |
| image |Bajt[] |
| int |Int32 |
| Peníze |Desetinné číslo |
| Nchar |Řetězec, Znak[] |
| Ntext |Řetězec, Znak[] |
| numerické |Desetinné číslo |
| nvarchar |Řetězec, Znak[] |
| reálná |Single |
| Rowversion |Bajt[] |
| Smalldatetime |DateTime |
| smallint |Int16 |
| Smallmoney |Desetinné číslo |
| Sql_variant |Objekt * |
| text |Řetězec, Znak[] |
| time |TimeSpan |
| časové razítko |Bajt[] |
| tinyint |Byte |
| uniqueidentifier |Identifikátor GUID |
| Varbinary |Bajt[] |
| varchar |Řetězec, Znak[] |
| xml |XML |

Můžete také mapovat sloupce ze zdrojové datové sady do sloupců z datové sady jímky v definici aktivity kopírování. Podrobnosti najdete [v tématu Mapování sloupců datových sad v Azure Data Factory](data-factory-map-columns.md).

## <a name="json-examples-for-copying-data-to-and-from-sql-data-warehouse"></a>Příklady JSON pro kopírování dat do a z datového skladu SQL
Následující příklady poskytují ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí [Sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo Azure [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazují, jak kopírovat data do a z Azure SQL Data Warehouse a Azure Blob Storage. Data však můžete zkopírovat **přímo** z libovolného zdroje do libovolného [jímky uvedené zde](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.

### <a name="example-copy-data-from-azure-sql-data-warehouse-to-azure-blob"></a>Příklad: Kopírování dat z Azure SQL Data Warehouse do objektu Blob Azure
Ukázka definuje následující entity Data Factory:

1. Propojená služba typu [AzureSqlDW](#linked-service-properties).
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [AzureSqlDWTable](#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [SqlDWSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data časových řad (každou hodinu, denně atd.) z tabulky v databázi Azure SQL Data Warehouse do objektu blob každou hodinu. Vlastnosti JSON použité v těchto vzorcích jsou popsány v následujících částech.

**Propojená služba Azure SQL Data Warehouse:**

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
**Propojená služba úložiště objektů blob Azure:**

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
**Vstupní datová sada Datového skladu Azure:**

Ukázka předpokládá, že jste vytvořili tabulku "MyTable" v Azure SQL Data Warehouse a obsahuje sloupec s názvem "sloupec časového razítka" pro data časových řad.

Nastavení "externí": "true" informuje službu Data Factory, že datová sada je externí pro datové továrny a není vyráběna aktivitou v datové továrně.

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
**Výstupní datová sada objektu Blob Azure:**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt blob je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá části počátečního času rok, měsíc, den a hodiny.

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

**Kopírování aktivity v kanálu pomocí sqldwsource a blobsink:**

Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití vstupních a výstupních datových sad a je naplánována na každou hodinu. V definici kanálu JSON je **typ zdroje** nastaven na **SqlDWSource** a typ **jímky** je nastaven na **Objekt blobSink**. Dotaz SQL zadaný pro vlastnost **SqlReaderQuery** vybere data za poslední hodinu ke kopírování.

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
> V příkladu **sqlReaderQuery** je určen pro SqlDWSource. Aktivita kopírování spustí tento dotaz proti zdroji datového skladu Azure SQL, aby získala data.
>
> Alternativně můžete zadat uloženou proceduru zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters** (pokud uložená procedura přebírá parametry).
>
> Pokud nezadáte sqlReaderQuery nebo sqlReaderStoredProcedureName, sloupce definované v části struktura datové sady JSON se používají k vytvoření dotazu (vyberte sloupec1, sloupec2 z mytable) ke spuštění proti Azure SQL Data Warehouse. Pokud definice datové sady nemá strukturu, jsou z tabulky vybrány všechny sloupce.
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-sql-data-warehouse"></a>Příklad: Kopírování dat z objektu Blob Azure do datového skladu Azure SQL
Ukázka definuje následující entity Data Factory:

1. Propojená služba typu [AzureSqlDW](#linked-service-properties).
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureSqlDWTable](#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou Copy, která používá [Objekt BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) a [SqlDWSink](#copy-activity-properties).

Ukázka zkopíruje data časových řad (každou hodinu, denně atd.) z objektu blob Azure do tabulky v databázi Azure SQL Data Warehouse každou hodinu. Vlastnosti JSON použité v těchto vzorcích jsou popsány v následujících částech.

**Propojená služba Azure SQL Data Warehouse:**

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
**Propojená služba úložiště objektů blob Azure:**

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
**Vstupní datová sada objektu Blob Azure:**

Data se zírají z nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce a název souboru pro objekt blob jsou dynamicky vyhodnocovány na základě počátečního času zpracovávaného řezu. Cesta ke složce používá rok, měsíc a den část počátečního času a název souboru používá hodinovou část počátečního času. "externí": nastavení "true" informuje službu Data Factory, že tato tabulka je externí pro datovou továrnu a není vytvářena aktivitou v datové továrně.

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
**Výstupní datová sada datového skladu Azure SQL:**

Ukázka zkopíruje data do tabulky s názvem "MyTable" v Azure SQL Data Warehouse. Vytvořte tabulku v Azure SQL Data Warehouse se stejným počtem sloupců, jako u očekáváte, že soubor BLOB CSV bude obsahovat. Nové řádky jsou přidávány do tabulky každou hodinu.

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
**Kopírování aktivity v kanálu pomocí objektů BlobSource a SqlDWSink:**

Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití vstupních a výstupních datových sad a je naplánována na každou hodinu. V definici kanálu JSON je **typ zdroje** nastaven na **objekt BlobSource** a typ **jímky** je nastaven na **sqldwsink**.

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
Návod najdete v článku [Načtení 1 TB do Azure SQL Data Warehouse do 15 minut s Azure Data Factory](data-factory-load-sql-data-warehouse.md) a [načíst data s Azure Data Factory](../../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) článku v dokumentaci KOnZR.

## <a name="performance-and-tuning"></a>Výkon a ladění
[V tématu Průvodce sledováním výkonu & optimalizací se](data-factory-copy-activity-performance.md) dozvíte o klíčových faktorech, které ovlivňují výkon přesunu dat (aktivita kopírování) ve Službě Azure Data Factory, a o různých způsobech jeho optimalizace.
