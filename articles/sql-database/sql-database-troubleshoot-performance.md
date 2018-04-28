---
title: Monitorování a optimalizace výkonu – Azure SQL Database | Microsoft Docs
description: Tipy k ladění ve službě Azure SQL Database pomocí vyhodnocení a zlepšování výkonu.
services: sql-database
author: v-shysun
manager: craigg
editor: ''
keywords: ladění, ladění, ladění tipy, výkonu sql databáze výkonu výkonu SQL optimalizace výkonu databáze sql
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 04/01/2018
ms.author: v-shysun
ms.openlocfilehash: 710d517621cb9d4d9d2e9bf29e4facf7d75ab481
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="monitoring-and-performance-tuning"></a>Monitorování a optimalizace výkonu

Databáze SQL Azure je automaticky prováděna a flexibilní datová služba, kde můžete snadno sledovat využití, přidat nebo odebrat prostředky (procesor, paměť, vstupně-výstupní operace), najít doporučení, které může zlepšit výkon vaší databáze, nebo nechat databáze přizpůsobit pro vaši úlohu a automaticky optimalizace výkonu.

Tento článek obsahuje přehled monitorování a možností, které jsou k dispozici ve službě Azure SQL Database ladění výkonu.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="monitoring-and-troubleshooting-database-performance"></a>Monitorování a řešení potíží s výkonem databáze

Databáze SQL Azure umožňuje snadno monitorovat využití vaší databáze a identifikovat dotazy, které by mohly způsobit problémy s výkonem. Můžete sledovat výkon databáze pomocí Azure zobrazení portálu nebo systému. Máte následující možnosti pro monitorování a řešení potíží s výkonem databáze:

1. V [portál Azure](https://portal.azure.com), klikněte na tlačítko **databází SQL**, vyberte databázi a pak vyhledejte prostředky blíží jejich maximální pomocí graf sledování. Spotřeba DTU se zobrazí ve výchozím nastavení. Klikněte na tlačítko **upravit** změnit časový rozsah a hodnoty zobrazené.
2. Použití [Query Performance Insight](sql-database-query-performance.md) k identifikaci dotazy, které potřebují nejvíc prostředků.
3. Můžete použít zobrazení dynamické správy (zobrazení dynamické správy), rozšířených událostí (`XEvents`) a úložiště dotazů v aplikaci SSMS se získat parametry výkonu v reálném čase.

Najdete v článku [výkonu pokyny tématu](sql-database-performance-guidance.md) najít technik, které můžete použít ke zlepšení výkonu databáze SQL Azure, je-li identifikovat některé problém pomocí těchto sestav a zobrazení.

> [!IMPORTANT] 
> Doporučujeme vám vždy používat nejnovější verzi aplikace Management Studio, aby se zajistila synchronizovanost s aktualizacemi Microsoft Azure a SQL Database. [Aktualizovat aplikaci SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
>

## <a name="optimize-database-to-improve-performance"></a>Optimalizace databáze za účelem zvýšení výkonu

Databáze SQL Azure umožňuje identifikovat příležitosti pro zlepšení a optimalizovat výkon dotazů beze změny prostředky kontrolou [doporučení ladění výkonu](sql-database-advisor.md). Častým důvodem toho, že databáze je pomalá, jsou chybějící indexy a nedostatečně optimalizované dotazy. Můžete použít tyto vyladění doporučení pro zlepšení výkonu vašich úloh.
Můžete také umožňují Azure SQL database k [automaticky optimalizovat výkon vašich dotazů](sql-database-automatic-tuning.md) použitím všechny identifikovat doporučení a ověřování, že se zlepšit výkon databáze. Pro zlepšení výkonu databáze můžete použít následující možnosti:

1. Použití [Poradce pro funkci SQL Database](sql-database-advisor-portal.md) zobrazíte doporučení k vytváření a rušení indexů, parametrické dotazy a opravit problémy schématu.
2. [Povolit automatické ladění](sql-database-automatic-tuning-enable.md) a nechat Azure SQL databáze automaticky oprava identifikovat problémy s výkonem.

## <a name="improving-database-performance-with-more-resources"></a>Zvýšení výkonu databáze s další prostředky

Nakonec pokud nejsou žádné řešitelné položky, které může zlepšit výkon vaší databáze, můžete změnit objem prostředků, které jsou k dispozici ve službě Azure SQL Database. Můžete přiřadit více prostředků změnou [vrstvy služby DTU](sql-database-service-tiers-dtu.md) samostatná databáze nebo zvýšení jednotky Edtu fondu elastické databáze kdykoli. Případně pokud používáte [nákupní model (preview) na základě vCore](sql-database-service-tiers-vcore.md), můžete změnit úroveň služby nebo zvýšit prostředky přidělené vaší databáze. 
1. Pro samostatné databáze, můžete [Změna úrovně služeb](sql-database-service-tiers-dtu.md) nebo [výpočetní prostředky](sql-database-service-tiers-vcore.md)na vyžádání ke zlepšení výkonu databáze.
2. V případě více databází, zvažte použití [elastické fondy](sql-database-elastic-pool-guidance.md) prostředky automaticky škálovat.

## <a name="tune-and-refactor-application-or-database-code"></a>Ladění a refactor aplikace nebo kódu databáze

Kód aplikace více optimálně použít databázi, změňte indexy, vynutit plány nebo ručně přizpůsobit databázi do úlohy pomocí odkazů na servery, můžete změnit. Najít některé pokyny a tipy pro ruční ladění a přepisování kód [výkonu pokyny tématu](sql-database-performance-guidance.md) článku.


## <a name="next-steps"></a>Další postup

- Povolit automatické ladění ve službě Azure SQL Database a nechat automatické ladění funkce plně spravovat vaše úlohy najdete v tématu [povolit automatické ladění](sql-database-automatic-tuning-enable.md).
- Chcete-li použít ruční ladění, můžete zjistit [ladění doporučení na portálu Azure](sql-database-advisor-portal.md) a ty, které zlepšit výkon vašich dotazů použít ručně.
- Změnit prostředky, které jsou k dispozici ve vaší databázi změnou [úrovních služby databáze SQL Azure](sql-database-performance-guidance.md)
