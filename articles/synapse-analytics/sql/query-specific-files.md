---
title: Použití metadat souboru v dotazech
description: Funkce OPENROWSET poskytuje informace o souborech a cestě o každém souboru použitém v dotazu k filtrování nebo analýze dat na základě názvu souboru a/nebo cesty ke složce.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 40a8e2c153ec3d8e7b4007340b9433a38f9ccc89
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431550"
---
# <a name="using-file-metadata-in-queries"></a>Použití metadat souboru v dotazech

Služba dotazu na vyžádání může adresovat více souborů a složek, jak je popsáno ve [složkách dotazu a v](query-folders-multiple-csv-files.md) článku více souborů. V tomto článku se dozvíte, jak používat informace o metadatech o názvech souborů a složek v dotazech.

Někdy možná budete potřebovat vědět, který soubor nebo zdroj složky koreluje s určitým řádkem ve výsledné sadě.

Můžete použít `filepath` funkci `filename` a vrátit názvy souborů a/nebo cestu v sadě výsledků. Nebo je můžete použít k filtrování dat na základě názvu souboru nebo cesty ke složce. Tyto funkce jsou popsány ve [funkci název souboru](develop-storage-files-overview.md#filename-function) sekce syntaxe a [funkci Filepath](develop-storage-files-overview.md#filepath-function). Níže najdete krátké popisy podél vzorků.

## <a name="prerequisites"></a>Požadavky

Před přečtením zbývající části tohoto článku si přečtěte následující předpoklady:

- [První nastavení](query-data-storage.md#first-time-setup)
- [Požadavky](query-data-storage.md#prerequisites)

## <a name="functions"></a>Functions

### <a name="filename"></a>Název_souboru

Tato funkce vrátí název souboru, ze kterého řádek pochází.

Následující ukázka přečte datové soubory NYC Yellow Taxi za poslední tři měsíce roku 2017 a vrátí počet jízd na soubor. Openrowset část dotazu určuje, které soubory budou číst.

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

Následující příklad ukazuje, jak *filename()* lze použít v klauzuli WHERE filtrovat soubory ke čtení. Přistupuje k celé složce v části OPENROWSET dotazu a filtruje soubory v klauzuli WHERE.

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

### <a name="filepath"></a>Filepath

Funkce Cesta souboru vrátí úplnou nebo částečnou cestu:

- Při volání bez parametru vrátí úplnou cestu k souboru, ze které řádek pochází.
- Při volání s parametrem vrátí část cesty, která odpovídá zástupný znak na pozici zadané v parametru. Například hodnota parametru 1 vrátí část cesty, která odpovídá prvnímu zástupný znak.

Následující ukázka čte datové soubory NYC Yellow Taxi za poslední tři měsíce roku 2017. Vrátí počet jízd na cestu k souboru. Openrowset část dotazu určuje, které soubory budou číst.

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

Následující příklad ukazuje, jak *filepath()* lze použít v klauzuli WHERE filtrovat soubory ke čtení.

Můžete použít zástupné znaky v openrowset části dotazu a filtrovat soubory v where klauzule. Vaše výsledky budou stejné jako předchozí příklad.

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

V dalším článku se dozvíte, jak se dotaz ovat [parketové soubory](query-parquet-files.md).
