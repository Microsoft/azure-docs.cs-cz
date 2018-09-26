---
title: Monitorování a zlepšování výkonnosti – Azure SQL Database | Dokumentace Microsoftu
description: Azure SQL Database nabízí nástroje Sledování výkonu, který vám pomůže identifikovat oblasti, které může zlepšit výkon aktuálního dotazu.
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 35af3a926ab3c3a3046eab921e86b6610edc309c
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162951"
---
# <a name="monitor-and-improve-performance"></a>Monitorování a zlepšování výkonnosti
Identifikuje potenciální problémy ve vaší databázi Azure SQL Database a doporučuje akce, které může zlepšit výkon úloh tím, že poskytuje inteligentní optimalizačních akcí a doporučení.

Chcete-li zkontrolovat výkon své databáze, použijte **výkonu** dlaždice na stránce Přehled, nebo přejděte dolů "Podpora a řešení potíží" části:

   ![Zobrazení výkonu](./media/sql-database-performance/entries.png)

V "Podpora a řešení potíží" oddílu, můžete použít na následujících stránkách:


1. [Přehled výkonu](#performance-overview) k monitorování výkonu vaší databáze. 
2. [Doporučení k výkonu](#performance-recommendations) najít doporučení k výkonu, které může zvýšit výkon vašich úloh.
3. [Query Performance Insight](#query-performance-insight) najít hlavní dotazy využívající prostředky.
4. [Automatické ladění](#automatic-tuning) umožňuje automaticky Optimalizujte vaši databázi Azure SQL Database.

## <a name="performance-overview"></a>Přehled výkonu
Toto zobrazení poskytuje přehled výkonu vaší databáze a pomáhá s výkonem, ladění a řešení potíží. 

![Výkon](./media/sql-database-performance/performance.png)

* **Doporučení** dlaždice obsahuje rozpis systémů doporučení pro vaši databázi pro optimalizaci (první tři doporučení jsou zobrazena pokud existují další). Kliknutím na tuto dlaždici přejdete na  **[doporučení k výkonu](#performance-recommendations)**. 
* **Aktivita optimalizace** dlaždice obsahuje souhrn probíhající a dokončená ladění akce pro vaši databázi, získáte rychlý přehled historii aktivita optimalizace. Kliknutím na tuto dlaždici přejdete na zobrazení ladění úplné historie pro vaši databázi.
* **Automatického ladění** dlaždice ukazuje [konfiguraci automatického ladění](sql-database-automatic-tuning-enable.md) pro vaši databázi (optimalizace pro možnosti, které jsou automaticky použity k vaší databázi). Kliknutím na tuto dlaždici, otevře se dialogové okno Konfigurace služby automation.
* **Dotazy na databázi** dlaždici se zobrazuje souhrn výkonu pro vaši databázi (celkové DTU využití a zároveň klauzuli top dotazy využívající prostředky). Kliknutím na tuto dlaždici přejdete na  **[Query Performance Insight](#query-performance-insight)**.

## <a name="performance-recommendations"></a>Doporučení k výkonu
Tato stránka poskytuje inteligentní [doporučení pro optimalizaci](sql-database-advisor.md) , který může zlepšit výkon vaší databáze. Na této stránce jsou uvedeny následující typy doporučení:

* Doporučení pro indexy, které chcete vytvořit nebo vyřadit.
* Doporučení pro problémy s schématu jsou uvedené v databázi.
* Doporučení pro parametrizované dotazy využívat dotazy.

![Výkon](./media/sql-database-performance/recommendations.png)

Můžete také vyhledat úplnou historii ladění akce, které byly použity v minulosti.

Zjistěte, jak najít použít doporučení k výkonu v [vyhledání a použití doporučení k výkonu](sql-database-advisor-portal.md) článku.

## <a name="automatic-tuning"></a>Automatické ladění
Azure SQL Database může automaticky ladění výkonu databáze s použitím [doporučení k výkonu](sql-database-advisor.md). Další informace najdete v článku [automatické ladění článku](sql-database-automatic-tuning.md). Ho Pokud chcete povolit, přečtěte si [povolení automatického ladění](sql-database-automatic-tuning-enable.md).

## <a name="query-performance-insight"></a>Query Performance Insight
[Query Performance Insight](sql-database-query-performance.md) umožňuje strávit míň času tím, že poskytuje řešení potíží s výkonem databáze:

* Podrobnější přehledy o využití prostředků (DTU) databáze. 
* Dotazy, které mohou potenciálně optimalizovaných pro využívající procesor, vyšší výkon. 
* Možnost Přejít na podrobnosti o dotazu. 

  ![řídicí panel výkon](./media/sql-database-query-performance/performance.png)

Další informace o této stránce najdete v článku  **[postup použití nástroje Query Performance Insight](sql-database-query-performance.md)**.

## <a name="additional-resources"></a>Další zdroje informací:
* [Azure SQL Database – Průvodce výkonem pro izolované databáze](sql-database-performance-guidance.md)
* [Pokud by měl používat elastický fond?](sql-database-elastic-pool-guidance.md)

