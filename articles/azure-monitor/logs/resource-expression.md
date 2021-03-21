---
title: výraz prostředku () v Azure Monitor dotaz na protokol | Microsoft Docs
description: Výraz prostředku se používá v dotazu protokolu Azure Monitor orientovaném na prostředky k načtení dat z více zdrojů.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 9a5e5c7959a243d6c9d243b706524f624ffa3cdb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047209"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>výraz prostředku () v dotazu Azure Monitor protokolu

`resource`Výraz se používá v Azure monitor dotazu [vymezeném na prostředek](scope.md#query-scope) pro načtení dat z jiných prostředků. 


## <a name="syntax"></a>Syntax

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
