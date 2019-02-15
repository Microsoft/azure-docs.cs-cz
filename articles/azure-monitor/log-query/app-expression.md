---
title: App() použijte výraz v dotazech protokolu Azure Monitor | Dokumentace Microsoftu
description: Aplikace výraz je použit v dotazu protokolu Azure Monitor k načtení dat z konkrétní aplikaci Application Insights ve stejné skupině prostředků, jiné skupiny prostředků nebo jiného předplatného.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/25/2019
ms.author: bwren
ms.openlocfilehash: 0abe2c869a05d359f692db105eb647f9e6a69df8
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56266420"
---
# <a name="app-expression-in-azure-monitor-query"></a>výraz App() použijte v dotazu Azure Monitor

`app` Výrazu se používá v dotazu Azure Monitor k načtení dat z konkrétní aplikaci Application Insights ve stejné skupině prostředků, jiné skupiny prostředků nebo jiného předplatného. To je užitečné zahrnout data aplikací v dotazu protokolu Azure Monitor a provádět dotazy na data napříč více aplikacemi v dotazu Application Insights.



## <a name="syntax"></a>Syntaxe

`app(`*identifikátor*`)`


## <a name="arguments"></a>Argumenty

- *identifikátor*: Identifikuje aplikaci pomocí jednoho z formátů v následující tabulce.

| Identifikátor | Popis | Příklad:
|:---|:---|:---|
| Název prostředku | Lidské čitelný název aplikace (NEBOLI "název komponenty") | app("fabrikamapp") |
| Kvalifikovaný název | Celý název aplikace ve formátu: "subscriptionName/skupina prostředků/název komponenty" | App('AI-prototype/Fabrikam/fabrikamapp') |
| ID | Identifikátor GUID aplikace | app("988ba129-363e-4415-8fe7-8cbab5447518") |
| ID prostředku Azure | Identifikátor prostředku Azure |app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp") |


## <a name="notes"></a>Poznámky

* Musíte mít přístup pro čtení k aplikaci.
* Identifikace podle názvu aplikace předpokládá, že je jedinečný ve všech předplatných dostupné. Pokud máte více aplikací se zadaným názvem se dotaz nezdaří z důvodu nejednoznačnosti. V tomto případě musíte použít jeden z další identifikátory.
* Použijte související výraz [pracovní prostor](workspace-expression.md) pro dotazování napříč pracovních prostorů Log Analytics.
* App() použijte výraz se momentálně nepodporuje ve vyhledávací dotaz při použití na webu Azure portal k vytvoření [pravidlo upozornění vlastního protokolu hledání](../platform/alerts-log.md), pokud aplikace Application Insights se používá jako prostředek pro pravidlo upozornění.

## <a name="examples"></a>Příklady

```Kusto
app("fabrikamapp").requests | count
```
```Kusto
app("AI-Prototype/Fabrikam/fabrikamapp").requests | count
```
```Kusto
app("b438b4f6-912a-46d5-9cb1-b44069212ab4").requests | count
```
```Kusto
app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
```
```Kusto
union 
(workspace("myworkspace").Heartbeat | where Computer contains "Con"),
(app("myapplication").requests | where cloud_RoleInstance contains "Con")
| count  
```
```Kusto
union 
(workspace("myworkspace").Heartbeat), (app("myapplication").requests)
| where TimeGenerated between(todatetime("2018-02-08 15:00:00") .. todatetime("2018-12-08 15:05:00"))
```

## <a name="next-steps"></a>Další postup

- Najdete v článku [pracovní prostor výraz](workspace-expression.md) k odkazování na pracovní prostor Log Analytics.
- Přečtěte si, jak [dat monitorování Azure](../../azure-monitor/log-query/log-query-overview.md) uložená.
- Přístup ke kompletní dokumentaci k [Kusto dotazovací jazyk](/azure/kusto/query/).