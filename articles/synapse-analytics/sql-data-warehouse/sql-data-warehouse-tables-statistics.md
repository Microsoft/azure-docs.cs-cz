---
title: Vytváření, aktualizace statistik
description: Doporučení a příklady pro vytváření a aktualizaci statistikoptimalizace dotazů na tabulkách ve fondu SYNAPse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/09/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5fae2bba0acc4ab462c91f7272694d032fc6ceaa
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80742662"
---
# <a name="table-statistics-in-synapse-sql-pool"></a>Statistiky tabulek v fondu SYNAPse SQL

V tomto článku najdete doporučení a příklady pro vytváření a aktualizaci statistikoptimalizace dotazů v tabulkách ve fondu SQL.

## <a name="why-use-statistics"></a>Proč používat statistiky

Čím více fondu SQL ví o vašich datech, tím rychleji může provádět dotazy proti němu. Po načtení dat do fondu SQL je shromažďování statistik o vašich datech jednou z nejdůležitějších věcí, které můžete udělat pro optimalizaci dotazů.

Optimalizátor dotazů fondu SQL je optimalizátor založený na nákladech. Porovná náklady na různé plány dotazů a pak vybere plán s nejnižšími náklady. Ve většině případů zvolí plán, který provede nejrychlejší.

Pokud například optimalizátor odhadne, že datum filtrování dotazu vrátí jeden řádek, vybere jeden plán. Pokud odhadne, že vybrané datum vrátí 1 milion řádků, vrátí jiný plán.

## <a name="automatic-creation-of-statistic"></a>Automatické vytváření statistiky

Když je možnost AUTO_CREATE_STATISTICS databáze zapnutá, fond SQL analyzuje příchozí uživatelské dotazy na chybějící statistiky.

Pokud chybí statistiky, optimalizátor dotazů vytvoří statistiky na jednotlivé sloupce v predikátu dotazu nebo spojit podmínku ke zlepšení mohutnost odhady pro plán dotazu.

> [!NOTE]
> Automatické vytváření statistik je aktuálně zapnuto ve výchozím nastavení.

Pomocí následujícího příkazu můžete zkontrolovat, zda je váš fond SQL AUTO_CREATE_STATISTICS nakonfigurován:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Pokud váš fond SQL nemá AUTO_CREATE_STATISTICS nakonfigurován, doporučujeme tuto vlastnost povolit spuštěním následujícího příkazu:

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
> Automatické vytváření statistik není vytvořeno v dočasných nebo externích tabulkách.

Automatické vytváření statistik se provádí synchronně, takže může dojít k mírně snížený výkon dotazu, pokud sloupce chybí statistiky. Čas vytvoření statistiky pro jeden sloupec závisí na velikosti tabulky.

Chcete-li se vyhnout měřitelné snížení výkonu, měli byste zajistit, že statistiky byly vytvořeny nejprve spuštěním zatížení srovnávacího testu před profilováním systému.

> [!NOTE]
> Vytváření statistik bude zaznamenáno v [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) v jiném uživatelském kontextu.

Při vytváření automatických statistik budou mít podobu: _WA_Sys_<8místné id sloupce v Hex>_<8místné id tabulky v Hex>. Můžete zobrazit statistiky, které již byly vytvořeny spuštěním příkazu [DBCC SHOW_STATISTICS:](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

Table_name je název tabulky, která obsahuje statistiky, které chcete zobrazit. Tato tabulka nemůže být externí tabulka. Cílem je název cílového indexu, statistiky nebo sloupce, pro který chcete zobrazit statistické informace.

## <a name="update-statistics"></a>Aktualizovat statistiky

Jedním z osvědčených postupů je aktualizovat statistiky na datum sloupce každý den jako nová data jsou přidány. Při každém načtení nových řádků do fondu SQL jsou přidány nové data zatížení nebo data transakcí. Tyto dodatky změnit distribuci dat a statistiky zastaralé.

Statistiky sloupce země nebo oblasti v tabulce zákazníků nemusí být nikdy aktualizovány, protože rozdělení hodnot se obecně nemění. Za předpokladu, že distribuce je konstantní mezi zákazníky, přidání nových řádků do tabulky varianty nezmění rozdělení dat.

Pokud však fond SQL obsahuje pouze jednu zemi nebo oblast a přinášíte data z nové země nebo oblasti, což vede k ukládání dat z více zemí nebo oblastí, je třeba aktualizovat statistiky ve sloupci země nebo oblasti.

Níže jsou uvedeny doporučení aktualizace statistiky:

|||
|-|-|
| **Četnost aktualizací statistik**  | Konzervativní: Denně </br> Po načtení nebo transformaci dat |
| **Vzorkování** |  Méně než 1 miliarda řádků, použijte výchozí vzorkování (20 procent). </br> S více než 1 miliarda řádků, použijte vzorkování dvou procent. |

Jedna z prvních otázek, které je třeba položit při řešení potíží s dotazem, je **"Jsou statistiky aktuální?"**

Tato otázka není ten, který může být zodpovězena stáří dat. Aktuální objekt statistiky může být starý, pokud nedošlo k žádné podstatné změně podkladových dat.

> [!TIP]
> Pokud se podstatně změnil počet řádků nebo dojde k podstatné změně v rozdělení hodnot pro *sloupec,* je čas aktualizovat statistiky.

Neexistuje žádné dynamické zobrazení správy k určení, zda se data v tabulce změnila od poslední aktualizace statistik. Znalost stáří vašich statistik vám může poskytnout část obrázku.

Následující dotaz můžete použít k určení posledního aktualizace statistik v každé tabulce.

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

**Datum sloupce** ve fondu SQL, například obvykle potřebují časté aktualizace statistiky. Při každém načtení nových řádků do fondu SQL jsou přidány nové data zatížení nebo data transakcí. Tyto dodatky změnit distribuci dat a statistiky zastaralé.

Naopak statistiky sloupce pohlaví v tabulce zákazníků nemusí být nikdy nutné aktualizovat. Za předpokladu, že distribuce je konstantní mezi zákazníky, přidání nových řádků do tabulky varianty nezmění rozdělení dat.

Pokud váš fond SQL obsahuje pouze jedno pohlaví a nový požadavek má za následek více pohlaví, je třeba aktualizovat statistiky na sloupce pohlaví.

Další informace naleznete v obecných pokynech pro [statistiku](/sql/relational-databases/statistics/statistics?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="implementing-statistics-management"></a>Implementace správy statistik

Často je vhodné rozšířit proces načítání dat, abyste zajistili, že statistiky budou aktualizovány na konci zatížení.

Načítání dat je, když tabulky nejčastěji mění svou velikost nebo jejich rozložení hodnot. Načítání dat je logické místo pro implementaci některých procesů správy.

Následující hlavní zásady jsou uvedeny pro aktualizaci statistik během procesu načítání:

- Ujistěte se, že každá načtená tabulka má aktualizovaný alespoň jeden objekt statistiky. Tím se aktualizují informace o velikosti tabulky (počet řádků a počet stránek) jako součást aktualizace statistiky.
- Zaměřte se na sloupce, které se účastní klauzulí JOIN, GROUP BY, ORDER BY a DISTINCT.
- Zvažte aktualizaci sloupců "vzestupný klíč", jako jsou data transakcí častěji, protože tyto hodnoty nebudou zahrnuty do histogramu statistiky.
- Zvažte aktualizaci sloupců statické distribuce méně často.
- Nezapomeňte, že každý statistický objekt je aktualizován postupně. Jednoduchá implementace `UPDATE STATISTICS <TABLE_NAME>` není vždy ideální, zejména pro široké tabulky se spoustou objektů statistiky.

Další informace naleznete [v tématu Odhad mohutnosti](/sql/relational-databases/performance/cardinality-estimation-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="examples-create-statistics"></a>Příklady: Vytvoření statistiky

Tyto příklady ukazují, jak používat různé možnosti pro vytváření statistik. Možnosti, které používáte pro každý sloupec, závisí na charakteristikách dat a na způsobu použití sloupce v dotazech.

### <a name="create-single-column-statistics-with-default-options"></a>Vytvoření statistiky s jedním sloupcem s výchozími možnostmi

Chcete-li vytvořit statistiku ve sloupci, zadejte název objektu statistiky a název sloupce.

Tato syntaxe používá všechny výchozí možnosti. Ve výchozím nastavení fond SQL vzorky **20 procent** tabulky při vytváření statistiky.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Například:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="create-single-column-statistics-by-examining-every-row"></a>Vytvoření statistiky s jedním sloupcem prozkoumáním každého řádku

Výchozí vzorkovací frekvence 20 procent je dostatečná pro většinu situací. Můžete však upravit vzorkovací frekvenci.

Chcete-li ochutnat celou tabulku, použijte tuto syntaxi:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Například:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Vytvoření jednosloupcové statistiky zadáním velikosti vzorku

Alternativně můžete zadat velikost vzorku jako procento:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Vytvoření statistiky s jedním sloupcem pouze u některých řádků

Můžete také vytvořit statistiky na část řádků v tabulce. Tomu se říká filtrovaná statistika.

Filtrované statistiky můžete například použít při dotazování na určitý oddíl velké dělené tabulky. Vytvořením statistiky pouze hodnoty oddílu, přesnost statistiky zlepší a proto zlepšit výkon dotazu.

Tento příklad vytvoří statistiky na rozsah hodnot. Hodnoty lze snadno definovat tak, aby odpovídaly rozsahu hodnot v oddílu.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Pro optimalizaci dotazu zvážit použití filtrované statistiky při výběru plánu distribuovaných dotazů, dotaz musí přizpůsobit uvnitř definice objektu statistiky. Pomocí předchozího příkladu musí klauzule WHERE dotazu zadat hodnoty col1 mezi 2000101 a 20001231.

### <a name="create-single-column-statistics-with-all-the-options"></a>Vytvoření jednosloupcových statistik se všemi možnostmi

Můžete také kombinovat možnosti dohromady. Následující příklad vytvoří objekt filtrované statistiky s vlastní velikostí vzorku:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Úplný odkaz naleznete v tématu [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="create-multi-column-statistics"></a>Vytvořit vícesloupcové statistiky

Chcete-li vytvořit objekt statistiky s více sloupci, použijte předchozí příklady, ale zadejte více sloupců.

> [!NOTE]
> Histogram, který se používá k odhadu počtu řádků ve výsledku dotazu, je k dispozici pouze pro první sloupec uvedený v definici objektu statistiky.

V tomto příkladu je histogram na *kategorii produktu\_*. Statistiky mezi sloupci se počítají podle *kategorie\_produktu* a sub_category *produktu\_*:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Vzhledem k tomu, že existuje korelace mezi *kategorií produktu\_* a dílčí *\_\_kategorií produktu*, může být objekt statistiky s více sloupci užitečný, pokud jsou tyto sloupce přístupné současně.

### <a name="create-statistics-on-all-columns-in-a-table"></a>Vytvoření statistiky všech sloupců v tabulce

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

### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Použití uložené procedury k vytvoření statistiky ve všech sloupcích v databázi

Fond SQL nemá systém uloženou proceduru ekvivalentní sp_create_stats na serveru SQL Server. Tato uložená procedura vytvoří objekt statistiky jednoho sloupce v každém sloupci databáze, který ještě nemá statistiky.

Následující příklad vám pomůže začít s návrhem databáze. Neváhejte a přizpůsobte ji vašim potřebám.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
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

Chcete-li vytvořit statistiku všech sloupců v tabulce pomocí úplného prohledávání, zavolejte tento postup.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Chcete-li vytvořit ukázkové statistiky pro všechny sloupce v tabulce, zadejte 3 a procentuální procento vzorku. Tento postup používá vzorkovací frekvenci 20 procent.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

## <a name="examples-update-statistics"></a>Příklady: Aktualizovat statistiky

Chcete-li aktualizovat statistiky, můžete:

- Aktualizujte jeden objekt statistiky. Zadejte název objektu statistiky, který chcete aktualizovat.
- Aktualizujte všechny statistické objekty v tabulce. Zadejte název tabulky namísto jednoho konkrétního statistického objektu.

### <a name="update-one-specific-statistics-object"></a>Aktualizace jednoho konkrétního statistického objektu

K aktualizaci určitého objektu statistiky použijte následující syntaxi:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Například:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Aktualizací konkrétních objektů statistiky můžete minimalizovat čas a prostředky potřebné ke správě statistik. To vyžaduje některé myšlenky vybrat nejlepší statistiky objekty aktualizovat.

### <a name="update-all-statistics-on-a-table"></a>Aktualizace všech statistik v tabulce

Jednoduchá metoda pro aktualizaci všech objektů statistiky v tabulce je:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Například:

```sql
UPDATE STATISTICS dbo.table1;
```

Příkaz UPDATE STATISTICS se snadno používá. Jen nezapomeňte, že aktualizuje *všechny* statistiky na stole, a proto může provádět více práce, než je nutné. Pokud výkon není problém, je to nejjednodušší a nejúplnější způsob, jak zaručit, že statistiky jsou aktuální.

> [!NOTE]
> Při aktualizaci všech statistik v tabulce, fond SQL provádí prohledávání vzorkovat tabulku pro každý objekt statistiky. Pokud je tabulka velká a obsahuje mnoho sloupců a mnoho statistik, může být efektivnější aktualizovat jednotlivé statistiky na základě potřeby.

Implementace postupu naleznete `UPDATE STATISTICS` v tématu [Dočasné tabulky](sql-data-warehouse-tables-temporary.md). Metoda implementace se mírně liší od `CREATE STATISTICS` předchozího postupu, ale výsledek je stejný.

Úplnou syntaxi naleznete v tématu [Aktualizace statistiky](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="statistics-metadata"></a>Metadata statistiky

Existuje několik systémových zobrazení a funkcí, které můžete použít k vyhledání informací o statistikách. Můžete například zjistit, zda může být objekt statistiky zastaralý, pomocí funkce data statistiky, abyste zjistili, kdy byly statistiky naposledy vytvořeny nebo aktualizovány.

### <a name="catalog-views-for-statistics"></a>Zobrazení katalogu pro statistiky

Tato zobrazení systému poskytují informace o statistikách:

| Zobrazení katalogu | Popis |
|:--- |:--- |
| [sys.columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Jeden řádek pro každý sloupec. |
| [Sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Jeden řádek pro každý objekt v databázi. |
| [sys.schémata](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Jeden řádek pro každé schéma v databázi. |
| [sys.statistiky](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Jeden řádek pro každý objekt statistiky. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Jeden řádek pro každý sloupec v objektu statistiky. Odkazy zpět na sys.columns. |
| [sys.tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Jeden řádek pro každou tabulku (obsahuje externí tabulky). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Jeden řádek pro každý datový typ. |

### <a name="system-functions-for-statistics"></a>Systémové funkce pro statistiku

Tyto systémové funkce jsou užitečné pro práci se statistikami:

| Systémová funkce | Popis |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Datum poslední aktualizace objektu statistiky. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Souhrnná úroveň a podrobné informace o rozdělení hodnot podle statistického objektu. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Sloučení sloupců a funkcí statistiky do jednoho zobrazení

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
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

## <a name="dbcc-show_statistics-examples"></a>Příklady SHOW_STATISTICS(DBCC)

DBCC SHOW_STATISTICS() zobrazuje data uchovávaná v rámci statistického objektu. Tato data jsou dodávána ve třech částech:

- Hlavička
- Vektor hustoty
- Histogram

Metadata záhlaví o statistikách. Histogram zobrazuje rozdělení hodnot v prvním sloupci klíče objektu statistiky. Vektor hustoty měří korelaci mezi sloupci.

> [!NOTE]
> Fond SQL vypočítá počet odhadů mohutnosti s libovolnými daty v objektu statistiky.

### <a name="show-header-density-and-histogram"></a>Zobrazit záhlaví, hustotu a histogram

Tento jednoduchý příklad ukazuje všechny tři části objektu statistiky:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Například:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>Zobrazit jednu nebo více částí dbcc SHOW_STATISTICS()

Pokud vás zajímají pouze některé části, `WITH` použijte klauzuli a určete, které části chcete zobrazit:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Například:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-show_statistics-differences"></a>DbCC SHOW_STATISTICS() rozdíly

DBCC SHOW_STATISTICS() je více přísně implementována ve fondu SQL ve srovnání s SQL Server:

- Nezdokumentované funkce nejsou podporovány.
- Nelze použít Stats_stream.
- Nelze spojit výsledky pro konkrétní podmnožiny dat statistiky. Například STAT_HEADER JOIN DENSITY_VECTOR.
- NO_INFOMSGS nelze nastavit pro potlačení zprávy.
- Čtvercové závorky kolem názvů statistik nelze použít.
- Nelze použít názvy sloupců k identifikaci objektů statistiky.
- Vlastní chyba 2767 není podporována.

## <a name="next-steps"></a>Další kroky

Další zlepšení výkonu dotazů najdete [v tématu Sledování pracovního vytížení](sql-data-warehouse-manage-monitor.md)
