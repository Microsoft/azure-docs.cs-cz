---
title: Monitorování a zlepšení výkonu
description: Azure SQL Database poskytuje nástroje pro sledování výkonu, které vám pomůžou identifikovat oblasti, které můžou zlepšit aktuální výkon dotazů.
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 01/25/2019
ms.openlocfilehash: f7e5f698a822cd68d8319102e1b8bddbbfa8ad70
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821323"
---
# <a name="monitor-and-improve-performance"></a>Monitorování a zlepšení výkonu

Azure SQL Database identifikuje možné problémy v databázi a doporučuje akce, které můžou zlepšit výkon svého pracovního vytížení díky inteligentním akcím ladění a doporučením.

Chcete-li zkontrolovat výkon databáze, použijte dlaždici **výkon** na stránce Přehled nebo přejděte dolů k části Podpora a řešení potíží:

   ![Zobrazit výkon](./media/sql-database-performance/entries.png)

V části Podpora a řešení potíží můžete použít následující stránky:


1. [Přehled výkonu](#performance-overview) pro monitorování výkonu vaší databáze. 
2. [Doporučení týkající se výkonu](#performance-recommendations) pro hledání doporučení výkonu, která můžou zlepšit výkon vašich úloh.
3. [Query Performance Insight](#query-performance-insight) najít Nejčastější dotazy, které jsou náročné na prostředky.
4. [Automatické ladění](#automatic-tuning) , které umožní Azure SQL Database automaticky optimalizovat vaši databázi.

## <a name="performance-overview"></a>Přehled výkonu

Toto zobrazení poskytuje souhrn výkonu vaší databáze a pomáhá s optimalizací výkonu a řešením potíží. 

![Výkon](./media/sql-database-performance/performance.png)

* Dlaždice **doporučení** obsahuje rozpis doporučení pro ladění pro vaši databázi (pokud existuje více), zobrazí se v nich více hlavních tří doporučení. Kliknutím na tuto dlaždici přejdete na **[doporučení týkající se výkonu](#performance-recommendations)** . 
* Dlaždice **aktivity ladění** poskytuje shrnutí probíhajících a dokončených akcí optimalizace pro vaši databázi, takže získáte rychlý přehled o historii aktivity ladění. Kliknutím na tuto dlaždici přejdete k úplnému zobrazení Historie ladění pro vaši databázi.
* Dlaždice **automatického ladění** zobrazuje [konfiguraci automatického ladění](sql-database-automatic-tuning-enable.md) pro vaši databázi (možnosti ladění, které se automaticky aplikují na vaši databázi). Kliknutím na tuto dlaždici otevřete dialogové okno Konfigurace automatizace.
* Dlaždice **dotazy databáze** zobrazuje souhrn výkonu dotazů pro vaši databázi (celkové využití DTU a nejčastější dotazy týkající se využívání prostředků). Kliknutím na tuto dlaždici přejdete na **[Query Performance Insight](#query-performance-insight)** .

## <a name="performance-recommendations"></a>Doporučení k výkonu

Tato stránka poskytuje inteligentní [doporučení pro ladění](sql-database-advisor.md) , která můžou zlepšit výkon vaší databáze. Na této stránce jsou uvedeny následující typy doporučení:

* Doporučení, která indexům vytvořit nebo vyřadit.
* Doporučení při identifikaci problémů schématu v databázi.
* Doporučení v případě, kdy dotazy můžou těžit z parametrizovaných dotazů

![Výkon](./media/sql-database-performance/recommendations.png)

Můžete také najít úplnou historii akcí optimalizace, které byly v minulosti aplikovány.

Naučte se, jak najít doporučení pro použití výkonu v článku [hledání a použití doporučení](sql-database-advisor-portal.md) výkonu.

## <a name="automatic-tuning"></a>Automatické ladění

Databáze SQL Azure můžou automaticky ladit výkon databáze pomocí [doporučení pro výkon](sql-database-advisor.md). Pokud se chcete dozvědět víc, přečtěte si [článek věnované automatickému ladění](sql-database-automatic-tuning.md). Pokud ho chcete povolit, přečtěte si, [Jak povolit automatické ladění](sql-database-automatic-tuning-enable.md).

## <a name="query-performance-insight"></a>Query Performance Insight

[Query Performance Insight](sql-database-query-performance.md) vám umožní věnovat méně času řešení potíží s výkonem databáze tím, že poskytuje:

* Podrobnější přehled o spotřebě prostředků databází (DTU). 
* Nejčastější dotazy náročné na procesor, které je možné optimalizovat pro zlepšení výkonu. 
* Možnost přejít k podrobnostem dotazu. 

  ![řídicí panel výkonu](./media/sql-database-query-performance/performance.png)

Další informace o této stránce najdete v článku **[Jak používat Query Performance Insight](sql-database-query-performance.md)** .

## <a name="additional-resources"></a>Další zdroje

* [Pokyny k výkonu Azure SQL Database pro izolované databáze](sql-database-performance-guidance.md)
* [Kdy má být použit elastický fond?](sql-database-elastic-pool-guidance.md)

