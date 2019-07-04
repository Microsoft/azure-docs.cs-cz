---
title: Akce Webhooku pro upozornění protokolů ve výstrahách Azure
description: Tento článek popisuje, jak na pravidla upozornění protokolů pomocí pracovního prostoru nebo application insights analytics protokolu zařadí data jako HTTP webhook a podrobnosti o různá přizpůsobení je to možné.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: cad1b0ab484d172000bd62146a88a27bfab1e9f2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448775"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Akce Webhooku pro pravidla upozornění protokolů
Když [upozornění protokolu se vytvoří v Azure](alerts-log.md), máte možnost [konfigurace pomocí skupin akcí](action-groups.md) provést jednu nebo více akcí.  Tento článek popisuje různé webhooku akce, které jsou k dispozici a podrobnosti o tom, jak nakonfigurovat vlastní webhooku založenými na JSON.

> [!NOTE]
> Můžete také použít [společné schéma produktu výstrah](https://aka.ms/commonAlertSchemaDocs), která nabízí výhodu v podobě jediného rozšiřitelné a sjednocené výstrah datová část mezi všechny výstrahy služby ve službě Azure Monitor pro vaše integrace webhooku. [Další informace o běžných výstrah schématu definice.](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Akce Webhooku

Akce Webhooku vám umožňují spuštění externího procesu prostřednictvím jednoho požadavku HTTP POST.  Volané služby by měl podpora webhooků a zjistit, jak pomocí jakékoli datové části přijme.    

Akce Webhooku vyžadovat vlastnosti v následující tabulce:

| Vlastnost | Popis |
|:--- |:--- |
| Adresa URL Webhooku |Adresa URL webhooku. |
| Vlastní datová část JSON |Vlastní datová část k odesílání pomocí webhooku, pokud tato možnost zvolená během vytváření výstrahy. Podrobnosti o adrese [Spravovat výstrahy protokolu](alerts-log.md) |

> [!NOTE]
> Zobrazení – tlačítko Webhooku spolu s *zahrnout vlastní datovou část JSON pro webhook* možnost protokolu upozornění, zobrazí ukázkovou datovou část webhooku pro vlastní nastavení k dispozici. Skutečná data a zástupce schématu JSON pro upozornění protokolu neobsahuje. 

Webhooky zahrnují adresu URL a datové části je ve formátu JSON, který se data odeslaná do externí služby.  Ve výchozím nastavení datová část obsahuje hodnoty v následující tabulce:  Je možné nahradit tuto datovou část vlastní sadou vlastní.  V takovém případě můžete použít proměnné v tabulce pro každý z parametrů zahrnout vlastní datovou část jejich hodnoty.


| Parametr | Proměnná | Popis |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |Název pravidla výstrahy. |
| severity |#severity |Závažnost nastavit upozornění aktivace protokolu. |
| AlertThresholdOperator |#thresholdoperator |Operátor prahové hodnoty pro pravidla upozornění.  *Větší než* nebo *menší než*. |
| AlertThresholdValue |#thresholdvalue |Prahová hodnota pro pravidla upozornění. |
| LinkToSearchResults |#linktosearchresults |Odkaz na portál Analytics, která vrací záznamy z dotazu, která upozornění vytvořila. |
| ResultCount |#searchresultcount |Počet záznamů ve výsledcích hledání. |
| Čas ukončení Interval vyhledávání |#searchintervalendtimeutc |Koncový čas pro dotaz ve standardu UTC, format - mm/dd/rrrr hh: mm: ss dop. / odp. |
| Interval vyhledávání |#searchinterval |Časový interval pro upozornění pravidla, formát: hh: mm:. |
| StartTime Interval vyhledávání |#searchintervalstarttimeutc |Spuštění dotazu ve standardu UTC, format - mm/dd/rrrr hh: mm: ss dop. / odp. 
| SearchQuery |#searchquery |Vyhledávací dotaz log použitý pravidlem upozornění. |
| SearchResults |"IncludeSearchResults": true|Záznamů vrácených dotazem jako tabulka JSON, omezená na prvních 1 000 záznamů. Pokud "IncludeSearchResults": true přidá vlastní definici JSON webhook jako vlastnost nejvyšší úrovně. |
| Typ výstrahy| #alerttype | Typ pravidel upozornění protokolů nakonfigurované – [měření metriky](alerts-unified-log.md#metric-measurement-alert-rules) nebo [číslo výsledky](alerts-unified-log.md#number-of-results-alert-rules).|
| ID pracovního prostoru |#workspaceid |ID pracovního prostoru Log Analytics. |
| ID aplikace |#applicationid |ID Application Insights aplikaci. |
| ID předplatného |#subscriptionid |ID vašeho předplatného Azure používat. 

> [!NOTE]
> LinkToSearchResults předá parametrů, jako je čas SearchQuery, StartTime Interval hledání & Konec intervalu vyhledávání v adrese URL na webu Azure portal pro zobrazení v části Analytics. Portál Azure má identifikátor URI přibližně 2000 znaků. limit velikosti a bude *není* Otevřít odkaz podle výstrahy, pokud překročí uvedené mezní hodnoty parametrů. Uživatele můžete ručně zadat podrobnosti a zobrazit výsledky analýzy portálu nebo pomocí [REST API pro Application Insights Analytics](https://dev.applicationinsights.io/documentation/Using-the-API) nebo [rozhraní API REST služby Log Analytics](/rest/api/loganalytics/) k načtení výsledků prostřednictvím kódu programu 

Například můžete například zadat následující vlastní datovou část, která obsahuje jeden parametr s názvem *text*.  Služba, která volá tento webhook by byl očekáván tento parametr.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Tato datová část příklad by přeložit na podobné následujícímu při odeslání k webhooku.

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Všechny proměnné v webhooku vlastní musí zadat v rámci skříň JSON jako "#searchinterval", výsledná webhooku má také data proměnných ve skříni, jako je "00: 05:00".

Do výsledků hledání zahrnout vlastní datovou část, ujistěte se, že **IncludeSearchResults** je nastaven jako vlastnost v datové části json nejvyšší úrovně. 

## <a name="sample-payloads"></a>Ukázkové datové části
Tato část ukazuje ukázkovou datovou část webhooku pro upozornění protokolů, včetně při datové části je pevná a při jeho vlastní.

### <a name="standard-webhook-for-log-alerts"></a>Standardní Webhooku pro výstrahy protokolu 
Oba tyto příklady uvedli fiktivní datovou část s pouze dvěma sloupci a dva řádky.

#### <a name="log-alert-for-azure-log-analytics"></a>Upozornění protokolu pro Azure Log Analytics
Následuje ukázkovou datovou část pro akce webhooku standardní *bez možnosti vlastní Json* se používají pro upozornění na základě analýzy protokolů.

```json
{
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": "log alert rule",
    "Severity": "Warning",
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
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
 }
 ```

> [!NOTE]
> Pole hodnoty závažnosti může změnit, pokud máte [přepnout dáváte přednost API](alerts-log-api-switch.md) pro upozornění protokolů v Log Analytics.


#### <a name="log-alert-for-azure-application-insights"></a>Upozornění protokolu pro službu Azure Application Insights
Následuje ukázkovou datovou část pro standardní webhook *bez možnosti vlastní Json* pro application insights výstrahy založené na protokolu-.
    
```json
{
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"requests | where resultCode == \"500\"",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "3",
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
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
    "AlertType": "Number of results"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>Upozornění protokolu s vlastní datovou část JSON
Například pokud chcete vytvořit vlastní datovou část, která zahrnuje pouze název výstrahy a výsledky hledání, můžete použít následující: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

Následuje ukázkovou datovou část pro akce webhooku vlastní žádné upozornění protokolu.
    
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
- Další informace o [upozornění protokolů ve výstrahách Azure](alerts-unified-log.md)
- Vysvětlení [Správa upozornění protokolů v Azure](alerts-log.md)
- Vytvoření a správa [skupiny akcí v Azure](action-groups.md)
- Další informace o [Application Insights](../../azure-monitor/app/analytics.md)
- Další informace o [protokolu dotazy](../log-query/log-query-overview.md). 

