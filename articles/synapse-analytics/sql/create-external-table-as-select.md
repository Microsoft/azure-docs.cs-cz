---
title: Ukládání výsledků dotazů do úložiště
description: V tomto článku se dozvíte, jak ukládat výsledky dotazu do úložiště pomocí SQL na vyžádání (Preview).
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 0e5fda5e7a5caf72df3d4c80e37a684232ca5541
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91289357"
---
# <a name="store-query-results-to-storage-using-sql-on-demand-preview-using-azure-synapse-analytics"></a>Uložení výsledků dotazu do úložiště pomocí SQL na vyžádání (ve verzi Preview) pomocí Azure synapse Analytics

V tomto článku se dozvíte, jak ukládat výsledky dotazu do úložiště pomocí SQL na vyžádání (Preview).

## <a name="prerequisites"></a>Předpoklady

Prvním krokem je **Vytvoření databáze** , ve které budete spouštět dotazy. Pak inicializujte objekty spuštěním [instalačního skriptu](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) v této databázi. Tento instalační skript vytvoří zdroje dat, přihlašovací údaje v oboru databáze a formáty externích souborů, které se použijí pro čtení dat v těchto ukázkách.

Při vytváření zdrojů dat, přihlašovacích údajů v oboru databáze a formátů externích souborů, které se používají k zápisu dat do výstupního úložiště, postupujte podle pokynů v tomto článku.

## <a name="create-external-table-as-select"></a>Vytvořit externí tabulku jako SELECT

K uložení výsledků dotazu do úložiště můžete použít příkaz vytvořit externí tabulku jako SELECT (CETAS).

> [!NOTE]
> Změňte první řádek v dotazu, tj., [mydbname], takže používáte databázi, kterou jste vytvořili.

```sql
USE [mydbname];
GO

CREATE DATABASE SCOPED CREDENTIAL [SasTokenWrite]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://<storage account name>.blob.core.windows.net/csv', CREDENTIAL = [SasTokenWrite]
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
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0'
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
> Změňte první řádek v dotazu, tj., [mydbname], takže používáte databázi, kterou jste vytvořili.

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
