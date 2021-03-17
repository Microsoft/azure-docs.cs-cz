---
title: Vytváření a používání zobrazení ve fondu SQL bez serveru
description: V této části se dozvíte, jak vytvářet a používat zobrazení k zabalení dotazů na fond SQL bez serveru. Zobrazení vám umožní tyto dotazy znovu použít. Zobrazení jsou nutná také v případě, že chcete používat nástroje, jako je například Power BI, společně s fondem SQL bez serveru.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 0948c7c82d7577bae07057bff9d1be4d7e09f978
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462288"
---
# <a name="create-and-use-views-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Vytváření a používání zobrazení pomocí neserverového fondu SQL ve službě Azure synapse Analytics

V této části se dozvíte, jak vytvářet a používat zobrazení k zabalení dotazů na fond SQL bez serveru. Zobrazení vám umožní tyto dotazy znovu použít. Zobrazení jsou nutná také v případě, že chcete používat nástroje, jako je například Power BI, společně s fondem SQL bez serveru.

## <a name="prerequisites"></a>Předpoklady

Prvním krokem je vytvoření databáze, ve které se zobrazení vytvoří, a inicializace objektů potřebných k ověření v Azure Storage spuštěním [instalačního skriptu](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) v této databázi. Všechny dotazy v tomto článku se spustí v ukázkové databázi.

## <a name="create-a-view"></a>Vytvoření zobrazení

Zobrazení můžete vytvořit stejným způsobem jako běžné SQL Server zobrazení. Následující dotaz vytvoří zobrazení, které čte soubor *population.csv* .

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
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
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

```sql
CREATE VIEW TaxiView
AS SELECT *, nyc.filepath(1) AS [year], nyc.filepath(2) AS [month]
FROM
    OPENROWSET(
        BULK 'parquet/taxi/year=*/month=*/*.parquet',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT='PARQUET'
    ) AS nyc
```

## <a name="use-a-view"></a>Použití zobrazení

Zobrazení v dotazech můžete použít stejným způsobem jako zobrazení v SQL Serverch dotazech.

Následující dotaz znázorňuje použití zobrazení *population_csv* , které jsme vytvořili v části [Vytvoření zobrazení](#create-a-view). Vrátí názvy zemí nebo oblastí se svými populacemi v 2019 v sestupném pořadí.

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
