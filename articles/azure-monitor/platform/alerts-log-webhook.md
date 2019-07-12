---
title: Akce Webhooku pro upozornění protokolů ve výstrahách Azure
description: Tento článek popisuje, jak vytvořit pravidlo upozornění protokolu pomocí pracovního prostoru Log Analytics nebo Application Insights, jak výstrahy odesílá data jako webhook služby protokolu HTTP a podrobnosti o různá přizpůsobení, které jsou možné.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 6aa007c621e76cb0c188a7dab6279fd9e387b2b3
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705195"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Akce Webhooku pro pravidla upozornění protokolů
Když [upozornění protokolu se vytvoří v Azure](alerts-log.md), máte možnost [konfigurace s využitím skupin akcí](action-groups.md) provést jednu nebo více akcí. Tento článek popisuje různé webhooku akce, které jsou k dispozici a ukazuje, jak nakonfigurovat vlastní webhooku založenými na JSON.

> [!NOTE]
> Můžete také použít [společné schéma produktu upozornění](https://aka.ms/commonAlertSchemaDocs) pro vaše integrace webhooku. Ve společném schématu produktu výstrah nabízí výhodu v podobě jedné rozšiřitelné a sjednocené výstrah datové části napříč všemi službami výstrah ve službě Azure Monitor. [Další informace o běžných výstrah schématu definice.](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Akce Webhooku

Pomocí akce webhooku můžete vyvolat externí proces prostřednictvím jednoho požadavku HTTP POST. Služba, která je volána by měl podpora webhooků a určit, jak se pomocí jakékoli datové části, které obdrží.

Akce Webhooku vyžadují vlastnosti v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| **Webhook URL** |Adresa URL webhooku. |
| **Vlastní datová část JSON** |Vlastní datová část k odesílání pomocí webhooku, pokud tato možnost zvolená během vytváření výstrahy. Další informace najdete v tématu [spravovat upozornění protokolů](alerts-log.md).|

> [!NOTE]
> **Zobrazení Webhooku** tlačítko vedle **zahrnout vlastní datovou část JSON pro webhook** možnost pro zobrazení upozornění protokolu ukázkovou datovou část webhooku pro přizpůsobení, která byla k dispozici. Nemůže obsahovat skutečná data, ale je zástupce schématu JSON, který se používá pro výstrahy protokolu. 

Webhooky zahrnují adresu URL a datové části je ve formátu JSON, který se data odesílají do externí služby. Ve výchozím nastavení datová část obsahuje hodnoty v následující tabulce. Je možné nahradit tuto datovou část vlastní sadou vlastní. V takovém případě použijte proměnné v tabulce pro každý z parametrů zahrnout vlastní datovou část jejich hodnoty.


| Parametr | Proměnná | Popis |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |Název pravidla výstrahy. |
| *Závažnost* |#severity |Závažnost nastavit upozornění aktivace protokolu. |
| *AlertThresholdOperator* |#thresholdoperator |Operátor prahové hodnoty pro pravidlo výstrahy, která používá větší nebo menší než. |
| *AlertThresholdValue* |#thresholdvalue |Prahová hodnota pro pravidla upozornění. |
| *LinkToSearchResults* |#linktosearchresults |Odkaz na portál Analytics, která vrací záznamy z dotazu, která upozornění vytvořila. |
| *ResultCount* |#searchresultcount |Počet záznamů ve výsledcích hledání. |
| *Čas ukončení Interval vyhledávání* |#searchintervalendtimeutc |Koncový čas pro dotazování ve standardu UTC, pomocí formátu mm/dd/rrrr hh: mm: ss dop. / odp. |
| *Interval vyhledávání* |#searchinterval |Časový interval pro pravidlo výstrahy, ve formátu hh: mm:. |
| *StartTime Interval vyhledávání* |#searchintervalstarttimeutc |Počáteční čas pro dotaz ve standardu UTC, pomocí formátu mm/dd/rrrr hh: mm: ss dop. / odp. 
| *SearchQuery* |#searchquery |Vyhledávací dotaz log použitý pravidlem upozornění. |
| *SearchResults* |"IncludeSearchResults": true|Záznamů vrácených dotazem jako tabulka JSON, omezená na prvních 1 000 záznamů, pokud "IncludeSearchResults": true přidá vlastní definici JSON webhook jako vlastnost nejvyšší úrovně. |
| *Typ výstrahy*| #alerttype | Typ pravidel upozornění protokolů nakonfigurovaný jako [měření metriky](alerts-unified-log.md#metric-measurement-alert-rules) nebo [počet výsledků](alerts-unified-log.md#number-of-results-alert-rules).|
| *WorkspaceID* |#workspaceid |ID pracovního prostoru Log Analytics. |
| *ID aplikace* |#applicationid |ID služby Application Insights aplikaci. |
| *ID předplatného* |#subscriptionid |ID vašeho předplatného Azure používat. 

> [!NOTE]
> *LinkToSearchResults* předává parametry jako *SearchQuery*, *StartTime Interval vyhledávání*, a *čas ukončení Interval vyhledávání* v adrese URL do Azure portál pro zobrazení v sekci Analytics. Na webu Azure portal má maximální velikost URI přibližně 2 000 znaků. Na portálu se *není* odkazy, které jsou součástí výstrahy, pokud hodnoty parametrů překračují limit. Můžete ručně zadat podrobnosti a zobrazit výsledky na portálu Analytics. Nebo můžete použít [REST API pro Application Insights Analytics](https://dev.applicationinsights.io/documentation/Using-the-API) nebo [rozhraní API REST služby Log Analytics](/rest/api/loganalytics/) programově načíst výsledky. 

Například můžete například zadat následující vlastní datovou část, která obsahuje jeden parametr s názvem *text*. Služba, která volá tento webhook očekává, že tento parametr.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Tato datová část příkladu překládá na podobné následujícímu odeslání do webhook:

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Vzhledem k tomu, že všechny proměnné v webhooku vlastní musí být zadán v rámci JSON skříň, jako je "#searchinterval," výsledná webhooku má také data proměnných uvnitř skříně, například "00: 05:00."

Do výsledků hledání zahrnout vlastní datovou část, ujistěte se, že **IncludeSearchResults** je nastaven jako vlastnost v datové části JSON nejvyšší úrovně. 

## <a name="sample-payloads"></a>Ukázkové datové části
Tato část ukazuje ukázkové datové části pro webhooky pro výstrahy protokolu. Ukázkové datové části obsahují příklady po standard a kdy se jedná vlastní datovou část.

### <a name="standard-webhook-for-log-alerts"></a>Standardní webhooku pro výstrahy protokolu 
Oba tyto příklady mají fiktivní datovou část s pouze dvěma sloupci a dva řádky.

#### <a name="log-alert-for-log-analytics"></a>Upozornění protokolů pro Log Analytics
Následující ukázkovou datovou část je pro akce webhooku standardní *bez vlastní možnost JSON* , který se používá pro výstrahy založené na Log Analytics:

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
> Hodnota pole "Závažnost" může změnit, pokud jste [přepnout dáváte přednost API](alerts-log-api-switch.md) pro upozornění protokolů v Log Analytics.


#### <a name="log-alert-for-application-insights"></a>Upozornění protokolu pro službu Application Insights
Následující ukázkovou datovou část je standardní webhooku *bez vlastní možnost JSON* Pokud je použit pro upozornění protokolu podle Application Insights:
    
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

Následující ukázkovou datovou část je pro akce webhooku vlastní žádné upozornění protokolu:
    
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


## <a name="next-steps"></a>Další kroky
- Další informace o [upozornění protokolů ve službě Azure výstrahy](alerts-unified-log.md).
- Pochopit postup [spravovat upozornění protokolů v Azure](alerts-log.md).
- Vytvoření a správa [skupiny akcí v Azure](action-groups.md).
- Další informace o [Application Insights](../../azure-monitor/app/analytics.md).
- Další informace o [protokolu dotazy](../log-query/log-query-overview.md). 

