---
title: Použití dočasných tabulek v synapse SQL
description: Základní pokyny k používání dočasných tabulek v synapse SQL
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 4559c72481dfa0cefb2ce84cab56a50d0bf182ef
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90030323"
---
# <a name="temporary-tables-in-synapse-sql"></a>Dočasné tabulky v synapse SQL

Tento článek obsahuje základní pokyny k používání dočasných tabulek a zvýrazňuje principy dočasných tabulek úrovně relace v rámci synapse SQL. 

Prostředky ve fondu SQL i na vyžádání SQL (Preview) můžou využívat dočasné tabulky. SQL na vyžádání má omezení popsaná na konci tohoto článku. 

## <a name="temporary-tables"></a>Dočasné tabulky

Dočasné tabulky jsou užitečné při zpracování dat, zejména při transformaci, kde jsou mezilehlé výsledky přechodné. V synapse SQL existují dočasné tabulky na úrovni relace.  Jsou viditelné pouze v relaci, ve které byly vytvořeny. V takovém případě jsou automaticky vyhozeny při odhlášení relace. 

## <a name="temporary-tables-in-sql-pool"></a>Dočasné tabulky ve fondu SQL

V prostředku fondu SQL nabízí dočasné tabulky přínos pro zvýšení výkonu, protože jejich výsledky jsou zapisovány do místního úložiště místo vzdáleného úložiště.

### <a name="create-a-temporary-table"></a>Vytvoření dočasné tabulky

Dočasné tabulky jsou vytvářeny pomocí předpony názvu tabulky s `#` příponou.  Například:

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
> `CTAS` je účinný příkaz a má přidanou výhodu pro efektivní používání místa transakčního protokolu. 
> 
> 

### <a name="drop-temporary-tables"></a>Odstranit dočasné tabulky
Při vytvoření nové relace by neexistovaly žádné dočasné tabulky.  Nicméně pokud voláte stejnou uloženou proceduru, která vytvoří dočasný se stejným názvem, aby bylo zajištěno, že `CREATE TABLE` budou příkazy úspěšné, použijte jednoduchou kontrolu existence pomocí  `DROP` : 

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Pro konzistenci kódování je vhodným postupem použít tento model pro tabulky i dočasné tabulky.  Je také vhodné použít `DROP TABLE` k odebrání dočasných tabulek, až je budete mít hotovi.  

V případě vývoje uložených procedur je běžné vidět na konci postupu příkazy drop připojené dohromady, aby se tyto objekty vyčistily.

```sql
DROP TABLE #stats_ddl
```

### <a name="modularize-code"></a>Naplánovat modularizaci kód
Dočasné tabulky lze použít kdekoli v uživatelské relaci. Tato funkce se pak dá zneužít, aby vám pomohlo naplánovat modularizaci kód vaší aplikace.  Chcete-li předvést následující uloženou proceduru, vygeneruje DDL pro aktualizaci všech statistik v databázi podle názvu statistiky:

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

V této fázi je jediná akce, ke které došlo, vytvořit uloženou proceduru, která generuje #stats_ddl dočasnou tabulku.  Uložená procedura od#stats_ddl, pokud již existuje. Tím zajistíte, že při spuštění více než jednou v rámci relace dojde k chybě.  

Vzhledem k `DROP TABLE` tomu, že po dokončení uložené procedury není na konci uložené procedury, vytvořená tabulka zůstane a lze ji číst mimo uloženou proceduru.  

Na rozdíl od jiných databází SQL Server umožňuje synapse SQL použít dočasnou tabulku mimo postup, který ho vytvořil.  Dočasné tabulky vytvořené prostřednictvím fondu SQL lze použít **kdekoli** v relaci. V důsledku toho budete mít více modulárních a spravovatelných kódů, jak je znázorněno v následující ukázce:

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

### <a name="temporary-table-limitations"></a>Omezení dočasné tabulky

Fond SQL má několik omezení implementace pro dočasné tabulky:

- Jsou podporovány pouze dočasné tabulky s rozsahem relace.  Globální dočasné tabulky nejsou podporovány.
- V dočasných tabulkách nelze vytvořit zobrazení.
- Dočasné tabulky lze vytvořit pouze pomocí distribuce hash nebo kruhové dotazování.  Distribuce replikované dočasné tabulky není podporována. 

## <a name="temporary-tables-in-sql-on-demand-preview"></a>Dočasné tabulky na vyžádání SQL (Preview)

Dočasné tabulky na vyžádání SQL jsou podporovány, ale jejich použití je omezeno. Nelze je použít v dotazech, které cílí na soubory. 

Například nemůžete připojit dočasnou tabulku s daty ze souborů v úložišti. Počet dočasných tabulek je omezený na 100 a jejich celková velikost je omezená na 100 MB.

## <a name="next-steps"></a>Další kroky

Další informace o vývoji tabulek najdete v článku [navrhování tabulek pomocí prostředků SQL synapse](develop-tables-overview.md) .

