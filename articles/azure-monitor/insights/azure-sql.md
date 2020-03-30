---
title: Řešení Azure SQL Analytics ve službě Azure Monitor | Dokumenty společnosti Microsoft
description: Řešení Azure SQL Analytics vám pomůže spravovat databáze Azure SQL
ms.subservice: logs
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 02/21/2020
ms.reviewer: carlrab
ms.openlocfilehash: 921a05c4dc6c1d5cfa663ac71b469573b8f1925b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275461"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Monitorování Azure SQL Database pomocí Azure SQL Analytics (Preview)

![Symbol Azure SQL Analytics](./media/azure-sql/azure-sql-symbol.png)

Azure SQL Analytics je pokročilé cloudové monitorování řešení pro sledování výkonu všech databází Azure SQL ve velkém měřítku a napříč několika předplatnými v jednom zobrazení. Azure SQL Analytics shromažďuje a vizualizuje klíčové metriky výkonu s integrovanou inteligencí pro řešení potíží s výkonem.

Pomocí těchto shromážděných metrik můžete vytvořit vlastní pravidla monitorování a výstrahy. Azure SQL Analytics vám pomůže identifikovat problémy v každé vrstvě zásobníku aplikací. Používá metriky Azure Diagnostic spolu se zobrazeními Azure Monitor u prezentovat data o všech databázích Azure SQL v jednom pracovním prostoru Log Analytics. Azure Monitor vám pomůže shromažďovat, korelovat a vizualizovat strukturovaná a nestrukturovaná data.

Praktický přehled o používání řešení Azure SQL Analytics a o typických scénářích využití najdete v vloženém videu:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Připojené zdroje

Azure SQL Analytics je cloudové monitorování řešení podporující streamování telemetrie diagnostiky pro všechny vaše databáze Azure SQL. Vzhledem k tomu, že Azure SQL Analytics nepoužívá agenty pro připojení k Azure Monitoru, nepodporuje monitorování SQL Serveru hostovaného místně nebo ve virtuálních počítačích.

| Připojený zdroj | Podporuje se | Popis |
| --- | --- | --- |
| [Nastavení diagnostiky](../platform/diagnostic-settings.md) | **Ano** | Azure metriky a data protokolu se posílají do protokolů Azure Monitor přímo azure. |
| [Účet služby Azure Storage](../platform/collect-azure-metrics-logs.md) | Ne | Azure Monitor nečte data z účtu úložiště. |
| [Agenti systému Windows](../platform/agent-windows.md) | Ne | Přímá agenti Windows se azure SQL Analytics nepoužívají. |
| [Agenti systému Linux](../learn/quick-collect-linux-computer.md) | Ne | Agenti Direct Linuxu se azure SQL Analytics nepoužívají. |
| [Skupina pro správu nástroje System Center Operations Manager](../platform/om-agents.md) | Ne | Azure SQL Analytics nepoužívá přímé připojení agenta Operations Manageru k Azure Monitoru. |

## <a name="azure-sql-analytics-options"></a>Možnosti Azure SQL Analytics

Níže uvedená tabulka popisuje podporované možnosti pro dvě verze řídicího panelu Azure SQL Analytics, jednu pro jednu a sdruženou databázi a elastické fondy a druhou pro spravované instance a databáze instancí.

| Azure SQL Analytics, možnost | Popis | Podpora jedné a sdružené databáze a elastických fondů | Podpora databáze spravovaných instancí a instancí |
| --- | ------- | ----- | ----- |
| Zdroj podle typu | Perspektiva, která počítá všechny sledované zdroje. | Ano | Ano |
| Insights | Poskytuje hierarchické podrobné informace o inteligentním přehledu výkonu. | Ano | Ano |
| chyby | Poskytuje hierarchické přechodka k chybám SQL, ke kterým došlo v databázích. | Ano | Ano |
| Časové limity | Poskytuje hierarchické přechodka na časové osy SQL, ke kterým došlo v databázích. | Ano | Ne |
| Blokování | Poskytuje hierarchické přechodka k blokování SQL, ke kterým došlo v databázích. | Ano | Ne |
| Databáze čeká | Poskytuje hierarchické podrobnosti o statistikách čekání SQL na úrovni databáze. Zahrnuje souhrny celkové čekací doby a čekací doby na typ čekání. |Ano | Ne |
| Doba trvání dotazu | Poskytuje hierarchické podrobnosti o statistikách provádění dotazů, jako je doba trvání dotazu, využití procesoru, využití vozího vplatnosti, využití protokolu VO. | Ano | Ano |
| Query waits (Čekání dotazu) | Poskytuje hierarchické podrobnosti o statistikách čekání dotazu podle kategorie čekání. | Ano | Ano |

## <a name="configuration"></a>Konfigurace

Pomocí procesu popsaného v [části Přidání řešení Azure Monitor z Galerie řešení](../../azure-monitor/insights/solutions.md) přidejte Azure SQL Analytics (Preview) do pracovního prostoru Log Analytics.

### <a name="configure-azure-sql-databases-to-stream-diagnostics-telemetry"></a>Konfigurace databází Azure SQL pro telemetrii diagnostiky datových proudů

Jakmile vytvoříte řešení Azure SQL Analytics ve vašem pracovním prostoru, budete muset **nakonfigurovat každý** prostředek, který chcete monitorovat, aby streamoval jeho telemetrii diagnostiky do Azure SQL Analytics. Postupujte podle podrobných pokynů na této stránce:

- Povolte Diagnostiku Azure pro vaši databázi Azure SQL a [streamujte telemetrii diagnostiky do Azure SQL Analytics](../../sql-database/sql-database-metrics-diag-logging.md).

Výše uvedená stránka také obsahuje pokyny k povolení podpory pro monitorování více předplatných Azure z jednoho pracovního prostoru Azure SQL Analytics jako jediné podokno skla.

## <a name="using-azure-sql-analytics"></a>Používání Azure SQL Analytics

Když přidáte Azure SQL Analytics do svého pracovního prostoru, dlaždice Azure SQL Analytics se přidá do vašeho pracovního prostoru a zobrazí se v přehledu. Chcete-li načíst obsah dlaždice, vyberte zobrazit odkaz Souhrn.

![Dlaždice souhrnu Azure SQL Analytics](./media/azure-sql/azure-sql-sol-tile-01.png)

Po načtení dlaždice zobrazuje počet jednoa sdružených databází, elastických fondů, spravovaných instancí a databází spravovaných instancí, ze kterých Azure SQL Analytics přijímá telemetrii diagnostiky.

![Dlaždice Azure SQL Analytics](./media/azure-sql/azure-sql-sol-tile-02.png)

Azure SQL Analytics poskytuje dvě samostatná zobrazení – jedno pro monitorování jednotlivých databází a sdružených databází a elastických fondů a druhé zobrazení pro monitorování spravovaných instancí a databází instancí.

Chcete-li zobrazit řídicí panel monitorování Azure SQL Analytics pro jednu a sdruženou databázi a elastické fondy, klikněte na horní část dlaždice. Chcete-li zobrazit řídicí panel monitorování Azure SQL Analytics pro spravované instance a databáze instancí, klikněte na spodní část dlaždice.

### <a name="viewing-azure-sql-analytics-data"></a>Zobrazení dat Azure SQL Analytics

Řídicí panel obsahuje přehled všech databází, které jsou monitorovány prostřednictvím různých perspektiv. Pro různé perspektivy pracovat, musíte povolit správné metriky nebo protokoly na prostředky SQL, které mají být vysílané do pracovního prostoru Log Analytics.

Pokud některé metriky nebo protokoly nejsou vyponožené do Azure Monitoru, dlaždice v Azure SQL Analytics nejsou naplněny informace mise monitorování.

### <a name="single-and-pooled-databases-and-elastic-pools-view"></a>Zobrazení jedné a sdružené databáze a elastické fondy

Po výběru dlaždice Azure SQL Analytics pro databázi se zobrazí řídicí panel monitorování.

![Přehled Azure SQL Analytics](./media/azure-sql/azure-sql-sol-overview.png)

Výběrem libovolné dlaždice otevřete sestavu podrobností do konkrétní perspektivy. Po výběru perspektivy se otevře sestava podrobností.

![Časové výčasové opojení Azure SQL Analytics](./media/azure-sql/azure-sql-sol-metrics.png)

Každá perspektiva v tomto zobrazení poskytuje souhrny na úrovni předplatného, serveru, elastického fondu a databáze. Každá perspektiva navíc ukazuje perspektivu specifickou pro zprávu vpravo. Výběr předplatného, serveru, fondu nebo databáze ze seznamu pokračuje v přechodu k podrobnostem.

### <a name="managed-instance-and-instances-databases-view"></a>Zobrazení databází spravovaných instancí a instancí

Po výběru dlaždice Azure SQL Analytics pro databáze se zobrazí řídicí panel monitorování.

![Přehled Azure SQL Analytics](./media/azure-sql/azure-sql-sol-overview-mi.png)

Výběrem libovolné dlaždice otevřete sestavu podrobností do konkrétní perspektivy. Po výběru perspektivy se otevře sestava podrobností.

Výběr zobrazení spravované instance, zobrazí podrobnosti o využití spravované instance, databáze, které obsahuje, a telemetrie na dotazy prováděné v rámci instance.

![Časové výčasové opojení Azure SQL Analytics](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="intelligent-insights-report"></a>Sestava Inteligentní přehledy

Inteligentní [přehledy](../../sql-database/sql-database-intelligent-insights.md) Azure SQL Database vám umožní zjistit, co se děje s výkonem všech databází Azure SQL. Všechny shromážděné inteligentní přehledy lze vizualizovat a přistupovat k nim prostřednictvím perspektivy přehledů.

![Přehledy Azure SQL Analytics](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pools-and-database-reports"></a>Elastické fondy a sestavy databází

Elastické fondy i databáze mají své vlastní konkrétní sestavy, které zobrazují všechna data shromážděná pro prostředek v zadaném čase.

![Databáze Azure SQL Analytics](./media/azure-sql/azure-sql-sol-database.png)

![Elastický fond Azure SQL](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Sestavy dotazů

Prostřednictvím doby trvání dotazu a dotaz čeká perspektivy, můžete korelovat výkon libovolného dotazu prostřednictvím sestavy dotazu. Tato sestava porovnává výkon dotazu v různých databázích a usnadňuje určení databází, které provádějí vybraný dotaz dobře oproti databázím, které jsou pomalé.

![Dotazy Azure SQL Analytics](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Oprávnění

Aby bylo možné používat Azure SQL Analytics, uživatelé musí mít v Azure minimální oprávnění k roli čtečky. Tato role však neumožňuje uživatelům zobrazit text dotazu nebo provádět akce automatického ladění. Další tolerantní role v Azure, které umožňují používání Azure SQL Analytics v plném rozsahu jsou vlastník, přispěvatel, SQL DB přispěvatel nebo SQL Server Přispěvatel. Můžete také zvážit vytvoření vlastní role na portálu s konkrétními oprávněními, která jsou vyžadována pouze pro použití Azure SQL Analytics a bez přístupu ke správě jiných prostředků.

### <a name="creating-a-custom-role-in-portal"></a>Vytvoření vlastní role na portálu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Uznávajíce, že některé organizace vynucují přísné kontroly oprávnění v Azure, najděte následující skript Prostředí PowerShell, který umožňuje vytvoření vlastní role "SQL Analytics Monitoring Operator" na webu Azure Portal s minimálními oprávněními pro čtení a zápis, která jsou vyžadována azure sql analytics v plném rozsahu.

Nahraďte "{SubscriptionId}" v níže uvedeném skriptu id předplatného Azure a spusťte skript přihlášený jako vlastník nebo přispěvatel role v Azure.

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

Po vytvoření nové role přiřaďte tuto roli každému uživateli, který je potřeba udělit vlastní oprávnění k používání Azure SQL Analytics.

## <a name="analyze-data-and-create-alerts"></a>Analýza dat a vytváření výstrah

Analýza dat v Azure SQL Analytics je založená na [jazyce Log Analytics](../log-query/get-started-queries.md) pro vlastní dotazování a vytváření sestav. Najděte popis dostupných dat shromážděných z databázového prostředku pro vlastní dotazování v [metrikách a protokolech, které jsou k dispozici](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available).

Automatické upozorňování ve službě Azure SQL Analytics je založeno na napsání dotazu Analýzy protokolů, který aktivuje výstrahu při splnění podmínky. Níže naleznete několik příkladů v dotazech Log Analytics, na kterých lze nastavení výstrah v Azure SQL Analytics.

### <a name="creating-alerts-for-azure-sql-database"></a>Vytváření výstrah pro Azure SQL Database

Můžete snadno [vytvářet výstrahy](../platform/alerts-metric.md) s daty pocházejícími z prostředků Azure SQL Database. Zde jsou některé užitečné [dotazy protokolu,](../log-query/log-query-overview.md) které můžete použít s výstrahou protokolu:

#### <a name="high-cpu-on-azure-sql-database"></a>Vysoký procesor v azure sql databázi

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
> - Předběžný požadavek na nastavení této výstrahy je, že monitorované databáze streamují základní metriky do Azure SQL Analytics.
> - Nahraďte hodnotu MetricName cpu_percent dtu_consumption_percent, abyste místo toho získali vysoké výsledky DTU.

#### <a name="high-cpu-on-azure-sql-database-elastic-pools"></a>Vysoké úložiště procesoru v azure SQL database

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
> - Předběžný požadavek na nastavení této výstrahy je, že monitorované databáze streamují základní metriky do Azure SQL Analytics.
> - Nahraďte hodnotu MetricName cpu_percent dtu_consumption_percent, abyste místo toho získali vysoké výsledky DTU.

#### <a name="azure-sql-database-storage-in-average-above-95-in-the-last-1-hr"></a>Úložiště Azure SQL Database v průměru nad 95 % za poslední 1 hodinu

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
> - Předběžný požadavek na nastavení této výstrahy je, že monitorované databáze streamují základní metriky do Azure SQL Analytics.
> - Tento dotaz vyžaduje pravidlo výstrahy, které má být nastaveno tak, aby se spustilo výstraha, pokud existují výsledky (výsledky > 0) z dotazu, což znamená, že podmínka existuje v některých databázích. Výstup je seznam databázových prostředků, které jsou nad storage_threshold v rámci time_range definované.
> - Výstup je seznam databázových prostředků, které jsou nad storage_threshold v rámci time_range definované.

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
>
> - Předběžný požadavek na nastavení této výstrahy spočívá, že monitorované databáze streamují protokol diagnostiky SQLInsights do Azure SQL Analytics.
> - Tento dotaz vyžaduje, aby bylo pravidlo výstrahy nastaveno tak, aby bylo spuštěno se stejnou frekvencí jako alert_run_interval, aby se zabránilo duplicitním výsledkům. Pravidlo by mělo být nastaveno tak, aby se spustilo výstrahu, pokud existují výsledky (> 0 výsledků) z dotazu.
> - Přizpůsobte alert_run_interval a určete časový rozsah, který chcete zkontrolovat, pokud došlo ke stavu v databázích nakonfigurovaných pro streamování protokolu SQLInsights do Azure SQL Analytics.
> - Přizpůsobte insights_string, abyste zachytili výstup textu analýzy kořenové příčiny přehledů. Jedná se o stejný text zobrazený v ui Azure SQL Analytics, který můžete použít z existujících přehledů. Případně můžete pomocí níže uvedeného dotazu zobrazit text všech přehledů generovaných ve vašem předplatném. Použijte výstup dotazu ke sklizni různých řetězců pro nastavení výstrah na Insights.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instances"></a>Vytváření výstrah pro spravované instance

#### <a name="managed-instance-storage-is-above-90"></a>Úložiště spravovaných instancí je vyšší než 90 %

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
> - Předběžný požadavek na nastavení této výstrahy je, že monitorovaná spravovaná instance má streamování protokolu ResourceUsageStats povoleného pro Azure SQL Analytics.
> - Tento dotaz vyžaduje, aby bylo nastaveno pravidlo výstrahy, které by spustilo výstrahu, pokud z dotazu existují výsledky (> výsledků 0), což znamená, že podmínka existuje ve spravované instanci. Výstup je procento spotřeby úložiště ve spravované instanci.

#### <a name="managed-instance-cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>Průměrná spotřeba procesoru spravované instance je vyšší než 95 % za poslední 1 hodinu

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
> - Předběžný požadavek na nastavení této výstrahy je, že monitorovaná spravovaná instance má streamování protokolu ResourceUsageStats povoleného pro Azure SQL Analytics.
> - Tento dotaz vyžaduje, aby bylo nastaveno pravidlo výstrahy, které by spustilo výstrahu, pokud z dotazu existují výsledky (> výsledků 0), což znamená, že podmínka existuje ve spravované instanci. Výstupem je průměrná spotřeba procenta využití procesoru v definovaném období spravované instance.

### <a name="pricing"></a>Ceny

Zatímco Azure SQL Analytics je zdarma k použití, spotřeba diagnostiky telemetrie nad volné jednotky požití dat přidělené každý měsíc platí, viz [Log Analytics ceny](https://azure.microsoft.com/pricing/details/monitor). Volné jednotky požití dat poskytovaly bezplatné sledování několika databází každý měsíc. Aktivnější databáze s těžší úlohy ingestovat více dat versus nečinnosti databází. Spotřebu přihlašování dat můžete snadno sledovat v Azure SQL Analytics tak, že vyberete OMS Workspace v navigační nabídce Azure SQL Analytics a pak vyberete Využití a odhadované náklady.

## <a name="next-steps"></a>Další kroky

- Pomocí [dotazů protokolu](../log-query/log-query-overview.md) v Azure Monitoru zobrazte podrobná data Azure SQL.
- [Vytvořte si vlastní řídicí panely](../learn/tutorial-logs-dashboards.md) zobrazující data Azure SQL.
- [Vytvořte výstrahy,](../platform/alerts-overview.md) když dojde k konkrétní události Azure SQL.
