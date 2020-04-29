---
title: Standardní vlastnosti v Azure Monitor záznamů protokolu | Microsoft Docs
description: Popisuje vlastnosti, které jsou v protokolech Azure Monitor společné pro více datových typů.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: 252ddeb372744986df0b8ba9b742d0462a4e8202
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79274473"
---
# <a name="standard-properties-in-azure-monitor-logs"></a>Standardní vlastnosti v protokolech Azure Monitor
Data v Azure Monitor protokoly se [ukládají jako sada záznamů v pracovním prostoru Log Analytics nebo v Application Insights aplikaci](../log-query/logs-structure.md), z nichž každý má konkrétní datový typ, který má jedinečnou sadu vlastností. Mnoho datových typů bude mít standardní vlastnosti, které jsou společné napříč různými typy. Tento článek popisuje tyto vlastnosti a poskytuje příklady, jak je můžete používat v dotazech.

> [!NOTE]
> Některé ze standardních vlastností se v zobrazení schématu nebo IntelliSense v Log Analytics nezobrazí a nebudou se zobrazovat ve výsledcích dotazu, pokud explicitně neurčíte vlastnost ve výstupu.

## <a name="timegenerated-and-timestamp"></a>TimeGenerated a časové razítko
Vlastnosti **TimeGenerated** (Log Analytics pracovní prostor) a **časové razítko** (Application Insights aplikace) obsahují datum a čas vytvoření záznamu zdrojem dat. Další podrobnosti najdete [v tématu čas příjmu dat protokolu v Azure monitor](data-ingestion-time.md) .

**TimeGenerated** a **timestamp** poskytují společnou vlastnost, která se má použít pro filtrování nebo sumarizaci podle času. Když vyberete časový rozsah zobrazení nebo řídicího panelu v Azure Portal, použije se k filtrování výsledků TimeGenerated nebo časové razítko. 

### <a name="examples"></a>Příklady

Následující dotaz vrátí počet chybových událostí vytvořených pro každý den v předchozím týdnu.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

Následující dotaz vrátí počet výjimek vytvořených pro každý den v předchozím týdnu.

```Kusto
exceptions
| where timestamp between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by timestamp asc 
```

## <a name="_timereceived"></a>\_TimeReceived
Vlastnost ** \_TimeReceived** obsahuje datum a čas, kdy byl záznam přijat bodem příjmu Azure monitor v cloudu Azure. To může být užitečné k identifikaci potíží s latencí mezi zdrojem dat a cloudem. Příkladem může být problém se sítí, který způsobuje zpoždění přenášená daty z agenta. Další podrobnosti najdete [v tématu čas příjmu dat protokolu v Azure monitor](data-ingestion-time.md) .

Následující dotaz poskytuje průměrnou latenci za hodinu u záznamů událostí od agenta. Patří sem čas od agenta do cloudu a celková doba, po kterou bude záznam pro dotazy protokolu dostupný.

```Kusto
Event
| where TimeGenerated > ago(1d) 
| project TimeGenerated, TimeReceived = _TimeReceived, IngestionTime = ingestion_time() 
| extend AgentLatency = toreal(datetime_diff('Millisecond',TimeReceived,TimeGenerated)) / 1000
| extend TotalLatency = toreal(datetime_diff('Millisecond',IngestionTime,TimeGenerated)) / 1000
| summarize avg(AgentLatency), avg(TotalLatency) by bin(TimeGenerated,1hr)
``` 

## <a name="type-and-itemtype"></a>Typ a itemType
Vlastnosti **typ** (Log Analytics pracovní prostor) a **ItemType** (Application Insights aplikace) obsahují název tabulky, ze které byl záznam načten, a lze jej také představit jako typ záznamu. Tato vlastnost je užitečná v dotazech, které kombinují záznamy z více tabulek, například těch, které `search` používají operátor, k odlišení záznamů různých typů. **$Table** lze použít místo **typu** na některých místech.

### <a name="examples"></a>Příklady
Následující dotaz vrátí počet záznamů podle typu shromážděných za poslední hodinu.

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_ItemId
Vlastnost ** \_ItemId** obsahuje jedinečný identifikátor záznamu.


## <a name="_resourceid"></a>\_ResourceId
Vlastnost ** \_ResourceID** obsahuje jedinečný identifikátor prostředku, ke kterému je přiřazen záznam. Získáte tak standardní vlastnost, která se má použít k určení oboru dotazu jenom na záznamy z konkrétního prostředku, nebo pro spojování souvisejících dat napříč více tabulkami.

U prostředků Azure je hodnotou **_ResourceId** [Adresa URL ID prostředku Azure](../../azure-resource-manager/templates/template-functions-resource.md). Tato vlastnost je aktuálně omezená na prostředky Azure, ale bude rozšířena na prostředky mimo Azure, jako jsou například místní počítače.

> [!NOTE]
> Některé typy dat už obsahují pole, která obsahují ID prostředku Azure nebo alespoň části, jako je ID předplatného. I když jsou tato pole zachovaná kvůli zpětné kompatibilitě, doporučuje se použít _ResourceId k provedení vzájemné korelace, protože bude lépe konzistentní.

### <a name="examples"></a>Příklady
Následující dotaz propojuje data o výkonu a událostech pro každý počítač. Zobrazuje všechny události s ID _101_ a využitím procesoru nad 50%.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

Následující dotaz spojuje záznamy _AzureActivity_ se záznamy _SecurityEvent_ . Zobrazuje všechny operace aktivity s uživateli, kteří se k těmto počítačům přihlásili.

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

Následující dotaz analyzuje **_ResourceId** a agreguje fakturované datové svazky na předplatné Azure.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last 
```

Tyto `union withsource = tt *` dotazy můžete použít zřídka, protože kontroly napříč datovými typy jsou náročné na spouštění.

## <a name="_isbillable"></a>\_Fakturovatelnost
Vlastnost disfakturovatelné určuje, zda jsou příjemovaná data fakturovatelná. ** \_** Data, která ** \_se rovnají hodnotě** _false_ , se shromažďují zdarma a neúčtují se za váš účet Azure.

### <a name="examples"></a>Příklady
Chcete-li získat seznam počítačů, které odesílají Fakturovatelné datové typy, použijte následující dotaz:

> [!NOTE]
> Používejte dotazy s `union withsource = tt *` nenáročným způsobem, protože kontroly napříč datovými typy jsou náročné na spouštění. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

To se dá rozšířit tak, aby vracelo počet počítačů za hodinu, které odesílají Fakturovatelné datové typy:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="_billedsize"></a>\_BilledSize
Vlastnost ** \_BilledSize** určuje velikost (v bajtech) dat, která se budou fakturovat vašemu účtu Azure, pokud ** \_je hodnota Fakturovatelné** .


### <a name="examples"></a>Příklady
Chcete-li zobrazit velikost fakturovaných událostí, které jsou v jednotlivých počítačích `_BilledSize` k dispozici, použijte vlastnost, která poskytuje velikost v bajtech:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Pokud chcete zobrazit velikost fakturovaných událostí, které jsou v rámci jednoho předplatného, použijte následující dotaz:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId | sort by Bytes nulls last 
```

Pokud chcete zobrazit velikost fakturovaných událostí, které se na skupinu prostředků ingestují, použijte následující dotaz:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/resourcegroups/" ResourceGroupName "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId, ResourceGroupName | sort by Bytes nulls last 

```


Chcete-li zobrazit počet zpracovaných událostí na počítač, použijte následující dotaz:

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

Chcete-li zobrazit počet fakturovaných událostí zpracovaných na počítač, použijte následující dotaz: 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

Chcete-li zobrazit počet fakturovatelných datových typů z určitého počítače, použijte následující dotaz:

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o tom, jak [se ukládají data protokolu Azure monitor](../log-query/log-query-overview.md).
- Získejte lekci k [zápisu dotazů protokolu](../../azure-monitor/log-query/get-started-queries.md).
- Získejte lekci o [spojování tabulek v protokolových dotazech](../../azure-monitor/log-query/joins.md).
