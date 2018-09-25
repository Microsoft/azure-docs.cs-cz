---
title: Standardní vlastnosti v záznamech Azure Monitor Log Analytics | Dokumentace Microsoftu
description: Popisuje vlastnosti, které jsou společné pro více typů dat ve službě Azure Monitor Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 663f0b04c528c180e4130c1c157441cbc0ceb98b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955863"
---
# <a name="standard-properties-in-log-analytics-records"></a>Standardní vlastnosti v záznamech Log Analytics
Data v [Log Analytics](../log-analytics/log-analytics-queries.md) se ukládá jako sady záznamů, každý s konkrétním datovým typem, který má jedinečnou sadu vlastností. Mnoho datových typů, bude mít standardní vlastnosti, které jsou společné pro více typů. Tento článek popisuje tyto vlastnosti a poskytuje příklady, jak je použít v dotazech.

Některé z těchto vlastností jsou stále probíhá proces jeho implementování, takže je mohou zobrazit v některé typy dat, ale ještě není v jiných.


## <a name="resourceid"></a>_ResourceId
**_ResourceId** vlastnost obsahuje jedinečný identifikátor pro prostředek, který je přidružený záznam. To vám dává standardní vlastnosti, které chcete použít k určení oboru dotazu pouze záznamy z konkrétního prostředku nebo k související data z více tabulek.

Pro prostředky Azure, hodnota **_ResourceId** je [ID URL prostředku Azure](../azure-resource-manager/resource-group-template-functions-resource.md). Vlastnost je aktuálně omezená na prostředky Azure, ale bude možné rozšířit na prostředky mimo Azure, jako jsou místní počítače. 

### <a name="examples"></a>Příklady
Následující příklad ukazuje dotaz, který spojuje výkon a data události pro každý počítač. Zobrazí všechny události s ID _101_ a využití procesoru nad 50 %.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

Následující příklad ukazuje dotaz, který spojuje _AzureActivity_ záznamy s _SecurityEvent_ záznamy. Zobrazuje všechny operace aktivity pomocí uživatele, kteří se přihlásí do těchto počítačů.

```Kusto
AzureActivity 
| where  
    OperationName in ("Restart Virtual Machine", "Create or Update Virtual Machine", "Delete Virtual Machine")  
    and ActivityStatus == "Succeeded"  
| join kind= leftouter (    
   SecurityEvent 
   | where EventID == 4624  
   | summarize LoggedOnAccounts = makeset(Account) by _ResourceId 
) on _ResourceId  
```

## <a name="next-steps"></a>Další postup

- Další informace o [Log Analytics data se ukládají](../log-analytics/log-analytics-queries.md).
- Získejte lekci na [zápis dotazů v Log Analytics](../log-analytics/query-language/get-started-queries.md).
- Získejte lekci na [spojování tabulek v dotazy Log Analytics](../log-analytics/query-language/joins.md).