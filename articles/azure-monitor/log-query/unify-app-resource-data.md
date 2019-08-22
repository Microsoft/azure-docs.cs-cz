---
title: Sjednocení více prostředků Application Insights Azure Monitor | Microsoft Docs
description: Tento článek poskytuje podrobné informace o tom, jak používat funkci v Azure Monitor protokoly k dotazování na více prostředků Application Insights a k vizualizaci těchto dat.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: magoedte
ms.openlocfilehash: d441b72b34da6146eba523563a09c2908cdcbbf4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650139"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Sjednocení více Azure Monitorch prostředků Application Insights 
Tento článek popisuje, jak zadávat dotazy a zobrazovat všechna data protokolu Application Insights na jednom místě, i když jsou v různých předplatných Azure, jako náhrada za vyřazení Application Insights Connector. Počet prostředků Application Insights, které můžete zahrnout do jednoho dotazu, je omezený na 100.

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Doporučený postup pro dotazování více Application Insights prostředků 
Výpis více Application Insights prostředků v dotazu může být náročný a obtížně udržovatelný. Místo toho můžete využít funkci k oddělení logiky dotazů z oboru aplikací.  

Tento příklad ukazuje, jak můžete monitorovat více prostředků Application Insights a vizualizovat počet neúspěšných žádostí podle názvu aplikace. Než začnete, spusťte tento dotaz v pracovním prostoru, který je připojený k Application Insights prostředkům, abyste získali seznam připojených aplikací: 

```
ApplicationInsights
| summarize by ApplicationName
```

Vytvořte funkci pomocí operátoru Union se seznamem aplikací a pak dotaz uložte ve svém pracovním prostoru jako funkci s aliasem *applicationsScoping*. 

Uvedené aplikace můžete kdykoli upravit na portálu tak, že přejdete do Průzkumníku dotazů v pracovním prostoru a vyberete funkci pro úpravy a uložení nebo pomocí `SavedSearch` rutiny PowerShellu. 

>[!NOTE]
>Tuto metodu nelze použít s upozorněními protokolu, protože ověření přístupu prostředků pravidla výstrahy, včetně pracovních prostorů a aplikací, se provádí při vytváření výstrahy. Přidání nových prostředků do funkce po vytvoření výstrahy není podporováno. Pokud upřednostňujete použití funkce pro obory prostředků v upozorněních protokolu, je nutné upravit pravidlo výstrahy na portálu nebo pomocí šablony Správce prostředků pro aktualizaci oboru prostředků. Případně můžete do dotazu protokolu výstrahy zahrnout seznam prostředků.

`withsource= SourceApp` Příkaz přidá sloupec do výsledků, který určí aplikaci, která protokol odeslala. Operátor Parse je v tomto příkladu volitelný a používá se k extrahování názvu aplikace z vlastnosti SourceApp. 

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

Nyní jste připraveni použít funkci applicationsScoping v dotazu mezi prostředky:  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

Dotaz používá Application Insights schéma, i když se dotaz spustí v pracovním prostoru, protože funkce applicationsScoping vrací datovou strukturu Application Insights. Alias funkce vrací sjednocení požadavků ze všech definovaných aplikací. Dotaz pak filtruje neúspěšné žádosti a vizualizuje trendy podle aplikace.

![Příklad výsledků mezi dotazy](media/unify-app-resource-data/app-insights-query-results.png)

## <a name="query-across-application-insights-resources-and-workspace-data"></a>Dotazování napříč prostředky Application Insights a daty pracovního prostoru 
Když konektor zastavíte a potřebujete provádět dotazy v časovém rozsahu, který byl zkrácen pomocí Application Insights uchovávání dat (90 dní), budete muset provádět [dotazy na více prostředků](../../azure-monitor/log-query/cross-workspace-query.md) v pracovním prostoru a Application Insights prostředky pro zprostředkující hodin. Je to až do doby, kdy se data vaší aplikace shromáždí podle nového Application Insights uchovávání dat uvedeného výše. Dotaz vyžaduje některé manipulace, protože schémata v Application Insights a pracovní prostor se liší. V tabulce dále v této části jsou zvýrazněny rozdíly ve schématu. 

>[!NOTE]
>[Dotaz na více prostředků](../log-query/cross-workspace-query.md) v upozorněních protokolu se podporuje v novém [rozhraní scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Ve výchozím nastavení používá Azure Monitor [starší rozhraní api Log Analytics výstrahy](../platform/api-alerts.md) pro vytváření nových pravidel upozornění protokolu z Azure Portal, pokud nepřepnete ze [starší verze rozhraní API upozornění protokolu](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Po přepínači se nové rozhraní API nastaví jako výchozí pro nová pravidla upozornění v Azure Portal a umožní vám vytvořit pravidla pro výstrahy protokolu dotazů mezi prostředky. Pravidla upozornění protokolu [dotazu pro více prostředků](../log-query/cross-workspace-query.md) můžete vytvořit bez toho, aby byl přepínač použit pomocí [šablony ARM pro rozhraní scheduledQueryRules API](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) , ale toto pravidlo upozornění lze spravovat i v případě, že [rozhraní scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) , nikoli z Azure Portal.

Pokud například konektor přestal pracovat na 2018-11-01, při dotazování protokolů napříč Application Insights prostředky a data aplikací v pracovním prostoru bude dotaz vytvořen podobně jako v následujícím příkladu:

```
applicationsScoping //this brings data from Application Insights resources 
| where timestamp between (datetime("2018-11-01") .. now()) 
| where success == 'False' 
| where duration > 1000 
| union ( 
    ApplicationInsights //this is Application Insights data in Log Analytics workspace 
    | where TimeGenerated < (datetime("2018-12-01") 
    | where RequestSuccess == 'False' 
    | where RequestDuration > 1000 
    | extend duration = RequestDuration //align to Application Insights schema 
    | extend timestamp = TimeGenerated //align to Application Insights schema 
    | extend name = RequestName //align to Application Insights schema 
    | extend resultCode = ResponseCode //align to Application Insights schema 
    | project-away RequestDuration , RequestName , ResponseCode , TimeGenerated 
) 
| project timestamp , duration , name , resultCode 
```

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Rozdíly mezi Application Insights a Log Analytics schémat pracovního prostoru
V následující tabulce jsou uvedeny rozdíly v schématech mezi Log Analytics a Application Insights.  

| Vlastnosti Log Analytics pracovního prostoru| Vlastnosti prostředku Application Insights|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationId | appId|
| ApplicationName | appName|
| ApplicationTypeVersion | application_Version |
| AvailabilityCount | Vlastnost ItemCount |
| AvailabilityDuration | duration |
| AvailabilityMessage | zpráva |
| AvailabilityRunLocation | location |
| AvailabilityTestId | id |
| AvailabilityTestName | name |
| AvailabilityTimestamp | timestamp |
| Browser | client_browser |
| City | client_city |
| ClientIP | client_IP |
| Computer | cloud_RoleInstance | 
| Country | client_CountryOrRegion | 
| CustomEventCount | Vlastnost ItemCount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | name | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExceptionCount | Vlastnost ItemCount | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | zpráva | 
| ExceptionType | type |
| OperationID | operation_id |
| OperationName | operation_Name | 
| OS | client_OS | 
| PageViewCount | Vlastnost ItemCount |
| PageViewDuration | duration | 
| PageViewName | name | 
| ParentOperationID | operation_Id | 
| RequestCount | Vlastnost ItemCount | 
| RequestDuration | duration | 
| RequestID | id | 
| RequestName | name | 
| RequestSuccess | success | 
| ResponseCode | resultCode | 
| Role | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| SessionId | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | type |
| URL | url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>Další postup

Použití [prohledávání protokolů](../../azure-monitor/log-query/log-query-overview.md) k zobrazení podrobných informací pro vaše aplikace Application Insights.
