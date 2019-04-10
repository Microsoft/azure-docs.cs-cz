---
title: Firewall webových aplikací monitorování a protokolování
description: Přečtěte si firewall webových aplikací (WAF) s FrontDoor monitorování a protokolování
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: tyao;kumud
ms.openlocfilehash: 5368ed8d1e60a646366065e2cf617fb2f3735b53
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2019
ms.locfileid: "59363035"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Firewall webových aplikací monitorování a protokolování 

Protokolování a monitorování Azure webových aplikací (WAF) brána firewall, je zajišťována protokolováním a integrací se službami Azure Monitor a Azure Monitor protokoly.

> [!IMPORTANT]
> WAF, monitorování a protokolování pro Azure branou funkce je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="azure-monitor"></a>Azure Monitor

WAF FrontDoor protokolu je integrovaná s [Azure Monitor](../azure-monitor/overview.md). Azure Monitor umožňuje sledovat diagnostické informace, včetně výstrah a protokolů WAF. Můžete nakonfigurovat v rámci branou prostředků na portálu v části monitorování WAF **diagnostiky** kartu nebo přímo prostřednictvím služby Azure Monitor service.

Z webu Azure portal přejděte na typ prostředku branou. Z **monitorování**/**metriky** kartu na levé straně, můžete přidat **WebApplicationFirewallRequestCount** sledovat počet požadavků, které odpovídají pravidla firewallu webových aplikací. Vlastní filtry lze vytvořit na základě typy akcí a názvy pravidel.

![WAFMetrics](./media//waf-front-door-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Protokolování a Diagnostika

WAF s branou poskytuje podrobné generování sestav o každém útoky, které zjistí. Protokolování je integrované v protokolech diagnostiky Azure a výstrahy se zaznamenávají ve formátu JSON. Tyto protokoly je možné integrovat se službou [protokoly Azure monitoru](../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag](./media/waf-front-door-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog protokoluje všechny požadavky, které se předávají back EndY zákazníka. FrontdoorWebApplicationFirewallLog protokoluje všechny požadavky, které odpovídá pravidlu WAF.

Následující příklad dotazu získá protokolů WAF na blokované požadavky:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

Následující příklad dotazu získá AccessLogs položky:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"


```

## <a name="next-steps"></a>Další postup

- Další informace o [branou](front-door-overview.md).

