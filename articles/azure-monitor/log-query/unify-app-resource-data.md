---
title: Sjednocení více prostředků Application Insights Azure Monitor | Microsoft Docs
description: Tento článek poskytuje podrobné informace o tom, jak používat funkci v Azure Monitor protokoly k dotazování na více prostředků Application Insights a k vizualizaci těchto dat.
author: bwren
ms.author: bwren
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: ce58aae3b1db1f0f338d353025d4f277aeb6944f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "77137501"
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
>[Dotaz na více prostředků](../log-query/cross-workspace-query.md) v upozorněních protokolu se podporuje v novém [rozhraní scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Ve výchozím nastavení používá Azure Monitor [starší rozhraní api Log Analytics výstrahy](../platform/api-alerts.md) pro vytváření nových pravidel upozornění protokolu z Azure Portal, pokud nepřepnete ze [starší verze rozhraní API upozornění protokolu](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Po přepínači se nové rozhraní API nastaví jako výchozí pro nová pravidla upozornění v Azure Portal a umožní vám vytvořit pravidla pro výstrahy protokolu dotazů mezi prostředky. Pravidla upozornění protokolu [dotazu pro více prostředků](../log-query/cross-workspace-query.md) můžete vytvořit bez toho, aby byl přepínač použit pomocí [šablony ARM pro rozhraní scheduledQueryRules API](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) , ale toto pravidlo upozornění lze spravovat i v případě, že [rozhraní scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) , nikoli z Azure Portal.

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
| Město | client_city |
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

K zobrazení podrobných informací o aplikacích Application Insights použijte [hledání v protokolu](../../azure-monitor/log-query/log-query-overview.md) .
