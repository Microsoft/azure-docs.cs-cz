---
title: Dočasné tabulky v SQL Data Warehouse | Microsoft Docs
description: Základní pokyny pro používání dočasných tabulek a označuje se zásadami relace úrovni dočasných tabulek.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: a3e06a4074bc7b5cd8612162a624718107a50656
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31518395"
---
# <a name="temporary-tables-in-sql-data-warehouse"></a>Dočasné tabulky v SQL Data Warehouse
Tento článek obsahuje základní pokyny pro používání dočasných tabulek a zvýrazní se zásadami relace úrovni dočasných tabulek. Pomocí informací v tomto článku vám může pomoct rozčlenění kódu, vylepšení – opětovné použití a usnadnění údržby kódu na moduly.

## <a name="what-are-temporary-tables"></a>Jaké jsou dočasné tabulky?
Dočasné tabulky jsou užitečné při zpracování dat – zejména při transformace, které jsou přechodné mezilehlých výsledků. V SQL Data Warehouse existovat dočasné tabulky na úrovni relace.  Jsou viditelné pro relace, ve kterém byly vytvořeny a jsou automaticky zrušeny při odhlášení této relaci.  Dočasné tabulky nabízejí výhody výkonu, protože jejich výsledky se zapisují na místní místo vzdálené úložiště.  Dočasné tabulky jsou v Azure SQL Data Warehouse poněkud liší od Azure SQL Database, jako jsou dostupné z kdekoli v této relaci, včetně uvnitř i mimo ni uložené procedury.

## <a name="create-a-temporary-table"></a>Vytvořit dočasnou tabulku.
Dočasné tabulky se vytváří pomocí prefixu názvem tabulky `#`.  Příklad:

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

Dočasné tabulky můžete také vytvořit s `CTAS` pomocí přesně stejný přístup:

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
> `CTAS` je výkonný příkazu a má výhodu, probíhá efektivní v jeho použití místa protokolu transakcí. 
> 
> 

## <a name="dropping-temporary-tables"></a>Vyřazení dočasných tabulek
Když je vytvořena nová relace, musí existovat žádné dočasné tabulky.  Ale pokud jsou volání stejné uložené procedury, která vytvoří dočasný se stejným názvem, aby vaše `CREATE TABLE` příkazy jsou úspěšné kontrolu jednoduché předběžné existence s `DROP` lze použít jako v následujícím příkladu:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Pro kódování konzistence, je vhodné použít tento vzor pro tabulky a dočasných tabulek.  Je také vhodné použít `DROP TABLE` odebrat dočasných tabulek po dokončení s nimi ve vašem kódu.  V uložené proceduře vývoje je běžné zjistit že příkazů drop sdruženy na konci postup Ujistěte se, že jsou tyto objekty vyčistit.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Modularizing kódu
Vzhledem k tomu, že dočasných tabulek se zobrazí kdekoli v relaci uživatele, můžete to zneužití můžete rozčlenění moduly kódu aplikace.  Například následující uložené procedury generuje DDL a aktualizujte všechny statistiky v databázi tak, že název statistiky.

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

V této fázi je jedinou akcí, došlo k vytvoření uložené procedury, že generatess do dočasné tabulky #stats_ddl s příkazy DDL.  Tuto uloženou proceduru zahodí #stats_ddl, pokud již existuje a zda že neselže, je-li spustit více než jednou v rámci relace.  Ale vzhledem k tomu, že neexistuje žádné `DROP TABLE` na konci uložené procedury, při dokončení uložené procedury, zůstanou vytvořené tabulky tak, aby ho mohou číst mimo uložené procedury.  V SQL Data Warehouse na rozdíl od jiných databází systému SQL Server, je možné použít dočasnou tabulku mimo procedury, která ji vytvořila.  Je možné dočasných tabulek SQL Data Warehouse **kdekoli** uvnitř relace. To může vést k další modulární a spravovat kód jako v následujícím příkladu:

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

## <a name="temporary-table-limitations"></a>Dočasné tabulky omezení
SQL Data Warehouse zavádí několik omezení při implementaci dočasných tabulek.  V současné době pouze relace vymezená dočasných tabulek jsou podporovány.  Globální dočasné tabulky nejsou podporovány.  Kromě toho zobrazení nelze vytvořit v dočasných tabulkách.

## <a name="next-steps"></a>Další postup
Další informace o vývoji tabulky najdete v tématu [tabulky přehled](sql-data-warehouse-tables-overview.md).

