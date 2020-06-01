---
title: Dotazování na vnořené typy Parquet pomocí SQL na vyžádání (Preview)
description: V tomto článku se naučíte, jak zadávat dotazy na vnořené typy Parquet.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: d5a10e3fe2803c7b9a10abe9bf959a694030cc8c
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235436"
---
# <a name="query-parquet-nested-types-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Dotazy Parquet vnořené typy pomocí SQL na vyžádání (Preview) ve službě Azure synapse Analytics

V tomto článku se dozvíte, jak napsat dotaz pomocí SQL na vyžádání (ve verzi Preview) ve službě Azure synapse Analytics.  Tento dotaz načte vnořené typy Parquet.

## <a name="prerequisites"></a>Požadavky

Prvním krokem je **Vytvoření databáze** se zdrojem dat, který odkazuje na. Pak inicializujte objekty spuštěním [instalačního skriptu](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) v této databázi. Tento instalační skript vytvoří zdroje dat, přihlašovací údaje v oboru databáze a formáty externích souborů, které jsou použity v těchto ukázkách.

## <a name="project-nested-or-repeated-data"></a>Vnořená nebo opakující se data projektu

Následující dotaz přečte soubor *justSimpleArray. Parquet* . Projekty IT jsou všechny sloupce ze souboru Parquet, včetně vnořených nebo opakovaných dat.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="access-elements-from-nested-columns"></a>Přístup k prvkům z vnořených sloupců

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
        -- you can see original nested columns values by uncommenting lines below
        --DateStruct VARCHAR(8000),
        [DateValue] DATE '$.DateStruct.Date',
        --TimeStruct VARCHAR(8000),
        [TimeStruct.Time] TIME,
        --TimestampStruct VARCHAR(8000),
        [TimestampStruct.Timestamp] DATETIME2,
        --DecimalStruct VARCHAR(8000),
        DecimalValue DECIMAL(18, 5) '$.DecimalStruct.Decimal',
        --FloatStruct VARCHAR(8000),
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

## <a name="next-steps"></a>Další kroky

V dalším článku se dozvíte, jak [zadávat dotazy na soubory JSON](query-json-files.md).
