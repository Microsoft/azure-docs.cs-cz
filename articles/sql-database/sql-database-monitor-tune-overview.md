---
title: Sledování a ladění výkonu
description: Přehled možností a metodologie ladění a sledování výkonu v Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 837d88665c1fdffe902c9c478e5d6dc65a2e402a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79268727"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database"></a>Sledování a ladění výkonu v Azure SQL Database

Pokud chcete monitorovat výkon databáze v Azure SQL Database, Začněte monitorováním prostředků procesoru a v/v, které vaše zatížení používá vzhledem k úrovni výkonu databáze, kterou jste zvolili při výběru konkrétní úrovně služby a úrovně výkonu. K tomu Azure SQL Database emituje metriky prostředků, které se dají zobrazit v Azure Portal nebo pomocí některého z těchto nástrojů pro správu SQL: [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) nebo [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

Pro databáze typu Single a Pool Azure SQL Database poskytuje řadu poradců databází, které poskytují doporučení k inteligentnímu ladění výkonu a možnosti automatického ladění pro zlepšení výkonu. Kromě toho Query Performance Insight zobrazí podrobnosti o dotazech zodpovědných za největší využití procesoru a vstupně-výstupních operací pro databáze s jednou a ve fondu.

Azure SQL Database poskytuje další možnosti monitorování a ladění, které jsou zajištěné umělou logikou, což vám pomůže při řešení potíží a maximalizaci výkonu databází a řešení. Můžete zvolit konfiguraci [exportu](sql-database-metrics-diag-logging.md) těchto [Intelligent Insights](sql-database-intelligent-insights.md) a dalších protokolů prostředků SQL Database a metriky do jednoho z několika cílů pro účely využití a analýzy, zejména pomocí [SQL Analytics](../azure-monitor/insights/azure-sql.md)). Azure SQL Analytics je pokročilé řešení monitorování cloudu pro monitorování výkonu všech databází SQL Azure ve velkém měřítku a napříč několika předplatnými v jednom zobrazení. Seznam protokolů a metrik, které můžete exportovat, najdete v tématu [diagnostická telemetrie pro export](sql-database-metrics-diag-logging.md#diagnostic-telemetry-for-export-for-azure-sql-database) .

SQL má také vlastní monitorování a diagnostické funkce s [SQL serverm úložiště dotazů](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) a [zobrazeními dynamické správy (zobrazení dynamické správy)](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views). Pokud chcete sledovat nejrůznější problémy s výkonem, podívejte se na téma [monitorování pomocí zobrazení dynamické správy](sql-database-monitoring-with-dmvs.md) .

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Monitorování a ladění schopností Azure Portal

V Azure Portal Azure SQL Database poskytovat monitorování metrik prostředků pro všechny typy nasazení. Další pro databáze s jednou a fondem, poradci databáze a Query Performance Insight poskytují doporučení k vyladění dotazů a analýzu výkonu dotazů. Nakonec můžete v Azure Portal povolit automatické vytváření logických serverů a jejich jednotlivých a sdružených databází.

### <a name="sql-database-resource-monitoring"></a>Monitorování prostředků SQL Database

V Azure Portal v zobrazení **metrik** můžete rychle monitorovat následující metriky prostředků:

- **Využití DTU**

  Zkontrolujte, jestli databáze nebo elastický fond dosahuje 100% využití DTU po delší dobu. Použití vysoké DTU indikuje, že vaše úlohy můžou potřebovat více prostředků procesoru nebo v/v. Může také označovat dotazy, které je třeba optimalizovat.
- **Využití procesoru**

  Zkontrolujte, jestli databáze, elastický fond nebo spravovaná instance dosáhly po delší dobu 100% využití procesoru. Vysoký procesor indikuje, že vaše úlohy můžou potřebovat více prostředků procesoru nebo v/v. Může také označovat dotazy, které je třeba optimalizovat.
- **Použití v/v**

  Zkontrolujte, jestli databáze, elastický fond nebo instance spravované instance dosáhnou omezení vstupně-výstupních operací základního úložiště. Vysoké použití v/v znamená, že vaše úlohy můžou potřebovat více prostředků procesoru nebo vstupně-výstupních prostředků. Může také označovat dotazy, které je třeba optimalizovat.

  ![Metriky prostředků](./media/sql-database-monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors"></a>Databázové poradce
' Azure SQL Database zahrnuje [databázové poradce](sql-database-advisor.md) , které poskytují doporučení pro ladění výkonu pro databáze s jednou a ve fondu. Tato doporučení jsou k dispozici v Azure Portal a také pomocí [prostředí PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseadvisor). Můžete také povolit [Automatické ladění](sql-database-automatic-tuning.md) , aby SQL Database mohl automaticky implementovat tato doporučení pro ladění.

### <a name="query-performance-insight"></a>Query Performance Insight

[Query Performance Insight](sql-database-query-performance.md) zobrazuje výkon v Azure Portal nejdůležitějších a nejdelších spuštěných dotazů pro databáze s jednou a ve fondu.

## <a name="generate-intelligent-assessments-of-performance-issues"></a>Generování inteligentního posouzení problémů s výkonem

Azure SQL Database [Intelligent Insights](sql-database-intelligent-insights.md) pomocí integrovaných inteligentních funkcí průběžně monitoruje využití databáze prostřednictvím umělých inteligentních funkcí a detekuje rušivé události, které způsobují špatný výkon. Intelligent Insights automaticky detekuje problémy s výkonem u databází v Azure SQL Database na základě doby čekání na zpracování dotazu, chyb nebo časových limitů. Po zjištění se provede podrobná analýza, která generuje protokol prostředků (nazývaný SQLInsights) s [inteligentním posouzením problémů](sql-database-intelligent-insights-troubleshoot-performance.md). Toto posouzení se skládá z analýzy hlavní příčiny problémů s výkonem databáze a tam, kde je to možné, doporučení pro zlepšení výkonu.

Intelligent Insights je jedinečná funkce integrovaných inteligentních funkcí Azure, která poskytuje následující hodnotu:

- Proaktivní monitorování
- Přehledy výkonu s upraveným výkonem
- Předčasné zjištění snížení výkonu databáze
- Zjistila se analýza problémů hlavní příčiny.
- Doporučení pro zlepšení výkonu
- Možnosti horizontálního navýšení kapacity na stovkách tisíc databází
- Kladný dopad na prostředky DevOps a celkové náklady na vlastnictví

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>Povolení exportu metrik a protokolů prostředků streamování

Můžete povolit a nakonfigurovat [Export diagnostické telemetrie](sql-database-metrics-diag-logging.md) do jednoho z několika míst, včetně protokolu prostředků Intelligent Insights. Pomocí [analýzy SQL](../azure-monitor/insights/azure-sql.md) a dalších možností můžete využít tuto další diagnostickou telemetrii k identifikaci a řešení problémů s výkonem.

Nastavení diagnostiky nakonfigurujete pro streamování kategorií metrik a protokolů zdrojů pro izolované databáze, databáze ve fondu, elastické fondy, spravované instance a databáze instancí na jeden z následujících prostředků Azure.

### <a name="log-analytics-workspace-in-azure-monitor"></a>Pracovní prostor Log Analytics ve službě Azure monitor

Metriky a protokoly prostředků můžete streamovat do [Log Analytics pracovního prostoru v Azure monitor](../azure-monitor/platform/resource-logs-collect-workspace.md). Data streamovaná tady můžou využívat [SQL Analytics](../azure-monitor/insights/azure-sql.md), což je jenom cloudové řešení monitorování, které poskytuje inteligentní monitorování vašich databází, které obsahuje sestavy výkonu, výstrahy a doporučení pro zmírnění rizik. Data streamovaná do pracovního prostoru Log Analytics lze analyzovat pomocí dalších shromažďovaných dat monitorování a také vám umožní využít jiné funkce Azure Monitor, jako jsou výstrahy a vizualizace.

### <a name="azure-event-hubs"></a>Azure Event Hubs

Do [Azure Event Hubs](../azure-monitor/platform/resource-logs-stream-event-hubs.md)můžete streamovat metriky a protokoly prostředků. Streamování diagnostiky diagnostiky do centra událostí, které poskytuje následující funkce:

- **Streamování protokolů na systémy protokolování a telemetrie třetích stran**

  Streamujte všechny metriky a protokoly prostředků do jednoho centra událostí, abyste data protokolu kanálu SIEM nebo nástroj Log Analytics od jiného výrobce.
- **Sestavení vlastní telemetrie a protokolovací platformy**

  Vysoce škálovatelná možnost publikování a odběru centra událostí umožňuje flexibilní přijímání metrik a protokolů prostředků do vlastní platformy telemetrie. Podrobnosti najdete v tématu [navrhování a změna velikosti platformy telemetrie s globálním škálováním na platformě Azure Event Hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) .
- **Zobrazení stavu služby podle streamování dat do Power BI**

  Pomocí Event Hubs, Stream Analytics a Power BI můžete transformovat diagnostická data na vaše služby Azure téměř v reálném čase. Podrobnosti o tomto řešení najdete v tématu [Stream Analytics a Power BI: řídicí panel analýzy v reálném čase pro streamovaná data](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard) .

### <a name="azure-storage"></a>Azure Storage

[Azure Storage](../azure-monitor/platform/resource-logs-collect-storage.md)metriky proudu a protokoly prostředků. Využijte Azure Storage k archivaci obrovského množství diagnostické telemetrie za zlomek nákladů na předchozí dvě možnosti streamování.

## <a name="use-extended-events-in-the-sql-database-engine"></a>Použití rozšířených událostí v modulu SQL Database Engine

Kromě toho můžete v SQL použít [Rozšířené události](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) pro další pokročilé monitorování a řešení potíží. Architektura rozšířených událostí umožňuje uživatelům shromažďovat co nejvíc nebo jen malá data, která jsou nutná k řešení potíží nebo určení problému s výkonem. Informace o použití rozšířených událostí v SQL Database najdete v tématu [Rozšířené události v SQL Database](sql-database-xevent-db-diff-from-svr.md).

## <a name="next-steps"></a>Další kroky

- Další informace o inteligentních doporučeních výkonu pro jednotlivé databáze a databáze ve fondu najdete v tématu [doporučení k výkonu služby Database Advisor](sql-database-advisor.md).
- Další informace o automatickém monitorování výkonu databáze pomocí automatizované diagnostiky a analýzy hlavních příčin problémů s výkonem najdete v tématu [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md).
'''''''''