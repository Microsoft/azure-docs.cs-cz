---
title: Sjednocení více prostředků Application Insights Azure Monitor | Microsoft Docs
description: Tento článek poskytuje podrobné informace o tom, jak používat funkci v Azure Monitor protokoly k dotazování na více prostředků Application Insights a k vizualizaci těchto dat.
author: bwren
ms.author: bwren
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 54a026bfe9dfba121799d850ca0c81485bf63874
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2021
ms.locfileid: "100545662"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Sjednocení více Azure Monitorch prostředků Application Insights 
Tento článek popisuje, jak zadávat dotazy a zobrazovat všechna data protokolu Application Insights na jednom místě, i když jsou v různých předplatných Azure, jako náhrada za vyřazení Application Insights Connector. Počet prostředků Application Insights, které můžete zahrnout do jednoho dotazu, je omezený na 100.

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Doporučený postup pro dotazování více Application Insights prostředků 
Výpis více Application Insights prostředků v dotazu může být náročný a obtížně udržovatelný. Místo toho můžete využít funkci k oddělení logiky dotazů z oboru aplikací.  

Tento příklad ukazuje, jak můžete monitorovat více prostředků Application Insights a vizualizovat počet neúspěšných žádostí podle názvu aplikace.

Vytvořte funkci pomocí operátoru Union se seznamem aplikací a pak dotaz uložte ve svém pracovním prostoru jako funkci s aliasem *applicationsScoping*. 

Uvedené aplikace můžete kdykoli upravit na portálu tak, že přejdete do Průzkumníku dotazů v pracovním prostoru a vyberete funkci pro úpravy a uložení nebo pomocí `SavedSearch` rutiny PowerShellu. 

>[!NOTE]
>Tuto metodu nelze použít s upozorněními protokolu, protože ověření přístupu prostředků pravidla výstrahy, včetně pracovních prostorů a aplikací, se provádí při vytváření výstrahy. Přidání nových prostředků do funkce po vytvoření výstrahy není podporováno. Pokud upřednostňujete použití funkce pro obory prostředků v upozorněních protokolu, je nutné upravit pravidlo výstrahy na portálu nebo pomocí šablony Správce prostředků pro aktualizaci oboru prostředků. Případně můžete do dotazu protokolu výstrahy zahrnout seznam prostředků.

`withsource= SourceApp`Příkaz přidá sloupec do výsledků, který určí aplikaci, která protokol odeslala. Operátor Parse je v tomto příkladu volitelný a používá se k extrahování názvu aplikace z vlastnosti SourceApp. 

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

>[!NOTE]
>[Dotazy na více prostředků](./cross-workspace-query.md) v upozorněních protokolu jsou podporovány pouze v aktuálním [rozhraní scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules). Pokud používáte starší rozhraní API pro výstrahy Log Analytics, budete muset [Přepnout na aktuální rozhraní API](../platform/alerts-log-api-switch.md). [Viz příklady šablon](../platform/alerts-log-create-templates.md).

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Rozdíly mezi Application Insights a Log Analytics schémat pracovního prostoru
V následující tabulce jsou uvedeny rozdíly v schématech mezi Log Analytics a Application Insights.  

| Vlastnosti Log Analytics pracovního prostoru| Vlastnosti prostředku Application Insights|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationId | appId|
| ApplicationName | appName|
| ApplicationTypeVersion | application_Version |
| AvailabilityCount | Vlastnost ItemCount |
| AvailabilityDuration | doba trvání |
| AvailabilityMessage | zpráva |
| AvailabilityRunLocation | location |
| AvailabilityTestId | id |
| AvailabilityTestName | name |
| AvailabilityTimestamp | časové razítko |
| Prohlížeč | client_browser |
| City (Město) | client_city |
| IP adresa klienta | client_IP |
| Počítač | cloud_RoleInstance | 
| Země | client_CountryOrRegion | 
| CustomEventCount | Vlastnost ItemCount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | name | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExceptionCount | Vlastnost ItemCount | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | zpráva | 
| Typvýjimky | typ |
| OperationID | operation_id |
| OperationName | operation_Name | 
| Operační systém | client_OS | 
| PageViewCount | Vlastnost ItemCount |
| PageViewDuration | doba trvání | 
| PageViewName | name | 
| ParentOperationID | operation_Id | 
| RequestCount | Vlastnost ItemCount | 
| RequestDuration | doba trvání | 
| ID požadavku | id | 
| Žádost o zadání | name | 
| RequestSuccess | úspěch | 
| ResponseCode | resultCode | 
| Role | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| SessionId | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | typ |
| URL | url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>Další kroky

K zobrazení podrobných informací o aplikacích Application Insights použijte [hledání v protokolu](./log-query-overview.md) .

