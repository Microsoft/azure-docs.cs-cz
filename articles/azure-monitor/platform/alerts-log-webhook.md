---
title: Akce Webhooku pro výstrahy protokolu v upozorněních Azure
description: Popisuje, jak nakonfigurovat nabízená oznámení protokolu pomocí akce Webhooku a dostupných úprav.
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 06/25/2019
ms.subservice: alerts
ms.openlocfilehash: 9a074be9bcc62d8c20635400f462f52fb796d2fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91294304"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Akce webhooku pro pravidla výstrah protokolu

[Výstraha protokolu](alerts-log.md) podporuje [konfiguraci skupin akcí Webhooku](action-groups.md#webhook). V tomto článku popíšeme, které vlastnosti jsou k dispozici a jak nakonfigurovat vlastní Webhook JSON.

> [!NOTE]
> Vlastní Webhook založený na JSON se v současnosti ve verzi rozhraní API nepodporuje. `2020-05-01-preview`

> [!NOTE]
> Pro integraci Webhooku se doporučuje použít [běžné schéma výstrah](alerts-common-schema.md) . Běžné schéma výstrah poskytuje výhodu pro jednu rozšiřitelnou a jednotnou datovou část pro všechny služby výstrah v Azure Monitor. Pro pravidla upozornění protokolů, která mají definovanou vlastní datovou část JSON, a povolením společného schématu se obnoví schéma datové části [na tu popsanou.](alerts-common-schema-definitions.md#log-alerts) Výstrahy s povoleným společným schématem mají omezení horní velikosti 256 KB na jednu výstrahu, vyšší výstraha nebude zahrnovat výsledky hledání. Pokud nejsou zahrnuty výsledky hledání, měli byste použít `LinkToFilteredSearchResultsAPI` nebo `LinkToSearchResultsAPI` pro přístup k výsledkům dotazu pomocí rozhraní Log Analytics API.

## <a name="webhook-payload-properties"></a>Vlastnosti datové části Webhooku

Akce Webhooku umožňují vyvolat jednu žádost HTTP POST. Služba, která je volána, by měla podporovat Webhooky a zjistit, jak lze použít datovou část, kterou přijímá.

Výchozí vlastnosti akce Webhooku a jejich vlastní názvy parametrů JSON:

| Parametr | Proměnná | Popis |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |Název pravidla výstrahy. |
| *Závažnost* |#severity |Závažnost nastavená pro výstrahu protokolu, která se vyvolala. |
| *AlertThresholdOperator* |#thresholdoperator |Prahová hodnota pro pravidlo výstrahy. |
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
| *Typ výstrahy*| #alerttype | Typ pravidla upozornění protokolu nakonfigurovaného jako [měření metriky nebo počet výsledků](alerts-unified-log.md#measure).|
| *ID pracovního prostoru* |#workspaceid |ID vašeho pracovního prostoru Log Analytics |
| *ID aplikace* |#applicationid |ID vaší aplikace Application Insights |
| *ID předplatného* |#subscriptionid |ID vašeho předplatného Azure |

## <a name="custom-webhook-payload-definition"></a>Vlastní definice datové části Webhooku

K získání vlastní datové části JSON pomocí výše uvedených parametrů můžete použít **vlastní datovou část JSON pro Webhook** . Můžete také vygenerovat další vlastnosti.
Můžete například zadat následující vlastní datovou část, která obsahuje jeden parametr s názvem *text*. Služba, kterou tato volání Webhooku očekává tento parametr:

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
Ve skříni JSON musí být zadané proměnné ve vlastním Webhooku. Například odkazování "#searchresultcount" ve výše uvedeném příkladu Webhooku bude mít výstup na základě výsledků výstrah.

Chcete-li zahrnout výsledky hledání, přidejte **IncludeSearchResults** jako vlastnost nejvyšší úrovně ve vlastním formátu JSON. Výsledky hledání jsou zahrnuté jako struktura JSON, takže výsledky nejde odkazovat ve vlastních definovaných polích. 

> [!NOTE]
> Tlačítko pro **zobrazení Webhooku** vedle možnosti **zahrnout vlastní datovou část JSON pro Webhook** zobrazí náhled toho, co se vám poskytlo. Neobsahuje skutečná data, ale je reprezentativní pro schéma JSON, které se bude používat. 

## <a name="sample-payloads"></a>Ukázková datová část
Tato část ukazuje ukázkovou datovou část pro Webhooky pro výstrahy protokolu. Ukázková datová část obsahuje příklady v případě, kdy je datová část standardní a kdy je vlastní.

### <a name="log-alert-for-log-analytics"></a>Výstraha protokolu pro Log Analytics
Následující ukázková datová část je určena pro akci standardního Webhooku, která se používá pro výstrahy na základě Log Analytics:

> [!NOTE]
> Hodnota pole "závažnost" se změní, pokud jste [přešli na aktuální rozhraní scheduledQueryRules API](alerts-log-api-switch.md) z [rozhraní API pro upozornění na starší verzi Log Analytics](api-alerts.md).

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

### <a name="log-alert-for-application-insights"></a>Výstraha protokolu pro Application Insights
Následující ukázková datová část je určena pro standardní Webhook, pokud se používá pro výstrahy protokolu na základě Application Insightsch prostředků:
    
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

### <a name="log-alert-for-other-resources-logs-from-api-version-2020-05-01-preview"></a>Výstraha protokolu pro další protokoly prostředků (z verze rozhraní API `2020-05-01-preview` )

> [!NOTE]
> Pro verzi rozhraní API `2020-05-01-preview` a výstrahy protokolu orientované na prostředky se momentálně neúčtují žádné další poplatky.  Ceny pro funkce, které jsou ve verzi Preview, budou v budoucnu ohlášeny a oznámení poskytované před zahájením fakturace. Pokud se rozhodnete dál používat novou verzi rozhraní API a výstrahy protokolu orientované na prostředky po období oznámení, bude se vám účtovat příslušná sazba.

Následující ukázková datová část je určena pro standardní Webhook, pokud se používá pro výstrahy protokolu na základě jiných protokolů prostředků (kromě pracovních prostorů a Application Insights):

```json
{
    "schemaId": "azureMonitorCommonAlertSchema",
    "data": {
        "essentials": {
            "alertId": "/subscriptions/12345a-1234b-123c-123d-12345678e/providers/Microsoft.AlertsManagement/alerts/12345a-1234b-123c-123d-12345678e",
            "alertRule": "AcmeRule",
            "severity": "Sev4",
            "signalType": "Log",
            "monitorCondition": "Fired",
            "monitoringService": "Log Alerts V2",
            "alertTargetIDs": [
                "/subscriptions/12345a-1234b-123c-123d-12345678e/resourcegroups/ai-engineering/providers/microsoft.compute/virtualmachines/testvm"
            ],
            "originAlertId": "123c123d-1a23-1bf3-ba1d-dd1234ff5a67",
            "firedDateTime": "2020-07-09T14:04:49.99645Z",
            "description": "log alert rule V2",
            "essentialsVersion": "1.0",
            "alertContextVersion": "1.0"
        },
        "alertContext": {
            "properties": null,
            "conditionType": "LogQueryCriteria",
            "condition": {
                "windowSize": "PT10M",
                "allOf": [
                    {
                        "searchQuery": "Heartbeat",
                        "metricMeasure": null,
                        "targetResourceTypes": "['Microsoft.Compute/virtualMachines']",
                        "operator": "LowerThan",
                        "threshold": "1",
                        "timeAggregation": "Count",
                        "dimensions": [
                            {
                                "name": "ResourceId",
                                "value": "/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm"
                            }
                        ],
                        "metricValue": 0.0,
                        "failingPeriods": {
                            "numberOfEvaluationPeriods": 1,
                            "minFailingPeriodsToAlert": 1
                        },
                        "linkToSearchResultsUI": "https://portal.azure.com#@12f345bf-12f3-12af-12ab-1d2cd345db67/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%2F12345a-1234b-123c-123d-12345678e%2FresourceGroups%2FTEST%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2Ftestvm%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToFilteredSearchResultsUI": "https://portal.azure.com#@12f345bf-12f3-12af-12ab-1d2cd345db67/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%2F12345a-1234b-123c-123d-12345678e%2FresourceGroups%2FTEST%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2Ftestvm%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z"
                    }
                ],
                "windowStartTime": "2020-07-07T13:54:34Z",
                "windowEndTime": "2020-07-09T13:54:34Z"
            }
        }
    }
}
```

### <a name="log-alert-with-a-custom-json-payload"></a>Výstraha protokolu s vlastní datovou částí JSON
Chcete-li například vytvořit vlastní datovou část, která bude obsahovat pouze název výstrahy a výsledky hledání, použijte tuto konfiguraci: 

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
- Přečtěte si další informace o [Application Insights](../log-query/log-query-overview.md).
- Přečtěte si další informace o [dotazech protokolu](../log-query/log-query-overview.md). 
