---
title: Pokyny k návrhu replikovaných tabulek
description: Doporučení pro návrh replikovaných tabulek ve fondu SQL synapse
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/19/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 7dcb884d8eafdfa5218e96d63f62a5d462d20cf8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98679926"
---
# <a name="design-guidance-for-using-replicated-tables-in-synapse-sql-pool"></a>Pokyny k návrhu pro použití replikovaných tabulek ve fondu SQL synapse

Tento článek obsahuje doporučení pro návrh replikovaných tabulek ve schématu synapse fondu SQL. Pomocí těchto doporučení můžete zlepšit výkon dotazů tím, že snížíte pohyb dat a složitost dotazů.

> [!VIDEO https://www.youtube.com/embed/1VS_F37GI9U]

## <a name="prerequisites"></a>Předpoklady

V tomto článku se předpokládá, že máte zkušenosti s koncepty distribuce dat a přesunu dat ve fondu SQL.  Další informace najdete v článku o [architektuře](massively-parallel-processing-mpp-architecture.md) .

Jako součást návrhu tabulky Pochopte co nejvíce dat a způsob dotazování na data.  Zvažte například tyto otázky:

- Jak velká je tabulka?
- Jak často je tabulka aktualizována?
- Mám tabulky faktů a dimenzí ve fondu SQL?

## <a name="what-is-a-replicated-table"></a>Co je replikovaná tabulka?

Replikovaná tabulka má úplnou kopii tabulky přístupné na každém výpočetním uzlu. Replikace tabulky eliminuje nutnost převádět data mezi výpočetními uzly před spojením nebo agregací. Vzhledem k tomu, že tabulka obsahuje více kopií, replikované tabulky fungují nejlépe, pokud je velikost tabulky méně než 2 GB komprimovaná.  2 GB není pevný limit.  Pokud jsou data statická a nemění se, můžete replikovat větší tabulky.

Následující diagram znázorňuje replikovanou tabulku, která je přístupná na každém výpočetním uzlu. V rámci fondu SQL je replikovaná tabulka plně zkopírována do distribuční databáze na každém výpočetním uzlu.

![Replikovaná tabulka](./media/design-guidance-for-replicated-tables/replicated-table.png "Replikovaná tabulka")  

Replikované tabulky dobře fungují pro tabulky dimenzí ve schématu hvězdičky. Tabulky dimenzí jsou obvykle spojeny s tabulkami faktů, které jsou distribuovány jinak než tabulka dimenze.  Dimenze mají obvykle velikost, která umožňuje uložit a spravovat více kopií. Dimenze ukládají popisné údaje, které se mění pomalu, například jméno zákazníka a adresa a podrobnosti o produktu. Pomalá změna povahy dat vede k menší údržbě replikované tabulky.

Při použití replikované tabulky zvažte následující:

- Velikost tabulky na disku je menší než 2 GB, bez ohledu na počet řádků. Chcete-li zjistit velikost tabulky, můžete použít příkaz [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) : `DBCC PDW_SHOWSPACEUSED('ReplTableCandidate')` .
- Tabulka se používá ve spojeních, která by jinak vyžadovala přesun dat. Při spojování tabulek, které nejsou distribuované na stejném sloupci, jako je tabulka distribuovaná pomocí algoritmu hash, do tabulky kruhového dotazování, je pro dokončení dotazu potřeba přesun dat.  Pokud je jedna z tabulek malá, vezměte v úvahu replikovanou tabulku. Ve většině případů doporučujeme použít replikované tabulky místo tabulek kruhového dotazování. Chcete-li zobrazit operace přesunu dat v plánech dotazů, použijte [Sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  BroadcastMoveOperation je typická operace přesunu dat, kterou lze eliminovat pomocí replikované tabulky.  

Replikované tabulky nemůžou poskytovat nejlepší výkon dotazů v těchto případech:

- Tabulka obsahuje časté operace vložení, aktualizace a odstranění. Operace jazyka DML (data Language) vyžadují opětovné sestavení replikované tabulky. Opakované sestavování může způsobit pomalejší výkon.
- Fond SQL se často škáluje. Škálování fondu SQL změní počet výpočetních uzlů, které způsobí opakované sestavení replikované tabulky.
- Tabulka má velký počet sloupců, ale datové operace obvykle mají přístup pouze k malému počtu sloupců. V tomto scénáři může být místo replikace celé tabulky efektivnější, aby se tabulka rozšíří a potom se vytvořil index na často používaných sloupcích. Když dotaz vyžaduje přesun dat, přesune SQL fond pouze data pro požadované sloupce.

## <a name="use-replicated-tables-with-simple-query-predicates"></a>Použití replikovaných tabulek s jednoduchými predikáty dotazů

Než se rozhodnete distribuovat nebo replikovat tabulku, zamyslete se nad typy dotazů, které v tabulce plánujete spustit. Kdykoli je to možné,

- Používejte replikované tabulky pro dotazy s jednoduchými predikáty dotazů, jako je rovnost nebo nerovnost.
- Použijte distribuované tabulky pro dotazy se složitými predikáty dotazů, jako např. například LIKE nebo NOT.

Dotazy náročné na procesor fungují nejlépe při distribuci práce napříč všemi výpočetními uzly. Například dotazy, které spouštějí výpočty na jednotlivých řádcích tabulky, fungují lépe v distribuovaných tabulkách než replikované tabulky. Vzhledem k tomu, že replikovaná tabulka je uložená v plném rozsahu na každém výpočetním uzlu, spouští se dotaz náročný na procesor proti replikované tabulce na všech výpočetních uzlech na celé tabulce. Dodatečný výpočet může zpomalit výkon dotazů.

Tento dotaz má například složitý predikát.  Spouští se rychleji, když jsou data v distribuované tabulce místo v replikované tabulce. V tomto příkladu může být data distribuována kruhové dotazování.

```sql

SELECT EnglishProductName
FROM DimProduct
WHERE EnglishDescription LIKE '%frame%comfortable%'

```

## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>Převést existující tabulky kruhového dotazování na replikované tabulky

Pokud již máte tabulky kruhového dotazování, doporučujeme je převést na replikované tabulky, pokud splňují kritéria uvedená v tomto článku. Replikované tabulky zlepšují výkon přes tabulky kruhového dotazování, protože eliminují nutnost přesunu dat.  Tabulka kruhového dotazování vždy vyžaduje přesun dat pro spojení.

V tomto příkladu se používá [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) ke změně tabulky DimSalesTerritory na replikovanou tabulku. Tento příklad funguje bez ohledu na to, jestli je DimSalesTerritoryy distribuované hodnoty hash nebo kruhové dotazování.

```sql
CREATE TABLE [dbo].[DimSalesTerritory_REPLICATE]
WITH
  (
    CLUSTERED COLUMNSTORE INDEX,  
    DISTRIBUTION = REPLICATE  
  )  
AS SELECT * FROM [dbo].[DimSalesTerritory]
OPTION  (LABEL  = 'CTAS : DimSalesTerritory_REPLICATE')

-- Switch table names
RENAME OBJECT [dbo].[DimSalesTerritory] to [DimSalesTerritory_old];
RENAME OBJECT [dbo].[DimSalesTerritory_REPLICATE] TO [DimSalesTerritory];

DROP TABLE [dbo].[DimSalesTerritory_old];
```

### <a name="query-performance-example-for-round-robin-versus-replicated"></a>Příklad výkonu dotazů pro kruhové dotazování versus replikaci

Replikovaná tabulka nevyžaduje žádné přesuny dat pro spojení, protože celá tabulka je již na každém výpočetním uzlu přítomna. Pokud jsou tabulky dimenzí distribuované kruhové dotazování, připojí se tabulka Dimension v plném rozsahu ke každému výpočetnímu uzlu. K přesunu dat plán dotazu obsahuje operaci nazvanou BroadcastMoveOperation. Tento typ operace přesunu dat zpomaluje dotazování a eliminuje se pomocí replikovaných tabulek. Chcete-li zobrazit kroky plánu dotazů, použijte zobrazení katalog systému [Sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .  

Například v následujících dotazech na schéma AdventureWorks `FactInternetSales` je tabulka rozložená pomocí algoritmu hash. `DimDate`Tabulky a `DimSalesTerritory` jsou menší tabulky dimenzí. Tento dotaz vrátí celkový prodej v Severní Amerika pro fiskální rok 2004:

```sql
SELECT [TotalSalesAmount] = SUM(SalesAmount)
FROM dbo.FactInternetSales s
INNER JOIN dbo.DimDate d
  ON d.DateKey = s.OrderDateKey
INNER JOIN dbo.DimSalesTerritory t
  ON t.SalesTerritoryKey = s.SalesTerritoryKey
WHERE d.FiscalYear = 2004
  AND t.SalesTerritoryGroup = 'North America'
```

Znovu jsme vytvořili `DimDate` a `DimSalesTerritory` jako tabulky kruhového dotazování. V důsledku toho dotaz vykázal následující plán dotazu, který obsahuje více operací přesunu všesměrového vysílání:

![Plán dotazů kruhového dotazování](./media/design-guidance-for-replicated-tables/round-robin-tables-query-plan.jpg)

Znovu jsme vytvořili `DimDate` a `DimSalesTerritory` jako replikované tabulky a spustili dotaz znovu. Výsledný plán dotazu je mnohem kratší a nemá žádné přesuny všesměrového vysílání.

![Plán replikovaných dotazů](./media/design-guidance-for-replicated-tables/replicated-tables-query-plan.jpg)

## <a name="performance-considerations-for-modifying-replicated-tables"></a>Požadavky na výkon pro úpravu replikovaných tabulek

Fond SQL implementuje replikovanou tabulku udržováním hlavní verze tabulky. Zkopíruje hlavní verzi do první distribuční databáze na každém výpočetním uzlu. Pokud dojde ke změně, je nejprve aktualizována hlavní verze a pak se znovu sestaví tabulky na každém výpočetním uzlu. Nové sestavení replikované tabulky zahrnuje kopírování tabulky do každého výpočetního uzlu a následné sestavení indexů.  Například replikovaná tabulka na DW2000c má 5 kopií dat.  Hlavní kopii a úplnou kopii na každém výpočetním uzlu.  Všechna data jsou uložená v distribučních databázích. Fond SQL používá tento model k podpoře rychlejších příkazů pro úpravu dat a flexibilních operací škálování.

Asynchronní opětovná sestavení se spouštějí prvním dotazem na replikovanou tabulku po:

- Data se načítají nebo upravují.
- Instance SQL synapse se škáluje na jinou úroveň.
- Definice tabulky je aktualizovaná.

Nové sestavení nejsou požadovány po:

- Operace pozastavení
- Operace obnovení

Nové sestavení se nestane hned po úpravě dat. Místo toho se nové sestavení aktivuje při prvním výběru dotazu z tabulky.  Dotaz, který spustil opětovné sestavení, je okamžitě z hlavní verze tabulky, zatímco data jsou asynchronně zkopírována do každého výpočetního uzlu. Až do dokončení kopírování dat budou následné dotazy dál používat hlavní verzi tabulky.  Pokud dojde k nějaké aktivitě na replikované tabulce, která vynucuje jiné opětovné sestavení, bude zrušena platnost kopie dat a další příkaz SELECT bude aktivovat data, která se mají znovu zkopírovat.

### <a name="use-indexes-conservatively"></a>Používejte indexy uvážlivě

Standardní postupy indexování se vztahují na replikované tabulky. Fond SQL znovu sestaví každý replikovaný index tabulky jako součást opětovného sestavení. Indexy se používají jenom v případě, že výkon převyšuje náklady na opětovné sestavení indexů.

### <a name="batch-data-load"></a>Dávkové načítání dat

Při načítání dat do replikovaných tabulek se pokuste minimalizovat sestavení pomocí dávkového načtení. Před spuštěním příkazů SELECT proveďte všechna dávkovaná načtení.

Například tento vzor zatížení načte data ze čtyř zdrojů a vyvolá čtyři opětovná sestavení.

- Zatížení ze zdroje 1.
- Příkaz SELECT Trigger spustí znovu sestavení 1.
- Zatížení ze zdroje 2.
- Příkaz SELECT Trigger znovu sestaví 2.
- Zatížení ze zdroje 3.
- Příkaz SELECT Triggers spustí znovu sestavení 3.
- Zatížení ze zdroje 4.
- Příkaz SELECT Trigger spustí znovu sestavení 4.

Například tento vzor zatížení načte data ze čtyř zdrojů, ale vyvolá pouze jedno opětovné sestavení.

- Zatížení ze zdroje 1.
- Zatížení ze zdroje 2.
- Zatížení ze zdroje 3.
- Zatížení ze zdroje 4.
- Příkaz SELECT Trigger se znovu sestaví.

### <a name="rebuild-a-replicated-table-after-a-batch-load"></a>Po načtení dávky znovu sestavit replikovanou tabulku

Chcete-li zajistit konzistentní dobu provádění dotazů, zvažte vynucení sestavení replikovaných tabulek po načtení dávky. V opačném případě bude první dotaz dál používat přesun dat k dokončení dotazu.

Tento dotaz používá [Sys.pdw_replicated_table_cache_state](/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) DMV k vypsání replikovaných tabulek, které byly upraveny, ale ne znovu sestaveny.

```sql
SELECT [ReplicatedTable] = t.[name]
  FROM sys.tables t  
  JOIN sys.pdw_replicated_table_cache_state c  
    ON c.object_id = t.object_id
  JOIN sys.pdw_table_distribution_properties p
    ON p.object_id = t.object_id
  WHERE c.[state] = 'NotReady'
    AND p.[distribution_policy_desc] = 'REPLICATE'
```

Chcete-li spustit opětovné sestavení, spusťte následující příkaz u každé tabulky v předchozím výstupu.

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
```

## <a name="next-steps"></a>Další kroky

Chcete-li vytvořit replikovanou tabulku, použijte jeden z následujících příkazů:

- [CREATE TABLE (fond SQL)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [CREATE TABLE jako SELECT (fond SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

Přehled distribuovaných tabulek najdete v tématu [distribuované tabulky](sql-data-warehouse-tables-distribute.md).
