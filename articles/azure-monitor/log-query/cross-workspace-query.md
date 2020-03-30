---
title: Dotaz ujte napříč prostředky pomocí Azure Monitoru | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak můžete dotazovat na prostředky z více pracovních prostorů a aplikace App Insights aplikace v předplatném.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/05/2019
ms.openlocfilehash: 4740034bd970f42833125fa43bfdf72f710ac147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249604"
---
# <a name="perform-cross-resource-log-queries-in-azure-monitor"></a>Provádění dotazů protokolu mezi prostředky v Azure Monitoru  

Dříve s Azure Monitor, můžete analyzovat pouze data z aktuálního pracovního prostoru a omezuje možnost dotazovat se na více pracovních prostorů definovaných ve vašem předplatném.  Kromě toho můžete vyhledávat pouze telemetrické položky shromážděné z webové aplikace s Application Insights přímo v Application Insights nebo z Visual Studia. To také dělalo to výzvu nativně analyzovat provozní a aplikační data společně.

Teď můžete dotazovat nejen napříč více pracovními prostory Log Analytics, ale také data z konkrétní aplikace Insights aplikace ve stejné skupině prostředků, jiné skupině prostředků nebo jiném předplatném. To vám poskytuje zobrazení dat v celém systému. Tyto typy dotazů lze provádět pouze v [log analytics](portals.md).

## <a name="cross-resource-query-limits"></a>Omezení dotazů mezi prostředky 

* Počet prostředků Application Insights a pracovních prostorů Analýzy protokolů, které můžete zahrnout do jednoho dotazu, je omezen na 100.
* V návrháři zobrazení není podporován dotaz mezi prostředky. Můžete vytvořit dotaz v Log Analytics a připnout na řídicí panel Azure [vizualizovat dotaz protokolu](../learn/tutorial-logs-dashboards.md). 
* Dotaz mezi prostředky ve výstrahách protokolu je podporován v novém [rozhraní API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Ve výchozím nastavení Azure Monitor používá [starší rozhraní API upozornění log Analytics](../platform/api-alerts.md) pro vytváření nových pravidel upozornění protokolu z webu Azure Portal, pokud nepřepnete z [staršího rozhraní API pro výstrahy protokolů](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Po přepnutí se nové rozhraní API stane výchozím pro nová pravidla výstrah na webu Azure Portal a umožňuje vytvářet pravidla upozornění protokolu dotazů mezi prostředky. Můžete vytvořit pravidla výstrah protokolu dotazů mezi prostředky bez provedení přepínače pomocí [šablony Azure Resource Manager pro naplánované rozhraní API QueryRules](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) – ale toto pravidlo výstrahy je spravovatelné prostřednictvím rozhraní API [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) a nikoli z portálu Azure.


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Dotazování napříč pracovními prostory Analýzy protokolů a z přehledů aplikací
Chcete-li odkazovat na jiný pracovní prostor v dotazu, použijte identifikátor [*pracovního prostoru*](https://docs.microsoft.com/azure/log-analytics/query-language/workspace-expression) a pro aplikaci z Application Insights použijte identifikátor [*aplikace.*](https://docs.microsoft.com/azure/log-analytics/query-language/app-expression)  

### <a name="identifying-workspace-resources"></a>Identifikace zdrojů pracovního prostoru
Následující příklady ukazují dotazy v pracovních prostorech Analýzy protokolů a vrátí souhrnný počet protokolů z tabulky Aktualizace v pracovním prostoru s názvem *contosoretail-it*. 

Identifikaci pracovního prostoru lze provést jedním z několika způsobů:

* Název prostředku - je lidsky čitelný název pracovního prostoru, někdy označovaný jako *název součásti*. 

    `workspace("contosoretail-it").Update | count`

* Kvalifikovaný název - je "úplný název" pracovního prostoru, který se skládá z názvu předplatného, skupiny prostředků a názvu komponenty v tomto formátu: *subscriptionName/resourceGroup/componentName*. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count`

    >[!NOTE]
    >Vzhledem k tomu, že názvy předplatného Azure nejsou jedinečné, tento identifikátor může být nejednoznačný. 
    >

* ID pracovního prostoru – ID pracovního prostoru je jedinečný, neměnný identifikátor přiřazený každému pracovnímu prostoru reprezentovanému jako globálně jedinečný identifikátor (GUID).

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* ID prostředků Azure – jedinečná identita pracovního prostoru definovaná v Azure. ID prostředku se používá, pokud je název prostředku nejednoznačný.  Pro pracovní prostory je formát: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. OperationalInsights/workspaces/componentName*.  

    Například:
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>Identifikace aplikace
Následující příklady vrátí souhrnný počet požadavků na aplikaci s názvem *fabrikamapp* v Application Insights. 

Identifikaci aplikace v Application Insights lze provést pomocí výrazu *aplikace (identifikátor).*  Argument *Identifier* určuje aplikaci pomocí jedné z následujících možností:

* Název prostředku - je čitelný název aplikace čitelný pro člověka, někdy označovaný jako *název komponenty*.  

    `app("fabrikamapp")`

    >[!NOTE]
    >Identifikace aplikace podle názvu předpokládá jedinečnost ve všech přístupných předplatných. Pokud máte více aplikací se zadaným názvem, dotaz se nezdaří z důvodu nejednoznačnosti. V takovém případě je nutné použít jeden z dalších identifikátorů.

* Kvalifikovaný název - je "úplný název" aplikace, který se skládá z názvu předplatného, skupiny prostředků a názvu komponenty v tomto formátu: *subscriptionName/resourceGroup/componentName*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Vzhledem k tomu, že názvy předplatného Azure nejsou jedinečné, tento identifikátor může být nejednoznačný. 
    >

* ID - GUID aplikace.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* ID prostředků Azure – jedinečná identita aplikace definovaná V Azure. ID prostředku se používá, pokud je název prostředku nejednoznačný. Formát je: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. OperationalInsights/components/componentName*.  

    Například:
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>Provádění dotazu napříč více prostředky
Můžete dotazovat více prostředků z libovolné instance prostředků, mohou to být pracovní prostory a aplikace kombinované.
    
Příklad dotazu ve dvou pracovních prostorech:    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>Použití dotazu mezi prostředky pro více prostředků
Při použití dotazů mezi zdroji ke korelaci dat z více pracovních prostorů Log Analytics a prostředků Application Insights může být dotaz složitý a obtížně udržovatelné. Měli byste využít [funkce v dotazech protokolu Azure Monitor](functions.md) oddělit logiku dotazu od oboru prostředků dotazu, což zjednodušuje strukturu dotazu. Následující příklad ukazuje, jak můžete sledovat více prostředků Application Insights a vizualizovat počet neúspěšných požadavků podle názvu aplikace. 

Vytvořte dotaz, jako je následující, který odkazuje na obor prostředků Application Insights. Příkaz `withsource= SourceApp` přidá sloupec, který označuje název aplikace, která protokol odeslala. [Uložte dotaz jako funkci](functions.md#create-a-function) s _aliasaplikaceScoping_.

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



Nyní můžete [tuto funkci použít](../../azure-monitor/log-query/functions.md#use-a-function) v dotazu mezi prostředky, jako je následující. Funkce alias _aplikaceScoping_ vrátí sjednocení tabulky požadavků ze všech definovaných aplikací. Dotaz pak filtruje neúspěšné požadavky a vizualizuje trendy podle aplikace. Operátor _analýzy_ je volitelné v tomto příkladu. Extrahuje název aplikace z _SourceApp_ vlastnost.

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

>[!NOTE]
>Tuto metodu nelze použít s výstrahami protokolu, protože ověření přístupu prostředků pravidla výstrahy, včetně pracovních prostorů a aplikací, se provádí v době vytvoření výstrahy. Přidání nových prostředků do funkce po vytvoření výstrahy není podporováno. Pokud dáváte přednost použití funkce pro obory prostředků v výstrahách protokolu, je třeba upravit pravidlo výstrahy na portálu nebo pomocí šablony Správce prostředků a aktualizovat prostředky s vymezeným oborem. Případně můžete zahrnout seznam prostředků do dotazu upozornění protokolu.


![Časový diagram](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>Další kroky

- Zkontrolujte [analyzovat data protokolu v Azure Monitor](log-query-overview.md) u přehledu dotazů protokolu a jak azure monitor data protokolu je strukturována.
- Zkontrolujte [dotazy protokolu Azure Monitor](query-language.md) zobrazíte všechny prostředky pro dotazy protokolu Azure Monitor.
