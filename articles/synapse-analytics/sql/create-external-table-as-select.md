---
title: Ukládání výsledků dotazů do úložiště
description: V tomto článku se dozvíte, jak ukládat výsledky dotazu do úložiště pomocí SQL na vyžádání (Preview).
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: cbf6d42f3b1d130a6bf89f07bd3a7009ff0e8fa8
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647517"
---
# <a name="store-query-results-to-storage-using-sql-on-demand-preview-using-azure-synapse-analytics"></a>Uložení výsledků dotazu do úložiště pomocí SQL na vyžádání (ve verzi Preview) pomocí Azure synapse Analytics

V tomto článku se dozvíte, jak ukládat výsledky dotazu do úložiště pomocí SQL na vyžádání (Preview).

## <a name="prerequisites"></a>Požadavky

V prvním kroku si Projděte níže uvedené články a ujistěte se, že jste splnili požadavky:

- [Nastavení při prvním spuštění](query-data-storage.md#first-time-setup)
- [Požadavky](query-data-storage.md#prerequisites)

## <a name="create-external-table-as-select"></a>Vytvořit externí tabulku jako SELECT

K uložení výsledků dotazu do úložiště můžete použít příkaz vytvořit externí tabulku jako SELECT (CETAS).

> [!NOTE]
> Změňte první řádek v dotazu, tj., [mydbname], takže používáte databázi, kterou jste vytvořili. Pokud jste databázi nevytvořili, přečtěte si prosím [nastavení při prvním spuštění](query-data-storage.md#first-time-setup). Je potřeba změnit umístění pro externí zdroj dat MyDataSource, aby odkazovalo na umístění, pro které máte oprávnění k zápisu. 

```sql
USE [mydbname];
GO

CREATE DATABASE SCOPED CREDENTIAL [SasTokenWrite]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://<storage account name>.blob.core.windows.net/csv', CREDENTIAL [SasTokenWrite]
);
GO

CREATE EXTERNAL FILE FORMAT [ParquetFF] WITH (
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
);
GO

CREATE EXTERNAL TABLE [dbo].[PopulationCETAS] WITH (
        LOCATION = 'populationParquet/',
        DATA_SOURCE = [MyDataSource],
        FILE_FORMAT = [ParquetFF]
) AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix/population.csv',
        FORMAT='CSV'
    ) WITH (
        CountryCode varchar(4),
        CountryName varchar(64),
        Year int,
        PopulationCount int
    ) AS r;

```

## <a name="use-the-external-table"></a>Použití externí tabulky

Můžete použít externí tabulku vytvořenou prostřednictvím CETAS jako normální externí tabulku.

> [!NOTE]
> Změňte první řádek v dotazu, tj., [mydbname], takže používáte databázi, kterou jste vytvořili. Pokud jste databázi nevytvořili, přečtěte si prosím [nastavení při prvním spuštění](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM PopulationCETAS
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak zadávat dotazy na různé typy souborů, najdete v článcích [dotaz na jeden soubor CSV](query-single-csv-file.md), [soubory dotazů Parquet](query-parquet-files.md)a [dotazy na soubory JSON dotazu](query-json-files.md) .
