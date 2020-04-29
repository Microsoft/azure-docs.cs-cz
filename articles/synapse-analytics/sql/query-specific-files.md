---
title: Použití metadat souboru v dotazech
description: Funkce OPENROWSET poskytuje informace o souboru a cestě ke každému souboru použitému v dotazu k filtrování nebo analýze dat na základě názvu souboru nebo cesty ke složce.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 40a8e2c153ec3d8e7b4007340b9433a38f9ccc89
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431550"
---
# <a name="using-file-metadata-in-queries"></a>Použití metadat souboru v dotazech

Dotazovací služba SQL na vyžádání může adresovat více souborů a složek, jak je popsáno v článku [složky dotazů a více souborů](query-folders-multiple-csv-files.md) . V tomto článku se dozvíte, jak v dotazech používat informace o metadatech souborů a složek.

V některých případech možná budete muset zjistit, který zdroj souborů nebo složek je v sadě výsledků v souvislosti s konkrétním řádkem.

Pomocí funkce `filepath` a `filename` můžete vracet názvy souborů nebo cestu v sadě výsledků dotazu. Můžete je také použít k filtrování dat na základě názvu souboru nebo cesty ke složce. Tyto funkce jsou popsány v oddílu syntaxe [filename](develop-storage-files-overview.md#filename-function) a funkce [FilePath](develop-storage-files-overview.md#filepath-function). Níže najdete krátké popisy v rámci ukázek.

## <a name="prerequisites"></a>Požadavky

Než si přečtete zbytek tohoto článku, přečtěte si následující požadavky:

- [Nastavení při prvním spuštění](query-data-storage.md#first-time-setup)
- [Požadavky](query-data-storage.md#prerequisites)

## <a name="functions"></a>Functions

### <a name="filename"></a>Bitmap

Tato funkce vrátí název souboru, ze kterého řádek pochází.

Následující příklad přečte NYC žluté taxislužby datové soubory za poslední tři měsíce 2017 a vrátí počet jezdí na soubor. Část OPENROWSET dotazu určuje, které soubory budou čteny.

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET') AS [r]
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

Následující příklad ukazuje, jak *filename ()* lze použít v klauzuli WHERE pro filtrování souborů, které mají být čteny. Přistupuje k celé složce v části OPENROWSET dotazu a filtruje soubory v klauzuli WHERE.

Vaše výsledky budou stejné jako předchozí příklad.

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
    FORMAT='PARQUET') AS [r]
WHERE
    r.filename() IN ('yellow_tripdata_2017-10.parquet', 'yellow_tripdata_2017-11.parquet', 'yellow_tripdata_2017-12.parquet')
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

### <a name="filepath"></a>FilePath

Funkce FilePath vrátí úplnou nebo částečnou cestu:

- Při volání bez parametru vrátí úplnou cestu k souboru, ze které řádek pochází.
- Při volání s parametrem vrátí část cesty, která odpovídá zástupnému znaku na pozici zadané v parametru. Například hodnota parametru 1 vrátí část cesty, která se shoduje s prvním zástupným znakem.

Následující ukázka přečte NYC žluté taxislužby datové soubory za poslední tři měsíce 2017. Vrátí počet jezdí na cestu k souboru. Část OPENROWSET dotazu určuje, které soubory budou čteny.

```sql
SELECT
    r.filepath() AS filepath
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_2017-1*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 2
    )
    WITH (
        vendor_id INT,
        pickup_datetime DATETIME2,
        dropoff_datetime DATETIME2,
        passenger_count SMALLINT,
        trip_distance FLOAT,
        rate_code SMALLINT,
        store_and_fwd_flag SMALLINT,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type SMALLINT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS [r]
GROUP BY
    r.filepath()
ORDER BY
    filepath;
```

Následující příklad ukazuje, jak lze použít *FilePath ()* v klauzuli WHERE pro filtrování souborů, které mají být čteny.

Můžete použít zástupné znaky v části OPENROWSET dotazu a filtrovat soubory v klauzuli WHERE. Vaše výsledky budou stejné jako předchozí příklad.

```sql
SELECT
    r.filepath() AS filepath
    ,r.filepath(1) AS [year]
    ,r.filepath(2) AS [month]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_*-*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 2
    )
WITH (
    vendor_id INT,
    pickup_datetime DATETIME2,
    dropoff_datetime DATETIME2,
    passenger_count SMALLINT,
    trip_distance FLOAT,
    rate_code SMALLINT,
    store_and_fwd_flag SMALLINT,
    pickup_location_id INT,
    dropoff_location_id INT,
    payment_type SMALLINT,
    fare_amount FLOAT,
    extra FLOAT,
    mta_tax FLOAT,
    tip_amount FLOAT,
    tolls_amount FLOAT,
    improvement_surcharge FLOAT,
    total_amount FLOAT
) AS [r]
WHERE
    r.filepath(1) IN ('2017')
    AND r.filepath(2) IN ('10', '11', '12')
GROUP BY
    r.filepath()
    ,r.filepath(1)
    ,r.filepath(2)
ORDER BY
    filepath;
```

## <a name="next-steps"></a>Další kroky

V dalším článku se dozvíte, jak [zadávat dotazy na soubory Parquet](query-parquet-files.md).
