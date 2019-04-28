---
title: Dotazování napříč prostředky prostřednictvím služby Azure Monitor | Dokumentace Microsoftu
description: Tento článek popisuje, jak můžete zadávat dotazy na prostředky z několika pracovních prostorů a aplikace pro App Insights ve vašem předplatném.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: magoedte
ms.openlocfilehash: b0d12021be5a5dca348ea3ffa3f0b853725812da
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60589297"
---
# <a name="perform-cross-resource-log-queries-in-azure-monitor"></a>Provádění dotazů protokolů napříč prostředky ve službě Azure Monitor  

Dříve prostřednictvím služby Azure Monitor je mohli analyzovat data pouze z v rámci aktuálního pracovního prostoru a omezený možnost dotazování napříč několika pracovními prostory definovanými v rámci vašeho předplatného.  Kromě toho může hledat pouze položky telemetrická data shromážděná z vaší webové aplikace pomocí Application Insights přímo ve službě Application Insights nebo ze sady Visual Studio.  Kvůli tomu také bylo obtížné analyzovat nativně provozní a data aplikací společně.   

Nyní se můžete dotazovat nejen napříč několika pracovních prostorů Log Analytics, ale také data z konkrétní aplikace Application Insights ve stejné skupině prostředků, jiné skupiny prostředků nebo jiného předplatného. To vám poskytne systémová přehled o datech.  Lze provést pouze tyto typy dotazů v [Log Analytics](portals.md).

## <a name="cross-resource-query-limits"></a>Omezení dotazu napříč prostředky 

* Počet prostředků Application Insights, které mohou obsahovat v jediném dotazu je omezený na 100.
* Dotaz napříč prostředky není podporován v zobrazení návrhu. Můžete upravit dotaz, který ve službě Log Analytics a připnout na řídicí panel Azure a [vizualizace prohledávání protokolu](../../azure-monitor/learn/tutorial-logs-dashboards.md#visualize-a-log-search). 
* Dotaz napříč prostředky v upozornění protokolu je podporován v novém [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Ve výchozím nastavení, využívá Azure Monitor [starší verze API upozornění Log Analytics](../platform/api-alerts.md) pro vytvoření nového protokolu pravidla upozornění z webu Azure portal, pokud přejdete z [starší verze rozhraní API upozornění Log](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Po přepnutí nové rozhraní API se stane výchozí pro nové pravidla upozornění na webu Azure portal a umožňuje vám vytvořit dotaz napříč prostředky, že pravidla upozornění protokolů. Můžete vytvořit dotaz napříč prostředky log pravidla upozornění přitom přepínač pomocí [šablony ARM pro scheduledQueryRules API](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) – ale toto pravidlo upozornění se dají spravovat přes [scheduledQueryRules rozhraní API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) a ne z portálu Azure portal.


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Dotazování napříč pracovních prostorů Log Analytics a ze služby Application Insights
Chcete-li odkazovat na jiný pracovní prostor v dotazu, použijte [ *pracovní prostor* ](https://docs.microsoft.com/azure/log-analytics/query-language/workspace-expression) identifikátor a pro aplikace ze služby Application Insights, použijte [ *aplikace* ](https://docs.microsoft.com/azure/log-analytics/query-language/app-expression)identifikátor.  

### <a name="identifying-workspace-resources"></a>Identifikační prostředky pracovního prostoru
Následující příklady ukazují dotazy napříč pracovními prostory Log Analytics k vrácení souhrnný počet protokolů z tabulky aktualizace v pracovním prostoru s názvem *contosoretail it*. 

Identifikace pracovní prostor může být jedním z několika způsobů:

* Název prostředku – je popisný název pracovního prostoru, někdy označovány jako *název komponenty*. 

    `workspace("contosoretail-it").Update | count`
 
    >[!NOTE]
    >Identifikace podle názvu pracovního prostoru předpokládá jedinečnost napříč všemi předplatnými přístupné. Pokud máte více aplikací se zadaným názvem, že se dotaz nezdaří z důvodu nejednoznačnosti. V takovém případě musíte použít jeden z další identifikátory.

* Úplný název - je "úplný název" pracovního prostoru, skládá z názvu předplatného, skupiny prostředků a název komponenty v tomto formátu: *subscriptionName/skupina prostředků/název komponenty*. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count`

    >[!NOTE]
    >Vzhledem k tomu, že názvy předplatného Azure nejsou jedinečné, tento identifikátor může být nejednoznačný. 
    >

* ID pracovního prostoru - ID pracovního prostoru je jedinečný, nezměnitelný, identifikátor přiřazený do každého pracovního prostoru reprezentovaná jako globálně jedinečný identifikátor (GUID).

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Azure Resource ID – definované Azure jedinečnou identitu pracovního prostoru. ID prostředku, které se používají, když je nejednoznačný název prostředku.  U pracovních prostorů, formát je: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. OperationalInsights/pracovních prostorů/componentName*.  

    Příklad:
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>Určení aplikace
Následující příklady vrátí souhrnný počet požadavků na přístup aplikaci s názvem *fabrikamapp* ve službě Application Insights. 

Identifikuje aplikaci ve službě Application Insights můžete docílit, když se *app(Identifier)* výrazu.  *Identifikátor* argument určuje aplikace pomocí jedné z následujících akcí:

* Název prostředku – je lidské čitelný název aplikace, která se někdy označuje jako *název komponenty*.  

    `app("fabrikamapp")`

* Úplný název - je "úplný název" aplikace skládá z názvu předplatného, skupiny prostředků a název komponenty v tomto formátu: *subscriptionName/skupina prostředků/název komponenty*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Vzhledem k tomu, že názvy předplatného Azure nejsou jedinečné, tento identifikátor může být nejednoznačný. 
    >

* ID – identifikátor GUID aplikace aplikace.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Azure ID prostředku - definované Azure jedinečnou identitu aplikace. ID prostředku, které se používají, když je nejednoznačný název prostředku. Formát je: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. OperationalInsights/součásti/componentName*.  

    Příklad:
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>Provádění dotazu napříč více zdrojů
Z některého z vašich instancí prostředku můžete dotazovat více zdrojů, může jít o pracovních prostorech a aplikacích kombinovat.
    
Příklad pro dotazování napříč dvěma pracovními prostory.    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>Pomocí dotazu napříč prostředky u několika prostředků
Při použití dotazy napříč prostředky pro korelaci dat z více pracovních prostorů Log Analytics a prostředky Application Insights, dotaz může být složité a obtížné na správu. By je měli využít [dotazů protokolu funkcí ve službě Azure Monitor](functions.md) oddělení logiku dotazu od nepříznivě prostředky dotazu, což zjednodušuje struktura dotazu. Následující příklad ukazuje, jak můžete monitorovat různé prostředky Application Insights a vizualizovat počet neúspěšných žádostí podle názvu aplikace. 

Vytvořte dotaz následujícím postupem, který odkazuje na obor prostředky Application Insights. `withsource= SourceApp` Příkaz přidá sloupec, který určuje název aplikace, které odeslání protokolu. [Uložit dotaz jako funkce](functions.md#create-a-function) s aliasem _applicationsScoping_.

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



Teď můžete [tuto funkci použít](../../azure-monitor/log-query/functions.md#use-a-function) v dotazu napříč prostředky následujícím postupem. Alias funkce _applicationsScoping_ Vrátí sjednocení z tabulky requests ze všech definovaných aplikací. Dotazu a filtry pro chybné žádosti a vizualizuje vývoje aplikací. _Analyzovat_ operátor je volitelné v tomto příkladu. Extrahuje název aplikace z _SourceApp_ vlastnost.

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```
![Časový graf](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>Další postup

- Kontrola [analyzovat data protokolů ve službě Azure Monitor](log-query-overview.md) přehledné informace o protokolu dotazy a strukturování dat protokolu Azure Monitor.
- Kontrola [dotazů na protokoly Azure monitoru](query-language.md) Chcete-li zobrazit všechny prostředky pro dotazů na protokoly Azure monitoru.
