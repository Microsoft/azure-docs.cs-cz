---
title: Vytváření a aktualizace statistik v tabulkách
description: Doporučení a příklady pro vytváření a aktualizaci statistik pro optimalizaci dotazů v tabulkách ve vyhrazeném fondu SQL
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/09/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3ade41c51cbb8065734e8957cfc8b9f0c22b2df3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98673362"
---
# <a name="table-statistics-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Statistiky tabulek pro vyhrazený fond SQL ve službě Azure synapse Analytics

V tomto článku najdete doporučení a příklady pro vytváření a aktualizaci statistik pro optimalizaci dotazů v tabulkách ve vyhrazeném fondu SQL.

## <a name="why-use-statistics"></a>Proč používat statistiku

Čím více vyhrazeného fondu SQL ví o vašich datech, tím rychleji se dá na něj spouštět dotazy. Po načtení dat do vyhrazeného fondu SQL je shromažďování statistických údajů o vašich datech jedním z nejdůležitějších věcí, které můžete udělat k optimalizaci vašich dotazů.

Optimalizátor dotazů na vyhrazený fond SQL je modul pro optimalizaci na základě nákladů. Porovnává náklady na různé plány dotazů a pak zvolí plán s nejnižšími náklady. Ve většině případů si zvolí plán, který se spustí nejrychleji.

Například pokud Optimalizátor odhadne, že datum, na kterém dotaz vyfiltruje, vrátí jeden řádek, ve kterém se vybere jeden plán. Pokud se odhaduje, že vybrané datum vrátí 1 000 000 řádků, vrátí se jiný plán.

## <a name="automatic-creation-of-statistic"></a>Automatické vytváření statistik

Pokud je možnost databáze AUTO_CREATE_STATISTICS zapnutá, vyhrazený fond SQL analyzuje příchozí dotazy uživatelů pro chybějící statistiky.

Pokud Statistika chybí, vytvoří Optimalizátor dotazů statistiku pro jednotlivé sloupce v predikátu dotazu nebo podmínky spojení ke zvýšení odhadů mohutnosti pro plán dotazu.

> [!NOTE]
> Automatické vytváření statistik je teď ve výchozím nastavení zapnuté.

Pomocí následujícího příkazu můžete ověřit, jestli se váš vyhrazený fond SQL AUTO_CREATE_STATISTICS nakonfigurovat:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Pokud váš vyhrazený fond SQL není AUTO_CREATE_STATISTICS nakonfigurovaný, doporučujeme tuto vlastnost Povolit spuštěním následujícího příkazu:

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
> Automatické vytváření statistik není vytvořeno pro dočasné nebo externí tabulky.

Automatické vytváření statistik je prováděno synchronně, takže pokud ve sloupcích chybí statistiky, může dojít k mírnému snížení výkonu dotazů. Čas vytvoření statistiky pro jeden sloupec závisí na velikosti tabulky.

Abyste se vyhnuli měřitelnému snížení výkonu, měli byste před profilací systému zajistit, aby byly nejprve vytvořeny statistiky spuštěním úlohy srovnávacích testů.

> [!NOTE]
> Vytváření statistik bude přihlášeno [Sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) pod jiným uživatelským kontextem.

Při vytváření automatických statistik budou mít podobu: _WA_Sys_ id sloupce<8 číslic v šestnáctkovém>_<desítkové tabulce s číslem ID v šestnáctkové>. Můžete zobrazit statistiky, které již byly vytvořeny spuštěním příkazu [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) :

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

Table_name je název tabulky obsahující statistiku, která se má zobrazit. Tato tabulka nemůže být externí tabulkou. Cíl je název cílového indexu, statistiky nebo sloupce, pro které chcete zobrazit informace o statistice.

## <a name="update-statistics"></a>Aktualizovat statistiku

Jedním z osvědčených postupů je aktualizovat statistiku pro sloupce data každý den, protože se přidávají nová data. Pokaždé, když se do vyhrazeného fondu SQL načtou nové řádky, přidají se nová data načítání nebo data transakcí. Tyto doplňky změní distribuci dat a zavedou statistiku.

Statistiky ve sloupci země nebo oblasti v tabulce zákazníků nemusí být nikdy potřeba aktualizovat, protože distribuce hodnot se obecně nemění. Za předpokladu, že je rozdělení mezi zákazníky konstantní, přidání nových řádků do varianty tabulky nemění distribuci dat.

Pokud ale vyhrazený fond SQL obsahuje jenom jednu zemi nebo oblast a data z nové země nebo oblasti, která jsou uložená v různých zemích nebo oblastech, budete muset aktualizovat statistiku ve sloupci země/oblast.

V následující části jsou doporučení aktualizující statistiky:

|||
|-|-|
| **Frekvence aktualizací statistiky**  | Konzervativní: denně </br> Po načtení nebo transformaci dat |
| **Vzorkování** |  Méně než 1 000 000 000 řádků, použijte výchozí vzorkování (20 procent). </br> S více než 1 000 000 000 řádky použijte vzorkování ze dvou procent. |

Jedna z prvních otázek, které je třeba položit při řešení potíží s dotazem, **jsou statistiky aktuální? "**

Tato otázka není ta, kterou by bylo možné zodpovědět o stáří dat. Aktuální objekt statistiky může být starý, pokud nedošlo ke změně materiálu podkladových dat. Když se počet řádků podstatně změnil, nebo dojde ke změně v rozdělení hodnot pro *sloupec, je* čas aktualizovat statistiku. 

Neexistuje žádné zobrazení dynamické správy, abyste zjistili, jestli se data v tabulce od poslední aktualizace statistiky změnila.  Následující dva dotazy vám pomohou určit, zda jsou vaše statistiky zastaralé.

**Dotaz 1:**  Zjistí rozdíl mezi počtem řádků z statistiky (**stats_row_count**) a skutečným počtem řádků (**actual_row_count**). 

```sql
select 
objIdsWithStats.[object_id], 
actualRowCounts.[schema], 
actualRowCounts.logical_table_name, 
statsRowCounts.stats_row_count, 
actualRowCounts.actual_row_count,
row_count_difference = CASE
    WHEN actualRowCounts.actual_row_count >= statsRowCounts.stats_row_count THEN actualRowCounts.actual_row_count - statsRowCounts.stats_row_count
    ELSE statsRowCounts.stats_row_count - actualRowCounts.actual_row_count
END,
percent_deviation_from_actual = CASE
    WHEN actualRowCounts.actual_row_count = 0 THEN statsRowCounts.stats_row_count
    WHEN statsRowCounts.stats_row_count = 0 THEN actualRowCounts.actual_row_count
    WHEN actualRowCounts.actual_row_count >= statsRowCounts.stats_row_count THEN CONVERT(NUMERIC(18, 0), CONVERT(NUMERIC(18, 2), (actualRowCounts.actual_row_count - statsRowCounts.stats_row_count)) / CONVERT(NUMERIC(18, 2), actualRowCounts.actual_row_count) * 100)
    ELSE CONVERT(NUMERIC(18, 0), CONVERT(NUMERIC(18, 2), (statsRowCounts.stats_row_count - actualRowCounts.actual_row_count)) / CONVERT(NUMERIC(18, 2), actualRowCounts.actual_row_count) * 100)
END
from
(
    select distinct object_id from sys.stats where stats_id > 1
) objIdsWithStats
left join
(
    select object_id, sum(rows) as stats_row_count from sys.partitions group by object_id
) statsRowCounts
on objIdsWithStats.object_id = statsRowCounts.object_id 
left join
(
    SELECT sm.name [schema] ,
    tb.name logical_table_name ,
    tb.object_id object_id ,
    SUM(rg.row_count) actual_row_count
    FROM sys.schemas sm
    INNER JOIN sys.tables tb ON sm.schema_id = tb.schema_id
    INNER JOIN sys.pdw_table_mappings mp ON tb.object_id = mp.object_id
    INNER JOIN sys.pdw_nodes_tables nt ON nt.name = mp.physical_name
    INNER JOIN sys.dm_pdw_nodes_db_partition_stats rg
    ON rg.object_id = nt.object_id
    AND rg.pdw_node_id = nt.pdw_node_id
    AND rg.distribution_id = nt.distribution_id
    WHERE 1 = 1
    GROUP BY sm.name, tb.name, tb.object_id
) actualRowCounts
on objIdsWithStats.object_id = actualRowCounts.object_id

```

**Dotaz 2:** Zjistěte stáří statistik tím, že zkontrolujete čas poslední aktualizace statistiky na každé tabulce. 

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

**Sloupce data** ve vyhrazeném fondu SQL například obvykle potřebují časté aktualizace statistiky. Pokaždé, když se do vyhrazeného fondu SQL načtou nové řádky, přidají se nová data načítání nebo data transakcí. Tyto doplňky změní distribuci dat a zavedou statistiku.

V opačném případě nemusí být Statistika u sloupce žen v tabulce zákazníků nikdy potřeba aktualizovat. Za předpokladu, že je rozdělení mezi zákazníky konstantní, přidání nových řádků do varianty tabulky nemění distribuci dat.

Pokud váš vyhrazený fond SQL obsahuje jenom jedno pohlaví a výsledkem nového požadavku je více pohlaví, budete muset aktualizovat statistiku pro sloupec pohlaví.

Další informace najdete v tématu Obecné pokyny pro [statistiku](/sql/relational-databases/statistics/statistics?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="implementing-statistics-management"></a>Implementace správy statistik

Je často vhodné zvětšit proces načítání dat, aby bylo zajištěno, že statistiky budou aktualizovány na konci zatížení, aby nedocházelo k omezení blokování nebo kolize prostředků mezi souběžnými dotazy.  

Načítání dat je v případě, že tabulky nejčastěji mění velikost a/nebo jejich distribuci hodnot. Načítání dat je logické místo pro implementaci některých procesů správy.

Následující principy GUID jsou k dispozici pro aktualizaci statistik:

- Zajistěte, aby se v každé načtené tabulce aktualizoval alespoň jeden objekt statistiky. Tím se aktualizuje informace o velikosti tabulky (počet řádků a počet stránek) jako součást aktualizace statistiky.
- Zaměřte se na sloupce účastnící se klauzulí JOIN, GROUP BY, ORDER BY a DISTINCT.
- Zvažte možnost aktualizovat sloupce vzestupného klíče, například data transakcí častěji, protože tyto hodnoty nebudou zahrnuty v histogramu statistiky.
- Zvažte možnost aktualizace statických distribučních sloupců méně často.
- Nezapomeňte, že každý objekt statistiky se aktualizuje v sekvenci. Pouhá implementace `UPDATE STATISTICS <TABLE_NAME>` není vždy ideální, zejména pro nejrůznější tabulky s velkým množstvím objektů statistiky.

Další informace najdete v tématu [odhad mohutnosti](/sql/relational-databases/performance/cardinality-estimation-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="examples-create-statistics"></a>Příklady: vytváření statistik

Tyto příklady ukazují, jak používat různé možnosti vytváření statistik. Možnosti, které pro jednotlivé sloupce použijete, závisí na charakteristikách vašich dat a způsobu, jakým bude sloupec použit v dotazech.

### <a name="create-single-column-statistics-with-default-options"></a>Vytváření statistik s jedním sloupcem s výchozími možnostmi

Chcete-li vytvořit statistiku pro sloupec, zadejte název objektu statistiky a název sloupce.

Tato syntaxe používá všechny výchozí možnosti. Ve výchozím nastavení je při vytváření statistik vytvořena ukázka **20 procent** tabulky.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Například:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="create-single-column-statistics-by-examining-every-row"></a>Vytváření statistik s jedním sloupcem prozkoumáním každého řádku

Výchozí vzorkovací frekvence 20 procent je ve většině případů dostačující. Vzorkovací frekvenci ale můžete upravit.

Chcete-li vzorkovat celou tabulku, použijte tuto syntaxi:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Například:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Vytvoření statistiky s jedním sloupcem zadáním velikosti vzorku

Alternativně můžete určit velikost vzorku jako procento:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Vytváření statistik s jedním sloupcem pouze na některých řádcích

Můžete také vytvořit statistiku pro část řádků v tabulce. Tato statistika se nazývá filtrovaná.

Filtrované statistiky můžete například použít, pokud plánujete dotaz na konkrétní oddíl velké dělené tabulky. Vytvořením statistiky jenom pro hodnoty oddílů se vylepšit přesnost statistik, a proto se zvýší výkon dotazů.

Tento příklad vytvoří statistiku pro rozsah hodnot. Hodnoty je možné snadno definovat tak, aby odpovídaly rozsahu hodnot v oddílu.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Aby Optimalizátor dotazů při volbě plánu distribuovaného dotazu bral v úvahu vyfiltrované statistiky, dotaz se musí vejít do definice objektu statistiky. V předchozím příkladu je nutné, aby klauzule WHERE dotazu určovala hodnoty Sloupec1 mezi 2000101 a 20001231.

### <a name="create-single-column-statistics-with-all-the-options"></a>Vytváření statistik s jedním sloupcem s využitím všech možností

Můžete také kombinovat možnosti dohromady. Následující příklad vytvoří objekt filtrované statistiky s vlastní velikostí vzorku:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Úplný odkaz naleznete v tématu [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="create-multi-column-statistics"></a>Vytváření statistik více sloupců

Chcete-li vytvořit objekt statistiky s více sloupci, použijte předchozí příklady, ale zadejte více sloupců.

> [!NOTE]
> Histogram, který se používá k odhadu počtu řádků ve výsledku dotazu, je k dispozici pouze pro první sloupec uvedený v definici objektu statistice.

V tomto příkladu je histogram v *\_ kategorii produktu*. Statistiky mezi sloupci se počítají podle *\_ kategorií produktů* a *\_ sub_category produktů*:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Vzhledem k tomu, že existuje korelace mezi *\_ kategorií produktů* a *\_ \_ podkategoriím produktu*, může být objekt statistiky s více sloupci užitečný, pokud jsou k těmto sloupcům přistupovaly ve stejnou dobu.

### <a name="create-statistics-on-all-columns-in-a-table"></a>Vytvořit statistiku pro všechny sloupce v tabulce

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

### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-sql-pool"></a>Pomocí uložené procedury můžete vytvořit statistiku pro všechny sloupce ve fondu SQL.

Vyhrazený fond SQL neobsahuje systémovou uloženou proceduru, která odpovídá sp_create_stats v SQL Server. Tato uložená procedura vytvoří objekt statistiky s jedním sloupcem v každém sloupci ve fondu SQL, který ještě nemá statistiku.

Následující příklad vám pomůže začít s návrhem fondu SQL. Nebojte se, abyste ji přizpůsobili vašim potřebám.

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

Chcete-li vytvořit statistiku pro všechny sloupce v tabulce pomocí výchozích hodnot, spusťte uloženou proceduru.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

Chcete-li vytvořit statistiku pro všechny sloupce v tabulce pomocí FULLSCAN, zavolejte tento postup.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Chcete-li vytvořit ukázkové statistiky pro všechny sloupce v tabulce, zadejte hodnotu 3 a vzorek procento. Tato procedura používá vzorkovací frekvenci 20 procent.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

## <a name="examples-update-statistics"></a>Příklady: aktualizace statistiky

Chcete-li aktualizovat statistiku, můžete:

- Aktualizujte jeden objekt statistiky. Zadejte název objektu statistiky, který chcete aktualizovat.
- Aktualizuje všechny objekty statistik v tabulce. Místo jednoho konkrétního objektu statistiky zadejte název tabulky.

### <a name="update-one-specific-statistics-object"></a>Aktualizace jednoho konkrétního objektu statistiky

K aktualizaci konkrétního objektu statistiky použijte následující syntax:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Například:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Aktualizací specifických objektů statistiky můžete minimalizovat čas a prostředky potřebné ke správě statistik. K tomu je potřeba si představit, že si vyberete nejlepší statistické objekty, které se mají aktualizovat.

### <a name="update-all-statistics-on-a-table"></a>Aktualizace všech statistik v tabulce

Jednoduchá metoda aktualizace všech objektů statistiky v tabulce je:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Například:

```sql
UPDATE STATISTICS dbo.table1;
```

Příkaz Aktualizovat STATISTIKu je snadno použitelný. Stačí si pamatovat, že aktualizuje *všechny* statistiky v tabulce, a proto může probíhat více práce, než je nutné. Pokud výkon není problémem, je to nejjednodušší a nejucelenější způsob, jak zaručit, že statistiky jsou aktuální.

> [!NOTE]
> Při aktualizaci všech statistik v tabulce provede vyhrazený fond SQL kontrolu na vzorové tabulce pro každý objekt statistiky. Pokud je tabulka velká a má mnoho sloupců a mnoho statistik, může být efektivnější aktualizovat jednotlivé statistiky podle potřeby.

Implementaci `UPDATE STATISTICS` procedury najdete v tématu [dočasné tabulky](sql-data-warehouse-tables-temporary.md). Metoda implementace je mírně odlišná od předchozího `CREATE STATISTICS` postupu, ale výsledek je stejný.

Úplnou syntaxi naleznete v tématu [UPDATE STATISTICS](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="statistics-metadata"></a>Statistika – metadata

K dispozici je několik systémových zobrazení a funkcí, které můžete použít k vyhledání informací o statistice. Můžete například zjistit, jestli objekt statistiky může být zastaralý, pomocí funkce data-datum, která se zobrazí při posledním vytvoření nebo aktualizaci statistiky.

### <a name="catalog-views-for-statistics"></a>Zobrazení katalogu pro statistiku

Tato systémová zobrazení obsahují informace o statistice:

| Zobrazení katalogu | Description |
|:--- |:--- |
| [sys. Columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Jeden řádek pro každý sloupec. |
| [sys. Objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Jeden řádek pro každý objekt v databázi. |
| [sys. schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Jeden řádek pro každé schéma v databázi. |
| [sys. stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Jeden řádek pro každý objekt statistiky. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Jeden řádek pro každý sloupec v objektu statistiky. Vrátí odkazy zpět na sys. Columns. |
| [sys. Tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Jeden řádek pro každou tabulku (zahrnuje externí tabulky). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Jeden řádek pro každý datový typ. |

### <a name="system-functions-for-statistics"></a>Systémové funkce pro statistiku

Tyto systémové funkce jsou užitečné pro práci s statistikami:

| Systémová funkce | Description |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Datum poslední aktualizace objektu statistiky |
| [PŘÍKAZ DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Souhrnná úroveň a podrobné informace o distribuci hodnot, které přirozuměl objektům statistiky. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Kombinovat statistické sloupce a funkce do jednoho zobrazení

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
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

## <a name="dbcc-show_statistics-examples"></a>Příklady příkazu DBCC SHOW_STATISTICS ()

Příkaz DBCC SHOW_STATISTICS () zobrazuje data uchovávaná v rámci objektu statistiky. Tato data jsou dodávána se třemi částmi:

- Hlavička
- Vektor hustoty
- Histogram

Metadata hlavičky informací o statistice. Histogram zobrazí distribuci hodnot v prvním klíčovém sloupci objektu statistiky. Vektor hustoty měří korelaci mezi sloupci.

> [!NOTE]
> Vyhrazený fond SQL vypočítává odhady mohutnosti s libovolnými daty v objektu statistiky.

### <a name="show-header-density-and-histogram"></a>Zobrazit záhlaví, hustotu a histogram

Tento jednoduchý příklad ukazuje všechny tři části objektu statistiky:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Například:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>Zobrazit jednu nebo více částí příkazu DBCC SHOW_STATISTICS ()

Pokud vás zajímá jenom zobrazení konkrétních částí, použijte `WITH` klauzuli a určete, které části chcete zobrazit:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Například:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-show_statistics-differences"></a>Rozdíly DBCC SHOW_STATISTICS ()

Příkaz DBCC SHOW_STATISTICS () je v porovnání s SQL Server striktně implementován ve vyhrazeném fondu SQL:

- Nedokumentované funkce se nepodporují.
- Nelze použít Stats_stream.
- Nelze spojit výsledky pro konkrétní podmnožiny dat statistiky. Například STAT_HEADER JOIN DENSITY_VECTOR.
- Pro potlačení zprávy nelze nastavit NO_INFOMSGS.
- Nelze použít hranaté závorky kolem názvů statistických údajů.
- K identifikaci objektů statistiky nelze použít názvy sloupců.
- Vlastní chyba 2767 není podporována.

## <a name="next-steps"></a>Další kroky

Další vylepšení výkonu dotazů najdete v tématu [Monitorování úloh](sql-data-warehouse-manage-monitor.md) .
