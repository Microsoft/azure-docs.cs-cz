---
title: výraz prostředku () v Azure Monitor dotaz na protokol | Microsoft Docs
description: Výraz prostředku se používá v dotazu protokolu Azure Monitor orientovaném na prostředky k načtení dat z více zdrojů.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 2a729caefe698b13833098ba48df9d4bfbd97356
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77665695"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>výraz prostředku () v dotazu Azure Monitor protokolu

`resource`Výraz se používá v Azure monitor dotazu [vymezeném na prostředek](scope.md#query-scope) pro načtení dat z jiných prostředků. 


## <a name="syntax"></a>Syntaxe

`resource(`*RID*`)`

## <a name="arguments"></a>Argumenty

- *Identifikátor*: ID prostředku prostředku.

| Identifikátor | Popis | Příklad
|:---|:---|:---|
| Prostředek | Zahrnuje data pro prostředek. | prostředek ("/Subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/Providers/Microsoft.COMPUTE/VirtualMachines/myvm") |
| Skupina prostředků nebo předplatné | Zahrnuje data prostředku a všech prostředků, které obsahuje.  | prostředek ("/Subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup) |


## <a name="notes"></a>Poznámky

* Musíte mít oprávnění ke čtení tohoto prostředku.


## <a name="examples"></a>Příklady

```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm").Heartbeat) | summarize count() by _ResourceId, TenantId
```
```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup).Heartbeat) | summarize count() by _ResourceId, TenantId
```


## <a name="next-steps"></a>Další kroky

- Podrobnosti o oboru dotazu najdete v tématu [Rozsah dotazů protokolu a rozsah času ve Azure Monitor Log Analytics](scope.md) .
- Přístup k celé dokumentaci pro [dotazovací jazyk Kusto](/azure/kusto/query/).
