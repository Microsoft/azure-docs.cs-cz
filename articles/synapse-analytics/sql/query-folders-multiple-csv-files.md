---
title: Složky dotazů a více souborů CSV pomocí SQL na vyžádání (náhled)
description: SQL on-demand (preview) podporuje čtení více souborů nebo složek pomocí zástupných znaků, které jsou podobné zástupné znaky používané v operačním systému Windows.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 070574f0eec40c27eb05cec3bf0a84282b85050f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431797"
---
# <a name="query-folders-and-multiple-csv-files"></a>Složky dotazů a více souborů CSV  

V tomto článku se dozvíte, jak napsat dotaz pomocí SQL na vyžádání (preview) v Azure Synapse Analytics.

SQL on-demand podporuje čtení více souborů nebo složek pomocí zástupných znaků, které jsou podobné zástupné znaky používané v operačním systému Windows. Je však k dispozici větší flexibilita, protože je povoleno více zástupných znaků.

## <a name="prerequisites"></a>Požadavky

Před přečtením zbytku tohoto článku, ujistěte se, že zkontrolovat níže uvedené články:

- [První nastavení](query-data-storage.md#first-time-setup)
- [Požadavky](query-data-storage.md#prerequisites)

## <a name="read-multiple-files-in-folder"></a>Čtení více souborů ve složce

Budete používat složku *csv/taxi* sledovat ukázkové dotazy. Obsahuje data NYC Taxi - Yellow Taxi Trip Records od července 2016 do června 2018.

Soubory v *csv / taxi* jsou pojmenovány po roce a měsíci:

- yellow_tripdata_2016-07.csv
- yellow_tripdata_2016-08.csv
- yellow_tripdata_2016-09.csv
- ...
- yellow_tripdata_2018-04.csv
- yellow_tripdata_2018-05.csv
- yellow_tripdata_2018-06.csv

Každý soubor má následující strukturu:
        
    [First 10 rows of the CSV file](./media/querying-folders-and-multiple-csv-files/nyc-taxi.png)

## <a name="read-all-files-in-folder"></a>Čtení všech souborů ve složce
    
V níže uvedeném příkladu jsou uvedeny všechny datové soubory NYC Yellow Taxi ze složky *csv/taxi* a vrátí celkový počet cestujících a jízd za rok. Zobrazuje také využití agregačních funkcí.

```sql
SELECT 
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/*.*',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
           trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
           pickup_location_id INT,'
        dropoff_location_id INT,
           payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Všechny soubory přístupné pomocí jediného OPENROWSET musí mít stejnou strukturu (tj. počet sloupců a jejich datové typy).

### <a name="read-subset-of-files-in-folder"></a>Čtení podmnožiny souborů ve složce

Následující příklad přečte datové soubory 2017 NYC Yellow Taxi ze složky *csv/taxi* pomocí zástupné znakové karty a vrátí celkovou částku jízdného na typ platby.

```sql
SELECT 
    payment_type,  
    SUM(fare_amount) AS fare_total
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_2017-*.csv',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY payment_type
ORDER BY payment_type;
```

> [!NOTE]
> Všechny soubory přístupné pomocí jediného OPENROWSET musí mít stejnou strukturu (tj. počet sloupců a jejich datové typy).

## <a name="read-folders"></a>Čtení složek

Cesta, kterou zadáte openrowset může být také cesta ke složce. Následující části obsahují tyto typy dotazů.

### <a name="read-all-files-from-specific-folder"></a>Čtení všech souborů z určité složky

Všechny soubory ve složce můžete číst pomocí zástupného znaku na úrovni souborů, jak je znázorněno v části [Číst všechny soubory ve složce](#read-all-files-in-folder). Existuje však způsob, jak zadat dotaz na složku a spotřebovat všechny soubory v této složce.

Pokud cesta k dispozici v OPENROWSET odkazuje na složku, všechny soubory v této složce budou použity jako zdroj pro váš dotaz. Následující dotaz bude číst všechny soubory ve složce *csv/taxi.*

> [!NOTE]
> Všimněte si existence / na konci cesty v níže uvedeném dotazu. Označuje složku. Pokud / je vynechán, dotaz se zaměří na soubor s názvem *taxi* místo.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Všechny soubory přístupné pomocí jediného OPENROWSET musí mít stejnou strukturu (tj. počet sloupců a jejich datové typy).

### <a name="read-all-files-from-multiple-folders"></a>Čtení všech souborů z více složek

Je možné číst soubory z více složek pomocí zástupného znaku. Následující dotaz bude číst všechny soubory ze všech složek umístěných ve složce *CSV,* které mají názvy začínající *na t* a končící *i*.

> [!NOTE]
> Všimněte si existence / na konci cesty v níže uvedeném dotazu. Označuje složku. Pokud / je vynechán, dotaz se zaměří na soubory s názvem *t&ast;i* místo.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/t*i/', 
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Všechny soubory přístupné pomocí jediného OPENROWSET musí mít stejnou strukturu (tj. počet sloupců a jejich datové typy).

Vzhledem k tomu, že máte pouze jednu složku, která odpovídá kritériím, výsledek dotazu je stejný jako [číst všechny soubory ve složce](#read-all-files-in-folder).

## <a name="multiple-wildcards"></a>Více zástupných znaků

Na různých úrovních cesty můžete použít více zástupných znaků. Můžete například obohatit předchozí dotaz na čtení souborů pouze s daty 2017, ze všech složek, jejichž názvy začínají *písmenem t* a končí *i*.

> [!NOTE]
> Všimněte si existence / na konci cesty v níže uvedeném dotazu. Označuje složku. Pokud / je vynechán, dotaz se zaměří na soubory s názvem *t&ast;i* místo.
> Maximální limit 10 zástupných znaků na dotaz.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/t*i/yellow_tripdata_2017-*.csv',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Všechny soubory přístupné pomocí jediného OPENROWSET musí mít stejnou strukturu (tj. počet sloupců a jejich datové typy).

Vzhledem k tomu, že máte pouze jednu složku, která odpovídá kritériím, výsledek dotazu je stejný jako [podmnožina souborů Pro čtení ve složce](#read-subset-of-files-in-folder) a [Čtení všech souborů z určité složky](#read-all-files-from-specific-folder). Složitější scénáře použití zástupných symbolů jsou popsány v [souborech parket ových dotazů](query-parquet-files.md).

## <a name="next-steps"></a>Další kroky

Další informace naleznete v článku V článku [soubory specifické pro dotaz.](query-specific-files.md)
