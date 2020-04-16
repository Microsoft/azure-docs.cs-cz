---
title: Dotaz na parketové soubory pomocí SQL na vyžádání (náhled)
description: V tomto článku se dozvíte, jak dotaz ovat parketové soubory pomocí SQL na vyžádání (náhled).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0b272a8c8ce81fc40585014e5930f5d7b1b5f2c0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431693"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Dotaz na parketové soubory pomocí SQL na vyžádání (preview) v Azure Synapse Analytics

V tomto článku se dozvíte, jak napsat dotaz pomocí SQL na vyžádání (náhled), který bude číst parketové soubory.

## <a name="prerequisites"></a>Požadavky

Než si přečtete zbytek tohoto článku, přečtěte si následující články:

- [První nastavení](query-data-storage.md#first-time-setup)
- [Požadavky](query-data-storage.md#prerequisites)

## <a name="dataset"></a>Datová sada

Parquet files můžete dotazovat stejným způsobem, jakým čtete soubory CSV. Jediným rozdílem je, že parametr FILEFORMAT by měl být nastaven na PARKETy. Příklady v tomto článku ukazují specifika čtení parketových souborů.

> [!NOTE]
> Není nutné zadat sloupce v klauzuli OPENROWSET WITH při čtení parketových souborů. SQL on-demand bude využívat metadata v souboru parket a vázat sloupce podle názvu.

Budete používat složku *parkety / taxi* pro ukázkové dotazy. Obsahuje nyc taxi - yellow taxi trip records data z července 2016. do června 2018.

Data jsou rozdělena podle roku a měsíce a struktura složek je následující:

- rok =2016
  - měsíc=6
  - ...
  - měsíc=12
- rok =2017
  - měsíc=1
  - ...
  - měsíc=12
- rok =2018
  - měsíc=1
  - ...
  - měsíc=6

## <a name="query-set-of-parquet-files"></a>Sada dotazů na parketové soubory

Při dotazování na parketové soubory můžete zadat pouze sloupce, které vás zajímají.

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

## <a name="automatic-schema-inference"></a>Odvození automatického schématu

Při čtení parketových souborů není nutné používat klauzuli OPENROWSET WITH. Názvy sloupců a datové typy se automaticky čtou z parketových souborů.

Následující ukázka ukazuje možnosti odvození automatického schématu pro soubory parket. Vrátí počet řádků v září 2017 bez zadání schématu.

> [!NOTE]
> Nemusíte zadávat sloupce v klauzuli OPENROWSET WITH při čtení parketových souborů. V takovém případě sql on-demand dotazová služba bude využívat metadata v souboru parket a vázat sloupce podle názvu.

```sql
SELECT
    COUNT_BIG(*)
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

### <a name="query-partitioned-data"></a>Dotaz rozdělená data

Sada dat uvedená v této ukázce je rozdělena (rozdělena na oddíly) do samostatných podsložek. Pomocí funkce Filepath můžete cílit na určité oddíly. Tento příklad zobrazuje částky tarifu podle roku, měsíce a payment_type pro první tři měsíce roku 2017.

> [!NOTE]
> Dotaz SQL na vyžádání je kompatibilní s hive/hadoop schéma dělení.

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

Parketové soubory obsahují popisy typů pro každý sloupec. Následující tabulka popisuje, jak jsou typy parket mapovány na nativní typy SQL.

| Typ parket | Logický typ parket (anotace) | Datový typ SQL |
| --- | --- | --- |
| Boolean | | bitové |
| BINÁRNÍ / BYTE_ARRAY | | Varbinary |
| Dvojité | | float |
| Float | | reálná |
| INT32 | | int |
| INT64 | | bigint |
| 96. | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binární |
| Binární |UTF8 |varchar \*(UTF8 kolace) |
| Binární |Řetězec |varchar \*(UTF8 kolace) |
| Binární |Výčtu|varchar \*(UTF8 kolace) |
| Binární |Uuid |uniqueidentifier |
| Binární |Desetinných |decimal |
| Binární |JSON |varchar(max) \*(Řazení UTF8) |
| Binární |BSON |Varbinary(max) |
| FIXED_LEN_BYTE_ARRAY |Desetinných |decimal |
| BYTE_ARRAY |Interval |varchar(max), serializovaný do standardizovaného formátu |
| INT32 |INT(8, pravda) |smallint |
| INT32 |INT(16, pravda) |smallint |
| INT32 |INT(32, pravda) |int |
| INT32 |INT(8, nepravdivé) |tinyint |
| INT32 |INT(16, nepravdivé) |int |
| INT32 |INT(32, nepravdivé) |bigint |
| INT32 |DATE (Datum) |date |
| INT32 |Desetinných |decimal |
| INT32 |ČAS (MILLIS)|time |
| INT64 |INT(64, pravda) |bigint |
| INT64 |INT(64, nepravdivé ) |desetinné místo (20,0) |
| INT64 |Desetinných |decimal |
| INT64 |ČAS (MICROS / NANOS) |time |
|INT64 |TIMESTAMP (MILLIS / MICROS / NANOS) |datetime2 |
|[Komplexní typ](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |Seznamu |varchar(max), serializovaný do JSON |
|[Komplexní typ](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|Mapu|varchar(max), serializovaný do JSON |

## <a name="next-steps"></a>Další kroky

Přejdete k dalšímu článku a dozvíte se, jak [zadávat vnořené typy parket](query-parquet-nested-types.md).
