---
title: Použití Azure Log Analytics k prohlédnutí Application Gateway protokolů firewallu webových aplikací
description: V tomto článku se dozvíte, jak můžete použít Azure Log Analytics k prohlédnutí Application Gateway protokolů firewallu webových aplikací.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/06/2019
ms.author: victorh
ms.openlocfilehash: c928d4234e82e7c43c2365e20d57d0b97d4dbc1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100589006"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-waf-logs"></a>Použití Log Analytics pro kontrolu Application Gateway protokolů firewallu webových aplikací (WAF)

Jakmile Application Gateway WAF, můžete povolit protokoly a zkontrolovat, co se děje s jednotlivými požadavky. Protokoly brány firewall poskytují přehled o tom, co WAF vyhodnocuje, odpovídá a blokuje. Pomocí Azure Monitor Log Analytics můžete zkoumat data v protokolech brány firewall a získat ještě další přehledy. Další informace o vytváření pracovního prostoru Log Analytics najdete v tématu [Vytvoření pracovního prostoru Log Analytics v Azure Portal](../../azure-monitor/logs/quick-create-workspace.md). Další informace o dotazech protokolu najdete [v tématu Přehled dotazů protokolu v Azure monitor](../../azure-monitor/logs/log-query-overview.md).

## <a name="import-waf-logs"></a>Importovat protokoly WAF

Pokud chcete importovat protokoly brány firewall do Log Analytics, přečtěte si téma [stav back-endu, protokoly prostředků a metriky pro Application Gateway](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging). Když máte v pracovním prostoru Log Analytics protokoly brány firewall, můžete zobrazit data, zapisovat dotazy, vytvářet vizualizace a přidávat je na řídicí panel portálu.

## <a name="explore-data-with-examples"></a>Příklady zkoumání dat

Chcete-li zobrazit nezpracovaná data v protokolu brány firewall, můžete spustit následující dotaz:

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

Bude vypadat podobně jako následující dotaz:

![Log Analytics dotaz](../media/log-analytics/log-query.png)

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

![Snímek obrazovky ukazuje řídicí panel Azure, kde můžete přidat dotaz.](../media/log-analytics/dashboard.png)

## <a name="next-steps"></a>Další kroky

[Stav back-endu, protokoly prostředků a metriky pro Application Gateway](../../application-gateway/application-gateway-diagnostics.md)