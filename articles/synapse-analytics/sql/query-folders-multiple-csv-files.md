---
title: Složky dotazů a více souborů CSV pomocí SQL na vyžádání (Preview)
description: SQL na vyžádání (Preview) podporuje čtení více souborů nebo složek pomocí zástupných znaků, které jsou podobné zástupným znakům používaným v operačním systému Windows.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 8f8af7fab7113e38b91c3f5f1bcc41b4e4fba2c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457361"
---
# <a name="query-folders-and-multiple-csv-files"></a>Složky dotazů a více souborů CSV  

V tomto článku se dozvíte, jak napsat dotaz pomocí SQL na vyžádání (ve verzi Preview) ve službě Azure synapse Analytics.

SQL na vyžádání podporuje čtení více souborů nebo složek pomocí zástupných znaků, které jsou podobné zástupným znakům používaným v operačním systému Windows. Větší flexibilita je však k dispozici, protože je povoleno více zástupných znaků.

## <a name="prerequisites"></a>Požadavky

Než si přečtete zbytek tohoto článku, Projděte si níže uvedené články:

- [Nastavení při prvním spuštění](query-data-storage.md#first-time-setup)
- [Požadavky](query-data-storage.md#prerequisites)

## <a name="read-multiple-files-in-folder"></a>Číst více souborů ve složce

K provedení ukázkových dotazů použijete složku *CSV/taxislužby* . Obsahuje NYC taxislužby – žlutá Taxislužbyová cesta zaznamenává data z července 2016 do června 2018.

Soubory ve *formátu CSV/taxislužby* jsou pojmenovány po rocích a měsících:

- yellow_tripdata_2016 -07. csv
- yellow_tripdata_2016 -08. csv
- yellow_tripdata_2016 -09. csv
- ...
- yellow_tripdata_2018 -04. csv
- yellow_tripdata_2018 -05. csv
- yellow_tripdata_2018 -06. csv

Každý soubor má následující strukturu:
        
    [First 10 rows of the CSV file](./media/querying-folders-and-multiple-csv-files/nyc-taxi.png)

## <a name="read-all-files-in-folder"></a>Číst všechny soubory ve složce
    
Následující příklad přečte všechny NYC žluté taxislužby datové soubory ze složky *CSV/taxislužby* a vrátí celkový počet cestujících a jezdí za rok. Zobrazuje také využití agregačních funkcí.

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
> Všechny soubory, které jsou k dispozici s jednou OPENROWSET, musí mít stejnou strukturu (tj. počet sloupců a jejich datové typy).

### <a name="read-subset-of-files-in-folder"></a>Číst podmnožinu souborů ve složce

Následující příklad přečte 2017 NYC žluté taxislužby datové soubory ze složky *CSV/taxislužby* pomocí zástupného znaku a vrátí celkovou částku tarifu na typ platby.

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
> Všechny soubory, které jsou k dispozici s jednou OPENROWSET, musí mít stejnou strukturu (tj. počet sloupců a jejich datové typy).

## <a name="read-folders"></a>Číst složky

Cesta, kterou zadáte do OPENROWSET, může být také cestou ke složce. Následující části obsahují tyto typy dotazů.

### <a name="read-all-files-from-specific-folder"></a>Číst všechny soubory z konkrétní složky

Všechny soubory ve složce můžete číst pomocí zástupného znaku na úrovni souboru, jak je znázorněno v části [čtení všech souborů ve složce](#read-all-files-in-folder). Existuje však způsob, jak zadat dotaz na složku a využívat všechny soubory v této složce.

Pokud cesta zadaná v OPENROWSET odkazuje na složku, budou se všechny soubory v této složce používat jako zdroj dotazu. Následující dotaz načte všechny soubory ve složce *CSV/taxislužby* .

> [!NOTE]
> Všimněte si existence/na konci cesty v následujícím dotazu. Označuje složku. Pokud je hodnota/vynechána, dotaz bude místo toho cílen na soubor s názvem *taxislužby* .

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
> Všechny soubory, které jsou k dispozici s jednou OPENROWSET, musí mít stejnou strukturu (tj. počet sloupců a jejich datové typy).

### <a name="read-all-files-from-multiple-folders"></a>Číst všechny soubory z více složek

Je možné číst soubory z více složek pomocí zástupného znaku. Následující dotaz načte všechny soubory ze všech složek umístěných ve složce *CSV* , jejichž názvy začínají na *t* a končí na *i*.

> [!NOTE]
> Všimněte si existence/na konci cesty v následujícím dotazu. Označuje složku. Pokud je hodnota/vynechána, dotaz bude místo toho cílen na soubory s názvem *t&ast;* .

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
> Všechny soubory, které jsou k dispozici s jednou OPENROWSET, musí mít stejnou strukturu (tj. počet sloupců a jejich datové typy).

Vzhledem k tomu, že máte pouze jednu složku, která odpovídá kritériím, je výsledek dotazu stejný jako [čtení všech souborů ve složce](#read-all-files-in-folder).

## <a name="multiple-wildcards"></a>Několik zástupných znaků

Na různých úrovních cesty můžete použít více zástupných znaků. Můžete například rozšířit předchozí dotaz a číst soubory pouze s 2017 daty ze všech složek, jejichž názvy začínají na *t* a končí na *i*.

> [!NOTE]
> Všimněte si existence/na konci cesty v následujícím dotazu. Označuje složku. Pokud je hodnota/vynechána, dotaz bude místo toho cílen na soubory s názvem *t&ast;* .
> Maximální počet zástupných znaků na jeden dotaz je 10.

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
> Všechny soubory, které jsou k dispozici s jednou OPENROWSET, musí mít stejnou strukturu (tj. počet sloupců a jejich datové typy).

Vzhledem k tomu, že máte pouze jednu složku, která odpovídá kritériím, je výsledek dotazu stejný jako [podmnožina souborů ve složce](#read-subset-of-files-in-folder) a [čtení všech souborů z konkrétní složky](#read-all-files-from-specific-folder). Složitější scénáře použití se zástupnými znaky jsou pokryté v [souborech dotazů Parquet](query-parquet-files.md).

## <a name="next-steps"></a>Další kroky

Další informace najdete v článku v tématu [dotaz na konkrétní soubory](query-specific-files.md) .
