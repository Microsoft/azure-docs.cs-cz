---
title: resource() výraz v dotazu protokolu Azure Monitor | Dokumenty společnosti Microsoft
description: Výraz prostředků se používá v dotazu protokolu Azure Monitor zaměřené na prostředky k načtení dat z více prostředků.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 2a729caefe698b13833098ba48df9d4bfbd97356
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665695"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>výraz resource() v dotazu protokolu Azure Monitor

Výraz `resource` se používá v dotazu Azure Monitor [vymezené prostředek](scope.md#query-scope) k načtení dat z jiných prostředků. 


## <a name="syntax"></a>Syntaxe

`resource(`*Identifikátor*`)`

## <a name="arguments"></a>Argumenty

- *Identifikátor*: ID prostředku.

| Identifikátor | Popis | Příklad
|:---|:---|:---|
| Prostředek | Zahrnuje data pro prostředek. | resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm") |
| Skupina prostředků nebo předplatné | Zahrnuje data pro prostředek a všechny prostředky, které obsahuje.  | resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup) |


## <a name="notes"></a>Poznámky

* Musíte mít přístup pro čtení k prostředku.


## <a name="examples"></a>Příklady

```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm").Heartbeat) | summarize count() by _ResourceId, TenantId
```
```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup).Heartbeat) | summarize count() by _ResourceId, TenantId
```


## <a name="next-steps"></a>Další kroky

- Podrobnosti o oboru dotazu najdete v tématu Rozsah a časový rozsah protokolu protokolu [služby Azure Monitor.](scope.md)
- Získejte přístup k úplné dokumentaci [k dotazovacímu jazyku Kusto](/azure/kusto/query/).
