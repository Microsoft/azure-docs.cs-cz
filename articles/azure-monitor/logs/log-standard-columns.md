---
title: Standardní sloupce v Azure Monitor záznamy protokolu | Microsoft Docs
description: Popisuje sloupce, které jsou v protokolech Azure Monitor společné pro více datových typů.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/25/2021
ms.openlocfilehash: 5b906bdbd07d59d2acc88f6b30f0db6b6cbc961a
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103562242"
---
# <a name="standard-columns-in-azure-monitor-logs"></a>Standardní sloupce v protokolech Azure Monitor
Data v Azure Monitor protokoly se [ukládají jako sada záznamů v pracovním prostoru Log Analytics nebo v Application Insights aplikaci](../logs/data-platform-logs.md), z nichž každý má konkrétní datový typ, který má jedinečnou sadu sloupců. Mnoho datových typů bude mít standardní sloupce, které jsou společné pro různé typy. Tento článek popisuje tyto sloupce a poskytuje příklady, jak je můžete použít v dotazech.

Aplikace založené na pracovních prostorech v Application Insights ukládají svá data do pracovního prostoru Log Analytics a používají stejné standardní sloupce jako jiné jiné tabulky v pracovním prostoru. Klasické aplikace ukládají svá data samostatně a mají různé standardní sloupce, jak je uvedeno v tomto článku.

> [!NOTE]
> Některé standardní sloupce se nebudou zobrazovat v zobrazení schématu nebo IntelliSense v Log Analytics a nebudou se zobrazovat ve výsledcích dotazu, pokud explicitně neurčíte sloupec ve výstupu.
> 

## <a name="tenantid"></a>TenantId
Sloupec **TenantId** obsahuje ID pracovního prostoru Log Analytics pracovního prostoru.

## <a name="timegenerated-and-timestamp"></a>TimeGenerated a časové razítko
Sloupce **TimeGenerated** (Log Analytics pracovní prostor) a **časové razítko** (Application Insights aplikace) obsahují datum a čas vytvoření záznamu zdrojem dat. Další podrobnosti najdete [v tématu čas příjmu dat protokolu v Azure monitor](../logs/data-ingestion-time.md) .

**TimeGenerated** a **timestamp** poskytují společný sloupec, který se má použít pro filtrování nebo sumarizaci podle času. Když vyberete časový rozsah zobrazení nebo řídicího panelu v Azure Portal, použije se k filtrování výsledků TimeGenerated nebo časové razítko. 

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
Sloupec **\_ TimeReceived** obsahuje datum a čas, kdy byl záznam přijat bodem příjmu Azure monitor v cloudu Azure. To může být užitečné k identifikaci potíží s latencí mezi zdrojem dat a cloudem. Příkladem může být problém se sítí, který způsobuje zpoždění přenášená daty z agenta. Další podrobnosti najdete [v tématu čas příjmu dat protokolu v Azure monitor](../logs/data-ingestion-time.md) .

> [!NOTE]
> Sloupec **\_ TimeReceived** se vypočítá pokaždé, když se použije. Tento proces je náročný na prostředky. Upřesněte jejich použití pro filtrování velkého počtu záznamů. Současné použití této funkce může vést ke zvýšení doby trvání provádění dotazu.


Následující dotaz poskytuje průměrnou latenci za hodinu u záznamů událostí od agenta. To zahrnuje dobu od agenta do cloudu a celkovou dobu, po kterou bude záznam pro dotazy protokolu dostupný.

```Kusto
Event
| where TimeGenerated > ago(1d) 
| project TimeGenerated, TimeReceived = _TimeReceived, IngestionTime = ingestion_time() 
| extend AgentLatency = toreal(datetime_diff('Millisecond',TimeReceived,TimeGenerated)) / 1000
| extend TotalLatency = toreal(datetime_diff('Millisecond',IngestionTime,TimeGenerated)) / 1000
| summarize avg(AgentLatency), avg(TotalLatency) by bin(TimeGenerated,1hr)
``` 

## <a name="type-and-itemtype"></a>Typ a itemType
Sloupce **typ** (Log Analytics pracovní prostor) a **ItemType** (Application Insights aplikace) obsahují název tabulky, ze které byl záznam načten, a lze jej také představit jako typ záznamu. Tento sloupec je užitečný v dotazech, které kombinují záznamy z více tabulek, například těch, které používají `search` operátor, k odlišení záznamů různých typů. **$Table** lze použít místo **typu** na některých místech.

### <a name="examples"></a>Příklady
Následující dotaz vrátí počet záznamů podle typu shromážděných za poslední hodinu.

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_ItemId
Sloupec **\_ itemid** obsahuje jedinečný identifikátor záznamu.


## <a name="_resourceid"></a>\_ResourceId
Sloupec **\_ ResourceID** obsahuje jedinečný identifikátor prostředku, ke kterému je přiřazen záznam. Díky tomu máte standardní sloupec, který se použije k určení oboru dotazu jenom na záznamy z konkrétního prostředku, nebo pro spojování souvisejících dat napříč více tabulkami.

U prostředků Azure je hodnotou **_ResourceId** [Adresa URL ID prostředku Azure](../../azure-resource-manager/templates/template-functions-resource.md). Sloupec je omezený na prostředky Azure, včetně prostředků [ARC Azure](../../azure-arc/overview.md) , nebo vlastních protokolů, které během příjmu uvedli ID prostředku.

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

Následující dotaz analyzuje **_ResourceId** a agreguje účtované datové svazky na skupinu prostředků Azure.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by resourceGroup | sort by Bytes nulls last 
```

Tyto dotazy můžete použít `union withsource = tt *` zřídka, protože kontroly napříč datovými typy jsou náročné na spouštění.

Je vždycky efektivnější použít \_ sloupec SubscriptionId, než ho extrahujete analýzou \_ sloupce ResourceID.

## <a name="_subscriptionid"></a>\_SubscriptionId
Sloupec **\_ SUBSCRIPTIONID** obsahuje ID předplatného prostředku, ke kterému je daný záznam přidružen. Získáte tak standardní sloupec, který se použije k určení oboru dotazu jenom na záznamy z konkrétního předplatného nebo pro porovnání různých předplatných.

U prostředků Azure je hodnotou **__SubscriptionId** předplatným část [adresy URL prostředku Azure ID](../../azure-resource-manager/templates/template-functions-resource.md). Sloupec je omezený na prostředky Azure, včetně prostředků [ARC Azure](../../azure-arc/overview.md) , nebo vlastních protokolů, které během příjmu uvedli ID prostředku.

> [!NOTE]
> Některé typy dat už obsahují pole, která obsahují ID předplatného Azure. I když jsou tato pole zachovaná pro zpětnou kompatibilitu, doporučuje se použít \_ sloupec SubscriptionId k provedení vzájemné korelace, protože bude lépe konzistentní.
### <a name="examples"></a>Příklady
Následující dotaz prověřuje údaje o výkonu pro počítače určitého předplatného. 

```Kusto
Perf 
| where TimeGenerated > ago(24h) and CounterName == "memoryAllocatableBytes"
| where _SubscriptionId == "57366bcb3-7fde-4caf-8629-41dc15e3b352"
| summarize avgMemoryAllocatableBytes = avg(CounterValue) by Computer
```

Následující dotaz analyzuje **_ResourceId** a agreguje fakturované datové svazky na předplatné Azure.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _SubscriptionId | sort by Bytes nulls last 
```

Tyto dotazy můžete použít `union withsource = tt *` zřídka, protože kontroly napříč datovými typy jsou náročné na spouštění.


## <a name="_isbillable"></a>\_Fakturovatelnost
Sloupec **\_ disfakturovatelné** určuje, zda jsou příjemovaná data fakturovatelná. Data s **hodnotou, která \_** se rovná, `false` se shromažďují zdarma a neúčtují se za váš účet Azure.

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
Sloupec **\_ BilledSize** určuje velikost dat (v bajtech), která se budou fakturovat vašemu účtu Azure, pokud je hodnota **\_ Fakturovatelné** .


### <a name="examples"></a>Příklady
Chcete-li zobrazit velikost fakturovaných událostí, které jsou v jednotlivých počítačích k `_BilledSize` dispozici, použijte sloupec, který poskytuje velikost v bajtech:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Pokud chcete zobrazit velikost fakturovaných událostí, které jsou v rámci jednoho předplatného, použijte následující dotaz:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  _SubscriptionId | sort by Bytes nulls last 
```

Pokud chcete zobrazit velikost fakturovaných událostí, které se na skupinu prostředků ingestují, použijte následující dotaz:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/resourcegroups/" ResourceGroupName "/" *
| summarize Bytes=sum(_BilledSize) by  _SubscriptionId, ResourceGroupName | sort by Bytes nulls last 

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

- Přečtěte si další informace o tom, jak [se ukládají data protokolu Azure monitor](./log-query-overview.md).
- Získejte lekci k [zápisu dotazů protokolu](./get-started-queries.md).
- Získejte lekci o [spojování tabulek v protokolových dotazech](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins).
