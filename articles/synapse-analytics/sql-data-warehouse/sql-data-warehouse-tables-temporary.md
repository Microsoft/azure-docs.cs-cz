---
title: Dočasné tabulky
description: Základní pokyny pro použití dočasných tabulek v Azure SQL Data Warehouse, zvýraznění principy relace na úrovni dočasných tabulek.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 3a8772550e67c250b1a84dbae17d1d3fe6c5c90e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351173"
---
# <a name="temporary-tables-in-sql-data-warehouse"></a>Dočasné tabulky v datovém skladu SQL
Tento článek obsahuje základní pokyny pro použití dočasných tabulek a zdůrazňuje zásady dočasné tabulky na úrovni relace. Pomocí informací v tomto článku vám může pomoci modularizovat kód, zlepšení opětovné použitelnosti a snadnou údržbu kódu.

## <a name="what-are-temporary-tables"></a>Co jsou dočasné tabulky?
Dočasné tabulky jsou užitečné při zpracování dat – zejména během transformace, kde jsou přechodné průběžné výsledky. V datovém skladu SQL existují dočasné tabulky na úrovni relace.  Jsou viditelné pouze pro relaci, ve které byly vytvořeny a jsou automaticky vynechány, když se relace odhlásí.  Dočasné tabulky nabízejí výhodu výkonu, protože jejich výsledky jsou zapsány do místního, nikoli vzdáleného úložiště.

## <a name="create-a-temporary-table"></a>Vytvoření dočasné tabulky
Dočasné tabulky jsou vytvářeny předponou `#`názvu tabulky pomocí aplikace .  Například:

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

Dočasné tabulky lze také `CTAS` vytvořit pomocí přesně stejný přístup:

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
;
``` 

> [!NOTE]
> `CTAS`je výkonný příkaz a má tu výhodu, že je efektivní při jeho využití prostoru transakční protokol. 
> 
> 

## <a name="dropping-temporary-tables"></a>Upuštění dočasných tabulek
Při vytvoření nové relace by neměly existovat žádné dočasné tabulky.  Pokud však voláte stejnou uloženou proceduru, která vytvoří dočasnou `CREATE TABLE` proceduru se stejným názvem, abyste `DROP` zajistili, že vaše příkazy jsou úspěšné, lze použít jednoduchou kontrolu před existencí s a jako v následujícím příkladu:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Pro konzistenci kódování je vhodné použít tento vzor pro tabulky i dočasné tabulky.  Je také vhodné použít `DROP TABLE` k odebrání dočasných tabulek po dokončení s nimi v kódu.  Ve vývoji uložené procedury je běžné zobrazit příkazy přetažení svázané dohromady na konci procedury, aby bylo zajištěno, že tyto objekty jsou vyčištěny.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Modularizace kódu
Vzhledem k tomu, že dočasné tabulky lze zobrazit kdekoli v relaci uživatele, to může být zneužita, které vám pomohou modularizovat kód aplikace.  Například následující uložená procedura generuje DDL aktualizovat všechny statistiky v databázi podle názvu statistiky.

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

V této fázi pouze akce, ke které došlo, je vytvoření uložené procedury, která generuje dočasnou tabulku, #stats_ddl, s ddl příkazy.  Tato uložená procedura klesne #stats_ddl pokud již existuje, aby bylo zajištěno, že neselže, pokud je spuštěna více než jednou v rámci relace.  Však vzhledem `DROP TABLE` k tomu, že neexistuje žádný na konci uložené procedury, po dokončení uložené procedury opustí vytvořené tabulky tak, aby ji lze číst mimo uložené procedury.  V SQL Data Warehouse, na rozdíl od jiných databází SQL Server, je možné použít dočasnou tabulku mimo proceduru, která ji vytvořila.  Dočasné tabulky SQL Data Warehouse lze použít **kdekoli** v rámci relace. To může vést k více modulární a spravovatelný kód jako v následujícím příkladu:

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

## <a name="temporary-table-limitations"></a>Dočasná omezení tabulek
SQL Data Warehouse ukládá několik omezení při implementaci dočasné tabulky.  V současné době jsou podporovány pouze dočasné tabulky s rozsahem relace.  Globální dočasné tabulky nejsou podporovány.  Kromě toho nelze vytvořit zobrazení v dočasných tabulkách.  Dočasné tabulky lze vytvořit pouze s hash nebo kruhové dotazování distribuce.  Replikované dočasné rozložení tabulky není podporováno. 

## <a name="next-steps"></a>Další kroky
Další informace o vývoji tabulek naleznete v tématu [Přehled tabulek](sql-data-warehouse-tables-overview.md).

