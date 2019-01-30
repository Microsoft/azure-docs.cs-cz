---
title: Distribuované tabulky s pokyny k návrhu – Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Doporučení pro navrhování distribuovaných hash a kruhové dotazování distribuované tabulky ve službě Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: c3de7b46449b8075d17a19733eda88d692b1d876
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55243075"
---
# <a name="guidance-for-designing-distributed-tables-in-azure-sql-data-warehouse"></a>Pokyny k návrhu distribuované tabulky ve službě Azure SQL Data Warehouse
Doporučení pro navrhování distribuovaných hash a kruhové dotazování distribuované tabulky ve službě Azure SQL Data Warehouse.

Tento článek předpokládá, že máte zkušenosti s distribuci dat a koncepty přesouvání dat ve službě SQL Data Warehouse.  Další informace najdete v tématu [Azure SQL Data Warehouse – architektura masivně paralelní zpracování (MPP)](massively-parallel-processing-mpp-architecture.md). 

## <a name="what-is-a-distributed-table"></a>Co je distribuované tabulky?
Distribuované tabulky se zobrazí jako jedné tabulky, ale řádky jsou ve skutečnosti uložených ve 60 distribucí. Řádky se distribuují se kruhové dotazování algoritmus nebo hash.  

**Hodnoty hash distribuované tabulky** zlepšení výkonu dotazů na velkých tabulek faktů, a jsou hlavním cílem tohoto článku. **Kruhové dotazování tabulky** jsou užitečné pro zlepšení rychlosti. Tyto volby návrhu mít významný dopad na zlepšení dotazu a výkon při načítání.

Další možností úložiště table je replikovat malé tabulky do výpočetních uzlů. Další informace najdete v tématu [pro replikované tabulky s pokyny k návrhu](design-guidance-for-replicated-tables.md). Rychle vybrat mezi tři možnosti, najdete v článku distribuované tabulky v [Přehled tabulek](sql-data-warehouse-tables-overview.md). 

Jako součást návrh tabulky zjistěte, co nejvíc o vašich datech a jak dotazovat data.  Představte si třeba tyto otázky:

- Jak velké jsou tabulky?   
- Jak často se aktualizuje v tabulce?   
- Musím tabulkami faktů a dimenzí v datovém skladu?   


### <a name="hash-distributed"></a>Provádět distribuci hodnot hash
Tabulka hash distribuce distribuuje řádky tabulky na výpočetních uzlech pomocí funkce hash deterministické přiřadit každý řádek jednu [distribuce](massively-parallel-processing-mpp-architecture.md#distributions). 

![Distribuované tabulky](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "distribuované tabulky")  

Vzhledem k tomu, že stejné hodnoty hash vždycky na stejný distribuční, data warehouse má integrované znalosti o umístění řádku. SQL Data Warehouse používá tyto znalosti, chcete-li minimalizovat přesun dat v dotazech, což zvyšuje výkon dotazů. 

Hodnoty hash distribuované tabulky fungují dobře u velkých tabulek faktů v hvězdicovém schématu. Mohou být velmi velký počet řádků a zachováním vysoký výkon. Existuje samozřejmě některé aspekty návrhu, které vám umožní získat distribuovaného systému je navržené pro poskytování výkonu. Volba vhodné distribučního sloupce je jeden takový faktor, který je popsaný v tomto článku. 

Zvažte použití algoritmu hash distribuce tabulky, když:

- Velikost tabulky na disku je větší než 2 GB.
- Tabulka obsahuje často vložení, aktualizace a odstranění operace. 

### <a name="round-robin-distributed"></a>Distribuované metodou kruhového
Řádky tabulky distribuovanou tabulku kruhového rovnoměrně distribuuje mezi všechny distribuce. Přiřazení řádků, které mají distribuce je náhodné. Na rozdíl od tabulky hash distribuované není zaručena řádků pomocí stejné hodnoty k přiřazení ke stejné rozdělení. 

V důsledku toho systém někdy potřebuje k vyvolání operace přesunu dat lépe uspořádat data před překlad dotazu.  Tento krok navíc může zpomalit vaše dotazy. Například připojení kruhové dotazování tabulky obvykle vyžaduje promísení řádky, což je výkon.

Zvažte použití kruhové dotazování na distribuci pro tabulky v následujících scénářích:

- Při zahájení práce jako jednoduchý výchozí bod, protože jde o výchozí nastavení
- Pokud není žádný zřejmý spojovacího klíč
- Pokud není k dispozici sloupec vhodným kandidátem pro algoritmus hash distribuce tabulky
- Pokud v tabulce nesdílí společný klíč spojení s jinými tabulkami
- Pokud je méně důležité než jiné spojení v dotazu spojení
- Když je tabulka dočasnou fázovou tabulku

Tento kurz [data taxislužby města New York zatížení do služby Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md#load-the-data-into-your-data-warehouse) poskytuje příklad načítání dat do kruhové dotazování pracovní tabulky.


## <a name="choosing-a-distribution-column"></a>Výběr distribučního sloupce
Má tabulka hash distribuované distribučního sloupce, který je klíč algoritmu hash. Například následující kód vytvoří tabulku hash distribuované se ProductKey jako sloupec distribuce.

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

Výběr distribučního sloupce je rozhodnutí o návrhu důležité, protože hodnoty v tomto sloupci určit, jakým způsobem se distribuují řádky. Nejlepší volbou závisí na několika faktorech a obvykle zahrnuje kompromisy. Nicméně, pokud se rozhodnete není nejlepší sloupec poprvé, můžete použít [vytvořit TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) k opětovnému vytvoření tabulky se sloupcem různé distribuční. 

### <a name="choose-a-distribution-column-that-does-not-require-updates"></a>Zvolte distribuční sloupce, který nevyžaduje, aby aktualizace
Rozdělení sloupce nelze aktualizovat, není-li odstranit řádek a vložit nový řádek s aktualizovanými hodnotami. Proto zvolte sloupec s statickými hodnotami. 

### <a name="choose-a-distribution-column-with-data-that-distributes-evenly"></a>Zvolte distribuční sloupce s daty, která distribuuje rovnoměrně

Pro zajištění nejlepšího výkonu všechny distribuce by měl mít přibližně stejný počet řádků. Pokud jeden nebo více distribuce nepoměrně velký počet řádků, finish některých distribucích jejich část paralelní dotaz před ostatními. Protože dotaz nelze dokončit, dokud nedokončí zpracování všech distribucí, každý dotaz je pouze nejrychleji nejpomalejší distribuce.

- Nerovnoměrná distribuce dat znamená, že se data rovnoměrně distribuovaných prostřednictvím distribuce
- Zpracování zkosení znamená, že některých distribucích trvat déle než jiné spouštěním paralelních dotazů. To může nastat při zkosený data.
  
Pro paralelní zpracování, výběr distribučního sloupce, který:

- **Má mnoho jedinečné hodnoty.** Sloupce mají duplicitní hodnoty. Však všechny řádky se stejnou hodnotou jsou přiřazeny do stejné distribuce. Protože je 60 distribucí, sloupec by měl mít alespoň 60 jedinečné hodnoty.  Počet jedinečných hodnot, který je obvykle mnohem větší.
- **Nemá žádné hodnoty Null, nebo má jenom pár hodnoty Null.** Extrémní příklad Pokud jsou všechny hodnoty ve sloupci Hodnota NULL, všechny řádky jsou přiřazeny k stejné rozdělení. V důsledku toho zpracování dotazů zkosený k jednomu distribučnímu a nijak přínosné paralelní zpracování. 
- **Sloupec data není**. Všechna data pro stejné datum, jsou ve stejné rozdělení. Pokud se několik uživatelů jsou filtrování na stejné datum, pouze 1 60 distribucí udělal všechnu práci zpracování. 

### <a name="choose-a-distribution-column-that-minimizes-data-movement"></a>Zvolte distribuční sloupce, který minimalizuje přesun dat

Chcete-li získat správný dotaz dotazů s výsledkem může být přesunout data z jednom výpočetním uzlu do jiného. Přesun dat běžně dochází v případě dotazů mít spojování a agregaci pro distribuované tabulky. Výběr distribučního sloupce, který se může minimalizovat přesun dat je jednou z vašich nejdůležitějších strategie pro optimalizaci výkonu služby SQL Data Warehouse.

Chcete-li minimalizovat přesun dat, vyberte sloupec distribuce, které:

- Se používá v `JOIN`, `GROUP BY`, `DISTINCT`, `OVER`, a `HAVING` klauzule. Pokud často spojení dvou velkých tabulek faktů, zvyšuje výkon dotazů při distribuci obou tabulek na jednotlivé sloupce spojení.  Pokud tabulka není použit ve spojeních, vezměte v úvahu distribuce tabulku podle sloupce, který se často ve `GROUP BY` klauzuli.
- Je *není* používané `WHERE` klauzule. To může zúžení dotazu a nebudou tedy spuštěny na distribuce. 
- Je *není* sloupec data. Klauzule WHERE často filtrovat podle data.  Pokud k tomu dojde, veškeré zpracování může spustit v pouze několika distribucích.

### <a name="what-to-do-when-none-of-the-columns-are-a-good-distribution-column"></a>Co dělat, když žádný sloupec jsou dobré distribučního sloupce

Pokud žádný sloupců nemá dostatek různé hodnoty pro sloupec distribuce, můžete vytvořit nový sloupec jako složený z jednoho nebo více hodnot. Pokud chcete vyhnout přesun dat při provádění dotazu, použijte jako sloupec spojení v dotazech složené distribučního sloupce.

Když navrhujete distribuované hash tabulku, dalším krokem je načtení dat do tabulky.  Doprovodné materiály k načítání, najdete v části [přehledem načítání](sql-data-warehouse-overview-load.md). 

## <a name="how-to-tell-if-your-distribution-column-is-a-good-choice"></a>Jak zjistit, zda distribučního sloupce je dobrou volbou
Po načtení dat do tabulky hash distribuované, zkontrolujte, jak se řádky rovnoměrně mezi 60 distribucí. Až 10 % znatelný vliv na výkon se může lišit řádků na distribuci. 

### <a name="determine-if-the-table-has-data-skew"></a>Určete, jestli tabulka obsahuje nerovnoměrnou distribucí dat.
Rychlý způsob, jak zkontrolujte, zda je Nerovnoměrná distribuce dat pomocí [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql). Následující kód SQL, vrátí počet řádků tabulky, které jsou uloženy v každém 60 distribucí. Vyvážený výkon by měl být řádky v tabulce distribuované, rovnoměrně rozloženy distribuce.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Určení, které tabulky může být maximálně Nerovnoměrná distribuce dat 10 %:

1. Vytvořit zobrazení dbo.vTableSizes, zobrazené v [Přehled tabulek](sql-data-warehouse-tables-overview.md#table-size-queries) článku.  
2. Spuštěním následujícího dotazu:

```sql
select *
from dbo.vTableSizes
where two_part_name in
    (
    select two_part_name
    from dbo.vTableSizes
    where row_count > 0
    group by two_part_name
    having (max(row_count * 1.000) - min(row_count * 1.000))/max(row_count * 1.000) >= .10
    )
order by two_part_name, row_count
;
```

### <a name="check-query-plans-for-data-movement"></a>Zkontrolujte plány dotazů pro přesun dat
Dobré distribučního sloupce umožňuje spojování a agregaci mít přesun dat minimální. Tato akce ovlivní způsob, jakým by měly být napsány spojení. Přesun minimální dat pro připojení na dvě tabulky hash distribuované získáte jednu spojované sloupce musí být sloupec distribuce.  Když se připojí k dvě tabulky hash distribuován na stejný datový typ. sloupec distribuce, spojení přesun dat nevyžaduje. Spojení můžete použít další sloupce, aniž by došlo k přesunu dat.

Aby se zabránilo přesun dat během spojení:

- Příslušné tabulky ve spojení musí být distribuován na hodnotu hash **jeden** sloupců podílejících se ve spojení.
- Datové typy sloupce spojení musí odpovídat mezi oběma tabulkami.
- Sloupce musí být připojené pomocí operátoru rovná se.
- Typ spojení nemůže být `CROSS JOIN`.

Pokud chcete zobrazit, pokud dotazů dochází k přesunu dat, můžete si prohlédnout plán dotazu.  


## <a name="resolve-a-distribution-column-problem"></a>Řešení problémů sloupec distribuce
Není nutné vyřešit, že všechny případy dat zkosení. Distribuce dat je otázkou najít správnou rovnováhu mezi minimalizovat Nerovnoměrná distribuce dat a přesun dat. Není vždy možné minimalizovat Nerovnoměrná distribuce dat a přesun dat. Výhodou přesouvání dat minimální někdy může převážit nad s nerovnoměrnou distribucí dat. dopad.

Rozhodování, pokud by měla vyřešit data zkosení v tabulce, měli byste porozumět co nejvíc o objemy dat a dotazy ve vašich úloh. Můžete použít kroky v [monitorování dotazů](sql-data-warehouse-manage-monitor.md) článku sledovat dopad na výkon dotazů nerovnoměrné rozdělení. Hledejte především, jak dlouho trvá dokončení pro jednotlivé distribuce rozsáhlé dotazy.

Protože distribučního sloupce na existující tabulky nelze změnit, je typické způsob, jak vyřešit Nerovnoměrná distribuce dat k opětovnému vytvoření tabulky se sloupcem různé distribuční.  

### <a name="re-create-the-table-with-a-new-distribution-column"></a>Znovu vytvořit v tabulce nový sloupec distribuce
Tento příklad používá [CREATE TABLE AS SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=aps-pdw-2016-au7) znovu vytvořit tabulku se sloupci distribuce jiný algoritmus hash.

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


