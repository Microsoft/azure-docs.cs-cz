---
title: Datové sady
description: Seznamte se s datovými sadami v Data Factory. Datové sady reprezentují vstupní a výstupní data.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/24/2020
ms.openlocfilehash: 4dafb738fd7d44893705994baa962831c101804b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390024"
---
# <a name="datasets-in-azure-data-factory"></a>Datové sady v Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-create-datasets.md)
> * [Aktuální verze](concepts-datasets-linked-services.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


Tento článek popisuje, jaké jsou datové sady, jak jsou definovány ve formátu JSON a jak se používají v Azure Data Factorych kanálech.

Pokud s Data Factory začínáte, přečtěte si téma [Úvod do Azure Data Factory](introduction.md) pro přehled.

## <a name="overview"></a>Přehled
Objekt pro vytváření dat může mít jeden nebo víc kanálů. **Kanál** je logické seskupení **aktivit** , které dohromady provádějí úlohu. Aktivity v kanálu definují akce, které se mají provádět s daty. Nyní je **datovou sadou** pojmenované zobrazení dat, která jednoduše odkazují na data, která chcete ve svých **aktivitách** použít jako vstupy a výstupy. Datové sady identifikují data v rámci různých úložišť dat, jako jsou tabulky, soubory, složky a dokumenty. Datová sada objektu blob Azure například určuje kontejner objektů blob a složku v úložišti objektů blob, ze kterých by měla aktivita číst data.

Před vytvořením datové sady je nutné vytvořit [**propojenou službu**](concepts-linked-services.md) , která propojí úložiště dat s datovou továrnou. Propojené služby jsou velmi podobné připojovacím řetězcům, které definují informace o připojení, které služba Data Factory potřebuje pro připojení k externím prostředkům. Zamyslete se nad tímto způsobem; datová sada představuje strukturu dat v propojených úložištích dat a propojená služba definuje připojení ke zdroji dat. Například propojená služba Azure Storage propojuje účet úložiště s datovou továrnou. Datová sada objektů BLOB v Azure představuje kontejner objektů BLOB a složku v rámci tohoto Azure Storage účtu, která obsahuje vstupní objekty blob, které se mají zpracovat.

Tady je ukázkový scénář. Pokud chcete kopírovat data z úložiště objektů blob do SQL Database, vytvoříte dvě propojené služby: Azure Blob Storage a Azure SQL Database. Pak vytvořte dvě datové sady: textovou datovou sadu s oddělovači (která odkazuje na propojenou službu Azure Blob Storage za předpokladu, že máte textové soubory jako zdroj) a datovou sadu Azure SQL Table (která odkazuje na Azure SQL Database propojenou službu). Propojené služby Azure Blob Storage a Azure SQL Database obsahují připojovací řetězce, které Data Factory používá za běhu, aby se připojily k vašim Azure Storagem a Azure SQL Databasem v uvedeném pořadí. Datová sada s oddělovači určuje kontejner objektů BLOB a složku objektů blob, které obsahují vstupní objekty blob ve službě BLOB Storage, spolu s nastaveními týkajícími se formátu. Datová sada tabulky SQL Azure Určuje tabulku SQL ve vašem SQL Database, do které se mají kopírovat data.

Následující diagram znázorňuje vztahy mezi kanálem, aktivitou, datovou sadou a propojenou službou v Data Factory:

![Vztah mezi kanálem, aktivitou, datovou sadou, propojenými službami](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>Datová sada – JSON
Datová sada v Data Factory je definována v následujícím formátu JSON:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: DelimitedText, AzureSqlTable etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "schema":[

        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```
Následující tabulka obsahuje popis vlastností ve výše uvedeném formátu JSON:

Vlastnost | Popis | Vyžadováno |
-------- | ----------- | -------- |
name | Název datové sady Viz [pravidla pro Pojmenovávání Azure Data Factory](naming-rules.md). |  Yes |
typ | Typ datové sady Zadejte jeden z typů, které podporuje Data Factory (například: DelimitedText, AzureSqlTable). <br/><br/>Podrobnosti najdete v tématu [typy datových sad](#dataset-type). | Yes |
schema | Schéma datové sady představuje fyzický datový typ a tvar. | No |
typeProperties | Vlastnosti typu jsou pro každý typ odlišné. Podrobnosti o podporovaných typech a jejich vlastnostech naleznete v tématu [Typ datové sady](#dataset-type). | Yes |

Po importu schématu datové sady vyberte tlačítko **importovat schéma** a zvolte Import ze zdroje nebo z místního souboru. Ve většině případů importujete schéma přímo ze zdroje. Pokud již máte místní soubor schématu (soubor Parquet nebo CSV s hlavičkou), můžete Data Factory nasměrovat na základní schéma tohoto souboru.

V aktivitě kopírování jsou datové sady použity ve zdroji a jímky. Schéma definované v datové sadě je volitelné jako reference. Pokud chcete použít mapování sloupce nebo pole mezi zdrojem a jímkou, přečtěte si [schéma a mapování typů](copy-activity-schema-and-type-mapping.md).

V toku dat se datové sady používají při transformaci zdroje a jímky. Datové sady definují základní schémata dat. Pokud vaše data neobsahují žádné schéma, můžete pro zdroj a jímku použít posun schématu. Metadata z datových sad se zobrazí ve zdrojové transformaci jako zdrojová projekce. Projekce ve zdrojové transformaci představuje data toku dat s definovanými názvy a typy.

## <a name="dataset-type"></a>Typ datové sady

Azure Data Factory podporuje mnoho různých typů datových sad v závislosti na úložištích dat, která používáte. Seznam úložišť dat podporovaných nástrojem Data Factory najdete v článku [Přehled konektoru](connector-overview.md) . Kliknutím na úložiště dat se dozvíte, jak vytvořit propojenou službu a datovou sadu pro ni.

Například pro objektovou datovou sadu s oddělovači je typ datové sady nastavený na **DelimitedText** , jak je znázorněno v následující ukázce JSON:

```json
{
    "name": "DelimitedTextInput",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorage",
            "type": "LinkedServiceReference"
        },
        "annotations": [],
        "type": "DelimitedText",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": "input.log",
                "folderPath": "inputdata",
                "container": "adfgetstarted"
            },
            "columnDelimiter": ",",
            "escapeChar": "\\",
            "quoteChar": "\""
        },
        "schema": []
    }
}
```

## <a name="create-datasets"></a>Vytvoření datových sad
Datové sady můžete vytvořit pomocí jednoho z těchto nástrojů nebo sad SDK: [.NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [REST API](quickstart-create-data-factory-rest-api.md), Azure Resource Manager Template a Azure Portal

## <a name="current-version-vs-version-1-datasets"></a>Aktuální verze a datové sady verze 1

Tady je několik rozdílů mezi Data Factory a datovými sadami Data Factory verze 1:

- Vlastnost External není v aktuální verzi podporována. Je nahrazen [triggerem](concepts-pipeline-execution-triggers.md).
- Vlastnosti zásad a dostupnosti nejsou v aktuální verzi podporované. Čas spuštění kanálu závisí na [triggerech](concepts-pipeline-execution-triggers.md).
- V aktuální verzi nejsou podporovány datové sady s vymezeným oborem (datové sady definované v kanálu).

## <a name="next-steps"></a>Další kroky
Podrobné pokyny k vytváření kanálů a datových sad pomocí jednoho z těchto nástrojů nebo sad SDK najdete v následujícím kurzu.

- [Rychlý start: Vytvoření datové továrny pomocí rozhraní .NET](quickstart-create-data-factory-dot-net.md)
- [Rychlý Start: vytvoření datové továrny pomocí PowerShellu](quickstart-create-data-factory-powershell.md)
- [Rychlý Start: vytvoření datové továrny pomocí REST API](quickstart-create-data-factory-rest-api.md)
- [Rychlý Start: vytvoření datové továrny pomocí Azure Portal](quickstart-create-data-factory-portal.md)
