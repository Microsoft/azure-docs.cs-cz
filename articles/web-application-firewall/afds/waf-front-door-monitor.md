---
title: Monitorování a protokolování brány Azure Web Application Firewall
description: Naučte se firewall webových aplikací (WAF) s monitorováním a protokolováním frontdoorů
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 4488fadf5db3b32049b5dce4bbee1fa76c320e96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284139"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Monitorování a protokolování brány Azure Web Application Firewall 

Azure Web Application Firewall (WAF) monitorování a protokolování jsou k dispozici prostřednictvím protokolování a integrace s protokoly Azure Monitor a Azure Monitor.

## <a name="azure-monitor"></a>Azure Monitor

WAF s protokolem FrontDoor je integrovaný s [Azure Monitor](../../azure-monitor/overview.md). Azure Monitor umožňuje sledovat diagnostické informace, včetně waf výstrahy a protokoly. Monitorování WAF můžete nakonfigurovat v rámci prostředku front door na portálu na kartě Diagnostika nebo přímo prostřednictvím **služby** Azure Monitor.

Z portálu Azure přejděte na typ prostředku Front Door. Z karty **Monitorování**/**metrik** na levé straně můžete přidat **WebApplicationFirewallRequestCount** ke sledování počtu požadavků, které odpovídají pravidlům WAF. Vlastní filtry lze vytvořit na základě typů akcí a názvů pravidel.

![WAFMetrics](../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Protokoly a diagnostika

WAF s předními dveřmi poskytuje podrobné zprávy o každé hrozbě, které detekuje. Protokolování je integrované v protokolech diagnostiky Azure a výstrahy se zaznamenávají ve formátu JSON. Tyto protokoly lze integrovat s [protokoly Azure Monitor](../../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog protokoluje všechny požadavky, které jsou předány back-endů zákazníka. FrontdoorWebApplicationFirewallLog protokoluje všechny požadavky, které odpovídají pravidlu WAF.

Následující ukázkový dotaz získá protokoly WAF o blokovaných požadavcích:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_name_s == "Block"

```

Zde je příklad protokolovaného požadavku v protokolu WAF:

``` WAFlogQuerySample
{
    "PreciseTimeStamp": "2020-01-25T00:11:19.3866091Z",
    "time": "2020-01-25T00:11:19.3866091Z",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "xx.xx.xxx.xxx",
        "socketIP": "xx.xx.xxx.xxx",
        "requestUri": "https://wafdemofrontdoorwebapp.azurefd.net:443/?q=../../x",
        "ruleName": "Microsoft_DefaultRuleSet-1.1-LFI-930100",
        "policy": "WafDemoCustomPolicy",
        "action": "Block",
        "host": "wafdemofrontdoorwebapp.azurefd.net",
        "refString": "0p4crXgAAAABgMq5aIpu0T6AUfCYOroltV1NURURHRTA2MTMANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "policyMode": "prevention"
    }
}

``` 

Následující ukázkový dotaz získá položky AccessLogs:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

Zde je příklad protokolovaného požadavku v protokolu aplikace Access:

``` AccessLogSample
{
    "PreciseTimeStamp": "2020-01-25T00:11:12.0160150Z",
    "time": "2020-01-25T00:11:12.0160150Z",
    "category": "FrontdoorAccessLog",
    "operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
    "properties": {
        "trackingReference": "0n4crXgAAAACnRKbdALbyToAqNfSHssDvV1NURURHRTA2MTMANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "httpMethod": "GET",
        "httpVersion": "2.0",
        "requestUri": "https://wafdemofrontdoorwebapp.azurefd.net:443/",
        "requestBytes": "710",
        "responseBytes": "3116",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4017.0 Safari/537.36 Edg/81.0.389.2",
        "clientIp": "xx.xx.xxx.xxx",
        "timeTaken": "0.598",
        "securityProtocol": "TLS 1.2",
        "routingRuleName": "WAFdemoWebAppRouting",
        "backendHostname": "wafdemouksouth.azurewebsites.net:443",
        "sentToOriginShield": false,
        "httpStatusCode": "200",
        "httpStatusDetails": "200"
    }
}

```

## <a name="next-steps"></a>Další kroky

- Další informace o [předních dveřích](../../frontdoor/front-door-overview.md).
