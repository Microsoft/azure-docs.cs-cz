---
title: výraz prostředku () v Azure Monitor dotaz na protokol | Microsoft Docs
description: Výraz prostředku se používá v dotazu protokolu Azure Monitor orientovaném na prostředky k načtení dat z více zdrojů.
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
ms.openlocfilehash: deca6e7ef1c231a82a73067971d86a6e9cdd0599
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817407"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>výraz prostředku () v dotazu Azure Monitor protokolu

Výraz `resource` se používá v rámci dotazu Azure Monitor [oboru](scope.md#query-scope) pro načtení dat z jiných prostředků. 


## <a name="syntax"></a>Syntaxe

*identifikátor*`resource(` `)`

## <a name="arguments"></a>Argumenty

- *Identifikátor*: ID prostředku prostředku.

| Identifikátor | Popis | Příklad:
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
