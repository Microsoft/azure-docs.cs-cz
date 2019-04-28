---
title: Migrujte svoje schéma do SQL Data Warehouse | Dokumentace Microsoftu
description: Tipy pro migraci vašeho schématu do služby Azure SQL Data Warehouse pro vývoj řešení.
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
origin.date: 04/17/2018
ms.date: 10/15/2018
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: 4139ea776f6947eeacf4620c3676606d6535dd2b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60748148"
---
# <a name="migrate-your-schemas-to-sql-data-warehouse"></a>Migrace vaší schémata do SQL Data Warehouse
Pokyny k migraci vašeho SQL schémata do služby SQL Data Warehouse. 

## <a name="plan-your-schema-migration"></a>Plánování migrace schématu

Při plánování migrace, najdete v článku [Přehled tabulek] [ table overview] se seznámit s aspekty návrhu tabulky jako je například statistiky, distribuci, dělení a indexování.  Také uvádí některé [nepodporované funkce tabulky] [ unsupported table features] a jejich řešení.

## <a name="use-user-defined-schemas-to-consolidate-databases"></a>Schémata definovaná uživatelem umožňuje konsolidovat databáze

Vaše existující úloha pravděpodobně obsahuje více než jednu databázi. Datový sklad SQL serveru může například obsahovat pracovní databáze, databáze datového skladu a některé databáze datového tržiště. V této topologii Každá databáze spouští jako samostatné úlohy se zásadami zabezpečení samostatné.

Naopak spouští úlohy celého datového skladu v rámci jedné databáze SQL Data Warehouse. Pro různé databáze nejsou povoleny spojení. Proto se SQL Data Warehouse očekává, že všechny tabulky v datovém skladu používá mají být uloženy v rámci jedné databáze.

Doporučujeme používat schémat definovaných uživateli ke sloučení vašich stávajících úloh do jedné databáze. Příklady najdete v tématu [schémat definovaných uživateli](sql-data-warehouse-develop-user-defined-schemas.md)

## <a name="use-compatible-data-types"></a>Kompatibilní datové typy
Upravte vaše datové typy, aby byl kompatibilní s SQL Data Warehouse. Seznam podporované a nepodporované datové typy najdete v tématu [datové typy][data types]. Toto téma poskytuje řešení pro nepodporované typy. Poskytuje také dotaz k identifikaci existujících typů, které nejsou podporované ve službě SQL Data Warehouse.

## <a name="minimize-row-size"></a>Minimální velikost řádku
Pro nejlepší výkon minimalizujte délka řádku tabulky. Protože kratší délky řádku vést k vyšší výkon, použijte nejmenší datové typy, které fungují pro vaše data. 

Šířka řádku tabulky PolyBase má limit 1 MB.  Pokud budete chtít načíst data do SQL Data Warehouse pomocí PolyBase, aktualizujte tabulky má maximální šířku menší než 1 MB. 

## <a name="specify-the-distribution-option"></a>Zadejte možnosti distribuce
SQL Data Warehouse je distribuovaný databázový systém. Každá tabulka je distribuované nebo replikovat na výpočetních uzlech. Je možnost tabulky, který umožňuje určit, jak se bude distribuovat data. K dispozici jsou možnosti kruhové dotazování, replikaci, nebo hodnoty hash distribuován. Každá obsahuje výhody a nevýhody. Pokud nezadáte možnost distribuce, SQL Data Warehouse použije jako výchozí kruhové dotazování.

- Výchozím nastavením je kruhové dotazování. Je nejjednodušší na používání a načtení dat tak rychle, ale spojení bude vyžadovat přesun dat, což zpomalí výkon dotazů.
- Replikované úložiště kopii tabulky na jednotlivých výpočetních uzlech. Replikované tabulky jsou výkonné, protože nevyžadují přesunu dat pro spojování a agregaci. Vyžadovat dodatečné úložiště a proto nejvhodnější pro menší tabulky.
- Provádět distribuci hodnot hash distribuuje řádky napříč všemi uzly pomocí funkce hash. Provádět distribuci hodnot hash tabulky jsou srdce služby SQL Data Warehouse, protože jsou určeny k poskytování vysokého výkonu dotazů na velké tabulky. Tato možnost vyžaduje některé plánování vybrat nejlepší sloupec na základě které chcete distribuovat data. Ale pokud se rozhodnete není nejlepší sloupec poprvé, můžete snadno znovu distribuovat data na jiný sloupec. 

Vyberte si nejvhodnější plán distribuce pro každou tabulku, najdete v článku [distribuovaných tabulkách](sql-data-warehouse-tables-distribute.md).

## <a name="next-steps"></a>Další postup
Jakmile se úspěšně migrovaly schématu databáze do SQL Data Warehouse, pokračujte na některý z následujících článků:

* [Migrace dat][Migrate your data]
* [Migrace kódu][Migrate your code]

Další informace o osvědčených postupech pro SQL Data Warehouse, najdete v článku [osvědčené postupy] [ best practices] článku.

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

<!--Update_Description: update meta properties, add new content about Migrate schemas to SQL Data Warehouse -->