---
title: výraz Workspace() v dotazu protokolu Azure Monitor | Dokumentace Microsoftu
description: Pracovní prostor výraz je použit v dotazu protokolu Azure Monitor k načtení dat z konkrétní pracovní prostor ve stejné skupině prostředků, jiné skupiny prostředků nebo jiného předplatného.
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
ms.openlocfilehash: 933d37f576d0b8507d2311a3e31e34182a0a2e69
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56269831"
---
# <a name="workspace-expression-in-azure-monitor-log-query"></a>výraz Workspace() v dotazu protokolu Azure Monitor

`workspace` Výrazu se používá v dotazu Azure Monitor k načtení dat z konkrétní pracovní prostor ve stejné skupině prostředků, jiné skupiny prostředků nebo jiného předplatného. To je užitečné zahrnout data protokolu v dotazu Application Insights a provádět dotazy na data napříč několika pracovními prostory v dotazu protokolu.


## <a name="syntax"></a>Syntaxe

`workspace(`*identifikátor*`)`

## <a name="arguments"></a>Argumenty

- *identifikátor*: Určuje pracovní prostor pomocí jednoho z formátů v následující tabulce.

| Identifikátor | Popis | Příklad:
|:---|:---|:---|
| Název prostředku | Lidské čitelný název pracovního prostoru (NEBOLI "název komponenty") | Workspace("contosoretail") |
| Kvalifikovaný název | Celý název pracovního prostoru ve formě: "subscriptionName/skupina prostředků/název komponenty" | Workspace('Contoso/ContosoResource/ContosoWorkspace') |
| ID | Identifikátor GUID pracovního prostoru | workspace("b438b3f6-912a-46d5-9db1-b42069242ab4") |
| ID prostředku Azure | Identifikátor prostředku Azure | Workspace("/subscriptions/e4227-645-44e-9c67-3b84b5982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail") |


## <a name="notes"></a>Poznámky

* Do pracovního prostoru musí mít oprávnění ke čtení.
* Související výraz je `app` , který umožňuje dotazování napříč aplikací služby Application Insights.

## <a name="examples"></a>Příklady

```Kusto
workspace("contosoretail").Update | count
```
```Kusto
workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count
```
```Kusto
workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
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

- Najdete v článku [aplikace výraz](app-expression.md) odkázat na aplikaci Application Insights.
- Přečtěte si, jak [dat monitorování Azure](log-query-overview.md) uložená.
- Přístup ke kompletní dokumentaci k [Kusto dotazovací jazyk](/azure/kusto/query/).