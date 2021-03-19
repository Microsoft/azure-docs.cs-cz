---
title: Monitorování a protokolování brány firewall webových aplikací Azure
description: Zjistěte, jak Firewall webových aplikací (WAF) s FrontDoor monitoring a protokolování
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: 596374d4f3f188e08a10bd25b36b178cc79a6e57
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "84808956"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Monitorování a protokolování brány firewall webových aplikací Azure

Monitorování a protokolování firewallu webových aplikací Azure (WAF) se poskytuje prostřednictvím protokolování a integrace s protokoly Azure Monitor a Azure Monitor.

## <a name="azure-monitor"></a>Azure Monitor

WAF s protokolem FrontDoor je integrovaná do [Azure monitor](../../azure-monitor/overview.md). Azure Monitor umožňuje sledovat diagnostické informace, včetně výstrah a protokolů WAF. Monitorování WAF v rámci prostředku front-dveří můžete nakonfigurovat na portálu na kartě **Diagnostika** nebo přímo prostřednictvím služby Azure monitor.

Z Azure Portal přejít na typ prostředku front-dveří. Na kartě **monitorování** / **metriky** na levé straně můžete přidat **WebApplicationFirewallRequestCount** ke sledování počtu požadavků odpovídajících pravidlům WAF. Vlastní filtry je možné vytvořit na základě typů akcí a názvů pravidel.

:::image type="content" source="../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png" alt-text="WAFMetrics ":::

## <a name="logs-and-diagnostics"></a>Protokoly a diagnostika

WAF s předními dveřmi poskytuje podrobné hlášení o každé hrozbě, kterou detekuje. Protokolování je integrované v protokolech diagnostiky Azure a výstrahy se zaznamenávají ve formátu JSON. Tyto protokoly je možné integrovat s [protokoly Azure monitor](../../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

[FrontdoorAccessLog](../../frontdoor/front-door-diagnostics.md) zaznamenává všechny požadavky. FrontdoorWebApplicationFirewallLog zaznamená všechny požadavky, které odpovídají pravidlu WAF, má následující schéma:

| Vlastnost  | Popis |
| ------------- | ------------- |
|Akce|Akce prováděná na žádosti|
| ClientIp | IP adresa klienta, který odeslal požadavek. Pokud v žádosti existovala hlavička X předaná-pro, pak je IP adresa klienta převzata z pole hlavička. |
| ClientPort | Port IP klienta, který odeslal požadavek. |
| Podrobnosti|Další podrobnosti o odpovídajícím požadavku |
|| matchVariableName: název parametru http odpovídající žádosti, například názvy hlaviček|
|| matchVariableValue: hodnoty, které spouštějí shodu|
| Hostitel | Hlavička hostitele porovnané žádosti |
| Zásady | Název zásad WAF, které požadavek odpovídal. |
| PolicyMode | Režim operací zásad WAF Možné hodnoty jsou "prevence" a "detekce". |
| RequestUri | Úplný identifikátor URI odpovídající žádosti. |
| RuleName | Název pravidla WAF, které požadavek odpovídal. |
| SocketIp | Zdrojová IP adresa, kterou uvidí WAF Tato IP adresa je založena na relaci TCP, nezávisle na všech hlavičkách požadavků.|
| TrackingReference | Jedinečný referenční řetězec, který identifikuje požadavek, který je obsluhován předními dvířky, je také odeslán jako hlavička X-Azure-ref na klienta. Vyžaduje se pro hledání podrobností v protokolech přístupu pro konkrétní požadavek. |

Následující příklad dotazu vrátí protokoly WAF u blokovaných požadavků:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

Tady je příklad protokolovaných požadavků v protokolu WAF:

``` WAFlogQuerySample
{
    "time":  "2020-06-09T22:32:17.8376810Z",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoorWebApplicationFirewallLog/Write",
    "properties":
    {
        "clientIP":"xxx.xxx.xxx.xxx",
        "clientPort":"52097",
        "socketIP":"xxx.xxx.xxx.xxx",
        "requestUri":"https://wafdemofrontdoorwebapp.azurefd.net:443/?q=%27%20or%201=1",
        "ruleName":"Microsoft_DefaultRuleSet-1.1-SQLI-942100",
        "policy":"WafDemoCustomPolicy",
        "action":"Block",
        "host":"wafdemofrontdoorwebapp.azurefd.net",
        "trackingReference":"08Q3gXgAAAAAe0s71BET/QYwmqtpHO7uAU0pDRURHRTA1MDgANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "policyMode":"prevention",
        "details":
            {
            "matches":
                [{
                "matchVariableName":"QueryParamValue:q",
                "matchVariableValue":"' or 1=1"
                }]
            }
     }
}
```

Následující příklad dotazu vrátí položky AccessLogs:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

Tady je příklad protokolovaných požadavků v protokolu přístupu:

``` AccessLogSample
{
"time": "2020-06-09T22:32:17.8383427Z",
"category": "FrontdoorAccessLog",
"operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
 "properties":
    {
    "trackingReference":"08Q3gXgAAAAAe0s71BET/QYwmqtpHO7uAU0pDRURHRTA1MDgANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
    "httpMethod":"GET",
    "httpVersion":"2.0",
    "requestUri":"https://wafdemofrontdoorwebapp.azurefd.net:443/?q=%27%20or%201=1",
    "requestBytes":"715",
    "responseBytes":"380",
    "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4157.0 Safari/537.36 Edg/85.0.531.1",
    "clientIp":"xxx.xxx.xxx.xxx",
    "socketIp":"xxx.xxx.xxx.xxx",
    "clientPort":"52097",
    "timeTaken":"0.003",
    "securityProtocol":"TLS 1.2",
    "routingRuleName":"WAFdemoWebAppRouting",
    "rulesEngineMatchNames":[],
    "backendHostname":"wafdemowebappuscentral.azurewebsites.net:443",
    "sentToOriginShield":false,
    "httpStatusCode":"403",
    "httpStatusDetails":"403",
    "pop":"SJC",
    "cacheStatus":"CONFIG_NOCACHE"
    }
}

```

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [front-dveřích](../../frontdoor/front-door-overview.md).
