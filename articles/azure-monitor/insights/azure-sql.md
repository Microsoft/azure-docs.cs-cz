---
title: Azure SQL Analytics řešení v Azure Monitor | Microsoft Docs
description: Řešení Azure SQL Analytics vám pomůže spravovat databáze SQL Azure.
ms.subservice: logs
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 09/19/2020
ms.reviewer: carlrab
ms.openlocfilehash: 0015138f4da9f66e2f9148e468dd1b5543ae0c4b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397075"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Monitorování Azure SQL Database pomocí Azure SQL Analytics (Preview)

![Symbol Azure SQL Analytics](./media/azure-sql/azure-sql-symbol.png)

Azure SQL Analytics je pokročilé řešení monitorování cloudu pro monitorování výkonu všech databází SQL Azure ve velkém měřítku a napříč několika předplatnými v jednom zobrazení. Azure SQL Analytics shromažďuje a vizualizuje klíčové metriky výkonu pomocí integrovaných inteligentních funkcí pro řešení potíží s výkonem.

Pomocí těchto shromážděných metrik můžete vytvořit vlastní pravidla monitorování a výstrahy. Azure SQL Analytics vám pomůže identifikovat problémy v každé vrstvě zásobníku vaší aplikace. Pomocí diagnostických metrik Azure spolu s zobrazeními Azure Monitor prezentuje data o všech databázích SQL Azure v jednom pracovním prostoru Log Analytics. Azure Monitor pomáhá shromažďovat, korelovat a vizualizovat strukturovaná a nestrukturovaná data.

Praktické přehledy o používání řešení Azure SQL Analytics a typických scénářích použití najdete ve vloženém videu:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Připojené zdroje

Azure SQL Analytics je pouze cloudové řešení monitorování, které podporuje streamování diagnostické telemetrie pro všechny vaše databáze SQL Azure. Vzhledem k tomu, že Azure SQL Analytics nepoužívá agenty pro připojení k Azure Monitor, nepodporuje monitorování SQL Server hostovaných místně nebo na virtuálních počítačích.

| Připojený zdroj | Podporováno | Description |
| --- | --- | --- |
| [Nastavení diagnostiky](../platform/diagnostic-settings.md) | **Ano** | Data metriky a protokolu Azure se odesílají do protokolů Azure Monitor přímo pomocí Azure. |
| [Účet úložiště Azure](../platform/resource-logs.md#send-to-log-analytics-workspace) | No | Azure Monitor nečtou data z účtu úložiště. |
| [Agenti systému Windows](../platform/agent-windows.md) | No | Přímo agenti Windows nepoužívají Azure SQL Analytics. |
| [Agenti systému Linux](../learn/quick-collect-linux-computer.md) | No | Přímo agenti Linux nepoužívají Azure SQL Analytics. |
| [Skupina pro správu nástroje System Center Operations Manager](../platform/om-agents.md) | No | Azure SQL Analytics nepoužívá přímé připojení od Operations Managerho agenta k Azure Monitor. |

## <a name="azure-sql-analytics-options"></a>Možnosti Azure SQL Analytics

Následující tabulka obsahuje přehled podporovaných možností pro dvě verze řídicího panelu Azure SQL Analytics, jeden pro Azure SQL Database a druhý pro databáze spravované instance Azure SQL.

| Možnost Azure SQL Analytics | Description | Podpora SQL Database | Podpora SQL Managed Instance |
| --- | ------- | ----- | ----- |
| Prostředek podle typu | Perspektiva, která počítá všechny monitorované prostředky. | Yes | Yes |
| Insights | Poskytuje hierarchické procházení k podrobnostem Intelligent Insights do výkonu. | Yes | Yes |
| chyby | Poskytuje hierarchické procházení k podrobnostem o chybách SQL, k nimž došlo v databázích. | Yes | Yes |
| Časové limity | Poskytuje hierarchické procházení podrobností k časovým limitům SQL, k nimž došlo v databázích. | Yes | No |
| Blokování | Poskytuje hierarchické procházení do bloků SQL, ke kterým došlo v databázích. | Yes | No |
| Databáze čeká | Poskytuje hierarchickou hloubkovou statistiku SQL na úrovni databáze. Obsahuje souhrny celkové čekací doby a dobu čekání na typ čekání. |Yes | No |
| Doba trvání dotazu | Poskytuje hierarchické procházení k podrobnostem do statistik provádění dotazů, jako je například doba trvání dotazu, využití procesoru, použití dat v/v, protokol použití v/v. | Yes | Yes |
| Query waits (Čekání dotazu) | Poskytuje hierarchické procházení k podrobnostem do statistiky čekání na dotaz podle kategorie čekání. | Yes | Yes |

## <a name="configuration"></a>Konfigurace

Pomocí postupu popsaného v tématu [přidání Azure monitor řešení z galerie řešení](./solutions.md) přidejte Azure SQL Analytics (Preview) do pracovního prostoru Log Analytics.

### <a name="configure-azure-sql-database-to-stream-diagnostics-telemetry"></a>Konfigurace Azure SQL Database ke streamování diagnostiky diagnostiky

Po vytvoření Azure SQL Analytics řešení ve vašem pracovním prostoru musíte **nakonfigurovat každý** prostředek, který chcete monitorovat, aby se jeho diagnostická telemetrie mohla Azure SQL Analytics vysílat. Postupujte podle podrobných pokynů na této stránce:

- Povolí Azure Diagnostics pro vaši databázi [streamovat telemetrii diagnostiky do Azure SQL Analytics](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

Na výše uvedené stránce najdete taky pokyny, jak povolit podporu monitorování více předplatných Azure z jednoho Azure SQL Analytics pracovního prostoru jako jediného podokna skla.

## <a name="using-azure-sql-analytics"></a>Použití Azure SQL Analytics

Když do svého pracovního prostoru přidáte Azure SQL Analytics, přidá se do pracovního prostoru Azure SQL Analytics dlaždice a zobrazí se v přehledu. Vyberte odkaz Zobrazit souhrn a načtěte obsah dlaždice.

![Dlaždice souhrnu Azure SQL Analytics](./media/azure-sql/azure-sql-sol-tile-01.png)

Po načtení se na dlaždici zobrazí počet databází a elastických fondů v SQL Database a instancích a databázích instancí ve spravované instanci SQL, ze které Azure SQL Analytics přijímá telemetrii diagnostiky.

![Dlaždice Azure SQL Analytics](./media/azure-sql/azure-sql-sol-tile-02.png)

Azure SQL Analytics poskytuje dvě samostatná zobrazení – jednu pro monitorování SQL Database a další zobrazení pro monitorování spravované instance SQL.

Chcete-li zobrazit řídicí panel monitorování Azure SQL Analytics pro SQL Database, klikněte na horní část dlaždice. Chcete-li zobrazit řídicí panel monitorování Azure SQL Analytics pro spravovanou instanci SQL, klikněte na spodní část dlaždice.

### <a name="viewing-azure-sql-analytics-data"></a>Zobrazení Azure SQL Analytics dat

Řídicí panel obsahuje přehled všech databází, které jsou monitorovány pomocí různých perspektiv. Aby bylo možné pracovat s různými perspektivami, musíte povolit správné metriky nebo protokoly na svých prostředcích SQL pro streamování do Log Analytics pracovního prostoru.

Pokud se některé metriky nebo protokoly nestreamují do Azure Monitor, dlaždice v Azure SQL Analytics nejsou naplněny informacemi o monitorování.

### <a name="sql-database-view"></a>Zobrazení SQL Database

Po výběru dlaždice Azure SQL Analytics pro databázi se zobrazí řídicí panel monitorování.

![Snímek obrazovky, který zobrazuje řídicí panel monitorování.](./media/azure-sql/azure-sql-sol-overview.png)

Když vyberete některou dlaždici, otevře se v konkrétní perspektivě sestava s podrobnostmi. Po výběru perspektivy se otevře sestava procházení sestav.

![Snímek obrazovky, který zobrazuje sestavu přechodu k podrobnostem v konkrétní perspektivě.](./media/azure-sql/azure-sql-sol-metrics.png)

Každá perspektiva v tomto zobrazení poskytuje souhrny na úrovni předplatného, serveru, elastického fondu a databáze. Kromě toho Každá perspektiva zobrazuje perspektivu specifickou pro sestavu na pravé straně. Výběr předplatného, serveru, fondu nebo databáze ze seznamu pokračuje v procházení podrobností.

### <a name="sql-managed-instance-view"></a>Zobrazení spravované instance SQL

Po výběru dlaždice Azure SQL Analytics pro databáze se zobrazí řídicí panel monitorování.

![Přehled Azure SQL Analytics](./media/azure-sql/azure-sql-sol-overview-mi.png)

Když vyberete některou dlaždici, otevře se v konkrétní perspektivě sestava s podrobnostmi. Po výběru perspektivy se otevře sestava procházení sestav.

Když vyberete zobrazení spravované instance SQL, zobrazí se podrobnosti o využití instance, databázích instancí a telemetrii u dotazů provedených napříč spravovanou instancí.

![Azure SQL Analytics vypršení časových limitů](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="intelligent-insights-report"></a>Sestava Intelligent Insights

Azure SQL Database [Intelligent Insights](../../azure-sql/database/intelligent-insights-overview.md) vám umožní zjistit, co se děje s výkonem všech databází SQL Azure. Všechny shromážděné Intelligent Insights lze vizuálně rozčlenit a získat z perspektivy Insights.

![Azure SQL Analytics přehledy](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pools-and-database-reports"></a>Elastické fondy a sestavy databáze

Elastické fondy i databáze mají vlastní konkrétní sestavy, které zobrazují všechna data shromážděná pro daný prostředek v zadaném čase.

![Azure SQL Analytics databáze](./media/azure-sql/azure-sql-sol-database.png)

![Elastický fond Azure SQL](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Sestavy dotazů

Po dobu trvání dotazu a dotaz čeká na perspektivu, můžete korelovat výkon libovolného dotazu prostřednictvím sestavy dotazu. Tato sestava porovnává výkon dotazů napříč různými databázemi a usnadňuje rozpoznání databází, které provádějí odpovídající i pomalé dotazy.

![Azure SQL Analytics dotazy](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Oprávnění

Aby bylo možné použít Azure SQL Analytics, musí být uživatelům v Azure uděleno minimální oprávnění role čtenář. Tato role ale neumožňuje uživatelům zobrazit text dotazu nebo provádět žádné akce automatického ladění. Účinnější role v Azure, které umožňují použití Azure SQL Analytics s největším rozsahem je vlastník, přispěvatel, přispěvatel databáze SQL nebo přispěvatel SQL Server. Možná budete chtít zvážit vytvoření vlastní role na portálu s konkrétními oprávněními, která jsou nutná jenom pro použití Azure SQL Analytics, a bez přístupu ke správě jiných prostředků.

### <a name="creating-a-custom-role-in-portal"></a>Vytvoření vlastní role na portálu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

UZNÁVAJÍCE, že některé organizace vynutily přísné ovládací prvky oprávnění v Azure, najdete následující skript PowerShellu, který umožňuje vytvoření vlastní role "operátor monitorování analýzy SQL" v Azure Portal s minimálními oprávněními pro čtení a zápis, která jsou nutná k použití Azure SQL Analytics ve svém plném rozsahu.

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

Analýza dat v Azure SQL Analytics vychází z [Log Analytics jazyka](../log-query/get-started-queries.md) pro vlastní dotazování a vytváření sestav. Vyhledejte popis dostupných dat shromážděných z databázového prostředku pro vlastní dotazování v [metrikách a protokolech, které jsou k dispozici](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md#metrics-and-logs-available).

Automatizované upozorňování v Azure SQL Analytics jsou založené na zápisu Log Analytics dotazu, který aktivuje výstrahu při splnění podmínky. Níže naleznete několik příkladů Log Analytics dotazů, na kterých je možné nastavit upozorňování v Azure SQL Analytics.

### <a name="creating-alerts-for-azure-sql-database"></a>Vytváření upozornění pro Azure SQL Database

Můžete snadno [vytvářet výstrahy](../platform/alerts-metric.md) s daty, která přicházejí z Azure SQL Databasech prostředků. Tady jsou některé užitečné [dotazy k protokolům](../log-query/log-query-overview.md) , které můžete použít s výstrahou protokolu:

#### <a name="high-cpu"></a>Vysoké využití procesoru

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - Představte si, že nastavení této výstrahy je v případě, že se pro monitorované databáze streamují základní metriky na Azure SQL Analytics.
> - Nahraďte hodnotu hodnoty metric cpu_percent hodnotou dtu_consumption_percent, abyste místo toho získali vysoké výsledky DTU.

#### <a name="high-cpu-on-elastic-pools"></a>Vysoký procesor u elastických fondů

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - Představte si, že nastavení této výstrahy je v případě, že se pro monitorované databáze streamují základní metriky na Azure SQL Analytics.
> - Nahraďte hodnotu hodnoty metric cpu_percent hodnotou dtu_consumption_percent, abyste místo toho získali vysoké výsledky DTU.

#### <a name="storage-in-average-above-95-in-the-last-1-hr"></a>Úložiště v průměru nad 95% za poslední 1 hod.

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
>
> - Představte si, že nastavení této výstrahy je v případě, že se pro monitorované databáze streamují základní metriky na Azure SQL Analytics.
> - Tento dotaz vyžaduje nastavení pravidla výstrahy, aby se aktivovala výstraha, když existují výsledky (> 0 výsledků) z dotazu, a označuje, že podmínka existuje v některých databázích. Výstupem je seznam databázových prostředků, které jsou nad storage_threshold v rámci time_range definované.
> - Výstupem je seznam databázových prostředků, které jsou nad storage_threshold v rámci time_range definované.

#### <a name="alert-on-intelligent-insights"></a>Upozornění na inteligentní přehledy

> [!IMPORTANT]
> V případě, že databáze pracuje dobře a že nebyla vygenerována žádná Intelligent Insights, tento dotaz selže s chybovou zprávou: Nepodařilo se přeložit skalární výraz s názvem "rootCauseAnalysis_s". Toto chování se očekává pro všechny případy, kdy pro databázi neexistují žádné inteligentní přehledy.

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
>
> - Představte si, že nastavení této výstrahy znamená, že monitorovaný protokol SQLInsights log streamování databází se Azure SQL Analytics.
> - Aby se zabránilo duplicitním výsledkům, tento dotaz vyžaduje, aby se nastavilo pravidlo upozornění, které se má spustit se stejnou frekvencí jako alert_run_interval. Pravidlo by mělo být nastavené tak, aby výstraha v případě existence výsledků (> 0 výsledků) z dotazu.
> - Přizpůsobte alert_run_interval a určete časový rozsah, který bude kontrolovat, jestli se v databázích nakonfigurovaných pro streamování protokolu SQLInsights do Azure SQL Analytics zapsala podmínka.
> - Přizpůsobte insights_string pro zachycení výstupu textu analýzy hlavní příčiny Insights. Jedná se o stejný text, který se zobrazuje v uživatelském rozhraní Azure SQL Analytics, které můžete použít z existujících přehledů. Alternativně můžete pomocí následujícího dotazu zobrazit text všech přehledů generovaných v rámci vašeho předplatného. Použijte výstup dotazu k získání jedinečných řetězců pro nastavení výstrah pro přehledy.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-sql-managed-instance"></a>Vytváření výstrah pro spravovanou instanci SQL

#### <a name="storage-is-above-90"></a>Úložiště je nad 90%

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
>
> - Představte si nastavení této výstrahy znamená, že monitorovaná spravovaná instance má k dispozici streamování protokolu ResourceUsageStats s povoleným Azure SQL Analytics.
> - Tento dotaz vyžaduje nastavení pravidla výstrahy, aby se aktivovala výstraha v případě, že v dotazu existují výsledky (> 0 výsledků), a označuje, že podmínka ve spravované instanci existuje. Výstupem je procento využití úložiště na spravované instanci.

#### <a name="cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>Průměrná spotřeba procesoru je nad 95% za poslední 1 hod.

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
>
> - Představte si nastavení této výstrahy znamená, že monitorovaná spravovaná instance má k dispozici streamování protokolu ResourceUsageStats s povoleným Azure SQL Analytics.
> - Tento dotaz vyžaduje nastavení pravidla výstrahy, aby se aktivovala výstraha v případě, že v dotazu existují výsledky (> 0 výsledků), a označuje, že podmínka ve spravované instanci existuje. Výstupem je průměrné procento využití procesoru v definovaném období ve spravované instanci.

### <a name="pricing"></a>Ceny

I když je Azure SQL Analytics zdarma, používá se spotřeba diagnostiky nad rámec bezplatných jednotek pro příjem dat. Další informace najdete v článku [Log Analytics ceny](https://azure.microsoft.com/pricing/details/monitor). Bezplatné jednotky poskytované při příjmu dat umožňují bezplatné monitorování několika databází každý měsíc. Další aktivní databáze s těžšími úlohami ingestují více dat oproti nečinným databázím. Spotřebu příjmu dat v Azure SQL Analytics můžete snadno monitorovat tak, že v navigační nabídce Azure SQL Analytics vyberete pracovní prostor OMS a pak vyberete využití a odhadované náklady.

## <a name="next-steps"></a>Další kroky

- Použijte [dotazy protokolu](../log-query/log-query-overview.md) v Azure monitor k zobrazení podrobných dat SQL Azure.
- [Vytvořte si vlastní řídicí panely](../learn/tutorial-logs-dashboards.md) zobrazující data Azure SQL.
- [Vytváření výstrah](../platform/alerts-overview.md) při výskytu určitých událostí Azure SQL.

