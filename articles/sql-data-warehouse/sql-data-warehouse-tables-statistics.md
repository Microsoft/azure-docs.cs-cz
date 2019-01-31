---
title: Vytváření, aktualizaci statistiky – Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Doporučení a příklady pro vytváření a aktualizaci statistik optimalizaci dotazu na tabulky ve službě Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 05/09/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: c11cdd6d1cc24d639d837993e94f3b304228634a
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55299550"
---
# <a name="creating-updating-statistics-on-tables-in-azure-sql-data-warehouse"></a>Vytváření, aktualizaci statistik tabulek v Azure SQL Data Warehouse
Doporučení a příklady pro vytváření a aktualizaci statistik optimalizaci dotazu na tabulky ve službě Azure SQL Data Warehouse.

## <a name="why-use-statistics"></a>Proč používat statistiky?
Čím se Azure SQL Data Warehouse ví týkající se vašich dat, tím rychleji se provádění dotazů na to. Shromažďování statistik na vašich datech a jejich načtením do SQL Data Warehouse je jedním z nejdůležitějších věcí, které vám pomůžou optimalizovat dotazy. Je to proto, že Optimalizátor dotazů SQL Data Warehouse je optimalizace na základě nákladů. Porovná náklady na různé plány dotazů a potom vybere plán s nejnižšími náklady. ve většině případů je to plán, který běží nejrychleji. Například pokud Optimalizátor odhaduje, že data, kdy se filtrování v dotazu vrátí jeden řádek, ho zvolit jiný plán než pokud, který odhaduje vybraným datem vrátí 1 milion řádků.

## <a name="automatic-creation-of-statistics"></a>Automatické vytváření statistik
Automatické vytváření statistik možnost zapnutá AUTO_CREATE_STATISTICS, SQL Data Warehouse analyzuje příchozí dotazy uživatelů, kde jsou vytvářeny jeden sloupec statistiky pro sloupce, kterým chybí statistiky. Optimalizace dotazů vytvoří statistiku pro jednotlivé sloupce v podmínka predikátu nebo připojení k dotazu ke zlepšení odhad kardinality pro plán dotazu. Automatické vytváření statistik je aktuálně ve výchozím nastavení zapnutá.

Můžete zkontrolovat, jestli váš datový sklad má nakonfigurovaný spuštěním následujícího příkazu:

```sql
SELECT name, is_auto_create_stats_on 
FROM sys.databases
```
Pokud váš datový sklad nemá AUTO_CREATE_STATISTICS nakonfigurované, doporučujeme vám povolení této vlastnosti spuštěním následujícího příkazu:

```sql
ALTER DATABASE <yourdatawarehousename> 
SET AUTO_CREATE_STATISTICS ON
```
Následující příkazy se aktivuje automatické vytváření statistik: Vyberte INSERT SELECT, CTAS, UPDATE, DELETE a VYSVĚTLIT při obsahující spojení nebo je zjištěna přítomnost predikátu. 

> [!NOTE]
> Automatické vytváření statistik na dočasné nebo externí tabulky nevytvoří.
> 

Automatické vytváření statistik generováno synchronně, případně utrpíte dotazu mírné snížení výkonu, pokud sloupce již nemají statistiky pro ně vytvořili. Vytvoření statistiky může trvat několik sekund na jeden sloupec v závislosti na velikosti tabulky. Aby se zabránilo snížení výkonu, zejména v srovnávací testy výkonu, měření se ujistěte, že se statistiky vytvořily nejprve spuštěním úlohou srovnávacího testu před profilací systému.

> [!NOTE]
> Vytvoření statistiky také zaznamenají [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016) v kontextu jiného uživatele.
> 

Když se vytvoří automatické statistiky, budou mít formuláře: _WA_Sys_< id sloupce 8 číslice v šestnáctkové soustavě > _ < id tabulky 8 číslice v šestnáctkové soustavě >. Můžete zobrazit statistiky, které již byly vytvořeny spuštěním [DBCC SHOW_STATISTICS](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?view=sql-server-2017) příkaz:

```sql
DBCC SHOW_STATISTICS (<tablename>, <targetname>)
```
První argument je tabulka, která obsahuje statistiku k zobrazení. Nelze se jednat externí tabulky. Druhým argumentem je název cílový index, statistiky nebo sloupec, pro který chcete zobrazit informace statistiky.



## <a name="updating-statistics"></a>Aktualizovat statistiku

Jeden osvědčeným postupem je aktualizovat statistiky pro sloupce Datum každý den se přidají nová data. Nové řádky každý čas se načtou do datového skladu, nové datum zatížení nebo data transakcí se přidají. Tyto změnit distribuci dat a vytvořit statistiky zastaralá. Naopak statistik země sloupce v tabulce zákazníků může nikdy potřeba aktualizovat, protože obecně nezmění distribuce hodnot. Předpokládáme, že distribuce je konstantní mezi zákazníky a přidání nových řádků do tabulky variace se chystáte změnit distribuci dat. Ale pokud váš datový sklad obsahuje pouze v jedné ze zemí a můžete přenést data z nové země, výsledkem jsou data z několika zemích, které ukládají, musíte aktualizovat statistiku sloupci země.

Toto jsou doporučení aktualizaci statistiky:

|||
|-|-|
| **Četnosti aktualizací statistik**  | Konzervativní: Denně <br></br> Po načtení nebo transformace dat |
| **Vzorkování** |  Menší než 1 miliarda řádků, použijte výchozí vzorkování (20 procent) <br></br> S více než 1 miliarda řádků je dobré statistiky v oblasti % 2 |

Jedním ze základních otázek požádat, pokud řešíte potíže s dotazu je, **"Je statistika aktuální?"**

Tento dotaz není ten, který může být zodpoví starší data. Statistiky aktuální objekt může být staré, pokud došlo ke změně žádné materiálu na podkladová data. Když počet řádků, podstatně změní nebo je značné změně v distribuci hodnot ve sloupci *pak* je čas aktualizovat statistiku.

Protože neexistuje žádné zobrazení dynamické správy k určení, jestli se data v tabulce změnila od poslední časové statistiky byly aktualizovány, znalost stáří vašich statistik vám může zajistit s část obrázku.  Následující dotaz můžete použít k určení posledního statistikami se aktualizovala na každou tabulku.  

> [!NOTE]
> Mějte na paměti, pokud je značné změně v distribuci hodnot ve sloupci, měli byste aktualizovat statistiky bez ohledu na čas poslední byly aktualizovány.  
> 
> 

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

**Datum sloupce** v datovém skladu, například obvykle vyžadují časté aktualizace statistiky. Nové řádky každý čas se načtou do datového skladu, nové datum zatížení nebo data transakcí se přidají. Tyto změnit distribuci dat a vytvořit statistiky zastaralá.  Naopak statistiky o pohlaví sloupce v tabulce zákazníků nikdy nemusíte potřebovat aktualizovat. Předpokládáme, že distribuce je konstantní mezi zákazníky a přidání nových řádků do tabulky variace se chystáte změnit distribuci dat. Ale pokud váš datový sklad obsahuje pouze jeden pohlaví a nové výsledky požadavku v několika pohlaví, musíte aktualizovat statistiku na sloupci pohlaví.

Další informace najdete v části Obecné pokyny pro [statistiky](/sql/relational-databases/statistics/statistics).

## <a name="implementing-statistics-management"></a>Implementace správy statistiky
Často je vhodné k rozšíření procesu načítání dat k zajištění, že se statistika aktualizuje na konci zatížení. Načtení dat je při tabulky nejčastěji změně jejich velikosti a/nebo jejich distribuci hodnot. To je proto logické místo, kde můžete implementovat několik procesů správy.

Tyto zásady jsou k dispozici pro aktualizaci vašich statistik během procesu načtení:

* Zajistěte, aby každý načtenou tabulku alespoň jeden objekt statistiky aktualizovat. Tím se aktualizuje informace o velikosti (počet řádků a počet stránek) tabulky jako součást aktualizace statistiky.
* Zaměřte se na sloupce, na které se účastní spojení, GROUP BY, ORDER BY a DISTINCT klauzulí.
* Vezměte v úvahu aktualizace sloupců "ascending klíč", jako je transakce data častěji, protože tyto hodnoty nebudou zahrnuty v histogramu statistiky.
* Zvažte aktualizaci statické distribuční sloupce méně často.
* Mějte na paměti, postupně se aktualizuje každý objekt statistiky. Implementací `UPDATE STATISTICS <TABLE_NAME>` vždy není ideální, zejména u širokých tabulek s mnoha objekty statistiky.

Další informace najdete v tématu [odhad kardinality](/sql/relational-databases/performance/cardinality-estimation-sql-server).

## <a name="examples-create-statistics"></a>Příklady: Vytvoření statistiky
Tyto příklady ukazují, jak používat různé možnosti pro vytváření statistik. Možnosti, které používáte pro každý sloupec závisí na charakteristikách vašich dat a použití sloupci v dotazech.

### <a name="create-single-column-statistics-with-default-options"></a>Vytvořit jednosloupcovou statistiku s výchozími možnostmi
Pokud chcete vytvořit statistiku pro sloupec, stačí zadejte název statistiky objektu a název sloupce.

Tato syntaxe používá všechny výchozí možnosti. Ve výchozím nastavení, SQL Data Warehouse ukázky **20 procent** z tabulku v případě, že vytvoří statistiku.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Příklad:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="create-single-column-statistics-by-examining-every-row"></a>Vytvořit jednosloupcovou statistiku tím, že kontroluje všechny řádky
Výchozí vzorkování 20 procent stačí pro většinu situací. Však můžete upravit vzorkovací frekvenci.

Pro vzorkování celou tabulku, použijte následující syntaxi:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Příklad:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Vytvořit jednosloupcovou statistiku tak, že určíte velikost vzorku
Alternativně můžete zadat velikost vzorku v procentech:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Vytvořit jednosloupcovou statistiku pro pouze některé řádky
Můžete také vytvořit statistiky na část řádky v tabulce. Tomu se říká filtrované statistiky.

Můžete například použít filtrovanou statistiku při plánování provedení dotazu konkrétní oddíl velké dělenou tabulku. Vytvořením statistiky na pouze hodnoty oddílu přesnost statistiky vylepšíme a proto zlepšit výkon dotazů.

Tento příklad vytvoří statistiku pro rozsah hodnot. Hodnoty lze snadno definovat tak, aby odpovídala rozsahu hodnot v oddílu.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Pro optimalizátoru dotazů pro zvážení použití filtrovanou statistiku, když se rozhodne plánu distribuovaných dotazů dotaz se musí vejít do definice objektu statistiky. Použijeme předchozí příklad, dotazu kde klauzule musí určovat Sloupec1 hodnoty mezi 2000101 a 20001231.
> 
> 

### <a name="create-single-column-statistics-with-all-the-options"></a>Vytvořit jednosloupcovou statistiku se všemi možnostmi
Možnosti můžete také kombinovat společně. Následující příklad vytvoří objekt filtrovanou statistiku s velikostí ukázková:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Úplný přehled najdete v tématu [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql).

### <a name="create-multi-column-statistics"></a>Vytvoření statistiky více sloupci
K vytvoření objektu vícesloupcové statistiky, jednoduše použijte v předchozích příkladech, ale zadat více sloupců.

> [!NOTE]
> Histogramu, který se používá k odhadu počtu řádků ve výsledku dotazu, je dostupná jenom pro první sloupec uvedené v definici objektu statistiky.
> 
> 

V tomto příkladu je histogram na *produktu\_kategorie*. Statistiky mezi sloupci se počítají na *produktu\_kategorie* a *produktu\_sub_category*:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Vzhledem k tomu, že existuje korelace mezi *produktu\_kategorie* a *produktu\_sub\_kategorie*, objekt vícesloupcové statistik může být užitečné, pokud tyto sloupce jsou přístupné ve stejnou dobu.

### <a name="create-statistics-on-all-columns-in-a-table"></a>Vytvořit statistiku pro všechny sloupce v tabulce
Jeden způsob, jak vytvořit statistiky je vydávat příkazy CREATE STATISTICS po vytvoření v tabulce:

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

### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Vytvořit statistiku pro všechny sloupce v databázi pomocí uložené procedury
SQL Data Warehouse nemá ekvivalentní sp_create_stats systémové uložené procedury v systému SQL Server. Tuto uloženou proceduru vytvoří objekt jeden sloupec statistiky nad každým sloupcem databáze, která ještě nemá statistiky.

V následujícím příkladu vám pomůže začít pracovat s návrhem vaší databáze. Teď můžete přizpůsobit podle vašich potřeb:

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

Vytvořit statistiku pro všechny sloupce v tabulce s použitím výchozích nastavení, jednoduše zavolejte proceduru.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```
Vytvořit statistiku pro všechny sloupce v tabulce s využitím fullscan, zavolejte tento postup:

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```
Vytvoření vzorkové statistiky pro všechny sloupce v tabulce, zadejte 3 a procentuální vzorek.  Postupů používá vzorkovací frekvence 20 procent.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```


Chcete-li vytvořit odběr statistiku pro všechny sloupce 

## <a name="examples-update-statistics"></a>Příklady: Aktualizovat statistiku
Chcete-li aktualizovat statistiku, můžete:

- Aktualizujte statistiku objektů. Zadejte název objektu statistiky, které chcete aktualizovat.
- Aktualizace všech objektů statistiky na tabulku. Zadejte název tabulky místo jednoho objektu konkrétní statistiku.

### <a name="update-one-specific-statistics-object"></a>Aktualizovat jeden objekt konkrétní Statistika
Aktualizace objektu konkrétní statistiky použijte následující syntaxi:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Příklad:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Aktualizováním konkrétní statistiku objektů lze minimalizovat čas a prostředky potřebné k Správa statistik. To vyžaduje některé si mysleli, že k výběru nejvhodnější statistiky objekty, které chcete aktualizovat.

### <a name="update-all-statistics-on-a-table"></a>Aktualizovat všechny statistiky na tabulku
To ukazuje jednoduchý způsob pro aktualizaci všech objektů statistiky na tabulku:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Příklad:

```sql
UPDATE STATISTICS dbo.table1;
```

Tento příkaz se snadno používá. Jenom nezapomeňte, že aktualizace *všechny* statistiky pro tabulku a proto může provádět další práci, než je nezbytné. Pokud výkon není problém, to je nejúplnější a nejjednodušší způsob, jak zaručit, že statistiky jsou aktuální.

> [!NOTE]
> Při aktualizaci všechny statistiky na tabulku SQL Data Warehouse nepodporuje vyhledávání s cílem ukázkové tabulky pro každý objekt statistiky. Pokud v tabulce je velká a má mnoho sloupců a mnoho statistiky, může být efektivnější aktualizovat podle potřeb jednotlivých statistiky.
> 
> 

Implementace `UPDATE STATISTICS` postupu naleznete v tématu [dočasné tabulky](sql-data-warehouse-tables-temporary.md). Implementace metody se mírně liší od předchozí `CREATE STATISTICS` postup, ale výsledek je stejný.

Úplná syntaxe, naleznete v tématu [Update Statistics](/sql/t-sql/statements/update-statistics-transact-sql).

## <a name="statistics-metadata"></a>Statistika metadat
Existuje několik zobrazení systému a funkcí, které vám umožní najít informace o statistice. Například můžete zobrazit, pokud objekt statistik může být zastaralá pomocí funkce Statistika date a zjistěte, kdy byly naposledy vytvoří nebo aktualizuje statistiky.

### <a name="catalog-views-for-statistics"></a>Zobrazení katalogu pro statistiku
Tato systémová zobrazení poskytují informace o statistiky:

| Zobrazení katalogu | Popis |
|:--- |:--- |
| [sys.columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql) |Jeden řádek pro každý sloupec. |
| [sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql) |Jeden řádek pro každý objekt v databázi. |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql) |Jeden řádek pro každé schéma databáze. |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql) |Jeden řádek pro každý objekt statistiky. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql) |Jeden řádek pro každý sloupec v objektu statistiky. Obsahuje odkazy na sys.columns zpět. |
| [sys.tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql) |Jeden řádek pro každou tabulku (včetně externích tabulek). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql) |Jeden řádek pro jednotlivé datové typy. |

### <a name="system-functions-for-statistics"></a>Funkce systému pro statistiku
Tyto funkce systému jsou užitečné při práci s statistiky:

| System – funkce | Popis |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql) |Datum poslední aktualizace statistik objektu. |
| [PŘÍKAZ DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql) |Souhrn úrovně a podrobné informace o distribuci hodnot, jak je chápán statistik objektu. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Zkombinovat do jednoho zobrazení statistiky sloupce a funkce
Toto zobrazení přináší sloupce, které se týkají statistiky a výsledky z funkce STATS_DATE() společně.

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

## <a name="dbcc-showstatistics-examples"></a>Příkaz DBCC SHOW_STATISTICS() příklady
Příkaz DBCC SHOW_STATISTICS() zobrazuje data ukládaná v rámci objektu statistiky. Tato data k dispozici ve třech částech:

- Hlavička
- Hustota vektoru
- Histogram

Hlavičku metadat o statistikách. V prvním sloupci klíče objektu statistiky zobrazuje histogram distribuce hodnot. Korelace mezi sloupci hustota vektoru opatření. SQL Data Warehouse vypočítá Kardinalita odhadů se jakákoliv data v objektu statistiky.

### <a name="show-header-density-and-histogram"></a>Zobrazit záhlaví, hustotou a histogramu
Tento jednoduchý příklad ukazuje všechny tři části objektu statistiky:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Příklad:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-showstatistics"></a>Zobrazit jednu nebo více částí DBCC SHOW_STATISTICS()
Pokud vás zajímá jenom v zobrazení konkrétní části, použijte `WITH` klauzule a určit, které části, které chcete zobrazit:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Příklad:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-showstatistics-differences"></a>Příkaz DBCC SHOW_STATISTICS() rozdíly
Příkaz DBCC SHOW_STATISTICS() je přísněji implementované v SQL Data Warehouse, SQL serveru:

- Nezdokumentovaný funkce nejsou podporovány.
- Nelze použít Stats_stream.
- Nelze připojit výsledky pro konkrétní podmnožiny dat statistiky. Například (STAT_HEADER připojení DENSITY_VECTOR).
- NO_INFOMSGS nelze nastavit pro potlačení zpráv.
- Hranaté závorky kolem názvy statistiky nelze použít.
- Názvy sloupců nelze použít k identifikaci objektů statistiky.
- Vlastní chyba 2767 není podporován.

## <a name="next-steps"></a>Další postup
Pro ještě zlepšit výkon dotazů, najdete v článku [monitorování úloh](sql-data-warehouse-manage-monitor.md)

