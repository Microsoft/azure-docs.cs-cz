---
title: Monitorování a protokolování brány firewall webových aplikací Azure
description: Zjistěte, jak Firewall webových aplikací (WAF) s FrontDoor monitoring a protokolování
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: b4f666415a96307b89022c6caf6af90581f294f3
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115359"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Monitorování a protokolování brány firewall webových aplikací Azure 

Monitorování a protokolování firewallu webových aplikací Azure (WAF) se poskytuje prostřednictvím protokolování a integrace s protokoly Azure Monitor a Azure Monitor.

## <a name="azure-monitor"></a>Azure Monitor

WAF s protokolem FrontDoor je integrovaná do [Azure monitor](../../azure-monitor/overview.md). Azure Monitor umožňuje sledovat diagnostické informace, včetně výstrah a protokolů WAF. Monitorování WAF v rámci prostředku front-dveří můžete nakonfigurovat na portálu na kartě **Diagnostika** nebo přímo prostřednictvím služby Azure monitor.

Z Azure Portal přejít na typ prostředku front-dveří. Na kartě **monitorování**/**metriky** na levé straně můžete přidat **WebApplicationFirewallRequestCount** ke sledování počtu požadavků odpovídajících pravidlům WAF. Vlastní filtry je možné vytvořit na základě typů akcí a názvů pravidel.

![WAFMetrics](../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Protokoly a diagnostika

WAF s předními dveřmi poskytuje podrobné hlášení o každé hrozbě, kterou detekuje. Protokolování je integrované v protokolech diagnostiky Azure a výstrahy se zaznamenávají ve formátu JSON. Tyto protokoly je možné integrovat s [protokoly Azure monitor](../../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog zaznamená všechny požadavky, které se předají do back-endu zákazníků. FrontdoorWebApplicationFirewallLog zaznamená všechny požadavky, které odpovídají pravidlu WAF.

Následující příklad dotazu získá v blokovaných požadavcích protokoly WAF:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

Tady je příklad protokolovaných požadavků v protokolu WAF:

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

Následující příklad dotazu získá položky AccessLogs:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

Tady je příklad protokolovaných požadavků v protokolu přístupu:

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

- Přečtěte si další informace o [front-dveřích](../../frontdoor/front-door-overview.md).
