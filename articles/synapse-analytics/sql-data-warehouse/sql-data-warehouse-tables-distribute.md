---
title: Doprovodné materiály k návrhu distribuovaných tabulek
description: Doporučení pro návrh distribuovaných tabulek distribuovaných pomocí algoritmu hash a kruhové dotazování pomocí vyhrazeného fondu SQL ve službě Azure synapse Analytics.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 5aefe869041d9fff8112b6aa380961ca6568ae0b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98673565"
---
# <a name="guidance-for-designing-distributed-tables-using-dedicated-sql-pool-in-azure-synapse-analytics"></a>Pokyny pro návrh distribuovaných tabulek pomocí vyhrazeného fondu SQL ve službě Azure synapse Analytics

Doporučení pro návrh distribuovaných tabulek distribuovaných algoritmem hash a kruhové dotazování ve vyhrazených fondech SQL

Tento článek předpokládá, že máte zkušenosti s koncepty distribuce dat a přesunu dat ve vyhrazeném fondu SQL.  Další informace najdete v tématu [Architektura Azure synapse Analytics](massively-parallel-processing-mpp-architecture.md).

## <a name="what-is-a-distributed-table"></a>Co je distribuovaná tabulka?

Distribuovaná tabulka se zobrazí jako jediná tabulka, ale tyto řádky jsou ve skutečnosti uloženy v rámci 60 distribucí. Řádky jsou distribuovány pomocí algoritmu hash nebo kruhového dotazování.  

**Hodnoty hash-distribuované tabulky** zlepšují výkon dotazů u rozsáhlých tabulek faktů a jsou zaměřeny na tento článek. **Tabulky kruhového dotazování** jsou užitečné pro zlepšení rychlosti načítání. Tyto možnosti návrhu mají výrazný dopad na zlepšení výkonu dotazů a načítání.

Další možností úložiště tabulek je replikace malé tabulky ve všech výpočetních uzlech. Další informace najdete v tématu [pokyny k návrhu replikovaných tabulek](design-guidance-for-replicated-tables.md). Pokud si chcete rychle vybrat ze tří možností, přečtěte si téma distribuované tabulky v tématu [Přehled tabulek](sql-data-warehouse-tables-overview.md).

Jako součást návrhu tabulky Pochopte co nejvíce dat a způsob dotazování na data.  Zvažte například tyto otázky:

- Jak velká je tabulka?
- Jak často je tabulka aktualizována?
- Mám tabulky faktů a dimenzí ve vyhrazeném fondu SQL?

### <a name="hash-distributed"></a>Hodnota hash distribuována

Tabulka distribuovaná algoritmem hash distribuuje řádky tabulky napříč výpočetními uzly pomocí deterministické funkce hash k přiřazení každého řádku k jedné [distribuci](massively-parallel-processing-mpp-architecture.md#distributions).

![Distribuovaná tabulka](./media/sql-data-warehouse-tables-distribute/hash-distributed-table.png "Distribuovaná tabulka")  

Vzhledem k tomu, že identické hodnoty vždycky vycházejí z hodnoty hash na stejnou distribuci, SQL Analytics má integrované znalosti o umístěních řádků. Ve vyhrazeném fondu SQL se tato znalost používá k minimalizaci přesunu dat během dotazů, což zvyšuje výkon dotazů.

Tabulky distribuované pomocí algoritmu hash fungují dobře u velkých tabulek faktů ve schématu hvězdičky. Mohou mít velmi velký počet řádků a stále dosahovat vysokého výkonu. K dispozici jsou samozřejmě některé faktory návrhu, které vám pomohou získat výkon, který je distribuován distribuovaným systémům. Výběr dobrého distribučního sloupce je jedním z těchto aspektů, které jsou popsány v tomto článku.

Zvažte použití tabulky distribuované pomocí algoritmu hash v těchto případech:

- Velikost tabulky na disku je větší než 2 GB.
- Tabulka obsahuje časté operace vložení, aktualizace a odstranění.

### <a name="round-robin-distributed"></a>Distribuované kruhové dotazování

Distribuovaná tabulka kruhového dotazování rozděluje řádky tabulky rovnoměrně napříč všemi distribucí. Přiřazení řádků k distribucím je náhodné. Na rozdíl od tabulek distribuovaných pomocí algoritmu hash nejsou řádky se stejnými hodnotami přiřazeny ke stejné distribuci.

V důsledku toho systém někdy potřebuje vyvolat operaci přesunu dat, aby lépe organizoval vaše data předtím, než dokáže dotaz vyřešit.  Tento krok navíc může zpomalit vaše dotazy. Například spojení tabulky kruhového dotazování obvykle vyžaduje přepočet řádků, což je úspěšnost výkonu.

V následujících scénářích zvažte použití distribuce kruhového dotazování pro tabulku:

- Když začnete s jednoduchým výchozím bodem, protože se jedná o výchozí
- Pokud se žádný zjevně nepřipojuje klíč
- Pokud neexistuje dobrý kandidátný sloupec pro rozdělení hodnoty hash do tabulky
- Pokud tabulka nesdílí společný klíč JOIN s jinými tabulkami
- Pokud je spojení méně významné než jiné spojení v dotazu
- Když je tabulka dočasná pracovní tabulka

Kurz [načtení dat New York taxislužby města](./load-data-from-azure-blob-storage-using-copy.md#load-the-data-into-your-data-warehouse) poskytuje příklad načtení dat do pracovní tabulky kruhového dotazování.

## <a name="choosing-a-distribution-column"></a>Výběr distribučního sloupce

Tabulka distribuovaná algoritmem hash má distribuční sloupec, který je klíčem hash. Například následující kód vytvoří tabulku distribuovanou algoritmem hash s označením ProductKey jako distribuční sloupec.

```sql
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

Data uložená v distribučním sloupci lze aktualizovat. Aktualizace dat ve sloupci distribuce by mohla vést k náhodnému fungování dat.

Výběr distribučního sloupce je důležité rozhodnutí o návrhu, protože hodnoty v tomto sloupci určují, jak jsou řádky distribuovány. Nejlepší volba závisí na několika faktorech a obvykle zahrnuje kompromisy. Po výběru distribučního sloupce jej nelze změnit.  

Pokud jste nevybrali nejlepší sloupec poprvé, můžete pomocí [Create Table jako Select (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) znovu vytvořit tabulku s jiným distribučním sloupcem.

### <a name="choose-a-distribution-column-with-data-that-distributes-evenly"></a>Volba distribučního sloupce s daty, která se rovnoměrně distribuuje

Nejlepšího výkonu dosáhnete, pokud všechny distribuce mají přibližně stejný počet řádků. V případě, že jedna nebo více distribucí mají neúměrný počet řádků, některá distribuce dokončí jejich část paralelního dotazu ještě před ostatními. Vzhledem k tomu, že dotaz nelze dokončit, dokud nebudou dokončeny všechny distribuce, je každý dotaz co nejpomalejšího rozdělení rychlejší.

- Zešikmení dat znamená, že data nejsou rovnoměrně rozložena napříč distribucí.
- Při zpracování je možné, že některé distribuce při spouštění paralelních dotazů pobírají déle než jiné. K tomu může dojít, když jsou data nakloněná.
  
Pro vyrovnávání paralelního zpracování vyberte distribuční sloupec, který:

- **Má mnoho jedinečných hodnot.** Sloupec může obsahovat duplicitní hodnoty. Všechny řádky se stejnou hodnotou jsou však přiřazeny ke stejné distribuci. Vzhledem k tomu, že existují 60 distribuce, by měl mít sloupec aspoň 60 jedinečných hodnot.  Počet jedinečných hodnot je obvykle mnohem větší.
- **Nemá hodnoty NULL nebo obsahuje pouze několik hodnot NULL.** Pro extrémní příklad, pokud jsou všechny hodnoty ve sloupci NULL, jsou všechny řádky přiřazeny ke stejné distribuci. Výsledkem je, že zpracování dotazů je rozdělené na jednu distribuci a nemá výhodu paralelního zpracování.
- **Není sloupec data**. Všechna data pro stejné datum na stejné distribuci. Pokud se ke stejnému datu filtruje několik uživatelů, pak jenom 1 distribuce 60 provádí veškerou práci na zpracování.

### <a name="choose-a-distribution-column-that-minimizes-data-movement"></a>Výběr distribučního sloupce, který minimalizuje pohyb dat

Chcete-li získat správné dotazy na výsledky dotazu, může přesunout data z jednoho výpočetního uzlu do jiného. K přesunu dat často dochází, když dotazy mají spojení a agregace v distribuovaných tabulkách. Výběr distribučního sloupce, který pomáhá minimalizovat pohyb dat, je jedním z nejdůležitějších strategií pro optimalizaci výkonu vyhrazeného fondu SQL.

Chcete-li snížit pohyb dat, vyberte distribuční sloupec:

- Se používá v `JOIN` `GROUP BY` `DISTINCT` klauzulích,,, a `OVER` `HAVING` . Pokud mají dvě velké tabulky faktů časté spojení, výkon dotazů se vylepšuje při distribuci obou tabulek v jednom ze sloupců spojení.  V případě, že se tabulka v joins nepoužívá, zvažte možnost distribuovat tabulku do sloupce, který je v `GROUP BY` klauzuli často.
- Se *nepoužívá* v `WHERE` klauzulích. To může zúžit dotaz tak, aby se nespouštěl ve všech distribucích.
- Není *sloupec* data. Klauzule WHERE často filtrují podle data.  V takovém případě může být veškeré zpracování spuštěno pouze v několika distribucích.

### <a name="what-to-do-when-none-of-the-columns-are-a-good-distribution-column"></a>Co dělat, když žádný ze sloupců není dobrým distribučním sloupcem

Pokud žádný z vašich sloupců nemá dostatek jedinečných hodnot pro distribuční sloupec, můžete vytvořit nový sloupec jako složený z jedné nebo více hodnot. Chcete-li se vyhnout přesunu dat během provádění dotazu, použijte jako sloupec JOIN v dotazech složený sloupec distribuce.

Po navržení tabulky distribuované pomocí algoritmu hash je dalším krokem načtení dat do tabulky.  Pokyny k načtení najdete v tématu [načítání přehledu](design-elt-data-loading.md).

## <a name="how-to-tell-if-your-distribution-column-is-a-good-choice"></a>Jak zjistit, jestli je váš distribuční sloupec dobrou volbou

Po načtení dat do tabulky distribuované pomocí algoritmu hash zkontrolujte, jak rovnoměrně jsou řádky distribuovány v rámci 60 distribucí. Řádky na distribuci se mohou lišit až o 10%, aniž by to mělo znatelný dopad na výkon.

### <a name="determine-if-the-table-has-data-skew"></a>Určení, jestli má tabulka zešikmení dat

Rychlý způsob, jak zjistit, zda je možné data zkosit, je použití [příkazu DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Následující kód SQL vrátí počet řádků tabulky, které jsou uloženy v každé z distribucí 60. U vyváženého výkonu by se měly řádky v distribuované tabulce rovnoměrně rozložit napříč všemi distribucí.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Určení, které tabulky mají více než 10% zešikmení dat:

1. Vytvořte zobrazení dbo. vTableSizes, které se zobrazí v článku [Přehled tabulek](sql-data-warehouse-tables-overview.md#table-size-queries) .  
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

### <a name="check-query-plans-for-data-movement"></a>Kontrolovat plány dotazů na přesun dat

Dobrý distribuční sloupec umožňuje spojení a agregace mít minimální pohyb dat. To má vliv na způsob zápisu spojení. Aby bylo možné získat minimální pohyb dat pro spojení se dvěma tabulkami distribuovanými pomocí algoritmu hash, je nutné, aby jeden ze sloupců JOIN byl distribučním sloupcem.  Když se dvě tabulky distribuované pomocí algoritmu hash spojí na distribučním sloupci se stejným datovým typem, spojení nevyžaduje přesun dat. Spojení můžou používat další sloupce bez nutnosti přesunu dat.

Zamezení přesunu dat během spojování:

- Tabulky spojené s připojením musí být rozloženy na **jeden** ze sloupců účastnících se spojení.
- Datové typy sloupců spojení se musí shodovat mezi oběma tabulkami.
- Sloupce musí být spojeny s operátorem rovnosti.
- Typ spojení nesmí být `CROSS JOIN` .

Pokud chcete zjistit, jestli dotazy nastávají pohyb dat, můžete se podívat na plán dotazu.  

## <a name="resolve-a-distribution-column-problem"></a>Řešení problému s distribučním sloupcem

Není nutné vyhodnotit všechny případy zkosení dat. Distribuce dat je věcí k nalezení správného zůstatku mezi minimalizací zešikmení dat a pohybem dat. K minimalizaci obou možností a přesunu dat není vždy možné. Někdy se může stát, že minimální pohyb dat může převážit dopad, který by se měl zkosit.

Chcete-li se rozhodnout, jestli byste měli v tabulce vyhodnotit zešikmení dat, měli byste co nejvíce pochopit datové svazky a dotazy ve svých úlohách. Pomocí kroků v článku [monitorování dotazů](sql-data-warehouse-manage-monitor.md) můžete monitorovat dopad zkosení na výkon dotazů. Konkrétně hledejte, jak dlouho trvá provádění velkých dotazů na jednotlivých distribucích.

Vzhledem k tomu, že nemůžete změnit distribuční sloupec v existující tabulce, je typický způsob, jak vyřešit zešikmení dat, vytvořit tabulku znovu s jiným distribučním sloupcem.  

### <a name="re-create-the-table-with-a-new-distribution-column"></a>Opětovné vytvoření tabulky s novým distribučním sloupcem

V tomto příkladu se používá [Create Table jako vybrat](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) k opětovnému vytvoření tabulky s jiným sloupcem pro distribuci algoritmem hash.

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

Chcete-li vytvořit distribuovanou tabulku, použijte jeden z následujících příkazů:

- [CREATE TABLE (vyhrazený fond SQL)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [CREATE TABLE jako SELECT (vyhrazený fond SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)