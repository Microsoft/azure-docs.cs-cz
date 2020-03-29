---
title: výraz workspace() v dotazu protokolu Azure Monitor | Dokumenty společnosti Microsoft
description: Výraz pracovního prostoru se používá v dotazu protokolu Azure Monitor k načtení dat z určitého pracovního prostoru ve stejné skupině prostředků, jiné skupině prostředků nebo jiném předplatném.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 255888acf5da6149b6a964b23ed038b99715481c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75364947"
---
# <a name="workspace-expression-in-azure-monitor-log-query"></a>výraz workspace() v dotazu protokolu Azure Monitor

Výraz `workspace` se používá v dotazu Azure Monitor k načtení dat z určitého pracovního prostoru ve stejné skupině prostředků, jiné skupině prostředků nebo jiném předplatném. To je užitečné zahrnout data protokolu do dotazu Application Insights a dotazovat data navíce v různých pracovních prostorech v protokolu dotazu.


## <a name="syntax"></a>Syntaxe

`workspace(`*Identifikátor*`)`

## <a name="arguments"></a>Argumenty

- *Identifikátor*: Identifikuje pracovní prostor pomocí jednoho z formátů v následující tabulce.

| Identifikátor | Popis | Příklad
|:---|:---|:---|
| Název prostředku | Čitelný název pracovního prostoru (AKA "název komponenty") | ("contosoretail") |
| Kvalifikovaný název | Úplný název pracovního prostoru ve formuláři: "subscriptionName/resourceGroup/componentName" | ('Contoso/ContosoResource/ContosoWorkspace') |
| ID | Identifikátor GUID pracovního prostoru | ("b438b3f6-912a-46d5-9db1-b42069242ab4") |
| ID prostředků Azure | Identifikátor prostředku Azure | workspace("/subscriptions/e4227-645-44e-9c67-3b84b5982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail") |


## <a name="notes"></a>Poznámky

* Musíte mít přístup pro čtení do pracovního prostoru.
* Související výraz `app` je, který umožňuje dotazovat napříč aplikacemi Application Insights.

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

## <a name="next-steps"></a>Další kroky

- Viz [výraz aplikace,](app-expression.md) který odkazuje na aplikaci Application Insights.
- Přečtěte si o tom, jak se data [Azure Monitoru](log-query-overview.md) ukládají.
- Získejte přístup k úplné dokumentaci [k dotazovacímu jazyku Kusto](/azure/kusto/query/).
