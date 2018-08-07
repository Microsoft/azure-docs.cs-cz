---
title: Monitorování a optimalizace výkonu – Azure SQL Database | Dokumentace Microsoftu
description: Tipy pro ladění ve službě Azure SQL Database prostřednictvím hodnocení a zlepšování výkonu.
services: sql-database
author: danimir
manager: craigg
editor: ''
keywords: ladění, ladění, tipy pro optimalizaci výkonu sql database výkonu výkonu SQL ladění výkonu sql database
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: v-daljep
ms.reviewer: carlrab
ms.openlocfilehash: 8d8b983a5304f227d09392198d0f1307fe6dafde
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39524180"
---
# <a name="monitoring-and-performance-tuning"></a>Monitorování a optimalizace výkonu

Azure SQL Database automaticky spravuje, a flexibilní datová služba, kde můžete snadno monitorovat využití, přidání nebo odebrání prostředků (procesoru, paměti, vstupně-výstupní operace), najít doporučení, která může zlepšit výkon vaší databáze, nebo nechat databáze přizpůsobit vaší úloze a automaticky optimalizace výkonu.

Tento článek obsahuje přehled monitorování a možnosti, které jsou k dispozici ve službě Azure SQL Database pro optimalizaci výkonu.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="monitoring-and-troubleshooting-database-performance"></a>Monitorování a řešení potíží s výkonem databáze

Azure SQL Database umožňuje snadno monitorovat využití databáze a taky identifikovat dotazy, které by mohly způsobit problémy s výkonem. Můžete monitorovat výkon databáze pomocí zobrazení Azure portal nebo systému. Máte následující možnosti pro monitorování a řešení potíží s výkonem databáze:

1. V [webu Azure portal](https://portal.azure.com), klikněte na tlačítko **databází SQL**, vyberte databázi a pak použít graf monitorování k vyhledání prostředků blíží jejich maximální. Ve výchozím nastavení se zobrazí spotřeba DTU. Klikněte na tlačítko **upravit** změnit časový rozsah a hodnoty.
2. Použití [Query Performance Insight](sql-database-query-performance.md) identifikovat dotazy, které tráví nejvíce prostředků.
3. Můžete použít zobrazení dynamické správy (DMV), rozšířených událostí (`XEvents`) a Query Store v aplikaci SSMS zobrazíte parametry výkonu v reálném čase.

Zobrazit [tématu pokyny k výkonu](sql-database-performance-guidance.md) najít techniky, které můžete použít ke zlepšení výkonu služby Azure SQL Database, případě identifikujete některý problém pomocí těchto sestav nebo zobrazení.

> [!IMPORTANT] 
> Doporučujeme vám vždy používat nejnovější verzi aplikace Management Studio, aby se zajistila synchronizovanost s aktualizacemi Microsoft Azure a SQL Database. [Aktualizovat aplikaci SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
>

## <a name="optimize-database-to-improve-performance"></a>Optimalizace databáze za účelem zvýšení výkonu

Azure SQL Database vám pomůže identifikovat příležitosti, jak vylepšit a optimalizovat výkon dotazů beze změny zdroje kontrolou [doporučení pro optimalizaci výkonu](sql-database-advisor.md). Častým důvodem toho, že databáze je pomalá, jsou chybějící indexy a nedostatečně optimalizované dotazy. Můžete použít tato doporučení pro vyladění výkonu vašich úloh.
Můžete také umožňují Azure SQL database a [automaticky optimalizace výkonu vašich dotazů](sql-database-automatic-tuning.md) použitím všechny určené doporučení a ověřuje, že pomáhají zvýšit výkon databáze. Kvůli zvýšení výkonu vaší databáze můžete použít následující možnosti:

1. Použití [služby SQL Database Advisor](sql-database-advisor-portal.md) Chcete-li zobrazit doporučení pro vytváření a vyřazení indexů, parametrické dotazy a řešení problémů schématu.
2. [Povolení automatického ladění](sql-database-automatic-tuning-enable.md) a nechat Azure SQL database automaticky opravě zjištěných problémů s výkonem.

## <a name="improving-database-performance-with-more-resources"></a>Zlepšení výkonu databáze s více prostředky

Nakonec pokud nebyly nalezeny žádné užitečné položky, které může zlepšit výkon vaší databáze, můžete změnit množství prostředků, které jsou k dispozici ve službě Azure SQL Database. Můžete přiřadit více prostředků tak, že změníte [jednotek DTU úrovně](sql-database-service-tiers-dtu.md) samostatná databáze nebo zvýšení Edtu elastického fondu v každém okamžiku. Případně pokud používáte [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md), můžete změnit úroveň služby nebo zvýšit prostředky přidělené k vaší databázi. 
1. Pro samostatné databáze, můžete [změna úrovní služeb](sql-database-service-tiers-dtu.md) nebo [výpočetní prostředky](sql-database-service-tiers-vcore.md)na vyžádání ke zlepšení výkonu databáze.
2. Pro více databází, zvažte použití [elastické fondy](sql-database-elastic-pool-guidance.md) automaticky škálovat prostředky.

## <a name="tune-and-refactor-application-or-database-code"></a>Ladění a Refaktorujte aplikace nebo kódu databáze

Můžete změnit kód aplikace více optimálně použít databázi, změňte indexy, vynutit plány nebo používání pomocné parametry ručně přizpůsobit databáze vaší úloze. Najdete některé pokyny a tipy pro ruční optimalizace a přepsáním kódu v [tématu pokyny k výkonu](sql-database-performance-guidance.md) článku.


## <a name="next-steps"></a>Další postup

- Povolení automatického ladění ve službě Azure SQL Database a nechat funkce automatického ladění plně spravovat vaše úlohy najdete v tématu [povolení automatického ladění](sql-database-automatic-tuning-enable.md).
- Chcete-li použít ruční ladění, můžete zkontrolovat [doporučení na webu Azure portal pro optimalizaci](sql-database-advisor-portal.md) a aplikovat ručně ty, které zlepšují výkon vašich dotazů.
- Změnit prostředky, které jsou k dispozici v databázi tak, že změníte [úrovně služby Azure SQL Database](sql-database-performance-guidance.md)
