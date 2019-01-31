---
title: Pro replikované tabulky – Azure SQL Data Warehouse s pokyny k návrhu | Dokumentace Microsoftu
description: Doporučení k návrhu replikovaných tabulkách ve schématu Azure SQL Data Warehouse. 
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/23/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 5c791dc8216a4c905b4147f59a42d52091f14aae
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55465975"
---
# <a name="design-guidance-for-using-replicated-tables-in-azure-sql-data-warehouse"></a>Pokyny k návrhu pro použití replikované tabulky ve službě Azure SQL Data Warehouse
Tento článek obsahuje doporučení pro navrhování replikované tabulky ve schématu SQL Data Warehouse. Ke zlepšení výkonu dotazů zmenšením složitost přesunu a dotazování dat pomocí těchto doporučení.

> [!VIDEO https://www.youtube.com/embed/1VS_F37GI9U]

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že máte zkušenosti s distribuci dat a koncepty přesouvání dat ve službě SQL Data Warehouse.  Další informace najdete v tématu [architektura](massively-parallel-processing-mpp-architecture.md) článku. 

Jako součást návrh tabulky zjistěte, co nejvíc o vašich datech a jak dotazovat data.  Představte si třeba tyto otázky:

- Jak velké jsou tabulky?   
- Jak často se aktualizuje v tabulce?   
- Musím tabulkami faktů a dimenzí v datovém skladu?   

## <a name="what-is-a-replicated-table"></a>Co je replikované tabulky?
Replikované tabulky má úplnou kopii tabulky, které jsou dostupné na jednotlivých výpočetních uzlech. Replikuje tabulku eliminuje nutnost provádět přenos dat mezi výpočetní uzly před spojení nebo agregace. Protože tabulka má několik kopií, replikované tabulky fungují lépe, když velikost tabulky je menší než 2 GB komprimované.

Následující diagram znázorňuje replikované tabulky, které je dostupné na jednotlivých výpočetních uzlech. Ve službě SQL Data Warehouse replikované tabulce zkopírován plně distribuční databázi na jednotlivých výpočetních uzlech. 

![Replikované tabulky](media/guidance-for-using-replicated-tables/replicated-table.png "replikované tabulky")  

Replikovaných tabulkách fungují dobře u malé tabulky dimenzí v hvězdicovém schématu. Tabulky dimenzí se obvykle velikosti, která je vhodná pro ukládání a správě více kopií. Dimenze ukládat popisný data, která se mění pomalu, jako je například jméno zákazníka a adresy a podrobnosti o produktu. Pomalu se měnící charakteru dat vede k méně znovu sestaví replikované tabulky. 

Zvažte použití replikované tabulky, když:

- Velikost tabulky na disku je menší než 2 GB, bez ohledu na počet řádků. Pokud chcete najít velikost tabulky, můžete použít [DBCC PDW_SHOWSPACEUSED](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql) příkaz: `DBCC PDW_SHOWSPACEUSED('ReplTableCandidate')`. 
- Ve spojení, které by jinak vyžadovaly přesun dat se používá v tabulce. Při spojování tabulek, které nejsou distribuovány ve stejném sloupci, jako je například tabulku hash distribuovat do kruhové dotazování tabulky, přesun dat je předpokladem pro dokončení dotazu.  Pokud některou z tabulek je malá, vezměte v úvahu replikované tabulky. Doporučujeme používat místo tabulek kruhového ve většině případů replikované tabulky. Pokud chcete zobrazit operace přesunu dat v plány dotazů, použijte [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql).  BroadcastMoveOperation je typické datové operace přesunu, který jde je eliminovat pomocí replikované tabulky.  
  Replikované tabulky nemusí poskytovat nejlepší výkon dotazů při:

- Tabulka obsahuje často vložení, aktualizace a odstranění operace. Tyto operace manipulace s jazyk (DML) dat vyžadovat opětovné vytvoření replikované tabulky. Znovu sestavit často může způsobit snížení výkonu.
- Často je škálovat datový sklad. Počet výpočetních uzlů, které s sebou nese náklady opětovné sestavení datového skladu škálování změní.
- Tabulka obsahuje velký počet sloupců, ale operace s daty obvykle přístup pouze malý počet sloupců. V tomto scénáři, namísto replikace celou tabulku může být efektivnější distribuovat v tabulce a pak vytvořit index pro často používané sloupce. Když dotaz potřebuje přesun dat, SQL Data Warehouse pouze přesouvá data pro požadované sloupce. 

## <a name="use-replicated-tables-with-simple-query-predicates"></a>Pomocí jednoduchého dotazu predikáty replikované tabulky
Než budete chtít distribuovat nebo replikovat tabulku, rozmyslete si typy dotazů, které plánujete provozovat s tabulkou. Kdykoli je to možné,

- Použití replikované tabulky pro dotazy pomocí jednoduchého dotazu predikátech, jako je zjištění rovnosti či nerovnosti.
- Použití distribuované tabulky pro dotazy pomocí predikátů složitého dotazu, jako jsou podobné nebo nebude VYHOVOVAT.

Náročnou na procesor dotazů si vede nejlépe při práci se distribuuje do všech výpočetních uzlů. Například dotazy, které spouštění výpočtů na každý řádek tabulky líp fungovat na distribuované tabulky než replikované tabulky. Protože replikované tabulky je uložená v plné výši na jednotlivých výpočetních uzlech, náročnou na procesor proti replikované tabulky. spustí se dotaz celou tabulku na všech výpočetních uzlů. Výpočet navíc můžou způsobit snížení výkonu dotazů.

Tento dotaz má například komplexní predikátu.  To bude rychlejší, když dodavatel je distribuované tabulky namísto replikované tabulky. V tomto příkladu může být dodavatele distribuované kruhové dotazování.

```sql

SELECT EnglishProductName 
FROM DimProduct 
WHERE EnglishDescription LIKE '%frame%comfortable%'

```

## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>Převod existujících tabulek kruhového replikované tabulky
Pokud už máte tabulek kruhového dotazování, doporučujeme převod na replikované tabulky, pokud se splní kritéria uvedených v tomto článku. Protože odstraňují potřebu přesun dat replikovaných tabulkách zlepšit výkon na kruhové dotazování tabulky.  Přesun dat kruhové dotazování tabulky vždy vyžaduje pro spojení. 

Tento příklad používá [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) změna tabulky DimSalesTerritory do replikované tabulky. Tento příklad funguje bez ohledu na to, zda DimSalesTerritory distribuovat hash nebo kruhové dotazování.

```sql
CREATE TABLE [dbo].[DimSalesTerritory_REPLICATE]   
WITH   
  (   
    CLUSTERED COLUMNSTORE INDEX,  
    DISTRIBUTION = REPLICATE  
  )  
AS SELECT * FROM [dbo].[DimSalesTerritory]
OPTION  (LABEL  = 'CTAS : DimSalesTerritory_REPLICATE') 

--Create statistics on new table
CREATE STATISTICS [SalesTerritoryKey] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryKey]);
CREATE STATISTICS [SalesTerritoryAlternateKey] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryAlternateKey]);
CREATE STATISTICS [SalesTerritoryRegion] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryRegion]);
CREATE STATISTICS [SalesTerritoryCountry] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryCountry]);
CREATE STATISTICS [SalesTerritoryGroup] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryGroup]);

-- Switch table names
RENAME OBJECT [dbo].[DimSalesTerritory] to [DimSalesTerritory_old];
RENAME OBJECT [dbo].[DimSalesTerritory_REPLICATE] TO [DimSalesTerritory];

DROP TABLE [dbo].[DimSalesTerritory_old];
```  

### <a name="query-performance-example-for-round-robin-versus-replicated"></a>Příklad dotazu výkonu pro kruhové dotazování a replikují 

Replikované tabulky. nevyžaduje žádné přesunu dat pro spojení, protože celá tabulka je již k dispozici na jednotlivých výpočetních uzlech. Pokud tabulky dimenzí distribuované kruhové dotazování, spojení zkopíruje tabulky dimenzí v plné výši na každém výpočetním uzlu. Plán dotazu pro přesun dat, obsahuje operaci volat BroadcastMoveOperation. Tento typ operace přesunu dat zpomalí výkon dotazů a vyloučit pomocí replikované tabulky. Chcete-li zobrazit kroky plán dotazu, použijte [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql) zobrazení katalogu systému. 

Například v následující dotaz proti schématu AdventureWorks ` FactInternetSales` je tabulka hash distribuován. `DimDate` a `DimSalesTerritory` menší tabulky dimenze. Tento dotaz vrátí celkový prodej v Severní Americe fiskálního roku 2004:
 
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
Znovu vytvořit `DimDate` a `DimSalesTerritory` jako tabulky kruhové dotazování. V důsledku toho dotazu jsme si ukázali, následující plán dotazu, který má více vysílání přesunout operace: 
 
![Plán dotazu kruhové dotazování](media/design-guidance-for-replicated-tables/round-robin-tables-query-plan.jpg) 

Znovu vytvořit `DimDate` a `DimSalesTerritory` jako replikovaných tabulkách a znovu se spustil dotaz. Výsledný plán dotazu je mnohem kratší a mají některé nevysílá přesune.

![Replikované plán dotazu](media/design-guidance-for-replicated-tables/replicated-tables-query-plan.jpg) 


## <a name="performance-considerations-for-modifying-replicated-tables"></a>Důležité informace o výkonu pro úpravu replikované tabulky
SQL Data Warehouse implementuje replikované tabulky. pomocí hlavní verze tabulky. Hlavní verze zkopíruje do jednoho distribuční databázi na jednotlivých výpočetních uzlech. Když dojde ke změně, SQL Data Warehouse nejprve aktualizuje hlavní tabulku. Potom ho znovu sestaví tabulky na jednotlivých výpočetních uzlech. Opětovné sestavení replikované tabulky. zahrnuje kopírování tabulky na každém výpočetním uzlu a následným sestavením indexy.  Replikované tabulky na DW400 má například 5 zkopíruje data.  Hlavní kopie a úplná kopie na jednotlivých výpočetních uzlech.  Všechna data jsou uložená v distribuční databázi. SQL Data Warehouse používá tento model pro podporu rychlejší kontrolní změny dat a flexibilní škálování operace. 

Znovu sestaví se vyžadují po:
- Načtení nebo upravit data
- Datový sklad je škálovaný na jinou úroveň
- Aktualizovat definici tabulky

Znovu sestaví se nevyžadují po:
- Operaci pozastavení
- Pokračovat v operaci

Sestavení se neodehrává ihned poté, co byla změněna data. Místo toho sestavení se aktivuje při prvním dotaz vybere z tabulky.  Dotaz, který aktivuje sestavení čte okamžitě z hlavní verze tabulky, zatímco jsou data zkopírována asynchronně na každém výpočetním uzlu. Dokud se kopírování dat dokončí, následující dotazy budou nadále používat hlavní verze tabulky.  Pokud žádnou aktivitu zranitelnosti replikované tabulky, který vynutí opětovné jiné sestavení, zneplatněna kopírování dat a další příkaz select bude aktivovat dat. znovu. 

### <a name="use-indexes-conservatively"></a>Konzervativní pomocí indexů
Standardní indexování postupy platí pro replikované tabulky. SQL Data Warehouse znovu sestaví každou replikovanou tabulku indexu jako součást sestavení. Indexy používejte, pouze pokud výkonový zisk plynoucí větší váhu než náklady na opětovné sestavení indexů.  
 
### <a name="batch-data-loads"></a>Načtení dat služby batch
Při načítání dat do replikované tabulky, zkuste minimalizování znovu sestaví dávkování zatížení společně. Provádění dávkových zatížení před spuštěním příkazů select.

Například tento vzor zatížení načte data ze čtyř zdrojů a vyvolá čtyři znovu sestavovat. 

- Načíst ze zdroje 1.
- Příkaz SELECT triggery znovu sestavit 1.
- Načíst ze zdroje 2.
- Příkaz SELECT triggery znovu sestavit 2.
- Načíst ze zdroje 3.
- Příkaz SELECT triggery znovu sestavit 3.
- Načíst ze zdroje 4.
- Příkaz SELECT triggery znovu sestavit 4.

Například tento vzor zatížení načte data ze čtyř zdrojů, ale jen volá jeden opětovné sestavení.

- Načíst ze zdroje 1.
- Načíst ze zdroje 2.
- Načíst ze zdroje 3.
- Načíst ze zdroje 4.
- Znovu sestavte aktivační události příkazu SELECT.


### <a name="rebuild-a-replicated-table-after-a-batch-load"></a>Sestavte znovu replikované tabulky. po načtení služby batch
K zajištění konzistentní dotazu spuštění s úspěšností, vezměte v úvahu vynucení sestavení replikované tabulky po načtení služby batch. V opačném případě prvního dotazu budou stále používat přesunu dat pro dokončení dotazu. 

Tento dotaz používá [sys.pdw_replicated_table_cache_state](/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql) a seznamu replikované tabulky, které byla upravena, ale není znovu sestavit.

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
 
Spustit opětovné sestavení, že spustíte následující příkaz pro každou tabulku v předchozím výstupu. 

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
``` 
 
## <a name="next-steps"></a>Další postup 
K vytvoření replikované tabulky, použijte jednu z těchto příkazů:

- [Vytvoření tabulky (Azure SQL Data Warehouse)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [Vytvoření TABLE AS SELECT (Azure SQL Data Warehouse)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)

Přehled distribuované tabulky, naleznete v tématu [distribuovaných tabulkách](sql-data-warehouse-tables-distribute.md).



