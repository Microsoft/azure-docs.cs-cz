---
title: Řešení Azure SQL Analytics ve službě Log Analytics | Dokumentace Microsoftu
description: Řešení Azure SQL Analytics vám usnadní správu vašich databází Azure SQL
services: log-analytics
ms.service: log-analytics
ms.subservice: performance
ms.custom: ''
ms.devlang: na
ms.topic: conceptual
author: danimir
ms.reviewer: carlrab
manager: craigg
ms.date: 11/26/2018
ms.author: v-daljep
ms.openlocfilehash: 57faa347b2733ebf65757b02ee9395f94776cf10
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52635386"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Monitorování Azure SQL Database pomocí Azure SQL Analytics (Preview)

![Azure SQL Analytics symbol](./media/log-analytics-azure-sql/azure-sql-symbol.png)

Azure SQL Analytics je Cloudová řešení pro monitorování výkonu databáze, elastické fondy a spravovaných instancí Azure SQL ve velkém měřítku a napříč několika předplatnými prostřednictvím podokně ze skla. Díky monitorování. Shromažďuje a vizualizuje důležité metriky výkonu databáze SQL Azure s integrovanými inteligentními funkcemi pro řešení potíží s výkonem.

Pomocí metrik, která shromáždíte pomocí řešení můžete vytvořit vlastní pravidla monitorování a výstrah. Toto řešení vám umožní identifikovat problémy v každé vrstvě vaším zásobníkem aplikace. Prezentovat data o všech Azure SQL databáze, elastické fondy a databáze spravované instance v jeden pracovní prostor Log Analytics pomocí Azure diagnostických metrik společně se zobrazeními Log Analytics. Log Analytics umožňuje shromažďovat, korelovat a vizualizovat strukturovanými i nestrukturovanými daty.

Praktické přehled o použití řešení Azure SQL Analytics a typické scénáře využití najdete v části vložené video:

>[!VIDEO https://youtu.be/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Připojené zdroje

Azure SQL Analytics je cloud určený jen pro monitorování řešení podpůrné vysílání datového proudu telemetrická data diagnostiky pro Azure SQL Database: jeden, ve fondu a spravované instanci databáze. Jako řešení nepoužívá agentů pro připojení ke službě Log Analytics, řešení neobsahuje podporují monitorování na SQL Server je hostovaný na místním nebo ve virtuálních počítačích, najdete v následující tabulce kompatibility.

| Připojený zdroj | Podporováno | Popis |
| --- | --- | --- |
| [Diagnostika Azure](log-analytics-azure-storage.md) | **Ano** | Azure data metrik a protokolů se odesílají do Log Analytics přímo v Azure. |
| [Účet služby Azure Storage](log-analytics-azure-storage.md) | Ne | Log Analytics nemá číst data z účtu úložiště. |
| [Agenti systému Windows](../azure-monitor/platform/agent-windows.md) | Ne | Přímí agenti Windows nejsou používány nástrojem řešení. |
| [Agenti systému Linux](log-analytics-quick-collect-linux-computer.md) | Ne | Přímí agenti systému Linux nejsou používány nástrojem řešení. |
| [Skupina pro správu System Center Operations Manager](log-analytics-om-agents.md) | Ne | Přímé připojení z agenta Operations Manageru k Log Analytics se nepoužívá tímto řešením. |

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

Když vytvoříte řešení Azure SQL Analytics ve vašem pracovním prostoru, budete muset **nakonfigurujte každý** prostředky, které chcete monitorovat Streamovat jeho telemetrická data diagnostiky do řešení. Postupujte podle podrobných pokynů na této stránce:

- Povolení diagnostiky Azure pro vaši databázi Azure SQL na [Streamovat telemetrická data diagnostiky do Azure SQL Analytics](../sql-database/sql-database-metrics-diag-logging.md).

Výše uvedená stránka také obsahuje pokyny k povolení monitorování více předplatných Azure z jednoho pracovního prostoru Azure SQL Analytics jako podokně ze skla.

## <a name="using-the-solution"></a>Použití řešení

Když přidáte řešení do pracovního prostoru, na dlaždici Azure SQL Analytics je přidána do pracovního prostoru a zobrazí se v přehledu. Na dlaždici se zobrazuje počet databáze, elastické fondy, spravované instance a databáze Azure SQL v Managed instance, které přijímá telemetrická data diagnostiky z řešení.

![Dlaždici Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

Řešení poskytuje dvě samostatné zobrazení – jeden pro monitorování databází Azure SQL Database a elastické fondy a ostatních zobrazeních monitorování Managed Instance a databáze ve spravovaných instancí.

Chcete-li zobrazit řídicí panel pro monitorování Azure SQL Analytics pro Azure SQL Database a elastické fondy, klikněte na tlačítko v horní části dlaždice. Chcete-li zobrazit řídicí panel monitorování pro Managed Instance a databáze ve spravované instanci Azure SQL Analytics, klikněte v dolní části dlaždice.

### <a name="viewing-azure-sql-analytics-data"></a>Zobrazení dat Azure SQL Analytics

Řídicí panel obsahuje přehled všech databází, které jsou monitorovány prostřednictvím různých perspektiv. Pro různých perspektiv pracovat musíte povolit správné metriky nebo protokoly o SQL odesílání do pracovního prostoru Azure Log Analytics.

Všimněte si, že pokud některé metriky nebo protokoly nejsou streamovali do služby Azure Log Analytics, dlaždice v řešení se vyplní informace o sledování.

### <a name="azure-sql-database-and-elastic-pool-view"></a>Azure SQL Database a zobrazení elastického fondu

Po výběru dlaždice databázi Azure SQL Analytics se zobrazí řídicí panel monitorování.

![Přehled služby Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-overview.png)

Výběrem libovolné prvku dlaždice, otevře sestavu procházení do konkrétní perspektivy. Po výběru perspektivy otevření sestavy procházení.

![Vypršení časového limitu pro Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-metrics.png)

Každá perspektiva v tomto zobrazení obsahuje souhrny předplatného, server, elastický fond a úroveň databáze. Kromě toho každá Perspektiva zobrazí Perspektiva specifické pro sestavu na pravé straně. Výběr předplatného, server, fondu nebo databázi ze seznamu pokračuje procházení podrobností.

### <a name="managed-instance-and-databases-in-managed-instance-view"></a>Spravovaná Instance a databáze v zobrazení pro Managed Instance

Po výběru dlaždice Azure SQL Analytics pro databáze, se zobrazí řídicí panel monitorování.

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

Azure SQL Database [Intelligent Insights](../sql-database/sql-database-intelligent-insights.md) vás informuje, co se děje s výkonem všechny databáze Azure SQL. Všechny Intelligent Insights shromažďovat můžete vizualizovat a získat přístup prostřednictvím Insights perspektivy.

![Azure SQL Analytics Insights](./media/log-analytics-azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Elastický fond a databáze sestav

Elastické fondy a databáze SQL mají své vlastní konkrétní sestavy, které zobrazují všechna data, která se shromažďují pro prostředek v určený čas.

![Databáze Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-database.png)

![Elastický fond Azure SQL](./media/log-analytics-azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Sestavy dotazu

Doba trvání dotazu a dotaz čeká perspektivy mohli porovnat výkon jakýkoli dotaz dotazu sestavy. Tato sestava porovná výkonu dotazů napříč různými databázemi a usnadňuje identifikaci databází, které provádějí vybraný dotaz a srovnání, které jsou pomalé.

![Azure SQL Analytics dotazů](./media/log-analytics-azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Oprávnění

Pokud chcete používat Azure SQL Analytics, uživatelé musí udělit minimální oprávnění role čtenáře v Azure. Tuto roli však není povolit uživatelům zobrazit text dotazu, nebo provádět jakékoli automatického ladění akce. Mnohem mírnější role v Azure, které umožňují použití řešení v nejširším rozsahu jsou vlastník, Přispěvatel, Přispěvatel databází SQL nebo Přispěvatel SQL serveru. Můžete také chtít zvažte možnost vytvořit vlastní roli na portálu s konkrétní oprávnění požadovaná pouze pro použití služby Azure SQL Analytics a bez přístupu ke správě jiných zdrojů.

### <a name="creating-a-custom-role-in-portal"></a>Vytvořením vlastní role na portálu

Rozpozná, že některé organizace přinutit striktní oprávnění ovládací prvky v Azure, vyhledejte následující skript Powershellu vytvořit vlastní roli "SQL Analytics monitorování operátor" na webu Azure portal s minimální čtení a zápis oprávnění potřebná k povolení použití Azure SQL Analytics jeho nejširším rozsahu.

Nahradit v "{SubscriptionId}" níže uvedený skript s ID vašeho předplatného Azure a spusťte tento skript přihlášení jako roli vlastníka nebo přispěvatele v Azure.

   ```powershell
    Connect-AzureRmAccount
    Select-AzureRmSubscription {SubscriptionId}
    $role = Get-AzureRmRoleDefinition -Name Reader
    $role.Name = "SQL Analytics Monitoring Operator"
    $role.Description = "Lets you monitor database performance with Azure SQL Analytics as a reader. Does not allow change of resources."
    $role.IsCustom = $true
    $role.Actions.Add("Microsoft.SQL/servers/databases/read");
    $role.Actions.Add("Microsoft.SQL/servers/databases/topQueries/queryText/*");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/*");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Resources/deployments/write");
    $role.AssignableScopes = "/subscriptions/{SubscriptionId}"
    New-AzureRmRoleDefinition $role
   ```

Po vytvoření nové role tuto roli přiřaďte každý uživatel, který je potřeba udělit vlastní oprávnění k používání Azure SQL Analytics.

## <a name="analyze-data-and-create-alerts"></a>Analýza dat a vytvářet výstrahy

Analýza dat ve službě Azure SQL Analytics vychází [jazyk Log Analytics](./query-language/get-started-queries.md) pro vaše vlastní dotazování a generování sestav. Popis hledání dostupných dat shromážděných z databáze prostředků pro vlastní dotazování v [metrik a protokolů, které jsou k dispozici](../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available).

Automatické výstrahy v řešení je založena na psaní dotazu Log Analytics, která aktivuje upozornění na podmínky splněny. Najdete pod několik příkladů na dotazy Log Analytics na výstrahy, které lze nastavit v řešení.

### <a name="creating-alerts-for-azure-sql-database"></a>Vytvoření výstrah pro službu Azure SQL Database

Můžete snadno [vytvářet upozornění](../monitoring-and-diagnostics/alert-metric.md) s daty pocházející z prostředků Azure SQL Database. Tady jsou některé užitečné [prohledávání protokolů](log-analytics-queries.md) dotazy, které můžete používat upozornění protokolu:

#### <a name="high-cpu-on-azure-sql-database"></a>Vysoké využití procesoru pro službu Azure SQL Database

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

#### <a name="high-cpu-on-azure-sql-database-elastic-pools"></a>Vysoké využití procesoru na elastické fondy Azure SQL Database

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

#### <a name="azure-sql-database-storage-in-average-above-95-in-the-last-1-hr"></a>Úložiště Azure SQL Database v průměr nad 95 % v poslední 1 hodina

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

#### <a name="alert-on-intelligent-insights"></a>Upozornění na užitečné přehledy

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

#### <a name="managed-instance-storage-is-above-90"></a>Spravovaná Instance je úložiště větší než 90 %

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
> - Předběžné požadavky nastavení toto upozornění je, že monitorovaná Managed Instance má streamování ResourceUsageStats protokol povolen do řešení.
> - Tento dotaz vyžaduje pravidlo upozornění pro nastavit tak, aby aktivovat výstrahu, když existují výsledky (> 0 výsledků) z dotazu, které označuje, že na Managed Instance existuje podmínka. Výstup je procento využití úložiště na Managed Instance.

#### <a name="managed-instance-cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>Spravované průměr spotřeby procesoru Instance je vyšší než 95 % v poslední 1 hodina

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
> - Předběžné požadavky nastavení toto upozornění je, že monitorovaná Managed Instance má streamování ResourceUsageStats protokol povolen do řešení.
> - Tento dotaz vyžaduje pravidlo upozornění pro nastavit tak, aby aktivovat výstrahu, když existují výsledky (> 0 výsledků) z dotazu, které označuje, že na Managed Instance existuje podmínka. Výstup je průměr spotřeby procento využití procesoru v definované období na Managed Instance.

### <a name="pricing"></a>Ceny

Při řešení používat, platí spotřeby telemetrická data diagnostiky nad volných jednotek ingestování přidělené každý měsíc, přečtěte si téma [přehledu cen Log Analytics](https://azure.microsoft.com/en-us/pricing/details/monitor). Volných jednotek příjem dat, které jsou k dispozici povolit bezplatné sledování několik databází každý měsíc. Všimněte si, že aktivní databáze s větším zatížení ingestování další data a nečinné databáze. Vyberte pracovní prostor OMS v navigační nabídce Azure SQL Analytics, a následným výběrem využití a odhadované náklady můžou snadno monitorovat spotřebu příjem dat v řešení.

## <a name="next-steps"></a>Další postup

- Použití [prohledávání protokolů](log-analytics-queries.md) v Log Analytics k zobrazení podrobných dat Azure SQL.
- [Vytvářejte vlastní řídicí panely](../azure-monitor/platform/dashboards.md) zobrazení dat Azure SQL.
- [Vytvořit upozornění](../monitoring-and-diagnostics/monitoring-overview-alerts.md) Pokud dojde k určité událostem Azure SQL.
