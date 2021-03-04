---
title: výraz pracovního prostoru () v Azure Monitor dotaz na protokol | Microsoft Docs
description: Výraz pracovního prostoru se používá v dotazu protokolu Azure Monitor k načtení dat z konkrétního pracovního prostoru ve stejné skupině prostředků, jiné skupině prostředků nebo jiném předplatném.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 34b1f850b262a0027d3a9cb2e926bcb2c8a49665
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710902"
---
# <a name="workspace-expression-in-azure-monitor-log-query"></a>výraz pracovního prostoru () v Azure Monitor dotaz protokolu

`workspace`Výraz se používá v Azure monitor dotazech k načtení dat z konkrétního pracovního prostoru ve stejné skupině prostředků, jiné skupině prostředků nebo jiném předplatném. To je užitečné, pokud chcete zahrnout data protokolu do Application Insightsho dotazu a dotazovat data napříč několika pracovními prostory v dotazu protokolu.


## <a name="syntax"></a>Syntax

`workspace(`*RID*`)`

## <a name="arguments"></a>Argumenty

- *Identifikátor*: identifikuje pracovní prostor pomocí jednoho z formátů v následující tabulce.

| Identifikátor | Popis | Příklad
|:---|:---|:---|
| Název prostředku | Lidské čitelné jméno pracovního prostoru (neboli "název komponenty") | pracovní prostor (ContosoRetail) |
| Kvalifikovaný název | Úplný název pracovního prostoru ve formátu: "Subscription/resourceName/resourceName/název součásti" | pracovní prostor (' contoso/ContosoResource/ContosoWorkspace ') |
| ID | Identifikátor GUID pracovního prostoru | pracovní prostor (b438b3f6-912a-46d5-9db1-b42069242ab4) |
| ID prostředku Azure | Identifikátor prostředku Azure | pracovní prostor (/subscriptions/e4227-645-44e-9c67-3b84b5982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail) |


## <a name="notes"></a>Poznámky

* Musíte mít přístup pro čtení k pracovnímu prostoru.
* Související výraz je `app` , který umožňuje dotazování napříč Application Insights aplikacemi.

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

- Podívejte se na [výraz aplikace](./app-expression.md) , který odkazuje na aplikaci Application Insights.
- Přečtěte si informace o tom, jak jsou uložená [data Azure monitor](./log-query-overview.md) .
- Přístup k celé dokumentaci pro [dotazovací jazyk Kusto](/azure/kusto/query/).