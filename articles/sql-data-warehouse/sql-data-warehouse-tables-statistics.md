---
title: Vytváření, aktualizaci statistiky – Azure SQL Data Warehouse | Microsoft Docs
description: Doporučení a příklady pro vytváření a aktualizaci statistiky optimalizaci dotazu na tabulky v Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 05/09/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: bbc6a5083aebba40885700cab6c67128c9d9f916
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34643426"
---
# <a name="creating-updating-statistics-on-tables-in-azure-sql-data-warehouse"></a>Vytváření, aktualizaci statistiky pro tabulky v Azure SQL Data Warehouse
Doporučení a příklady pro vytváření a aktualizaci statistiky optimalizaci dotazu na tabulky v Azure SQL Data Warehouse.

## <a name="why-use-statistics"></a>Proč používat statistiky?
Více Azure SQL Data Warehouse ví o vašich dat, tím rychleji se spouštět dotazy na ho. Shromažďování statistik na vaše data a pak ho načítání do SQL Data Warehouse je jedním z nejdůležitějších kroků, které můžete provést za účelem optimalizace své dotazy. Je to proto Optimalizátor dotazů SQL Data Warehouse je na základě nákladů pro optimalizaci. Porovná náklady na různé plány dotazů a potom vybere plán s nejnižšími náklady. ve většině případů je to plán, který běží nejrychleji. Například pokud pro optimalizaci odhadne, že datum, kdy jsou filtrování v dotazu vrátí jeden řádek, ho vyberte jiný plán než pokud odhadne, vybraným datem vrátí 1 milionu řádků.

## <a name="automatic-creation-of-statistics"></a>Automatické vytváření statistik
Při vytvoření automatického statistiky možnost na AUTO_CREATE_STATISTICS, SQL Data Warehouse analyzuje příchozí uživatelských dotazů, kde jeden sloupec statistiky jsou vytvořeny pro sloupce, které chybí statistiky. Optimalizátor dotazů vytvoří statistiky na jednotlivých sloupců v dotazu podmínka predikátu nebo připojení k ke zlepšení odhady mohutnost pro plán dotazu. Automatické vytváření statistik je nyní ve výchozím nastavení zapnutá.

Můžete zkontrolovat, jestli váš datový sklad má nakonfigurovaný tak, že spustíte následující příkaz:

```sql
SELECT name, is_auto_create_stats_on 
FROM sys.databases
```
Pokud datového skladu nemá AUTO_CREATE_STATISTICS nakonfigurované, doporučujeme vám povolení tuto vlastnost tak, že spustíte následující příkaz:

```sql
ALTER DATABASE <yourdatawarehousename> 
SET AUTO_CREATE_STATISTICS ON
```
Následující příkazy se aktivuje automatické vytváření statistik: vyberte, vyberte příkaz INSERT, funkce CTAS, aktualizace, odstranění a VYSVĚTLIT když obsahuje spojení nebo je zjištěna přítomnost predikátu. 

> [!NOTE]
> Automatické vytváření statistik nejsou vytvořeny na dočasné nebo externí tabulky.
> 

Automatické vytváření statistik se generuje synchronně, pokud vaše sloupce již nemají statistiky pro ně byly vytvořeny by vám mohla vzniknout výkon mírnou sníženou dotazů. Vytvoření statistiky může trvat několik sekund na jeden sloupec v závislosti na velikosti tabulky. Aby se zabránilo snížení výkonu, zejména v srovnávací testy výkonu, měření se ujistěte, že statistiky byly vytvořeny nejdřív spuštěním úlohy srovnávacího testu před profilace systému.

> [!NOTE]
> Vytvoření statistiky se taky zaznamená [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016) v kontextu jiného uživatele.
> 

Když se vytváří automatické statistiky, bude mít formu: _WA_Sys_< id sloupce 8 číslic v šestnáctkové soustavě > _ < id tabulky 8 číslic v šestnáctkové soustavě >. Můžete zobrazit statistiky, které již byly vytvořeny tak, že spustíte [DBCC SHOW_STATISTICS](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?view=sql-server-2017) příkaz:

```sql
DBCC SHOW_STATISTICS (<tablename>, <targetname>)
```
První argument je tabulka, která obsahuje statistiku k zobrazení. To nemůže být externí tabulky. Druhý argument je název cílový index, statistiky nebo sloupec, pro který chcete zobrazit statistické informace.



## <a name="updating-statistics"></a>Aktualizuje statistické údaje

Jeden osvědčeným postupem je aktualizovat statistiku sloupců s kalendářními daty každý den při přidávání nová data. Každé nové řádky času jsou načtená do datového skladu, nové zatížení kalendářní data nebo data transakcí se přidají. Tyto změnit distribuci dat a proveďte statistiku zastaralá. Naopak statistiky země sloupec v tabulce zákazníka může být nikdy potřeba aktualizovat, protože rozdělení hodnot nemění obecně. Za předpokladu, že distribuce je konstantní mezi odběrateli, přidávání nových řádků do tabulky variace není chystáte změnit rozdělení data. Ale pokud váš datový sklad obsahuje pouze jeden země a připojte ve data z nové země, výsledkem data z několika zemích, které ukládají, pak budete muset aktualizovat statistiku na sloupci země.

Toto jsou doporučení aktualizaci statistiky:

| **Četnosti aktualizací statistik** | Konzervativní: denně <br></br> Poté, co načítání nebo transformace dat || **Vzorkování** |  Menší než 1 miliardy řádků, použijte výchozí vzorkování (20 procent) <br></br> S více než 1 miliardy řádků, je dobré statistiky v rozsahu 2 procent |

Jedním ze základních otázek a požádejte, když se řešení potíží s dotazu je, **"Jsou statistiku aktuální?"**

Tento dotaz není ten, který může odpovídat stáří data. Aktuální statistiku objektu může být starý, pokud byl žádné závažné změny v základních datech. Pokud počet řádků, došlo ke změně podstatně nebo dojde ke změně podstatným v distribuci hodnot ve sloupci *pak* je třeba aktualizovat statistiku.

Protože neexistuje žádné zobrazení dynamické správy k určení, jestli data v tabulce se změnil od posledního statistiku časových údajů byly aktualizovány, znalost stáří statistice můžete nabízejí část obrázku.  Následující dotaz můžete použít k určení, kdy jednotlivé tabulky byly aktualizovány statistice naposledy.  

> [!NOTE]
> Mějte na paměti, že pokud dojde k podstatným změn v distribuci hodnoty pro sloupec, by měl aktualizovat statistiku bez ohledu na to, kdy se aktualizovaly naposledy.  
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

**Datum sloupce** v datovém skladu, například obvykle třeba často aktualizace statistiky. Každé nové řádky času jsou načtená do datového skladu, nové zatížení kalendářní data nebo data transakcí se přidají. Tyto změnit distribuci dat a proveďte statistiku zastaralá.  Naopak statistiky o pohlaví sloupec v tabulce zákazníka může být nikdy potřeba aktualizovat. Za předpokladu, že distribuce je konstantní mezi odběrateli, přidávání nových řádků do tabulky variace není chystáte změnit rozdělení data. Ale pokud váš datový sklad obsahuje pouze jeden pohlaví a nový požadavek výsledkem více pohlaví, pak budete muset aktualizovat statistiku pohlaví sloupec.

Další informace najdete v tématu obecné pokyny pro [statistiky](/sql/relational-databases/statistics/statistics).

## <a name="implementing-statistics-management"></a>Implementace správy statistiky
Často je vhodné rozšířit váš proces načítání dat zajistit, že se statistika aktualizuje na konci zatížení. Načtení dat je při tabulky nejčastěji změnit jejich velikost a jejich distribuci hodnoty. To je proto logické místo, kde můžete implementovat některé procesy správy.

Tyto zásady jsou zadané pro aktualizaci statistice během procesu načítání:

* Zajistěte, aby každá tabulka načíst minimálně jeden objekt statistiky aktualizovat. Tím se aktualizuje informace o tabulce velikost (počet řádků a počet stránek) jako součást aktualizace statistiky.
* Soustřeďte se na sloupců podílejících se na připojení, GROUP BY, ORDER BY a DISTINCT klauzulí.
* Zvažte aktualizaci "vzestupné klíč" sloupců, jako je transakce data častěji, protože tyto hodnoty nebudou zahrnuty do histogram statistiky.
* Zvažte aktualizaci statické distribuční sloupce méně často.
* Pamatujte si, že se každý objekt statistiky se aktualizuje v pořadí. Implementací `UPDATE STATISTICS <TABLE_NAME>` není vždy ideální, zejména pro široké tabulky s mnoha objekty statistiky.

Další informace najdete v tématu [odhadu kardinality](/sql/relational-databases/performance/cardinality-estimation-sql-server).

## <a name="examples-create-statistics"></a>Příklady: Vytvoření statistiky
Tyto příklady ukazují, jak používat různé možnosti pro vytvoření statistiky. Možnosti, které používáte pro každý sloupec závisí na vlastnosti data a jak sloupec se použije v dotazech.

### <a name="create-single-column-statistics-with-default-options"></a>Vytvoření statistiky jednoho sloupce s výchozími možnostmi
Chcete-li vytvoření statistiky pro sloupec, stačí zadáte název pro objekt statistiky a název sloupce.

Tuto syntaxi používá všechny výchozí možnosti. Ve výchozím nastavení SQL Data Warehouse ukázky **20 procent** tabulky při vytváření statistik.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Příklad:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="create-single-column-statistics-by-examining-every-row"></a>Vytvořit jednosloupcovou statistiku tak, že prověří každý řádek
Pro většině případů stačí výchozí vzorkovací frekvenci 20 procent. Můžete však upravit vzorkovací frekvenci.

Chcete-li ukázkové úplné tabulky, použijte následující syntaxi:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Příklad:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Vytvořte jednosloupcovou statistiku zadáním velikost vzorku
Alternativně můžete určit velikost vzorku v procentech:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Vytvořit jednosloupcovou statistiku pro jenom některé řádky
Můžete také vytvořit statistiku na část řádky v tabulce. Tomu se říká filtrované statistiky.

Například můžete použít filtrovanou statistiku, když chcete zadat dotaz na konkrétní oddíl velkých oddílů tabulky. Vytvořením statistiky na pouze hodnoty oddílu se přesnost statistik pro zlepšení a proto zlepšit výkon dotazu.

Tento příklad vytvoří statistiky na rozsah hodnot. Hodnoty lze snadno definovat tak, aby odpovídaly rozsahu hodnot v oddílu.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Pro Optimalizátor dotazů, zvažte možnost použití filtrovanou statistiku, když se vybere plánu distribuovaných dotazů dotaz musí vejít do definice objektu statistiky. Použijeme předchozí příklad dotazu kde klauzule musí určovat Sloupec1 hodnoty mezi 2000101 a 20001231.
> 
> 

### <a name="create-single-column-statistics-with-all-the-options"></a>Vytvořit jednosloupcovou statistiku se všemi možnostmi
Můžete také kombinovat možnosti společně. Následující příklad vytvoří objekt filtrovanou statistiku s velikost vlastní vzorku:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Úplný přehled najdete v tématu [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql).

### <a name="create-multi-column-statistics"></a>Vytvoření statistiky více sloupci
Vytvoření objektu vícesloupcového statistiky, jednoduše použijte v předchozích příkladech, ale zadat více sloupců.

> [!NOTE]
> Histogram, který slouží k zjištění přibližné hodnoty počet řádků ve výsledku dotazu, je dostupná jenom pro první sloupec uvedené v definici objektu statistiky.
> 
> 

V tomto příkladu je histogramu na *produktu\_kategorie*. Statistiky mezi sloupce jsou vypočítány na *produktu\_kategorie* a *produktu\_sub_category*:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Vzhledem k tomu, že existuje korelace mezi *produktu\_kategorie* a *produktu\_sub\_kategorie*, objekt vícesloupcového statistiky může být užitečné, pokud tyto sloupce jsou dostupné ve stejnou dobu.

### <a name="create-statistics-on-all-columns-in-a-table"></a>Vytvoření statistiky pro všechny sloupce v tabulce
Jeden způsob, jak vytvořit statistiky je vystavit příkazy CREATE STATISTICS po vytvoření tabulky:

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

### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Vytvoření statistiky pro všechny sloupce v databázi pomocí uložené procedury
SQL Data Warehouse nemá ekvivalentní sp_create_stats systémové uložené procedury v systému SQL Server. Tato uložená procedura vytvoří objekt statistiky jeden sloupec pro každý sloupec databáze, který ještě nemá statistiky.

Následující příklad vám pomůže začít pracovat s návrhu databáze. Nebojte se, že jej přizpůsobit vašim potřebám:

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

Vytvoření statistiky pro všechny sloupce v tabulce s využitím výchozí hodnoty, jednoduše voláním procedury.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```
Chcete-li vytvoření statistiky pro všechny sloupce v tabulce s využitím fullscan, volejte tento postup:

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```
Vytvořte vzorky statistiku pro všechny sloupce v tabulce, zadejte 3 a ukázkové procent.  Této procedury používá vzorkovací frekvence 20 procent.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```


Chcete-li vytvořit vzorkovat statistiky pro všechny sloupce 

## <a name="examples-update-statistics"></a>Příklady: Aktualizovat statistiky
Chcete-li aktualizovat statistiku, můžete:

- Aktualizujte jeden objekt statistiky. Zadejte název objektu statistiku, který chcete aktualizovat.
- Aktualizujte všechny statistiky objekty v tabulce. Zadejte název tabulky místo jeden objekt konkrétní statistiku.

### <a name="update-one-specific-statistics-object"></a>Aktualizovat jeden objekt konkrétní Statistika
Aktualizovat objekt konkrétní statistiku použijte následující syntaxi:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Příklad:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Při aktualizaci statistiky konkrétní objekty, můžete snížit čas a prostředky, které jsou nezbytné ke správě statistiky. To vyžaduje některé představit vybrat nejlepší statistiky objekty, které chcete aktualizovat.

### <a name="update-all-statistics-on-a-table"></a>Aktualizovat všechny statistiky v tabulce
Ukazuje to jednoduše aktualizace všechny statistiky objektů v tabulce:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Příklad:

```sql
UPDATE STATISTICS dbo.table1;
```

Tento příkaz je snadno použitelný. Jenom nezapomeňte, že aktualizace *všechny* statistiky v tabulce a proto může provést další práci, než je nezbytné. Pokud výkon není problém, jedná se nejúplnější a nejjednodušší způsob, jak zajistit, aby se statistiky aktuální.

> [!NOTE]
> Při aktualizaci všechny statistiky v tabulce se SQL Data Warehouse nepodporuje vyhledávání s cílem ukázková tabulka pro každý objekt statistiky. Pokud v tabulce je velký a má mnoho sloupců a mnoho statistiky, může to být efektivnější jednotlivých statistiku podle potřeb.
> 
> 

Implementace `UPDATE STATISTICS` postupu najdete v části [dočasných tabulek](sql-data-warehouse-tables-temporary.md). Implementace metody se mírně liší od předchozí `CREATE STATISTICS` postup, ale výsledek je stejný.

Úplná syntaxe, najdete v části [Update Statistics](/sql/t-sql/statements/update-statistics-transact-sql).

## <a name="statistics-metadata"></a>Statistiky metadat
Existuje několik zobrazení systému a funkcí, které můžete použít k nalezení informací o statistikách. Například se zobrazí, pokud objekt statistiky může být zastaralý pomocí funkce statistiky date a zjistěte, kdy byly statistiky poslední vytvořil nebo aktualizoval.

### <a name="catalog-views-for-statistics"></a>Zobrazení katalogu pro statistiky
Tato systémová zobrazení obsahují informace o statistiky:

| zobrazení katalogu | Popis |
|:--- |:--- |
| [sys.columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql) |Jeden řádek pro každý sloupec. |
| [sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql) |Jeden řádek pro každý objekt v databázi. |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql) |Jeden řádek pro každý schématu v databázi. |
| [Sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql) |Jeden řádek pro každý objekt statistiky. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql) |Jeden řádek pro každý sloupec v objektu statistiky. Odkazy Zpět na sys.columns. |
| [zobrazení Sys.Tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql) |Jeden řádek pro každou tabulku (zahrnuje externí tabulky). |
| [Sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql) |Jeden řádek pro každý typ dat. |

### <a name="system-functions-for-statistics"></a>Funkce systému pro statistiky
Tyto funkce systému jsou užitečné pro práci s statistiky:

| System – funkce | Popis |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql) |Datum poslední aktualizace objekt statistiky. |
| [PŘÍKAZ DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql) |Souhrn úrovně a podrobné informace o distribuci hodnot jako rozumí objekt statistiky. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Zkombinovat do jednoho zobrazení statistiky sloupce a funkce
Toto zobrazení přináší sloupce, které se týkají statistiky a výsledkem funkce STATS_DATE() společně.

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_defiinition]
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
Příkaz DBCC SHOW_STATISTICS() zobrazuje data ukládaná v rámci objektu statistiky. Tato data je rozdělena na tři části:

- Záhlaví
- Hustotu vektoru
- Histogram

Hlavička metadata o statistikách. Histogramu zobrazí rozdělení hodnot ve sloupci první klíče objektu statistiky. Vektor hustotu měří korelace mezi sloupci. SQL Data Warehouse vypočítá mohutnost odhady s žádným z dat v objektu statistiky.

### <a name="show-header-density-and-histogram"></a>Zobrazit záhlaví, hustotu a histogram
Tento jednoduchý příklad ukazuje všechny tři částí objektu statistiky:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Příklad:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-showstatistics"></a>Zobrazit jednu nebo více částí DBCC SHOW_STATISTICS()
Pokud byste chtěli pouze v zobrazení konkrétní části, použijte `WITH` klauzule a zadejte části, které chcete zobrazit:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Příklad:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-showstatistics-differences"></a>Příkaz DBCC SHOW_STATISTICS() rozdíly
Příkaz DBCC SHOW_STATISTICS() je více výhradně implementované v SQL Data Warehouse ve srovnání s systému SQL Server:

- Nezdokumentovaný funkce nejsou podporovány.
- Nelze použít Stats_stream.
- Nemůže připojit k výsledky pro konkrétní podmnožiny dat statistiky. Například (STAT_HEADER připojení DENSITY_VECTOR).
- NO_INFOMSGS nelze nastavit pro potlačení zprávy.
- Hranaté závorky kolem názvů statistiky nelze použít.
- Názvy sloupců nelze použít k identifikaci objektů statistiky.
- Vlastní chyba 2767 není podporována.

## <a name="next-steps"></a>Další postup
Pro další zlepšit výkon dotazů najdete v tématu [monitorování úlohy](sql-data-warehouse-manage-monitor.md)

