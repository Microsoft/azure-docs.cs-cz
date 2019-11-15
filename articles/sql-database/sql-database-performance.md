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
ms.date: 11/14/2019
ms.openlocfilehash: c710e2b7c4376fc1725032c86cd02d9a6c8cafac
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74090356"
---
# <a name="monitor-and-improve-performance"></a>Monitorování a zlepšení výkonu

Azure SQL Database identifikuje možné problémy v databázi a doporučuje akce, které můžou zlepšit výkon svého pracovního vytížení díky inteligentním akcím ladění a doporučením.

## <a name="performance-tuning-options"></a>Možnosti ladění výkonu

Dostupné možnosti ladění výkonu Azure SQL Database jsou k dispozici v navigační nabídce databáze v části Inteligentní výkon:

| Možnost optimalizace výkonu | Podpora jedné databáze a databáze ve fondu | Podpora databáze instancí |
| :----------------------------- | ----- | ----- |
| **[Přehled výkonu](#performance-overview)** – monitorujte všechny aktivity výkonu pro vaši databázi. | Ano | Ne | 
| **[Doporučení pro výkon](#performance-recommendations)** – zobrazuje doporučení pro výkon, která můžou zlepšit výkon svých úloh. | Ano | Ne | 
| **[Query Performance Insight](#query-performance-insight)** – zobrazuje výkon vysoce náročných dotazů na databázi. | Ano | Ne | 
| **[Automatické ladění](#automatic-tuning)** – použijte Azure SQL Database k automatickému optimalizaci výkonu databáze. | Ano | Ne | 

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

## <a name="query-performance-insight"></a>Query Performance Insight

[Query Performance Insight](sql-database-query-performance.md) vám umožní věnovat méně času řešení potíží s výkonem databáze tím, že poskytuje:

* Podrobnější přehled o spotřebě prostředků databází (DTU). 
* Nejčastější dotazy náročné na procesor, které je možné optimalizovat pro zlepšení výkonu. 
* Možnost přejít k podrobnostem dotazu. 

  ![řídicí panel výkonu](./media/sql-database-query-performance/performance.png)

Další informace o této stránce najdete v článku **[Jak používat Query Performance Insight](sql-database-query-performance.md)** .

## <a name="automatic-tuning"></a>Automatické ladění

Databáze SQL Azure můžou automaticky ladit výkon databáze pomocí [doporučení pro výkon](sql-database-advisor.md). Pokud se chcete dozvědět víc, přečtěte si [článek věnované automatickému ladění](sql-database-automatic-tuning.md). Pokud ho chcete povolit, přečtěte si, [Jak povolit automatické ladění](sql-database-automatic-tuning-enable.md).

## <a name="additional-resources"></a>Další zdroje

* [Pokyny k výkonu Azure SQL Database pro izolované databáze](sql-database-performance-guidance.md)
* [Kdy má být použit elastický fond?](sql-database-elastic-pool-guidance.md)
