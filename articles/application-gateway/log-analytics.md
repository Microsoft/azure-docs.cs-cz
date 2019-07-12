---
title: Zkontrolujte protokoly firewallu webových aplikací pomocí Application Gateway pomocí Azure Log Analytics
description: Tento článek popisuje, jak vám pomůže Azure Log Analytics v protokolech Firewall webových aplikací pomocí Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/10/2019
ms.author: victorh
ms.openlocfilehash: aa867e33ef0faa96b6a66a9075a3a5b8b0b0bca4
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712173"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-logs"></a>Zkontrolujte protokoly firewallu webových aplikací v Application Gateway pomocí Log Analytics

Jakmile vaše waf služby Application Gateway je funkční, můžete povolit protokoly a zkontrolujte, co se děje s každou žádostí. Přehled poskytují protokoly brány firewall tak, aby co WAF je vaše rozhodnutí vyzkoušet, porovnávání a blokování. Pomocí služby Log Analytics můžete zkoumat data uvnitř protokoly brány firewall a poskytovat ještě více informací. Další informace o vytváření pracovního prostoru Log Analytics najdete v tématu [vytvořit pracovní prostor Log Analytics na portálu Azure portal](../azure-monitor/learn/quick-create-workspace.md). Další informace o dotazech protokolu najdete v tématu [přehled protokolu dotazů ve službě Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

## <a name="import-waf-logs"></a>Import protokolů WAF

Importovat vaše brána firewall protokoly do Log Analytics, najdete v článku [stav Back endu, diagnostické protokoly a metriky pro službu Application Gateway](application-gateway-diagnostics.md#diagnostic-logging). Pokud máte brány firewall protokoly ve vašem pracovním prostoru Log Analytics, můžete zobrazit data, psát dotazy, vytvářet vizualizace a přidat na řídicí panel portálu.

## <a name="explore-data-with-examples"></a>Zkoumání dat s příklady

Chcete-li zobrazit nezpracovaná data v protokolu brány firewall, můžete spustit následující dotaz:

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

To bude vypadat podobně jako následující dotaz:

![Dotaz log Analytics](media/log-analytics/log-query.png)

Můžete podrobná data a zobrazit grafy nebo vytvořit vizualizace odsud. Jako výchozí bod najdete v následujících dotazech:

### <a name="matchedblocked-requests-by-ip"></a>Odpovídající nebo zablokovaný požadavky podle IP

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>Odpovídající nebo zablokovaný požadavky pomocí identifikátoru URI

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>Začátek odpovídající pravidla

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>Hlavních pět skupin odpovídající pravidlo

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>Přidat na řídicí panel

Jakmile vytvoříte dotaz, přidáte jej do řídicího panelu.  Vyberte **připnout na řídicí panel** nahoře napravo od pracovního prostoru log analytics. S předchozí čtyři dotazy připnuté na řídicí panel příklad jedná se o data, které vidíte na první pohled:

![Řídicí panel](media/log-analytics/dashboard.png)

## <a name="next-steps"></a>Další postup

[Stav back endu, diagnostické protokoly a metriky pro službu Application Gateway](application-gateway-diagnostics.md)
