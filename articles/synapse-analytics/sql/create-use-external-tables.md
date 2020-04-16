---
title: Vytváření a používání externích tabulek v SQL na vyžádání (náhled)
description: V této části se dozvíte, jak vytvářet a používat externí tabulky v SQL na vyžádání (náhled). Externí tabulky jsou užitečné, když chcete řídit přístup k externím datům v SQL On-demand a pokud chcete používat nástroje, jako je Například Power BI, ve spojení s SQL na vyžádání.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a708e5e713e62ab5b7ca70b61235a84830b051cd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424549"
---
# <a name="create-and-use-external-tables-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Vytváření a používání externích tabulek v SQL na vyžádání (preview) pomocí Azure Synapse Analytics

V této části se dozvíte, jak vytvářet a používat externí tabulky v SQL na vyžádání (náhled). Externí tabulky jsou užitečné, když chcete řídit přístup k externím datům v SQL On-demand a pokud chcete používat nástroje, jako je Například Power BI, ve spojení s SQL na vyžádání.

## <a name="prerequisites"></a>Požadavky

Prvním krokem je projděte si níže uvedené články a ujistěte se, že jste splnili předpoklady pro vytváření a používání externích tabulek SQL na vyžádání:

- [První nastavení](query-data-storage.md#first-time-setup)
- [Požadavky](query-data-storage.md#prerequisites)

## <a name="create-an-external-table"></a>Vytvoření externí tabulky

Externí tabulky můžete vytvářet stejným způsobem jako běžné externí tabulky serveru SQL Server. Níže uvedený dotaz vytvoří externí tabulku, která čte soubor *population.csv.*

> [!NOTE]
> Změňte první řádek v dotazu, tj., [mydbname], takže používáte databázi, kterou jste vytvořili. Pokud jste databázi nevytvořili, přečtěte si [první nastavení](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

CREATE EXTERNAL DATA SOURCE [CsvDataSource] WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net/csv'
);
GO

CREATE EXTERNAL FILE FORMAT CSVFileFormat
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT,
    FORMAT_OPTIONS (
        FIELD_TERMINATOR = ',',
        STRING_DELIMITER = '"',
        FIRST_ROW = 2
    )
);
GO

CREATE EXTERNAL TABLE populationExternalTable
(
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
);
WITH (
    LOCATION = 'population/population.csv',
    DATA_SOURCE = CsvDataSource,
    FILE_FORMAT = CSVFileFormat
);
GO
```

## <a name="use-a-external-table"></a>Použití externí tabulky

Externí tabulky v dotazech můžete použít stejným způsobem jako v dotazech serveru SQL Server.

Následující dotaz ukazuje použití *základního externítabulky,* kterou jsme vytvořili v části [Vytvořit externí tabulku.](#create-an-external-table) V roce 2019 vrací jména zemí s jejich počtem obyvatel v sestupném pořadí.

> [!NOTE]
> Změňte první řádek v dotazu, tj., [mydbname], takže používáte databázi, kterou jste vytvořili. Pokud jste databázi nevytvořili, přečtěte si [první nastavení](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationExternalTable
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Další kroky

Informace o tom, jak ukládat výsledky dotazu do úložiště, naleznete ve [výsledcích dotazu store do úložiště](../sql/create-external-table-as-select.md).
