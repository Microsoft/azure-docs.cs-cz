---
title: Vytváření a používání zobrazení na vyžádání SQL na vyžádání (Preview)
description: V této části se dozvíte, jak vytvářet a používat zobrazení k zabalení dotazů SQL na vyžádání (Preview). Zobrazení vám umožní tyto dotazy znovu použít. Zobrazení jsou také nutná, pokud chcete používat nástroje, jako je například Power BI, ve spojení s SQL na vyžádání.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 508c12d89d5b0476e4cd4cbd8dc43b8bda8fbd90
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647475"
---
# <a name="create-and-use-views-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Vytváření a používání zobrazení v SQL na vyžádání (ve verzi Preview) pomocí Azure synapse Analytics

V této části se dozvíte, jak vytvářet a používat zobrazení k zabalení dotazů SQL na vyžádání (Preview). Zobrazení vám umožní tyto dotazy znovu použít. Zobrazení jsou také nutná, pokud chcete používat nástroje, jako je například Power BI, ve spojení s SQL na vyžádání.

## <a name="prerequisites"></a>Požadavky

Prvním krokem je vytvoření databáze, ve které se zobrazení vytvoří, a inicializace objektů potřebných k ověření v Azure Storage spuštěním [instalačního skriptu](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) v této databázi. Všechny dotazy v tomto článku se spustí v ukázkové databázi.

## <a name="create-a-view"></a>Vytvoření zobrazení

Zobrazení můžete vytvořit stejným způsobem jako běžné SQL Server zobrazení. Následující dotaz vytvoří zobrazení, které načte soubor *naplnění. csv* .

> [!NOTE]
> Změňte první řádek v dotazu, tj., [mydbname], takže používáte databázi, kterou jste vytvořili.

```sql
USE [mydbname];
GO

DROP VIEW IF EXISTS populationView;
GO

CREATE VIEW populationView AS
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
) AS [r];
```

Zobrazení v tomto příkladu používá `OPENROWSET` funkci, která používá absolutní cestu k podkladovým souborům. Pokud máte `EXTERNAL DATA SOURCE` s kořenovou adresou URL vašeho úložiště, můžete použít `OPENROWSET` s `DATA_SOURCE` a relativní cestou k souboru:

```
CREATE VIEW TaxiView
AS SELECT *, nyc.filepath(1) AS [year], nyc.filepath(2) AS [month]
FROM
    OPENROWSET(
        BULK 'parquet/taxi/year=*/month=*/*.parquet',
        DATA_SOURCE = 'sqlondemandstorage',
        FORMAT='PARQUET'
    ) AS nyc
```

## <a name="use-a-view"></a>Použití zobrazení

Zobrazení v dotazech můžete použít stejným způsobem jako zobrazení v SQL Serverch dotazech.

Následující dotaz znázorňuje použití zobrazení *population_csv* , které jsme vytvořili v části [Vytvoření zobrazení](#create-a-view). V sestupném pořadí vrátí názvy zemí se svými populacemi v 2019.

> [!NOTE]
> Změňte první řádek v dotazu, tj., [mydbname], takže používáte databázi, kterou jste vytvořili.

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationView
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Další kroky

Informace o tom, jak zadávat dotazy na různé typy souborů, najdete v článcích [dotaz na jeden soubor CSV](query-single-csv-file.md), [soubory dotazů Parquet](query-parquet-files.md)a [dotazy na soubory JSON dotazu](query-json-files.md) .
