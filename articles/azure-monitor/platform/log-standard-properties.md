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
ms.topic: article
ms.date: 09/27/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: e4e921223676f4b5d64025c67914fc8b7c29a6fe
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52974973"
---
# <a name="standard-properties-in-log-analytics-records"></a>Standardní vlastnosti v záznamech Log Analytics
Data v [Log Analytics](../log-query/log-query-overview.md) se ukládá jako sady záznamů, každý s konkrétním datovým typem, který má jedinečnou sadu vlastností. Mnoho datových typů, bude mít standardní vlastnosti, které jsou společné pro více typů. Tento článek popisuje tyto vlastnosti a poskytuje příklady, jak je použít v dotazech.

Některé z těchto vlastností jsou stále probíhá proces jeho implementování, takže je mohou zobrazit v některé typy dat, ale ještě není v jiných.

## <a name="timegenerated"></a>TimeGenerated
**TimeGenerated** vlastnost obsahuje datum a čas, který byl vytvořen záznam. Poskytuje běžné vlastnosti, které chcete použít pro filtrování nebo sumarizace podle času. Vyberte časový rozsah pro zobrazení nebo řídicím panelu na webu Azure Portal, použije k filtrování výsledků TimeGenerated.

### <a name="examples"></a>Příklady

Následující dotaz vrátí počet chyb, vytvoří se události za každý den předchozího týdne.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

## <a name="type"></a>Typ
**Typ** vlastnost obsahuje název tabulky, ze kterého byla načtena záznam můžete také představit jako typ záznamu. Tato vlastnost je užitečná v dotazech, které kombinují záznamů z několika tabulek, jako jsou ty, které používají `search` operátor k rozlišení mezi záznamy různých typů. **$table** lze použít místo **typ** na některých místech.

### <a name="examples"></a>Příklady
Následující dotaz vrátí počet záznamů podle typu shromážděné za poslední hodinu.

```Kusto
search * 
| where TimeGenerated > ago(1h) 
| summarize count() by Type 
```

## <a name="resourceid"></a>\_ID prostředku
 **\_ResourceId** vlastnost obsahuje jedinečný identifikátor pro prostředek, který je přidružený záznam. To vám dává standardní vlastnosti, které chcete použít k určení oboru dotazu pouze záznamy z konkrétního prostředku nebo k související data z více tabulek.

Pro prostředky Azure, hodnota **_ResourceId** je [ID URL prostředku Azure](../../azure-resource-manager/resource-group-template-functions-resource.md). Vlastnost je aktuálně omezená na prostředky Azure, ale bude možné rozšířit na prostředky mimo Azure, jako jsou místní počítače.

> [!NOTE]
> Některé typy dat již obsahuje pole, která obsahují ID prostředku Azure nebo alespoň části jeho jako ID předplatného. Zatímco tato pole se neodstraní z důvodu zpětné kompatibility se doporučuje použít _ResourceId provést křížové korelace, protože je konzistentnější.

### <a name="examples"></a>Příklady
Následující dotaz spojí data o výkonu a události pro každý počítač. Zobrazí všechny události s ID _101_ a využití procesoru nad 50 %.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

Následující dotaz spojení _AzureActivity_ záznamy s _SecurityEvent_ záznamy. Zobrazuje všechny operace aktivity pomocí uživatele, kteří se přihlásí do těchto počítačů.

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

- Další informace o [Log Analytics data se ukládají](../log-query/log-query-overview.md).
- Získejte lekci na [zápis dotazů v Log Analytics](../../azure-monitor/log-query/get-started-queries.md).
- Získejte lekci na [spojování tabulek v dotazy Log Analytics](../../azure-monitor/log-query/joins.md).
