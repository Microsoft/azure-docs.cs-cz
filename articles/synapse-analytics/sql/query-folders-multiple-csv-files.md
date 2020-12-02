---
title: Složky dotazů a více souborů s využitím fondu SQL bez serveru
description: Fond SQL bez serveru podporuje čtení více souborů nebo složek pomocí zástupných znaků, které jsou podobné zástupným znakům používaným v operačním systému Windows.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 83c4d88e1a87f6b546e26dd55da338a36f16ebe4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462625"
---
# <a name="query-folders-and-multiple-files"></a>Dotazování složek a několika souborů  

V tomto článku se dozvíte, jak napsat dotaz pomocí neserverového fondu SQL ve službě Azure synapse Analytics.

Fond SQL bez serveru podporuje čtení více souborů nebo složek pomocí zástupných znaků, které jsou podobné zástupným znakům používaným v operačním systému Windows. Větší flexibilita je však k dispozici, protože je povoleno více zástupných znaků.

## <a name="prerequisites"></a>Předpoklady

Prvním krokem je **Vytvoření databáze** , ve které budete spouštět dotazy. Pak inicializujte objekty spuštěním [instalačního skriptu](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) v této databázi. Tento instalační skript vytvoří zdroje dat, přihlašovací údaje v oboru databáze a formáty externích souborů, které jsou použity v těchto ukázkách.

K provedení ukázkových dotazů použijete složku *CSV/taxislužby* . Obsahuje NYC taxislužby – žlutá Taxislužbyová cesta zaznamenává data z července 2016 do června 2018. Soubory ve *formátu CSV/taxislužby* se pojmenují po rocích a měsících pomocí následujícího vzoru: yellow_tripdata_ <year> - <month> . csv.

## <a name="read-all-files-in-folder"></a>Číst všechny soubory ve složce

Následující příklad přečte všechny NYC žluté taxislužby datové soubory ze složky *CSV/taxislužby* a vrátí celkový počet cestujících a jezdí za rok. Zobrazuje také využití agregačních funkcí.

```sql
SELECT 
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        pickup_datetime DATETIME2 2, 
        passenger_count INT 4
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
        BULK 'csv/taxi/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        payment_type INT 10,
        fare_amount FLOAT 11
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
        BULK 'csv/taxi/',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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
> Všimněte si existence/na konci cesty v následujícím dotazu. Označuje složku. Pokud je hodnota/vynechána, dotaz bude místo toho cílen na soubory s názvem *t &ast;* .

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/t*i/', 
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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

## <a name="traverse-folders-recursively"></a>Rekurzivní procházení složek

Fond SQL bez serveru může rekurzivně Procházet složky, pokud zadáte/* * na konci cesty. Následující dotaz načte všechny soubory ze všech složek a podsložek umístěných ve složce *CSV* .

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/**', 
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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

## <a name="multiple-wildcards"></a>Několik zástupných znaků

Na různých úrovních cesty můžete použít více zástupných znaků. Můžete například rozšířit předchozí dotaz a číst soubory pouze s 2017 daty ze všech složek, jejichž názvy začínají na *t* a končí na *i*.

> [!NOTE]
> Všimněte si existence/na konci cesty v následujícím dotazu. Označuje složku. Pokud je hodnota/vynechána, dotaz bude místo toho cílen na soubory s názvem *t &ast;* .
> Maximální počet zástupných znaků na jeden dotaz je 10.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/t*i/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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

Další informace najdete v článku dotaz na [konkrétní soubory](query-specific-files.md) .
