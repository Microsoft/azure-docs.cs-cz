---
title: Dotazování na soubory CSV pomocí SQL na vyžádání (Preview)
description: V tomto článku se dozvíte, jak zadávat dotazy na samostatné soubory CSV s různými formáty souborů pomocí SQL na vyžádání (Preview).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 3d09692c06bcdffbb070f545950092592e417838
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431589"
---
# <a name="query-csv-files"></a>Dotazování na soubory CSV

V tomto článku se dozvíte, jak zadat dotaz na jeden soubor CSV pomocí SQL na vyžádání (Preview) ve službě Azure synapse Analytics. Soubory CSV můžou mít různé formáty: 

- S a bez řádku záhlaví
- Hodnoty oddělené čárkami a tabulátory
- Konce řádků ve stylu Windows a UNIX
- Hodnoty, které nejsou v uvozovkách a uvozovkách, a znaky pro uvozovací znaky

Všechny výše uvedené variace budou uvedené níže.

## <a name="prerequisites"></a>Požadavky

Než si přečtete zbytek tohoto článku, přečtěte si následující články:

- [Nastavení při prvním spuštění](query-data-storage.md#first-time-setup)
- [Požadavky](query-data-storage.md#prerequisites)

## <a name="windows-style-new-line"></a>Nový řádek stylu Windows

Následující dotaz ukazuje, jak číst soubor CSV bez řádku záhlaví, s novým řádkem ve stylu systému Windows a se sloupci oddělenými čárkami.

Náhled souboru:

![Prvních 10 řádků souboru CSV bez záhlaví, nový řádek stylu Windows](./media/query-single-csv-file/population.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
         FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="unix-style-new-line"></a>Nový řádek ve stylu systému UNIX

Následující dotaz ukazuje, jak číst soubor bez řádku záhlaví, s novým řádkem ve stylu systému UNIX a sloupci oddělenými čárkami. Všimněte si, že se v porovnání s ostatními příklady liší umístění souboru.

Náhled souboru:

![Prvních 10 řádků souboru CSV bez řádku záhlaví a s novým řádkem ve stylu UNIX](./media/query-single-csv-file/population-unix.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix/population.csv',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="header-row"></a>Řádek záhlaví

Následující dotaz ukazuje, jak číst soubor s řádkem záhlaví, s novým řádkem ve stylu systému UNIX a sloupci oddělenými čárkami. Všimněte si, že se v porovnání s ostatními příklady liší umístění souboru.

Náhled souboru:

![Prvních 10 řádků souboru CSV s řádkem záhlaví a s novým řádkem ve stylu UNIX.](./media/query-single-csv-file/population-unix-hdr.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr/population.csv',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="custom-quote-character"></a>Vlastní znak uvozovky

Následující dotaz ukazuje, jak přečíst soubor s řádkem záhlaví s novým řádkem ve stylu systému UNIX, sloupci s oddělovači a hodnotami v uvozovkách. Všimněte si, že se v porovnání s ostatními příklady liší umístění souboru.

Náhled souboru:

![Prvních 10 řádků souboru CSV s řádkem záhlaví a s hodnotami nového řádku a v uvozovkách ve stylu systému UNIX.](./media/query-single-csv-file/population-unix-hdr-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-quoted/population.csv',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        FIELDQUOTE = '"'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

> [!NOTE]
> Tento dotaz vrátí stejné výsledky, pokud jste vynechali parametr FIELDQUOTE, protože výchozí hodnota pro FIELDQUOTE je dvojité uvozovky.

## <a name="escaping-characters"></a>Znaky uvozovacích znaků

Následující dotaz ukazuje, jak číst soubor s řádkem záhlaví s novým řádkem ve stylu systému UNIX, sloupci s oddělovači a řídicím znakem použitým pro oddělovač pole (čárka) v rámci hodnot. Všimněte si, že se v porovnání s ostatními příklady liší umístění souboru.

Náhled souboru:

![Prvních 10 řádků souboru CSV s řádkem záhlaví a s novým řádkem a řídicím znakem ve stylu systému UNIX, který se používá pro oddělovač polí.](./media/query-single-csv-file/population-unix-hdr-escape.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-escape/population.csv',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        ESCAPECHAR = '\\'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slov,enia';
```

> [!NOTE]
> Tento dotaz by se nezdařil, pokud není zadán parametr ESCAPECHAR, protože čárka v "slov, enia" by byla považována za oddělovač polí namísto části názvu země. "Slov, enia" by se považovat za dva sloupce. Proto bude mít konkrétní řádek jeden sloupec více než ostatní řádky a jeden sloupec je více, než jste definovali v klauzuli WITH.

## <a name="tab-delimited-files"></a>Soubory s hodnotami oddělenými tabulátory

Následující dotaz ukazuje, jak číst soubor s řádkem záhlaví, s novým řádkem ve stylu systému UNIX a sloupci oddělenými tabulátory. Všimněte si, že se v porovnání s ostatními příklady liší umístění souboru.

Náhled souboru:

![Prvních 10 řádků souboru CSV s řádkem záhlaví a se systémem UNIX – nový řádek a oddělovač tabulátoru](./media/query-single-csv-file/population-unix-hdr-tsv.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-tsv/population.csv',
        FORMAT = 'CSV',
        FIELDTERMINATOR ='\t',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017
```

## <a name="returning-subset-of-columns"></a>Vrácení podmnožiny sloupců

Zatím jste určili schéma souboru CSV pomocí nástroje s a seznamem všech sloupců. V dotazu můžete zadat pouze sloupce, které skutečně potřebujete, a to pomocí pořadového čísla pro každý požadovaný sloupec. Vynecháte také sloupce bez zájmu.

Následující dotaz vrátí do souboru počet jedinečných názvů zemí a určí pouze sloupce, které jsou potřeba:

> [!NOTE]
> Podívejte se na klauzuli WITH v následujícím dotazu a Všimněte si, že na konci řádku, ve kterém definujete sloupec *[country_name]* , se nachází znak "2" (bez uvozovek). To znamená, že sloupec *[country_name]* je druhým sloupcem v souboru. Dotaz bude ignorovat všechny sloupce v souboru s výjimkou druhé.

```sql
SELECT
    COUNT(DISTINCT country_name) AS countries
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
         FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
) AS [r]
```

## <a name="next-steps"></a>Další kroky

V dalších článcích se dozvíte, jak:

- [Dotazování na soubory Parquet](query-parquet-files.md)
- [Dotazování na složky a více souborů](query-folders-multiple-csv-files.md)
