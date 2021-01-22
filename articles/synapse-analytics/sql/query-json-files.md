---
title: Dotazování souborů JSON pomocí SQL fondu bez serveru
description: V této části se dozvíte, jak číst soubory JSON pomocí neserverového fondu SQL ve službě Azure synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 8dc07a3aa954a74ba594eb99da1ea3ee59610c9b
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678317"
---
# <a name="query-json-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Dotazování souborů JSON pomocí neserverového fondu SQL ve službě Azure synapse Analytics

V tomto článku se dozvíte, jak napsat dotaz pomocí neserverového fondu SQL ve službě Azure synapse Analytics. Cílem dotazu je přečíst soubory JSON pomocí [OpenRowset](develop-openrowset.md). 
- Standardní soubory JSON, kde je více dokumentů JSON uloženo jako pole JSON.
- Soubory JSON s hodnotami oddělenými řádky, kde jsou dokumenty JSON oddělené znakem nového řádku. Společná rozšíření pro tyto typy souborů jsou `jsonl` , `ldjson` a `ndjson` .

## <a name="read-json-documents"></a>Čtení dokumentů JSON

Nejjednodušší způsob, jak zobrazit obsah souboru JSON, je poskytnout adresu URL souboru této `OPENROWSET` funkci, zadat CSV `FORMAT` a nastavit hodnoty `0x0b` pro `fieldterminator` a `fieldquote` . Pokud potřebujete číst soubory JSON s oddělovači řádků, je to pro vás dostačující. Pokud máte klasický soubor JSON, budete muset nastavit hodnoty `0x0b` pro `rowterminator` . `OPENROWSET` funkce bude analyzovat JSON a vracet všechny dokumenty v následujícím formátu:

| přípon |
| --- |
|{"date_rep": "2020-07-24"; "Day": 24; "Month": 7, "Year": 2020, "Cases": 3, "úhyn": 0, "geo_id": "AF"}|
|{"date_rep": "2020-07-25"; "Day": 25; "Month": 7; "Year": 2020, "Cases": 7, "úhyn": 0, "geo_id": "AF"}|
|{"date_rep": "2020-07-26", "Day": 26; "Month": 7; "Year": 2020, "Cases": 4, "úhyn": 0, "geo_id": "AF"}|
|{"date_rep": "2020-07-27", "den": 27, "měsíc": 7, "rok": 2020, "případy": 8, "úhyn": 0, "geo_id": "AF"}|

Pokud je soubor veřejně dostupný, nebo pokud vaše identita Azure AD může získat přístup k tomuto souboru, měli byste vidět obsah tohoto souboru pomocí dotazu, jako je ten, který je uvedený v následujících příkladech.

### <a name="read-json-files"></a>Čtení souborů JSON

Následující vzorový dotaz přečte soubory JSON a oddělené řádky JSON a vrátí každý dokument jako samostatný řádek.

```sql
select top 10 *
from openrowset(
        bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.jsonl',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
go
select top 10 *
from openrowset(
        bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b',
        rowterminator = '0x0b' --> You need to override rowterminator to read classic JSON
    ) with (doc nvarchar(max)) as rows
```

Dokument JSON v předchozím ukázkovém dotazu obsahuje pole objektů. Dotaz vrátí každý objekt jako samostatný řádek v sadě výsledků dotazu. Ujistěte se, že máte přístup k tomuto souboru. Pokud je soubor chráněný pomocí klíče SAS nebo vlastní identity, musíte nastavit [přihlašovací údaje na úrovni serveru pro přihlášení SQL](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential). 

### <a name="data-source-usage"></a>Využití zdroje dat

Předchozí příklad používá úplnou cestu k souboru. Jako alternativu můžete vytvořit externí zdroj dat s umístěním, které odkazuje na kořenovou složku úložiště, a použít tento zdroj dat a relativní cestu k souboru ve `OPENROWSET` funkci:

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.json',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b',
        rowterminator = '0x0b' --> You need to override rowterminator to read classic JSON
    ) with (doc nvarchar(max)) as rows
```

Pokud je zdroj dat chráněný pomocí klíče SAS nebo vlastní identity, můžete nakonfigurovat [zdroj dat s použitím přihlašovacích údajů s rozsahem databáze](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential).

V následujících částech vidíte, jak se dotazovat na různé typy souborů JSON.

## <a name="parse-json-documents"></a>Analyzovat dokumenty JSON

Dotazy v předchozích příkladech vrátí každý dokument JSON jako jeden řetězec v samostatném řádku sady výsledků dotazu. Můžete používat funkce `JSON_VALUE` a `OPENJSON` analyzovat hodnoty v dokumentech JSON a vracet je jako relační hodnoty, jak je znázorněno v následujícím příkladu:

| datum \_ zástupce | věcech | geografické \_ ID |
| --- | --- | --- |
| 2020-07-24 | 3 | AF |
| 2020-07-25 | 7 | AF |
| 2020-07-26 | 4 | AF |
| 2020-07-27 | 8| AF |

### <a name="sample-json-document"></a>Ukázkový dokument JSON

Příklady dotazů čtou soubory *JSON* obsahující dokumenty s následující strukturou:

```json
{
    "date_rep":"2020-07-24",
    "day":24,"month":7,"year":2020,
    "cases":13,"deaths":0,
    "countries_and_territories":"Afghanistan",
    "geo_id":"AF",
    "country_territory_code":"AFG",
    "continent_exp":"Asia",
    "load_date":"2020-07-25 00:05:14",
    "iso_country":"AF"
}
```

> [!NOTE]
> Pokud jsou tyto dokumenty uloženy jako JSON s oddělovači řádků, je nutné nastavit `FIELDTERMINATOR` a `FIELDQUOTE` na 0x0B. Pokud máte standardní formát JSON, musíte nastavit `ROWTERMINATOR` 0x0B.

### <a name="query-json-files-using-json_value"></a>Dotazování souborů JSON pomocí JSON_VALUE

Následující dotaz ukazuje, jak použít [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) k načtení skalárních hodnot (title, Publisher) z dokumentů JSON:

```sql
select
    JSON_VALUE(doc, '$.date_rep') AS date_reported,
    JSON_VALUE(doc, '$.countries_and_territories') AS country,
    JSON_VALUE(doc, '$.cases') as cases,
    doc
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
order by JSON_VALUE(doc, '$.geo_id') desc
```

### <a name="query-json-files-using-openjson"></a>Dotazování souborů JSON pomocí OPENJSON

Následující dotaz používá [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Načte statistiku COVID, která se oznamuje v Srbsku:

```sql
select
    *
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
    cross apply openjson (doc)
        with (  date_rep datetime2,
                cases int,
                fatal int '$.deaths',
                country varchar(100) '$.countries_and_territories')
where country = 'Serbia'
order by country, date_rep desc;
```

## <a name="next-steps"></a>Další kroky

Další články v této sérii vám poukazují, jak:

- [Dotazování na složky a více souborů](query-folders-multiple-csv-files.md)
- [Vytváření a používání zobrazení](create-use-views.md)
