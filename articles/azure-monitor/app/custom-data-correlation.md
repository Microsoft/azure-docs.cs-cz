---
title: Přehledy aplikací Azure | Dokumenty společnosti Microsoft
description: Korelujte data z Application Insights s jinými datovými sadami, jako je například obohacení dat nebo vyhledávací tabulky, zdroje dat, které nejsou application insights, a vlastní data.
ms.topic: conceptual
author: eternovsky
ms.author: evternov
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 121e4699bd6a72f6865d3a6ffdef58c1b3806047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082757"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>Korelování dat Application Insights s vlastními zdroji dat

Application Insights shromažďuje několik různých datových typů: výjimky, trasování, zobrazení stránek a další. I když je to často dostačující k prozkoumání výkonu, spolehlivosti a využití vaší aplikace, existují případy, kdy je užitečné korelovat data uložená v Application Insights s jinými zcela vlastní datové sady.

Některé situace, kde můžete chtít vlastní data patří:

- Obohacení dat nebo vyhledávací tabulky: například doplnit název serveru o vlastníka serveru a umístění testovacího prostředí, ve kterém se nachází 
- Korelace s neaplikačními zdroji dat: například korelovat data o nákupu v internetovém obchodě s informacemi ze služby plnění nákupu a nákupu, abyste zjistili, jak přesné byly odhady doby přepravy 
- Zcela vlastní data: mnoho našich zákazníků miluje dotazovací jazyk a výkon platformy protokolu Azure Monitor, která podporuje Application Insights a chcete je použít k dotazování dat, která vůbec nesouvisí s Application Insights. Chcete-li například sledovat výkon solárního panelu jako součást inteligentní domácí instalace, jak je uvedeno [zde](https://www.catapultsystems.com/blogs/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/).

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>Jak korelovat vlastní data s daty Application Insights 

Vzhledem k tomu, že Application Insights je zálohována výkonnou platformou protokolu Azure Monitor, jsme schopni využít plný výkon Azure Monitor u ingestování dat. Potom budeme psát dotazy pomocí operátoru "join", který bude korelovat tato vlastní data s daty, která máme k dispozici v protokolech Azure Monitor. 

## <a name="ingesting-data"></a>Příjem dat

V této části zkontrolujeme, jak získat vaše data do protokolů Azure Monitor.

Pokud ještě nemáte, zřídit nový pracovní prostor Log Analytics podle [těchto pokynů](../learn/quick-collect-azurevm.md) prostřednictvím a včetně "vytvořit pracovní prostor" krok.

Chcete-li začít odesílat data protokolu do Azure Monitoru. Existuje několik možností:

- Pro synchronní mechanismus můžete buď přímo volat [rozhraní API pro sběr dat](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) nebo použít náš konektor aplikace logiky – jednoduše vyhledejte "Azure Log Analytics" a vyberte možnost "Odeslat data":

  ![Výběr a akce pro výběr snímku obrazovky](./media/custom-data-correlation/01-logic-app-connector.png)  

- Pro asynchronní možnost použijte rozhraní API kolektoru dat k vytvoření kanálu zpracování. Podrobnosti najdete v [tomto článku.](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api)

## <a name="correlating-data"></a>Korelace dat

Application Insights je založen na platformě protokolu Azure Monitor. Proto můžeme použít spojení mezi prostředky ke korelaci všech dat, která jsme intetovali do Azure Monitoru s našimi [daty](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search) Application Insights.

Například můžeme ingestovat naše laboratorní inventář e-míst do tabulky s názvem "LabLocations_CL" v pracovním prostoru Log Analytics s názvem "myLA". Pokud bychom pak chtěli zkontrolovat naše požadavky sledované v aplikaci Application Insights s názvem "myAI" a korelovat názvy počítačů, které obsluhovaly požadavky na umístění těchto počítačů uložených ve výše uvedené vlastní tabulce, můžeme spustit následující dotaz z kontext Application Insights nebo Azure Monitor:

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>Další kroky

- Podívejte se na odkaz [rozhraní API kolektoru dat.](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api)
- Další informace o [spojení mezi zdroji](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).
