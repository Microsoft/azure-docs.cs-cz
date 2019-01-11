---
title: Sjednocení několik prostředků Azure monitoru Application Insights | Dokumentace Microsoftu
description: Tento článek obsahuje podrobnosti o tom, jak použít funkci v protokolech monitorování Azure pro různé prostředky Application Insights dotazovat a vizualizovat data.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: magoedte
ms.openlocfilehash: 39dd475c776a3cdb4f2281b9b5468968745024ac
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54215163"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Sjednocení několik prostředků Azure monitoru Application Insights 
Tento článek popisuje, jak dotaz a zobrazit všechna data protokolů Application Insights aplikaci na jednom místě, i když se nachází v různých předplatných Azure, jako náhrada vyřazení Application Insights Connector.  

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Doporučenému přístupu k dotazování více prostředky Application Insights 
Výpis několika prostředky služby Application Insights v dotazu může být náročné a obtížné na správu. Místo toho můžete využít funkce rozdělte logiku dotazu z aplikací oborů.  

Tento příklad ukazuje, jak můžete monitorovat různé prostředky Application Insights a vizualizovat počet neúspěšných žádostí podle názvu aplikace. Než začnete, spusťte tento dotaz v pracovním prostoru, který je připojen k prostředky Application Insights k získání seznamu propojených aplikací: 

```
ApplicationInsights
| summarize by ApplicationName
```

Vytvoření funkce pomocí operátoru union se seznam aplikací a uložit dotaz jako funkce s aliasem *applicationsScoping*.  

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

>[!NOTE]
>Uvedené aplikace kdykoli můžete upravit tak, že přejdete do Průzkumníka dotazů na protokoly portálu a úpravy funkce, nebo pomocí `SavedSearch` rutiny Powershellu. `withsource= SourceApp` Příkaz přidá sloupec do výsledků, který určuje aplikace, které odeslání protokolu. 
>
>Dotaz používá schéma Application Insights, i když dotaz je provést v pracovním prostoru, protože funkce applicationsScoping vrátí strukturu dat Application Insights. 
>
>Operátor analýzy je volitelné v tomto příkladu, extrahuje z vlastnosti SourceApp název aplikace. 

Nyní jste připraveni používat funkce applicationsScoping v dotazu napříč prostředky. Alias funkce vrátí sjednocení žádosti ze všech definovaných aplikací. Dotazu a filtry pro chybné žádosti a vizualizuje vývoje aplikací. ![Příklad výsledky dotazů napříč](media/unify-app-resource-data/app-insights-query-results.png)

## <a name="query-across-application-insights-resources-and-workspace-data"></a>Dotazování napříč prostředky Application Insights a pracovní prostor dat 
Když zastavíte konektoru a nutnosti provádět dotazy za časové období, která byla oříznuta podle uchovávání dat Application Insights (90 dnů), je potřeba provést [dotazy napříč prostředky](../../azure-monitor/log-query/cross-workspace-query.md) v pracovním prostoru a Application Insights prostředky v přechodném období. To je, dokud vaše aplikace data hromadí za nové uchovávání dat Application Insights uvedených výše. Dotaz vyžaduje některé manipulace, protože schémata v Application Insights a pracovní prostor se liší. V tabulce dále v tomto oddílu zdůrazněním rozdílů schématu. 

Například pokud konektor přestala pracovat na 2018-11-01, při dotazování protokolů napříč prostředky a aplikace data Application Insights v pracovním prostoru, dotaz by vytvořen jako v následujícím příkladu:

```
applicationsScoping //this brings data from Application Insights resources 
| where timestamp between (datetime("2018-11-01") .. now()) 
| where success == 'False' 
| where duration > 1000 
| union ( 
    ApplicationInsights //this is Application Insights data in Log Analytics worspace 
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

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Application Insights a Log Analytics rozdíly ve schématu pracovního prostoru
V následující tabulce jsou uvedeny rozdíly ve schématu mezi Log Analytics a Application Insights.  

| Vlastnosti pracovního prostoru analýzy protokolů| Vlastnosti prostředku Application Insights|
|------------|------------| 
| AnonUserId | USER_ID|
| ApplicationId | appId|
| ApplicationName | AppName|
| ApplicationTypeVersion | application_Version |
| AvailabilityCount | Vlastnost ItemCount |
| AvailabilityDuration | doba trvání |
| AvailabilityMessage | zpráva |
| AvailabilityRunLocation | location |
| AvailabilityTestId | id |
| AvailabilityTestName | jméno |
| AvailabilityTimestamp | časové razítko |
| Prohlížeč | client_browser |
| Město | client_city |
| Když | client_IP |
| Počítač | cloud_RoleInstance | 
| Země | client_CountryOrRegion | 
| CustomEventCount | Vlastnost ItemCount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | jméno | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExceptionCount | Vlastnost ItemCount | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | zpráva | 
| ExceptionType | type |
| ID operace | operation_id |
| OperationName | opeation_Name | 
| Operační systém | client_OS | 
| PageViewCount | Vlastnost ItemCount |
| PageViewDuration | doba trvání | 
| PageViewName | jméno | 
| ParentOpeartionID | operation_Id | 
| RequestCount | Vlastnost ItemCount | 
| RequestDuration | doba trvání | 
| ID žádosti | id | 
| RequestName | jméno | 
| RequestSuccess | úspěch | 
| ResponseCode | Kód výsledku | 
| Role | cloud_RoleName |
| Instance role | cloud_RoleInstance |
| ID relace | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | type |
| zprostředkovatele identity | adresy _url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>Další postup

Použití [prohledávání protokolů](../../azure-monitor/log-query/log-query-overview.md) k zobrazení podrobných informací pro vaše aplikace Application Insights.