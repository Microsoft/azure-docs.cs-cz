---
title: Dotazování na vnořené typy Parquet pomocí SQL na vyžádání (Preview)
description: V tomto článku se naučíte, jak zadávat dotazy na vnořené typy Parquet.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 60f2a1992750950b50995fcf36513e44e377004d
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386601"
---
# <a name="query-nested-types-in-parquet-and-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Dotazování vnořených typů v souborech Parquet a JSON pomocí SQL na vyžádání (Preview) ve službě Azure synapse Analytics

V tomto článku se dozvíte, jak napsat dotaz pomocí SQL na vyžádání (ve verzi Preview) ve službě Azure synapse Analytics. Tento dotaz načte vnořené typy Parquet.
Vnořené typy jsou komplexní struktury, které představují objekty nebo pole. Vnořené typy mohou být uloženy v: 
- [PARQUET](query-parquet-files.md) , kde můžete mít více složitých sloupců obsahujících pole a objekty.
- Hierarchické [soubory JSON](query-json-files.md) , kde můžete číst složité dokumenty JSON jako jeden sloupec.
- Kolekce CosmosDB, kde každý dokument může obsahovat komplexní vnořené vlastnosti (aktuálně v rámci ověřované verze Public Preview).

Synapse SQL na vyžádání formátuje všechny vnořené typy jako objekty JSON a pole, takže můžete [extrahovat nebo upravovat složité objekty pomocí funkcí JSON](https://docs.microsoft.com/sql/relational-databases/json/validate-query-and-change-json-data-with-built-in-functions-sql-server) nebo [analyzovat data JSON pomocí funkce OPENJSON](https://docs.microsoft.com/sql/relational-databases/json/convert-json-data-to-rows-and-columns-with-openjson-sql-server). 

Následuje příklad dotazu, který extrahuje hodnoty skalárních hodnot a objektů z COVID. soubor JSON s [otevřenými datovou sadou dat](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) JSON se zobrazuje níže. 

```sql
SELECT
    title = JSON_VALUE(doc, '$.metadata.title'),
    first_author = JSON_QUERY(doc, '$.metadata.authors[0]'),
    first_author_name = JSON_VALUE(doc, '$.metadata.authors[0].first'),
    complex_object = doc
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
        FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b'
    )
    WITH ( doc varchar(MAX) ) AS docs;
```

`JSON_VALUE`funkce vrací skalární hodnotu z pole v zadané cestě. `JSON_QUERY`funkce vrátí objekt formátovaný jako JSON z pole v zadané cestě.

> [!IMPORTANT]
> V tomto příkladu se používá soubor z [COVID-19 Open Research DataSet](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/). Viz licence na tuto a struktura dat na této stránce.

## <a name="prerequisites"></a>Předpoklady

Prvním krokem je **Vytvoření databáze** se zdrojem dat, který odkazuje na. Pak inicializujte objekty spuštěním [instalačního skriptu](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) v této databázi. Tento instalační skript vytvoří zdroje dat, přihlašovací údaje v oboru databáze a formáty externích souborů, které jsou použity v těchto ukázkách.

## <a name="project-nested-or-repeated-data"></a>Vnořená nebo opakující se data projektu

Soubor PARQUET může mít více sloupců se složitými typy. Hodnoty z těchto sloupců jsou formátovány jako text JSON a jsou vráceny jako sloupec VARCHAR. Následující dotaz přečte soubor *structExample. Parquet* a ukazuje, jak číst hodnoty vnořených sloupců: 

```sql
SELECT
    DateStruct, TimeStruct, TimestampStruct, DecimalStruct, FloatStruct
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        DateStruct VARCHAR(8000),
        TimeStruct VARCHAR(8000),
        TimestampStruct VARCHAR(8000),
        DecimalStruct VARCHAR(8000),
        FloatStruct VARCHAR(8000)
    ) AS [r];
```

Tento dotaz vrátí následující výsledek, ve kterém se vrátí obsah každého vnořeného objektu jako text JSON:

| DateStruct    | TimeStruct    | TimestampStruct   | DecimalStruct | FloatStruct |
| --- | --- | --- | --- | --- |
|{"Date": "2009-04-25"}| {"Time": "20:51:54.3598000"}|    {"Timestamp": "5501-04-08 12:13:57.4821000"}|    {"Decimal": 11143412.25350}| {"Float": 0.5}|
|{"Date": "1916-04-29"}| {"Time": "00:16:04.6778000"}|    {"Timestamp": "1990-06-30 20:50:52.6828000"}|    {"Decimal": 1963545.62800}|  {"Float":-2,125}|

Následující dotaz přečte soubor *justSimpleArray. Parquet* . Projekty IT jsou všechny sloupce ze souboru Parquet, včetně vnořených nebo opakovaných dat.

```sql
SELECT
    SimpleArray
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Tento dotaz vrátí následující výsledek:

|SimpleArray|
| --- |
|[11, 12, 13]|
|[21, 22, 23]|

## <a name="read-properties-from-nested-object-columns"></a>Načíst vlastnosti ze sloupců vnořených objektů

`JSON_VALUE`funkce umožňuje vracet hodnoty ze sloupce formátovaného jako text JSON:

```sql
SELECT
    title = JSON_VALUE(complex_column, '$.metadata.title'),
    first_author_name = JSON_VALUE(complex_column, '$.metadata.authors[0].first'),
    body_text = JSON_VALUE(complex_column, '$.body_text.text'),
    complex_column
FROM
    OPENROWSET( BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
                FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b' ) WITH ( complex_column varchar(MAX) ) AS docs;
```

Výsledek je zobrazen v následující tabulce:

|title  | first_author_name | body_text | complex_column |
| --- | --- | --- | --- |
| Doplňující informace epidemiolo... | Julien   | Obrázek S1: Phylogeny... | `{    "paper_id": "000b7d1517ceebb34e1e3e817695b6de03e2fa78",    "metadata": {        "title": "Supplementary Information An eco-epidemiological study of Morbilli-related paramyxovirus infection in Madagascar bats reveals host-switching as the dominant macro-evolutionary mechanism",        "authors": [            {                "first": "Julien"` |

Na rozdíl od souborů JSON, ve většině případů vrací jeden sloupec obsahující komplexní objekt JSON. Soubory PARQUET můžou mít víc složitých. Vlastnosti vnořeného sloupce můžete číst pomocí `JSON_VALUE` funkce v každém sloupci. `OPENROWSET`umožňuje přímo zadat cesty vnořených vlastností v `WITH` klauzuli. Cesty lze nastavit jako název sloupce nebo můžete přidat [výraz cesty JSON](https://docs.microsoft.com/sql/relational-databases/json/json-path-expressions-sql-server) za typ sloupce.

Následující dotaz přečte soubor *structExample. Parquet* a ukazuje, jak surfovat prvky vnořeného sloupce. Existují dva způsoby, jak odkazovat na vnořenou hodnotu:
- Určení výrazu cesty vnořené hodnoty po specifikaci typu.
- Formátování názvu sloupce jako vnořené cesty pomocí příkazu do "." odkazuje na pole.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        [DateValue] DATE '$.DateStruct.Date',
        [TimeStruct.Time] TIME,
        [TimestampStruct.Timestamp] DATETIME2,
        DecimalValue DECIMAL(18, 5) '$.DecimalStruct.Decimal',
        [FloatStruct.Float] FLOAT
    ) AS [r];
```

## <a name="access-elements-from-repeated-columns"></a>Přístup k prvkům z opakujících se sloupců

Následující dotaz přečte soubor *justSimpleArray. Parquet* a používá [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) k načtení **skalárního** prvku v rámci opakujícího se sloupce, jako je například pole nebo mapa:

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Výsledek je zobrazen v následující tabulce:

|SimpleArray    | FirstElement  | SecondElement | ThirdElement |
| --- | --- | --- | --- |
| [11, 12, 13] | 11   | 12 | 13 |
| [21, 22, 23] | 21   | 22 | 23 |

## <a name="access-sub-objects-from-complex-columns"></a>Přístup k dílčím objektům ze složitých sloupců

Následující dotaz přečte soubor *mapExample. Parquet* a používá [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) k načtení **neskalárního** prvku z opakujícího se sloupce, jako je například pole nebo mapa:

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Můžete také explicitně odkazovat na sloupce, které chcete vrátit v `WITH` klauzuli:

```sql
SELECT DocId,
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) 
    WITH (DocId bigint, MapOfPersons VARCHAR(max)) AS [r];
```

Struktura `MapOfPersons` se vrátí jako `VARCHAR` sloupec a naformátuje jako řetězec JSON.

## <a name="projecting-values-from-repeated-columns"></a>Projekce hodnot z opakujících se sloupců

Pokud máte pole skalárních hodnot (například `[1,2,3]` ) v některých sloupcích, můžete je snadno rozšířit a připojit se k hlavnímu řádku pomocí následujícího skriptu:

```sql
SELECT
    SimpleArray, Element
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS arrays
    CROSS APPLY OPENJSON (SimpleArray) WITH (Element int '$') as array_values
```

## <a name="next-steps"></a>Další kroky

V dalším článku se dozvíte, jak [zadávat dotazy na soubory JSON](query-json-files.md).
