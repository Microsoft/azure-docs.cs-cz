---
title: Dočasné tabulky v SQL Data Warehouse | Microsoft Docs
description: Základní pokyny k používání dočasných tabulek a vysvětlují principy dočasných tabulek úrovně relace.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: e43e52e56ec7abbf5d8eb879defef54bd7d50658
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479833"
---
# <a name="temporary-tables-in-sql-data-warehouse"></a>Dočasné tabulky v SQL Data Warehouse
Tento článek obsahuje základní pokyny k používání dočasných tabulek a zvýrazňuje principy dočasných tabulek úrovně relace. Pomocí informací v tomto článku můžete naplánovat modularizaci svůj kód, což zlepšuje jak opětovné použitelnost, tak i jednoduchost údržby kódu.

## <a name="what-are-temporary-tables"></a>Co jsou dočasné tabulky?
Dočasné tabulky jsou užitečné při zpracování dat – zejména při transformaci, kde jsou mezilehlé výsledky přechodné. V SQL Data Warehouse na úrovni relace existují dočasné tabulky.  Jsou viditelné pouze v relaci, ve které byly vytvořeny, a při odhlášení se automaticky vynechává.  Dočasné tabulky nabízejí výkonnostní výhodu, protože jejich výsledky jsou zapisovány do místního úložiště místo vzdáleného úložiště.

## <a name="create-a-temporary-table"></a>Vytvořit dočasnou tabulku
Dočasné tabulky jsou vytvářeny pomocí předpony názvu tabulky s `#`příponou.  Příklad:

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

Dočasné tabulky lze také vytvořit s `CTAS` použitím naprosto stejného přístupu:

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
> `CTAS`je účinný příkaz a má přidanou výhodu pro efektivní používání místa transakčního protokolu. 
> 
> 

## <a name="dropping-temporary-tables"></a>Vyřazení dočasných tabulek
Při vytvoření nové relace by neexistovaly žádné dočasné tabulky.  Nicméně pokud voláte stejnou uloženou proceduru, která vytvoří dočasný se stejným názvem, aby bylo zajištěno, že jsou vaše `CREATE TABLE` příkazy úspěšné, je `DROP` možné použít jako v následujícím příkladu jednoduchou kontrolu před existencí.

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Pro konzistenci kódování je vhodným postupem použít tento model pro tabulky i dočasné tabulky.  Je také vhodné použít k odebrání dočasných tabulek `DROP TABLE` , pokud jste s nimi ve svém kódu dokončili práci.  V případě vývoje uložených procedur je běžné vidět na konci postupu příkazy drop připojené dohromady, aby se tyto objekty vyčistily.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Modularizing kód
Vzhledem k tomu, že dočasné tabulky lze najít kdekoli v uživatelské relaci, je možné ji zneužít, aby vám pomohlo naplánovat modularizaci kód aplikace.  Například následující uložená procedura generuje DDL pro aktualizaci všech statistik v databázi podle názvu statistiky.

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

V této fázi je jediná akce, ke které došlo, vytvořit uloženou proceduru, která generuje dočasnou tabulku #stats_ddl s příkazy DDL.  Tato uložená procedura #stats_ddl, pokud už existuje, aby se zajistilo, že selže, pokud se v rámci relace spustí více než jednou.  Vzhledem k tomu, že po `DROP TABLE` dokončení uložené procedury na konci uloženou proceduru neexistuje, opustí vytvořenou tabulku, aby ji bylo možné číst mimo uloženou proceduru.  V SQL Data Warehouse na rozdíl od jiných databází SQL Server je možné použít dočasnou tabulku mimo postup, který ho vytvořil.  SQL Data Warehouse dočasné tabulky lze použít **kdekoli** v relaci. To může vést k obecnější a spravovatelnému kódu, jak je uvedeno v následujícím příkladu:

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
SQL Data Warehouse při implementaci dočasných tabulek zavádí několik omezení.  V současné době jsou podporovány pouze dočasné tabulky s rozsahem relace.  Globální dočasné tabulky nejsou podporovány.  V dočasných tabulkách nelze navíc vytvořit zobrazení.  Dočasné tabulky lze vytvořit pouze pomocí distribuce hash nebo kruhové dotazování.  Distribuce replikované dočasné tabulky není podporována. 

## <a name="next-steps"></a>Další postup
Další informace o vývoji tabulek najdete v [přehledu tabulky](sql-data-warehouse-tables-overview.md).

