---
title: Návrh distribuovaných tabulky pokyny - Azure SQL Data Warehouse | Microsoft Docs
description: Doporučení pro navrhování distribuovat algoritmu hash a kruhového dotazování distribuované tabulky v Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: d65ca91fc4cffa53adf3a7c56c7919e46c5037d9
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31526249"
---
# <a name="guidance-for-designing-distributed-tables-in-azure-sql-data-warehouse"></a>Pokyny pro návrh distribuovaných tabulek v Azure SQL Data Warehouse
Doporučení pro navrhování distribuovat algoritmu hash a kruhového dotazování distribuované tabulky v Azure SQL Data Warehouse.

Tento článek předpokládá, že jste obeznámeni s distribuci dat a koncepty přesun dat v SQL Data Warehouse.  Další informace najdete v tématu [Azure SQL Data Warehouse - masivně paralelní zpracování () architektura MPP](massively-parallel-processing-mpp-architecture.md). 

## <a name="what-is-a-distributed-table"></a>Co je distribuované tabulky?
Distribuované tabulka se zobrazí jako jednu tabulku, ale řádky jsou ve skutečnosti ukládat v rámci 60 distribuce. Řádky jsou distribuované s hash nebo algoritmus kruhového dotazování.  

**Distribuovat algoritmu hash tabulky** zlepšit výkon dotazu na tabulky faktů velké a jsou zaměřeny tohoto článku. **Kruhové dotazování tabulky** jsou užitečné pro zlepšení rychlost načítání. Tyto možnosti návrhu, které mají významný dopad na vylepšení dotazů a načítání výkonu.

Další možností úložiště table je replikaci malé tabulky pro všechny výpočetní uzly. Další informace najdete v tématu [návrh pokyny pro replikované tabulky](design-guidance-for-replicated-tables.md). Rychle vybrat mezi tři možnosti, najdete v části tabulky distribuované [tabulky přehled](sql-data-warehouse-tables-overview.md). 

Jako součást návrh tabulky Pochopte, co nejvíce o vašich dat a jak je dotazován data.  Například zvažte tyto otázky:

- Jak velká je tabulka?   
- Jak často se aktualizují v tabulce?   
- Je nutné provést tabulkami faktů a dimenzí v datovém skladu?   


### <a name="hash-distributed"></a>Hodnota hash distribuované
Distribuovat algoritmu hash tabulku rozděluje řádky tabulky na výpočetní uzly pomocí funkce deterministickou hash přiřadit každý řádek jednu [distribuční](massively-parallel-processing-mpp-architecture.md#distributions). 

![Tabulka distribuované](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "distribuované tabulky")  

Vzhledem k tomu, že identické hodnoty hash vždycky na stejný distribuční, datový sklad má integrovanou znalosti o umístění řádek. SQL Data Warehouse používá tyto znalosti minimalizovat přesun dat během dotazů, což zvyšuje výkon dotazů. 

Distribuovat algoritmu hash tabulky fungují dobře u velkých fakt tabulek v hvězdicové schéma. Můžou mít velmi velké počty řádků a stále dosáhnout vysoký výkon. Existují samozřejmě některé aspekty návrhu, které pomáhají při dosažení výkonu, které je distribuovaný systém určená k poskytnutí. Výběr správné distribuční sloupce je jeden tyto vklady, který je popsaný v tomto článku. 

Zvažte použití distribuovat algoritmu hash tabulky, když:

- Velikost tabulky na disku je větší než 2 GB.
- Tabulka obsahuje časté vložit, aktualizovat a odstraňovat operace. 

### <a name="round-robin-distributed"></a>Distribuované kruhového dotazování
Řádky tabulky distribuované tabulku kruhového dotazování rovnoměrně distribuuje mezi všechny distribuce. Přiřazení řádků, které mají distribuce je náhodný. Na rozdíl od distribuovat algoritmu hash tabulky řádky s stejné hodnoty není zaručena bezpečnost pro přiřazení ke stejné rozdělení. 

V důsledku toho systém někdy potřebuje k vyvolání operace přesunu dat lepší uspořádání dat před překlad dotazu.  Tento krok navíc může zpomalit své dotazy. Například obvykle připojení pomocí kruhového dotazování tabulky vyžaduje promísení řádky, což je podle výkonu.

Zvažte použití rozdělení kruhového dotazování pro tabulku v následujících scénářích:

- Když Začínáme jako jednoduchý počáteční bod, protože je to výchozí hodnota
- Pokud neexistuje zřejmé spojující klíč
- Pokud není k dispozici vhodným kandidátem sloupce pro hodnoty hash distribuce tabulky
- Pokud v tabulce nesdílí společný klíč spojení s jinými tabulkami
- Pokud je méně důležité než jiné spojení v dotazu spojení
- Pokud je tabulka dočasné pracovní tabulky

Tento kurz [data taxicab zatížení New Yorku k Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md#load-the-data-into-your-data-warehouse) poskytuje příklad načítání dat do pracovní tabulky pomocí kruhového dotazování.


## <a name="choosing-a-distribution-column"></a>Výběr distribuční sloupce
Tabulku distribuovat algoritmu hash má distribuční sloupec, který je klíč algoritmu hash. Například následující kód vytvoří tabulku distribuovat algoritmu hash se ProductKey jako distribuční sloupce.

```SQL
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,  DISTRIBUTION = HASH([ProductKey])
)
;
``` 

Výběr sloupce distribuce je rozhodnutí o návrhu důležité, protože hodnoty v tomto sloupci určují, jak jsou distribuovány řádky. Nejlepší volbou závisí na několika faktorech a obvykle zahrnuje kompromisy. Ale když nebude sloupci nejlepší poprvé, můžete použít [vytvořit tabulku AS vyberte funkce CTAS ()](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) opětovné vytvoření tabulky se sloupcem jiný distribuční. 

### <a name="choose-a-distribution-column-that-does-not-require-updates"></a>Zvolte distribuční sloupec, který nevyžaduje aktualizace
Distribuční sloupce nelze aktualizovat, není-li odstranit řádek a vložit nový řádek aktualizovanými hodnotami. Proto vyberte sloupec s statické hodnoty. 

### <a name="choose-a-distribution-column-with-data-that-distributes-evenly"></a>Zvolte distribuční sloupce s daty, která distribuuje rovnoměrně

Pro nejlepší výkon všechny z rozdělení musí mít přibližně stejný počet řádků. Pokud jeden nebo více distribuce nesoustředil příliš velký počet řádků, některých distribucích dokončit jejich část paralelního dotazu než jiné. Vzhledem k tomu, že dotaz nelze dokončit, dokud všechny distribuce dokončení zpracování, je pouze tak rychlý jako nejpomalejší distribuce každý dotaz.

- Zkosení dat znamená, že data není rovnoměrně mezi distribucí
- Zpracování zkosení znamená, že některé distribuce trvat déle než jiné při spouštění paralelní dotazy. To může dojít, když se nesouměrně rozdělí data.
  
Vyvážit paralelní zpracování, vyberte distribuční sloupec, který:

- **Má mnoho jedinečné hodnoty.** Sloupec může mít některé duplicitní hodnoty. Všechny řádky se stejnou hodnotou, ale jsou přiřazeny k stejné rozdělení. Vzhledem k tomu, že existují 60 distribuce, měli sloupec minimálně 60 jedinečné hodnoty.  Obvykle je mnohem větší počet jedinečných hodnot.
- **Nemá žádné hodnoty Null, nebo má jenom pár hodnoty Null.** Příklad extreme Pokud jsou všechny hodnoty ve sloupci NULL, všechny řádky přiřazené k stejné rozdělení. V důsledku toho zpracování dotazu se nesouměrně rozdělí na jeden distribuční a nevyužívají paralelní zpracování. 
- **Není sloupec kalendářních**. Všechna data pro stejné datum pojmenováváme stejné rozdělení. Pokud několik uživatelů se filtrování ve stejný den, udělejte jenom 1 60 distribuce všechnu práci zpracování. 

### <a name="choose-a-distribution-column-that-minimizes-data-movement"></a>Zvolte distribuční sloupec, který minimalizuje přesun dat

Získat výsledek dotazů se správným dotazem může přesun dat z jednom výpočetním uzlu do jiného. Přesun dat obvykle dojde, když dotazy mít spojování a agregaci pro distribuované tabulky. Výběr distribuční sloupec, který se může minimalizovat přesun dat je jedním z nejdůležitějších strategie pro optimalizaci výkonu SQL Data Warehouse.

Minimalizovat přesun dat, vyberte distribuční sloupec, který:

- Se používá v `JOIN`, `GROUP BY`, `DISTINCT`, `OVER`, a `HAVING` klauzule. Pokud dvě tabulky faktů velké časté spojení, zlepšuje výkon dotazů při distribuci obě tabulky v jednom ze sloupce spojení.  Při tabulku se nepoužívá v spojení, vezměte v úvahu tabulky na sloupci, který je často v distribuci `GROUP BY` klauzule.
- Je *není* použít v `WHERE` klauzule. To může upřesněte dotaz pro nejde spustit na všechny distribuce. 
- Je *není* sloupec data. Klauzule WHERE často filtrovat podle data.  V takovém případě by mohl spustit veškeré zpracování na pouze několik distribuce.

### <a name="what-to-do-when-none-of-the-columns-are-a-good-distribution-column"></a>Co dělat, pokud žádná ze sloupce jsou správné distribuční sloupce

Pokud žádná z vaší sloupce mají dostatek jedinečných hodnot ve sloupci distribuční, můžete vytvořit nový sloupec jako složené z jedné nebo více hodnot. Abyste se vyhnuli přesun dat během provádění dotazu, používejte složené distribuční sloupec jako sloupec spojení v dotazech.

Když navrhujete tabulku distribuovat algoritmu hash, dalším krokem je načíst data do tabulky.  Načítání pokyny, najdete v části [přehledem načítání](sql-data-warehouse-overview-load.md). 

## <a name="how-to-tell-if-your-distribution-column-is-a-good-choice"></a>Jak zjistit, pokud vaše distribuční sloupce je dobrá volba
Po načtení dat do tabulky distribuovat algoritmu hash, zkontrolujte, jak se řádky rovnoměrně mezi 60 distribuce. Řádky za distribuční se může lišit až 10 % bez znatelný dopad na výkon. 

### <a name="determine-if-the-table-has-data-skew"></a>Určete, jestli tabulka obsahuje data zkreslit
Rychlý způsob, jak zkontrolovat pro zkosení dat je použití [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql). Následující kód SQL vrátí počet řádků tabulky, které jsou uložené v každé z 60 distribuce. Vyrovnáváním výkonu by měl být řádky v tabulce distribuované, rovnoměrně rozloženy všechny distribuce.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Určení, které tabulky mít víc než 10 % data zkosení:

1. Vytvoření dbo.vTableSizes zobrazení, které se zobrazí v [Přehled tabulek](sql-data-warehouse-tables-overview.md#table-size-queries) článku.  
2. Spusťte následující dotaz:

```sql
select *
from dbo.vTableSizes
where two_part_name in
    (
    select two_part_name
    from dbo.vTableSizes
    where row_count > 0
    group by two_part_name
    having min(row_count * 1.000)/max(row_count * 1.000) > .10
    )
order by two_part_name, row_count
;
```

### <a name="check-query-plans-for-data-movement"></a>Zkontrolujte plány dotazů pro přesun dat
Sloupec správné distribuční umožňuje spojování a agregaci tak, aby měl přesun dat minimální. Tato akce ovlivní způsob, jakým budou zasílány spojení. Získat přesun minimální dat pro připojení k na dvě tabulky distribuovat algoritmu hash, některý ze sloupců připojení musí být distribuční sloupce.  Pokud na stejný datový typ. sloupec distribuční připojení dvě tabulky distribuovat algoritmu hash, spojení přesun dat nevyžaduje. Spojení můžete použít další sloupce, aniž by docházelo k přesunu dat.

Aby se zabránilo přesun dat během spojení:

- Příslušné tabulky ve spojení musí být hodnota hash rozdělit na **jeden** sloupců podílejících se připojení.
- Datové typy sloupce spojení se musí shodovat mezi obě tabulky.
- Sloupce musí být připojené pomocí operátoru rovná se.
- Typ spojení nesmí být `CROSS JOIN`.

Pokud chcete zobrazit, pokud dotazy dochází k přesunu dat, můžete si prohlédnout plán dotazu.  


## <a name="resolve-a-distribution-column-problem"></a>Vyřešení problému distribuční sloupce
Není nutné vyřešit že všechny případy dat zkreslit. Distribuci dat je řádu najít rovnováhu mezi minimalizovat přesun dat a dat zkosení. Vždy není možné minimalizovat zkosení dat a přesun dat. Výhodou vytvoření přesun dat minimální někdy může převažují nad důsledky toho, že data zkreslit.

Rozhodnout, pokud byste měli vyřešit dat zkosení v tabulce, měli byste se seznámit co nejvíce o datové svazky a dotazy v vaše úlohy. Můžete použít kroky v [dotazu monitorování](sql-data-warehouse-manage-monitor.md) článek k monitorování dopad na výkon dotazů zkosení. Konkrétně podívejte se na tom, jak dlouho trvalo rozsáhlé dotazy dokončení pro jednotlivé distribuce.

Vzhledem k tomu, že sloupec distribuční na existující tabulky nelze změnit, typické způsob řešení zkosení dat je znovu vytvořit tabulku se sloupcem jiný distribuční.  

### <a name="re-create-the-table-with-a-new-distribution-column"></a>Opětovným vytvořením tabulky se sloupcem nové distribuce
Tento příklad používá [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse.md) a znovu vytvořte tabulku se sloupcem distribuční různé hodnoty hash.

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey]
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_CustomerKey] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_CustomerKey] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_CustomerKey] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_CustomerKey] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_CustomerKey] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_CustomerKey] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_CustomerKey] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_CustomerKey] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

## <a name="next-steps"></a>Další postup

Pokud chcete vytvořit distribuovanou tabulku, použijte jednu z těchto příkazů:

- [Vytvoření tabulky (Azure SQL Data Warehouse)](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [Vytvoření TABLE AS SELECT (Azure SQL Data Warehouse](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)


