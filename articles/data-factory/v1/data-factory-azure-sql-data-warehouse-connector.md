---
title: Kopírování dat do/z Azure synapse Analytics
description: Přečtěte si, jak kopírovat data do a z Azure synapse Analytics pomocí Azure Data Factory
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
ms.openlocfilehash: 0d071599b72f6a71bdff815f514311fb87f53d5b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452362"
---
# <a name="copy-data-to-and-from-azure-synapse-analytics-using-azure-data-factory"></a>Kopírování dat do a z Azure synapse Analytics pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-azure-sql-data-warehouse-connector.md)
> * [Verze 2 (aktuální verze)](../connector-azure-sql-data-warehouse.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si článek [konektor Azure synapse Analytics ve verzi v2](../connector-azure-sql-data-warehouse.md).

Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory přesouvat data do a z Azure synapse Analytics. Sestavuje se podle článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , který prezentuje obecný přehled přesunu dat s aktivitou kopírování.

> [!TIP]
> Abyste dosáhli nejlepšího výkonu, využijte základ k načtení dat do služby Azure synapse Analytics. Informace najdete v části [použití základu k načtení dat do služby Azure synapse Analytics](#use-polybase-to-load-data-into-azure-synapse-analytics) . Návod s případem použití najdete v tématu [načtení 1 TB do služby Azure synapse Analytics za 15 minut s Azure Data Factory](data-factory-load-sql-data-warehouse.md).

## <a name="supported-scenarios"></a>Podporované scénáře
Data **z Azure synapse Analytics** můžete kopírovat do následujících úložišť dat:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Data z následujících úložišť dat můžete kopírovat do služby **Azure synapse Analytics**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!TIP]
> Pokud při kopírování dat z SQL Server nebo Azure SQL Database do služby Azure synapse Analytics neexistuje tabulka v cílovém úložišti, Data Factory může automaticky vytvořit tabulku v Azure synapse Analytics pomocí schématu tabulky ve zdrojovém úložišti dat. Podrobnosti najdete v tématu [Automatické vytváření tabulek](#auto-table-creation) .

## <a name="supported-authentication-type"></a>Typ podporovaného ověřování
Konektor Azure synapse Analytics podporuje základní ověřování.

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data do/z analýzy Azure synapse pomocí různých nástrojů/rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, který kopíruje data do/z Azure synapse Analytics, je použít Průvodce kopírováním dat. Rychlý návod k vytváření kanálu pomocí Průvodce kopírováním dat najdete v tématu [kurz: načtení dat do služby Azure synapse Analytics pomocí Data Factory](../load-azure-sql-data-warehouse.md) .

K vytvoření kanálu můžete také použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** a **REST API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v [kurzu kopírování aktivit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Bez ohledu na to, jestli používáte nástroje nebo rozhraní API, provedete následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **datovou továrnu**. Datová továrna může obsahovat jeden nebo více kanálů. 
2. Vytvořte **propojené služby** , které propojí vstupní a výstupní úložiště dat s datovou továrnou. Pokud například kopírujete data z úložiště objektů BLOB v Azure do služby Azure synapse Analytics, vytvoříte dvě propojené služby, které propojí váš účet služby Azure Storage a Azure synapse Analytics s datovou továrnou. Vlastnosti propojených služeb, které jsou specifické pro Azure synapse Analytics, najdete v části [vlastnosti propojených služeb](#linked-service-properties) . 
3. Vytvořte datové **sady** , které reprezentují vstupní a výstupní data pro operaci kopírování. V příkladu uvedeném v posledním kroku vytvoříte datovou sadu pro určení kontejneru objektů BLOB a složky, která obsahuje vstupní data. A vytvoříte další datovou sadu pro určení tabulky v Azure synapse Analytics, která obsahuje data zkopírovaná z úložiště objektů BLOB. Vlastnosti datové sady, které jsou specifické pro Azure synapse Analytics, najdete v části [Vlastnosti datové sady](#dataset-properties) .
4. Vytvořte **kanál** s aktivitou kopírování, která převezme datovou sadu jako vstup a datovou sadu jako výstup. V předchozím příkladu použijete jako jímku aktivity kopírování BlobSource jako zdroj a SqlDWSink. Podobně pokud kopírujete z Azure synapse Analytics do Azure Blob Storage, v aktivitě kopírování použijete SqlDWSource a BlobSink. Vlastnosti aktivity kopírování, které jsou specifické pro Azure synapse Analytics, najdete v části [vlastnosti aktivity kopírování](#copy-activity-properties) . Podrobnosti o tom, jak používat úložiště dat jako zdroj nebo jímku, získáte kliknutím na odkaz v předchozí části úložiště dat.

Při použití Průvodce se automaticky vytvoří definice JSON pro tyto Entity Data Factory (propojené služby, datové sady a kanál). Pokud používáte nástroje/rozhraní API (s výjimkou rozhraní .NET API), definujete tyto Data Factory entit pomocí formátu JSON. Ukázky s definicemi JSON pro Entity Data Factory, které se používají ke kopírování dat do a ze služby Azure synapse Analytics, najdete v části [Příklady JSON](#json-examples-for-copying-data-to-and-from-azure-synapse-analytics) tohoto článku.

Následující části obsahují podrobné informace o vlastnostech JSON, které se používají k definování Data Factory entit specifických pro Azure synapse Analytics:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka uvádí popis pro prvky JSON specifické pro propojenou službu Azure synapse Analytics.

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| typ |Vlastnost Type musí být nastavená na: **AzureSqlDW** . |Yes |
| připojovací řetězec |Zadejte informace potřebné pro připojení ke službě Azure synapse Analytics instance pro vlastnost connectionString. Podporuje se jenom základní ověřování. |Yes |

> [!IMPORTANT]
> Nakonfigurujte [bránu Azure SQL Database firewall](/previous-versions/azure/ee621782(v=azure.100)#ConnectingFromAzure) a databázový server, aby měly [služby Azure přístup k serveru](/previous-versions/azure/ee621782(v=azure.100)#ConnectingFromAzure). Pokud navíc kopírujete data do Azure synapse Analytics z oblasti mimo Azure, včetně z místních zdrojů dat pomocí brány služby Data Factory, nakonfigurujte pro počítač, který odesílá data do Azure synapse Analytics, vhodný rozsah IP adres.

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam sekcí & vlastností dostupných pro definování datových sad naleznete v článku [vytvoření datových sad](data-factory-create-datasets.md) . Oddíly, jako je například struktura, dostupnost a zásada pro datovou sadu JSON, jsou podobné pro všechny typy datových sad (Azure SQL, Azure Blob, tabulka Azure atd.).

Oddíl typeProperties se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Oddíl **typeProperties** pro sadu dat typu **AzureSqlDWTable** má následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| tableName |Název tabulky nebo zobrazení v databázi Azure synapse Analytics, na kterou odkazuje propojená služba |Yes |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam sekcí & vlastností dostupných pro definování aktivit najdete v článku [vytvoření kanálů](data-factory-create-pipelines.md) . Pro všechny typy aktivit jsou k dispozici vlastnosti, jako je název, popis, vstupní a výstupní tabulka a zásada.

> [!NOTE]
> Aktivita kopírování používá pouze jeden vstup a vytváří pouze jeden výstup.

V takovém případě se vlastnosti dostupné v části typeProperties v aktivitě liší podle typu aktivity. U aktivity kopírování se liší v závislosti na typech zdrojů a jímky.

### <a name="sqldwsource"></a>SqlDWSource
Pokud je zdroj typu **SqlDWSource**, jsou v oddílu **typeProperties** k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| sqlReaderQuery |Pomocí vlastního dotazu můžete číst data. |Řetězec dotazu SQL. Příklad: SELECT * FROM MyTable. |No |
| sqlReaderStoredProcedureName |Název uložené procedury, která čte data ze zdrojové tabulky. |Název uložené procedury Poslední příkaz SQL musí být příkaz SELECT v uložené proceduře. |No |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Páry název-hodnota. Názvy a malá písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. |No |

Pokud je pro SqlDWSource zadaný **sqlReaderQuery** , aktivita kopírování spustí tento dotaz proti zdroji Azure synapse Analytics, aby získala data.

Alternativně můžete zadat uloženou proceduru zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters** (Pokud uložená procedura přijímá parametry).

Pokud nezadáte buď sqlReaderQuery nebo sqlReaderStoredProcedureName, použijí se sloupce definované v oddílu struktury JSON datové sady k vytvoření dotazu pro spuštění s Azure synapse Analytics. Příklad: `select column1, column2 from mytable`. Pokud definice datové sady nemá strukturu, všechny sloupce jsou vybrány z tabulky.

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

### <a name="sqldwsink"></a>SqlDWSink
**SqlDWSink** podporuje následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Zadejte dotaz pro aktivitu kopírování, která se má provést, aby se vyčistila data konkrétního řezu. Podrobnosti najdete v části s možností [opakování](#repeatability-during-copy). |Příkaz dotazu. |No |
| allowPolyBase |Označuje, zda použít základ (je-li k dispozici) místo mechanismu BULKINSERT. <br/><br/> **Použití základny je doporučeným způsobem, jak načíst data do služby Azure synapse Analytics.** Omezení a podrobnosti najdete v části [použití základu k načtení dat do služby Azure synapse Analytics](#use-polybase-to-load-data-into-azure-synapse-analytics) . |Pravda <br/>False (výchozí) |No |
| polyBaseSettings |Skupina vlastností, které lze zadat, je-li vlastnost **allowPolybase** nastavena na **hodnotu true**. |&nbsp; |No |
| rejectValue |Určuje počet nebo procento řádků, které lze odmítnout před tím, než se dotaz nezdařil. <br/><br/>Další informace o možnostech odmítnutí základní třídy najdete v části **argumenty** v tématu [vytvoření externí tabulky (Transact-SQL)](/sql/t-sql/statements/create-external-table-transact-sql) . |0 (výchozí), 1, 2,... |No |
| rejectType |Určuje, zda je možnost rejectValue zadána jako hodnota literálu nebo jako procento. |Hodnota (výchozí), procenta |No |
| Rejecttype rejectsamplevalue |Určuje počet řádků, které se mají načíst před tím, než základ přepočítá procento odmítnutých řádků. |1, 2,... |Ano, pokud **rejectType** je **procento** |
| useTypeDefault |Určuje, jak se mají zpracovat chybějící hodnoty v textových souborech s oddělovači, když základ dat načte data z textového souboru.<br/><br/>Přečtěte si další informace o této vlastnosti z oddílu argumenty v tématu [Create External File Format (Transact-SQL)](/sql/t-sql/statements/create-external-file-format-transact-sql). |True, false (výchozí) |No |
| writeBatchSize |Když velikost vyrovnávací paměti dosáhne writeBatchSize, vloží data do tabulky SQL. |Integer (počet řádků) |Ne (výchozí: 10000) |
| writeBatchTimeout |Počkejte, než se operace dávkového vložení dokončí předtím, než vyprší časový limit. |timespan<br/><br/> Příklad: "00:30:00" (30 minut). |No |

#### <a name="sqldwsink-example"></a>Příklad SqlDWSink

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-synapse-analytics"></a>Použití základny k načtení dat do služby Azure synapse Analytics
Použití **[základny](/sql/relational-databases/polybase/polybase-guide)** je účinný způsob, jak načítat velké objemy dat do Azure synapse Analytics s vysokou propustností. Místo výchozího mechanismu BULKINSERT můžete v propustnosti zobrazit velký nárůst využití pomocí základu. Viz téma [kopírování referenčního čísla výkonu](data-factory-copy-activity-performance.md#performance-reference) s detailním porovnáním. Návod s případem použití najdete v tématu [načtení 1 TB do služby Azure synapse Analytics za 15 minut s Azure Data Factory](data-factory-load-sql-data-warehouse.md).

* Pokud jsou vaše zdrojová data v **Azure Blob nebo Azure Data Lake Store** a formát je kompatibilní s základnu, můžete přímo zkopírovat do Azure synapse Analytics pomocí základu. Podrobnosti najdete v tématu **[Přímá kopie pomocí základu](#direct-copy-using-polybase)** .
* Pokud se vaše zdrojové úložiště dat a formát v základu nepodporují, můžete místo toho použít funkci **[dvoufázové kopie pomocí základní](#staged-copy-using-polybase)** funkce. Poskytuje taky lepší propustnost tím, že automaticky převádí data do formátu kompatibilního se standardem a ukládá data do úložiště objektů BLOB v Azure. Poté načte data do služby Azure synapse Analytics.

Nastavte `allowPolyBase` vlastnost na **hodnotu true** , jak je znázorněno v následujícím příkladu, aby bylo možné Azure Data Factory použít základ ke kopírování dat do služby Azure synapse Analytics. Při nastavování allowPolyBase na hodnotu true můžete určit vlastnosti specifické pro základní třídu pomocí `polyBaseSettings` skupiny vlastností. Podrobnosti o vlastnostech, které můžete použít s polyBaseSettings, najdete v části [SqlDWSink](#sqldwsink) .

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

### <a name="direct-copy-using-polybase"></a>Přímá kopie pomocí základu
Základ analýzy Azure synapse Analytics přímo podporuje službu Azure Blob a Azure Data Lake Store (pomocí instančního objektu) jako zdroj a s konkrétními požadavky na formát souboru. Pokud zdrojová data splňují kritéria popsaná v této části, můžete přímo kopírovat ze zdrojového úložiště dat do služby Azure synapse Analytics pomocí základu. V opačném případě můžete použít [dvoufázové kopírování pomocí základu](#staged-copy-using-polybase).

> [!TIP]
> Pokud chcete kopírovat data z Data Lake Store do služby Azure synapse Analytics efektivně, přečtěte si další informace z [Azure Data Factory díky tomu, že při použití Data Lake Store se službou Azure synapse Analytics je ještě jednodušší a pohodlnější získat přehledy z dat](/archive/blogs/azuredatalake/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse).

Pokud nejsou splněny požadavky, Azure Data Factory zkontroluje nastavení a automaticky se vrátí k BULKINSERT mechanismu přesunu dat.

1. **Zdrojová propojená služba** je typu: **AzureStorage** nebo **AzureDataLakeStore s ověřováním instančního objektu**.
2. **Vstupní datová sada** je typu: **azureblobu** nebo **AzureDataLakeStore** a typ formátu v části `type` vlastnosti je **OrcFormat**, **ParquetFormat** nebo **TextFormat** s následujícími konfiguracemi:

   1. `rowDelimiter` musí být **\n**.
   2. `nullValue` je nastaven na **prázdný řetězec** ("") nebo `treatEmptyAsNull` je nastaven na **hodnotu true**.
   3. `encodingName` je nastavená na **UTF-8**, což je **výchozí** hodnota.
   4. `escapeChar`nejsou `quoteChar` `firstRowAsHeader` zadány,, a `skipLineCount` .
   5. `compression` nemůže být **žádná komprese**, **gzip** nebo **Deflate**.

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

3. `skipHeaderLineCount`V části **BlobSource** nebo **AzureDataLakeStore** není žádné nastavení pro aktivitu kopírování v kanálu.
4. `sliceIdentifierColumnName`V rámci **SqlDWSink** neexistuje žádné nastavení pro aktivitu kopírování v kanálu. (Základ základů zaručuje, že všechna data jsou aktualizována nebo dokud není v jednom spuštění aktualizována žádná aktualizace. Chcete-li dosáhnout **opakovatelnosti**, můžete použít `sqlWriterCleanupScript` ).
5. `columnMapping`V přidružené aktivitě kopírování se nepoužívá žádný.

### <a name="staged-copy-using-polybase"></a>Připravené kopírování pomocí základu
Pokud zdrojová data nesplňují kritéria zavedená v předchozí části, můžete kopírovat data prostřednictvím dočasné přípravy Blob Storage Azure (nelze Premium Storage). V takovém případě Azure Data Factory automaticky provádí transformace dat, aby splňovaly požadavky na formát dat základní základny, a pak pomocí základů načítá data do Azure synapse Analytics a v poslední době vyčistila dočasná data z úložiště objektů BLOB. Podrobnosti o tom, jak kopírují data prostřednictvím pracovního objektu Azure Blob, najdete v části [připravené kopírování](data-factory-copy-activity-performance.md#staged-copy) .

> [!NOTE]
> Při kopírování dat z místního úložiště dat do služby Azure synapse Analytics pomocí základu a přípravy, pokud je verze Správa dat brány nižší než 2,4, je na počítači brány vyžadován JRE (Java Runtime Environment), který se používá k transformaci zdrojových dat do správného formátu. Navrhněte, abyste bránu inovovali na nejnovější verzi, abyste se vyhnuli této závislosti.
>

Pokud chcete tuto funkci použít, vytvořte [propojenou službu Azure Storage](data-factory-azure-blob-connector.md#azure-storage-linked-service) , která odkazuje na účet Azure Storage s dočasným úložištěm objektů blob, a pak zadejte `enableStaging` `stagingSettings` vlastnosti a aktivity kopírování, jak je znázorněno v následujícím kódu:

```json
"activities":[
{
    "name": "Sample copy activity from SQL Server to Azure Synapse Analytics via PolyBase",
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

## <a name="best-practices-when-using-polybase"></a>Osvědčené postupy při použití základny
Následující části poskytují další osvědčené postupy pro ty, které jsou uvedené v článku [osvědčené postupy pro Azure synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Požadovaná oprávnění databáze
Aby bylo možné použít základnu, vyžaduje, aby uživatel, který se používá k načtení dat do služby Azure synapse Analytics, měl v cílové databázi [oprávnění Control](/sql/relational-databases/security/permissions-database-engine) . Jedním ze způsobů, jak toho dosáhnout, je přidat tohoto uživatele jako člena role "db_owner". Přečtěte si, jak postupovat podle pokynů v [této části](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limitation"></a>Velikost řádku a omezení datových typů
Základní zatížení jsou omezené na načítání řádků menších než **1 MB** a nelze je načíst do VARCHR (max), nvarchar (max) nebo varbinary (max). Přečtěte si [tady](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Pokud máte zdrojová data s řádky o velikosti větší než 1 MB, možná budete chtít rozdělit zdrojové tabulky vertikálně na několik malých, kde největší velikost řádku každého z nich nepřekračuje limit. Menší tabulky se pak dají načíst pomocí základu a sloučí se společně v Azure synapse Analytics.

### <a name="azure-synapse-analytics-resource-class"></a>Třída prostředků Azure synapse Analytics
Abyste dosáhli nejlepší možné propustnosti, zvažte, jestli přiřadíte větší třídu prostředků uživateli, který se používá k načtení dat do služby Azure synapse Analytics prostřednictvím základu. Přečtěte si, jak postupovat podle následujícího postupu: [Změna třídy prostředků uživatele](../../synapse-analytics/sql-data-warehouse/resource-classes-for-workload-management.md).

### <a name="tablename-in-azure-synapse-analytics"></a>tableName ve službě Azure synapse Analytics
Následující tabulka uvádí příklady, jak zadat vlastnost **TableName** v datové sadě JSON pro různé kombinace schématu a názvu tabulky.

| Schéma databáze | Název tabulky | tableName – vlastnost formátu JSON |
| --- | --- | --- |
| dbo |MyTable |MyTable nebo dbo. MyTable nebo [dbo]. MyTable |
| dbo1 |MyTable |dbo1. MyTable nebo [dbo1]. MyTable |
| dbo |Moje tabulka |[My. Table] nebo [dbo]. [My. Table] |
| dbo1 |Moje tabulka |[dbo1]. [My. Table] |

Pokud se zobrazí následující chyba, může se jednat o problém s hodnotou, kterou jste zadali pro vlastnost tableName. V tabulce najdete správný způsob zadání hodnot pro vlastnost tableName formátu JSON.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Sloupce s výchozími hodnotami
V současné době základní funkce v Data Factory akceptuje pouze stejný počet sloupců jako v cílové tabulce. Řekněme, že máte tabulku se čtyřmi sloupci a jedna z nich je definovaná s výchozí hodnotou. Vstupní data by měla stále obsahovat čtyři sloupce. Poskytnutím vstupní datové sady se třemi sloupci by došlo k chybě podobné této zprávě:

```
All columns of the table must be specified in the INSERT BULK statement.
```
Hodnota NULL je speciální forma výchozí hodnoty. Pokud je sloupec null, vstupní data (v objektu BLOB) pro tento sloupec můžou být prázdná (ve vstupní datové sadě nejde najít). Základ pro vložení do služby Azure synapse Analytics pro tyto hodnoty NULL.

## <a name="auto-table-creation"></a>Vytvoření automatické tabulky
Pokud ke kopírování dat z SQL Server nebo Azure SQL Database do služby Azure synapse Analytics používáte Průvodce kopírováním a tabulka, která odpovídá zdrojové tabulce, v cílovém úložišti neexistuje, Data Factory může automaticky vytvořit tabulku v datovém skladu pomocí schématu zdrojové tabulky.

Data Factory vytvoří tabulku v cílovém úložišti se stejným názvem tabulky ve zdrojovém úložišti dat. Datové typy pro sloupce jsou zvoleny na základě následujícího mapování typů. V případě potřeby provede převody typu, aby se opravily nekompatibility mezi zdrojovým a cílovým úložištěm. Používá taky distribuci tabulek kruhového dotazování.

| Typ zdrojového SQL Databaseového sloupce | Cílový typ sloupce Azure synapse Analytics (omezení velikosti) |
| --- | --- |
| Int | Int |
| BigInt | BigInt |
| SmallInt | SmallInt |
| TinyInt | TinyInt |
| 40bitového | 40bitového |
| Decimal | Decimal |
| Numeric | Decimal |
| Float | Float |
| Peněžní částka | Peněžní částka |
| Skutečné | Skutečné |
| SmallMoney | SmallMoney |
| Binární | Binární |
| Varbinary | Varbinary (až 8000) |
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
| NChar | NChar |
| VarChar | VarChar (až 8000) |
| NVarChar | NVarChar (až 4000) |
| XML | Varchar (až 8000) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../../includes/data-factory-type-repeatability-for-sql-sources.md)]

## <a name="type-mapping-for-azure-synapse-analytics"></a>Mapování typů pro Azure synapse Analytics
Jak je uvedeno v článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , aktivita kopírování provádí automatické převody typů ze zdrojových typů do typů jímky s následujícím přístupem ke dvěma krokům:

1. Převod z nativních zdrojových typů na typ .NET
2. Převést z typu .NET na nativní typ jímky

Když přesunete data do & z Azure synapse Analytics, z typu SQL do typu .NET se použijí následující mapování, a to naopak.

Mapování je stejné jako [SQL Server mapování datových typů pro ADO.NET](/dotnet/framework/data/adonet/sql-server-data-type-mappings).

| Typ databázového stroje SQL Server | Typ rozhraní .NET Framework |
| --- | --- |
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
| sql_variant |Předmětů |
| text |Řetězec, znak [] |
| time |TimeSpan |
| časové razítko |Byte [] |
| tinyint |Byte |
| uniqueidentifier |Identifikátor GUID |
| varbinary |Byte [] |
| varchar |Řetězec, znak [] |
| xml |XML |

Sloupce můžete také namapovat ze zdrojové datové sady na sloupce z datové sady jímky v definici aktivity kopírování. Podrobnosti najdete v tématu [mapování sloupců datové sady v Azure Data Factory](data-factory-map-columns.md).

## <a name="json-examples-for-copying-data-to-and-from-azure-synapse-analytics"></a>Příklady JSON pro kopírování dat do a z Azure synapse Analytics
V následujících příkladech jsou uvedeny ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí sady [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazují, jak kopírovat data z a do Azure synapse Analytics a Azure Blob Storage. Data se ale dají zkopírovat **přímo** z libovolného zdroje do kterékoli z těchto umyvadel, které jsou [tady](data-factory-data-movement-activities.md#supported-data-stores-and-formats) uvedené, pomocí aktivity kopírování v Azure Data Factory.

### <a name="example-copy-data-from-azure-synapse-analytics-to-azure-blob"></a>Příklad: kopírování dat z Azure synapse Analytics do Azure Blob
Ukázka definuje následující Entity Data Factory:

1. Propojená služba typu [AzureSqlDW](#linked-service-properties).
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [AzureSqlDWTable](#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](data-factory-azure-blob-connector.md#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [SqlDWSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka kopíruje data časové řady (každou hodinu, každý den atd.) z tabulky ve službě Azure synapse Analytics Database do objektu BLOB každou hodinu. Vlastnosti JSON použité v těchto ukázkách jsou popsány v oddílech následujících po ukázkách.

**Propojená služba Azure synapse Analytics:**

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
**Propojená služba úložiště objektů BLOB v Azure:**

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
**Vstupní datová sada Azure synapse Analytics:**

Ukázka předpokládá, že jste v Azure synapse Analytics vytvořili tabulku "MyTable" a obsahuje sloupec s názvem "timestampcolumn" pro data časových řad.

Nastavení "externí": "true" informuje službu Data Factory o tom, že datová sada je externí pro objekt pro vytváření dat, a není vytvořena aktivitou v datové továrně.

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

Data se zapisují do nového objektu BLOB každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt BLOB je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá části rok, měsíc, den a hodiny v počátečním čase.

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

Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala vstupní a výstupní datové sady a má naplánované spuštění každou hodinu. V definici JSON kanálu je typ **zdroje** nastavený na **SqlDWSource** a typ **jímky** je nastavený na **BlobSink**. Dotaz SQL zadaný pro vlastnost **SqlReaderQuery** vybere data během uplynulé hodiny ke zkopírování.

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
> V příkladu je **sqlReaderQuery** určena pro SqlDWSource. Aktivita kopírování spustí tento dotaz proti zdroji analýzy Azure synapse, aby získala data.
>
> Alternativně můžete zadat uloženou proceduru zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters** (Pokud uložená procedura přijímá parametry).
>
> Pokud nezadáte buď sqlReaderQuery nebo sqlReaderStoredProcedureName, použijí se sloupce definované v oddílu struktury JSON datové sady k vytvoření dotazu (vyberte Sloupec1, Sloupec2 od myTable), který se spustí s Azure synapse Analytics. Pokud definice datové sady nemá strukturu, všechny sloupce jsou vybrány z tabulky.
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-synapse-analytics"></a>Příklad: kopírování dat z Azure Blob do Azure synapse Analytics
Ukázka definuje následující Entity Data Factory:

1. Propojená služba typu [AzureSqlDW](#linked-service-properties).
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](data-factory-azure-blob-connector.md#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureSqlDWTable](#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) a [SqlDWSink](#copy-activity-properties).

Ukázka kopíruje data časových řad (každou hodinu, každý den atd.) z objektu blob Azure do tabulky v databázi Azure synapse Analytics každou hodinu. Vlastnosti JSON použité v těchto ukázkách jsou popsány v oddílech následujících po ukázkách.

**Propojená služba Azure synapse Analytics:**

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
**Propojená služba úložiště objektů BLOB v Azure:**

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
**Vstupní datová sada Azure Blob:**

Data se vybírají z nového objektu BLOB každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce a název souboru pro objekt BLOB jsou dynamicky vyhodnocovány na základě počátečního času zpracovávaného řezu. Cesta ke složce používá část rok, měsíc a den v čase zahájení a název souboru používá hodinovou část času spuštění. nastavení externí: "true" informuje službu Data Factory o tom, že tato tabulka je pro objekt pro vytváření dat externá a není vytvořená aktivitou v datové továrně.

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
**Výstupní datová sada Azure synapse Analytics:**

Ukázka zkopíruje data do tabulky s názvem "MyTable" ve službě Azure synapse Analytics. Vytvořte tabulku v Azure synapse Analytics se stejným počtem sloupců, jako byste očekávali, že soubor CSV BLOB bude obsahovat. Nové řádky jsou do tabulky přidány každou hodinu.

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

Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala vstupní a výstupní datové sady a má naplánované spuštění každou hodinu. V definici JSON kanálu je typ **zdroje** nastavený na **BlobSource** a typ **jímky** je nastavený na **SqlDWSink**.

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
Návod najdete v tématu [načtení 1 TB do Azure synapse Analytics za 15 minut pomocí Azure Data Factory](data-factory-load-sql-data-warehouse.md) a [načtení dat pomocí Azure Data Factory](../load-azure-sql-data-warehouse.md) článku v dokumentaci k Azure synapse Analytics.

## <a name="performance-and-tuning"></a>Výkon a optimalizace
Další informace o klíčových faktorech, které mají vliv na výkon přesunu dat (aktivita kopírování) v Azure Data Factory a různých způsobech jejich optimalizace, najdete v tématu [Průvodce optimalizací aktivity kopírování &](data-factory-copy-activity-performance.md) .