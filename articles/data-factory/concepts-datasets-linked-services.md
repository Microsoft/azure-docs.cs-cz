---
title: Datové sady v Azure Data Factory | Microsoft Docs
description: Seznamte se s datovými sadami v Data Factory. Datové sady reprezentují vstupní a výstupní data.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: 788fee724f381ab317b97a682aa21d17ec1ffa9d
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70137297"
---
# <a name="datasets-in-azure-data-factory"></a>Datové sady v Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-create-datasets.md)
> * [Aktuální verze](concepts-datasets-linked-services.md)

Tento článek popisuje, jaké jsou datové sady, jak jsou definovány ve formátu JSON a jak se používají v Azure Data Factorych kanálech.

Pokud s Data Factory začínáte, přečtěte si téma [Úvod do Azure Data Factory](introduction.md) pro přehled.

## <a name="overview"></a>Přehled
Objekt pro vytváření dat může mít jeden nebo víc kanálů. **Kanál** je logické seskupení **aktivit** , které dohromady provádějí úlohu. Aktivity v kanálu definují akce, které se mají provést s vašimi daty. Nyní je **datovou sadou** pojmenované zobrazení dat, která jednoduše odkazují na data, která chcete ve svých aktivitách použít jako vstupy a výstupy. Datové sady identifikují data v rámci různých úložišť dat, jako jsou tabulky, soubory, složky a dokumenty. Datová sada objektu blob Azure například určuje kontejner objektů blob a složku v úložišti objektů blob, ze kterých by měla aktivita číst data.

Před vytvořením datové sady je nutné vytvořit propojenou [**službu**](concepts-linked-services.md) , která propojí úložiště dat s datovou továrnou. Propojené služby jsou velmi podobné připojovacím řetězcům, které definují informace o připojení, které služba Data Factory potřebuje pro připojení k externím prostředkům. Zamyslete se nad tímto způsobem; datová sada představuje strukturu dat v propojených úložištích dat a propojená služba definuje připojení ke zdroji dat. Například propojená služba Azure Storage propojuje účet úložiště s datovou továrnou. Datová sada objektů BLOB v Azure představuje kontejner objektů BLOB a složku v rámci tohoto účtu úložiště Azure, která obsahuje vstupní objekty blob, které se mají zpracovat.

Tady je ukázkový scénář. Pokud chcete kopírovat data z úložiště objektů blob do databáze SQL, vytvoříte dvě propojené služby: Azure Storage a Azure SQL Database. Pak vytvořte dvě datové sady: Datová sada objektu blob Azure (která odkazuje na propojenou službu Azure Storage) a datovou sadu tabulky Azure SQL (která odkazuje na propojenou službu Azure SQL Database). Azure Storage a Azure SQL Database propojené služby obsahují připojovací řetězce, které Data Factory používá za běhu, aby se připojily k vašemu Azure Storage a Azure SQL Database. Datová sada Azure Blob určuje kontejner objektů BLOB a složku objektů blob, které obsahují vstupní objekty BLOB v úložišti objektů BLOB. Datová sada tabulky SQL Azure Určuje tabulku SQL ve vaší databázi SQL, do které se zkopírují data.

Následující diagram znázorňuje vztahy mezi kanálem, aktivitou, datovou sadou a propojenou službou v Data Factory:

![Vztah mezi kanálem, aktivitou, datovou sadou, propojenými službami](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>Datová sada – JSON
Datová sada v Data Factory je definována v následujícím formátu JSON:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```
Následující tabulka obsahuje popis vlastností ve výše uvedeném formátu JSON:

Vlastnost | Popis | Požadováno |
-------- | ----------- | -------- |
name | Název datové sady Viz [pravidla pro Pojmenovávání Azure Data Factory](naming-rules.md). |  Ano |
type | Typ datové sady Zadejte jeden z typů, které podporuje Data Factory (například: Azureblobu, AzureSqlTable). <br/><br/>Podrobnosti najdete v tématu [typy datových sad](#dataset-type). | Ano |
structure | Schéma datové sady Podrobnosti najdete v tématu [schéma datové sady](#dataset-structure-or-schema). | Ne |
typeProperties | Vlastnosti typu se pro každý typ liší (například: Azure Blob, tabulka SQL Azure) Podrobnosti o podporovaných typech a jejich vlastnostech naleznete v tématu [Typ datové sady](#dataset-type). | Ano |

### <a name="data-flow-compatible-dataset"></a>Datová sada kompatibilní s datovým proudem

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Seznam typů datových sad, které jsou kompatibilní s [datovým proudem](concepts-data-flow-overview.md) , najdete v tématu [podporované typy datových sad](#dataset-type) . Datové sady, které jsou kompatibilní s datovým tokem, vyžadují pro transformace jemně odstupňované definice datových sad. Definice JSON se tedy mírně liší. Místo vlastnosti _struktury_ mají datové sady, které jsou kompatibilní s datovým proudem, vlastnost _schématu_ .

V toku dat se datové sady používají při transformaci zdroje a jímky. Datové sady definují základní schémata dat. Pokud vaše data neobsahují žádné schéma, můžete pro zdroj a jímku použít posun schématu. Schéma v datové sadě představuje fyzický datový typ a tvar.

Definováním schématu z datové sady získáte související datové typy, formáty dat, umístění souborů a informace o připojení z přidružené propojené služby. Metadata z datových sad se zobrazí ve zdrojové transformaci jako zdrojová *projekce*. Projekce ve zdrojové transformaci představuje data toku dat s definovanými názvy a typy.

Po importu schématu datové sady toku dat vyberte tlačítko **importovat schéma** a zvolte Import ze zdroje nebo z místního souboru. Ve většině případů importujete schéma přímo ze zdroje. Pokud již máte místní soubor schématu (soubor Parquet nebo CSV s hlavičkou), můžete Data Factory nasměrovat na základní schéma tohoto souboru.


```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "schema": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```

Následující tabulka obsahuje popis vlastností ve výše uvedeném formátu JSON:

Vlastnost | Popis | Požadováno |
-------- | ----------- | -------- |
name | Název datové sady Viz [pravidla pro Pojmenovávání Azure Data Factory](naming-rules.md). |  Ano |
type | Typ datové sady Zadejte jeden z typů, které podporuje Data Factory (například: Azureblobu, AzureSqlTable). <br/><br/>Podrobnosti najdete v tématu [typy datových sad](#dataset-type). | Ano |
schema | Schéma datové sady Podrobnosti najdete v tématu [datové sady kompatibilní](#dataset-type)s datovým proudem. | Ne |
typeProperties | Vlastnosti typu se pro každý typ liší (například: Azure Blob, tabulka SQL Azure) Podrobnosti o podporovaných typech a jejich vlastnostech naleznete v tématu [Typ datové sady](#dataset-type). | Ano |


## <a name="dataset-example"></a>Příklad datové sady
V následujícím příkladu datová sada představuje tabulku s názvem MyTable v databázi SQL.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": {
                "referenceName": "MyAzureSqlLinkedService",
                "type": "LinkedServiceReference",
        },
        "typeProperties":
        {
            "tableName": "MyTable"
        },
    }
}

```
Je třeba počítat s následujícím:

- typ je nastavený na AzureSqlTable.
- vlastnost typu tableName (specifická pro typ AzureSqlTable) je nastavená na MyTable.
- linkedServiceName odkazuje na propojenou službu typu AzureSqlDatabase, která je definována v následujícím fragmentu kódu JSON.

## <a name="dataset-type"></a>Typ datové sady
V závislosti na používaném úložišti dat existuje mnoho různých typů datových sad. Seznam dat, která podporuje Data Factory, najdete v článku [Přehled konektoru](connector-overview.md) . Kliknutím na úložiště dat se dozvíte, jak vytvořit propojenou službu a datovou sadu pro toto úložiště dat.

V příkladu v předchozí části je typ datové sady nastavený na **AzureSqlTable**. Podobně pro datovou sadu objektů BLOB v Azure je typ datové sady nastavený na **azureblobu**, jak je znázorněno v následujícím kódu JSON:

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
                "referenceName": "MyAzureStorageLinkedService",
                "type": "LinkedServiceReference",
        },

        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        }
    }
}
```

## <a name="dataset-structure-or-schema"></a>Struktura nebo schéma datové sady
Datové sady oddílu **struktury** nebo **schématu** (kompatibilní s tokem dat) jsou volitelné. Definuje schéma datové sady obsahující kolekci názvů a datových typů sloupců. Oddíl Structure (struktura) slouží k poskytnutí informací o typu, které slouží k převodu typů a mapování sloupců ze zdroje do cíle.

Každý sloupec ve struktuře obsahuje následující vlastnosti:

Vlastnost | Popis | Požadováno
-------- | ----------- | --------
name | Název sloupce | Ano
type | Datový typ sloupce Data Factory podporuje následující dočasné datové typy jako povolené hodnoty: **Int16, Int32, Int64, Single, Double, Decimal, Byte [], Boolean, String, GUID, DateTime, DateTimeOffset a TimeSpan** | Ne
culture | . Jazyková verze založená na síti, která se má použít, pokud je typem typ `Datetime` .NET `Datetimeoffset`: nebo. Výchozí hodnota je `en-us`. | Ne
format | Řetězec formátu, který se má použít, pokud je typem typ .NET `Datetime` : `Datetimeoffset`nebo. Informace o formátování hodnoty DateTime naleznete v tématu [Vlastní řetězce formátu data a času](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) . | Ne

### <a name="example"></a>Příklad
V následujícím příkladu Předpokládejme, že zdrojová data objektů BLOB jsou ve formátu CSV a obsahují tři sloupce: UserID, Name a LastLoginDate. Jsou typu Int64, String a DateTime s vlastním formátem DateTime pomocí zkrácených francouzských názvů pro den v týdnu.

Určete strukturu datové sady objektů BLOB následovně a s definicemi typů pro sloupce:

```json
"structure":
[
    { "name": "userid", "type": "Int64"},
    { "name": "name", "type": "String"},
    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
]
```

### <a name="guidance"></a>Doprovodné materiály

Následující pokyny vám pomohou pochopit, kdy zahrnout informace o struktuře a co zahrnout do části **Struktura** . Další informace o tom, jak Data Factory mapuje zdrojová data do jímky a kdy specifikovat informace o struktuře ze [schématu a mapování typů](copy-activity-schema-and-type-mapping.md).

- **U zdrojů dat**se silným schématem určete oddíl struktura pouze v případě, že chcete mapovat zdrojové sloupce na sloupce jímky a jejich názvy nejsou stejné. Tento druh strukturovaného zdroje dat ukládá schéma dat a informace o typech spolu s samotnými daty. Příklady strukturovaných zdrojů dat zahrnují SQL Server, Oracle a Azure SQL Database.<br/><br/>Jelikož informace o typu jsou již k dispozici pro strukturované zdroje dat, neměli byste při zahrnutí oddílu struktury zahrnovat informace o typu.
- **V případě nebezpečných zdrojů dat schématu, například textových souborů v úložišti objektů BLOB**, zahrňte strukturu, pokud je datová sada vstupem pro aktivitu kopírování, a datové typy zdrojové datové sady by měly být převedeny na nativní typy jímky. A zahrnují strukturu, pokud chcete namapovat zdrojové sloupce na sloupce jímky.

## <a name="create-datasets"></a>Vytvoření datových sad
Datové sady můžete vytvořit pomocí jednoho z těchto nástrojů nebo sad SDK: [.NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [REST API](quickstart-create-data-factory-rest-api.md), Azure Resource Manager Template a Azure Portal

## <a name="current-version-vs-version-1-datasets"></a>Aktuální verze a datové sady verze 1

Tady je několik rozdílů mezi Data Factory a datovými sadami Data Factory verze 1:

- Vlastnost External není v aktuální verzi podporována. Je nahrazen triggerem. [](concepts-pipeline-execution-triggers.md)
- Vlastnosti zásad a dostupnosti nejsou v aktuální verzi podporované. Čas spuštění kanálu závisí na triggerech. [](concepts-pipeline-execution-triggers.md)
- V aktuální verzi nejsou podporovány datové sady s vymezeným oborem (datové sady definované v kanálu).

## <a name="next-steps"></a>Další postup
Podrobné pokyny k vytváření kanálů a datových sad pomocí jednoho z těchto nástrojů nebo sad SDK najdete v následujícím kurzu.

- [Rychlý start: Vytvoření datové továrny pomocí rozhraní .NET](quickstart-create-data-factory-dot-net.md)
- [Rychlý Start: vytvoření datové továrny pomocí PowerShellu](quickstart-create-data-factory-powershell.md)
- [Rychlý Start: vytvoření datové továrny pomocí REST API](quickstart-create-data-factory-rest-api.md)
- [Rychlý Start: vytvoření datové továrny pomocí Azure Portal](quickstart-create-data-factory-portal.md)
