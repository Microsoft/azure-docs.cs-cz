---
title: Typy vnořených par dotazů pomocí SQL na vyžádání (náhled)
description: V tomto článku se dozvíte, jak dotaz parkety vnořené typy.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a1e3d3c7494aa75b3f6d481d12135316791772d4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431654"
---
# <a name="query-parquet-nested-types-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Vnořené typy parek dotazu pomocí SQL na vyžádání (preview) v Azure Synapse Analytics

V tomto článku se dozvíte, jak napsat dotaz pomocí SQL na vyžádání (preview) v Azure Synapse Analytics.  Tento dotaz bude číst parkety vnořené typy.

## <a name="prerequisites"></a>Požadavky

Než si přečtete zbytek tohoto článku, přečtěte si následující články:

- [První nastavení](query-data-storage.md#first-time-setup)
- [Požadavky](query-data-storage.md#prerequisites)

## <a name="project-nested-or-repeated-data"></a>Vnořená nebo opakovaná data projektu

Následující dotaz přečte soubor *justSimpleArray.parquet.* Promítá všechny sloupce ze souboru parket včetně vnořených nebo opakovaných dat.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/justSimpleArray.parquet',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="access-elements-from-nested-columns"></a>Přístup k prvkům z vnořených sloupců

Následující dotaz přečte soubor *structExample.parquet* a ukazuje, jak zobrazit prvky vnořeného sloupce:

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/structExample.parquet',
        FORMAT='PARQUET'
    )
    WITH (
        -- you can see original n"sted columns values by uncommenting lines below
        --DateStruct VARCHAR(8000),
        [DateStruct.Date] DATE,
        --TimeStruct VARCHAR(8000),
        [TimeStruct.Time] TIME,
        --TimestampStruct VARCHAR(8000),
        [TimestampStruct.Timestamp] DATETIME2,
        --DecimalStruct VARCHAR(8000),
        [DecimalStruct.Decimal] DECIMAL(18, 5),
        --FloatStruct VARCHAR(8000),
        [FloatStruct.Float] FLOAT
    ) AS [r];
```

## <a name="access-elements-from-repeated-columns"></a>Přístup k prvkům z opakovaných sloupců

Následující dotaz přečte soubor *justSimpleArray.parquet* a používá [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) k načtení **skalárního** prvku z opakovaného sloupce, například Array nebo Map:

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/justSimpleArray.parquet',
        FORMAT='PARQUET'
    ) AS [r];
```

Následující dotaz přečte soubor *mapExample.parquet* a používá [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) k načtení **neskalárního** prvku z opakovaného sloupce, například Array nebo Map:

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/mapExample.parquet',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="next-steps"></a>Další kroky

V dalším článku se zobrazí dotaz na [soubory JSON](query-json-files.md).
