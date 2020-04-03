---
title: Pokyny pro návrh distribuovaných tabulek
description: Doporučení pro navrhování hash distribuovaných a round-robin distribuovaných tabulek ve fondu SYNApse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 8a93f3ada8e56853b78321bdc7d99a667cee6158
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583508"
---
# <a name="guidance-for-designing-distributed-tables-in-synapse-sql-pool"></a>Pokyny pro navrhování distribuovaných tabulek ve fondu Synapse SQL

Doporučení pro navrhování hash distribuovaných a round-robin distribuovaných tabulek v fondech Synapse SQL.

Tento článek předpokládá, že jste obeznámeni s rozdělení dat a přesun dat koncepty v synapse fondu SQL.Další informace naleznete v [tématu Azure Synapse Analytics masivně paralelní zpracování (MPP) architektura](massively-parallel-processing-mpp-architecture.md). 

## <a name="what-is-a-distributed-table"></a>Co je distribuovaná tabulka?

Distribuovaná tabulka se zobrazí jako jedna tabulka, ale řádky jsou ve skutečnosti uloženy v 60 distribucích. Řádky jsou distribuovány pomocí algoritmu hash nebo kruhového dotazování.  

**Tabulky distribuované hash** zlepšit výkon dotazu na velké tabulky faktů a jsou fokus tohoto článku. **Kulaté robin tabulky** jsou užitečné pro zlepšení rychlosti načítání. Tyto možnosti návrhu mají významný vliv na zlepšení výkonu dotazů a načítání.

Další možností úložiště tabulky je replikovat malou tabulku ve všech výpočetních uzlech. Další informace naleznete v [pokynech k návrhu pro replikované tabulky](design-guidance-for-replicated-tables.md). Chcete-li rychle vybrat ze tří možností, přečtěte si část Distribuované tabulky v [přehledu tabulek](sql-data-warehouse-tables-overview.md). 

V rámci návrhu tabulky pochopte co nejvíce informací o datech a způsobu dotazování na data.Zvažte například tyto otázky:

- Jak velký je stůl?   
- Jak často se tabulka aktualizuje?   
- Mám tabulky faktů a dimenzí ve fondu Synapse SQL?   


### <a name="hash-distributed"></a>Hash distribuovány

Tabulka distribuovaná hash distribuuje řádky tabulky mezi výpočetními uzly pomocí deterministické funkce hash pro přiřazení každého řádku k jednomu [rozdělení](massively-parallel-processing-mpp-architecture.md#distributions). 

![Distribuovaná tabulka](./media/sql-data-warehouse-tables-distribute/hash-distributed-table.png "Distribuovaná tabulka")  

Vzhledem k tomu, že identické hodnoty vždy hash na stejné rozdělení, datový sklad má vestavěné znalosti umístění řádků. Ve fondu Synapse SQL se tyto znalosti používají k minimalizaci pohybu dat během dotazů, což zlepšuje výkon dotazů. 

Tabulky distribuované hash fungují dobře pro tabulky velkých faktů ve schématu hvězd. Mohou mít velmi velký počet řádků a stále dosáhnout vysokého výkonu. Existují samozřejmě některé aspekty návrhu, které vám pomohou získat výkon distribuovaný systém je navržen tak, aby poskytovat. Výběr sloupce dobré distribuce je jedním z takových úvah, které je popsáno v tomto článku. 

Použití tabulky distribuované hash zvažte v následujících případěch:

- Velikost tabulky na disku je větší než 2 GB.
- Tabulka obsahuje časté operace vkládání, aktualizace a odstraňování. 

### <a name="round-robin-distributed"></a>Kruhové dotazování distribuováno

Distribuovaná tabulka kruhového dotazování distribuuje řádky tabulky rovnoměrně ve všech distribucích. Přiřazení řádků k rozdělení je náhodné. Na rozdíl od tabulek distribuovaných hash není zaručeno, že řádky se stejnými hodnotami budou přiřazeny ke stejnému rozdělení. 

V důsledku toho systém někdy potřebuje vyvolat operaci přesunu dat, aby lépe uspořádal data, než bude moci vyřešit dotaz.  Tento další krok může zpomalit dotazy. Například připojení tabulky kruhového dotazování obvykle vyžaduje přemíchání řádků, což je přístupů k výkonu.

Zvažte použití distribuce kruhového dotazování pro tabulku v následujících scénářích:

- Když začínáme jako jednoduchý výchozí bod, protože se jedná o výchozí
- Pokud není k dispozici žádný zřejmý spojovací klíč
- Pokud neexistuje žádný dobrý kandidát sloupec pro hash distribuci tabulky
- Pokud tabulka nesdílí společný klíč spojení s jinými tabulkami
- Pokud je spojení méně významné než ostatní spojení v dotazu
- Pokud je tabulka dočasná pracovní tabulka

Kurz [Načíst data taxislužby v New Yorku](load-data-from-azure-blob-storage-using-polybase.md#load-the-data-into-your-data-warehouse) poskytuje příklad načítání dat do pracovní tabulky kruhového dotazování.


## <a name="choosing-a-distribution-column"></a>Výběr distribučního sloupce
Tabulka distribuovaná hash má distribuční sloupec, který je klíčem hash. Například následující kód vytvoří tabulku distribuovanou hash s ProductKey jako distribučním sloupcem.

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

Výběr distribučního sloupce je důležitým rozhodnutím návrhu, protože hodnoty v tomto sloupci určují způsob distribuce řádků. Nejlepší volba závisí na několika faktorech a obvykle zahrnuje kompromisy. Pokud však nevyberete nejlepší sloupec poprvé, můžete použít [vytvořit tabulku jako SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) znovu vytvořit tabulku s jiným sloupcem distribuce. 

### <a name="choose-a-distribution-column-that-does-not-require-updates"></a>Zvolte distribuční sloupec, který nevyžaduje aktualizace.
Distribuční sloupec nelze aktualizovat, pokud řádek neodstraníte a nevložíte nový řádek s aktualizovanými hodnotami. Proto vyberte sloupec se statickými hodnotami. 

### <a name="choose-a-distribution-column-with-data-that-distributes-evenly"></a>Zvolte distribuční sloupec s daty, která se distribuují rovnoměrně

Pro nejlepší výkon všechny distribuce by měl mít přibližně stejný počet řádků. Pokud má jedna nebo více distribucí neúměrný počet řádků, některé distribuce dokončí svou část paralelního dotazu před ostatními. Vzhledem k tomu, že dotaz nelze dokončit, dokud všechny distribuce dokončily zpracování, každý dotaz je pouze tak rychle jako nejpomalejší distribuce.

- Zkosení dat znamená, že data nejsou rovnoměrně rozložena mezi distribucemi.
- Zpracování zkosení znamená, že některé distribuce trvat déle než ostatní při spuštění paralelní dotazy. K tomu může dojít, když jsou data zkosená.
  
Chcete-li vyvážit paralelní zpracování, vyberte distribuční sloupec, který:

- **Má mnoho jedinečných hodnot.** Sloupec může mít některé duplicitní hodnoty. Všechny řádky se stejnou hodnotou jsou však přiřazeny ke stejnému rozdělení. Vzhledem k tomu, že existuje 60 rozdělení, sloupec by měl mít alespoň 60 jedinečné hodnoty.  Obvykle je počet jedinečných hodnot mnohem větší.
- **Nemá nulls nebo má jen několik nulls.** Pro extrémní příklad, pokud všechny hodnoty ve sloupci jsou NULL, všechny řádky jsou přiřazeny ke stejnému rozdělení. V důsledku toho je zpracování dotazů zkosené na jednu distribuci a nemá prospěch z paralelního zpracování. 
- **Není sloupec data**. Všechna data za stejné datum jsou přidělena ve stejném rozdělení. Pokud několik uživatelů filtruje ve stejný den, pak pouze 1 z 60 distribucí provést všechny zpracování práce. 

### <a name="choose-a-distribution-column-that-minimizes-data-movement"></a>Výběr distribučního sloupce, který minimalizuje pohyb dat

Chcete-li získat správný dotaz výsledek dotazy může přesunout data z jednoho výpočetního uzlu do jiného. Přesun dat se obvykle stává, když dotazy mají spojení a agregace v distribuovaných tabulkách. Výběr distribučního sloupce, který pomáhá minimalizovat pohyb dat, je jednou z nejdůležitějších strategií pro optimalizaci výkonu fondu SYNAPse SQL.

Chcete-li minimalizovat přesun dat, vyberte distribuční sloupec, který:

- Používá se `JOIN` `GROUP BY`v `DISTINCT` `OVER`klauzulích `HAVING` , , , a klauzulích. Pokud dvě velké tabulky faktů mají časté spojení, výkon dotazu se zlepší při distribuci obou tabulek na jednom ze sloupců spojení.  Pokud tabulka není použita ve spojeních, zvažte distribuci tabulky na `GROUP BY` sloupec, který je často v klauzuli.
- *Nepoužívá* se `WHERE` v klauzulích. To by mohlo zúžit dotaz není spuštěn na všechny distribuce. 
- Není *not* sloupec data. Kde klauzule často filtrují podle data.  V takovém případě může být veškeré zpracování spuštěno pouze na několika distribucích.

### <a name="what-to-do-when-none-of-the-columns-are-a-good-distribution-column"></a>Co dělat, když žádný ze sloupců není dobrý distribuční sloupec

Pokud žádný ze sloupců nemá dostatek odlišných hodnot pro distribuční sloupec, můžete vytvořit nový sloupec jako složený z jedné nebo více hodnot. Chcete-li se vyhnout přesunu dat během provádění dotazu, použijte sloupec složené distribuce jako sloupec spojení v dotazech.

Po návrhu tabulky distribuované hash je dalším krokem načtení dat do tabulky.  Pokyny k načtení najdete v tématu [Přehled načítání](design-elt-data-loading.md). 

## <a name="how-to-tell-if-your-distribution-column-is-a-good-choice"></a>Jak zjistit, zda je váš distribuční sloupec dobrou volbou
Po načtení dat do tabulky distribuované hash zkontrolujte, jak rovnoměrně jsou řádky rozloženy v 60 distribucích. Řádky na distribuci se mohou lišit až o 10 % bez znatelného dopadu na výkon. 

### <a name="determine-if-the-table-has-data-skew"></a>Určení, zda je v tabulce zkosení dat
Rychlý způsob, jak zkontrolovat zkosení dat, je použít [dbcc PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql). Následující kód SQL vrátí počet řádků tabulky, které jsou uloženy v každé z 60 distribucí. Pro vyvážený výkon by měly být řádky v distribuované tabulce rovnoměrně rozloženy na příčky všech distribucí.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Chcete-li zjistit, které tabulky mají více než 10 % zkosení dat:

1. Vytvořte zobrazení dbo.vTableSizes, které je zobrazeno v článku [Přehled tabulek.](sql-data-warehouse-tables-overview.md#table-size-queries)  
2. Spusťte tento dotaz:

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

### <a name="check-query-plans-for-data-movement"></a>Kontrola plánů dotazů pro přesun dat
Dobrý sloupec distribuce umožňuje spojení a agregace mít minimální přesun dat. To má vliv na způsob, jakým by měla být zapsána spojení. Chcete-li získat minimální přesun dat pro spojení na dvou tabulkách distribuovaných hash, jeden ze sloupců spojení musí být distribuční sloupec.  Pokud se dvě tabulky distribuované hash spojí v distribučním sloupci stejného datového typu, spojení nevyžaduje přesun dat. Spojení můžete použít další sloupce bez přenosu dat.

Chcete-li se vyhnout pohybu dat během spojení:

- Tabulky zapojené do spojení musí být hash distribuovány na **jednom** ze sloupců účastnících se spojení.
- Datové typy sloupců spojení se musí shodovat mezi oběma tabulkami.
- Sloupce musí být spojeny operátorem rovná se.
- Typ spojení nemusí být `CROSS JOIN`.

Chcete-li zjistit, zda dotazy dochází k přesunu dat, můžete se podívat na plán dotazů.  


## <a name="resolve-a-distribution-column-problem"></a>Řešení problému s distribučním sloupcem
Není nutné řešit všechny případy zkosení dat. Distribuce dat je otázkou nalezení správné rovnováhy mezi minimalizací zkosení dat a pohybem dat. Není vždy možné minimalizovat zkosení dat i přesun dat. Někdy může výhoda minimálního pohybu dat převážit nad dopadem zkosení dat.

Chcete-li se rozhodnout, zda byste měli vyřešit zkosení dat v tabulce, měli byste co nejvíce pochopit, o objemech dat a dotazech v zatížení. Kroky v článku [monitorování dotazů](sql-data-warehouse-manage-monitor.md) můžete použít ke sledování dopadu zkosení na výkon dotazu. Konkrétně vyhledejte, jak dlouho trvá velké dotazy k dokončení na jednotlivé distribuce.

Vzhledem k tomu, že nelze změnit sloupec distribuce v existující tabulce, typickýzpůsob, jak vyřešit zkosení dat, je opětovné vytvoření tabulky s jiným sloupcem distribuce.  

### <a name="re-create-the-table-with-a-new-distribution-column"></a>Opětovné vytvoření tabulky s novým sloupcem distribuce
Tento příklad používá [create table as select](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=aps-pdw-2016-au7) k opětovnému vytvoření tabulky s jiným sloupcem distribuce hash.

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

## <a name="next-steps"></a>Další kroky

Chcete-li vytvořit distribuovanou tabulku, použijte jeden z těchto příkazů:

- [VYTVOŘIT TABULKU (Synapse SQL fond)](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [VYTVOŘIT TABULKU JAKO SELECT (Fond Synapse SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)


