---
title: Azure SQL Analytics řešení v Azure Monitor | Microsoft Docs
description: Řešení Azure SQL Analytics vám pomůže spravovat databáze SQL Azure.
services: log-analytics
ms.service: log-analytics
ms.custom: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: carlrab
manager: craigg
ms.date: 12/17/2018
ms.openlocfilehash: f27ea984e2c11be03d69295d206c1b99791a3f72
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693283"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Monitorování Azure SQL Database pomocí Azure SQL Analytics (Preview)

![Symbol Azure SQL Analytics](./media/azure-sql/azure-sql-symbol.png)

Azure SQL Analytics je pokročilé řešení monitorování cloudu pro monitorování výkonu databází SQL Azure, elastických fondů a spravovaných instancí ve velkém měřítku a napříč několika předplatnými přes jedno podokno skla. S využitím integrovaných inteligentních funkcí shromažďuje a vizualizuje důležité metriky výkonu služby Azure SQL Database pro účely řešení potíží s výkonem.

Pomocí metrik, které shromažďujete s řešením, můžete vytvořit vlastní pravidla monitorování a výstrahy. Řešení vám pomůže identifikovat problémy v každé vrstvě zásobníku aplikace. Pomocí diagnostických metrik Azure spolu s zobrazeními Azure Monitor prezentuje data o všech vašich databázích SQL Azure, elastických fondech a databázích ve spravovaných instancích v rámci jediného pracovního prostoru Log Analytics. Azure Monitor pomáhá shromažďovat, korelovat a vizualizovat strukturovaná a nestrukturovaná data.

Praktické přehledy o používání řešení Azure SQL Analytics a typických scénářích použití najdete ve vloženém videu:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Připojené zdroje

Azure SQL Analytics je pouze cloudové řešení monitorování, které podporuje streamování diagnostiky diagnostiky pro Azure SQL Database: jedna, sdružená a databáze spravované instance. Jelikož řešení nepoužívá pro připojení k Azure Monitor agenty, řešení nepodporuje monitorování SQL Server hostovaných místně nebo na virtuálních počítačích, viz následující tabulka kompatibility.

| Připojený zdroj | Podporováno | Popis |
| --- | --- | --- |
| [Nastavení diagnostiky](../platform/diagnostic-settings.md) | **Ano** | Data metriky a protokolu Azure se odesílají do protokolů Azure Monitor přímo pomocí Azure. |
| [Účet služby Azure Storage](../platform/collect-azure-metrics-logs.md) | Ne | Azure Monitor nečtou data z účtu úložiště. |
| [Agenti systému Windows](../platform/agent-windows.md) | Ne | Přímo agenti Windows nepoužívá řešení. |
| [Agenti systému Linux](../learn/quick-collect-linux-computer.md) | Ne | Přímo agenti pro Linux nepoužívá řešení. |
| [System Center Operations Manager skupina pro správu](../platform/om-agents.md) | Ne | Řešení nepoužívá přímé připojení od agenta Operations Manager k Azure Monitor. |

## <a name="configuration"></a>Konfigurace
Pomocí postupu popsaného v tématu [přidání Azure monitor řešení z galerie řešení](../../azure-monitor/insights/solutions.md) přidejte řešení Azure SQL Analytics (Preview) do pracovního prostoru Log Analytics.

### <a name="configure-azure-sql-databases-elastic-pools-and-managed-instances-to-stream-diagnostics-telemetry"></a>Konfigurace databází Azure SQL, elastických fondů a spravovaných instancí pro streamování diagnostiky diagnostiky

Jakmile vytvoříte řešení Azure SQL Analytics v pracovním prostoru, budete muset **nakonfigurovat všechny** prostředky, které chcete monitorovat, aby se do něj mohla zasílat jeho diagnostická telemetrie. Postupujte podle podrobných pokynů na této stránce:

- Povolte Azure Diagnostics služby Azure SQL Database, abyste mohli [streamovat diagnostiku diagnostiky do Azure SQL Analytics](../../sql-database/sql-database-metrics-diag-logging.md).

Na výše uvedené stránce najdete taky pokyny, jak povolit podporu monitorování více předplatných Azure z jednoho Azure SQL Analytics pracovního prostoru jako jediného podokna skla.

## <a name="using-the-solution"></a>Použití řešení

Když přidáte řešení do svého pracovního prostoru, přidá se do pracovního prostoru Azure SQL Analytics dlaždice a zobrazí se v přehledu. Vyberte odkaz Zobrazit souhrn a načtěte obsah dlaždice.

![Dlaždice souhrnu Azure SQL Analytics](./media/azure-sql/azure-sql-sol-tile-01.png)

Po načtení se na dlaždici zobrazí počet databází Azure SQL, elastické fondy, spravované instance a databáze ve spravovaných instancích, ze kterých řešení přijímá telemetrii diagnostiky.

![Dlaždice Azure SQL Analytics](./media/azure-sql/azure-sql-sol-tile-02.png)

Řešení nabízí dvě samostatná zobrazení – jednu pro monitorování databází Azure SQL a elastických fondů a další zobrazení pro monitorování spravované instance a databáze ve spravovaných instancích.

Pokud chcete zobrazit řídicí panel monitorování Azure SQL Analytics pro databáze SQL Azure a elastické fondy, klikněte na horní část dlaždice. Pokud chcete zobrazit řídicí panel monitorování Azure SQL Analytics pro spravovanou instanci a databáze ve spravované instanci, klikněte na spodní část dlaždice.

### <a name="viewing-azure-sql-analytics-data"></a>Zobrazení Azure SQL Analytics dat

Řídicí panel obsahuje přehled všech databází, které jsou monitorovány pomocí různých perspektiv. Aby bylo možné pracovat s různými perspektivami, musíte povolit správné metriky nebo protokoly na svých prostředcích SQL pro streamování do Log Analytics pracovního prostoru.

Všimněte si, že pokud se některé metriky nebo protokoly nestreamují do Azure Monitor, dlaždice v řešení nejsou naplněny informacemi o monitorování.

### <a name="azure-sql-database-and-elastic-pool-view"></a>Zobrazení Azure SQL Database a elastického fondu

Po výběru dlaždice Azure SQL Analytics pro databázi se zobrazí řídicí panel monitorování.

![Přehled Azure SQL Analytics](./media/azure-sql/azure-sql-sol-overview.png)

Když vyberete některou dlaždici, otevře se v konkrétní perspektivě sestava s podrobnostmi. Po výběru perspektivy se otevře sestava procházení sestav.

![Azure SQL Analytics vypršení časových limitů](./media/azure-sql/azure-sql-sol-metrics.png)

Každá perspektiva v tomto zobrazení poskytuje souhrny na úrovni předplatného, serveru, elastického fondu a databáze. Kromě toho Každá perspektiva zobrazuje perspektivu specifickou pro sestavu na pravé straně. Výběr předplatného, serveru, fondu nebo databáze ze seznamu pokračuje v procházení podrobností.

### <a name="managed-instance-and-databases-in-managed-instance-view"></a>Spravovaná instance a databáze v zobrazení spravované instance

Po výběru dlaždice Azure SQL Analytics pro databáze se zobrazí řídicí panel monitorování.

![Přehled Azure SQL Analytics](./media/azure-sql/azure-sql-sol-overview-mi.png)

Když vyberete některou dlaždici, otevře se v konkrétní perspektivě sestava s podrobnostmi. Po výběru perspektivy se otevře sestava procházení sestav.

Když vyberete zobrazení spravovaná instance, zobrazí se podrobnosti o využití spravované instance, databázích, které obsahuje, a telemetrie na dotazech spuštěných napříč instancí.

![Azure SQL Analytics vypršení časových limitů](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="perspectives"></a>Perspektivy

Níže uvedená tabulka shrnuje perspektivy podporované pro dvě verze řídicího panelu, jeden pro Azure SQL Database a elastické fondy a druhý pro spravovanou instanci.

| Perspektivy | Popis | Podpora SQL Database a elastických fondů | Podpora spravované instance |
| --- | ------- | ----- | ----- |
| Prostředek podle typu | Perspektiva, která počítá všechny monitorované prostředky. | Ano | Ano |
| Poznatky | Poskytuje hierarchické procházení k podrobnostem Intelligent Insights do výkonu. | Ano | Ano |
| Chyby | Poskytuje hierarchické procházení k podrobnostem o chybách SQL, k nimž došlo v databázích. | Ano | Ano |
| Časové limity | Poskytuje hierarchické procházení podrobností k časovým limitům SQL, k nimž došlo v databázích. | Ano | Ne |
| Blokování | Poskytuje hierarchické procházení do bloků SQL, ke kterým došlo v databázích. | Ano | Ne |
| Databáze čeká | Poskytuje hierarchickou hloubkovou statistiku SQL na úrovni databáze. Obsahuje souhrny celkové čekací doby a dobu čekání na typ čekání. |Ano | Ano |
| Doba trvání dotazu | Poskytuje hierarchické procházení k podrobnostem do statistik provádění dotazů, jako je například doba trvání dotazu, využití procesoru, použití dat v/v, protokol použití v/v. | Ano | Ano |
| Dotaz čeká | Poskytuje hierarchické procházení k podrobnostem do statistiky čekání na dotaz podle kategorie čekání. | Ano | Ano |

### <a name="intelligent-insights-report"></a>Sestava Intelligent Insights

Azure SQL Database [Intelligent Insights](../../sql-database/sql-database-intelligent-insights.md) vám umožní zjistit, co se děje s výkonem všech databází SQL Azure. Všechny shromážděné Intelligent Insights lze vizuálně rozčlenit a získat z perspektivy Insights.

![Azure SQL Analytics přehledy](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Elastický fond a sestavy databáze

Elastické fondy i databáze SQL mají vlastní konkrétní sestavy, které zobrazují všechna data shromážděná pro daný prostředek v zadaném čase.

![Azure SQL Analytics databáze](./media/azure-sql/azure-sql-sol-database.png)

![Elastický fond Azure SQL](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Sestavy dotazů

Po dobu trvání dotazu a dotaz čeká na perspektivu, můžete korelovat výkon libovolného dotazu prostřednictvím sestavy dotazu. Tato sestava porovnává výkon dotazů napříč různými databázemi a usnadňuje rozpoznání databází, které provádějí odpovídající i pomalé dotazy.

![Azure SQL Analytics dotazy](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Oprávnění

Aby bylo možné použít Azure SQL Analytics, musí být uživatelům v Azure uděleno minimální oprávnění role čtenář. Tato role ale neumožňuje uživatelům zobrazit text dotazu nebo provádět žádné akce automatického ladění. Účinnější role v Azure, které umožňují použití řešení s největším rozsahem, jsou Owner, přispěvatel, SQL DB přispěvatel nebo přispěvatel SQL Server. Možná budete chtít zvážit vytvoření vlastní role na portálu s konkrétními oprávněními, která jsou nutná jenom pro použití Azure SQL Analytics, a bez přístupu ke správě jiných prostředků.

### <a name="creating-a-custom-role-in-portal"></a>Vytvoření vlastní role na portálu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

UZNÁVAJÍCE, že některé organizace vynutily přísné ovládací prvky oprávnění v Azure, najdete následující skript PowerShellu, který umožňuje vytvoření vlastní role "operátor monitorování analýzy SQL" v Azure Portal s minimálními oprávněními ke čtení a zápisu vyžadovanými na Azure SQL Analytics můžete použít do plného rozsahu.

Nahraďte "{SubscriptionId}" ve spodním skriptu vaším ID předplatného Azure a spusťte skript přihlášený jako vlastník nebo role přispěvatele v Azure.

   ```powershell
    Connect-AzAccount
    Select-AzSubscription {SubscriptionId}
    $role = Get-AzRoleDefinition -Name Reader
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
    $role.Actions.Add("Microsoft.Sql/servers/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Resources/deployments/write");
    $role.AssignableScopes = "/subscriptions/{SubscriptionId}"
    New-AzRoleDefinition $role
   ```

Jakmile je nová role vytvořená, přiřaďte tuto roli každému uživateli, který potřebujete k udělení vlastních oprávnění k použití Azure SQL Analytics.

## <a name="analyze-data-and-create-alerts"></a>Analýza dat a vytváření výstrah

Analýza dat v Azure SQL Analytics vychází z [Log Analytics jazyka](../log-query/get-started-queries.md) pro vlastní dotazování a vytváření sestav. Vyhledejte popis dostupných dat shromážděných z databázového prostředku pro vlastní dotazování v [metrikách a protokolech, které jsou k dispozici](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available).

Automatizované upozorňování v řešení je založené na zápisu Log Analytics dotaz, který aktivuje výstrahu při splnění podmínky. Níže naleznete několik příkladů Log Analytics dotazů, na kterých je možné nastavit upozorňování v řešení.

### <a name="creating-alerts-for-azure-sql-database"></a>Vytváření upozornění pro Azure SQL Database

Můžete snadno [vytvářet výstrahy](../platform/alerts-metric.md) s daty, která přicházejí z Azure SQL Databasech prostředků. Tady jsou některé užitečné [dotazy k protokolům](../log-query/log-query-overview.md) , které můžete použít s výstrahou protokolu:

#### <a name="high-cpu-on-azure-sql-database"></a>Vysoký procesor v Azure SQL Database

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - Představte si, že nastavení této výstrahy znamená, že se v rámci tohoto řešení streamují základní metriky pro monitorované databáze.
> - Nahraďte hodnotu hodnoty metric cpu_percent hodnotou dtu_consumption_percent, abyste místo toho získali vysoké výsledky DTU.

#### <a name="high-cpu-on-azure-sql-database-elastic-pools"></a>Vysoký procesor v Azure SQL Database elastických fondech

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - Představte si, že nastavení této výstrahy znamená, že se v rámci tohoto řešení streamují základní metriky pro monitorované databáze.
> - Nahraďte hodnotu hodnoty metric cpu_percent hodnotou dtu_consumption_percent, abyste místo toho získali vysoké výsledky DTU.

#### <a name="azure-sql-database-storage-in-average-above-95-in-the-last-1-hr"></a>Azure SQL Database úložiště v průměru nad 95% za poslední 1 hod.

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
> - Představte si, že nastavení této výstrahy znamená, že se v rámci tohoto řešení streamují základní metriky pro monitorované databáze.
> - Tento dotaz vyžaduje nastavení pravidla výstrahy, aby se aktivovala výstraha, když existují výsledky (> 0 výsledků) z dotazu, a označuje, že podmínka existuje v některých databázích. Výstupem je seznam databázových prostředků, které jsou nad storage_threshold ve time_range definovaném.
> - Výstupem je seznam databázových prostředků, které jsou nad storage_threshold ve time_range definovaném.

#### <a name="alert-on-intelligent-insights"></a>Upozornění na inteligentní přehledy

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
> - Představte si, že nastavení této výstrahy znamená, že monitorované databáze streamují protokol SQLInsights Diagnostics do řešení.
> - Aby se předešlo duplicitním výsledkům, tento dotaz vyžaduje, aby se nastavilo pravidlo upozornění, které se má spustit se stejnou frekvencí jako alert_run_interval. Pravidlo by mělo být nastavené tak, aby výstraha v případě existence výsledků (> 0 výsledků) z dotazu.
> - Přizpůsobte alert_run_interval a určete časový rozsah, který se má ověřit, jestli se podmínka nevyskytla v databázích nakonfigurovaných na streamování SQLInsights protokolu do řešení.
> - Přizpůsobte insights_string a Zachyťte výstup textu analýzy hlavní příčiny Insights. Jedná se o stejný text, který se zobrazuje v uživatelském rozhraní řešení, které můžete použít z existujících přehledů. Alternativně můžete pomocí následujícího dotazu zobrazit text všech přehledů generovaných v rámci vašeho předplatného. Použijte výstup dotazu k získání jedinečných řetězců pro nastavení výstrah pro přehledy.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instance"></a>Vytváření výstrah pro spravovanou instanci

#### <a name="managed-instance-storage-is-above-90"></a>Úložiště spravované instance je nad 90%.

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
> - Před nastavením této výstrahy je tato výstraha v případě, že monitorovaná spravovaná instance má k řešení povolené streamování protokolu ResourceUsageStats.
> - Tento dotaz vyžaduje nastavení pravidla výstrahy, aby se aktivovala výstraha v případě, že v dotazu existují výsledky (> 0 výsledků), a označuje, že podmínka ve spravované instanci existuje. Výstupem je procento využití úložiště na spravované instanci.

#### <a name="managed-instance-cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>Průměrná spotřeba procesoru spravované instance je nad 95% za poslední 1 hod.

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
> - Před nastavením této výstrahy je tato výstraha v případě, že monitorovaná spravovaná instance má k řešení povolené streamování protokolu ResourceUsageStats.
> - Tento dotaz vyžaduje nastavení pravidla výstrahy, aby se aktivovala výstraha v případě, že v dotazu existují výsledky (> 0 výsledků), a označuje, že podmínka ve spravované instanci existuje. Výstupem je průměrné procento využití procesoru v definovaném období ve spravované instanci.

### <a name="pricing"></a>Ceny

I když je řešení bezplatné, bude se používat spotřeba diagnostiky nad rámec volných jednotek přidělených k příjmu dat. Další informace najdete v tématu [Log Analytics ceny](https://azure.microsoft.com/pricing/details/monitor). Bezplatné jednotky poskytované při příjmu dat umožňují bezplatné monitorování několika databází každý měsíc. Mějte na paměti, že další aktivní databáze s těžšími úlohami ingestují více dat oproti nečinným databázím. Spotřebu příjmu dat můžete v řešení snadno monitorovat tak, že v navigační nabídce Azure SQL Analytics vyberete pracovní prostor OMS a pak vyberete využití a odhadované náklady.

## <a name="next-steps"></a>Další kroky

- Použijte [dotazy protokolu](../log-query/log-query-overview.md) v Azure monitor k zobrazení podrobných dat SQL Azure.
- [Vytvořte si vlastní řídicí panely](../learn/tutorial-logs-dashboards.md) zobrazující data Azure SQL.
- [Vytváření výstrah](../platform/alerts-overview.md) při výskytu určitých událostí Azure SQL.
