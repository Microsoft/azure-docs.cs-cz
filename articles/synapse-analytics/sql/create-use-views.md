---
title: Vytváření a používání zobrazení v SQL na vyžádání (náhled)
description: V této části se dozvíte, jak vytvořit a použít zobrazení k zabalení dotazů SQL na vyžádání (náhled). Zobrazení vám umožní znovu použít tyto dotazy. Zobrazení jsou potřeba také v případě, že chcete používat nástroje, jako je Například Power BI, ve spojení s SQL na vyžádání.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0f5323193706fdd00739be6c71a4fe12cfedf21b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424535"
---
# <a name="create-and-use-views-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Vytváření a používání zobrazení v SQL na vyžádání (preview) pomocí Azure Synapse Analytics

V této části se dozvíte, jak vytvořit a použít zobrazení k zabalení dotazů SQL na vyžádání (náhled). Zobrazení vám umožní znovu použít tyto dotazy. Zobrazení jsou potřeba také v případě, že chcete používat nástroje, jako je Například Power BI, ve spojení s SQL na vyžádání.

## <a name="prerequisites"></a>Požadavky

Prvním krokem je projděte si níže uvedené články a ujistěte se, že jste splnili předpoklady pro vytváření a používání zobrazení SQL na vyžádání:

- [První nastavení](query-data-storage.md#first-time-setup)
- [Požadavky](query-data-storage.md#prerequisites)

## <a name="create-a-view"></a>Vytvoření zobrazení

Zobrazení můžete vytvářet stejným způsobem jako běžná zobrazení serveru SQL Server. Níže uvedený dotaz vytvoří zobrazení, které čte soubor *population.csv.*

> [!NOTE]
> Změňte první řádek v dotazu, tj., [mydbname], takže používáte databázi, kterou jste vytvořili. Pokud jste databázi nevytvořili, přečtěte si [první nastavení](query-data-storage.md#first-time-setup).

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

## <a name="use-a-view"></a>Použití zobrazení

Zobrazení v dotazech můžete použít stejným způsobem jako zobrazení v dotazech serveru SQL Server.

Následující dotaz ukazuje pomocí *zobrazení population_csv,* které jsme vytvořili v [příkazu Vytvořit zobrazení](#create-a-view). V roce 2019 vrací jména zemí s jejich počtem obyvatel v sestupném pořadí.

> [!NOTE]
> Změňte první řádek v dotazu, tj., [mydbname], takže používáte databázi, kterou jste vytvořili. Pokud jste databázi nevytvořili, přečtěte si [první nastavení](query-data-storage.md#first-time-setup).

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

Informace o tom, jak dotazovat různé typy souborů, naleznete v [článcích souboru Soubor u jednoho souboru CSV dotazu](query-single-csv-file.md), [soubory paretů dotazu](query-parquet-files.md)a soubory [DotazjSON.](query-json-files.md)
