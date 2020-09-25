---
title: Vytváření a používání externích tabulek na vyžádání SQL (Preview)
description: V této části se dozvíte, jak vytvořit a používat externí tabulky v SQL na vyžádání (Preview).
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 182deba959144f6a3992bb41243f29023bad5e5c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91289323"
---
# <a name="create-and-use-external-tables-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Vytvoření a použití externích tabulek v SQL na vyžádání (ve verzi Preview) pomocí Azure synapse Analytics

V této části se dozvíte, jak vytvořit a používat [externí tabulky](develop-tables-external-tables.md) v SQL na vyžádání (Preview). Externí tabulky jsou užitečné, pokud chcete řídit přístup k externím datům v SQL na vyžádání a pokud chcete používat nástroje, jako je například Power BI, ve spojení s SQL na vyžádání. Externí tabulky mají přístup ke dvěma typům úložiště:
- Veřejné úložiště, kde uživatelé přistupují k souborům veřejného úložiště.
- Chráněné úložiště, kde uživatelé přistupují k souborům úložiště pomocí pověření SAS, identity Azure AD nebo spravované identity pracovního prostoru synapse.

## <a name="prerequisites"></a>Požadavky

Prvním krokem je vytvoření databáze, ve které budou vytvořeny tabulky. Pak inicializujte objekty spuštěním [instalačního skriptu](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) v této databázi. Tento instalační skript vytvoří následující objekty, které jsou použity v této ukázce:
- PŘIHLAŠOVACÍ údaje v oboru databáze `sqlondemand` , které umožňují přístup k `https://sqlondemandstorage.blob.core.windows.net` účtu úložiště Azure Protected SAS.

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL [sqlondemand]
    WITH IDENTITY='SHARED ACCESS SIGNATURE',  
    SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
    ```

- EXTERNÍ zdroj dat `sqlondemanddemo` , který odkazuje na ukázkový účet úložiště chráněný klíčem SAS, a externí zdroj dat `YellowTaxi` , který odkazuje na veřejně dostupný účet Azure Storage na místě `https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/` .

    ```sql
    CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
        LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
        CREDENTIAL = sqlondemand
    );
    GO
    CREATE EXTERNAL DATA SOURCE YellowTaxi
    WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/')
    ```

- Formáty souborů `QuotedCSVWithHeaderFormat` a `ParquetFormat` popisují typy souborů CSV a Parquet.

    ```sql
    CREATE EXTERNAL FILE FORMAT QuotedCsvWithHeaderFormat
    WITH (  
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( FIELD_TERMINATOR = ',', STRING_DELIMITER = '"', FIRST_ROW = 2   )
    );
    GO
    CREATE EXTERNAL FILE FORMAT ParquetFormat WITH (  FORMAT_TYPE = PARQUET );
    ```

Dotazy v tomto článku se spustí ve vzorové databázi a použijí tyto objekty. 

## <a name="create-an-external-table-on-protected-data"></a>Vytvoření externí tabulky v chráněných datech

Můžete vytvářet externí tabulky, které přistupují k datům v účtu služby Azure Storage, který umožňuje přístup k uživatelům s určitou identitou Azure AD nebo klíčem SAS. Externí tabulky můžete vytvořit stejným způsobem jako běžné SQL Server externích tabulkách. 

Následující dotaz vytvoří externí tabulku, která přečte *population.csv* soubor z ukázkového účtu Azure Storage SynapseSQL, na který se odkazuje pomocí `sqlondemanddemo` zdroje dat a který je chráněný pomocí pověření s oborem databáze s názvem `sqlondemand` . 

Přihlašovací údaje zdroje dat a oboru databáze se vytvoří ve [skriptu instalace](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql).

> [!NOTE]
> Změňte první řádek v dotazu, tj., [mydbname], takže používáte databázi, kterou jste vytvořili. 

```sql
USE [mydbname];
GO
CREATE EXTERNAL TABLE populationExternalTable
(
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
)
WITH (
    LOCATION = 'csv/population/population.csv',
    DATA_SOURCE = sqlondemanddemo,
    FILE_FORMAT = QuotedCSVWithHeaderFormat
);
```

## <a name="create-an-external-table-on-public-data"></a>Vytvoření externí tabulky pro veřejná data

Můžete vytvářet externí tabulky, které čtou data ze souborů umístěných do veřejně dostupného úložiště Azure. Tento [skript instalace](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) vytvoří veřejnou externí zdroj dat a Parquet definici formátu souboru, který se používá v následujícím dotazu:

```sql
CREATE EXTERNAL TABLE Taxi (
     vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
     pickup_datetime DATETIME2, 
     dropoff_datetime DATETIME2,
     passenger_count INT,
     trip_distance FLOAT,
     fare_amount FLOAT,
     tip_amount FLOAT,
     tolls_amount FLOAT,
     total_amount FLOAT
) WITH (
         LOCATION = 'puYear=*/puMonth=*/*.parquet',
         DATA_SOURCE = YellowTaxi,
         FILE_FORMAT = ParquetFormat
);
```
## <a name="use-an-external-table"></a>Použití externí tabulky

[Externí tabulky](develop-tables-external-tables.md) v dotazech můžete použít stejným způsobem jako v SQL Server dotazy.

Následující dotaz demonstruje to pomocí externí tabulky *obyvatel* , kterou jsme vytvořili v předchozí části. Vrátí názvy zemí nebo oblastí se svými populacemi v 2019 v sestupném pořadí.

> [!NOTE]
> Změňte první řádek v dotazu, tj., [mydbname], takže používáte databázi, kterou jste vytvořili.

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

Informace o tom, jak uložit výsledky dotazu do úložiště, najdete v článku [uložení výsledků dotazu](../sql/create-external-table-as-select.md) do úložiště.
