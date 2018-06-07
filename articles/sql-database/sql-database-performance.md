---
title: Monitorování a zlepšit výkon - Azure SQL Database | Microsoft Docs
description: Databáze SQL Azure poskytuje nástroje pro sledování výkonu, který vám pomůže identifikovat oblasti, které může zlepšit výkon aktuální dotaz.
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 7d1fa8d05de11ed68bedbc91fbe22aa64b25372e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34650379"
---
# <a name="monitor-and-improve-performance"></a>Monitorování a zlepšení výkonu
Azure SQL Database identifikuje potenciální problémy ve vaší databázi a doporučuje akce, které může zlepšit výkon vašich úloh zadáním inteligentního vyladění akce a doporučení.

Chcete-li zkontrolovat výkon databáze, použijte **výkonu** dlaždici na stránce Přehled, nebo přejděte dolů "Podpora + řešení potíží" část:

   ![Zobrazení výkonu](./media/sql-database-performance/entries.png)

V "Podpora + řešení potíží" části, můžete použít následující stránky:


1. [Přehled výkonnostní](#performance-overview) sledovat výkon vaší databáze. 
2. [Doporučení pro optimální výkon](#performance-recommendations) najít doporučení výkonu, které může zlepšit výkon vašich úloh.
3. [Dotaz na informace o výkonu](#query-performance-insight) najít dotazy na nejvyšší prostředky.
4. [Automatické ladění](#automatic-tuning) umožníte automaticky optimalizací databáze Azure SQL Database.

## <a name="performance-overview"></a>Přehled výkonnostní
Toto zobrazení obsahuje souhrn výkon databáze a pomůže vám s výkonem, ladění a řešení potíží. 

![Výkon](./media/sql-database-performance/performance.png)

* **Doporučení** dlaždice obsahuje rozpis systémů ladění doporučení pro vaši databázi (první tři doporučení jsou uvedené. Pokud existují další). Kliknutím na tuto dlaždici přejdete k  **[výkonu doporučení](#performance-recommendations)**. 
* **Ladění aktivity** dlaždice poskytuje souhrn probíhající a dokončené ladění akce pro vaši databázi, která poskytuje rychlý přehled do historie ladění aktivity. Kliknutím na tuto dlaždici přejdete na úplné ladění zobrazení historie pro vaši databázi.
* **Automatické ladění** dlaždici ukazuje [automatické ladění konfigurace](sql-database-automatic-tuning-enable.md) pro vaši databázi (optimalizace pro možnosti, které budou automaticky použita pro vaši databázi). Kliknutím na tuto dlaždici, otevře se dialogové okno Konfigurace automatizace.
* **Dotazy na databázi** dlaždice zobrazuje souhrn výkon dotazů pro databázi (celkový počet jednotek DTU využití a horní na prostředky dotazy). Kliknutím na tuto dlaždici přejdete k  **[Query Performance Insight](#query-performance-insight)**.

## <a name="performance-recommendations"></a>Doporučení k výkonu
Tato stránka obsahuje inteligentního [ladění doporučení](sql-database-advisor.md) , může zlepšit výkon vaší databáze. Na této stránce se zobrazují následující typy doporučení:

* Doporučení pro indexy, které se vytvořit nebo vyřadit.
* Doporučení, když jsou zjištěny problémy schématu v databázi.
* Doporučení pro dotazy využívat parametrizované dotazy.

![Výkon](./media/sql-database-performance/recommendations.png)

Můžete také získat úplnou historii ladění akce, které byly použity v minulosti.

Zjistěte, jak najít výkonu doporučení v operátoru apply [najít a použít výkonu doporučení](sql-database-advisor-portal.md) článku.

## <a name="automatic-tuning"></a>Automatické ladění
Databáze Azure SQL můžete vyladit výkon databáze automaticky použitím [výkonu doporučení](sql-database-advisor.md). Další informace, přečtěte si [automatické ladění článku](sql-database-automatic-tuning.md). Chcete-li ji povolit, přečtěte si [povolení automatické ladění](sql-database-automatic-tuning-enable.md).

## <a name="query-performance-insight"></a>Query Performance Insight
[Dotaz na informace o výkonu](sql-database-query-performance.md) umožňuje trávit méně času tím, že poskytuje řešení potíží s výkonem databáze:

* Podrobnější přehled o vaší spotřeby prostředků (DTU) databází. 
* Nejvíce využívají procesor dotazů, které lze ladit potenciálně pro zlepšení výkonu. 
* Možnost k podrobnostem na podrobné informace o dotazu. 

  ![řídicí panel výkonu](./media/sql-database-query-performance/performance.png)

Další informace o této stránce naleznete v článku  **[použití Query Performance Insight](sql-database-query-performance.md)**.

## <a name="additional-resources"></a>Další zdroje informací:
* [Azure SQL Database – Průvodce výkonem pro izolované databáze](sql-database-performance-guidance.md)
* [Pokud má být použita fondu elastické databáze?](sql-database-elastic-pool-guidance.md)

