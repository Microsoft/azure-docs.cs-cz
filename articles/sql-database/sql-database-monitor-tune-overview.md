---
title: Monitorování a optimalizace výkonu – Azure SQL Database | Dokumentace Microsoftu
description: Tipy pro ladění ve službě Azure SQL Database prostřednictvím hodnocení a zlepšování výkonu.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 07/16/2018
ms.openlocfilehash: c79285247950510791ede915fcf0e5373792044f
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47165037"
---
# <a name="monitoring-and-performance-tuning"></a>Monitorování a optimalizace výkonu

Azure SQL Database automaticky spravuje, a flexibilní datová služba, kde můžete snadno monitorovat využití, přidání nebo odebrání prostředků (procesoru, paměti, vstupně-výstupní operace), najít doporučení, která může zlepšit výkon vaší databáze, nebo nechat databáze přizpůsobit vaší úloze a automaticky optimalizace výkonu.

## <a name="overview-of-monitoring-database-performance-in-azure-sql-database"></a>Přehled monitorování výkonu databáze ve službě Azure SQL Database
Monitorování výkonu databáze SQL v Azure začíná sledováním využití prostředků relativně ke zvolené úrovni výkonu databáze. Monitorování vám pomůže určit, zda má přebytečnou kapacitu databáze, nebo má potíže, protože prostředky jsou maxed navýšení kapacity a následně se rozhodnete, zda je třeba upravit výpočetního prostředí a databáze v úrovních služeb [nákupu založený na DTU model](sql-database-service-tiers-dtu.md) nebo [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md). Můžete monitorovat v databázi [webu Azure portal](https://portal.azure.com) pomocí následující grafických nástrojů nebo pomocí SQL [zobrazení dynamické správy (DMV)](sql-database-monitoring-with-dmvs.md).

Azure SQL Database vám pomůže identifikovat příležitosti, jak vylepšit a optimalizovat výkon dotazů beze změny zdroje kontrolou [doporučení pro optimalizaci výkonu](sql-database-advisor.md). Častým důvodem toho, že databáze je pomalá, jsou chybějící indexy a nedostatečně optimalizované dotazy. Můžete použít tato doporučení pro vyladění výkonu vašich úloh.
Můžete také umožňují Azure SQL database a [automaticky optimalizace výkonu vašich dotazů](sql-database-automatic-tuning.md) použitím všechny určené doporučení a ověřuje, že pomáhají zvýšit výkon databáze. Máte následující možnosti pro monitorování a řešení potíží s výkonem databáze:

- V [webu Azure portal](https://portal.azure.com), klikněte na tlačítko **databází SQL**, vyberte databázi a pak použít graf monitorování k vyhledání prostředků blíží jejich maximální. Ve výchozím nastavení se zobrazí spotřeba DTU. Klikněte na tlačítko **upravit** změnit časový rozsah a hodnoty.
- Použití [Query Performance Insight](sql-database-query-performance.md) identifikovat dotazy, které tráví nejvíce prostředků.
- Použití [služby SQL Database Advisor](sql-database-advisor-portal.md) Chcete-li zobrazit doporučení pro vytváření a vyřazení indexů, parametrické dotazy a řešení problémů schématu.
- Použití [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) pro automatické monitorování výkonu vaší databáze. Po zjištění problému s výkonem se vygeneruje protokol diagnostiky s podrobnostmi a kořenové příčiny analýzy RCA () problému. Pokud je to možné, poskytuje doporučení pro zlepšení výkonu.
- [Povolení automatického ladění](sql-database-automatic-tuning-enable.md) a nechat Azure SQL database automaticky opravě zjištěných problémů s výkonem.
- Můžete také použít [zobrazení dynamické správy (DMV)](sql-database-monitoring-with-dmvs.md), [rozšířených událostí (`XEvents`) (sql-database/sql-database-xevent-db-diff-from-svr.md) a [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) výkon Parametry v reálném čase. Zobrazit [Průvodce výkonem](sql-database-performance-guidance.md) najít techniky, které můžete použít ke zlepšení výkonu služby Azure SQL Database, případě identifikujete některý problém pomocí těchto sestav nebo zobrazení.

## <a name="monitor-databases-using-the-azure-portal"></a>Monitorování databází na portálu Azure
Na [portálu Azure](https://portal.azure.com/) můžete monitorovat využití izolované databáze jednoduše tak, že vyberete databázi a kliknete na graf **Monitorování**. Zobrazí se okno **Metrika**, které můžete upravit kliknutím na **Upravit graf**. Přidejte následující metriky:

* Procento CPU
* Procento DTU
* Procento datových V/V
* Procento velikosti databáze

Jakmile přidáte tyto metriky, můžete pokračovat jejich zobrazením v **monitorování** graf s dalšími informacemi o **metrika** okna. Tyto čtyři metriky uvádějí průměrné využití v procentech vzhledem k hodnotě **DTU** vaší databáze. Zobrazit [nákupní model založený na DTU](sql-database-service-tiers-dtu.md) a [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md) články pro další informace o úrovních služeb.  

![Monitorování výkonu databáze v rámci úrovně služeb](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

Můžete také nastavit upozornění na výkonové metriky. Klikněte na tlačítko **Přidat upozornění** v okně **Metrika**. Nastavte upozornění podle pokynů průvodce. Můžete určit, zda chcete být upozorněni na překročení zadané prahové hodnoty, nebo naopak když metrika poklesne pod zadanou mez.

Například pokud očekáváte nárůst zatížení databáze, můžete nastavit e-mailové upozornění pro případ, že databáze překročí 80 % kterékoli výkonové metriky. Můžete to použít jako včasné varování zjistit, když bude pravděpodobně nutné přepnout na další vyšší výpočetní velikost.

Metriky výkonu také můžete zjistit, zda je možné nižší výpočty velikosti. Předpokládejme, že používáte databáze S2 v úrovni Standard a všechny metriky ukazují, že databáze v průměru nevyužívá více než 10 % dostupného výkonu. Je pravděpodobné, že databáze bude dobře fungovat i v úrovni Standard S1. Nezapomínejte, úloh, které výskytu špiček nebo náhlého před provedením kolísání nižší výpočty velikosti.

## <a name="improving-database-performance-with-more-resources"></a>Zlepšení výkonu databáze s více prostředky

Nakonec pokud nebyly nalezeny žádné užitečné položky, které může zlepšit výkon vaší databáze, můžete změnit množství prostředků, které jsou k dispozici ve službě Azure SQL Database. Můžete přiřadit více prostředků tak, že změníte [jednotek DTU úrovně](sql-database-service-tiers-dtu.md) izolované databáze nebo zvýšení Edtu elastického fondu v každém okamžiku. Případně pokud používáte [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md), můžete změnit úroveň služby nebo zvýšit prostředky přidělené k vaší databázi. 
1. Pro izolované databáze můžete [změna úrovní služeb](sql-database-service-tiers-dtu.md) nebo [výpočetní prostředky](sql-database-service-tiers-vcore.md) na vyžádání ke zlepšení výkonu databáze.
2. Pro více databází, zvažte použití [elastické fondy](sql-database-elastic-pool-guidance.md) automaticky škálovat prostředky.

## <a name="tune-and-refactor-application-or-database-code"></a>Ladění a Refaktorujte aplikace nebo kódu databáze

Můžete změnit kód aplikace více optimálně použít databázi, změňte indexy, vynutit plány nebo používání pomocné parametry ručně přizpůsobit databáze vaší úloze. Najdete některé pokyny a tipy pro ruční optimalizace a přepsáním kódu v [tématu pokyny k výkonu](sql-database-performance-guidance.md) článku.


## <a name="next-steps"></a>Další postup

- Povolení automatického ladění ve službě Azure SQL Database a nechat funkce automatického ladění plně spravovat vaše úlohy najdete v tématu [povolení automatického ladění](sql-database-automatic-tuning-enable.md).
- Chcete-li použít ruční ladění, můžete zkontrolovat [doporučení na webu Azure portal pro optimalizaci](sql-database-advisor-portal.md) a aplikovat ručně ty, které zlepšují výkon vašich dotazů.
- Změnit prostředky, které jsou k dispozici v databázi tak, že změníte [úrovně služby Azure SQL Database](sql-database-performance-guidance.md)
