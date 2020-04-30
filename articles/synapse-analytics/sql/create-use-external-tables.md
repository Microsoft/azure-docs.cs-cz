---
title: Vytváření a používání externích tabulek na vyžádání SQL (Preview)
description: V této části se dozvíte, jak vytvořit a používat externí tabulky v SQL na vyžádání (Preview). Externí tabulky jsou užitečné, pokud chcete řídit přístup k externím datům v SQL na vyžádání a pokud chcete používat nástroje, jako je například Power BI, ve spojení s SQL na vyžádání.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a708e5e713e62ab5b7ca70b61235a84830b051cd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424549"
---
# <a name="create-and-use-external-tables-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Vytvoření a použití externích tabulek v SQL na vyžádání (ve verzi Preview) pomocí Azure synapse Analytics

V této části se dozvíte, jak vytvořit a používat externí tabulky v SQL na vyžádání (Preview). Externí tabulky jsou užitečné, pokud chcete řídit přístup k externím datům v SQL na vyžádání a pokud chcete používat nástroje, jako je například Power BI, ve spojení s SQL na vyžádání.

## <a name="prerequisites"></a>Požadavky

V prvním kroku si Projděte níže uvedené články a ujistěte se, že jste splnili předpoklady pro vytváření a používání externích tabulek SQL na vyžádání:

- [Nastavení při prvním spuštění](query-data-storage.md#first-time-setup)
- [Požadavky](query-data-storage.md#prerequisites)

## <a name="create-an-external-table"></a>Vytvoření externí tabulky

Externí tabulky můžete vytvořit stejným způsobem jako běžné SQL Server externích tabulkách. Následující dotaz vytvoří externí tabulku, která načte soubor *. csv* .

> [!NOTE]
> Změňte první řádek v dotazu, tj., [mydbname], takže používáte databázi, kterou jste vytvořili. Pokud jste databázi nevytvořili, přečtěte si prosím [nastavení při prvním spuštění](query-data-storage.md#first-time-setup).

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

Externí tabulky v dotazech můžete použít stejným způsobem jako v SQL Server dotazy.

Následující dotaz znázorňuje použití externí tabulky *napopulace* , kterou jsme vytvořili v části [vytvoření externí tabulky](#create-an-external-table) . V sestupném pořadí vrátí názvy zemí se svými populacemi v 2019.

> [!NOTE]
> Změňte první řádek v dotazu, tj., [mydbname], takže používáte databázi, kterou jste vytvořili. Pokud jste databázi nevytvořili, přečtěte si prosím [nastavení při prvním spuštění](query-data-storage.md#first-time-setup).

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

Informace o tom, jak uložit výsledky dotazu do úložiště, najdete v tématu úložiště [výsledků dotazu úložiště](../sql/create-external-table-as-select.md).
