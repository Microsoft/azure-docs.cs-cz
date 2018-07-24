---
title: Řešení Azure SQL Analytics ve službě Log Analytics | Dokumentace Microsoftu
description: Řešení Azure SQL Analytics vám usnadní správu vašich databází Azure SQL
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
ms.openlocfilehash: 440e16416b8567178c61c3d6ce2155e0e331521c
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216321"
---
# <a name="monitor-azure-sql-databases-using-azure-sql-analytics-preview"></a>Monitorování databází Azure SQL Database pomocí Azure SQL Analytics (Preview)

![Azure SQL Analytics symbol](./media/log-analytics-azure-sql/azure-sql-symbol.png)

Azure SQL Analytics je Cloudová řešení pro monitorování výkonu databáze SQL Azure ve velkém měřítku napříč více elastických fondů a předplatná pro monitorování. Shromažďuje a vizualizuje důležité metriky výkonu databáze SQL Azure s integrovanými inteligentními funkcemi pro řešení potíží s nejvyšší výkon. 

Pomocí metrik, která shromáždíte pomocí řešení můžete vytvořit vlastní pravidla monitorování a výstrah. Toto řešení vám umožní identifikovat problémy v každé vrstvě vaším zásobníkem aplikace. K dispozici data o vašich databází Azure SQL a elastických fondů v jeden pracovní prostor Log Analytics pomocí Azure diagnostických metrik společně se zobrazeními Log Analytics. Log Analytics umožňuje shromažďovat, korelovat a vizualizovat strukturovanými i nestrukturovanými daty.

Toto řešení ve verzi preview v současné době podporuje až 150 000 databází Azure SQL Database a 5 000 Elastických fondech SQL za jednotlivé pracovní prostory.

Praktické přehled o použití řešení Azure SQL Analytics a typické scénáře využití najdete v části vložené video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

## <a name="connected-sources"></a>Připojené zdroje

Azure SQL Analytics je cloud určený pro monitorování řešení podpůrné vysílání datového proudu telemetrická data diagnostiky pro Azure SQL Database a elastické fondy. Jak se připojit ke službě Log Analytics nepoužívá agentů, řešení nepodporuje připojení s Windows, Linux nebo SCOM prostředky, najdete v následující tabulce kompatibility.

| Připojený zdroj | Podpora | Popis |
| --- | --- | --- |
| **[Diagnostika Azure](log-analytics-azure-storage.md)** | **Ano** | Azure data metrik a protokolů se odesílají do Log Analytics přímo v Azure. |
| [Účet služby Azure Storage](log-analytics-azure-storage.md) | Ne | Log Analytics nenačítá data z účtu úložiště. |
| [Agenti systému Windows](log-analytics-windows-agent.md) | Ne | Přímí agenti Windows nejsou používány nástrojem řešení. |
| [Agenti systému Linux](log-analytics-linux-agents.md) | Ne | Přímí agenti systému Linux nejsou používány nástrojem řešení. |
| [Skupiny pro správu SCOM](log-analytics-om-agents.md) | Ne | Přímé připojení z agenta nástroje SCOM k Log Analytics se nepoužívá tímto řešením. |

## <a name="configuration"></a>Konfigurace

Provedení následujících kroků přidejte řešení pro Azure SQL Analytics do pracovního prostoru.

1. Přidat do pracovního prostoru z řešení Azure SQL Analytics [Azure marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview).
2. Na webu Azure Portal, klikněte na tlačítko **+ vytvořit prostředek**, vyhledejte **Azure SQL Analytics**.  
    ![Monitorování a správa](./media/log-analytics-azure-sql/monitoring-management.png)
3. Vyberte **Azure SQL Analytics (Preview)** ze seznamu
4. V **Azure SQL Analytics (Preview)** oblast, klikněte na tlačítko **vytvořit**.  
    ![Vytvoření](./media/log-analytics-azure-sql/portal-create.png)
5. V **vytvořit nové řešení** vytvořit novou oblast, nebo vyberte existující pracovní prostor, který chcete přidat řešení a potom klikněte na **vytvořit**.  
    ![Přidat do pracovního prostoru](./media/log-analytics-azure-sql/add-to-workspace.png)

### <a name="configure-azure-sql-databases-and-elastic-pools-to-stream-diagnostics-telemetry"></a>Konfigurace databází Azure SQL Database a elastické fondy na datový proud diagnostickou telemetrii

Po vytvoření řešení Azure SQL Analytics ve vašem pracovním prostoru, aby bylo možné sledovat výkon databází Azure SQL Database a/nebo elastické fondy, budete muset **nakonfigurujte každý** Azure SQL Database a chcete, aby prostředků elastického fondu k monitorování datového proudu jeho telemetrická data diagnostiky do řešení.

- Povolení diagnostiky Azure pro Azure SQL Database a elastické fondy a [nakonfigurujete je, aby odesílají data do Log Analytics](../sql-database/sql-database-metrics-diag-logging.md).

### <a name="to-configure-multiple-azure-subscriptions"></a>Chcete-li nakonfigurovat více předplatných Azure

Pro podporu více předplatných, pomocí skriptu prostředí PowerShell z [protokolování metrik prostředku povolit Azure pomocí Powershellu](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/). Zadejte ID prostředku pracovního prostoru jako parametr při spuštění skriptu k odesílání diagnostických dat z prostředků v rámci jednoho předplatného Azure do pracovního prostoru do jiného předplatného Azure.

**Příklad**

```
PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/oms/providers/microsoft.operationalinsights/workspaces/omsws"
```

```
PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
```

## <a name="using-the-solution"></a>Použití řešení

Když přidáte řešení do pracovního prostoru, na dlaždici Azure SQL Analytics je přidána do pracovního prostoru a zobrazí se v přehledu. Na dlaždici se zobrazuje počet databází Azure SQL a elastické fondy Azure SQL, které řešení je připojený k.

![Dlaždici Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

### <a name="viewing-azure-sql-analytics-data"></a>Zobrazení dat Azure SQL Analytics

Klikněte na **Azure SQL Analytics** dlaždici otevřete řídicí panel Azure SQL Analytics. Řídicí panel obsahuje přehled všech databází, které jsou monitorovány prostřednictvím různých perspektiv. Pro různých perspektiv pracovat musíte povolit správné metriky nebo protokoly o SQL odesílání do pracovního prostoru Azure Log Analytics.

![Přehled služby Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-overview.png)

Výběrem libovolné prvku dlaždice, otevře sestavu procházení do konkrétní perspektivy. Po výběru perspektivy otevření sestavy procházení.

![Vypršení časového limitu pro Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-metrics.png)

Každá Perspektiva poskytuje souhrny na předplatné, server, elastický fond a úroveň databáze. Kromě toho každá Perspektiva zobrazí Perspektiva specifické pro sestavu na pravé straně. Výběr předplatného, server, fondu nebo databázi ze seznamu pokračuje procházení podrobností.

| Perspektivy | Popis |
| --- | --- |
| Prostředek podle typu | Perspektivy, která vrátí všechny prostředky, které jsou monitorovány. Procházení poskytuje přehled metrik DTU a GB. |
| Insights | Poskytuje hierarchické procházení na užitečné přehledy. Další informace o inteligentních přehledů. |
| Chyby | Obsahuje hierarchické procházení na chyby SQL, k nimž došlo v databázích. |
| Vypršení časových limitů | Poskytuje hierarchické procházení do vypršení časových limitů SQL, který se stalo v databázích. |
| Blokování | Poskytuje hierarchické procházení do SQL blokování, který se stalo v databázích. |
| Databáze čeká | Poskytuje hierarchické procházení do SQL statistiky čekání na úrovni databáze. Obsahuje souhrnné informace o celkový čas čekání a čekací doba čekání typu. |
| Doba trvání dotazu | Poskytuje hierarchické procházení do statistiky provádění dotazu, jako je doba trvání dotazu, využití procesoru, využití datových v/v, využití protokolovacích v/v. |
| Dotaz čeká | Poskytuje hierarchické procházení do statistiky čekání dotazu podle kategorie čekání. |

### <a name="intelligent-insights-report"></a>Intelligent Insights sestavy

Azure SQL Database [Intelligent Insights](../sql-database/sql-database-intelligent-insights.md) vás informuje, co se děje s výkonu vaší databáze. Všechny Intelligent Insights shromažďovat můžete vizualizovat a získat přístup prostřednictvím Insights perspektivy.

![Azure SQL Analytics Insights](./media/log-analytics-azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Elastický fond a databáze sestav

Elastické fondy a databáze mají své vlastní konkrétní sestavy, které zobrazují všechna data, která se shromažďují pro prostředek v určený čas.

![Databáze Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-database.png)

![Elastický fond Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Sestavy dotazu

Doba trvání dotazu a dotaz čeká perspektivy mohli porovnat výkon jakýkoli dotaz dotazu sestavy. Tato sestava porovná výkonu dotazů napříč různými databázemi a usnadňuje identifikaci databází, které provádějí vybraný dotaz a srovnání, které jsou pomalé.

![Azure SQL Analytics dotazů](./media/log-analytics-azure-sql/azure-sql-sol-queries.png)

### <a name="analyze-data-and-create-alerts"></a>Analýza dat a vytvářet výstrahy

Můžete snadno [vytvářet upozornění](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) s daty pocházející z prostředků Azure SQL Database. Tady jsou některé užitečné [prohledávání protokolů](log-analytics-log-searches.md) dotazy, které můžete používat upozornění protokolu:



*High DTU na databázi Azure SQL*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/DATABASES/" and MetricName=="dtu_consumption_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

*High DTU na elastický fond Azure SQL Database*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/ELASTICPOOLS/" and MetricName=="dtu_consumption_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```



## <a name="next-steps"></a>Další postup

- Použití [prohledávání protokolů](log-analytics-log-searches.md) v Log Analytics k zobrazení podrobných dat Azure SQL.
- [Vytvářejte vlastní řídicí panely](log-analytics-dashboards.md) zobrazení dat Azure SQL.
- [Vytvořit upozornění](log-analytics-alerts.md) Pokud dojde k určité událostem Azure SQL.
