---
title: Dočasné tabulky v SQL Data Warehouse | Dokumentace Microsoftu
description: Základní pokyny k používání dočasné tabulky a vybraná vystoupení zásady relace úrovni dočasné tabulky.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: db13064c93381f87f82959ed3386abfc0a8e4593
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238658"
---
# <a name="temporary-tables-in-sql-data-warehouse"></a>Dočasné tabulky v SQL Data Warehouse
Tento článek obsahuje základní pokyny k používání dočasné tabulky a zvýrazní zásady relace úrovni dočasné tabulky. Pomocí informací v tomto článku vám může pomoct modularizaci vašeho kódu, vylepšení opětovné použití a snadnost údržby kódu.

## <a name="what-are-temporary-tables"></a>Jaké jsou dočasné tabulky?
Dočasné tabulky jsou užitečné při zpracování dat – zejména při transformaci, kde jsou přechodné mezilehlých výsledků. Ve službě SQL Data Warehouse dočasné tabulky existují na úrovni relace.  Jsou viditelné pouze do relace, ve kterém byly vytvořeny a jsou automaticky zrušeny při odhlášení relace.  Dočasné tabulky nabízejí zvýšení výkonu, protože jejich výsledky se zapisují na místní místo vzdálené úložiště.  Dočasné tabulky jsou ve službě Azure SQL Data Warehouse trochu liší od Azure SQL Database, jak k nim může přistupovat kdekoli uvnitř relace, včetně uvnitř i mimo uloženou proceduru.

## <a name="create-a-temporary-table"></a>Vytvořte dočasnou tabulku
Dočasné tabulky vytvářejí prefixu názvem vaší tabulky `#`.  Příklad:

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]        NVARCHAR(128) NOT NULL
,    [table_name]            NVARCHAR(128) NOT NULL
,    [stats_name]            NVARCHAR(128) NOT NULL
,    [stats_is_filtered]     BIT           NOT NULL
,    [seq_nmbr]              BIGINT        NOT NULL
,    [two_part_name]         NVARCHAR(260) NOT NULL
,    [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
```

Dočasné tabulky lze vytvořit také pomocí `CTAS` pomocí přesně stejný přístup:

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
``` 

> [!NOTE]
> `CTAS` příkaz pro výkonné a má výhodu současného jsou efektivní v jeho použití místo v protokolu transakcí. 
> 
> 

## <a name="dropping-temporary-tables"></a>Rušení dočasné tabulky
Když se vytvoří novou relaci, by měla existovat žádné dočasné tabulky.  Ale pokud voláte stejné uloženou proceduru, která vytvoří dočasnou se stejným názvem, aby vaše `CREATE TABLE` příkazy jsou úspěšné jednoduchého vyhledání před existenci s `DROP` lze použít jako v následujícím příkladu:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Pro kódování konzistence, je vhodné pro tento model se používá pro tabulky a dočasné tabulky.  Je také vhodné použít `DROP TABLE` odebrat dočasné tabulky po dokončení s nimi ve vašem kódu.  Při vývoji uložené procedury je běžné vidět, že příkazů drop spojeny dohromady na konci postupu zajistěte, aby že tyto objekty vyčistily.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Modularizing kódu
Protože dočasné tabulky lze zobrazit kdekoli v relaci uživatele, to je někdo zneužije umožňují modularizaci kódu aplikace.  Například generuje následující uložené procedury DDL aktualizovat všechny statistiky z databáze podle názvu statistiky.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

V této fázi je jedinou akcí, došlo k vytvoření uložené procedury této generatess dočasnou tabulku #stats_ddl s příkazy jazyka DDL.  Tuto uloženou proceduru #stats_ddl zahodí, pokud chcete zajistit, aby že neselže, je-li spustit více než jednou v rámci relace již existuje.  Ale protože neexistuje žádný `DROP TABLE` na konci uložené procedury, po dokončení uložené procedury, ponechá vytvořenou tabulku tak, aby je může přečíst mimo uloženou proceduru.  Ve službě SQL Data Warehouse na rozdíl od jiných databází systému SQL Server, je možné použít v dočasné tabulce mimo proces, který byl vytvořen.  SQL Data Warehouse dočasné tabulky je možné **kdekoli** uvnitř relace. To může vést k další modulární a dají se spravovat kód jako v následujícím příkladu:

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

## <a name="temporary-table-limitations"></a>Omezení dočasné tabulky
SQL Data Warehouse zavádí několik omezení při implementaci dočasné tabulky.  V současné době pouze relace s vymezeným oborem dočasných tabulek se podporují.  Globální dočasné tabulky nejsou podporovány.  Kromě toho nelze vytvořit zobrazení v dočasných tabulkách.

## <a name="next-steps"></a>Další postup
Další informace o vývoji tabulky, najdete v článku [Přehled tabulek](sql-data-warehouse-tables-overview.md).

