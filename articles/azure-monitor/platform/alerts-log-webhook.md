---
title: Akce Webhooku pro výstrahy protokolu ve výstrahách Azure
description: Tento článek popisuje, jak vytvořit pravidlo výstrahprotokolu pomocí pracovního prostoru Analýzy protokolů nebo application insights, jak výstraha odesílá data jako http webhooku a podrobnosti o různých přizpůsobeních, které jsou možné.
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 06/25/2019
ms.subservice: alerts
ms.openlocfilehash: 7b1956ad2bf9bf38ba9edc4c7234078557564071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667699"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Akce Webhooku pro pravidla výstrah protokolu
Když [se v Azure vytvoří výstraha protokolu](alerts-log.md), máte možnost [ji nakonfigurovat pomocí skupin akcí](action-groups.md) k provedení jedné nebo více akcí. Tento článek popisuje různé akce webhooku, které jsou k dispozici, a ukazuje, jak nakonfigurovat vlastní webhook založený na JSON.

> [!NOTE]
> Můžete také použít [společné schéma výstrah](https://aka.ms/commonAlertSchemaDocs) pro vaše integrace webhooku. Společné schéma výstrah poskytuje výhodu s jednu rozšiřitelnou a jednotnou datovou část výstrahy ve všech službách výstrah v Azure Monitor.Upozorňujeme, že společné schéma výstrah nesplňuje vlastní možnost JSON pro výstrahy protokolu. Odkládá se na běžnou datovou část schématu výstrah, pokud je vybrána bez ohledu na vlastní nastavení, které jste mohli provést na úrovni pravidla výstrahy. [Seznamte se s definicemi schématu běžných výstrah.](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Akce webhooků

S akcemi webhooku můžete vyvolat externí proces prostřednictvím jediného požadavku HTTP POST. Služba, která se nazývá by měla podporovat webhooky a určit, jak používat všechny datové části obdrží.

Akce webhooku vyžadují vlastnosti v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| **Adresa URL webového háku** |Adresa URL webhooku. |
| **Vlastní datová část JSON** |Vlastní datová část odeslat s webhooku, když je vybrána tato možnost během vytváření výstrah. Další informace naleznete v [tématu Správa výstrah protokolu](alerts-log.md).|

> [!NOTE]
> Tlačítko **Zobrazit webhooku** vedle **možnosti Zahrnout vlastní datovou část JSON pro webhookpro** výstrahu protokolu zobrazuje ukázkovou datovou část webhooku pro vlastní nastavení, které bylo poskytnuto. Neobsahuje skutečná data, ale je zástupcem schématu JSON, který se používá pro výstrahy protokolu. 

Webhooky obsahují adresu URL a datovou část formátovanou v JSON, že data odeslaná externí službě. Ve výchozím nastavení obsahuje datová část hodnoty v následující tabulce. Můžete se rozhodnout nahradit tuto datovou část vlastní vlastní. V takovém případě použijte proměnné v tabulce pro každý z parametrů zahrnout jejich hodnoty ve vlastní datové části.


| Parametr | Proměnná | Popis |
|:--- |:--- |:--- |
| *Název_výstrahy* |#alertrulename |Název pravidla výstrahy. |
| *Závažnost* |#severity |Závažnost nastavena pro výstrahu vypalování protokolu. |
| *AlertThresholdOperator* |#thresholdoperator |Operátor Prahová hodnota pro pravidlo výstrahy, který používá větší nebo menší než. |
| *AlertThresholdValue* |#thresholdvalue |Prahová hodnota pro pravidlo výstrahy. |
| *Výsledky linktovyhledávání* |#linktosearchresults |Odkaz na portál Analytics, který vrací záznamy z dotazu, který výstrahu vytvořil. |
| *Počet výsledků* |#searchresultcount |Počet záznamů ve výsledcích hledání. |
| *Čas ukončení intervalu hledání* |#searchintervalendtimeutc |Čas ukončení dotazu v utc, s formátem mm/dd/yyyy HH:mm:ss AM/PM. |
| *Interval hledání* |#searchinterval |Časové okno pro pravidlo výstrahy s formátem HH:mm:ss. |
| *Interval hledání StartTime* |#searchintervalstarttimeutc |Čas zahájení dotazu v utc, s formátem mm/dd/yyyy HH:mm:ss AM/PM. 
| *Vyhledávací dotaz* |#searchquery |Protokolovat vyhledávací dotaz používaný pravidlem výstrahy. |
| *HledatVýsledky* |"IncludeSearchResults": true|Záznamy vrácené dotazem jako tabulka JSON, omezené na prvních 1 000 záznamů, pokud "IncludeSearchResults": true je přidán do vlastní definice json webhooku jako vlastnost nejvyšší úrovně. |
| *Typ výstrahy*| #alerttype | Typ pravidla upozornění protokolu nakonfigurovaný jako [metrické měření](alerts-unified-log.md#metric-measurement-alert-rules) nebo [počet výsledků](alerts-unified-log.md#number-of-results-alert-rules).|
| *Id pracovního prostoru* |#workspaceid |ID pracovního prostoru Analýzy protokolů. |
| *ID aplikace* |#applicationid |ID aplikace Application Insights. |
| *ID předplatného* |#subscriptionid |ID použitého předplatného Azure. 

> [!NOTE]
> *LinkToSearchResults* předá parametry jako *SearchQuery*, *Interval hledání StartTime*a *Čas ukončení intervalu hledání* v adrese URL na portálu Azure pro zobrazení v části Analytics. Portál Azure má limit velikosti IDENTIFIKÁTORU URI přibližně 2 000 znaků. Portál *neotevře* odkazy poskytované v výstrahách, pokud hodnoty parametrů překročí limit. Můžete ručně zadat podrobnosti a zobrazit výsledky na portálu Analytics. Nebo můžete použít [rozhraní REST API Analýzy aplikačních poznatků](https://dev.applicationinsights.io/documentation/Using-the-API) nebo [rozhraní REST API analýzy protokolů](/rest/api/loganalytics/) k programovému načtení výsledků. 

Můžete například zadat následující vlastní datovou část, která obsahuje jeden parametr nazývaný *text*. Služba, která volá tento webhooku očekává tento parametr.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
V tomto příkladu datové části překládá na něco jako následující, když je odeslána do webhooku:

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Vzhledem k tomu, že všechny proměnné ve vlastním webhooku musí být zadány v rámci přílohy JSON, jako je "#searchinterval", výsledný webhook má také proměnná data uvnitř skříní, jako je "00:05:00.".

Chcete-li zahrnout výsledky hledání do vlastní datové části, ujistěte se, že **IncludeSearchResults** je nastavena jako vlastnost nejvyšší úrovně v datové části JSON. 

## <a name="sample-payloads"></a>Ukázkové užitečné zatížení
Tato část zobrazuje ukázkové datové části pro webhooky pro výstrahy protokolu. Ukázkové datové části zahrnují příklady, kdy je datová část standardní a kdy je vlastní.

### <a name="standard-webhook-for-log-alerts"></a>Standardní webhook pro upozornění protokolu 
Oba tyto příklady mají fiktivní datovou část s pouze dva sloupce a dva řádky.

#### <a name="log-alert-for-log-analytics"></a>Upozornění protokolu pro analýzu protokolů
Následující ukázková datová část je pro standardní akci *webhooku bez vlastní možnosti JSON,* která se používá pro výstrahy založené na log analytics:

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
> Hodnota pole Závažnost se může změnit, pokud jste [přepnuli předvolbu rozhraní API](alerts-log-api-switch.md) pro upozornění protokolu v Log Analytics.


#### <a name="log-alert-for-application-insights"></a>Výstraha protokolu pro přehledy aplikací
Následující ukázková datová část je pro standardní webhook *bez vlastní možnosti JSON,* pokud se používá pro výstrahy protokolu založené na Application Insights:
    
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

#### <a name="log-alert-with-custom-json-payload"></a>Výstraha protokolu s vlastní datovou částí JSON
Chcete-li například vytvořit vlastní datovou část, která obsahuje pouze název výstrahy a výsledky hledání, můžete použít následující: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

Následující ukázková datová část je pro vlastní akci webhooku pro všechny výstrahy protokolu:
    
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
- Další informace o [výstrahách protokolů v azure výstrahy](alerts-unified-log.md).
- Zjistěte, jak [spravovat výstrahy protokolu v Azure](alerts-log.md).
- Vytvářejte a spravujte [skupiny akcí v Azure](action-groups.md).
- Další informace o [application insights](../../azure-monitor/app/analytics.md).
- Další informace o [dotazech protokolu](../log-query/log-query-overview.md). 

