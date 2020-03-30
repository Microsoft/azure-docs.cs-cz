---
title: Sledování a ladění výkonu
description: Přehled možností monitorování a optimalizace výkonu a metodiky v Azure SQL Database.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268727"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database"></a>Monitorování a optimalizace výkonu v Azure SQL Database

Chcete-li sledovat výkon databáze v Azure SQL Database, začněte sledováním prostředků procesoru a vstupně-iO používaných vaší úlohou vzhledem k úrovni výkonu databáze, kterou jste zvolili při výběru konkrétní úrovně služby a úrovně výkonu. K dosažení tohoto cíle Azure SQL Database vyzařuje metriky prostředků, které lze zobrazit na webu Azure Portal nebo pomocí jednoho z těchto nástrojů pro správu SQL: [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) nebo SQL Server Management [Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

Pro jednu a sdruženou databázi Poskytuje Azure SQL Database řadu databázových poradců, kteří poskytují inteligentní doporučení pro optimalizaci výkonu a možnosti automatického ladění pro zlepšení výkonu. Kromě toho přehled výkonu dotazů zobrazuje podrobnosti o dotazech, které jsou zodpovědné za většinu využití procesoru a vi pro jednu a sdruženou databázi.

Azure SQL Database poskytuje další funkce monitorování a ladění podporované umělou inteligencí, které vám pomohou při řešení potíží a maximalizaci výkonu databází a řešení. Můžete nakonfigurovat [export datových proudů](sql-database-metrics-diag-logging.md) těchto [inteligentních přehledů](sql-database-intelligent-insights.md) a dalších protokolů prostředků databáze SQL a metrik do jednoho z několika cílů pro spotřebu a analýzu, zejména pomocí [služby SQL Analytics](../azure-monitor/insights/azure-sql.md)). Azure SQL Analytics je pokročilé cloudové monitorování řešení pro sledování výkonu všech databází Azure SQL ve velkém měřítku a napříč několika předplatnými v jednom zobrazení. Seznam protokolů a metrik, které můžete exportovat, naleznete [v tématu diagnostická telemetrie pro export](sql-database-metrics-diag-logging.md#diagnostic-telemetry-for-export-for-azure-sql-database)

Nakonec SQL má své vlastní možnosti monitorování a diagnostiky s [SQL Server Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) a zobrazení dynamické správy [(DMVs)](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views). Viz [Monitorování pomocí dmvs](sql-database-monitoring-with-dmvs.md) skripty pro sledování různých problémů s výkonem.

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Možnosti monitorování a ladění na webu Azure Portal

Na webu Azure Portal poskytuje Azure SQL Database monitorování metrik prostředků pro všechny typy nasazení. Další, pro jednu a sdružené databáze, databázové poradci a přehled výkonu dotazu poskytují doporučení pro optimalizaci dotazů a analýzu výkonu dotazů. Nakonec na webu Azure Portal můžete povolit automatické pro logické servery a jejich jediné a sdružené databáze.

### <a name="sql-database-resource-monitoring"></a>Monitorování prostředků databáze SQL

V zobrazení **Metriky** můžete rychle sledovat následující metriky prostředků na webu Azure Portal:

- **Využití DTU**

  Zkontrolujte, zda databáze nebo elastický fond dosahuje 100 procent využití DTU po delší dobu. Vysoké využití DTU označuje, že vaše úloha může potřebovat více prostředků procesoru nebo iO. Může také znamenat dotazy, které je třeba optimalizovat.
- **Využití procesoru**

  Zkontrolujte, zda databáze, elastický fond nebo spravovaná instance dosahuje 100 procent využití procesoru po delší dobu. Vysoká procesor znamená, že vaše úloha může potřebovat více prostředků procesoru nebo vi. Může také znamenat dotazy, které je třeba optimalizovat.
- **Využití vi**

  Zkontrolujte, jestli databáze, elastický fond nebo instance správy dosahuje limitů vstupně-souhlasím v základním úložišti. Vysoké využití vi označuje, že vaše úloha může potřebovat více prostředků procesoru nebo vi. Může také znamenat dotazy, které je třeba optimalizovat.

  ![Metriky zdrojů](./media/sql-database-monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors"></a>Databázoví poradci
' Azure SQL Database obsahuje [databázové poradce,](sql-database-advisor.md) kteří poskytují doporučení pro optimalizaci výkonu pro jednu a sdruženou databázi. Tato doporučení jsou dostupná na webu Azure Portal i pomocí [PowerShellu](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseadvisor). Můžete také povolit [automatické ladění](sql-database-automatic-tuning.md) tak, aby databáze SQL můžete automaticky implementovat tato doporučení optimalizace.

### <a name="query-performance-insight"></a>Query Performance Insight

[Přehled výkonu dotazů](sql-database-query-performance.md) zobrazuje výkon na portálu Azure s nejvyšší spotřebou a nejdelší spuštění dotazů pro jednu a sdruženou databázi.

## <a name="generate-intelligent-assessments-of-performance-issues"></a>Generování inteligentních hodnocení problémů s výkonem

Azure SQL Database [Intelligent Insights](sql-database-intelligent-insights.md) využívá integrovanou inteligenci k průběžnému sledování využití databáze prostřednictvím umělé inteligence a zjišťování rušivých událostí, které způsobují nízký výkon. Inteligentní přehledy automaticky zjišťuje problémy s výkonem databází v Azure SQL Database na základě čekacích dob, chyb nebo časových limitů. Po zjištění se provede podrobná analýza, která generuje protokol prostředků (nazývaný SQLInsights) s [inteligentním vyhodnocením problémů](sql-database-intelligent-insights-troubleshoot-performance.md). Toto posouzení se skládá z analýzy hlavní příčiny problému s výkonem databáze a pokud je to možné, doporučení pro zlepšení výkonu.

Intelligent Insights je jedinečná funkce integrované inteligence Azure, která poskytuje následující hodnotu:

- Proaktivní monitorování
- Přehledy výkonu na míru
- Včasná detekce snížení výkonu databáze
- Analýza hlavní příčiny zjištěných problémů
- Doporučení ke zlepšení výkonu
- Škálování možností na stovkách tisíc databází
- Pozitivní dopad na zdroje DevOps a celkové náklady na vlastnictví

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>Povolení streamování exportu metrik a protokolů prostředků

Můžete povolit a nakonfigurovat [export datových proudů diagnostické telemetrie](sql-database-metrics-diag-logging.md) do jednoho z několika cílů, včetně protokolu prostředků Intelligent Insights. Pomocí [služby SQL Analytics](../azure-monitor/insights/azure-sql.md) a dalších funkcí využijte tuto další diagnostickou telemetrii k identifikaci a řešení problémů s výkonem.

Nastavení diagnostiky nakonfigurujete pro streamování kategorií metrik a protokolů prostředků pro jednotlivé databáze, sdružené databáze, elastické fondy, spravované instance a databáze instancí do jednoho z následujících prostředků Azure.

### <a name="log-analytics-workspace-in-azure-monitor"></a>Pracovní prostor Log Analytics v Azure monitoru

Metriky a protokoly prostředků můžete streamovat do [pracovního prostoru Log Analytics v Azure Monitoru](../azure-monitor/platform/resource-logs-collect-workspace.md). Data streamovaná zde může být spotřebována [službou SQL Analytics](../azure-monitor/insights/azure-sql.md), což je cloudové řešení, které poskytuje inteligentní monitorování vašich databází, které zahrnuje sestavy výkonu, výstrahy a doporučení ke zmírnění rizik. Data streamovaná do pracovního prostoru Log Analytics lze analyzovat pomocí dalších shromážděných dat monitorování a také umožňuje využívat další funkce Azure Monitoru, jako jsou výstrahy a vizualizace.

### <a name="azure-event-hubs"></a>Azure Event Hubs

Metriky a protokoly prostředků můžete streamovat do [Centra událostí Azure](../azure-monitor/platform/resource-logs-stream-event-hubs.md). Streamování diagnostické telemetrie do rozbočovačů událostí, které poskytují následující funkce:

- **Streamovat protokoly do protokolovacích a telemetrických systémů třetích stran**

  Streamujte všechny metriky a protokoly prostředků do jednoho centra událostí a můžete data protokolu do kanálu do nástroje siem nebo analýzy protokolů třetí strany.
- **Vytvoření vlastní platformy pro telemetrii a protokolování**

  Vysoce škálovatelné publikování a odběru povahy centra událostí umožňuje flexibilně ingestovat metriky a protokoly prostředků do vlastní telemetrické platformy. Podrobnosti najdete [v tématu Navrhování a dimenzování globální platformy telemetrie v azure event hubech.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)
- **Zobrazení stavu služby datovým proudem dat do Power BI**

  Pomocí centra událostí, Stream Analytics a Power BI můžete svá diagnostická data přeměnit na přehledy služeb Azure téměř v reálném čase. Podrobnosti o tomto řešení najdete v [tématech Stream Analytics a Power BI: Řídicí panel analýzy v reálném čase pro streamování dat.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard)

### <a name="azure-storage"></a>Azure Storage

Streamujte metriky a protokoly prostředků do [Azure Storage](../azure-monitor/platform/resource-logs-collect-storage.md). Pomocí úložiště Azure archivujte obrovské množství diagnostické telemetrie za zlomek nákladů na předchozí dvě možnosti streamování.

## <a name="use-extended-events-in-the-sql-database-engine"></a>Použití rozšířených událostí v databázovém stroji SQL

Kromě toho můžete použít [rozšířené události](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) v SQL pro další pokročilé monitorování a řešení potíží. Architektura rozšířené události umožňuje uživatelům shromažďovat tolik nebo tolik dat, kolik je nezbytné k řešení nebo identifikaci problému s výkonem. Informace o použití rozšířených událostí v databázi SQL naleznete [v tématu Rozšířené události v databázi SQL](sql-database-xevent-db-diff-from-svr.md).

## <a name="next-steps"></a>Další kroky

- Další informace o inteligentních doporučeních výkonu pro jednu a sdružené databáze naleznete v [tématu Doporučení pro výkon poradce pro databázi](sql-database-advisor.md).
- Další informace o automatickém sledování výkonu databáze pomocí automatické diagnostiky a analýzy hlavních příčin problémů s výkonem naleznete v [tématu Azure SQL Intelligent Insights](sql-database-intelligent-insights.md).
'''''''''