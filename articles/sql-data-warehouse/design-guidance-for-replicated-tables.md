---
title: Návrh pokyny pro replikované tabulky – Azure SQL Data Warehouse | Microsoft Docs
description: Doporučení k návrhu replikovaných tabulek v Azure SQL Data Warehouse schéma.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/23/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 1cc796061056ff017e3d778ebb2e50e13d55a4c1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32189560"
---
# <a name="design-guidance-for-using-replicated-tables-in-azure-sql-data-warehouse"></a>Pokyny k návrhu pro používání replikovaných tabulek v Azure SQL Data Warehouse
Tento článek obsahuje doporučení pro návrh replikovaných tabulek v SQL Data Warehouse schéma. Použijte tato doporučení pro zlepšení výkonu dotazů, protože se sníží složitost dat přesouvání a dotazu.

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že jste obeznámeni s distribuci dat a koncepty přesun dat v SQL Data Warehouse.  Další informace najdete v tématu [architektura](massively-parallel-processing-mpp-architecture.md) článku. 

Jako součást návrh tabulky Pochopte, co nejvíce o vašich dat a jak je dotazován data.  Například zvažte tyto otázky:

- Jak velká je tabulka?   
- Jak často se aktualizují v tabulce?   
- Je nutné provést tabulkami faktů a dimenzí v datovém skladu?   

## <a name="what-is-a-replicated-table"></a>Co je replikované tabulky?
Replikované tabulky obsahuje úplnou kopii v tabulce, která je přístupná na každém výpočetním uzlu. Replikace tabulku eliminuje nutnost k přenosu dat mezi výpočetní uzly před spojení nebo agregace. Vzhledem k tomu, že tabulka obsahuje více kopií, replikované tabulky fungují lépe, když velikost tabulky je menší než 2 GB komprimované.

Následující diagram znázorňuje replikované tabulky, která je přístupná na každém výpočetním uzlu. V SQL Data Warehouse replikované tabulce zkopírován plně distribuční databázi na každém výpočetním uzlu. 

![Replikované tabulky](media/guidance-for-using-replicated-tables/replicated-table.png "replikované tabulky")  

Replikované tabulky pracovní i pro malý dimenze tabulky v hvězdicové schéma. Tabulky dimenzí jsou obvykle velikosti, která je vhodná k uložení a správě více kopií. Dimenze ukládat popisný data, která změní pomalu, jako je například jméno zákazníka a adresu a podrobnosti o produktu. Pomalu se měnící se povaze dat vede k méně znovu sestaví replikované tabulky. 

Zvažte použití replikované tabulky, když:

- Velikost tabulky na disku je menší než 2 GB, bez ohledu na počet řádků. Chcete-li zjistit velikost tabulky, můžete použít [DBCC PDW_SHOWSPACEUSED](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql) příkaz: `DBCC PDW_SHOWSPACEUSED('ReplTableCandidate')`. 
- Tabulka se používá ve spojení, které by jinak vyžadovaly přesun dat. Při spojování tabulek, které nejsou distribuovány ve stejném sloupci, jako je například distribuovat algoritmu hash tabulku do tabulky pomocí kruhového dotazování přesun dat je potřeba provést dotaz.  Pokud jeden z tabulky je malý, vezměte v úvahu replikované tabulky. Doporučujeme používat replikované tabulky místo kruhového dotazování tabulky ve většině případů. Chcete-li zobrazit operace přesunu dat v plány dotazů, použijte [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql).  BroadcastMoveOperation je typické operaci přesunutí, můžete eliminovat pomocí replikované tabulky.  
 
Replikované tabulky nemusí poskytne nejlepší výkon dotazů při:

- Tabulka obsahuje časté vložit, aktualizovat a odstraňovat operace. Tyto operace jazyk (DML) manipulaci dat vyžadují opětovné sestavení replikované tabulky. Opětovné sestavení často může způsobit snížení výkonu.
- Datový sklad je často škálovat. Změna měřítka datového skladu změní počet výpočetních uzlů, který způsobuje opětovném sestavení.
- Tabulka obsahuje velký počet sloupců, ale operace dat obvykle přístup pouze malý počet sloupců. V tomto scénáři, namísto replikace celou tabulku může to být efektivnější distribuovat tabulky a pak vytvořit index pro často používaná sloupce. Pokud dotaz vyžaduje přesun dat, SQL Data Warehouse přesouvá pouze data pro požadované sloupce. 

## <a name="use-replicated-tables-with-simple-query-predicates"></a>Použití replikované tabulky s predikáty jednoduchý dotaz
Než rozhodnete distribuovat nebo replikovat tabulku, vezměte v úvahu typy dotazů, které máte v úmyslu provést na tabulce. Pokud je to možné,

- Pro dotazy s predikáty jednoduchý dotaz, jako je například rovnosti nebo nerovnosti použijte replikované tabulky.
- Pro dotazy s predikáty složitý dotaz, jako je například jako použijte distribuované tabulky nebo není jako.

Náročná na prostředky procesoru dotazů provést nejlépe při práci se distribuuje do všech výpočetních uzlů. Například dotazy, které běží výpočtů na každý řádek tabulky poskytují lepší výkon v distribuované tabulek než replikované tabulky. Vzhledem k tomu, že replikované tabulky je uložený ve plně na každém výpočetním uzlu, náročná na prostředky procesoru dotazy na replikované tabulky spouští celou tabulku na každý uzel výpočty. Navíc výpočet můžou způsobit snížení výkonnosti dotazu.

Tento dotaz má například komplexní predikátu.  Rychleji spustí po dodavatele distribuované tabulku místo replikované tabulky. V tomto příkladu může být dodavatele distribuované kruhového dotazování.

```sql

SELECT EnglishProductName 
FROM DimProduct 
WHERE EnglishDescription LIKE '%frame%comfortable%'

```

## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>Převést stávající tabulky pomocí kruhového dotazování na replikované tabulky
Pokud již máte kruhového dotazování tabulky, doporučujeme převodu na replikované tabulky, pokud splňují s kritérii uvedených v tomto článku. Replikované tabulky zlepšit výkon na kruhového dotazování tabulky, protože se vyhnete nutnosti pro přesun dat.  Přesun dat pomocí kruhového dotazování tabulky vždy vyžaduje pro spojení. 

Tento příklad používá [funkce CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) ke změně v tabulce DimSalesTerritory replikované tabulky. Tento příklad funguje bez ohledu na to, jestli je DimSalesTerritory distribuovat algoritmu hash nebo kruhového dotazování.

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

### <a name="query-performance-example-for-round-robin-versus-replicated"></a>Příklad výkonu dotazu, kruhové dotazování versus replikovat 

Replikované tabulky nevyžaduje žádné přesun dat pro spojení, protože celá tabulka je již na každém výpočetním uzlu. Pokud tabulky dimenzí distribuované kruhového dotazování, a připojte zkopíruje tabulce dimenze v plném rozsahu na každém výpočetním uzlu. Plán dotazu pro přesun dat, obsahuje operace názvem BroadcastMoveOperation. Tento typ operace přesunu dat zpomalí výkon dotazů a je eliminována použití replikované tabulky. Chcete-li zobrazit kroky plán dotazu, použijte [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql) zobrazení katalogu systému. 

Například v následujícím dotazu pro schéma AdventureWorks ` FactInternetSales` tabulka je distribuovat algoritmu hash. `DimDate` a `DimSalesTerritory` tabulky jsou menší tabulky dimenzí. Tento dotaz vrátí celkový prodej v Severní Americe pro fiskálního roku 2004:
 
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
Znovu vytvořit `DimDate` a `DimSalesTerritory` jako kruhového dotazování tabulky. V důsledku toho dotaz vám ukázal následující plán dotazu, který má více vysílání přesunout operace: 
 
![Plán dotazu pomocí kruhového dotazování](media/design-guidance-for-replicated-tables/round-robin-tables-query-plan.jpg) 

Znovu vytvořit `DimDate` a `DimSalesTerritory` jako replikovaných tabulek a znovu se spustil dotaz. Výsledný plán dotazu je mnohem kratší a mají některé nevysílá přesune.

![Replikovat plán dotazu](media/design-guidance-for-replicated-tables/replicated-tables-query-plan.jpg) 


## <a name="performance-considerations-for-modifying-replicated-tables"></a>Důležité informace o výkonu pro úpravy replikované tabulky
SQL Data Warehouse implementuje replikované tabulky udržováním hlavní verze tabulky. Hlavní verze se zkopíruje na jeden distribuční databázi na každém výpočetním uzlu. Když dojde ke změně, aktualizuje SQL Data Warehouse nejprve hlavní tabulka. Pak znovu sestaví tabulky na každém výpočetním uzlu. Opětovné sestavení replikované tabulky zahrnuje kopírování v tabulce na každém výpočetním uzlu a poté vytváření indexů.  Replikované tabulky na DW400 má například 5 kopie dat.  Hlavní kopii a úplné kopie na každém výpočetním uzlu.  Veškerá data budou uložena v distribuční databázi. SQL Data Warehouse používá tento model pro podporu rychlejší příkazy úpravu dat a flexibilní škálování operace. 

Znovu sestaví je potřeba po:
- Data jsou načíst nebo upravit
- Datový sklad je škálovat na jinou úroveň
- Aktualizace definice tabulky

Znovu sestaví nejsou nutné po:
- Operace pozastavení
- Operace obnovení

Sestavení se neodehrává ihned po data je upravit. Místo toho sestavení se aktivuje při prvním dotazu vybere z tabulky.  Dotaz, který aktivoval sestavení přečte okamžitě z hlavní verze tabulky, když data budou zkopírována asynchronně na každém výpočetním uzlu. Až do dokončení kopírování dat, následných dotazy bude nadále používat hlavní verze tabulky.  V případě jakékoli aktivity na replikované tabulky, který vynutí opětovné sestavení jiné zrušeno kopírování dat a další příkaz select se aktivuje data ke zkopírování znovu. 

### <a name="use-indexes-conservatively"></a>Můžete použít indexy
Standardní indexování postupy platí pro replikované tabulky. SQL Data Warehouse znovu sestaví každý index replikované tabulky v rámci sestavení. Indexy používejte pouze výkonnější převáží náklady znovu sestavit indexy.  
 
### <a name="batch-data-loads"></a>Načítání dat dávky
Při načítání dat do replikované tabulky, měli snažte minimalizovat znovu sestaví podle dávkování zatížení dohromady. Proveďte všechny dávkové zatížení před spuštěním příkazů select.

Tento vzor zatížení například načte data ze čtyř zdrojů a vyvolá čtyři znovu sestaví. 

- Načíst ze zdroje 1.
- Příkaz SELECT aktivační události znovu sestavit 1.
- Načíst ze zdroje. 2.
- Příkaz SELECT aktivační události znovu sestavit 2.
- Načíst ze zdroje 3.
- Příkaz SELECT aktivační události znovu sestavit 3.
- Načíst ze zdroje 4.
- Příkaz SELECT aktivační události znovu sestavit 4.

Tento vzor zatížení například načte data ze čtyř zdrojů, ale pouze vyvolá jeden opětovné sestavení.

- Načíst ze zdroje 1.
- Načíst ze zdroje. 2.
- Načíst ze zdroje 3.
- Načíst ze zdroje 4.
- Příkaz SELECT aktivační události znovu sestavit.


### <a name="rebuild-a-replicated-table-after-a-batch-load"></a>Znovu sestavte replikované tabulky po zatížení batch
Aby dobu provádění konzistentní dotazy, zvažte vynucení sestavení replikované tabulky po batch zatížení. Jinak první dotaz stále použije přesun dat pro dokončení dotazu. 

Tento dotaz používá [sys.pdw_replicated_table_cache_state](/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql) DMV seznam replikované tabulky, která byla upravena, ale není znovu sestavit.

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
 
K aktivaci nové vytvoření, že spustíte následující příkaz jednotlivé tabulky v předchozím výstup. 

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
``` 
 
## <a name="next-steps"></a>Další postup 
Pokud chcete vytvořit replikované tabulky, použijte jednu z těchto příkazů:

- [Vytvoření tabulky (Azure SQL Data Warehouse)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [Vytvoření TABLE AS SELECT (Azure SQL Data Warehouse)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)

Přehled distribuované tabulek, najdete v tématu [distribuované tabulky](sql-data-warehouse-tables-distribute.md).



