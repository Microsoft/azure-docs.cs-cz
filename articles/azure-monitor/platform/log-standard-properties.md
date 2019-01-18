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
ms.date: 01/14/2019
ms.author: bwren
ms.openlocfilehash: 27c732a2ddd21401ffbefa727cbb8001ec288293
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54381955"
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

## <a name="isbillable"></a>\_IsBillable
 **\_IsBillable** vlastnost určuje, zda přijatých dat se dají fakturovat. Data s  **\_IsBillable** rovna _false_ se shromažďují zadarmo a není účtují ke svému účtu Azure.

### <a name="examples"></a>Příklady
Pokud chcete získat seznam počítačů odesílajících billed datové typy, použijte následující dotaz:

> [!NOTE]
> Použití dotazů s `union withsource = tt *` střídmě kontroly typům dat je vždycky provést. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

To je možné rozšířit na vrátí počet počítačů za hodinu, které odesílají účtuje datové typy:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="billedsize"></a>\_BilledSize
 **\_BilledSize** vlastnost určuje velikost v bajtech data, která se ke svému účtu Azure účtovat, pokud  **\_IsBillable** má hodnotu true.

### <a name="examples"></a>Příklady
Pokud chcete zobrazit velikost účtovaných událostí může ingestovat počítače, použijte `_BilledSize` vlastnost, která poskytuje velikost v bajtech:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Pokud chcete zobrazit počet událostí může ingestovat počítače, použijte následující dotaz:

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

Pokud chcete zobrazit počet účtovaných událostí může ingestovat počítače, použijte tento dotaz: 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

Pokud chcete vidět, že počet účtovaných datové typy jsou odesílání dat k určitému počítači, použijte následující dotaz:

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```


## <a name="next-steps"></a>Další postup

- Další informace o [Log Analytics data se ukládají](../log-query/log-query-overview.md).
- Získejte lekci na [zápis dotazů v Log Analytics](../../azure-monitor/log-query/get-started-queries.md).
- Získejte lekci na [spojování tabulek v dotazy Log Analytics](../../azure-monitor/log-query/joins.md).
