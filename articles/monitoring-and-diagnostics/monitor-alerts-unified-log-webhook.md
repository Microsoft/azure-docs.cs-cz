---
title: Akce Webhooku protokolu výstrahy ve výstrahách Azure | Microsoft Docs
description: Tento článek popisuje, jak pro pravidlo výstrahy protokolu pomocí protokolu statistice analýzy nebo aplikace, bude nabízet data jako webhooku protokolu HTTP a další podrobnosti o různých přizpůsobení možné.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: vinagara
ms.openlocfilehash: 981b6b65675550fd1403064ad3113c2dca0c3f6e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34638666"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Akce Webhooku pro pravidla výstrah protokolu
Když [v Azure se vytvoří výstraha ](monitor-alerts-unified-usage.md), máte možnost [konfigurace pomocí akce skupiny](monitoring-action-groups.md) provést několik akcí.  Tento článek popisuje různé webhooku akce, které jsou k dispozici a podrobnosti o konfiguraci vlastních webhooku založenými na JSON.


## <a name="webhook-actions"></a>Akce Webhooku

Akce Webhooku umožňují vyvolání externího procesu prostřednictvím jedné žádosti HTTP POST.  Služba volané by měla podporovat webhooky a určit, jak se použijí všechny datové části obdrží.   Příklady použití webhook, jehož v reakci na výstrahy jsou odesílání zprávy v [Slack](http://slack.com) nebo vytváření incidentu v [PagerDuty](http://pagerduty.com/).  

Akce Webhooku vyžadovat vlastnosti v následující tabulce:

| Vlastnost | Popis |
|:--- |:--- |
| Adresa URL webhooku |Adresa URL webhooku. |
| Vlastní datovou část JSON |Vlastní datovou část odeslat spolu s webhook, když zvolíte tuto možnost během vytváření výstrahy. Podrobnosti jsou dostupné v [Správa výstrah pomocí Azure výstrahy ](monitor-alerts-unified-usage.md) |

> [!NOTE]
> Tlačítko Webhooku souběžně s test *zahrnout vlastní datovou část JSON webhooku* možnost protokolu výstrah, fiktivní volání otestovat adresu URL webhooku se aktivuje. Neobsahuje se skutečná data a zástupce schématu JSON se používá pro výstrahy, protokolu. 

Webhooky zahrnují adresu URL a datovou část ve formátu JSON, který se data odesílají externí služby.  Ve výchozím nastavení, datová část obsahuje hodnoty v následující tabulce: můžete nahradit vlastní jeden vlastní tato datová část.  V takovém případě můžete proměnné v tabulce pro jednotlivé parametry mají být zahrnuty jejich hodnota vaše vlastní datovou část.


| Parametr | Proměnná | Popis |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |Název pravidla výstrahy. |
| Severity |#severity |Závažnost nastavit výstrahy přímým protokolu. |
| AlertThresholdOperator |#thresholdoperator |Operátor prahová hodnota pro pravidlo výstrahy.  *Větší než* nebo *menší než*. |
| AlertThresholdValue |#thresholdvalue |Prahová hodnota pro pravidlo výstrahy. |
| LinkToSearchResults |#linktosearchresults |Odkaz na portálu analýza, který vrátí záznamy v dotazu, který vytvořili výstrahu. |
| Element resultcount element |#searchresultcount |Počet záznamů ve výsledcích hledání. |
| Čas ukončení Interval vyhledávání |#searchintervalendtimeutc |Koncový čas pro dotaz ve standardu UTC, formátu – mm/dd/rrrr hh: mm: ss dop. / odp. |
| Interval vyhledávání |#searchinterval |Časový interval pro výstrahu pravidla, formát: hh: mm:. |
| StartTime Interval vyhledávání |#searchintervalstarttimeutc |Počáteční čas pro dotaz ve standardu UTC, formátu – mm/dd/rrrr hh: mm: ss dop. / odp... 
| SearchQuery |#searchquery |Vyhledávací dotaz protokolu používá pravidlo výstrahy. |
| SearchResults |"IncludeSearchResults": true|Záznamů vrácených dotazem jako tabulku JSON, omezen na prvních 1000 záznamů; Pokud "IncludeSearchResults": true je přidaný do vlastní definici JSON webhooku jako vlastnost nejvyšší úrovně. |
| ID pracovního prostoru |#workspaceid |ID pracovního prostoru analýzy protokolů. |
| ID aplikace |#applicationid |ID vaší aplikace přehled aplikace. |
| ID předplatného |#subscriptionid |ID předplatného Azure používat s Application Insights. 


Například může určit následující vlastní datovou část, která obsahuje jeden parametr s názvem *text*.  Služby, který volá tento webhook by byla očekávána tento parametr.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Tato datová část příkladu by odkazující na něco jako následující odeslání do webhooku.

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Všechny proměnné v vlastní webhooku se musí zadat v rámci JSON skříň jako "#searchinterval", výsledná webhook má také proměnné datové uvnitř skříň jako "00: 05:00".

Zahrnout vlastní datovou část výsledky hledání, ujistěte se, že **IncudeSearchResults** je nastaven jako vlastnost nejvyšší úrovně v datové části json. 

## <a name="sample-payloads"></a>Ukázka datové části
Tato část uvádí datová část ukázky webhooku protokolu výstrahy, včetně při datová část je standardní a při jeho vlastní.

> [!NOTE]
> Pro zajištění zpětné kompatibility, je stejné jako datové části standardní webhooku pro výstrahy pomocí Azure Log Analytics [analýzy protokolů výstrahy správy](../log-analytics/log-analytics-alerts-creating.md). Ale pro výstrahy protokolu pomocí [Application Insights](../application-insights/app-insights-analytics.md), datové části standardní webhooku je založena na schéma akce skupiny.

### <a name="standard-webhook-for-log-alerts"></a>Standardní Webhooku protokolu výstrahy 
Obě tyto příklady uvedli fiktivní datové části s pouze dva sloupce a dva řádky.

#### <a name="log-alert-for-azure-log-analytics"></a>Výstraha protokolu pro Azure analýzy protokolů
Následuje ukázka datové části pro akci standardní webhooku *bez vlastní možnost Json* používá pro výstrahy na základě analýzy protokolů.

```json
{
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        },
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Warning"
 }
 ```   

#### <a name="log-alert-for-azure-application-insights"></a>Výstraha protokolu pro službu Azure Application Insights
Následuje ukázka datové části pro standardní webhooku *bez vlastní možnost Json* při použití pro application insights výstrahy na základě protokolu-.
    
```json
{
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        },
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://analytics.applicationinsights.io/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Error",
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>Výstraha protokolu s vlastní datovou část JSON
Například pokud chcete vytvořit vlastní datovou část, která obsahuje jenom název výstrahy a výsledky hledání, můžete použít následující: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

Následuje ukázka datové části pro akci vlastní webhooku pro všechny výstrahy protokolu.
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    }
```


## <a name="next-steps"></a>Další postup
- Další informace o [protokolu výstrahy ve výstrahách Azure ](monitor-alerts-unified-log.md)
- Vytvoření a správa [akce skupin v Azure](monitoring-action-groups.md)
- Další informace o [Application Insights](../application-insights/app-insights-analytics.md)
- Další informace o [analýzy protokolů](../log-analytics/log-analytics-overview.md). 