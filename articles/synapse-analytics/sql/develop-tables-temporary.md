---
title: Použití dočasných tabulek se službou Synapse SQL
description: Základní pokyny pro použití dočasných tabulek v Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 090f453771dba6f537ad60605c6e9b96f3ca9957
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428755"
---
# <a name="temporary-tables-in-synapse-sql"></a>Dočasné tabulky v synapse SQL

Tento článek obsahuje základní pokyny pro použití dočasných tabulek a zdůrazňuje zásady dočasné tabulky na úrovni relace v rámci Synapse SQL. 

Prostředky fondu SQL a SQL na vyžádání (náhled) mohou využívat dočasné tabulky. SQL na vyžádání má omezení, které jsou popsány na konci tohoto článku. 

## <a name="what-are-temporary-tables"></a>Co jsou dočasné tabulky?

Dočasné tabulky jsou užitečné při zpracování dat, zejména během transformace, kde jsou přechodné průběžné výsledky. S Synapse SQL existují dočasné tabulky na úrovni relace.  Jsou viditelné pouze pro relaci, ve které byly vytvořeny. Jako takové jsou automaticky vynechány, když se relace odhlásí. 

## <a name="temporary-tables-in-sql-pool"></a>Dočasné tabulky ve fondu SQL

V prostředku fondu SQL dočasné tabulky nabízejí výhodu výkonu, protože jejich výsledky jsou zapsány do místní ho nikoli vzdáleného úložiště.

### <a name="create-a-temporary-table"></a>Vytvoření dočasné tabulky

Dočasné tabulky jsou vytvářeny předponou `#`názvu tabulky pomocí aplikace .  Příklad:

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

### <a name="dropping-temporary-tables"></a>Upuštění dočasných tabulek
Při vytvoření nové relace by neměly existovat žádné dočasné tabulky.  Pokud však voláte stejnou uloženou proceduru, která vytvoří dočasnou `CREATE TABLE` proceduru se stejným názvem, abyste `DROP`zajistili, že vaše příkazy budou úspěšné, použijte jednoduchou kontrolu před existencí s : 

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Pro konzistenci kódování je vhodné použít tento vzor pro tabulky i dočasné tabulky.  Je také vhodné použít `DROP TABLE` k odebrání dočasných tabulek po dokončení s nimi.  

Ve vývoji uložené procedury je běžné zobrazit příkazy přetažení svázané dohromady na konci procedury, aby bylo zajištěno, že tyto objekty jsou vyčištěny.

```sql
DROP TABLE #stats_ddl
```

### <a name="modularizing-code"></a>Modularizace kódu
Dočasné tabulky lze použít kdekoli v relaci uživatele. Tuto funkci pak můžete využít k tomu, aby vám pomohla modularizovat kód aplikace.  Chcete-li demonstrovat, následující uložená procedura generuje DDL aktualizovat všechny statistiky v databázi podle názvu statistiky:

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

V této fázi pouze akce, ke které došlo, je vytvoření uložené procedury, která generuje #stats_ddl dočasnou tabulku.  Uložená procedura klesne #stats_ddl pokud již existuje. Tento pokles zajišťuje, že neselže, pokud spustit více než jednou v rámci relace.  

Vzhledem k tomu, že není `DROP TABLE` na konci uložené procedury, po dokončení uložené procedury zůstane vytvořená tabulka a lze číst mimo uložené procedury.  

Na rozdíl od jiných databází SQL Server, Synapse SQL umožňuje použít dočasnou tabulku mimo postup, který ji vytvořil.  Dočasné tabulky vytvořené prostřednictvím fondu SQL lze použít **kdekoli** uvnitř relace. V důsledku toho budete mít více modulární a spravovatelný kód, jak je znázorněno v následující ukázce:

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

### <a name="temporary-table-limitations"></a>Dočasná omezení tabulek

Fond SQL má několik omezení implementace pro dočasné tabulky:

- Podporovány jsou pouze dočasné tabulky s rozsahem relace.  Globální dočasné tabulky nejsou podporovány.
- Zobrazení nelze vytvořit v dočasných tabulkách.
- Dočasné tabulky lze vytvořit pouze s hash nebo kruhové dotazování distribuce.  Replikované dočasné rozložení tabulky není podporováno. 

## <a name="temporary-tables-in-sql-on-demand-preview"></a>Dočasné tabulky v SQL na vyžádání (náhled)

Dočasné tabulky v SQL na vyžádání jsou podporovány, ale jejich využití je omezené. Nelze je použít v dotazech, které cílí na soubory. 

Například nelze připojit dočasnou tabulku s daty ze souborů v úložišti. Počet dočasných tabulek je omezen na 100 a jejich celková velikost je omezena na 100 MB.

## <a name="next-steps"></a>Další kroky

Další informace o vývoji tabulek najdete v [tématu Navrhování tabulek pomocí synapse sql prostředky](develop-tables-overview.md) článku.

