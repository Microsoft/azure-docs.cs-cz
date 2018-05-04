---
title: Migrace vašeho schématu do SQL Data Warehouse | Microsoft Docs
description: Tipy pro migraci schématu do Azure SQL Data Warehouse na vývoj řešení.
services: sql-data-warehouse
author: jrowlandjones
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: fb1085450a16acb0f9a06a9dea9d91fc5ca23363
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="migrate-your-schemas-to-sql-data-warehouse"></a>Migrace vaší schémata do SQL Data Warehouse
Pokyny k migraci vaší schémata SQL do SQL Data Warehouse. 

## <a name="plan-your-schema-migration"></a>Plánování migrace schématu

Při plánování migrace najdete v článku [tabulky přehled] [ table overview] se seznámit s aspekty návrhu tabulky například statistiky, distribuci, vytváření oddílů a indexování.  Také uvádí některé [nepodporované funkce tabulky] [ unsupported table features] a jejich řešení.

## <a name="use-user-defined-schemas-to-consolidate-databases"></a>Použít vlastní schémata pro konsolidaci databází

Vaše stávající úlohy pravděpodobně obsahuje více než jednu databázi. Datového skladu SQL serveru může například obsahovat pracovní databáze, databáze datového skladu a některé databáze datového tržiště. V této topologii spouští každou databázi jako samostatné zatížení zásadám samostatné zabezpečení.

Naopak SQL Data Warehouse spouští úlohy celého datového skladu v rámci jedné databáze. Mezi databáze nejsou povoleny spojení. Proto SQL Data Warehouse očekává, že všechny tabulky použité v datovém skladu ukládaly v rámci jedné databáze.

Doporučujeme používat vlastní schémata pro konsolidaci vaše stávající úlohy do jedné databáze. Příklady najdete v tématu [uživatelem definované schémata](sql-data-warehouse-develop-user-defined-schemas.md)

## <a name="use-compatible-data-types"></a>Použít kompatibilní datové typy
Upravte svým datovým typům, aby byl kompatibilní s SQL Data Warehouse. Seznam podporované a nepodporované datové typy, naleznete v části [datové typy][data types]. Toto téma nabízí řešení pro nepodporované typy. Poskytuje také dotaz k identifikaci existující typy, které nejsou podporované v SQL Data Warehouse.

## <a name="minimize-row-size"></a>Minimální velikost řádku
Pro nejlepší výkon Minimalizujte délku řádku tabulky. Vzhledem k tomu, že kratší délky řádek vést k dosažení vyššího výkonu, použijte nejmenší datové typy, které fungují pro vaše data. 

Šířka řádku tabulky má PolyBase omezení 1 MB.  Pokud budete chtít načíst data do SQL Data Warehouse pomocí PolyBase, aktualizujte tabulky tak, aby měl maximální šířky menší než 1 MB. 

<!--
- For example, this table uses variable length data but the largest possible size of the row is still less than 1 MB. PolyBase will load data into this table.

- This table uses variable length data and the defined row width is less than one MB. When loading rows, PolyBase allocates the full length of the variable-length data. The full length of this row is greater than one MB.  PolyBase will not load data into this table.  

-->

## <a name="specify-the-distribution-option"></a>Zadejte možnosti distribuce
SQL Data Warehouse je systém distribuovanou databázi. Každá tabulka je distribuovat nebo replikovat mezi výpočetní uzly. Není možnost tabulky, který umožňuje určit, jak se bude distribuovat data. Mezi volby patří kruhového dotazování, replikují, nebo distribuovat algoritmu hash. Každý má výhody a nevýhody. Pokud nezadáte možnosti distribuce, použije SQL Data Warehouse pomocí kruhového dotazování jako výchozí.

- Kruhové dotazování je výchozí. Je nejjednodušším způsobem, a zatížením tak rychlý jako možný, ale spojení dat bude vyžadovat přesun dat, což zpomalí výkon dotazů.
- Replikované uloží kopie tabulky na každém výpočetním uzlu. Replikované tabulky jsou původce, protože nevyžadují přesun dat pro spojování a agregaci. Vyžadovat dodatečné úložiště a proto nejvhodnější pro menší tabulky.
- Hodnota hash distribuované rozděluje řádky na všechny uzly prostřednictvím funkce hash. Hodnota hash distribuované tabulky jsou srdcem SQL Data Warehouse vzhledem k tomu, že jsou navrženy k poskytování vysokého výkonu dotazu na velké tabulky. Tato možnost vyžaduje některé plánování vybrat nejlepší sloupec, na které chcete distribuovat data. Ale když nebude sloupci nejlepší poprvé, můžete snadno znovu distribuovat dat na jiný sloupec. 

Chcete-li zvolit nejlepší možnost distribuce pro každou tabulku, přečtěte si téma [distribuované tabulky](sql-data-warehouse-tables-distribute.md).


## <a name="next-steps"></a>Další postup
Po úspěšné migraci svého schématu databáze do SQL Data Warehouse, pokračujte na jednu z následujících článků:

* [Migrace dat][Migrate your data]
* [Migrace vašeho kódu][Migrate your code]

Další informace o osvědčených postupech pro SQL Data Warehouse najdete [osvědčené postupy] [ best practices] článku.

<!--Image references-->

<!--Article references-->
[Migrate your code]: ./sql-data-warehouse-migrate-code.md
[Migrate your data]: ./sql-data-warehouse-migrate-data.md
[best practices]: ./sql-data-warehouse-best-practices.md
[table overview]: ./sql-data-warehouse-tables-overview.md
[unsupported table features]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[data types]: ./sql-data-warehouse-tables-data-types.md
[unsupported data types]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types

<!--MSDN references-->


<!--Other Web references-->
