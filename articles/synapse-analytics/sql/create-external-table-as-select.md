---
title: Uložit výsledky dotazů do úložiště
description: V tomto článku se dozvíte, jak ukládat výsledky dotazů do úložiště pomocí SQL na vyžádání (náhled).
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 462185feb2b9cbebd17ce9cba54c2b23deea6c75
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81425144"
---
# <a name="store-query-results-to-storage-using-sql-on-demand-preview-using-azure-synapse-analytics"></a>Výsledky dotazů pro ukládání do úložiště pomocí SQL na vyžádání (preview) pomocí Azure Synapse Analytics

V tomto článku se dozvíte, jak ukládat výsledky dotazu do úložiště pomocí SQL On-demand (náhled).

## <a name="prerequisites"></a>Požadavky

Prvním krokem je projít si níže uvedené články a ujistit se, že jste splnili předpoklady:

- [První nastavení](query-data-storage.md#first-time-setup)
- [Požadavky](query-data-storage.md#prerequisites)

## <a name="create-external-table-as-select"></a>Vytvořit externí tabulku jako výběr

K uložení výsledků dotazu do úložiště můžete použít příkaz CREATE EXTERNAL TABLE AS SELECT (CETAS).

> [!NOTE]
> Změňte první řádek v dotazu, tj., [mydbname], takže používáte databázi, kterou jste vytvořili. Pokud jste databázi nevytvořili, přečtěte si [první nastavení](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net/csv'
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
        BULK 'https://showdemoweu.dfs.core.windows.net/data/population_csv/population.csv',
        FORMAT='CSV'
    ) WITH (
        CountryCode varchar(4),
        CountryName varchar(64),
        Year int,
        PopulationCount int
    ) AS r;

```

## <a name="use-a-external-table-created"></a>Použití vytvořené externí tabulky

Externí tabulku vytvořenou prostřednictvím CETAS můžete použít jako běžnou externí tabulku.

> [!NOTE]
> Změňte první řádek v dotazu, tj., [mydbname], takže používáte databázi, kterou jste vytvořili. Pokud jste databázi nevytvořili, přečtěte si [první nastavení](query-data-storage.md#first-time-setup).

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

Informace o tom, jak dotazovat různé typy souborů, naleznete v [článcích souboru Soubor u jednoho souboru CSV dotazu](query-single-csv-file.md), [soubory paretů dotazu](query-parquet-files.md)a soubory [DotazjSON.](query-json-files.md)
