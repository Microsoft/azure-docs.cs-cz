---
title: Použití metadat souboru v dotazech
description: Funkce OPENROWSET poskytuje informace o souboru a cestě ke každému souboru použitému v dotazu k filtrování nebo analýze dat na základě názvu souboru nebo cesty ke složce.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: d24ae1f42c685589309506b2d5e0eab157b2bc42
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96299611"
---
# <a name="use-file-metadata-in-serverless-sql-pool-queries"></a>Použití metadat souboru v neserverových dotazech na fond SQL

Fond SQL bez serveru může adresovat více souborů a složek, jak je popsáno v článku [složky dotazů a více souborů](query-folders-multiple-csv-files.md) . V tomto článku se dozvíte, jak v dotazech používat informace o metadatech souborů a složek.

V některých případech možná budete muset zjistit, který zdroj souborů nebo složek je v sadě výsledků v souvislosti s konkrétním řádkem.

Pomocí funkce a můžete `filepath` `filename` vracet názvy souborů nebo cestu v sadě výsledků dotazu. Můžete je také použít k filtrování dat na základě názvu souboru nebo cesty ke složce. Tyto funkce jsou popsány v oddílu syntaxe [filename](query-data-storage.md#filename-function) a funkce [FilePath](query-data-storage.md#filepath-function). V následujících částech najdete krátké popisy podél vzorků.

## <a name="prerequisites"></a>Předpoklady

Prvním krokem je **Vytvoření databáze** se zdrojem dat, který odkazuje na účet úložiště. Pak inicializujte objekty spuštěním [instalačního skriptu](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) v této databázi. Tento instalační skript vytvoří zdroje dat, přihlašovací údaje v oboru databáze a formáty externích souborů, které jsou použity v těchto ukázkách.

## <a name="functions"></a>Functions

### <a name="filename"></a>Bitmap

Tato funkce vrátí název souboru, ze kterého řádek pochází.

Následující příklad přečte NYC žluté taxislužby datové soubory za poslední tři měsíce 2017 a vrátí počet jezdí na soubor. Část OPENROWSET dotazu určuje, které soubory budou čteny.

```sql
SELECT
    nyc.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM  
    OPENROWSET(
        BULK 'parquet/taxi/year=2017/month=9/*.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) nyc
GROUP BY nyc.filename();
```

Následující příklad ukazuje, jak *filename ()* lze použít v klauzuli WHERE pro filtrování souborů, které mají být čteny. Přistupuje k celé složce v části OPENROWSET dotazu a filtruje soubory v klauzuli WHERE.

Vaše výsledky budou stejné jako předchozí příklad.

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
    BULK 'csv/taxi/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',
        FIRSTROW = 2) 
        WITH (C1 varchar(200) ) AS [r]
WHERE
    r.filename() IN ('yellow_tripdata_2017-10.csv', 'yellow_tripdata_2017-11.csv', 'yellow_tripdata_2017-12.csv')
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

### <a name="filepath"></a>FilePath

Funkce FilePath vrátí úplnou nebo částečnou cestu:

- Při volání bez parametru vrátí úplnou cestu k souboru, ze které řádek pochází. Pokud je použita DATA_SOURCE v OPENROWSET, vrátí cestu relativní k DATA_SOURCE. 
- Při volání s parametrem vrátí část cesty, která odpovídá zástupnému znaku na pozici zadané v parametru. Například hodnota parametru 1 vrátí část cesty, která se shoduje s prvním zástupným znakem.

Následující ukázka přečte NYC žluté taxislužby datové soubory za poslední tři měsíce 2017. Vrátí počet jezdí na cestu k souboru. Část OPENROWSET dotazu určuje, které soubory budou čteny.

```sql
SELECT
    r.filepath() AS filepath
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_2017-1*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id INT
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
        BULK 'csv/taxi/yellow_tripdata_*-*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',        
        FIRSTROW = 2
    )
WITH (
    vendor_id INT
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
