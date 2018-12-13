---
title: výraz App() použijte v dotazu Azure Log Analytics | Dokumentace Microsoftu
description: Aplikace výraz je použit v dotazu Log Analytics k načtení dat z konkrétní aplikaci Application Insights ve stejné skupině prostředků, jiné skupiny prostředků nebo jiného předplatného.
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
ms.date: 09/10/2018
ms.author: bwren
ms.openlocfilehash: e83ba321a98e40f07ff82e68c7961c2a6a49076d
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53191831"
---
# <a name="app-expression-in-log-analytics-query"></a>výraz App() použijte v dotazu Log Analytics

`app` Výrazu v dotazu Log Analytics slouží k načtení dat z konkrétní aplikace Application Insights ve stejné skupině prostředků, jiné skupiny prostředků nebo jiného předplatného. To je užitečné zahrnout data aplikací v dotazu Log Analytics a provádět dotazy na data napříč více aplikacemi v dotazu Application Insights.



## <a name="syntax"></a>Syntaxe

`app(`*identifikátor*`)`


## <a name="arguments"></a>Argumenty

- *identifikátor*: Identifikuje aplikaci pomocí jednoho z formátů v následující tabulce.

| Identifikátor | Popis | Příklad:
|:---|:---|:---|
| Název prostředku | Lidské čitelný název aplikace (NEBOLI "název komponenty") | App("fabrikamapp") |
| Kvalifikovaný název | Celý název aplikace ve formátu: "subscriptionName/skupina prostředků/název komponenty" | App('AI-prototype/Fabrikam/fabrikamapp') |
| ID | Identifikátor GUID aplikace | App("988ba129-363e-4415-8fe7-8cbab5447518") |
| ID prostředku Azure | Identifikátor prostředku Azure |App("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/Microsoft.insights/Components/fabrikamapp") |


## <a name="notes"></a>Poznámky

* Musíte mít přístup pro čtení k aplikaci.
* Identifikace podle názvu aplikace předpokládá, že je jedinečný ve všech předplatných dostupné. Pokud máte více aplikací se zadaným názvem se dotaz nezdaří z důvodu nejednoznačnosti. V tomto případě musíte použít jeden z další identifikátory.
* Použijte související výraz [pracovní prostor](workspace-expression.md) pro dotazování napříč pracovních prostorů Log Analytics.

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
- Přečtěte si, jak [dat Log Analytics](../../azure-monitor/log-query/log-query-overview.md) uložená.