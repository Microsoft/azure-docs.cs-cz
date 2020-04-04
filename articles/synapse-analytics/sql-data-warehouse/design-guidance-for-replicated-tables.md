---
title: Pokyny k návrhu pro replikované tabulky
description: Doporučení pro navrhování replikovaných tabulek v synapse SQL
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/19/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 128b4203d34b99df8363ef19783baa4a7b608aa5
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631318"
---
# <a name="design-guidance-for-using-replicated-tables-in-sql-analytics"></a>Pokyny k návrhu pro použití replikovaných tabulek v sql analytics

Tento článek obsahuje doporučení pro navrhování replikovaných tabulek ve schématu SQL Analytics. Tato doporučení slouží ke zlepšení výkonu dotazu snížením pohybu dat a složitosti dotazu.

> [!VIDEO https://www.youtube.com/embed/1VS_F37GI9U]

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste obeznámeni s koncepty distribuce dat a přesunu dat v SQL Analytics.Další informace naleznete v článku [architektury.](massively-parallel-processing-mpp-architecture.md)

V rámci návrhu tabulky pochopte co nejvíce informací o datech a způsobu dotazování na data.Zvažte například tyto otázky:

- Jak velký je stůl?
- Jak často se tabulka aktualizuje?
- Mám tabulky faktů a dimenzí v databázi SQL Analytics?

## <a name="what-is-a-replicated-table"></a>Co je replikovaná tabulka?

Replikovaná tabulka má úplnou kopii tabulky přístupné na každém výpočetním uzlu. Replikace tabulky eliminuje nutnost převádět data mezi výpočetními uzly před spojením nebo agregací. Vzhledem k tomu, že tabulka obsahuje více kopií, replikované tabulky fungují nejlépe, pokud je velikost tabulky menší než 2 GB komprimované.  2 GB není pevný limit.  Pokud jsou data statická a nezmění se, můžete replikovat větší tabulky.

Následující diagram znázorňuje replikovanou tabulku, která je přístupná na každém výpočetním uzlu. V SQL Analytics je replikovaná tabulka plně zkopírována do distribuční databáze na každém výpočetním uzlu.

![Replikovaná tabulka](./media/design-guidance-for-replicated-tables/replicated-table.png "Replikovaná tabulka")  

Replikované tabulky fungují dobře pro tabulky dimenzí ve schématu hvězd. Tabulky dimenzí jsou obvykle spojeny s tabulkami faktů, které jsou distribuovány jinak než tabulka dimenzí.  Dimenze jsou obvykle velikosti, která umožňuje ukládání a údržbu více kopií. Dimenze ukládají popisná data, která se mění pomalu, například jméno a adresa zákazníka a podrobnosti o produktu. Pomalu se měnící povaha dat vede k menší údržbě replikované tabulky.

Použití replikované tabulky zvažte v následujících možnostech:

- Velikost tabulky na disku je menší než 2 GB, bez ohledu na počet řádků. Chcete-li najít velikost tabulky, můžete použít příkaz `DBCC PDW_SHOWSPACEUSED('ReplTableCandidate')` [DBCC PDW_SHOWSPACEUSED:](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) .
- Tabulka se používá ve spojeních, která by jinak vyžadovala přesun dat. Při spojování tabulek, které nejsou distribuovány ve stejném sloupci, jako je například tabulka distribuovaná hodnotou hash, s tabulkou kruhového dotazování, je k dokončení dotazu nutné přesun dat.  Pokud je jedna z tabulek malá, zvažte replikovanou tabulku. Ve většině případů doporučujeme místo kulatých stolků používat replikované tabulky. Chcete-li zobrazit operace přesunu dat v plánech dotazů, použijte [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  BroadcastMoveOperation je typická operace přesunu dat, která může být eliminována pomocí replikované tabulky.  

Replikované tabulky nemusí přinést nejlepší výkon dotazu, pokud:

- Tabulka obsahuje časté operace vkládání, aktualizace a odstraňování.Operace jazyka pro manipulaci s daty (DML) vyžadují opětovné sestavení replikované tabulky.Časté opětovné sestavení může způsobit pomalejší výkon.
- Databáze SQL Analytics je často škálována. Škálování databáze SQL Analytics změní počet výpočetních uzlů, které vzniknou opětovné sestavení replikované tabulky.
- Tabulka má velký počet sloupců, ale datové operace obvykle přístup pouze malý počet sloupců. V tomto scénáři namísto replikace celé tabulky může být efektivnější distribuovat tabulku a potom vytvořit index na často používaných sloupcích. Pokud dotaz vyžaduje přesun dat, SQL Analytics přesune pouze data pro požadované sloupce.

## <a name="use-replicated-tables-with-simple-query-predicates"></a>Použití replikovaných tabulek s jednoduchými predikáty dotazů

Než se rozhodnete distribuovat nebo replikovat tabulku, přemýšlejte o typech dotazů, které chcete spustit s tabulkou. Kdykoli je to možné,

- Replikované tabulky používejte pro dotazy s jednoduchými predikáty dotazů, jako je rovnost nebo nerovnost.
- Distribuované tabulky používejte pro dotazy se složitými predikáty dotazů, například LIKE nebo NOT LIKE.

Dotazy náročné na procesor fungují nejlépe, když je práce distribuována ve všech výpočetních uzlech. Například dotazy, které spouštějí výpočty na každém řádku tabulky, fungují lépe v distribuovaných tabulkách než v replikovaných tabulkách. Vzhledem k tomu, že replikovaná tabulka je uložena v plném rozsahu na každém výpočetním uzlu, dotaz náročný na procesor proti replikované tabulce běží proti celé tabulce na každém výpočetním uzlu. Další výpočty může zpomalit výkon dotazu.

Tento dotaz má například komplexní predikát.  Běží rychleji, když jsou data v distribuované tabulce namísto replikované tabulky. V tomto příkladu mohou být data distribuována za obepínají.

```sql

SELECT EnglishProductName
FROM DimProduct
WHERE EnglishDescription LIKE '%frame%comfortable%'

```

## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>Převést existující tabulky kruhového dotazování na replikované tabulky

Pokud již máte tabulky kruhového dotazování, doporučujeme je převést na replikované tabulky, pokud splňují kritéria uvedená v tomto článku. Replikované tabulky zlepšují výkon u tabulek kruhového dotazování, protože eliminují potřebu přesunu dat.  Tabulka kruhového dotazování vždy vyžaduje přesun dat pro spojení.

Tento příklad používá [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) ke změně tabulky DimSalesTerritory na replikovanou tabulku. Tento příklad funguje bez ohledu na to, zda DimSalesTerritory je hash distribuované nebo kruhové dotazování.

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

### <a name="query-performance-example-for-round-robin-versus-replicated"></a>Příklad výkonu dotazu pro dotazování a replikaci

Replikovaná tabulka nevyžaduje žádný přesun dat pro spojení, protože celá tabulka je již k dispozici na každém výpočetním uzlu. Pokud jsou tabulky dimenzí distribuovány za každým svatou, spojení zkopíruje tabulku dimenzí v plném rozsahu do každého výpočetního uzlu. Chcete-li přesunout data, plán dotazu obsahuje operaci s názvem BroadcastMoveOperation. Tento typ operace přesunu dat zpomaluje výkon dotazu a je eliminován pomocí replikovaných tabulek. Chcete-li zobrazit kroky plánu dotazů, použijte zobrazení katalogu [systému sys.dm_pdw_request_steps.](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  

Například v následující dotaz proti AdventureWorks schéma, `FactInternetSales` tabulka je hash distribuované. Tabulky `DimDate` `DimSalesTerritory` a jsou tabulky menších dimenzí. Tento dotaz vrátí celkový prodej v Severní Americe za fiskální rok 2004:

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

Znovu jsme `DimDate` vytvořili a `DimSalesTerritory` jako kulaté robin tabulky. V důsledku toho dotaz ukázal následující plán dotazu, který má více operací přesunu všesměrového vysílání:

![Plán dotazů kruhového dotazování](./media/design-guidance-for-replicated-tables/round-robin-tables-query-plan.jpg)

Znovu jsme `DimDate` vytvořili a `DimSalesTerritory` jako replikované tabulky a znovu spustili dotaz. Výsledný plán dotazů je mnohem kratší a nemá žádné přesuny vysílání.

![Plán replikovaných dotazů](./media/design-guidance-for-replicated-tables/replicated-tables-query-plan.jpg)

## <a name="performance-considerations-for-modifying-replicated-tables"></a>Důležité informace o výkonu při úpravách replikovaných tabulek

SQL Analytics implementuje replikovanou tabulku udržováním hlavní verze tabulky. Zkopíruje hlavní verzi do jedné distribuční databáze na každém výpočetním uzlu. Pokud dojde ke změně, SQL Analytics nejprve aktualizuje hlavní tabulku. Potom znovu vytvoří tabulky na každém uzlu Compute. Opětovné sestavení replikované tabulky zahrnuje zkopírování tabulky do každého výpočetního uzlu a následné sestavení indexů.  Například replikovaná tabulka na DW400 obsahuje 5 kopií dat.  Hlavní kopie a úplná kopie na každém výpočetním uzlu.  Všechna data jsou uložena v distribučních databázích. SQL Analytics používá tento model k podpoře rychlejších příkazů pro úpravy dat a flexibilních operací škálování.

Znovusestavení jsou vyžadována po:

- Data jsou načtena nebo změněna.
- Instance Synapse SQL je škálována na jinou úroveň.
- Definice tabulky je aktualizována.

Opětovné sestavení nejsou vyžadovány po:

- Pozastavení operace
- Obnovit provoz

Opětovné sestavení nedojde ihned po změně dat. Místo toho je opětovné sestavení spuštěno při prvním výběru dotazu z tabulky.  Dotaz, který spustil opětovné sestavení, se přečte okamžitě z hlavní verze tabulky, zatímco data jsou asynchronně zkopírována do každého výpočetního uzlu. Dokud nebude kopie dat dokončena, následné dotazy budou nadále používat hlavní verzi tabulky.  Pokud dojde k jakékoli aktivitě proti replikované tabulce, která vynutí další opětovné sestavení, bude kopie dat zrušena a další příkaz select spustí opětovné zkopírování dat.

### <a name="use-indexes-conservatively"></a>Používejte indexy konzervativně

Standardní postupy indexování platí pro replikované tabulky. SQL Analytics znovu vytvoří každý replikovaný index tabulky jako součást opětovného sestavení. Indexy používejte pouze v případě, že zvýšení výkonu převáží náklady na opětovné sestavení indexů.

### <a name="batch-data-load"></a>Dávkové načítání dat

Při načítání dat do replikovaných tabulek se pokuste minimalizovat opětovné sestavení dávkou zatížení dohromady. Před spuštěním příkazů select proveďte všechna dávková zatížení.

Například tento vzorek zatížení načte data ze čtyř zdrojů a vyvolá čtyři znovusestavit.

        Load from source 1.
- Select příkaz aktivuje opětovné sestavení 1.
        Zatížení ze zdroje 2.
- Select příkaz aktivuje opětovné sestavení 2.
- Zatížení ze zdroje 3.
- Příkaz Select aktivuje opětovné sestavení 3.
- Zatížení ze zdroje 4.
- Select příkaz aktivuje opětovné sestavení 4.

Například tento vzorek zatížení načte data ze čtyř zdrojů, ale vyvolá pouze jedno opětovné sestavení.

- Zatížení ze zdroje 1.
- Zatížení ze zdroje 2.
- Zatížení ze zdroje 3.
- Zatížení ze zdroje 4.
- Select příkaz aktivuje opětovné sestavení.

### <a name="rebuild-a-replicated-table-after-a-batch-load"></a>Opětovné sestavení replikované tabulky po dávkovém zatížení

Chcete-li zajistit konzistentní časy provádění dotazů, zvažte vynucení sestavení replikovaných tabulek po dávkovém načtení. V opačném případě bude první dotaz stále používat přesun dat k dokončení dotazu.

Tento dotaz používá [sys.pdw_replicated_table_cache_state](/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) DMV seznam replikovaných tabulek, které byly změněny, ale nebyly znovu sestaveny.

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

Chcete-li spustit opětovné sestavení, spusťte následující příkaz v každé tabulce v předchozím výstupu.

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
```

## <a name="next-steps"></a>Další kroky

Chcete-li vytvořit replikovanou tabulku, použijte jeden z těchto příkazů:

- [VYTVOŘIT TABULKU (SQL Analytics)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [VYTVOŘIT TABULKU JAKO VÝBĚR (SQL Analytics)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

Přehled distribuovaných tabulek naleznete v [tématu distribuované tabulky](sql-data-warehouse-tables-distribute.md).
