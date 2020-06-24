---
title: Kontrola protokolů WAF pomocí Azure Log Analytics
titleSuffix: Azure Application Gateway
description: V tomto článku se dozvíte, jak můžete použít Azure Log Analytics k prohlédnutí Application Gateway protokolů firewallu webových aplikací.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 072640f5ba34b2782f75bbe4169567ade823f406
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/16/2020
ms.locfileid: "84806693"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-logs"></a>Využití Log Analytics ke kontrole protokolů Firewallu webových aplikací služby Application Gateway

Jakmile Application Gateway WAF, můžete povolit protokoly a zkontrolovat, co se děje s jednotlivými požadavky. Protokoly brány firewall poskytují přehled o tom, co WAF vyhodnocuje, odpovídá a blokuje. Pomocí Log Analytics můžete zkoumat data v protokolech brány firewall a získat ještě další přehledy. Další informace o vytváření pracovního prostoru Log Analytics najdete v tématu [Vytvoření pracovního prostoru Log Analytics v Azure Portal](../azure-monitor/learn/quick-create-workspace.md). Další informace o dotazech protokolu najdete [v tématu Přehled dotazů protokolu v Azure monitor](../azure-monitor/log-query/log-query-overview.md).

## <a name="import-waf-logs"></a>Importovat protokoly WAF

Pokud chcete importovat protokoly brány firewall do Log Analytics, přečtěte si téma [stav back-endu, diagnostické protokoly a metriky pro Application Gateway](application-gateway-diagnostics.md#diagnostic-logging). Když máte v pracovním prostoru Log Analytics protokoly brány firewall, můžete zobrazit data, zapisovat dotazy, vytvářet vizualizace a přidávat je na řídicí panel portálu.

## <a name="explore-data-with-examples"></a>Příklady zkoumání dat

Chcete-li zobrazit nezpracovaná data v protokolu brány firewall, můžete spustit následující dotaz:

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

Bude vypadat podobně jako následující dotaz:

![Log Analytics dotaz](media/log-analytics/log-query.png)

Můžete procházet k podrobnostem dat a kreslit grafy nebo vytvářet vizualizace z tohoto místa. Jako výchozí bod se zobrazí následující dotazy:

### <a name="matchedblocked-requests-by-ip"></a>Spárované/Blokované požadavky podle IP adresy

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>Spárované/Blokované požadavky podle identifikátoru URI

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>Nejvyšší odpovídající pravidla

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>Prvních pět odpovídajících skupin pravidel

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>Přidat na řídicí panel

Když vytvoříte dotaz, můžete ho přidat na řídicí panel.  V pravém horním rohu pracovního prostoru Log Analytics vyberte **Připnout na řídicí panel** . Pomocí předchozích čtyř dotazů připnuté na vzorový řídicí panel se jedná o data, která vidíte na první pohled:

![Řídicí panel](media/log-analytics/dashboard.png)

## <a name="next-steps"></a>Další kroky

[Stav back-endu, diagnostické protokoly a metriky pro Application Gateway](application-gateway-diagnostics.md)
