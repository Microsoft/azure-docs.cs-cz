---
title: Řešení Azure SQL Analytics ve službě Log Analytics | Dokumentace Microsoftu
description: Řešení Azure SQL Analytics vám usnadní správu vašich databází Azure SQL
services: log-analytics
documentationcenter: ''
author: danimir
manager: carmonm
ms.reviewer: carlrab
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/03/2018
ms.author: v-daljep
ms.component: na
ms.openlocfilehash: b7a7e2787128c74cd7d016c01b751d15628fb4b2
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47181987"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Monitorování Azure SQL Database pomocí Azure SQL Analytics (Preview)

![Azure SQL Analytics symbol](./media/log-analytics-azure-sql/azure-sql-symbol.png)

Azure SQL Analytics je Cloudová řešení pro monitorování výkonu Azure SQL Database, elastických fondů a spravovaných instancí ve velkém měřítku a napříč několika předplatnými monitorování. Shromažďuje a vizualizuje důležité metriky výkonu databáze SQL Azure s integrovanými inteligentními funkcemi pro řešení potíží s výkonem.

Pomocí metrik, která shromáždíte pomocí řešení můžete vytvořit vlastní pravidla monitorování a výstrah. Toto řešení vám umožní identifikovat problémy v každé vrstvě vaším zásobníkem aplikace. Prezentovat data o všech Azure SQL databáze, elastické fondy a databáze spravované instance v jeden pracovní prostor Log Analytics pomocí Azure diagnostických metrik společně se zobrazeními Log Analytics. Log Analytics umožňuje shromažďovat, korelovat a vizualizovat strukturovanými i nestrukturovanými daty.

Praktické přehled o použití řešení Azure SQL Analytics a typické scénáře využití najdete v části vložené video:


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

## <a name="connected-sources"></a>Připojené zdroje

Azure SQL Analytics je cloud určený pro monitorování pouze řešení podpůrné vysílání datového proudu telemetrická data diagnostiky pro Azure SQL Database Managed Instance databáze a elastické fondy.

Jako řešení nepoužívá agentů pro připojení ke službě Log Analytics, řešení neobsahuje podporují monitorování na SQL Server je hostovaný na místním nebo ve virtuálních počítačích, najdete v následující tabulce kompatibility.

| Připojený zdroj | Podporováno | Popis |
| --- | --- | --- |
| **[Diagnostika Azure](log-analytics-azure-storage.md)** | **Ano** | Azure data metrik a protokolů se odesílají do Log Analytics přímo v Azure. |
| [Účet služby Azure Storage](log-analytics-azure-storage.md) | Ne | Log Analytics nemá číst data z účtu úložiště. |
| [Agenti systému Windows](log-analytics-windows-agent.md) | Ne | Přímí agenti Windows nejsou používány nástrojem řešení. |
| [Agenti systému Linux](log-analytics-linux-agents.md) | Ne | Přímí agenti systému Linux nejsou používány nástrojem řešení. |
| [Skupiny pro správu SCOM](log-analytics-om-agents.md) | Ne | Přímé připojení z agenta nástroje SCOM k Log Analytics se nepoužívá tímto řešením. |

## <a name="configuration"></a>Konfigurace

Provedení následujících kroků přidejte řešení pro Azure SQL Analytics na řídicí panel Azure.

1. Přidat do pracovního prostoru z řešení Azure SQL Analytics [Azure marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview).
2. Na webu Azure Portal, klikněte na tlačítko **+ vytvořit prostředek**, vyhledejte **Azure SQL Analytics**.  
    ![Monitorování a správa](./media/log-analytics-azure-sql/monitoring-management.png)
3. Vyberte **Azure SQL Analytics (Preview)** ze seznamu
4. V **Azure SQL Analytics (Preview)** oblast, klikněte na tlačítko **vytvořit**.  
    ![Vytvoření](./media/log-analytics-azure-sql/portal-create.png)
5. V **vytvořit nové řešení** vytvořit novou oblast, nebo vyberte existující pracovní prostor, který chcete přidat řešení a potom klikněte na **vytvořit**.

    ![Přidat do pracovního prostoru](./media/log-analytics-azure-sql/add-to-workspace.png)

### <a name="configure-azure-sql-databases-elastic-pools-and-managed-instances-to-stream-diagnostics-telemetry"></a>Konfigurace databází Azure SQL Database, elastických fondů a spravovaných instancí pro datový proud diagnostickou telemetrii

Po vytvoření řešení Azure SQL Analytics ve vašem pracovním prostoru, aby bylo možné sledovat výkon Azure SQL Database, spravované instanci databáze a elastických fondů, budete muset **nakonfigurujte každý** těchto prostředků, kterou chcete monitorování a Streamovat jeho telemetrická data diagnostiky do řešení.

- Povolení diagnostiky Azure pro Azure SQL Database, spravované Instance databáze a elastické fondy na [Streamovat telemetrická data diagnostiky do Azure SQL Analytics](../sql-database/sql-database-metrics-diag-logging.md).

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

Když přidáte řešení do pracovního prostoru, na dlaždici Azure SQL Analytics je přidána do pracovního prostoru a zobrazí se v přehledu. Na dlaždici se zobrazuje počet databáze, elastické fondy, spravované instance a databáze Azure SQL v Managed instance, které přijímá telemetrická data diagnostiky z řešení.

![Dlaždici Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

Řešení poskytuje dvě samostatné zobrazení – jeden pro monitorování databází Azure SQL Database a elastické fondy a ostatních zobrazeních monitorování Managed Instance a databáze ve spravovaných instancí.

Chcete-li zobrazit řídicí panel pro monitorování Azure SQL Analytics pro Azure SQL Database a elastické fondy, klikněte na tlačítko v horní části dlaždice. Chcete-li zobrazit řídicí panel monitorování pro Managed Instance a databáze ve spravované instanci Azure SQL Analytics, klikněte v dolní části dlaždice.

### <a name="viewing-azure-sql-analytics-data"></a>Zobrazení dat Azure SQL Analytics

Řídicí panel obsahuje přehled všech databází, které jsou monitorovány prostřednictvím různých perspektiv. Pro různých perspektiv pracovat musíte povolit správné metriky nebo protokoly o SQL odesílání do pracovního prostoru Azure Log Analytics.

Mějte prosím na paměti, že pokud některé metriky nebo protokoly nejsou streamovali do služby Azure Log Analytics, nebude dlaždice v řešení vyplní s monitorováním informací.

### <a name="azure-sql-database-and-elastic-pool-view"></a>Azure SQL Database a zobrazení elastického fondu

Dlaždici Azure SQL Analytics pro Azure SQL Database a elastické fondy je vyberete, se zobrazí řídicí panel monitorování.

![Přehled služby Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-overview.png)

Výběrem libovolné prvku dlaždice, otevře sestavu procházení do konkrétní perspektivy. Po výběru perspektivy otevření sestavy procházení.

![Vypršení časového limitu pro Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-metrics.png)

Každá perspektiva v tomto zobrazení obsahuje souhrny předplatného, server, elastický fond a úroveň databáze. Kromě toho každá Perspektiva zobrazí Perspektiva specifické pro sestavu na pravé straně. Výběr předplatného, server, fondu nebo databázi ze seznamu pokračuje procházení podrobností.

### <a name="managed-instance-and-databases-in-managed-instance-view"></a>Spravovaná Instance a databáze v zobrazení pro Managed Instance

Azure SQL Analytics dlaždici pro Managed instance a databáze spravované Instnace je vyberete, se zobrazí řídicí panel monitorování.

![Přehled služby Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-overview-mi.png)

Výběrem libovolné prvku dlaždice, otevře sestavu procházení do konkrétní perspektivy. Po výběru perspektivy otevření sestavy procházení.

Výběr zobrazení Managed Instance, obsahuje podrobnosti o využití Managed Instance a databáze, které obsahuje a telemetrii na dotazy spouštěné přes instanci.

![Vypršení časového limitu pro Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="perspectives"></a>Perspektivy

Následující tabulka uvádí pro dvě verze řídicího panelu, jeden pro Azure SQL database a elastické fondy a druhou pro Managed Instance nepodporuje perspektivy.

| Perspektivy | Popis | Podpora SQL Database a elastické fondy | Managed Instance podpory |
| --- | ------- | ----- | ----- |
| Prostředek podle typu | Perspektivy, která vrátí všechny prostředky, které jsou monitorovány. | Ano | Ano | 
| Insights | Poskytuje hierarchické procházení na užitečné přehledy o výkonu. | Ano | Ano |
| Chyby | Obsahuje hierarchické procházení na chyby SQL, k nimž došlo v databázích. | Ano | Ano |
| Časové limity | Poskytuje hierarchické procházení do vypršení časových limitů SQL, který se stalo v databázích. | Ano | Ne |
| Blokování | Poskytuje hierarchické procházení do SQL blokování, který se stalo v databázích. | Ano | Ne |
| Databáze čeká | Poskytuje hierarchické procházení do SQL statistiky čekání na úrovni databáze. Obsahuje souhrnné informace o celkový čas čekání a čekací doba čekání typu. |Ano | Ano |
| Doba trvání dotazu | Poskytuje hierarchické procházení do statistiky provádění dotazu, jako je doba trvání dotazu, využití procesoru, využití datových v/v, využití protokolovacích v/v. | Ano | Ano |
| Dotaz čeká | Poskytuje hierarchické procházení do statistiky čekání dotazu podle kategorie čekání. | Ano | Ano |

### <a name="intelligent-insights-report"></a>Intelligent Insights sestavy

Azure SQL Database [Intelligent Insights](../sql-database/sql-database-intelligent-insights.md) vás informuje, co se děje s výkonem databáze Azure SQL Database a Managed Instance. Všechny Intelligent Insights shromažďovat můžete vizualizovat a získat přístup prostřednictvím Insights perspektivy.

![Azure SQL Analytics Insights](./media/log-analytics-azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Elastický fond a databáze sestav

Elastické fondy a databáze SQL mají své vlastní konkrétní sestavy, které zobrazují všechna data, která se shromažďují pro prostředek v určený čas.

![Databáze Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-database.png)

![Elastický fond Azure SQL](./media/log-analytics-azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Sestavy dotazu

Doba trvání dotazu a dotaz čeká perspektivy mohli porovnat výkon jakýkoli dotaz dotazu sestavy. Tato sestava porovná výkonu dotazů napříč různými databázemi a usnadňuje identifikaci databází, které provádějí vybraný dotaz a srovnání, které jsou pomalé.

![Azure SQL Analytics dotazů](./media/log-analytics-azure-sql/azure-sql-sol-queries.png)

### <a name="analyze-data-and-create-alerts"></a>Analýza dat a vytvářet výstrahy

### <a name="creating-alerts-for-azure-sql-database"></a>Vytvoření výstrah pro službu Azure SQL Database

Můžete snadno [vytvářet upozornění](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) s daty pocházející z prostředků Azure SQL Database. Tady jsou některé užitečné [prohledávání protokolů](log-analytics-log-searches.md) dotazy, které můžete používat upozornění protokolu:

*Vysoké využití procesoru pro službu Azure SQL Database*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - Před požadavkem nastavení toto upozornění je tento monitorovaných databází datový proud diagnostických metrik (možnost "Všechny metriky") do řešení.
> - Nahraďte hodnotu cpu_percent MetricName dtu_consumption_percent zajistit vysokou DTU výsledky.

*Vysoké využití procesoru na elastické fondy Azure SQL Database*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - Před požadavkem nastavení toto upozornění je tento monitorovaných databází datový proud diagnostických metrik (možnost "Všechny metriky") do řešení.
> - Nahraďte hodnotu cpu_percent MetricName dtu_consumption_percent zajistit vysokou DTU výsledky.

*Úložiště Azure SQL Database v průměr nad 95 % v poslední 1 hodina*

```
let time_range = 1h;
let storage_threshold = 95;
AzureMetrics
| where ResourceId contains "/DATABASES/"
| where MetricName == "storage_percent"
| summarize max_storage = max(Average) by ResourceId, bin(TimeGenerated, time_range)
| where max_storage > storage_threshold
| distinct ResourceId
```

> [!NOTE]
> - Před požadavkem nastavení toto upozornění je tento monitorovaných databází datový proud diagnostických metrik (možnost "Všechny metriky") do řešení.
> - Tento dotaz vyžaduje pravidlo upozornění pro nastavit tak, aby aktivovat výstrahu při existují výsledky (> 0 výsledků) z dotazu, které označuje, že na některé databáze existuje podmínka. Výstupem je seznam databázových prostředků, které jsou nad storage_threshold v rámci time_range definované.
> - Výstupem je seznam databázových prostředků, které jsou nad storage_threshold v rámci time_range definované.

*Upozornění na užitečné přehledy*

```
let alert_run_interval = 1h;
let insights_string = "hitting its CPU limits";
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active" 
| where TimeGenerated > ago(alert_run_interval)
| where rootCauseAnalysis_s contains insights_string
| distinct ResourceId
```

> [!NOTE]
> - Před požadavkem nastavení toto upozornění je protokol diagnostiky monitorovaných databází datového proudu SQLInsights do řešení.
> - Tento dotaz vyžaduje pravidlo upozornění pro nastavení pomocí stejně často jako alert_run_interval předejdete tak duplicitní výsledky. Pravidlo by měla být nastavena pro vyvolat vypnout výstrahy při existují výsledky (> 0 výsledků) z dotazu.
> - Přizpůsobení alert_run_interval zadat časový rozsah pro kontrolu, pokud se stav vyskytl v databázích nakonfigurovaný tak, aby datový proud protokolu SQLInsights k řešení.
> - Přizpůsobení insights_string zachytit výstup analýzy textu Insights kořenové příčiny. Toto je stejný text zobrazený v uživatelském rozhraní, které můžete použít existující insights řešení. Alternativně můžete použít následující dotaz k zobrazení textu všechny přehledy vytvořené v rámci předplatného. Získávání různých řetězců pro nastavení výstrah na základě informací použijte výstup dotazu.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active" 
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instance"></a>Vytvoření výstrah pro Managed Instance

* Spravované Instance storage je vyšší než 90 %

```
let storage_percentage_treshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_treshold
```

> [!NOTE]
> - Předběžné požadavky nastavení toto upozornění je, že má monitorovaných Managed Instance streamování ResourceUsageStats protokol povolen do řešení.
> - Tento dotaz vyžaduje pravidlo upozornění pro nastavit tak, aby aktivovat výstrahu, když existují výsledky (> 0 výsledků) z dotazu, které označuje, že na Managed Instance existuje podmínka. Výstup je procento využití úložiště na Managed Instance.

## <a name="next-steps"></a>Další postup

- Použití [prohledávání protokolů](log-analytics-log-searches.md) v Log Analytics k zobrazení podrobných dat Azure SQL.
- [Vytvářejte vlastní řídicí panely](log-analytics-dashboards.md) zobrazení dat Azure SQL.
- [Vytvořit upozornění](log-analytics-alerts.md) Pokud dojde k určité událostem Azure SQL.
