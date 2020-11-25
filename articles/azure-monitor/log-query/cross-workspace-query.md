---
title: Dotazování napříč prostředky pomocí Azure Monitor | Microsoft Docs
description: Tento článek popisuje, jak se můžete dotazovat na prostředky z několika pracovních prostorů a aplikace App Insights v rámci vašeho předplatného.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2020
ms.openlocfilehash: 8eb163c95fb1426ebae8956d50f6d8f6aec6fd7f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013645"
---
# <a name="perform-log-query-in-azure-monitor-that-span-across-workspaces-and-apps"></a>Provádění dotazů protokolu v Azure Monitor, které jsou rozloženy mezi pracovními prostory a aplikacemi

Protokoly Azure Monitor podporují dotaz napříč několika pracovními prostory Log Analytics a Application Insights aplikaci ve stejné skupině prostředků, jiné skupině prostředků nebo jiném předplatném. Získáte tak zobrazení dat v rámci systému.

Existují dvě metody dotazování na data, která jsou uložená ve více pracovních prostorech a aplikacích:
1. Explicitně zadáním pracovního prostoru a podrobností aplikace. Tato technika je podrobně popsána v tomto článku.
2. Implicitně pomocí [dotazů kontextu prostředků](../platform/design-logs-deployment.md#access-mode). Při dotazování v kontextu konkrétního prostředku, skupiny prostředků nebo předplatného budou relevantní data načtena ze všech pracovních prostorů, které obsahují data pro tyto prostředky. Application Insights data uložená v aplikacích nebudou načtena.

> [!IMPORTANT]
> Pokud používáte telemetrii [prostředků Application Insights na základě pracovního prostoru](../app/create-workspace-resource.md) , je uložena v pracovním prostoru Log Analytics se všemi ostatními daty protokolů. Použijte výraz log () k zápisu dotazu, který obsahuje aplikaci ve více pracovních prostorech. Pro více aplikací ve stejném pracovním prostoru nepotřebujete dotaz mezi jednotlivými pracovními prostory.


## <a name="cross-resource-query-limits"></a>Omezení dotazů mezi prostředky 

* Počet Application Insightsch prostředků a Log Analytics pracovních prostorů, které můžete zahrnout do jednoho dotazu, je omezený na 100.
* Dotaz mezi prostředky není v Návrháři zobrazení podporován. Dotaz můžete v Log Analytics vytvořit a připnout na řídicí panel Azure a [vizualizovat dotaz protokolu](../learn/tutorial-logs-dashboards.md). 
* Dotazy na více prostředků v upozorněních protokolu jsou podporovány pouze v aktuálním [rozhraní scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules). Pokud používáte starší rozhraní API pro výstrahy Log Analytics, budete muset [Přepnout na aktuální rozhraní API](../platform/alerts-log-api-switch.md).


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Dotazování napříč Log Analyticsmi pracovními prostory a z Application Insights
Pokud chcete odkazovat na jiný pracovní prostor v dotazu, použijte identifikátor [*pracovního prostoru*](./workspace-expression.md) a pro aplikaci z Application Insights použijte identifikátor [*aplikace*](./app-expression.md) .  

### <a name="identifying-workspace-resources"></a>Určení prostředků pracovního prostoru
Následující příklady ukazují dotazy napříč Log Analytics pracovními prostory pro vrácení souhrnných počtů protokolů z tabulky aktualizací v pracovním prostoru s názvem *ContosoRetail-IT*. 

Určení pracovního prostoru lze provést jedním z několika způsobů:

* Název prostředku – je popisný název pracovního prostoru, který se někdy označuje jako *název součásti*. 

    `workspace("contosoretail-it").Update | count`

* Kvalifikovaný název – je úplný název pracovního prostoru, který se skládá z názvu předplatného, skupiny prostředků a názvu komponenty v tomto formátu: název *předplatného/* skupina prostředků/název součásti. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count`

    >[!NOTE]
    >Vzhledem k tomu, že názvy předplatných Azure nejsou jedinečné, může být tento identifikátor dvojznačný. 
    >

* ID pracovního prostoru – ID pracovního prostoru je jedinečný a neměnný identifikátor přiřazený ke každému pracovnímu prostoru reprezentovanému jako globálně jedinečný identifikátor (GUID).

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* ID prostředku Azure – jedinečná identita pracovního prostoru definovaná v Azure ID prostředku použijete, pokud je název prostředku dvojznačný.  U pracovních prostorů je ve formátu: */Subscriptions/SubscriptionId/ResourceGroups/resourceGroup/Providers/Microsoft. OperationalInsights/pracovní prostory/součásti*.  

    Například:
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>Určení aplikace
Následující příklady vrátí souhrnný počet požadavků provedených proti aplikaci s názvem *fabrikamapp* v Application Insights. 

Určení aplikace v Application Insights lze provést pomocí výrazu *aplikace (identifikátor)* .  Argument *identifikátor* určuje aplikaci pomocí jedné z následujících možností:

* Název prostředku – jedná se o lidský čitelný název aplikace, který se někdy označuje jako *název součásti*.  

    `app("fabrikamapp")`

    >[!NOTE]
    >Identifikace aplikace podle názvu předpokládá jedinečnost napříč všemi přístupnými předplatnými. Pokud máte více aplikací se zadaným názvem, dotaz z důvodu nejednoznačnosti se nezdařil. V takovém případě je nutné použít jeden z ostatních identifikátorů.

* Kvalifikovaný název – je celé jméno aplikace, složené z názvu předplatného, skupiny prostředků a názvu komponenty v tomto formátu: název *předplatného/* skupina prostředků/název součásti. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Vzhledem k tomu, že názvy předplatných Azure nejsou jedinečné, může být tento identifikátor dvojznačný. 
    >

* ID – identifikátor GUID aplikace.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* ID prostředku Azure – jedinečná identita aplikace definovaná pro Azure ID prostředku použijete, pokud je název prostředku dvojznačný. Formát je: */Subscriptions/SubscriptionId/ResourceGroups/resourceGroup/Providers/Microsoft. OperationalInsights/Components/* Component.  

    Například:
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>Provádění dotazu napříč několika prostředky
Můžete zadávat dotazy na více prostředků z libovolné instance prostředků, můžou se jednat o pracovní prostory a aplikace v kombinaci.
    
Příklad pro dotaz ve dvou pracovních prostorech:    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>Použití dotazu mezi prostředky pro více prostředků
Při použití dotazů mezi prostředky ke korelaci dat z více Log Analyticsch pracovních prostorů a Application Insights prostředků může být dotaz složitý a obtížný. [Funkce v Azure Monitorch dotazech protokolu](functions.md) byste měli využít k oddělení logiky dotazů z oboru prostředků dotazů, což zjednodušuje strukturu dotazu. Následující příklad ukazuje, jak můžete monitorovat více prostředků Application Insights a vizualizovat počet neúspěšných žádostí podle názvu aplikace. 

Vytvořte dotaz podobný následujícímu, který odkazuje na rozsah Application Insightsch prostředků. `withsource= SourceApp`Příkaz přidá sloupec, který určuje název aplikace, která protokol odeslala. [Uložte dotaz jako funkci](functions.md#create-a-function) s aliasem _applicationsScoping_.

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



[Tuto funkci](./functions.md#use-a-function) teď můžete použít v dotazu mezi prostředky, jako je následující. Alias funkce _applicationsScoping_ vrací sjednocení tabulky requests ze všech definovaných aplikací. Dotaz pak filtruje neúspěšné žádosti a vizualizuje trendy podle aplikace. V tomto příkladu je operátor _Parse_ volitelný. Extrahuje název aplikace z vlastnosti _SourceApp_ .

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

>[!NOTE]
> Tuto metodu nelze použít s upozorněními protokolu, protože ověření přístupu prostředků pravidla výstrahy, včetně pracovních prostorů a aplikací, se provádí při vytváření výstrahy. Přidání nových prostředků do funkce po vytvoření výstrahy není podporováno. Pokud upřednostňujete použití funkce pro obory prostředků v upozorněních protokolu, je nutné upravit pravidlo výstrahy na portálu nebo pomocí šablony Správce prostředků pro aktualizaci oboru prostředků. Případně můžete do dotazu protokolu výstrahy zahrnout seznam prostředků.


![Timechart](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>Další kroky

- Přehled dotazů protokolu a způsobu strukturování dat protokolu Azure Monitor najdete [v tématu Analýza dat protokolu v Azure monitor](log-query-overview.md) .

