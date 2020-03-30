---
title: Zkontrolujte protokoly WAF pomocí Azure Log Analytics
titleSuffix: Azure Application Gateway
description: Tento článek ukazuje, jak můžete pomocí Azure Log Analytics prozkoumat protokoly brány firewall webové aplikace brány aplikace
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 9fe4462a71852e5f66268f798f6f0418f2dd39c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048097"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-logs"></a>Využití Log Analytics ke kontrole protokolů Firewallu webových aplikací služby Application Gateway

Jakmile je váš WAF aplikační brány funkční, můžete povolit protokoly ke kontrole, co se děje s každým požadavkem. Protokoly brány firewall poskytují přehled o tom, co WAF vyhodnocuje, porovnává a blokuje. Pomocí analýzy protokolů můžete prozkoumat data v protokolech brány firewall a poskytnout tak ještě více přehledů. Další informace o vytvoření pracovního prostoru Analýzy protokolů najdete [v tématu Vytvoření pracovního prostoru Analýzy protokolů na webu Azure Portal](../azure-monitor/learn/quick-create-workspace.md). Další informace o dotazech protokolu najdete [v tématu Přehled dotazů protokolu v Azure Monitoru](../azure-monitor/log-query/log-query-overview.md).

## <a name="import-waf-logs"></a>Import protokolů WAF

Chcete-li importovat protokoly brány firewall do analýzy protokolů, přečtěte si článek [Stav back-endu, diagnostické protokoly a metriky pro aplikační bránu](application-gateway-diagnostics.md#diagnostic-logging). Když máte protokoly brány firewall v pracovním prostoru Log Analytics, můžete zobrazit data, psát dotazy, vytvářet vizualizace a přidávat je na řídicí panel portálu.

## <a name="explore-data-with-examples"></a>Prozkoumejte data s příklady

Chcete-li zobrazit nezpracovaná data v protokolu brány firewall, můžete spustit následující dotaz:

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

To bude vypadat podobně jako následující dotaz:

![Dotaz Log Analytics](media/log-analytics/log-query.png)

Můžete přejít k podrobnostem o datech a vykreslit grafy nebo vytvořit vizualizace odtud. Jako výchozí bod naleznete následující dotazy:

### <a name="matchedblocked-requests-by-ip"></a>Odpovídající/blokované požadavky podle IP adresy

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>Odpovídající/blokované požadavky podle identifikátoru URI

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>Nejlépe sladěná pravidla

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>Pět nejlepších odpovídajících skupin pravidel

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>Přidání na řídicí panel

Jakmile vytvoříte dotaz, můžete ho přidat na řídicí panel.  Vyberte **připnout na řídicí panel** v pravém horním rohu pracovního prostoru analýzy protokolů. S předchozími čtyřmi dotazy připnutými na ukázkový řídicí panel se jedná o data, která můžete vidět na první pohled:

![Řídicí panel](media/log-analytics/dashboard.png)

## <a name="next-steps"></a>Další kroky

[Stav back-endu, diagnostické protokoly a metriky pro aplikační bránu](application-gateway-diagnostics.md)
