---
title: Vytváření, aktualizace statistik
description: Doporučení a příklady pro vytváření a aktualizaci statistik optimalizace dotazů v synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/19/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 5196c85ca1d68028893caee55035c6c455b37d64
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676940"
---
# <a name="statistics-in-synapse-sql"></a>Statistiky v Synapse SQL

K dispozici v tomto článku jsou doporučení a příklady pro vytváření a aktualizaci statistiky optimalizace dotazů pomocí synapse SQL prostředky: FOND SQL a SQL na vyžádání (náhled).

## <a name="statistics-in-sql-pool"></a>Statistiky ve fondu SQL

### <a name="why-use-statistics"></a>Proč používat statistiky

Čím více prostředek fondu SQL ví o vašich datech, tím rychleji může provádět dotazy. Po načtení dat do fondu SQL je shromažďování statistik o vašich datech jednou z nejdůležitějších věcí, které můžete udělat pro optimalizaci dotazů.  

Optimalizátor dotazů fondu SQL je optimalizátor založený na nákladech. Porovná náklady na různé plány dotazů a pak vybere plán s nejnižšími náklady. Ve většině případů zvolí plán, který provede nejrychlejší.

Pokud například optimalizátor odhadne, že datum filtrování dotazu vrátí jeden řádek, vybere jeden plán. Pokud odhadne, že vybrané datum vrátí 1 milion řádků, vrátí jiný plán.

### <a name="automatic-creation-of-statistics"></a>Automatické vytváření statistik

Fond SQL bude analyzovat příchozí dotazy uživatelů pro chybějící statistiky, pokud je nastavena možnost databáze AUTO_CREATE_STATISTICS na `ON`.  Pokud chybí statistiky, optimalizátor dotazů vytvoří statistiky jednotlivých sloupců v predikátu dotazu nebo podmínce spojení. Tato funkce se používá ke zlepšení odhady mohutnosti pro plán dotazu.

> [!IMPORTANT]
> Automatické vytváření statistik je aktuálně zapnuto ve výchozím nastavení.

Můžete zkontrolovat, zda váš datový sklad AUTO_CREATE_STATISTICS nakonfigurován spuštěním následujícího příkazu:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Pokud váš datový sklad nemá AUTO_CREATE_STATISTICS povolena, doporučujeme tuto vlastnost povolit spuštěním následujícího příkazu:

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

Tyto příkazy spustí automatické vytváření statistik:

- SELECT
- VLOŽIT-VYBRAT
- CTAS
- UPDATE
- DELETE
- Vysvětlete, kdy je detekována přítomnost spojení nebo přítomnost predikátu

> [!NOTE]
> Automatické vytváření statistik není generováno v dočasných nebo externích tabulkách.

Automatické vytváření statistik se provádí synchronně. Pokud tedy ve sloupcích chybí statistiky, může vám dojít k mírně snížení výkonu dotazu. Čas vytvoření statistiky pro jeden sloupec závisí na velikosti tabulky.

Chcete-li se vyhnout měřitelné snížení výkonu, měli byste zajistit, že statistiky byly vytvořeny nejprve spuštěním zatížení srovnávacího testu před profilováním systému.

> [!NOTE]
> Vytváření statistik je zaznamenáno v [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) v jiném uživatelském kontextu.

Při vytváření automatických statistik budou mít podobu: _WA_Sys_<id osmimístného sloupce v hex>_<8místné id tabulky v hex>. Již vytvořené statistiky můžete zobrazit spuštěním příkazu [SHOW_STATISTICS DBCC:](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

Table_name je název tabulky, která obsahuje statistiky, které se mají zobrazit, což nemůže být externí tabulka. Cílem je název cílového indexu, statistiky nebo sloupce, pro který chcete zobrazit statistické informace.

### <a name="update-statistics"></a>Aktualizovat statistiky

Jedním z osvědčených postupů je aktualizovat statistiky na datum sloupce každý den jako nová data jsou přidány. Při každém načtení nových řádků do datového skladu se přidají nová data načítání nebo data transakcí. Tyto dodatky změnit distribuci dat a statistiky zastaralé.

Statistiky sloupce země nebo oblasti v tabulce zákazníků nemusí být nikdy aktualizovány, protože rozdělení hodnot se obvykle nemění. Za předpokladu, že distribuce je konstantní mezi zákazníky, přidání nových řádků do tabulky varianty nezmění rozdělení dat.

Pokud však datový sklad obsahuje jenom jednu zemi nebo oblast a přinášíte data z nové země nebo oblasti, je třeba aktualizovat statistiky ve sloupci země nebo oblast.

Níže jsou uvedeny doporučení aktualizace statistiky:

|||
|-|-|
| **Četnost aktualizací statistik**  | Konzervativní: Denně </br> Po načtení nebo transformaci dat |
| **Vzorkování** |  Méně než 1 miliarda řádků, použijte výchozí vzorkování (20 procent). </br> S více než 1 miliarda řádků, použijte vzorkování dvou procent. |

### <a name="determine-last-statistics-update"></a>Určení poslední aktualizace statistiky

Jedna z prvních otázek, které je třeba položit při řešení potíží s dotazem, je **"Jsou statistiky aktuální?"**

Tato otázka není ten, který může být zodpovězena stáří dat. Aktuální objekt statistiky může být starý, pokud nedošlo k žádné podstatné změně podkladových dat. Pokud se podstatně změnil počet řádků nebo dojde ke změně materiálu v rozdělení hodnot pro *sloupec,* je čas aktualizovat statistiky.

K dispozici není dynamické zobrazení správy, které by určilo, zda se data v tabulce od poslední aktualizace statistik změnila. Znalost stáří vašich statistik vám může poskytnout část obrázku. Následující dotaz můžete použít k určení posledního aktualizace statistik v každé tabulce.

> [!NOTE]
> Pokud dojde k podstatné změně v rozdělení hodnot pro sloupec, měli byste aktualizovat statistiky bez ohledu na poslední aktualizaci.

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

**Sloupce kalendářních dat** v datovém skladu například obvykle vyžadují časté aktualizace statistik. Při každém načtení nových řádků do datového skladu se přidají nová data načítání nebo data transakcí. Tyto dodatky změnit distribuci dat a statistiky zastaralé.

Statistiky sloupce podle pohlaví v tabulce zákazníků nemusí být nikdy nutné aktualizovat. Za předpokladu, že distribuce je konstantní mezi zákazníky, přidání nových řádků do tabulky varianty nezmění rozdělení dat.

Pokud ale datový sklad obsahuje pouze jedno pohlaví a výsledkem nového požadavku je více pohlaví, je třeba aktualizovat statistiky sloupce pohlaví. Další informace naleznete v článku [Statistika.](/sql/relational-databases/statistics/statistics)

### <a name="implementing-statistics-management"></a>Implementace správy statistik

Často je vhodné rozšířit proces načítání dat, abyste zajistili, že statistiky budou aktualizovány na konci zatížení. Načítání dat je, když tabulky nejčastěji mění svou velikost, rozložení hodnot nebo obojí. Jako takové proces zatížení je logické místo pro implementaci některých procesů správy.

Následující hlavní zásady jsou uvedeny pro aktualizaci statistik během procesu načítání:

- Ujistěte se, že každá načtená tabulka má aktualizovaný alespoň jeden objekt statistiky. Tento proces aktualizuje informace o velikosti tabulky (počet řádků a počet stránek) jako součást aktualizace statistiky.
- Zaměřte se na sloupce, které se účastní klauzulí JOIN, GROUP BY, ORDER BY a DISTINCT.
- Zvažte aktualizaci sloupců "vzestupný klíč", jako jsou data transakcí častěji, protože tyto hodnoty nebudou zahrnuty do histogramu statistiky.
- Zvažte aktualizaci sloupců statické distribuce méně často.
- Nezapomeňte, že každý statistický objekt je aktualizován postupně. Jednoduchá implementace `UPDATE STATISTICS <TABLE_NAME>` není vždy ideální, zejména pro široké tabulky se spoustou objektů statistiky.

Další informace naleznete [v tématu Odhad mohutnosti](/sql/relational-databases/performance/cardinality-estimation-sql-server).

### <a name="examples-create-statistics"></a>Příklady: Vytvoření statistiky

Tyto příklady ukazují, jak používat různé možnosti pro vytváření statistik. Možnosti, které používáte pro každý sloupec, závisí na charakteristikách dat a na způsobu použití sloupce v dotazech.

#### <a name="create-single-column-statistics-with-default-options"></a>Vytvoření statistiky s jedním sloupcem s výchozími možnostmi

Chcete-li vytvořit statistiku ve sloupci, zadejte název objektu statistiky a název sloupce.
Tato syntaxe používá všechny výchozí možnosti. Ve výchozím nastavení fond SQL vzorky **20 procent** tabulky při vytváření statistiky.

```sql
CREATE STATISTICS [statistics_name]
    ON [schema_name].[table_name]([column_name]);
```

Příklad:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1);
```

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Vytvoření statistiky s jedním sloupcem prozkoumáním každého řádku

Výchozí vzorkovací frekvence 20 procent je dostatečná pro většinu situací. Můžete však upravit vzorkovací frekvenci. Chcete-li ochutnat celou tabulku, použijte tuto syntaxi:

```sql
CREATE STATISTICS [statistics_name]
    ON [schema_name].[table_name]([column_name])
    WITH FULLSCAN;
```

Příklad:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1)
    WITH FULLSCAN;
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Vytvoření jednosloupcové statistiky zadáním velikosti vzorku

Další možností, kterou máte, je určit velikost vzorku jako procento:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1)
    WITH SAMPLE = 50 PERCENT;
```

#### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Vytvoření statistiky s jedním sloupcem pouze u některých řádků

Můžete také vytvořit statistiky na část řádků v tabulce, která se nazývá filtrovaná statistika.

Filtrované statistiky můžete například použít při dotazování na určitý oddíl velké dělené tabulky. Vytvořením statistiky pouze o hodnotách oddílů se zlepší přesnost statistiky. Dojde také ke zlepšení výkonu dotazů.

Tento příklad vytvoří statistiky na rozsah hodnot. Hodnoty lze snadno definovat tak, aby odpovídaly rozsahu hodnot v oddílu.

```sql
CREATE STATISTICS stats_col1
    ON table1(col1)
    WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Pro optimalizaci dotazu zvážit použití filtrované statistiky při výběru plánu distribuovaných dotazů, dotaz musí přizpůsobit uvnitř definice objektu statistiky. Pomocí předchozího příkladu musí klauzule WHERE dotazu zadat hodnoty col1 mezi 2000101 a 20001231.

#### <a name="create-single-column-statistics-with-all-the-options"></a>Vytvoření jednosloupcových statistik se všemi možnostmi

Můžete také kombinovat možnosti dohromady. Následující příklad vytvoří objekt filtrované statistiky s vlastní velikostí vzorku:

```sql
CREATE STATISTICS stats_col1
    ON table1 (col1)
    WHERE col1 > '2000101' AND col1 < '20001231'
    WITH SAMPLE = 50 PERCENT;
```

Úplný odkaz naleznete v tématu [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

#### <a name="create-multi-column-statistics"></a>Vytvořit vícesloupcové statistiky

Chcete-li vytvořit objekt statistiky s více sloupci, použijte předchozí příklady, ale zadejte více sloupců.

> [!NOTE]
> Histogram, který se používá k odhadu počtu řádků ve výsledku dotazu, je k dispozici pouze pro první sloupec uvedený v definici objektu statistiky.

V tomto příkladu je histogram na *kategorii produktu\_*. Statistiky mezi sloupci se počítají podle *kategorie\_produktu* a sub_category *produktu\_*:

```sql
CREATE STATISTICS stats_2cols
    ON table1 (product_category, product_sub_category)
    WHERE product_category > '2000101' AND product_category < '20001231'
    WITH SAMPLE = 50 PERCENT;
```

Vzhledem k tomu, že existuje korelace mezi *kategorií produktu\_* a dílčí *\_\_kategorií produktu*, může být objekt statistiky s více sloupci užitečný, pokud jsou tyto sloupce přístupné současně.

#### <a name="create-statistics-on-all-columns-in-a-table"></a>Vytvoření statistiky všech sloupců v tabulce

Jedním ze způsobů, jak vytvořit statistiky, je vydat příkazy VYTVOŘIT STATISTIKU po vytvoření tabulky:

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

#### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Použití uložené procedury k vytvoření statistiky ve všech sloupcích v databázi

Fond SQL nemá systém uloženou proceduru ekvivalentní sp_create_stats na serveru SQL Server. Tato uložená procedura vytvoří objekt statistiky jednoho sloupce v každém sloupci databáze, který ještě nemá statistiky.
Následující příklad vám pomůže začít s návrhem databáze. Neváhejte a přizpůsobte ji vašim potřebám:

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default, 2 Fullscan, 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type IS NULL
BEGIN
    SET @create_type = 1;
END;

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN    sys.[external_tables] e    ON    e.[object_id]        = t.[object_id]
WHERE       l.[object_id] IS NULL
AND            e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+CONVERT(varchar(4),@sample_pct)+' PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Chcete-li vytvořit statistiku všech sloupců v tabulce pomocí výchozích hodnot, proveďte uloženou proceduru.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

Chcete-li vytvořit statistiku všech sloupců v tabulce pomocí úplného prohledávání, zavolejte tento postup:

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Chcete-li vytvořit ukázkové statistiky pro všechny sloupce v tabulce, zadejte 3 a procentuální procento vzorku. Níže uvedený postup používá vzorkovací frekvenci 20 procent.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

### <a name="examples-update-statistics"></a>Příklady: Aktualizovat statistiky

Chcete-li aktualizovat statistiky, můžete:

- Aktualizujte jeden objekt statistiky. Zadejte název objektu statistiky, který chcete aktualizovat.
- Aktualizujte všechny statistické objekty v tabulce. Zadejte název tabulky namísto jednoho konkrétního statistického objektu.

#### <a name="update-one-specific-statistics-object"></a>Aktualizace jednoho konkrétního statistického objektu

K aktualizaci určitého objektu statistiky použijte následující syntaxi:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Příklad:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Aktualizací konkrétních objektů statistiky můžete minimalizovat čas a prostředky potřebné ke správě statistik. Tato akce vyžaduje některé myšlenky pro výběr nejlepší statistiky objekty aktualizovat.

#### <a name="update-all-statistics-on-a-table"></a>Aktualizace všech statistik v tabulce

Jednoduchá metoda pro aktualizaci všech objektů statistiky v tabulce je:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Příklad:

```sql
UPDATE STATISTICS dbo.table1;
```

Příkaz UPDATE STATISTICS se snadno používá. Jen nezapomeňte, že aktualizuje *všechny* statistiky na stole, což vede k více práce, než je nutné. Pokud výkon není problém, tato metoda je nejjednodušší a nejúplnější způsob, jak zaručit, že statistiky jsou aktuální.

> [!NOTE]
> Při aktualizaci všech statistik v tabulce, fond SQL provádí prohledávání vzorkovat tabulku pro každý objekt statistiky. Pokud je tabulka velká a obsahuje mnoho sloupců a mnoho statistik, může být efektivnější aktualizovat jednotlivé statistiky na základě potřeby.

Implementace postupu naleznete `UPDATE STATISTICS` v tématu [Dočasné tabulky](develop-tables-temporary.md). Metoda implementace se mírně liší od `CREATE STATISTICS` předchozího postupu, ale výsledek je stejný.
Úplnou syntaxi naleznete v tématu [Aktualizace statistiky](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="statistics-metadata"></a>Metadata statistiky

Existuje několik systémových zobrazení a funkcí, které můžete použít k vyhledání informací o statistikách. Můžete například zjistit, zda může být objekt statistiky zastaralý pomocí funkce STATS_DATE(). STATS_DATE() umožňuje zobrazit, kdy byly statistiky naposledy vytvořeny nebo aktualizovány.

#### <a name="catalog-views-for-statistics"></a>Zobrazení katalogu pro statistiky

Tato zobrazení systému poskytují informace o statistikách:

| Zobrazení katalogu | Popis |
|:--- |:--- |
| [sys.columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Jeden řádek pro každý sloupec. |
| [Sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Jeden řádek pro každý objekt v databázi. |
| [sys.schémata](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Jeden řádek pro každé schéma v databázi. |
| [sys.statistiky](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Jeden řádek pro každý objekt statistiky. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Jeden řádek pro každý sloupec v objektu statistiky. Odkazy zpět na sys.columns. |
| [sys.tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Jeden řádek pro každou tabulku (obsahuje externí tabulky). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Jeden řádek pro každý datový typ. |

#### <a name="system-functions-for-statistics"></a>Systémové funkce pro statistiku

Tyto systémové funkce jsou užitečné pro práci se statistikami:

| Systémová funkce | Popis |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Datum poslední aktualizace objektu statistiky. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Souhrnná úroveň a podrobné informace o rozdělení hodnot podle statistického objektu. |

#### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Sloučení sloupců a funkcí statistiky do jednoho zobrazení

Toto zobrazení přináší sloupce, které se vztahují ke statistice a výsledky z funkce STATS_DATE() společně.

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_definition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON    co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm ON    tb.[schema_id]      = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

### <a name="dbcc-show_statistics-examples"></a>Příklady SHOW_STATISTICS(DBCC)

DBCC SHOW_STATISTICS() zobrazuje data uchovávaná v rámci statistického objektu. Tato data jsou dodávána ve třech částech:

- Hlavička
- Vektor hustoty
- Histogram

Záhlaví je metadata o statistiky. Histogram zobrazuje rozdělení hodnot v prvním sloupci klíče objektu statistiky. Vektor hustoty měří korelaci mezi sloupci. Fond SQL vypočítá počet odhadů mohutnosti s libovolnými daty v objektu statistiky.

#### <a name="show-header-density-and-histogram"></a>Zobrazit záhlaví, hustotu a histogram

Tento jednoduchý příklad ukazuje všechny tři části objektu statistiky:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Příklad:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

#### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>Zobrazit jednu nebo více částí dbcc SHOW_STATISTICS()

Pokud vás zajímají pouze některé části, `WITH` použijte klauzuli a určete, které části chcete zobrazit:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
    WITH stat_header, histogram, density_vector
```

Příklad:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1)
    WITH histogram, density_vector
```

### <a name="dbcc-show_statistics-differences"></a>DbCC SHOW_STATISTICS() rozdíly

`DBCC SHOW_STATISTICS()`je více přísně implementována ve fondu SQL ve srovnání s SQL Server:

- Nezdokumentované funkce nejsou podporovány.
- Nemůžu použít Stats_stream.
- Nelze spojit výsledky pro konkrétní podmnožiny dat statistiky. Například STAT_HEADER JOIN DENSITY_VECTOR.
- NO_INFOMSGS nelze nastavit pro potlačení zprávy.
- Čtvercové závorky kolem názvů statistik nelze použít.
- Nelze použít názvy sloupců k identifikaci objektů statistiky.
- Vlastní chyba 2767 není podporována.

### <a name="next-steps"></a>Další kroky

Další zlepšení výkonu dotazů najdete [v tématu Sledování pracovního vytížení](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="statistics-in-sql-on-demand-preview"></a>Statistiky v SQL na vyžádání (preview)

Statistiky jsou vytvářeny podle konkrétního sloupce pro konkrétní datovou sadu (cestu úložiště).

### <a name="why-use-statistics"></a>Proč používat statistiky

Čím více SQL na vyžádání (náhled) ví o vašich datech, tím rychleji může provádět dotazy proti němu. Shromažďování statistik o vašich datech je jednou z nejdůležitějších věcí, které můžete udělat pro optimalizaci dotazů. Optimalizátor dotazů SQL na vyžádání je optimalizátor založený na nákladech. Porovná náklady na různé plány dotazů a pak vybere plán s nejnižšími náklady. Ve většině případů zvolí plán, který provede nejrychlejší. Pokud například optimalizátor odhadne, že datum filtrování dotazu vrátí jeden řádek, vybere jeden plán. Pokud odhadne, že vybrané datum vrátí 1 milion řádků, vrátí jiný plán.

### <a name="automatic-creation-of-statistics"></a>Automatické vytváření statistik

SQL na vyžádání analyzuje příchozí dotazy uživatelů pro chybějící statistiky. Pokud chybí statistiky, optimalizátor dotazů vytvoří statistiky na jednotlivé sloupce v predikátu dotazu nebo spojit podmínku ke zlepšení mohutnost odhady pro plán dotazu.

Příkaz SELECT spustí automatické vytváření statistik.

> [!NOTE]
> Automatické vytváření statistik je zapnuto pro soubory parket. Pro soubory CSV je třeba vytvořit statistiky ručně, dokud není podporováno automatické vytváření statistik souborů CSV.

Automatické vytváření statistik se provádí synchronně, takže může dojít k mírně snížený výkon dotazu, pokud sloupce chybí statistiky. Čas vytvoření statistiky pro jeden sloupec závisí na velikosti cílových souborů.

### <a name="manual-creation-of-statistics"></a>Ruční tvorba statistik

SQL na vyžádání umožňuje vytvářet statistiky ručně. Pro soubory CSV je nutné vytvořit statistiky ručně, protože automatické vytváření statistik není pro soubory CSV zapnuto. Pokyny k ručnímu vytváření statistik najdete v následujících příkladech.

### <a name="updating-statistics"></a>Aktualizace statistik

Změny dat v souborech, odstranění a přidání souborů mají za následek změny distribuce dat a zastaraly se statistiky. V takovém případě je třeba aktualizovat statistiky.

SQL na vyžádání automaticky znovu vytvoří statistiky, pokud se výrazně změní data. Při každém automatickém vytvoření statistiky je uložen také aktuální stav datové sady: cesty k souborům, velikosti, data poslední změny.

Když jsou statistiky zastaralé, budou vytvořeny nové. Algoritmus prochází data a porovná je s aktuálním stavem datové sady. Pokud je velikost změn větší než konkrétní prahová hodnota, budou odstraněny staré statistiky a budou znovu vytvořeny přes novou datovou sadu.

Ruční statistiky nejsou nikdy deklarovány zastaralé.

> [!NOTE]
> Automatická rekreace statistik y je zapnuta pro parketové soubory. U souborů CSV je třeba přetáhnout a vytvořit statistiky ručně, dokud nebude podporováno automatické vytváření statistik souborů CSV. Podívejte se na níže uvedené příklady, jak přepadnout a vytvořit statistiky.

Jedna z prvních otázek, které je třeba položit při řešení potíží s dotazem, je **"Jsou statistiky aktuální?"**

Pokud se podstatně změnil počet řádků nebo došlo ke změně materiálu v rozdělení hodnot pro *sloupec,* je čas aktualizovat statistiky.

> [!NOTE]
> Pokud dojde k podstatné změně v rozdělení hodnot pro sloupec, měli byste aktualizovat statistiky bez ohledu na poslední aktualizaci.

### <a name="implementing-statistics-management"></a>Implementace správy statistik

Můžete chtít rozšířit datový kanál, abyste zajistili, že statistiky budou aktualizovány při výrazné změně dat přidáním, odstraněním nebo změnou souborů.

Pro aktualizaci statistik jsou uvedeny následující hlavní zásady:

- Ujistěte se, že datová sada má alespoň jeden objekt statistiky aktualizován. Tato aktualizace velikost (počet řádků a počet stránek) informace jako součást aktualizace statistiky.
- Zaměřte se na sloupce, které se účastní klauzulí JOIN, GROUP BY, ORDER BY a DISTINCT.
- Aktualizujte sloupce "vzestupný klíč", jako jsou data transakcí častěji, protože tyto hodnoty nebudou zahrnuty do histogramu statistiky.
- Aktualizovat statické distribuční sloupce méně často.

Další informace naleznete [v tématu Odhad mohutnosti](/sql/relational-databases/performance/cardinality-estimation-sql-server).

### <a name="examples-create-statistics-for-column-in-openrowset-path"></a>Příklady: Vytvoření statistiky pro sloupec v cestě OPENROWSET

Následující příklady ukazují, jak používat různé možnosti pro vytváření statistik. Možnosti, které používáte pro každý sloupec, závisí na charakteristikách dat a na způsobu použití sloupce v dotazech.

> [!NOTE]
> Statistiky s jedním sloupcem můžete vytvořit pouze v tomto okamžiku.

Následující uložená procedura se používá k vytvoření statistiky:

```sql
sys.sp_create_file_statistics [ @stmt = ] N'statement_text'
```

Argumenty: @stmt [ = N'statement_text' - Určuje příkaz Transact-SQL, který vrátí hodnoty sloupců, které se použijí pro statistiku. TableSAMPLE můžete použít k určení vzorků dat, která mají být použita. Pokud není zadán soubor TABLESAMPLE, bude použit soubor FULLSCAN.

```syntaxsql
<tablesample_clause> ::= TABLESAMPLE ( sample_number PERCENT )
```

> [!NOTE]
> Vzorkování CSV v tuto chvíli nefunguje, pro CSV je podporováno pouze FULLSCAN.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Vytvoření statistiky s jedním sloupcem prozkoumáním každého řádku

Chcete-li vytvořit statistiku ve sloupci, zadejte dotaz, který vrátí sloupec, pro který potřebujete statistiku.

Ve výchozím nastavení, pokud nezadáte jinak, SQL na vyžádání používá 100 % dat poskytnutých v datové sadě při vytváření statistiky.

Chcete-li například vytvořit statistiku s výchozími možnostmi (FULLSCAN) pro sloupec datové sady pro jeden rok založený na souboru population.csv:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_file_statistics N'SELECT year
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv'',
        FORMAT = ''CSV'',
        FIELDTERMINATOR ='','',
        ROWTERMINATOR = ''\n''
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
'
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Vytvoření jednosloupcové statistiky zadáním velikosti vzorku

Velikost vzorku můžete určit jako procento:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_file_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-update-statistics"></a>Příklady: Aktualizovat statistiky

Chcete-li aktualizovat statistiky, je třeba přetáhnout a vytvořit statistiky. Následující uložená procedura se používá k přetažení statistiky:

```sql
sys.sp_drop_file_statistics [ @stmt = ] N'statement_text'
```

Argumenty: @stmt [ = N'statement_text' - Určuje stejný příkaz Transact-SQL použitý při vytvoření statistiky.

Chcete-li aktualizovat statistiky pro sloupec rok v datové sadě, která je založena na souboru population.csv, musíte přetáhnout a vytvořit statistiky:

```sql
EXEC sys.sp_drop_file_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
GO

/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_file_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-create-statistics-for-external-table-column"></a>Příklady: Vytvoření statistiky pro sloupec externí tabulky

Následující příklady ukazují, jak používat různé možnosti pro vytváření statistik. Možnosti, které používáte pro každý sloupec, závisí na charakteristikách dat a na způsobu použití sloupce v dotazech.

> [!NOTE]
> Statistiky s jedním sloupcem můžete vytvořit pouze v tomto okamžiku.

Chcete-li vytvořit statistiku ve sloupci, zadejte název objektu statistiky a název sloupce.

```sql
CREATE STATISTICS statistics_name
ON { external_table } ( column )
    WITH
        { FULLSCAN
          | [ SAMPLE number PERCENT ] }
        , { NORECOMPUTE }
```

Argumenty: external_table Určuje externí tabulku, že by měly být vytvořeny statistiky.

FullSCAN Compute statistiky skenovánívšech řádků. FULLSCAN a VZOREK 100 procent mají stejné výsledky. FullScan nelze použít s volbou VZOREK.

Procento vzorkování Určuje přibližné procento nebo počet řádků v tabulce nebo v indexovaném zobrazení, které má optimalizátor dotazů použít při vytváření statistik. Číslo může být od 0 do 100.

Vzorek nelze použít s možností FULLSCAN.

> [!NOTE]
> Vzorkování CSV v tuto chvíli nefunguje, pro CSV je podporováno pouze FULLSCAN.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Vytvoření statistiky s jedním sloupcem prozkoumáním každého řádku

```sql
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH FULLSCAN, NORECOMPUTE
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Vytvoření jednosloupcové statistiky zadáním velikosti vzorku

```sql
-- following sample creates statistics with sampling 20%
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH SAMPLE 5 percent, NORECOMPUTE
```

### <a name="examples-update-statistics"></a>Příklady: Aktualizovat statistiky

Chcete-li aktualizovat statistiky, je třeba přetáhnout a vytvořit statistiky. Drop statistiky první:

```sql
DROP STATISTICS census_external_table.sState
```

A vytvářet statistiky:

```sql
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH FULLSCAN, NORECOMPUTE
```

## <a name="next-steps"></a>Další kroky

Další vylepšení výkonu dotazu naleznete [v tématu Doporučené postupy pro fond SQL](best-practices-sql-pool.md#maintain-statistics).
