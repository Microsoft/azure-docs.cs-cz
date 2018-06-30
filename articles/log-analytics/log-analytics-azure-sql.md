---
title: Řešení Azure SQL analýzy v Log Analytics | Microsoft Docs
description: Azure SQL Analytics řešení vám usnadní správu vaše databáze Azure SQL
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/03/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: f57a47677f752a644975a25fa746d78bced5d766
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37132867"
---
# <a name="monitor-azure-sql-databases-using-azure-sql-analytics-preview"></a>Monitorování databáze Azure SQL pomocí Analytics SQL Azure (Preview)

![Azure SQL Analytics symbol](./media/log-analytics-azure-sql/azure-sql-symbol.png)

Azure SQL Analytics je Cloudová řešení pro sledování výkonu databází SQL Azure se Škálováním na více elastické fondy a odběry monitorování. Shromažďuje a vizualizuje důležité metriky výkonu databáze SQL Azure s vestavěné inteligentní pro řešení potíží s nahoře výkonu. 

Metriky, které shromáždíte pomocí s řešením, můžete vytvořit vlastní pravidla monitorování a výstrahy. Toto řešení umožňuje identifikovat problémy v každé vrstvě zásobníku vaší aplikace. Pomocí diagnostiky Azure metriky společně s analýzy protokolů zobrazení prezentují data o databází Azure SQL a elastické fondy v jedné pracovní prostor analýzy protokolů. Analýzy protokolů umožňuje shromažďovat, korelaci a vizualizovat strukturovaná i nestrukturovaná data.

Toto řešení preview v současné době podporuje až 150 000 databází SQL Azure a 5 000 SQL elastické fondy podle pracovního prostoru.

Praktické přehled o používání řešení analýzy SQL Azure a příklady typických scénářů použití naleznete v tématu vložené video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

## <a name="connected-sources"></a>Připojené zdroje

Azure SQL Analytics je v cloudu, monitorování, řešení podpůrné vysílání datového proudu telemetrických dat diagnostiky pro databáze SQL Azure a elastické fondy. Jak se připojit ke službě Analýza protokolů nepoužívá agentů, řešení nepodporuje připojení s Windows, Linux nebo SCOM prostředky, viz následující tabulka kompatibility.

| Připojený zdroj | Podpora | Popis |
| --- | --- | --- |
| **[Azure Diagnostics](log-analytics-azure-storage.md)** | **Ano** | Metrika a protokolu dat Azure jsou odesílány Log Analytics přímo v Azure. |
| [Účet služby Azure Storage](log-analytics-azure-storage.md) | Ne | Analýzy protokolů číst data z účtu úložiště. |
| [Agenti systému Windows](log-analytics-windows-agent.md) | Ne | Přímé agentů v systému Windows nejsou používány nástrojem řešení. |
| [Agenti systému Linux](log-analytics-linux-agents.md) | Ne | Přímé agenty Linux nejsou používány nástrojem řešení. |
| [Skupiny správy nástroje SCOM](log-analytics-om-agents.md) | Ne | Přímé připojení z agenta nástroje SCOM k analýze protokolů nepoužívá řešení. |

## <a name="configuration"></a>Konfigurace

Proveďte následující postup pro přidání řešení analýzy SQL Azure do pracovního prostoru.

1. Přidat řešení analýzy SQL Azure do pracovního prostoru z [Azure marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview).
2. Na portálu Azure klikněte na tlačítko **+ vytvořit prostředek**, vyhledejte **Azure SQL Analytics**.  
    ![Monitorování a správa](./media/log-analytics-azure-sql/monitoring-management.png)
3. Vyberte **analýzy SQL Azure (Preview)** ze seznamu
4. V **analýzy SQL Azure (Preview)** oblast, klikněte na tlačítko **vytvořit**.  
    ![Vytvoření](./media/log-analytics-azure-sql/portal-create.png)
5. V **vytvořte nové řešení** oblasti, vytvořit nový, nebo vyberte existující pracovní prostor, který chcete přidat řešení a potom klikněte na **vytvořit**.  
    ![Přidat do pracovního prostoru](./media/log-analytics-azure-sql/add-to-workspace.png)

### <a name="configure-azure-sql-databases-and-elastic-pools-to-stream-diagnostics-telemetry"></a>Konfigurovat databáze SQL Azure a elastické fondy na datový proud telemetrie diagnostiky

Po vytvoření řešení analýzy SQL Azure v pracovním prostoru, aby bylo možné sledovat výkon databáze SQL Azure a/nebo elastické fondy, budete muset **nakonfigurujte všechny** Azure SQL Database a elastický fond prostředků, které chcete k monitorování k vysílání datového proudu jeho diagnostiky telemetrii do řešení.

- Povolit Azure Diagnostics pro databáze Azure SQL a elastické fondy a [nakonfigurovat je pro odesílají data k analýze protokolů](../sql-database/sql-database-metrics-diag-logging.md).

### <a name="to-configure-multiple-azure-subscriptions"></a>Ke konfiguraci víc předplatných Azure

Pro podporu více předplatných, pomocí skriptu prostředí PowerShell z [povolit Azure prostředku metriky protokolování pomocí prostředí PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/). Při provádění skriptu k odesílání diagnostických dat z prostředků v rámci jednoho předplatného Azure do pracovního prostoru v jiného předplatného Azure, zadejte ID prostředku prostoru jako parametr.

**Příklad**

```
PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/oms/providers/microsoft.operationalinsights/workspaces/omsws"
```

```
PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
```

## <a name="using-the-solution"></a>Použití řešení

Když přidáte řešení do pracovního prostoru, dlaždici analýzy SQL Azure je přidat do pracovního prostoru, a zobrazí se v přehledu. Na dlaždici zobrazuje počet databází Azure SQL a Azure SQL elastické fondy, které řešení je připojen k.

![Azure SQL Analytics dlaždice](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

### <a name="viewing-azure-sql-analytics-data"></a>Zobrazení dat analýzy SQL Azure

Klikněte na **analýzy SQL Azure** dlaždici otevřete řídicí panel Azure SQL Analytics. Řídicí panel obsahuje přehled všech databází, které jsou monitorovány prostřednictvím různých perspektiv. Pro různých perspektiv pracovat je nutné povolit správné metriky nebo protokoly na vašich prostředků SQL tok dat do pracovního prostoru analýzy protokolů Azure.

![Přehled analýzy Azure SQL](./media/log-analytics-azure-sql/azure-sql-sol-overview.png)

Výběrem libovolné dlaždice, otevře se podrobná sestava do konkrétní perspektivy. Když je vybraný perspektivy, je otevřené podrobná sestava.

![Časové limity analýzy Azure SQL](./media/log-analytics-azure-sql/azure-sql-sol-metrics.png)

Každý perspektivy obsahuje souhrny předplatné, server, elastický fond a úroveň databáze. Kromě toho každá perspektiva zobrazuje hlediska specifické pro sestavy na pravé straně. Výběr předplatného, server, fondu nebo databáze ze seznamu bude pokračovat procházení.

| Perspektivy | Popis |
| --- | --- |
| Prostředek podle typu | Perspektivu, která udává všechny prostředky, které jsou monitorovány. Procházení poskytuje souhrn DTU a GB metriky. |
| Insights | Poskytuje hierarchické procházení do inteligentního statistiky. Další informace o inteligentního statistice. |
| Chyby | Poskytuje hierarchické procházení do SQL chybách, ke kterým došlo v databázích. |
| Časové limity | Poskytuje hierarchické procházení do SQL vypršení časových limitů, které bylo provedeno v databázích. |
| Blokování | Poskytuje hierarchické procházení do blokování SQL, které bylo provedeno v databázích. |
| Databáze čeká | Poskytuje hierarchické procházení do SQL čekání Statistika na úrovni databáze. Obsahuje souhrnné informace o celkový čas čekání a doba čekání na typ čekání. |
| Doba trvání dotazu | Poskytuje hierarchické procházení do statistik provádění dotazů například doba trvání dotazu, využití procesoru, vstupně-výstupní operace dat využití, využití vstupně-výstupní operace protokolu. |
| Dotaz čeká | Poskytuje hierarchické procházení do statistiky čekání dotaz podle kategorie čekání. |

### <a name="intelligent-insights-report"></a>Inteligentní Statistika sestavy

Azure SQL Database [inteligentního Statistika](../sql-database/sql-database-intelligent-insights.md) umožňuje víte co se děje s výkon databáze. Všechny inteligentního Statistika shromážděných můžete vizualizována a přístupné prostřednictvím perspektivy statistiky.

![Přehled analýzy Azure SQL](./media/log-analytics-azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Elastický fond a databáze sestav

Databáze i elastické fondy mají své vlastní konkrétní sestavy, které budou zobrazit všechna data, která se shromažďují pro prostředek v určeném čase.

![Databáze Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-database.png)

![Elastický fond Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Dotaz sestavy

Prostřednictvím doba trvání dotazu a dotaz počká perspektivy mohou korelovat výkon jakýkoli dotaz dotazu sestavy. Tato sestava porovnávány výkon dotazů v rámci různých databází a usnadňuje přesné databází, které provádějí vybraný dotaz dobře a ta, která jsou pomalé.

![Analytické dotazy Azure SQL](./media/log-analytics-azure-sql/azure-sql-sol-queries.png)

### <a name="analyze-data-and-create-alerts"></a>Analyzovat data a vytvářet výstrahy

Můžete snadno [vytvářet výstrahy](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) s dat pocházejících z prostředků Azure SQL Database. Tady jsou některé užitečné [hledání protokolů](log-analytics-log-searches.md) dotazy, které můžete používat s výstrahu protokolu:



*Vysoký počet jednotek DTU na databázi Azure SQL*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/DATABASES/" and MetricName=="dtu_consumption_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

*Vysoký počet jednotek DTU na fond elastické databáze Azure SQL*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/ELASTICPOOLS/" and MetricName=="dtu_consumption_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```



## <a name="next-steps"></a>Další postup

- Použití [protokolu hledání](log-analytics-log-searches.md) v analýzy protokolů, chcete-li zobrazit podrobné dat Azure SQL.
- [Vytvořit vlastní řídicí panely](log-analytics-dashboards.md) zobrazující dat Azure SQL.
- [Vytvářet výstrahy](log-analytics-alerts.md) při výskytu určité události Azure SQL.
