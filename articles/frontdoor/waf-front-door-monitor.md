---
title: Monitorování a protokolování brány firewall webových aplikací Azure
description: Zjistěte, jak Firewall webových aplikací (WAF) s FrontDoor monitoring a protokolování
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 675d06f3d2071022da3867a4c45137efb818980d
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849147"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Monitorování a protokolování brány firewall webových aplikací Azure 

Monitorování a protokolování firewallu webových aplikací Azure (WAF) se poskytuje prostřednictvím protokolování a integrace s protokoly Azure Monitor a Azure Monitor.

## <a name="azure-monitor"></a>Azure Monitor

WAF s protokolem FrontDoor je integrovaná do [Azure monitor](../azure-monitor/overview.md). Azure Monitor umožňuje sledovat diagnostické informace, včetně výstrah a protokolů WAF. Monitorování WAF v rámci prostředku front-dveří můžete nakonfigurovat na portálu na kartě **Diagnostika** nebo přímo prostřednictvím služby Azure monitor.

Z Azure Portal přejít na typ prostředku front-dveří. Na kartě **monitorování**/**metriky** na levé straně můžete přidat **WebApplicationFirewallRequestCount** ke sledování počtu požadavků odpovídajících pravidlům WAF. Vlastní filtry je možné vytvořit na základě typů akcí a názvů pravidel.

![WAFMetrics](./media//waf-front-door-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Protokoly a diagnostika

WAF s předními dveřmi poskytuje podrobné hlášení o každé hrozbě, kterou detekuje. Protokolování je integrované v protokolech diagnostiky Azure a výstrahy se zaznamenávají ve formátu JSON. Tyto protokoly je možné integrovat s [protokoly Azure monitor](../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag](./media/waf-front-door-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog zaznamená všechny požadavky, které se předají do back-endu zákazníků. FrontdoorWebApplicationFirewallLog zaznamená všechny požadavky, které odpovídají pravidlu WAF.

Následující příklad dotazu získá v blokovaných požadavcích protokoly WAF:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

Následující příklad dotazu získá položky AccessLogs:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"


```

## <a name="next-steps"></a>Další postup

- Přečtěte si další informace o [front](front-door-overview.md)-dveřích.

