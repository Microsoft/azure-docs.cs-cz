---
title: Standardní vlastnosti v záznamech protokolu Azure Monitor | Dokumenty společnosti Microsoft
description: Popisuje vlastnosti, které jsou společné pro více datových typů v protokolech Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: 252ddeb372744986df0b8ba9b742d0462a4e8202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274473"
---
# <a name="standard-properties-in-azure-monitor-logs"></a>Standardní vlastnosti v protokolech monitorování Azure
Data v protokolech monitorování Azure se [ukládají jako sada záznamů v pracovním prostoru Analýzy protokolů nebo v aplikaci Application Insights](../log-query/logs-structure.md), z nichž každá má určitý datový typ, který má jedinečnou sadu vlastností. Mnoho datových typů bude mít standardní vlastnosti, které jsou společné pro více typů. Tento článek popisuje tyto vlastnosti a poskytuje příklady, jak je můžete použít v dotazech.

> [!NOTE]
> Některé standardní vlastnosti se nezobrazí v zobrazení schématu nebo intellisense v Log Analytics a nebudou zobrazovat ve výsledcích dotazu, pokud explicitně nezadáte vlastnost ve výstupu.

## <a name="timegenerated-and-timestamp"></a>TimeGenerated a časové razítko
**Vlastnosti TimeGenerated** (Log Analytics) a **timestamp** (Application Insights application) obsahují datum a čas, kdy byl záznam vytvořen zdrojem dat. Další podrobnosti najdete [v tématu Doba přihlašování dat protokolu v Azure Monitoru.](data-ingestion-time.md)

**TimeGenerated** a **časové razítko** poskytují společnou vlastnost, která se má použít pro filtrování nebo shrnutí podle času. Když vyberete časový rozsah pro zobrazení nebo řídicí panel na webu Azure Portal, použije TimeGenerated nebo časové razítko k filtrování výsledků. 

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

## <a name="_timereceived"></a>\_Časobí
Vlastnost ** \_TimeReceived** obsahuje datum a čas, kdy byl záznam přijat bodem ingestování Azure Monitor v cloudu Azure. To může být užitečné pro identifikaci problémů s latencí mezi zdrojem dat a cloudem. Příkladem může být problém se sítí, který způsobuje zpoždění s daty odesílanými od agenta. Další podrobnosti najdete [v tématu Doba přihlašování dat protokolu v Azure Monitoru.](data-ingestion-time.md)

Následující dotaz poskytuje průměrnou latenci po hodině pro záznamy událostí od agenta. To zahrnuje čas od agenta do cloudu a celkový čas pro záznam, který má být k dispozici pro dotazy protokolu.

```Kusto
Event
| where TimeGenerated > ago(1d) 
| project TimeGenerated, TimeReceived = _TimeReceived, IngestionTime = ingestion_time() 
| extend AgentLatency = toreal(datetime_diff('Millisecond',TimeReceived,TimeGenerated)) / 1000
| extend TotalLatency = toreal(datetime_diff('Millisecond',IngestionTime,TimeGenerated)) / 1000
| summarize avg(AgentLatency), avg(TotalLatency) by bin(TimeGenerated,1hr)
``` 

## <a name="type-and-itemtype"></a>Typ a itemType
**Vlastnosti Type** (Log Analytics workspace) a **itemType** (Application Insights application) mají název tabulky, ze které byl záznam načten, ze které lze také považovat za typ záznamu. Tato vlastnost je užitečná v dotazech, které kombinují `search` záznamy z více tabulek, jako jsou ty, které používají operátor, k rozlišení mezi záznamy různých typů. **$table** lze použít místo **typu** v některých místech.

### <a name="examples"></a>Příklady
Následující dotaz vrátí počet záznamů podle typu shromážděných za poslední hodinu.

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_Itemid
Vlastnost ** \_ItemId** obsahuje jedinečný identifikátor záznamu.


## <a name="_resourceid"></a>\_ResourceId
Vlastnost ** \_ResourceId** obsahuje jedinečný identifikátor prostředku, ke kterému je záznam přidružen. To vám poskytuje standardní vlastnost, která se používá k oboru dotazu pouze na záznamy z určitého prostředku nebo k připojení souvisejících dat mezi více tabulkami.

Pro prostředky Azure je hodnota **_ResourceId** [adresa URL ID prostředku Azure](../../azure-resource-manager/templates/template-functions-resource.md). Vlastnost je aktuálně omezená na prostředky Azure, ale bude rozšířena na prostředky mimo Azure, jako jsou místní počítače.

> [!NOTE]
> Některé datové typy už mají pole, která obsahují ID prostředků Azure nebo alespoň jeho části, jako je ID předplatného. Zatímco tato pole jsou uchovávány pro zpětnou kompatibilitu, je doporučeno použít _ResourceId k provedení křížové korelace, protože bude konzistentnější.

### <a name="examples"></a>Příklady
Následující dotaz spojuje data o výkonu a událostech pro každý počítač. Zobrazuje všechny události s ID _101_ a využití procesoru nad 50%.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

Následující dotaz spojuje záznamy _AzureActivity_ se záznamy _SecurityEvent._ Zobrazuje všechny operace aktivity s uživateli, kteří byli přihlášeni k těmto počítačům.

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

Následující dotaz analyzuje **_ResourceId** a agreguje fakturované datové svazky na jedno předplatné Azure.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last 
```

Používejte `union withsource = tt *` tyto dotazy střídmě jako prohledávání napříč datovými typy jsou nákladné spustit.

## <a name="_isbillable"></a>\_Je fakturovatelný
** \_IsBillable** vlastnost určuje, zda jsou zúčtovatelná data. Data s ** \_isfakturovatelným** rovná _se false_ se shromažďují zdarma a nefakturují se na váš účet Azure.

### <a name="examples"></a>Příklady
Chcete-li získat seznam počítačů odesílajících fakturované datové typy, použijte následující dotaz:

> [!NOTE]
> Používejte dotazy `union withsource = tt *` s střídmě jako prohledávání napříč datovými typy jsou nákladné spustit. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

To lze rozšířit vrátit počet počítačů za hodinu, které jsou odesílání fakturovaných datových typů:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="_billedsize"></a>\_Fakturovaná velikost
Vlastnost ** \_BilledSize** určuje velikost v bajtů dat, které se budou účtovat na váš účet Azure, pokud ** \_je true IsBillable.**


### <a name="examples"></a>Příklady
Chcete-li zobrazit velikost fakturovatelných událostí pozůstalých v počítači, použijte `_BilledSize` vlastnost, která poskytuje velikost v bajtů:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Chcete-li zobrazit velikost fakturovatelných událostí poživaných na jedno předplatné, použijte následující dotaz:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId | sort by Bytes nulls last 
```

Chcete-li zobrazit velikost fakturovatelných událostí polykaných podle skupiny prostředků, použijte následující dotaz:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/resourcegroups/" ResourceGroupName "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId, ResourceGroupName | sort by Bytes nulls last 

```


Chcete-li zobrazit počet požitých událostí v počítači, použijte následující dotaz:

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

Chcete-li zobrazit počet fakturovatelných událostí poživaných na počítači, použijte následující dotaz: 

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

- Přečtěte si další informace o tom, jak [se ukládají data protokolu Azure Monitor](../log-query/log-query-overview.md).
- Získejte lekci o [psaní dotazů protokolu](../../azure-monitor/log-query/get-started-queries.md).
- Získejte lekci [o spojování tabulek v dotazech protokolu](../../azure-monitor/log-query/joins.md).
