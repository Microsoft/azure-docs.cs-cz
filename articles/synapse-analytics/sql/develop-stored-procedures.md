---
title: Použití uložených procedur
description: Tipy pro implementaci uložených procedur v synapse SQL pro vývoj řešení
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 09/23/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: f2046614f3665a699d02c76210676fb32f99fc73
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91288915"
---
# <a name="use-stored-procedures-in-synapse-sql"></a>Použití uložených procedur v synapse SQL

Tipy pro implementaci uložených procedur v synapse fondu SQL (datový sklad) pro vývoj řešení

## <a name="what-to-expect"></a>Co očekávat

Synapse SQL podporuje mnoho funkcí T-SQL, které se používají v SQL Server. Důležitější je, že existují určité funkce škálované na více instancí, které můžete použít k maximalizaci výkonu řešení.

Aby bylo možné zachovat rozsah a výkon fondu SQL, existují také některé funkce a funkce, které mají rozdíly v chování a jiné, které nejsou podporovány.

## <a name="stored-procedures-in-synapse-sql"></a>Uložené procedury v synapse SQL

Uložené procedury jsou skvělým způsobem, jak zapouzdřit kód SQL a uložit ho blízko k datům v datovém skladu. Uložené procedury usnadňují vývojářům naplánovat modularizaci jejich řešení zapouzdřením kódu do spravovatelných jednotek, což usnadňuje větší použitelnost kódu. Každý uložený postup může také přijímat parametry, aby byly ještě flexibilnější. V následujícím příkladu vidíte postupy, které vyřadí externí objekty, pokud existují v databázi:

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_tables WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL TABLE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_file_format_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_file_formats WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL FILE FORMAT ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_data_source_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_data_sources WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL DATA SOURCE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
```

Tyto postupy lze provést pomocí `EXEC` příkazu, kde můžete zadat název a parametry procedury:

```sql
EXEC drop_external_table_if_exists 'mytest';
EXEC drop_external_file_format_if_exists 'mytest';
EXEC drop_external_data_source_if_exists 'mytest';
```

Synapse SQL poskytuje zjednodušenou a zjednodušenou implementaci uložených procedur. Největší rozdíl v porovnání s SQL Server je, že uložená procedura není předem zkompilován kód. V datových skladech je čas kompilace malý v porovnání s časem potřebným ke spouštění dotazů na velké objemy dat. Je důležité zajistit, aby byl kód uložené procedury správně optimalizován pro velké dotazy. Cílem je ušetřit hodiny, minuty a sekundy, ne milisekundy. Je proto vhodnější si představit uložené procedury jako kontejnery pro SQL logiku.

Když synapse SQL provede uloženou proceduru, příkazy SQL se analyzují, překládají a optimalizují za běhu. Během tohoto procesu se každý příkaz převede na distribuované dotazy. Kód SQL, který je proveden na základě dat, je jiný než odeslaný dotaz.

## <a name="encapsulate-validation-rules"></a>Zapouzdřit ověřovací pravidla

Uložené procedury umožňují vyhledat logiku ověřování v jednom modulu uloženém v SQL Database. V následujícím příkladu můžete vidět, jak ověřit hodnoty parametrů a změnit jejich výchozí hodnoty.

```sql
CREATE PROCEDURE count_objects_by_date_created 
                            @start_date DATETIME2,
                            @end_date DATETIME2
AS BEGIN 

    IF( @start_date >= GETUTCDATE() )
    BEGIN
        THROW 51000, 'Invalid argument @start_date. Value should be in past.', 1;  
    END

    IF( @end_date IS NULL )
    BEGIN
        SET @end_date = GETUTCDATE();
    END

    IF( @start_date >= @end_date )
    BEGIN
        THROW 51000, 'Invalid argument @end_date. Value should be greater than @start_date.', 2;  
    END

    SELECT
         year = YEAR(create_date),
         month = MONTH(create_date),
         objects_created = COUNT(*)
    FROM
        sys.objects
    WHERE
        create_date BETWEEN @start_date AND @end_date
    GROUP BY
        YEAR(create_date), MONTH(create_date);
END
```

Logika v proceduře SQL ověří vstupní parametry při volání procedury.

```sql

EXEC count_objects_by_date_created '2020-08-01', '2020-09-01'

EXEC count_objects_by_date_created '2020-08-01', NULL

EXEC count_objects_by_date_created '2020-09-01', '2020-08-01'
-- Error
-- Invalid argument @end_date. Value should be greater than @start_date.

EXEC count_objects_by_date_created '2120-09-01', NULL
-- Error
-- Invalid argument @start_date. Value should be in past.
```

## <a name="nesting-stored-procedures"></a>Vnořování uložených procedur

Když uložené procedury volají jiné uložené procedury nebo spustí dynamický příkaz SQL, pak je vnitřní uložená procedura nebo volání kódu označována jako vnořená.
Příklad vnořené procedury je zobrazen v následujícím kódu:

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS BEGIN
    EXEC drop_external_table_if_exists @name;
    EXEC drop_external_file_format_if_exists @name;
    EXEC drop_external_data_source_if_exists @name;
END
```

Tento postup přijímá parametr, který představuje nějaký název, a poté volá další procedury pro vyřazení objektů s tímto názvem.
Synapse fond SQL podporuje maximálně osm úrovní vnoření. Tato funkce se mírně liší od SQL Server. Úroveň vnoření v SQL Server je 32.

Volání uložené procedury na nejvyšší úrovni je rovno vnořené úrovni 1.

```sql
EXEC clean_up 'mytest'
```

Pokud uložená procedura také provede další volání EXEC, úroveň vnořování se zvýší na 2.

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

Pokud druhý postup následně provede nějaký dynamický SQL, úroveň vnořování se zvýší na tři.

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    /* See full code in the previous example */
    EXEC sp_executesql @tsql = @drop_stmt;  -- This call is nest level 3
END
GO
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

> [!NOTE]
> Synapse SQL v současné době nepodporuje [@ @NESTLEVEL ](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Je nutné sledovat úroveň vnoření. Je pravděpodobné, že byste překročili osm omezení na úrovni vnoření, ale pokud to uděláte, budete muset znovu pracovat svůj kód, aby odpovídal úrovním vnoření v rámci tohoto limitu.

## <a name="insertexecute"></a>INSERT..EXEROZTOMILÁ

Synapse SQL neumožňuje využívat sadu výsledků uložené procedury pomocí příkazu INSERT. Existuje alternativní postup, který můžete použít. Příklad najdete v článku o [dočasných tabulkách](develop-tables-temporary.md).

## <a name="limitations"></a>Omezení

Existují některé aspekty uložených procedur v jazyce Transact-SQL, které nejsou implementovány v synapse SQL, například:

* dočasné uložené procedury
* číslované uložené procedury
* rozšířené uložené procedury
* Uložené procedury CLR
* možnost šifrování
* možnost replikace
* parametry s hodnotou tabulky
* parametry jen pro čtení
* výchozí parametry (v zřízeném fondu)
* kontexty spuštění
* return – příkaz

## <a name="next-steps"></a>Další kroky

Další tipy pro vývoj najdete v tématu [Přehled vývoje](develop-overview.md).
