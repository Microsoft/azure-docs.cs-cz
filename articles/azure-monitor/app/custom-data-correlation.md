---
title: Application Insights Azure | Microsoft Docs
description: Korelujte data z Application Insights s jinými datovými sadami, jako jsou například obohacení dat nebo vyhledávací tabulky, zdroje dat, které nejsou Application Insights a vlastní data.
ms.topic: conceptual
author: eternovsky
ms.author: evternov
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 933280b5d3b81098f18f22a72bd2c7f942869e6a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578318"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>Korelace Application Insightsch dat s vlastními zdroji dat

Application Insights shromažďuje několik různých typů dat: výjimky, trasování, zobrazení stránky a další. I když je to často dostačující k prozkoumání výkonu, spolehlivosti a využití vaší aplikace, existují případy, kdy je vhodné sladit data uložená v Application Insights s dalšími zcela vlastními datovými sadami.

V některých situacích můžete chtít, aby vlastní data zahrnovala:

- Rozšíření dat nebo tabulky pro vyhledávání: doplňte název serveru s vlastníkem serveru a umístěním testovacího prostředí, ve kterém se dá najít. 
- Korelace s neApplication Insightsmi zdroji dat: můžete například korelovat data o nákupu na webu v obchodě s informacemi ze služby nákup-splnění, abyste zjistili, jak přesný odhad doby doručení byl 
- Zcela vlastní data: mnohé z našich zákazníků mají rádi dotazovací jazyk a výkon Azure Monitorové platformy pro Application Insights a chtějí je použít k dotazování na data, která se nevztahují na Application Insights. Například pro sledování výkonu zařízení slunečního chodu v rámci inteligentní instalace domů, jak je uvedeno [zde](https://www.catapultsystems.com/blogs/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/).

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>Jak korelovat vlastní data s Application Insightsmi daty 

Vzhledem k tomu, že je Application Insights zajištěna výkonnou platformou Azure Monitor, můžeme k ingestování dat využít celou sílu Azure Monitor. Pak budeme zapisovat dotazy pomocí operátoru JOIN, který bude korelovat Tato vlastní data s daty dostupnými v Azure Monitor protokoly. 

## <a name="ingesting-data"></a>Příjem dat

V této části se dozvíte, jak získat data do protokolů Azure Monitor.

Pokud ho ještě nemáte, zřiďte nový Log Analytics pracovní prostor pomocí [těchto pokynů prostřednictvím tohoto](../vm/quick-collect-azurevm.md) článku a včetně kroku vytvořit pracovní prostor.

Chcete-li začít odesílat data protokolu do Azure Monitor. Existuje několik možností:

- U synchronního mechanismu můžete buď přímo zavolat [rozhraní API kolekce dat](../logs/data-collector-api.md) nebo použít náš konektor aplikace logiky – stačí vyhledat "Azure Log Analytics" a vybrat možnost Odeslat data:

  ![Výběr a akce snímku obrazovky](./media/custom-data-correlation/01-logic-app-connector.png)  

- Pro asynchronní možnost použijte rozhraní API kolekce dat k sestavení kanálu zpracování. Podrobnosti najdete v [tomto článku](../logs/create-pipeline-datacollector-api.md) .

## <a name="correlating-data"></a>Korelace dat

Application Insights je založená na platformě Azure Monitor log. Proto můžeme pomocí [spojení mezi prostředky](../logs/cross-workspace-query.md) korelovat všechna data, která jsme ingestují do Azure monitor s využitím našich Application Insightsch dat.

Například můžeme ingestovat náš inventář a umístění testovacího prostředí do tabulky s názvem "LabLocations_CL" v pracovním prostoru Log Analytics s názvem "myLA". Pokud jsme pak chtěli zkontrolovat naše požadavky sledované v Application Insights App s názvem "myAI" a korelovat názvy počítačů, které tyto požadavky sloužily, do umístění těchto počítačů uložených v dřív zmíněné vlastní tabulce, můžeme spustit následující dotaz z kontextu Application Insights nebo Azure Monitor:

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>Další kroky

- Podívejte se na reference k [rozhraní API kolekce dat](../logs/data-collector-api.md) .
- Další informace o [spojeních mezi prostředky](../logs/cross-workspace-query.md).
