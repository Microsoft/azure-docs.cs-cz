---
title: Akce Webhooku pro výstrahy protokolu v upozorněních Azure
description: Tento článek popisuje, jak vytvořit pravidlo upozornění protokolu pomocí Log Analytics pracovní prostor nebo Application Insights, jak výstraha nahraje data jako Webhook HTTP, a podrobnosti o různých přizpůsobeních, která jsou možná.
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 06/25/2019
ms.subservice: alerts
ms.openlocfilehash: 6c9bacfc4354351cbbf2eb735414ff3334cd7d0a
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2020
ms.locfileid: "84323667"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Akce Webhooku pro pravidla upozornění protokolu
Když [se v Azure vytvoří výstraha protokolu](alerts-log.md), máte možnost [ji nakonfigurovat pomocí skupin akcí](action-groups.md) , aby se provedla jedna nebo více akcí. Tento článek popisuje různé akce Webhooku, které jsou k dispozici, a ukazuje, jak nakonfigurovat vlastní Webhook založený na formátu JSON.

> [!NOTE]
> Můžete také použít [běžné schéma výstrah](https://aka.ms/commonAlertSchemaDocs) pro vaše integrace Webhooku. Běžné schéma výstrah poskytuje výhodu pro jednu rozšiřitelnou a Sjednocenou datovou část pro všechny služby výstrah v Azure Monitor. Upozorňujeme, že běžné schéma výstrah nezpůsobuje vlastní možnost JSON pro výstrahy protokolu. Pokud je tato možnost zvolena bez ohledu na vlastní nastavení, které jste provedli na úrovni pravidla výstrahy, se odloží k běžné datové části schématu výstrah. [Přečtěte si o běžných definicích schémat výstrah.](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Akce webhooků

Pomocí akcí Webhooku můžete vyvolat externí proces prostřednictvím jedné žádosti HTTP POST. Služba, která má být volána, by měla podporovat Webhooky a určit, jak se má používat jakákoli datová část, kterou přijímá.

Akce Webhooku vyžadují vlastnosti v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| **Adresa URL Webhooku** |Adresa URL Webhooku |
| **Vlastní datová část JSON** |Vlastní datová část, která se má poslat pomocí Webhooku, když se vybere tato možnost během vytváření výstrahy. Další informace najdete v tématu [Správa upozornění protokolu](alerts-log.md).|

> [!NOTE]
> Tlačítko pro **zobrazení Webhooku** spolu s možností **zahrnout vlastní datovou část JSON pro Webhook** pro výstrahu protokolu zobrazuje ukázkovou datovou část Webhooku pro zadané přizpůsobení. Neobsahuje skutečná data, ale je reprezentativní pro schéma JSON, které se používá pro výstrahy protokolu. 

Webhooky zahrnují adresu URL a datovou část formátovanou ve formátu JSON, kterou data odesílaná do externí služby. Ve výchozím nastavení datová část zahrnuje hodnoty v následující tabulce. Tuto datovou část si můžete nahradit vlastní vlastní. V takovém případě použijte proměnné v tabulce pro každý z parametrů k zahrnutí jejich hodnot do vlastní datové části.


| Parametr | Proměnná | Description |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |Název pravidla výstrahy. |
| *Závažnost* |#severity |Závažnost nastavená pro výstrahu protokolu, která se vyvolala. |
| *AlertThresholdOperator* |#thresholdoperator |Operátor prahové hodnoty pro pravidlo výstrahy, které používá hodnotu větší než nebo menší než. |
| *AlertThresholdValue* |#thresholdvalue |Prahová hodnota pro pravidlo výstrahy. |
| *LinkToSearchResults* |#linktosearchresults |Připojte se k portálu Analytics, který vrací záznamy z dotazu, který výstrahu vytvořil. |
| *LinkToSearchResultsAPI* |#linktosearchresultsapi |Odkaz na analytické rozhraní API, které vrátí záznamy z dotazu, který výstrahu vytvořil. |
| *LinkToFilteredSearchResultsUI* |#linktofilteredsearchresultsui |Připojte se k portálu Analytics, který vrátí záznamy z dotazu filtrované kombinacemi hodnot dimenzí, které výstrahu vytvořily. |
| *LinkToFilteredSearchResultsAPI* |#linktofilteredsearchresultsapi |Propojte na analytické rozhraní API, které vrátí záznamy z dotazu filtrované kombinacemi hodnot dimenzí, které výstrahu vytvořily. |
| *Element resultcount nastavený* |#searchresultcount |Počet záznamů ve výsledcích hledání. |
| *Čas ukončení intervalu hledání* |#searchintervalendtimeutc |Čas ukončení dotazu ve standardu UTC, ve formátu MM/DD/RRRR HH: mm: ss dop./odp. |
| *Interval hledání* |#searchinterval |Časový interval pro pravidlo výstrahy, ve formátu HH: mm: ss. |
| *Interval hledání Čas_spuštění* |#searchintervalstarttimeutc |Čas spuštění dotazu ve standardu UTC, ve formátu MM/DD/RRRR HH: mm: ss dop./odp. 
| *SearchQuery* |#searchquery |Dotaz na hledání protokolu používaný pravidlem výstrahy |
| *SearchResults* |"IncludeSearchResults": true|Záznamy vrácené dotazem jako tabulka JSON omezené na prvních 1 000 záznamů. "IncludeSearchResults": hodnota true je přidána do vlastní definice Webhooku JSON jako vlastnost nejvyšší úrovně. |
| *Dimenze* |"IncludeDimensions": true|Kombinace hodnot dimenzí, které aktivovaly výstrahu jako oddíl JSON. "IncludeDimensions": hodnota true je přidána do vlastní definice Webhooku JSON jako vlastnost nejvyšší úrovně. |
| *Typ výstrahy*| #alerttype | Typ pravidla upozornění protokolu nakonfigurovaného jako [měření metriky](alerts-unified-log.md#metric-measurement-alert-rules) nebo [počet výsledků](alerts-unified-log.md#number-of-results-alert-rules).|
| *ID pracovního prostoru* |#workspaceid |ID vašeho pracovního prostoru Log Analytics |
| *ID aplikace* |#applicationid |ID vaší aplikace Application Insights |
| *ID předplatného* |#subscriptionid |ID vašeho předplatného Azure 

> [!NOTE]
> Zadané odkazy předává parametry, jako je *SearchQuery*, *čas_spuštění intervalu hledání*a *koncový čas intervalu hledání* v adrese URL pro Azure Portal nebo rozhraní API.

Můžete například zadat následující vlastní datovou část, která obsahuje jeden parametr s názvem *text*. Služba, kterou toto volání Webhooku očekává tento parametr.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Tento příklad datové části se překládá na něco podobného jako při odeslání do Webhooku jako následující:

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Vzhledem k tomu, že všechny proměnné ve vlastním Webhooku je nutné zadat v rámci skříně JSON, jako je například "#searchinterval", výsledný Webhook má také data proměnných uvnitř skříní, například "00:05:00".

Chcete-li zahrnout výsledky hledání do vlastní datové části, zajistěte, aby byla v datové části JSON nastavena vlastnost **IncludeSearchResults** jako vlastnost nejvyšší úrovně. 

## <a name="sample-payloads"></a>Ukázková datová část
Tato část ukazuje ukázkovou datovou část pro Webhooky pro výstrahy protokolu. Ukázková datová část obsahuje příklady v případě, kdy je datová část standardní a kdy je vlastní.

### <a name="standard-webhook-for-log-alerts"></a>Standardní Webhook pro výstrahy protokolu 
Oba tyto příklady mají zástupnou datovou část, která má pouze dva sloupce a dva řádky.

#### <a name="log-alert-for-log-analytics"></a>Výstraha protokolu pro Log Analytics
Následující ukázková datová část je určena pro akci standardního Webhooku *bez vlastní volby JSON* , která se používá pro výstrahy na základě Log Analytics:

```json
{
    "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
    "AlertRuleName": "AcmeRule",
    "SearchQuery": "Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToFilteredSearchResultsUI": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "LinkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "Description": "log alert rule",
    "Severity": "Warning",
    "AffectedConfigurationItems": [
        "INC-Gen2Alert"
    ],
    "Dimensions": [
        {
            "name": "Computer",
            "value": "INC-Gen2Alert"
        }
    ],
    "SearchResult": {
        "tables": [
            {
                "name": "PrimaryResult",
                "columns": [
                    {
                        "name": "$table",
                        "type": "string"
                    },
                    {
                        "name": "Computer",
                        "type": "string"
                    },
                    {
                        "name": "TimeGenerated",
                        "type": "datetime"
                    }
                ],
                "rows": [
                    [
                        "Fabrikam",
                        "33446677a",
                        "2018-02-02T15:03:12.18Z"
                    ],
                    [
                        "Contoso",
                        "33445566b",
                        "2018-02-02T15:16:53.932Z"
                    ]
                ]
            }
        ]
    },
    "WorkspaceId": "12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
}
 ```

> [!NOTE]
> Hodnota pole závažnost se může změnit, pokud jste [přepnuli předvolby rozhraní API](alerts-log-api-switch.md) pro výstrahy protokolu na Log Analytics.


#### <a name="log-alert-for-application-insights"></a>Výstraha protokolu pro Application Insights
Následující ukázková datová část je určena pro standardní Webhook *bez vlastní volby JSON* , pokud se používá pro výstrahy protokolu na základě Application Insights:
    
```json
{
    "schemaId": "Microsoft.Insights/LogAlert",
    "data": {
        "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
        "AlertRuleName": "AcmeRule",
        "SearchQuery": "requests | where resultCode == \"500\" | summarize AggregatedValue = Count by bin(Timestamp, 5m), IP",
        "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
        "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
        "AlertThresholdOperator": "Greater Than",
        "AlertThresholdValue": 0,
        "ResultCount": 2,
        "SearchIntervalInSeconds": 3600,
        "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToFilteredSearchResultsUI": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "LinkToFilteredSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "Description": null,
        "Severity": "3",
        "Dimensions": [
            {
                "name": "IP",
                "value": "1.1.1.1"
            }
        ],
        "SearchResult": {
            "tables": [
                {
                    "name": "PrimaryResult",
                    "columns": [
                        {
                            "name": "$table",
                            "type": "string"
                        },
                        {
                            "name": "Id",
                            "type": "string"
                        },
                        {
                            "name": "Timestamp",
                            "type": "datetime"
                        }
                    ],
                    "rows": [
                        [
                            "Fabrikam",
                            "33446677a",
                            "2018-02-02T15:03:12.18Z"
                        ],
                        [
                            "Contoso",
                            "33445566b",
                            "2018-02-02T15:16:53.932Z"
                        ]
                    ]
                }
            ]
        },
        "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
        "AlertType": "Metric measurement"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>Výstraha protokolu s vlastní datovou částí JSON
Chcete-li například vytvořit vlastní datovou část, která bude obsahovat pouze název výstrahy a výsledky hledání, můžete použít následující: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

Následující ukázková datová část je určena pro vlastní akci Webhooku pro jakékoli upozornění protokolu:
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResults":
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


## <a name="next-steps"></a>Další kroky
- Přečtěte si informace o [upozorněních protokolu v Azure Alerts](alerts-unified-log.md).
- Naučte se [Spravovat výstrahy protokolu v Azure](alerts-log.md).
- Vytváření a Správa [skupin akcí v Azure](action-groups.md).
- Přečtěte si další informace o [Application Insights](../../azure-monitor/app/analytics.md).
- Přečtěte si další informace o [dotazech protokolu](../log-query/log-query-overview.md). 

