---
title: Sledování a ladění výkonu
description: Přehled možností a metodologie optimalizace monitorování a výkonu v Azure SQL Database a spravované instanci Azure SQL.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, sstein
ms.date: 09/30/2020
ms.openlocfilehash: 6c8d048d43a16191cc7b1245ad2d686ba2ca22ab
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91596971"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database-and-azure-sql-managed-instance"></a>Monitorování a ladění výkonu ve službách Azure SQL Database a Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Pokud chcete monitorovat výkon databáze v Azure SQL Database a spravované instanci Azure SQL, Začněte monitorováním prostředků CPU a IO používaných úlohami relativně k úrovni výkonu databáze, kterou jste zvolili při výběru konkrétní úrovně služby a úrovně výkonu. K tomuto účelu Azure SQL Database a Azure SQL Managed instance generuje metriky prostředků, které se dají zobrazit v Azure Portal nebo pomocí některého z těchto nástrojů pro správu SQL Server: [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) nebo [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

Azure SQL Database poskytuje řadu poradců databází, které poskytují doporučení inteligentního ladění výkonu a možnosti automatického ladění pro zlepšení výkonu. Kromě toho Query Performance Insight zobrazí podrobnosti o dotazech zodpovědných za největší využití procesoru a vstupně-výstupních operací pro databáze s jednou a ve fondu.

Azure SQL Database a Azure SQL Managed instance poskytují pokročilé možnosti monitorování a ladění, které jsou zajištěné umělou logikou, což vám pomůže při řešení potíží a maximalizaci výkonu databází a řešení. Můžete zvolit konfiguraci [exportu](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) těchto [Intelligent Insights](intelligent-insights-overview.md) a dalších protokolů prostředků databáze a metriky do jednoho z několika míst pro účely využití a analýzy, zejména pomocí [SQL Analytics](../../azure-monitor/insights/azure-sql.md). Azure SQL Analytics je pokročilé řešení monitorování cloudu pro monitorování výkonu všech databází ve velkém měřítku a napříč několika předplatnými v jednom zobrazení. Seznam protokolů a metrik, které můžete exportovat, najdete v tématu [diagnostická telemetrie pro export](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export) .

SQL Server má své vlastní monitorovací a diagnostické možnosti, které SQL Database a využití spravované instance SQL, jako je například [úložiště dotazů](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) a [zobrazení dynamické správy (zobrazení dynamické správy)](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views). Pokud chcete sledovat nejrůznější problémy s výkonem, podívejte se na téma [monitorování pomocí zobrazení dynamické správy](monitoring-with-dmvs.md) .

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Monitorování a ladění schopností Azure Portal

V Azure Portal Azure SQL Database a Azure SQL Managed instance poskytují monitorování metrik prostředků. Azure SQL Database poskytuje Poradce pro databáze a Query Performance Insight poskytuje doporučení pro ladění dotazů a analýzu výkonu dotazů. V Azure Portal můžete povolit automatické ladění pro [logické servery SQL](logical-servers.md) a jejich jednotlivé a sdružené databáze.

> [!NOTE]
> Databáze s extrémně nízkým využitím se můžou zobrazovat na portálu s méně než skutečným využitím. Z důvodu způsobu, jakým se telemetrie vyvolá při převodu hodnoty Double na nejbližší celé 0,5 číslo, je zaokrouhleno na 0, což způsobí ztrátu v členitosti vyvolané telemetrie. Podrobnosti najdete v tématu [Zaokrouhlení metriky databáze a elastického fondu na hodnotu nula](#low-database-and-elastic-pool-metrics-rounding-to-zero).

### <a name="azure-sql-database-and-azure-sql-managed-instance-resource-monitoring"></a>Azure SQL Database a monitorování prostředků spravované instance Azure SQL

V zobrazení **metriky** můžete rychle monitorovat různé metriky prostředků v Azure Portal. Tyto metriky umožňují zjistit, jestli databáze dosáhla 100% prostředků procesoru, paměti nebo vstupně-výstupních prostředků. Vysoká hodnota DTU nebo procento procesoru a vysoké vstupně-výstupní operace označují, že vaše úlohy můžou potřebovat více prostředků procesoru nebo vstupně-výstupních prostředků. Může také označovat dotazy, které je třeba optimalizovat.

  ![Metriky prostředků](./media/monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors-in-azure-sql-database"></a>Databázové Poradce pro databáze v Azure SQL Database

Azure SQL Database zahrnuje služby pro [databáze](database-advisor-implement-performance-recommendations.md) , které poskytují doporučení pro ladění výkonu pro databáze s jednou a ve fondu. Tato doporučení jsou k dispozici v Azure Portal a také pomocí [prostředí PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseadvisor). Můžete také povolit [Automatické ladění](automatic-tuning-overview.md) , aby Azure SQL Database mohl automaticky implementovat tato doporučení pro ladění.

### <a name="query-performance-insight-in-azure-sql-database"></a>Query Performance Insight v Azure SQL Database

[Query Performance Insight](query-performance-insight-use.md) zobrazuje výkon v Azure Portal nejdůležitějších a nejdelších spuštěných dotazů pro databáze s jednou a ve fondu.

### <a name="low-database-and-elastic-pool-metrics-rounding-to-zero"></a>Zaokrouhlení metriky databáze a elastického fondu na nulu

Od září 2020 se databáze s extrémně nízkým využitím můžou na portálu zobrazit s méně než skutečným využitím. Z důvodu způsobu, jakým se vyvolá telemetrie při převodu hodnoty Double na nejbližší celé číslo, jsou hodnoty využití menší než 0,5 zaokrouhleny na 0, což způsobí ztrátu v členitosti emitované telemetrie.

Například: Vezměte v úvahu jedno okno s těmito čtyřmi datovými body: 0,1, 0,1, 0,1, 0,1, tyto hodnoty jsou zaokrouhleny dolů na 0, 0, 0, 0 a v průměru 0. Pokud je některý z datových bodů větší než 0,5, například: 0,1, 0,1, 0,9, 0,1, zaokrouhlí se na 0, 0, 1, 0 a zobrazí se průměrně 0,25.

Ovlivněné metriky databáze:
- cpu_percent
- log_write_percent
- workers_percent
- sessions_percent
- physical_data_read_percent
- dtu_consumption_percent2
- xtp_storage_percent

Ovlivněné metriky elastického fondu:
- cpu_percent
- physical_data_read_percent
- log_write_percent
- memory_usage_percent
- data_storage_percent
- peak_worker_percent
- peak_session_percent
- xtp_storage_percent
- allocated_data_storage_percent


## <a name="generate-intelligent-assessments-of-performance-issues"></a>Generování inteligentního posouzení problémů s výkonem

[Intelligent Insights](intelligent-insights-overview.md) pro Azure SQL Database a Azure SQL Managed instance používá integrované inteligentní funkce k nepřetržitému monitorování využití databáze prostřednictvím umělých inteligentních funkcí a detekci rušivých událostí, které způsobují špatný výkon. Intelligent Insights automaticky detekuje problémy s výkonem s databázemi na základě doby čekání na zpracování dotazu, chyb nebo časových limitů. Po zjištění se provede podrobná analýza, která generuje protokol prostředků (nazývaný SQLInsights) s [inteligentním posouzením problémů](intelligent-insights-troubleshoot-performance.md). Toto posouzení se skládá z analýzy hlavní příčiny problémů s výkonem databáze a tam, kde je to možné, doporučení pro zlepšení výkonu.

Intelligent Insights je jedinečná funkce integrovaných inteligentních funkcí Azure, která poskytuje následující hodnotu:

- Proaktivní monitorování
- Přehledy výkonu s upraveným výkonem
- Předčasné zjištění snížení výkonu databáze
- Zjistila se analýza problémů hlavní příčiny.
- Doporučení pro zlepšení výkonu
- Možnosti horizontálního navýšení kapacity na stovkách tisíc databází
- Kladný dopad na prostředky DevOps a celkové náklady na vlastnictví

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>Povolení exportu metrik a protokolů prostředků streamování

Můžete povolit a nakonfigurovat [Export diagnostické telemetrie](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) do jednoho z několika míst, včetně protokolu prostředků Intelligent Insights. Pomocí [analýzy SQL](../../azure-monitor/insights/azure-sql.md) a dalších možností můžete využít tuto další diagnostickou telemetrii k identifikaci a řešení problémů s výkonem.

Nastavení diagnostiky nakonfigurujete pro streamování kategorií metrik a protokolů zdrojů pro izolované databáze, databáze ve fondu, elastické fondy, spravované instance a databáze instancí na jeden z následujících prostředků Azure.

### <a name="log-analytics-workspace-in-azure-monitor"></a>Pracovní prostor Log Analytics v Azure Monitor

Metriky a protokoly prostředků můžete streamovat do [Log Analytics pracovního prostoru v Azure monitor](../../azure-monitor/platform/resource-logs-collect-workspace.md). Data streamovaná tady můžou využívat [SQL Analytics](../../azure-monitor/insights/azure-sql.md), což je jenom cloudové řešení monitorování, které poskytuje inteligentní monitorování vašich databází, které obsahuje sestavy výkonu, výstrahy a doporučení pro zmírnění rizik. Data streamovaná do pracovního prostoru Log Analytics lze analyzovat pomocí dalších shromažďovaných dat monitorování a také vám umožní využít jiné funkce Azure Monitor, jako jsou výstrahy a vizualizace.

### <a name="azure-event-hubs"></a>Azure Event Hubs

Do [Azure Event Hubs](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)můžete streamovat metriky a protokoly prostředků. Streamování diagnostiky diagnostiky do centra událostí, které poskytuje následující funkce:

- **Streamování protokolů na systémy protokolování a telemetrie třetích stran**

  Streamujte všechny metriky a protokoly prostředků do jednoho centra událostí, abyste data protokolu kanálu SIEM nebo nástroj Log Analytics od jiného výrobce.
- **Sestavení vlastní telemetrie a protokolovací platformy**

  Vysoce škálovatelná možnost publikování a odběru centra událostí umožňuje flexibilní přijímání metrik a protokolů prostředků do vlastní platformy telemetrie. Podrobnosti najdete v tématu [navrhování a změna velikosti platformy telemetrie s globálním škálováním na platformě Azure Event Hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) .
- **Zobrazení stavu služby podle streamování dat do Power BI**

  Pomocí Event Hubs, Stream Analytics a Power BI můžete transformovat diagnostická data na vaše služby Azure téměř v reálném čase. Podrobnosti o tomto řešení najdete v tématu [Stream Analytics a Power BI: řídicí panel analýzy v reálném čase pro streamovaná data](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard) .

### <a name="azure-storage"></a>Azure Storage

[Azure Storage](../../azure-monitor/platform/resource-logs-collect-storage.md)metriky proudu a protokoly prostředků. Využijte Azure Storage k archivaci obrovského množství diagnostické telemetrie za zlomek nákladů na předchozí dvě možnosti streamování.

## <a name="use-extended-events"></a>Použití rozšířených událostí 

Kromě toho můžete v SQL Server použít [Rozšířené události](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) pro pokročilé monitorování a řešení potíží. Architektura rozšířených událostí umožňuje uživatelům shromažďovat co nejvíc nebo jen malá data, která jsou nutná k řešení potíží nebo určení problému s výkonem. Informace o použití rozšířených událostí v Azure SQL Database najdete v tématu [Rozšířené události v Azure SQL Database](xevent-db-diff-from-svr.md).

## <a name="next-steps"></a>Další kroky

- Další informace o inteligentních doporučeních výkonu pro jednotlivé databáze a databáze ve fondu najdete v tématu [doporučení k výkonu služby Database Advisor](database-advisor-implement-performance-recommendations.md).
- Další informace o automatickém monitorování výkonu databáze pomocí automatizované diagnostiky a analýzy hlavních příčin problémů s výkonem najdete v tématu [Azure SQL Intelligent Insights](intelligent-insights-overview.md).
