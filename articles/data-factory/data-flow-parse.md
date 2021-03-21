---
title: Analýza transformace dat v toku mapování dat
description: Analyzovat vložené dokumenty sloupců
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 4db9503ea84ae13148a89a03048c73399413e5cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101710188"
---
# <a name="parse-transformation-in-mapping-data-flow"></a>Analyzovat transformaci v toku dat mapování

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Použijte transformaci Parse k analýze sloupců ve vašich datech, která jsou ve formuláři dokumentu. Aktuální podporované typy vložených dokumentů, které lze analyzovat, jsou JSON a text s oddělovači.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWykdO]

## <a name="configuration"></a>Konfigurace

Na panelu Konfigurace transformace transformace nejprve vyberte typ dat obsažený ve sloupcích, které chcete analyzovat jako vložené. Transformace analýzy obsahuje také následující nastavení konfigurace.

![Nastavení analýzy](media/data-flow/data-flow-parse-1.png "Analyzovat")

### <a name="column"></a>Sloupec

Podobně jako u odvozených sloupců a agregací je to místo, kde můžete buď upravit opuštění sloupce tak, že ho vyberete v rozevíracím seznamu pro výběr. Můžete také zadat název nového sloupce. ADF bude ukládat analyzovaná zdrojová data do tohoto sloupce.

### <a name="expression"></a>Výraz

Použijte Tvůrce výrazů k nastavení zdroje pro analýzu. To může být jednoduché, protože stačí vybrat zdrojový sloupec se samostatnými daty, která chcete analyzovat, nebo můžete vytvořit složité výrazy k analýze.

### <a name="output-column-type"></a>Typ výstupního sloupce

Tady je místo, kde budete konfigurovat cílové výstupní schéma z analýzy, která se zapíše do jednoho sloupce.

![Příklad analýzy](media/data-flow/data-flow-parse-2.png "Příklad analýzy")

V tomto příkladu jsme definovali analýzu příchozích polí "jsonString", což je prostý text, ale formátovaná jako struktura JSON. Analyzovat výsledky jako JSON do nového sloupce s názvem "JSON" s tímto schématem:

```(trade as boolean, customers as string[])```

Pokud chcete ověřit, že je váš výstup správně mapovaný, přečtěte si kartu Kontrola a náhled dat.

## <a name="examples"></a>Příklady

```
source(output(
        name as string,
        location as string,
        satellites as string[],
        goods as (trade as boolean, customers as string[], orders as (orderId as string, orderTotal as double, shipped as (orderItems as (itemName as string, itemQty as string)[]))[])
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false,
    documentForm: 'documentPerLine') ~> JsonSource
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false) ~> CsvSource
JsonSource derive(jsonString = toString(goods)) ~> StringifyJson
StringifyJson parse(json = jsonString ? (trade as boolean,
        customers as string[]),
    format: 'json',
    documentForm: 'arrayOfDocuments') ~> ParseJson
CsvSource derive(csvString = 'Id|name|year\n\'1\'|\'test1\'|\'1999\'') ~> CsvString
CsvString parse(csv = csvString ? (id as integer,
        name as string,
        year as string),
    format: 'delimited',
    columnNamesAsHeader: true,
    columnDelimiter: '|',
    nullValue: '',
    documentForm: 'documentPerLine') ~> ParseCsv
ParseJson select(mapColumn(
        jsonString,
        json
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> KeepStringAndParsedJson
ParseCsv select(mapColumn(
        csvString,
        csv
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> KeepStringAndParsedCsv
```

## <a name="data-flow-script"></a>Skript toku dat

### <a name="syntax"></a>Syntax

### <a name="examples"></a>Příklady

```
parse(json = jsonString ? (trade as boolean,
                                customers as string[]),
                format: 'json',
                documentForm: 'singleDocument') ~> ParseJson

parse(csv = csvString ? (id as integer,
                                name as string,
                                year as string),
                format: 'delimited',
                columnNamesAsHeader: true,
                columnDelimiter: '|',
                nullValue: '',
                documentForm: 'documentPerLine') ~> ParseCsv
```    

## <a name="next-steps"></a>Další kroky

* K pivotování řádků na sloupce použijte [transformaci s sloučením](data-flow-flatten.md) .
* Použijte [transformaci odvozeného sloupce](data-flow-derived-column.md) pro kontingenční sloupce do řádků.
