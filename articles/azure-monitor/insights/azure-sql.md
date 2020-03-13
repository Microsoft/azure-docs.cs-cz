---
title: Azure SQL Analytics řešení v Azure Monitor | Microsoft Docs
description: Řešení Azure SQL Analytics vám usnadní správu vašich databází Azure SQL
ms.subservice: logs
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 02/21/2020
ms.reviewer: carlrab
ms.openlocfilehash: 921a05c4dc6c1d5cfa663ac71b469573b8f1925b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275461"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Monitorování Azure SQL Database pomocí Azure SQL Analytics (Preview)

![Azure SQL Analytics symbol](./media/azure-sql/azure-sql-symbol.png)

Azure SQL Analytics je pokročilé řešení monitorování cloudu pro monitorování výkonu všech databází SQL Azure ve velkém měřítku a napříč několika předplatnými v jednom zobrazení. Azure SQL Analytics shromažďuje a vizualizuje klíčové metriky výkonu pomocí integrovaných inteligentních funkcí pro řešení potíží s výkonem.

Pomocí těchto shromážděných metrik můžete vytvořit vlastní pravidla monitorování a výstrahy. Azure SQL Analytics vám pomůže identifikovat problémy v každé vrstvě zásobníku vaší aplikace. Pomocí diagnostických metrik Azure spolu s zobrazeními Azure Monitor prezentuje data o všech databázích SQL Azure v jednom pracovním prostoru Log Analytics. Azure Monitor pomáhá shromažďovat, korelovat a vizualizovat strukturovaná a nestrukturovaná data.

Praktické přehled o použití řešení Azure SQL Analytics a typické scénáře využití najdete v části vložené video:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Připojené zdroje

Azure SQL Analytics je pouze cloudové řešení monitorování, které podporuje streamování diagnostické telemetrie pro všechny vaše databáze SQL Azure. Vzhledem k tomu, že Azure SQL Analytics nepoužívá agenty pro připojení k Azure Monitor, nepodporuje monitorování SQL Server hostovaných místně nebo na virtuálních počítačích.

| Připojený zdroj | Podporuje se | Popis |
| --- | --- | --- |
| [Nastavení diagnostiky](../platform/diagnostic-settings.md) | **Ano** | Data metriky a protokolu Azure se odesílají do protokolů Azure Monitor přímo pomocí Azure. |
| [Účet služby Azure Storage](../platform/collect-azure-metrics-logs.md) | Ne | Azure Monitor nečtou data z účtu úložiště. |
| [Agenti systému Windows](../platform/agent-windows.md) | Ne | Přímo agenti Windows nepoužívají Azure SQL Analytics. |
| [Agenti systému Linux](../learn/quick-collect-linux-computer.md) | Ne | Přímo agenti Linux nepoužívají Azure SQL Analytics. |
| [System Center Operations Manager skupina pro správu](../platform/om-agents.md) | Ne | Azure SQL Analytics nepoužívá přímé připojení od Operations Managerho agenta k Azure Monitor. |

## <a name="azure-sql-analytics-options"></a>Možnosti Azure SQL Analytics

Níže uvedená tabulka obsahuje přehled podporovaných možností pro dvě verze řídicího panelu Azure SQL Analytics, jeden pro databáze s jedním a fondem a elastické fondy a druhý pro spravované instance a databáze instancí.

| Možnost Azure SQL Analytics | Popis | Podpora pro jednotlivé a sdružené databáze a elastické fondy | Podpora databáze spravované instance a instance |
| --- | ------- | ----- | ----- |
| Prostředek podle typu | Perspektivy, která vrátí všechny prostředky, které jsou monitorovány. | Ano | Ano |
| Přehledy | Poskytuje hierarchické procházení na užitečné přehledy o výkonu. | Ano | Ano |
| Chyby | Obsahuje hierarchické procházení na chyby SQL, k nimž došlo v databázích. | Ano | Ano |
| Vypršení časových limitů | Poskytuje hierarchické procházení do vypršení časových limitů SQL, který se stalo v databázích. | Ano | Ne |
| Blokování | Poskytuje hierarchické procházení do SQL blokování, který se stalo v databázích. | Ano | Ne |
| Databáze čeká | Poskytuje hierarchické procházení do SQL statistiky čekání na úrovni databáze. Obsahuje souhrnné informace o celkový čas čekání a čekací doba čekání typu. |Ano | Ne |
| Doba trvání dotazu | Poskytuje hierarchické procházení do statistiky provádění dotazu, jako je doba trvání dotazu, využití procesoru, využití datových v/v, využití protokolovacích v/v. | Ano | Ano |
| Dotaz čeká | Poskytuje hierarchické procházení do statistiky čekání dotazu podle kategorie čekání. | Ano | Ano |

## <a name="configuration"></a>Konfigurace

Pomocí postupu popsaného v tématu [přidání Azure monitor řešení z galerie řešení](../../azure-monitor/insights/solutions.md) přidejte Azure SQL Analytics (Preview) do pracovního prostoru Log Analytics.

### <a name="configure-azure-sql-databases-to-stream-diagnostics-telemetry"></a>Konfigurace databází Azure SQL pro streamování diagnostiky diagnostiky

Po vytvoření Azure SQL Analytics řešení ve vašem pracovním prostoru musíte **nakonfigurovat každý** prostředek, který chcete monitorovat, aby se jeho diagnostická telemetrie mohla Azure SQL Analytics vysílat. Postupujte podle podrobných pokynů na této stránce:

- Povolte Azure Diagnostics služby Azure SQL Database, abyste mohli [streamovat diagnostiku diagnostiky do Azure SQL Analytics](../../sql-database/sql-database-metrics-diag-logging.md).

Výše uvedená stránka také obsahuje pokyny k povolení monitorování více předplatných Azure z jednoho pracovního prostoru Azure SQL Analytics jako podokně ze skla.

## <a name="using-azure-sql-analytics"></a>Použití Azure SQL Analytics

Když do svého pracovního prostoru přidáte Azure SQL Analytics, přidá se do pracovního prostoru Azure SQL Analytics dlaždice a zobrazí se v přehledu. Vyberte odkaz Zobrazit souhrn a načtěte obsah dlaždice.

![Dlaždice souhrnu Azure SQL Analytics](./media/azure-sql/azure-sql-sol-tile-01.png)

Po načtení se na dlaždici zobrazí počet databází s jedním a fondem, elastické fondy, spravované instance a databáze spravované instance, ze kterých Azure SQL Analytics přijímá telemetrii diagnostiky.

![Dlaždici Azure SQL Analytics](./media/azure-sql/azure-sql-sol-tile-02.png)

Azure SQL Analytics poskytuje dvě samostatná zobrazení – jednu pro monitorování izolovaných databází a databáze ve fondu a elastických fondů a další zobrazení pro monitorování spravovaných instancí a databází instancí.

Pokud chcete zobrazit řídicí panel monitorování Azure SQL Analytics pro jednu databázi a elastické fondy, klikněte na horní část dlaždice. Chcete-li zobrazit řídicí panel monitorování Azure SQL Analytics pro spravované instance a databáze instancí, klikněte na spodní část dlaždice.

### <a name="viewing-azure-sql-analytics-data"></a>Zobrazení dat Azure SQL Analytics

Řídicí panel obsahuje přehled všech databází, které jsou monitorovány prostřednictvím různých perspektiv. Aby bylo možné pracovat s různými perspektivami, musíte povolit správné metriky nebo protokoly na svých prostředcích SQL pro streamování do Log Analytics pracovního prostoru.

Pokud se některé metriky nebo protokoly nestreamují do Azure Monitor, dlaždice v Azure SQL Analytics nejsou naplněny informacemi o monitorování.

### <a name="single-and-pooled-databases-and-elastic-pools-view"></a>Samostatné a sdružené databáze a zobrazení elastických fondů

Po výběru dlaždice databázi Azure SQL Analytics se zobrazí řídicí panel monitorování.

![Přehled služby Azure SQL Analytics](./media/azure-sql/azure-sql-sol-overview.png)

Výběrem libovolné prvku dlaždice, otevře sestavu procházení do konkrétní perspektivy. Po výběru perspektivy otevření sestavy procházení.

![Vypršení časového limitu pro Azure SQL Analytics](./media/azure-sql/azure-sql-sol-metrics.png)

Každá perspektiva v tomto zobrazení poskytuje souhrny na úrovni předplatného, serveru, elastického fondu a databáze. Kromě toho každá Perspektiva zobrazí Perspektiva specifické pro sestavu na pravé straně. Výběr předplatného, server, fondu nebo databázi ze seznamu pokračuje procházení podrobností.

### <a name="managed-instance-and-instances-databases-view"></a>Zobrazení databáze spravované instance a instance

Po výběru dlaždice Azure SQL Analytics pro databáze, se zobrazí řídicí panel monitorování.

![Přehled služby Azure SQL Analytics](./media/azure-sql/azure-sql-sol-overview-mi.png)

Výběrem libovolné prvku dlaždice, otevře sestavu procházení do konkrétní perspektivy. Po výběru perspektivy otevření sestavy procházení.

Když vyberete zobrazení spravovaná instance, zobrazí se podrobnosti o využití spravované instance, databázích, které obsahuje, a telemetrie na dotazech spuštěných napříč instancí.

![Vypršení časového limitu pro Azure SQL Analytics](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="intelligent-insights-report"></a>Intelligent Insights sestavy

Azure SQL Database [Intelligent Insights](../../sql-database/sql-database-intelligent-insights.md) vám umožní zjistit, co se děje s výkonem všech databází SQL Azure. Všechny Intelligent Insights shromažďovat můžete vizualizovat a získat přístup prostřednictvím Insights perspektivy.

![Azure SQL Analytics Insights](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pools-and-database-reports"></a>Elastické fondy a sestavy databáze

Elastické fondy i databáze mají vlastní konkrétní sestavy, které zobrazují všechna data shromážděná pro daný prostředek v zadaném čase.

![Databáze Azure SQL Analytics](./media/azure-sql/azure-sql-sol-database.png)

![Elastický fond Azure SQL](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Sestavy dotazu

Po dobu trvání dotazu a dotaz čeká na perspektivu, můžete korelovat výkon libovolného dotazu prostřednictvím sestavy dotazu. Tato sestava porovná výkonu dotazů napříč různými databázemi a usnadňuje identifikaci databází, které provádějí vybraný dotaz a srovnání, které jsou pomalé.

![Azure SQL Analytics dotazů](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Oprávnění

Pokud chcete používat Azure SQL Analytics, uživatelé musí udělit minimální oprávnění role čtenáře v Azure. Tuto roli však není povolit uživatelům zobrazit text dotazu, nebo provádět jakékoli automatického ladění akce. Účinnější role v Azure, které umožňují použití Azure SQL Analytics s největším rozsahem je vlastník, přispěvatel, přispěvatel databáze SQL nebo přispěvatel SQL Server. Můžete také chtít zvažte možnost vytvořit vlastní roli na portálu s konkrétní oprávnění požadovaná pouze pro použití služby Azure SQL Analytics a bez přístupu ke správě jiných zdrojů.

### <a name="creating-a-custom-role-in-portal"></a>Vytvořením vlastní role na portálu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Rozpozná, že některé organizace přinutit striktní oprávnění ovládací prvky v Azure, vyhledejte následující skript Powershellu vytvořit vlastní roli "SQL Analytics monitorování operátor" na webu Azure portal s minimální čtení a zápis oprávnění potřebná k povolení použití Azure SQL Analytics jeho nejširším rozsahu.

Nahradit v "{SubscriptionId}" níže uvedený skript s ID vašeho předplatného Azure a spusťte tento skript přihlášení jako roli vlastníka nebo přispěvatele v Azure.

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

Po vytvoření nové role tuto roli přiřaďte každý uživatel, který je potřeba udělit vlastní oprávnění k používání Azure SQL Analytics.

## <a name="analyze-data-and-create-alerts"></a>Analýza dat a vytvářet výstrahy

Analýza dat v Azure SQL Analytics vychází z [Log Analytics jazyka](../log-query/get-started-queries.md) pro vlastní dotazování a vytváření sestav. Vyhledejte popis dostupných dat shromážděných z databázového prostředku pro vlastní dotazování v [metrikách a protokolech, které jsou k dispozici](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available).

Automatizované upozorňování v Azure SQL Analytics jsou založené na zápisu Log Analytics dotazu, který aktivuje výstrahu při splnění podmínky. Níže naleznete několik příkladů Log Analytics dotazů, na kterých je možné nastavit upozorňování v Azure SQL Analytics.

### <a name="creating-alerts-for-azure-sql-database"></a>Vytvoření výstrah pro službu Azure SQL Database

Můžete snadno [vytvářet výstrahy](../platform/alerts-metric.md) s daty, která přicházejí z Azure SQL Databasech prostředků. Tady jsou některé užitečné [dotazy k protokolům](../log-query/log-query-overview.md) , které můžete použít s výstrahou protokolu:

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
>
> - Představte si, že nastavení této výstrahy je v případě, že se pro monitorované databáze streamují základní metriky na Azure SQL Analytics.
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
>
> - Představte si, že nastavení této výstrahy je v případě, že se pro monitorované databáze streamují základní metriky na Azure SQL Analytics.
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
>
> - Představte si, že nastavení této výstrahy je v případě, že se pro monitorované databáze streamují základní metriky na Azure SQL Analytics.
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
>
> - Představte si, že nastavení této výstrahy znamená, že monitorovaný protokol SQLInsights log streamování databází se Azure SQL Analytics.
> - Tento dotaz vyžaduje pravidlo upozornění pro nastavení pomocí stejně často jako alert_run_interval předejdete tak duplicitní výsledky. Pravidlo by měla být nastavena pro vyvolat vypnout výstrahy při existují výsledky (> 0 výsledků) z dotazu.
> - Přizpůsobte alert_run_interval a určete časový rozsah, který bude kontrolovat, jestli se v databázích nakonfigurovaných pro streamování protokolu SQLInsights do Azure SQL Analytics zapsala podmínka.
> - Přizpůsobení insights_string zachytit výstup analýzy textu Insights kořenové příčiny. Jedná se o stejný text, který se zobrazuje v uživatelském rozhraní Azure SQL Analytics, které můžete použít z existujících přehledů. Alternativně můžete použít následující dotaz k zobrazení textu všechny přehledy vytvořené v rámci předplatného. Získávání různých řetězců pro nastavení výstrah na základě informací použijte výstup dotazu.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instances"></a>Vytváření výstrah pro spravované instance

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
>
> - Představte si, že nastavení této výstrahy je, že monitorovaná spravovaná instance má k dispozici streamování protokolu ResourceUsageStats s povoleným Azure SQL Analytics.
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
>
> - Představte si nastavení této výstrahy znamená, že monitorovaná spravovaná instance má k dispozici streamování protokolu ResourceUsageStats s povoleným Azure SQL Analytics.
> - Tento dotaz vyžaduje nastavení pravidla výstrahy, aby se aktivovala výstraha v případě, že v dotazu existují výsledky (> 0 výsledků), a označuje, že podmínka ve spravované instanci existuje. Výstupem je průměrné procento využití procesoru v definovaném období ve spravované instanci.

### <a name="pricing"></a>Ceny

I když je Azure SQL Analytics zdarma, používá se spotřeba diagnostiky nad rámec bezplatných jednotek pro příjem dat. Další informace najdete v článku [Log Analytics ceny](https://azure.microsoft.com/pricing/details/monitor). Volných jednotek příjem dat, které jsou k dispozici povolit bezplatné sledování několik databází každý měsíc. Další aktivní databáze s těžšími úlohami ingestují více dat oproti nečinným databázím. Spotřebu příjmu dat v Azure SQL Analytics můžete snadno monitorovat tak, že v navigační nabídce Azure SQL Analytics vyberete pracovní prostor OMS a pak vyberete využití a odhadované náklady.

## <a name="next-steps"></a>Další kroky

- Použijte [dotazy protokolu](../log-query/log-query-overview.md) v Azure monitor k zobrazení podrobných dat SQL Azure.
- [Vytvořte si vlastní řídicí panely](../learn/tutorial-logs-dashboards.md) zobrazující data Azure SQL.
- [Vytváření výstrah](../platform/alerts-overview.md) při výskytu určitých událostí Azure SQL.
