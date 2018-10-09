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
ms.date: 10/06/2018
ms.openlocfilehash: 7a83bb527a4ccea10e8d9d59d9b5f8e6e75ae106
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48857801"
---
# <a name="monitoring-and-performance-tuning"></a>Monitorování a optimalizace výkonu

Azure SQL Database automaticky spravuje, a flexibilní datová služba, kde můžete snadno monitorovat využití, přidání nebo odebrání prostředků (procesoru, paměti, vstupně-výstupní operace), najít doporučení, která může zlepšit výkon vaší databáze, nebo nechat databáze přizpůsobit vaší úloze a automaticky optimalizace výkonu.

## <a name="the-state-of-an-active-query"></a>Stav aktivního dotazu

Kvůli zvýšení výkonu Azure SQL Database se pochopit, že každý požadavek aktivní dotaz z vaší aplikace je buď ve stavu čekání nebo spuštěné. Při řešení problému s výkonem ve službě Azure SQL Database, mějte v následující tabulce:

![Stavy úlohy](./media/sql-database-monitor-tune-overview/workload-states.png)

Pro úlohy s problémy s výkonem, výkon vydávání Moje způsobovat kolize procesoru ( **týkající se spuštění** podmínky) nebo jednotlivé dotazy čekají na libovolnou položku ( **související čekání** podmínky) .

- **Nadměrné využití výkonu procesoru ve službě Azure SQL database**:

  Může se zobrazit nadměrnému využití procesoru využití způsobující problémy s výkonem za následujících podmínek:

  - Moc velký počet spuštěných dotazů
  - Příliš mnoho kompilaci dotazů
  - Jeden nebo více provádění dotazů používá plán neoptimálním průběhem dotazu

  Pokud tomu tak pro vaše úlohy, je vaším cílem je identifikovat a vyladit přidružené dotazy nebo upgradovat výpočetního prostředí nebo zvyšovat kapacitu databáze Azure SQL a chránit před požadavky na procesor úroveň služby. Další informace o škálování prostředků pro izolované databáze, najdete v části [škálování izolované databáze prostředků ve službě Azure SQL Database](sql-database-single-database-scale.md) a škálování prostředků pro elastické fondy, najdete v části [škálování elastického fondu prostředků ve službě Azure SQL Databáze](sql-database-elastic-pool-scale.md).

- **Konkrétní dotaz čeká na něco**

  Jednotlivé dotazy mohou mít problémy s výkonem z důvodu čekání na něco dotaz. Vaším cílem je v tomto scénáři, odebrat nebo snižte dobu čekání.

### <a name="determine-if-you-have-a-running-related-performance-issue"></a>Zjistit, jestli je problém s výkonem souvisejících s spuštění

Můžete identifikovat problémy s výkonem souvisejících s běžící několika způsoby. Nejběžnější metody jsou následující:

- Použití [webu Azure portal](#monitor-databases-using-the-azure-portal) k monitorování procenta využití procesoru.
- Pomocí následujících [zobrazení dynamické správy](sql-database-monitoring-with-dmvs.md):

  - [Sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) vrátí spotřeby procesoru, vstupně-výstupní operace a paměti pro databázi Azure SQL Database. Jeden řádek existuje pro každých 15 sekund, i v případě, že neexistuje žádná aktivita v databázi. Historická data se udržují za jednu hodinu.
  - [Sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) vrátí data o využití a úložiště CPU pro službu Azure SQL Database. Data se shromažďují a agregují v pětiminutovém intervalu.

> [!TIP]
> V rámci obecných pokynů Pokud vaše využití procesoru je trvale dosahovalo nebo přesahovalo 80 %, je nutné problém s výkonem souvisejících s spuštěná.

### <a name="determine-if-you-have-a-waiting-related-performance-issue"></a>Zjistit, jestli je problém s výkonem souvisejících s čekání

Nejprve si být jisti, že to není problém vysoké využití procesoru a spuštění související s výkonem. Pokud není, dalším krokem je identifikace nejvyšší čeká přidružené úlohy vaší aplikace.  Běžné metody pro zobrazení na začátek čekání kategorií typů:

- [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) poskytuje statistiky čekání každý dotaz v čase. Na Query Store čekání typy jsou sloučeny do kategorií čekání. Je k dispozici v mapování kategorií wait čekat typy [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql.md#wait-categories-mapping-table).
- [Sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) vrátí informace o všech čeká tak vlákna, která provedených během operace. Toto souhrnné zobrazení můžete použít k diagnostice problémů s výkonem s Azure SQL Database a také s konkrétní dotazy a dávek.
- [Sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) vrátí informace o frontě čekání úlohy, které čekají na některých prostředků.

Jak je znázorněno v předchozí tabulce, jsou nejčastěji používané čeká:

- Zámky (blokování)
- VSTUPNĚ-VÝSTUPNÍCH OPERACÍ
- související s databází TempDB kolizí
- Paměť udělení čeká

Každá kategorie čekání v závislosti na tom, co se zobrazí, má různé cesty poradce při potížích.

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

- Procento CPU
- Procento DTU
- Procento datových V/V
- Procento velikosti databáze

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
