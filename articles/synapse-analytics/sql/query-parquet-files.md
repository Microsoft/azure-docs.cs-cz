---
title: Dotazování souborů Parquet pomocí SQL na vyžádání (Preview)
description: V tomto článku se naučíte, jak zadávat dotazy na soubory Parquet pomocí SQL na vyžádání (Preview).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0b272a8c8ce81fc40585014e5930f5d7b1b5f2c0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431693"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Dotazování souborů Parquet pomocí SQL na vyžádání (Preview) ve službě Azure synapse Analytics

V tomto článku se dozvíte, jak napsat dotaz pomocí SQL na vyžádání (Preview), který načte soubory Parquet.

## <a name="prerequisites"></a>Požadavky

Než si přečtete zbytek tohoto článku, přečtěte si následující články:

- [Nastavení při prvním spuštění](query-data-storage.md#first-time-setup)
- [Požadavky](query-data-storage.md#prerequisites)

## <a name="dataset"></a>Datová sada

Soubory Parquet můžete dotazovat stejným způsobem jako soubory CSV. Jediným rozdílem je, že parametr formátu souboru by měl být nastaven na PARQUET. Příklady v tomto článku znázorňují konkrétní soubory pro čtení Parquet.

> [!NOTE]
> Při čtení souborů Parquet není nutné zadávat sloupce v klauzuli OPENROWSET WITH. SQL na vyžádání bude používat metadata v souboru Parquet a sváže sloupce podle názvu.

Pro ukázkové dotazy použijete složku *Parquet/taxislužby* . Obsahuje NYC taxislužby – žlutá Taxislužbyová data záznamů záznamů od července 2016. do června 2018.

Data jsou rozdělená podle roku a měsíce a struktura složek je následující:

- rok = 2016
  - měsíc = 6
  - ...
  - měsíc = 12
- rok = 2017
  - měsíc = 1
  - ...
  - měsíc = 12
- Year = 2018
  - měsíc = 1
  - ...
  - měsíc = 6

## <a name="query-set-of-parquet-files"></a>Dotaz sady souborů Parquet

Při dotazování souborů Parquet můžete zadat pouze sloupce s oznámením.

```sql
SELECT
        YEAR(pickup_datetime),
        passenger_count,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) WITH (
        pickup_datetime DATETIME2,
        passenger_count INT
    ) AS nyc
GROUP BY
    passenger_count,
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime),
    passenger_count;
```

## <a name="automatic-schema-inference"></a>Automatické odvození schématu

Při čtení souborů Parquet není nutné používat klauzuli OPENROWSET WITH. Názvy sloupců a datové typy se automaticky čtou ze souborů Parquet.

Následující ukázka ukazuje možnosti automatického odvození schématu pro soubory Parquet. Vrátí počet řádků v září 2017 bez zadání schématu.

> [!NOTE]
> Při čtení souborů Parquet není nutné zadávat sloupce v klauzuli OPENROWSET WITH. V takovém případě dotazovací služba SQL na vyžádání bude používat metadata v souboru Parquet a sváže sloupce podle názvu.

```sql
SELECT
    COUNT_BIG(*)
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

### <a name="query-partitioned-data"></a>Dotazování na dělená data

Datová sada uvedená v této ukázce je rozdělená (rozdělená do oddílů) na samostatné podsložky. Pomocí funkce FilePath můžete cílit na konkrétní oddíly. V tomto příkladu se zobrazuje množství tarifů podle roku, měsíce a payment_type v prvních třech měsících od 2017.

> [!NOTE]
> Dotaz SQL na vyžádání je kompatibilní se schématem dělení na oddíly/Hadoop.

```sql
SELECT
    nyc.filepath(1) AS [year],
    nyc.filepath(2) AS [month],
    payment_type,
    SUM(fare_amount) AS fare_total
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND pickup_datetime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    nyc.filepath(1),
    nyc.filepath(2),
    payment_type
ORDER BY
    nyc.filepath(1),
    nyc.filepath(2),
    payment_type;
```

## <a name="type-mapping"></a>Mapování typů

Soubory Parquet obsahují popisy typů pro každý sloupec. Následující tabulka popisuje, jak jsou typy Parquet mapovány na nativní typy SQL.

| Typ Parquet | Logický typ Parquet (anotace) | Datový typ SQL |
| --- | --- | --- |
| DATOVÉHO | | bitové |
| BINÁRNÍ/BYTE_ARRAY | | varbinary |
| KLEPAT | | float |
| Plovák | | real |
| UVEDENA | | int |
| INT64 | | bigint |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binární |
| TVARU |UTF |varchar \*(řazení UTF8) |
| TVARU |ŘETEZCE |varchar \*(řazení UTF8) |
| TVARU |VYTVÁŘENÍ|varchar \*(řazení UTF8) |
| TVARU |IDENTIFIKÁTOR |uniqueidentifier |
| TVARU |NOTACI |decimal |
| TVARU |JSON |varchar (max) \*(kolace UTF8) |
| TVARU |BSON |varbinary (max) |
| FIXED_LEN_BYTE_ARRAY |NOTACI |decimal |
| BYTE_ARRAY |DOBA |varchar (max), serializováno do standardizovaného formátu |
| UVEDENA |INT (8, true) |smallint |
| UVEDENA |INT (16, true) |smallint |
| UVEDENA |INT (32, true) |int |
| UVEDENA |INT (8, false) |tinyint |
| UVEDENA |INT (16, false) |int |
| UVEDENA |INT (32, false) |bigint |
| UVEDENA |DATE (Datum) |date |
| UVEDENA |NOTACI |decimal |
| UVEDENA |ČAS (LISOVNY)|time |
| INT64 |INT (64; true) |bigint |
| INT64 |INT (64, false) |desetinné číslo (20, 0) |
| INT64 |NOTACI |decimal |
| INT64 |ČAS (MIKROČASU A NANO) |time |
|INT64 |ČASOVÉ RAZÍTKO (LISOVNY//NANO) |datetime2 |
|[Komplexní typ](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |SEZNAMU |varchar (max), serializováno do formátu JSON |
|[Komplexní typ](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|MAPY|varchar (max), serializováno do formátu JSON |

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku a Naučte se, jak [zadávat dotazy na vnořené typy Parquet](query-parquet-nested-types.md).
