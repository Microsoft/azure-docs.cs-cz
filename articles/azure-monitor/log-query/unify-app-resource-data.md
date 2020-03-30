---
title: Sjednotit více prostředků Azure Monitor Application Insights | Dokumenty společnosti Microsoft
description: Tento článek obsahuje podrobnosti o tom, jak používat funkci v protokolech monitorování Azure k dotazování více prostředků Application Insights a vizualizovat tato data.
author: bwren
ms.author: bwren
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: ce58aae3b1db1f0f338d353025d4f277aeb6944f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137501"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Sjednocujte více prostředků Azure Monitor Application Insights 
Tento článek popisuje, jak dotazovat a zobrazit všechna data protokolu Application Insights na jednom místě, i když jsou v různých předplatných Azure, jako náhrada za vyřazení application insights konektoru. Počet prostředků Application Insights, které můžete zahrnout do jednoho dotazu, je omezen na 100.

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Doporučený přístup k dotazování více prostředků Application Insights 
Výpis více prostředků Application Insights v dotazu může být těžkopádné a obtížně udržovatelné. Místo toho můžete využít funkci oddělit logiku dotazu z oboru aplikací.  

Tento příklad ukazuje, jak můžete sledovat více prostředků Application Insights a vizualizovat počet neúspěšných požadavků podle názvu aplikace.

Vytvořte funkci pomocí operátoru sjednocení se seznamem aplikací a uložte dotaz do pracovního prostoru jako funkci s *aplikacemi aliasůScoping*. 

Uvedené aplikace můžete kdykoli upravit na portálu tak, že přejdete do průzkumníka dotazů v pracovním prostoru `SavedSearch` a vyberete funkci pro úpravy a následné uložení nebo pomocí rutiny prostředí PowerShell. 

>[!NOTE]
>Tuto metodu nelze použít s výstrahami protokolu, protože ověření přístupu prostředků pravidla výstrahy, včetně pracovních prostorů a aplikací, se provádí v době vytvoření výstrahy. Přidání nových prostředků do funkce po vytvoření výstrahy není podporováno. Pokud dáváte přednost použití funkce pro obory prostředků v výstrahách protokolu, je třeba upravit pravidlo výstrahy na portálu nebo pomocí šablony Správce prostředků a aktualizovat prostředky s vymezeným oborem. Případně můžete zahrnout seznam prostředků do dotazu upozornění protokolu.

Příkaz `withsource= SourceApp` přidá sloupec k výsledkům, který označuje aplikaci, která odeslala protokol. Operátor analýzy je volitelné v tomto příkladu a používá k extrahování název aplikace z SourceApp vlastnost. 

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

Nyní jste připraveni k použití aplikaceScoping funkce v dotazu mezi prostředky:  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

Dotaz používá schéma Application Insights, i když dotaz je spuštěn v pracovním prostoru, protože funkce ApplicationsCoping vrátí datovou strukturu Application Insights. Alias funkce vrátí sjednocení požadavků ze všech definovaných aplikací. Dotaz pak filtruje neúspěšné požadavky a vizualizuje trendy podle aplikace.

![Příklad výsledků křížového dotazu](media/unify-app-resource-data/app-insights-query-results.png)

>[!NOTE]
>[Dotaz mezi prostředky](../log-query/cross-workspace-query.md) ve výstrahách protokolu je podporován v novém [rozhraní API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Ve výchozím nastavení Azure Monitor používá [starší rozhraní API upozornění log Analytics](../platform/api-alerts.md) pro vytváření nových pravidel upozornění protokolu z webu Azure Portal, pokud nepřepnete z [staršího rozhraní API pro výstrahy protokolů](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Po přepnutí se nové rozhraní API stane výchozím pro nová pravidla výstrah na webu Azure Portal a umožňuje vytvářet pravidla upozornění protokolu dotazů mezi prostředky. Můžete vytvořit pravidla výstrah [protokolu dotazů mezi prostředky](../log-query/cross-workspace-query.md) bez provedení přepínače pomocí šablony ARM pro rozhraní API [scheduledQueryRules](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) – ale toto pravidlo výstrahy je spravovatelné prostřednictvím [rozhraní ScheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) a nikoli z portálu Azure.

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Rozdíly ve schématu pracovního prostoru Přehledy aplikací a analýzy protokolů
V následující tabulce jsou uvedeny rozdíly ve schématu mezi analýzou protokolů a přehledy aplikací.  

| Vlastnosti pracovního prostoru Log Analytics| Vlastnosti prostředků Application Insights|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationId | appId|
| ApplicationName | Appname|
| ApplicationTypeVersion | application_Version |
| Počet dostupností | Itemcount |
| DostupnostDoba trvání | doba trvání |
| Zpráva o dostupnosti | zpráva |
| AvailabilityRunLocation | location |
| AvailabilityTestId | id |
| AvailabilityTestName | jméno |
| AvailabilityTimerazítko | časové razítko |
| Prohlížeč | client_browser |
| Město | client_city |
| ClientIP | client_IP |
| Počítač | cloud_RoleInstance | 
| Země | client_CountryOrRegion | 
| CustomEventCount | Itemcount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | jméno | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExceptionCount | Itemcount | 
| ExceptionHandledAt | handledAt |
| Zpráva o výsaze | zpráva | 
| Exceptiontype | type |
| ID operace | operation_id |
| OperationName | operation_Name | 
| Operační systém | client_OS | 
| Počet zobrazení stránky | Itemcount |
| PageViewDuration | doba trvání | 
| Název_ _PageViewName | jméno | 
| Id nadřazené operace | operation_Id | 
| RequestCount | Itemcount | 
| Doba trvání_požadavku | doba trvání | 
| ID požadavku | id | 
| Název_požadavku | jméno | 
| RequestSuccess | úspěch | 
| ResponseCode | resultCode | 
| Role | cloud_RoleName |
| Instance role | cloud_RoleInstance |
| SessionId | Session_id | 
| SourceSystem | operation_SyntheticSource |
| TelemetrieTYpe | type |
| zprostředkovatele identity | url |
| Id uživatelského účtu | user_AccountId |

## <a name="next-steps"></a>Další kroky

Pomocí [funkce Hledání protokolů](../../azure-monitor/log-query/log-query-overview.md) můžete zobrazit podrobné informace o aplikacích Application Insights.
