---
title: Vytváření a aktualizace statistiky pomocí prostředků Azure synapse SQL
description: Doporučení a příklady pro vytváření a aktualizaci statistik pro optimalizaci dotazů v synapse SQL
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/19/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 6d59d64c861b74610e82b962ddd5db2331d3db64
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305018"
---
# <a name="statistics-in-synapse-sql"></a>Statistika v synapse SQL

V tomto článku najdete doporučení a příklady pro vytváření a aktualizaci statistik pro optimalizaci dotazů pomocí prostředků SQL synapse: vyhrazený fond SQL a SQL Server bez serveru (Preview).

## <a name="statistics-in-dedicated-sql-pool"></a>Statistika ve vyhrazeném fondu SQL

### <a name="why-use-statistics"></a>Proč používat statistiku

Čím více vyhrazený fond SQL ví o vašich datech, tím rychlejší může provádět dotazy. Po načtení dat do vyhrazeného fondu SQL je shromažďování statistických údajů o vašich datech jedním z nejdůležitějších věcí, které můžete udělat pro optimalizaci dotazů.  

Optimalizátor dotazů na vyhrazený fond SQL je modul pro optimalizaci na základě nákladů. Porovnává náklady na různé plány dotazů a pak zvolí plán s nejnižšími náklady. Ve většině případů si zvolí plán, který se spustí nejrychleji.

Například pokud Optimalizátor odhadne, že datum, na kterém dotaz vyfiltruje, vrátí jeden řádek, bude zvolit jeden plán. Pokud se odhaduje, že vybrané datum vrátí 1 000 000 řádků, vrátí se jiný plán.

### <a name="automatic-creation-of-statistics"></a>Automatické vytváření statistik

Vyhrazený modul SQL pool bude analyzovat příchozí dotazy uživatelů pro chybějící statistiky, pokud je možnost databáze AUTO_CREATE_STATISTICS nastavena na hodnotu `ON` .  Pokud Statistika chybí, vytvoří Optimalizátor dotazů statistiku pro jednotlivé sloupce v predikátu dotazu nebo v podmínce připojení. 

Tato funkce slouží ke zlepšení odhadů mohutnosti pro plán dotazu.

> [!IMPORTANT]
> Automatické vytváření statistik je teď ve výchozím nastavení zapnuté.

Spuštěním následujícího příkazu můžete ověřit, jestli je datový sklad AUTO_CREATE_STATISTICS.:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Pokud datový sklad nemá povolené AUTO_CREATE_STATISTICS, doporučujeme tuto vlastnost Povolit spuštěním následujícího příkazu:

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

Tyto příkazy spustí automatické vytváření statistik:

- SELECT
- VLOŽIT A VYBRAT
- CTAS
- UPDATE
- DELETE
- Vysvětlete, když je v něm obsažený JOIN nebo zda je zjištěna přítomnost predikátu.

> [!NOTE]
> Automatické vytváření statistik není vygenerováno pro dočasné nebo externí tabulky.

Automatické vytváření statistik je prováděno synchronně. V případě, že ve sloupcích chybí statistiky, může dojít k mírnému snížení výkonu dotazů. Čas vytvoření statistiky pro jeden sloupec závisí na velikosti tabulky.

Abyste se vyhnuli měřitelnému snížení výkonu, měli byste před profilací systému zajistit, aby byly nejprve vytvořeny statistiky spuštěním úlohy srovnávacích testů.

> [!NOTE]
> Vytváření statistik se přihlašuje [Sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) pod jiným uživatelským kontextem.

Při vytváření automatických statistik budou mít podobu: _WA_Sys_ id sloupce<8 číslic v šestnáctkovém>_<desítkové tabulce s číslem ID v šestnáctkové>. Již vytvořené statistiky můžete zobrazit spuštěním příkazu [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) :

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

Table_name je název tabulky, která obsahuje statistiku k zobrazení, která nemůže být externí tabulkou. Cíl je název cílového indexu, statistiky nebo sloupce, pro které chcete zobrazit informace o statistice.

### <a name="update-statistics"></a>Aktualizovat statistiku

Jedním z osvědčených postupů je aktualizovat statistiku pro sloupce data každý den, protože se přidávají nová data. Pokaždé, když se do datového skladu načtou nové řádky, přidají se nová data načítání nebo data transakcí. Tyto doplňky změní distribuci dat a zavedou statistiku.

Statistiky ve sloupci země nebo oblasti v tabulce zákazníků nemusí být nikdy potřeba aktualizovat, protože distribuce hodnot se většinou nemění. Za předpokladu, že je rozdělení mezi zákazníky konstantní, přidání nových řádků do varianty tabulky nemění distribuci dat.

Pokud však datový sklad obsahuje pouze jednu zemi nebo oblast a přinesete data z nové země nebo oblasti, je nutné aktualizovat statistiku ve sloupci země nebo oblast.

V následující části jsou doporučení aktualizující statistiky:

|||
|-|-|
| **Frekvence aktualizací statistiky**  | Konzervativní: denně </br> Po načtení nebo transformaci dat |
| **Vzorkování** |  Méně než 1 000 000 000 řádků, použijte výchozí vzorkování (20 procent). </br> S více než 1 000 000 000 řádky použijte vzorkování ze dvou procent. |

### <a name="determine-last-statistics-update"></a>Určení Poslední aktualizace statistiky

Jedna z prvních otázek, které je třeba položit při řešení potíží s dotazem, **jsou statistiky aktuální? "**

Tato otázka není ta, kterou by bylo možné zodpovědět o stáří dat. Aktuální objekt statistiky může být starý, pokud nedošlo ke změně materiálu podkladových dat. Když se počet řádků podstatně změnil, nebo dojde ke změně materiálu v distribuci hodnot pro *sloupec, je* čas aktualizovat statistiku.

K dispozici není zobrazení dynamické správy, které umožňuje určit, zda se data v tabulce od poslední aktualizace statistiky změnila. Znalost stáří statistik vám může poskytnout část obrázku. 

Pomocí následujícího dotazu můžete určit, kdy se vaše Statistika v každé tabulce aktualizovala.

> [!NOTE]
> Pokud dojde ke změně v distribuci hodnot pro sloupec, měli byste aktualizovat statistiku bez ohledu na čas poslední aktualizace.

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

**Sloupce** data v datovém skladu, například obvykle potřebují časté aktualizace statistiky. Pokaždé, když se do datového skladu načtou nové řádky, přidají se nová data načítání nebo data transakcí. Tyto doplňky změní distribuci dat a zavedou statistiku.

Statistiky ve sloupci pohlaví v tabulce zákazníků nemusí být nikdy potřeba aktualizovat. Za předpokladu, že je rozdělení mezi zákazníky konstantní, přidání nových řádků do varianty tabulky nemění distribuci dat.

Pokud ale váš datový sklad obsahuje jenom jedno pohlaví a nový požadavek má za následek více pohlaví, budete muset aktualizovat statistiku ve sloupci pohlaví. 

Další informace najdete v článku o [statistice](/sql/relational-databases/statistics/statistics) .

### <a name="implement-statistics-management"></a>Implementace správy statistik

Je často vhodné zvětšit proces načítání dat, aby bylo zajištěno, že statistika bude na konci zatížení aktualizována. Načítání dat je v případě, že tabulky nejčastěji mění velikost, distribuci hodnot nebo obojí. Jako takový je proces načítání logické místo pro implementaci některých procesů správy.

Následující principy GUID jsou k dispozici pro aktualizaci statistik během procesu načítání:

- Zajistěte, aby se v každé načtené tabulce aktualizoval alespoň jeden objekt statistiky. Tento proces aktualizuje informace o velikosti tabulky (počet řádků a počet stránek) jako součást aktualizace statistiky.
- Zaměřte se na sloupce účastnící se klauzulí JOIN, GROUP BY, ORDER BY a DISTINCT.
- Zvažte možnost aktualizovat sloupce vzestupného klíče, jako je například datum transakce častěji, protože tyto hodnoty nebudou zahrnuty do statistického histogramu.
- Zvažte možnost aktualizace statických distribučních sloupců méně často.
- Nezapomeňte, že každý objekt statistiky se aktualizuje v sekvenci. Pouhá implementace `UPDATE STATISTICS <TABLE_NAME>` není vždy ideální, zejména pro nejrůznější tabulky s velkým množstvím objektů statistiky.

Další informace najdete v tématu [odhad mohutnosti](/sql/relational-databases/performance/cardinality-estimation-sql-server).

### <a name="examples-create-statistics"></a>Příklady: vytváření statistik

Tyto příklady ukazují, jak používat různé možnosti vytváření statistik. Možnosti, které pro jednotlivé sloupce použijete, závisí na charakteristikách vašich dat a způsobu, jakým bude sloupec použit v dotazech.

#### <a name="create-single-column-statistics-with-default-options"></a>Vytváření statistik s jedním sloupcem s výchozími možnostmi

Chcete-li vytvořit statistiku pro sloupec, zadejte název objektu statistiky a název sloupce.
Tato syntaxe používá všechny výchozí možnosti. Ve výchozím nastavení vyhradí fond SQL Samples **20%** tabulky při vytváření statistik.

```sql
CREATE STATISTICS [statistics_name]
    ON [schema_name].[table_name]([column_name]);
```

Například:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1);
```

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Vytváření statistik s jedním sloupcem prozkoumáním každého řádku

Výchozí vzorkovací frekvence 20 procent je ve většině případů dostačující. Vzorkovací frekvenci ale můžete upravit. Chcete-li vzorkovat celou tabulku, použijte tuto syntaxi:

```sql
CREATE STATISTICS [statistics_name]
    ON [schema_name].[table_name]([column_name])
    WITH FULLSCAN;
```

Například:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1)
    WITH FULLSCAN;
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Vytvoření statistiky s jedním sloupcem zadáním velikosti vzorku

Další možností je určit velikost vzorku jako procento:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1)
    WITH SAMPLE = 50 PERCENT;
```

#### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Vytváření statistik s jedním sloupcem pouze na některých řádcích

Můžete také vytvořit statistiku pro část řádků v tabulce, která se nazývá filtrovaná Statistika.

Filtrované statistiky můžete například použít, pokud plánujete dotaz na konkrétní oddíl velké dělené tabulky. Když vytvoříte statistiku jenom pro hodnoty oddílu, vylepšit se přesnost statistik. Také dojde k vylepšení výkonu dotazů.

Tento příklad vytvoří statistiku pro rozsah hodnot. Hodnoty je možné snadno definovat tak, aby odpovídaly rozsahu hodnot v oddílu.

```sql
CREATE STATISTICS stats_col1
    ON table1(col1)
    WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Aby Optimalizátor dotazů při volbě plánu distribuovaného dotazu bral v úvahu vyfiltrované statistiky, dotaz se musí vejít do definice objektu statistiky. V předchozím příkladu je nutné, aby klauzule WHERE dotazu určovala hodnoty Sloupec1 mezi 2000101 a 20001231.

#### <a name="create-single-column-statistics-with-all-the-options"></a>Vytváření statistik s jedním sloupcem s využitím všech možností

Můžete také kombinovat možnosti dohromady. Následující příklad vytvoří objekt filtrované statistiky s vlastní velikostí vzorku:

```sql
CREATE STATISTICS stats_col1
    ON table1 (col1)
    WHERE col1 > '2000101' AND col1 < '20001231'
    WITH SAMPLE = 50 PERCENT;
```

Úplný odkaz naleznete v tématu [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

#### <a name="create-multi-column-statistics"></a>Vytváření statistik více sloupců

Chcete-li vytvořit objekt statistiky s více sloupci, použijte předchozí příklady, ale zadejte více sloupců.

> [!NOTE]
> Histogram, který se používá k odhadu počtu řádků ve výsledku dotazu, je k dispozici pouze pro první sloupec uvedený v definici objektu statistice.

V tomto příkladu je histogram v *\_ kategorii produktu*. Statistiky mezi sloupci se počítají podle *\_ kategorií produktů* a *\_ sub_category produktů* :

```sql
CREATE STATISTICS stats_2cols
    ON table1 (product_category, product_sub_category)
    WHERE product_category > '2000101' AND product_category < '20001231'
    WITH SAMPLE = 50 PERCENT;
```

Vzhledem k tomu, že existuje korelace mezi *\_ kategorií produktů* a *\_ \_ podkategoriím produktu* , může být objekt statistiky s více sloupci užitečný, pokud jsou k těmto sloupcům přistupovaly ve stejnou dobu.

#### <a name="create-statistics-on-all-columns-in-a-table"></a>Vytvořit statistiku pro všechny sloupce v tabulce

Jedním ze způsobů, jak vytvořit statistiku, je vystavit příkazy vytvořit STATISTIKu po vytvoření tabulky:

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

#### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Vytvoření statistiky pro všechny sloupce v databázi pomocí uložené procedury

Fond SQL neobsahuje systémovou uloženou proceduru, která odpovídá sp_create_stats v SQL Server. Tato uložená procedura vytvoří objekt statistiky s jedním sloupcem v každém sloupci databáze, který ještě nemá statistiku.

Následující příklad vám pomůže začít s návrhem databáze. Nebojte se, abyste ji přizpůsobili vašim potřebám:

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

Chcete-li vytvořit statistiku pro všechny sloupce v tabulce pomocí výchozích hodnot, spusťte uloženou proceduru.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

Chcete-li vytvořit statistiku pro všechny sloupce v tabulce pomocí FULLSCAN, zavolejte tuto proceduru:

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Chcete-li vytvořit ukázkové statistiky pro všechny sloupce v tabulce, zadejte hodnotu 3 a vzorek procento. Následující postup používá vzorkovací frekvenci 20 procent.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

### <a name="examples-update-statistics"></a>Příklady: aktualizace statistiky

Chcete-li aktualizovat statistiku, můžete:

- Aktualizujte jeden objekt statistiky. Zadejte název objektu statistiky, který chcete aktualizovat.
- Aktualizuje všechny objekty statistik v tabulce. Místo jednoho konkrétního objektu statistiky zadejte název tabulky.

#### <a name="update-one-specific-statistics-object"></a>Aktualizace jednoho konkrétního objektu statistiky

K aktualizaci konkrétního objektu statistiky použijte následující syntax:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Například:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Aktualizací specifických objektů statistiky můžete minimalizovat čas a prostředky potřebné ke správě statistik. Tato akce vyžaduje, aby se vybraly vhodné objekty statistiky, které se mají aktualizovat.

#### <a name="update-all-statistics-on-a-table"></a>Aktualizace všech statistik v tabulce

Jednoduchá metoda aktualizace všech objektů statistiky v tabulce je:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Například:

```sql
UPDATE STATISTICS dbo.table1;
```

Příkaz Aktualizovat STATISTIKu je snadno použitelný. Stačí si pamatovat, že aktualizuje *všechny* statistiky v tabulce a zobrazí se dotazování více práce, než je nutné. 

Pokud výkon není problémem, je tato metoda nejjednodušší a nejucelenější způsob, jak zaručit, že statistiky jsou aktuální.

> [!NOTE]
> Při aktualizaci všech statistik v tabulce provede vyhrazený fond SQL kontrolu na vzorové tabulce pro každý objekt statistiky. Pokud je tabulka velká a má mnoho sloupců a mnoho statistik, může být efektivnější aktualizovat jednotlivé statistiky podle potřeby.

Implementaci `UPDATE STATISTICS` procedury najdete v tématu [dočasné tabulky](develop-tables-temporary.md). Metoda implementace je mírně odlišná od předchozího `CREATE STATISTICS` postupu, ale výsledek je stejný.
Úplnou syntaxi naleznete v tématu [UPDATE STATISTICS](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="statistics-metadata"></a>Statistika – metadata

K dispozici je několik systémových zobrazení a funkcí, které můžete použít k vyhledání informací o statistice. Například můžete zjistit, zda je objekt statistiky pravděpodobně zastaralý, pomocí funkce STATS_DATE (). STATS_DATE () umožňuje zobrazit, kdy byla statistika naposledy vytvořena nebo aktualizována.

#### <a name="catalog-views-for-statistics"></a>Zobrazení katalogu pro statistiku

Tato systémová zobrazení obsahují informace o statistice:

| Zobrazení katalogu | Popis |
|:--- |:--- |
| [sys. Columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Jeden řádek pro každý sloupec. |
| [sys. Objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Jeden řádek pro každý objekt v databázi. |
| [sys. schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Jeden řádek pro každé schéma v databázi. |
| [sys. stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Jeden řádek pro každý objekt statistiky. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Jeden řádek pro každý sloupec v objektu statistiky. Vrátí odkazy zpět na sys. Columns. |
| [sys. Tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Jeden řádek pro každou tabulku (zahrnuje externí tabulky). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Jeden řádek pro každý datový typ. |

#### <a name="system-functions-for-statistics"></a>Systémové funkce pro statistiku

Tyto systémové funkce jsou užitečné pro práci s statistikami:

| Systémová funkce | Popis |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Datum poslední aktualizace objektu statistiky |
| [PŘÍKAZ DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Souhrnná úroveň a podrobné informace o distribuci hodnot, které přirozuměl objektům statistiky. |

#### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Kombinovat statistické sloupce a funkce do jednoho zobrazení

Toto zobrazení přináší sloupce, které se vztahují ke statistikám a výsledkům funkce STATS_DATE () společně.

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

### <a name="dbcc-show_statistics-examples"></a>Příklady příkazu DBCC SHOW_STATISTICS ()

Příkaz DBCC SHOW_STATISTICS () zobrazuje data uchovávaná v rámci objektu statistiky. Tato data jsou dodávána se třemi částmi:

- Záhlaví
- Vektor hustoty
- Histogram

Záhlaví je metadata o statistice. Histogram zobrazí distribuci hodnot v prvním klíčovém sloupci objektu statistiky. 

Vektor hustoty měří korelaci mezi sloupci. Vyhrazený fond SQL vypočítává odhady mohutnosti s libovolnými daty v objektu statistiky.

#### <a name="show-header-density-and-histogram"></a>Zobrazit záhlaví, hustotu a histogram

Tento jednoduchý příklad ukazuje všechny tři části objektu statistiky:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Například:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

#### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>Zobrazit jednu nebo více částí příkazu DBCC SHOW_STATISTICS ()

Pokud vás zajímá jenom zobrazení konkrétních částí, použijte `WITH` klauzuli a určete, které části chcete zobrazit:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
    WITH stat_header, histogram, density_vector
```

Například:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1)
    WITH histogram, density_vector
```

### <a name="dbcc-show_statistics-differences"></a>Rozdíly DBCC SHOW_STATISTICS ()

`DBCC SHOW_STATISTICS()` ve srovnání s SQL Server je ve vyhrazeném fondu SQL striktní implementace:

- Nedokumentované funkce se nepodporují.
- Nelze použít Stats_stream.
- Nelze spojit výsledky pro konkrétní podmnožiny dat statistiky. Například STAT_HEADER JOIN DENSITY_VECTOR.
- Nelze nastavit NO_INFOMSGS pro potlačení zprávy.
- Nedají se použít hranaté závorky kolem názvů statistických údajů.
- K identifikaci objektů statistiky nelze použít názvy sloupců.
- Vlastní chyba 2767 není podporována.


## <a name="statistics-in-serverless-sql-pool-preview"></a>Statistika ve fondu SQL bez serveru (Preview)

Statistiky se pro konkrétní datovou sadu (cestu v úložišti) vytvoří na konkrétní sloupec.

### <a name="why-use-statistics"></a>Proč používat statistiku

Čím více bez serveru SQL (Preview) ví o vašich datech, tím rychleji může na něm provádět dotazy. Shromažďování statistických údajů o vašich datech je jedním z nejdůležitějších věcí, které můžete udělat k optimalizaci vašich dotazů. 

Optimalizátor dotazů fondu SQL bez serveru je modul pro optimalizaci na základě nákladů. Porovnává náklady na různé plány dotazů a pak zvolí plán s nejnižšími náklady. Ve většině případů si zvolí plán, který se spustí nejrychleji. 

Například pokud Optimalizátor odhadne, že datum, na kterém dotaz vyfiltruje, vrátí jeden řádek, ve kterém se vybere jeden plán. Pokud se odhaduje, že vybrané datum vrátí 1 000 000 řádků, vrátí se jiný plán.

### <a name="automatic-creation-of-statistics"></a>Automatické vytváření statistik

Neserverový fond SQL analyzuje příchozí dotazy uživatelů na chybějící statistiky. Pokud Statistika chybí, vytvoří Optimalizátor dotazů statistiku pro jednotlivé sloupce v predikátu dotazu nebo podmínky spojení ke zvýšení odhadů mohutnosti pro plán dotazu.

Příkaz SELECT spustí automatické vytváření statistik.

> [!NOTE]
> Pro soubory Parquet je zapnuté automatické vytváření statistik. Pro soubory CSV je potřeba vytvořit statistiku ručně, dokud se nepodporují automatické vytváření statistik souborů CSV.

Automatické vytváření statistik je prováděno synchronně, takže pokud ve sloupcích chybí statistiky, může dojít k mírnému snížení výkonu dotazů. Čas vytvoření statistiky pro jeden sloupec závisí na velikosti cílových souborů.

### <a name="manual-creation-of-statistics"></a>Ruční vytváření statistik

Neserverový fond SQL umožňuje ruční vytváření statistik. Pro soubory CSV je nutné vytvořit statistiku ručně, protože automatické vytváření statistik není pro soubory CSV zapnuté. 

Pokyny k ručnímu vytváření statistik najdete v následujících příkladech.

### <a name="update-statistics"></a>Aktualizovat statistiku

Změny dat v souborech, odstraňování a přidávání souborů způsobují změnu distribuce dat a zajišťují jejich statistiku. V takovém případě je třeba aktualizovat statistiku.

Fond SQL bez serveru automaticky znovu vytvoří statistiku, pokud se data významně mění. Pokaždé, když se automaticky vytvoří statistiky, aktuální stav datové sady se taky uloží: cesty k souborům, velikosti, data poslední úpravy.

Když jsou statistiky zastaralé, vytvoří se nové. Algoritmus prochází daty a porovnává je s aktuálním stavem datové sady. Pokud je velikost změn větší než konkrétní prahová hodnota, pak jsou staré statistiky odstraněny a znovu se vytvoří nad novou datovou sadou.

Ruční Statistika není nikdy deklarována jako zastaralá.

> [!NOTE]
> Pro soubory Parquet se zapne automatické opětovné vytvoření statistiky. V případě souborů CSV je nutné ručně vyřadit a vytvářet statistiky, dokud nebude podporováno automatické vytváření statistik souborů CSV. Podívejte se na příklady, jak vyřadit a vytvořit statistiku.

Jedna z prvních otázek, které je třeba položit při řešení potíží s dotazem, **jsou statistiky aktuální? "**

Když se počet řádků podstatně změnil nebo dojde ke změně v distribuci hodnot pro *sloupec, je* čas aktualizovat statistiku.

> [!NOTE]
> Pokud dojde ke změně v distribuci hodnot pro sloupec, měli byste aktualizovat statistiku bez ohledu na čas poslední aktualizace.

### <a name="implement-statistics-management"></a>Implementace správy statistik

Můžete chtít rozšířit datový kanál, aby bylo zajištěno, že se statistiky aktualizují v případě, že se data významně mění, a to prostřednictvím přidání, odstranění nebo změny souborů.

Následující principy GUID jsou k dispozici pro aktualizaci statistik:

- Zajistěte, aby byla datová sada v aktualizovaném alespoň jednom objektu statistiky. Tato velikost aktualizací (počet řádků a počet stránek) se aktualizuje jako součást aktualizace statistiky.
- Zaměřte se na sloupce, které se účastní klauzulí WHERE, JOIN, GROUP BY, ORDER BY a DISTINCT.
- Aktualizujte sloupce vzestupného klíče, jako je například datum transakce častěji, protože tyto hodnoty nebudou zahrnuty do statistického histogramu.
- Aktualizujte statické distribuční sloupce méně často.

Další informace najdete v tématu [odhad mohutnosti](/sql/relational-databases/performance/cardinality-estimation-sql-server).

### <a name="examples-create-statistics-for-column-in-openrowset-path"></a>Příklady: vytvoření statistiky pro sloupec v cestě OPENROWSET

Následující příklady vám ukážou, jak používat různé možnosti vytváření statistik. Možnosti, které pro jednotlivé sloupce použijete, závisí na charakteristikách vašich dat a způsobu, jakým bude sloupec použit v dotazech.

> [!NOTE]
> V tuto chvíli můžete vytvořit statistiky s jedním sloupcem.
>
> Ke spuštění sp_create_openrowset_statistics a sp_drop_openrowset_statistics se vyžadují následující oprávnění: Správa HROMADných operací nebo Správa HROMADných operací databáze.

K vytvoření statistiky se používá následující uložená procedura:

```sql
sys.sp_create_openrowset_statistics [ @stmt = ] N'statement_text'
```

Argumenty: [ @stmt =] N ' statement_text ' – Určuje příkaz Transact-SQL, který vrátí hodnoty sloupce, které budou použity pro statistiku. Pomocí klauzule TABLESAMPLE můžete zadat ukázky dat, která se mají použít. Pokud není zadaná klauzule TABLESAMPLE, použije se FULLSCAN.

```syntaxsql
<tablesample_clause> ::= TABLESAMPLE ( sample_number PERCENT )
```

> [!NOTE]
> Vzorkování sdílených svazků clusteru v tuto chvíli nefunguje, pro sdílený svazek clusteru se podporuje jenom FULLSCAN.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Vytváření statistik s jedním sloupcem prozkoumáním každého řádku

Chcete-li vytvořit statistiku pro sloupec, zadejte dotaz, který vrátí sloupec, pro který budete potřebovat statistiku.

Ve výchozím nastavení, pokud neurčíte jinak, fond SQL bez serveru používá 100% dat poskytnutých v datové sadě při vytváření statistik.

Chcete-li například vytvořit statistiku s výchozími možnostmi (FULLSCAN) pro sloupec Year datové sady na základě souboru population.csv:

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

EXEC sys.sp_create_openrowset_statistics N'SELECT year
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

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Vytvoření statistiky s jedním sloupcem zadáním velikosti vzorku

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

EXEC sys.sp_create_openrowset_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-update-statistics"></a>Příklady: aktualizace statistiky

Chcete-li aktualizovat statistiku, je třeba vyřadit a vytvořit statistiku. K vyřazení statistiky se používá následující uložená procedura:

```sql
sys.sp_drop_openrowset_statistics [ @stmt = ] N'statement_text'
```

> [!NOTE]
> Ke spuštění sp_create_openrowset_statistics a sp_drop_openrowset_statistics se vyžadují následující oprávnění: Správa HROMADných operací nebo Správa HROMADných operací databáze.

Argumenty: [ @stmt =] N ' statement_text ' – určuje stejný příkaz jazyka Transact-SQL, který se používá při vytváření statistik.

Chcete-li aktualizovat statistiku pro sloupec year v datové sadě, která je založena na population.csv souboru, je třeba vyřadit a vytvořit statistiku:

```sql
EXEC sys.sp_drop_openrowset_statistics N'SELECT payment_type
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

EXEC sys.sp_create_openrowset_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-create-statistics-for-external-table-column"></a>Příklady: vytvoření statistiky pro sloupec externí tabulky

Následující příklady vám ukážou, jak používat různé možnosti vytváření statistik. Možnosti, které pro jednotlivé sloupce použijete, závisí na charakteristikách vašich dat a způsobu, jakým bude sloupec použit v dotazech.

> [!NOTE]
> V tuto chvíli můžete vytvořit statistiky s jedním sloupcem.

Chcete-li vytvořit statistiku pro sloupec, zadejte název objektu statistiky a název sloupce.

```sql
CREATE STATISTICS statistics_name
ON { external_table } ( column )
    WITH
        { FULLSCAN
          | [ SAMPLE number PERCENT ] }
        , { NORECOMPUTE }
```

Argumenty: external_table určuje externí tabulku, kterou by měla být vytvořena Statistika.

FULLSCAN statistiku výpočtů pomocí kontroly všech řádků. FULLSCAN a SAMPLE 100 procento mají stejné výsledky. FULLSCAN nelze použít s možností SAMPLE.

SAMPLE Number PERCENT určuje přibližnou procentuální hodnotu nebo počet řádků v tabulce nebo indexované zobrazení pro Optimalizátor dotazů, který se použije při vytváření statistik. Číslo může být od 0 do 100.

VZOREK nelze použít s možností FULLSCAN.

> [!NOTE]
> Vzorkování sdílených svazků clusteru v tuto chvíli nefunguje, pro sdílený svazek clusteru se podporuje jenom FULLSCAN.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Vytváření statistik s jedním sloupcem prozkoumáním každého řádku

```sql
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH FULLSCAN, NORECOMPUTE
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Vytvoření statistiky s jedním sloupcem zadáním velikosti vzorku

```sql
-- following sample creates statistics with sampling 20%
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH SAMPLE 5 percent, NORECOMPUTE
```

### <a name="examples-update-statistics"></a>Příklady: aktualizace statistiky

Chcete-li aktualizovat statistiku, je třeba vyřadit a vytvořit statistiku. Nejdřív přetáhněte statistiku:

```sql
DROP STATISTICS census_external_table.sState
```

A vytvořte statistiky:

```sql
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH FULLSCAN, NORECOMPUTE
```

## <a name="next-steps"></a>Další kroky

Další vylepšení výkonu dotazů pro vyhrazený fond SQL najdete v tématu [Monitorování úloh](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) a [osvědčených postupů pro vyhrazený fond SQL](best-practices-sql-pool.md#maintain-statistics).

Další vylepšení výkonu dotazů pro fond SQL bez serveru najdete v tématu [osvědčené postupy pro fond SQL bez serveru](best-practices-sql-on-demand.md) .