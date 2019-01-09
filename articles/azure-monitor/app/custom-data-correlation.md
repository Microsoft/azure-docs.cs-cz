---
title: Azure Application Insights | Dokumentace Microsoftu
description: ''
services: application-insights
documentationcenter: ''
author: eternovsky
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.author: Evgeny.Ternovsky
ms.openlocfilehash: d034bf130440fdb5b783db41161ab5a21a306478
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54103107"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>Usnadňuje korelování dat Application Insights s vlastním zdrojům dat

Application Insights shromažďuje několik různých datových typů: výjimky, trasování, zobrazení stránek a dalších. Když je často dostatečné pro zkoumání výkonu, spolehlivosti a využití vaší aplikace, existují případy, když je užitečné pro korelaci dat uložených ve službě Application Insights do jiné zcela vlastní datové sady.

Některé situace, kdy chcete vlastní data patří:

- Obohacení nebo vyhledávací tabulky dat: například doplněk s vlastníkem serveru a umístění testovacího prostředí, ve které je možné najít název serveru 
- Korelace se zdroji dat Application Insights: například byla korelaci dat o nákup na internetový obchod s informacemi o splnění nákupu služby k určení, jak přesné odhady přesouvání času 
- Zcela vlastní data: mnoha našich zákazníků je skvělá dotazovací jazyk a výkonu platformy dat Log Analytics, která zálohuje Application Insights a chcete ho použít k dotazování dat. vůbec týkající se služby Application Insights. Například pro sledování výkonu solární panely jako součást inteligentní domácí instalace jako uvedených [tady]( https://blogs.catapultsystems.com/cfuller/archive/2017/10/04/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/).

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>Tom, jak porovnat vlastní data s daty služby Application Insights 

Protože Application Insights se vztahuje smlouva výkonnou platformu dat Log Analytics, jsme schopní k ingestování dat plně využijte potenciál služby Log Analytics. Potom napíšeme dotazy pomocí operátoru "join", který bude korelovat tato vlastní data s daty nám k dispozici ve službě Log Analytics. 

## <a name="ingesting-data"></a>Příjem dat

V této části probereme, jak dostat data do Log Analytics.

Pokud již nemáte, zřídit nový pracovní prostor Log Analytics pomocí následujících [tyto pokyny]( https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm) prostřednictvím a včetně krok "vytvořit pracovní prostor".

Chcete-li začít odesílat data do Log Analytics. Existuje několik možností:

- Synchronní mechanismus, můžete buď přímo volat [rozhraní API kolekce dat](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) nebo použít konektor naší aplikace logiky – jednoduše vyhledejte "Azure Log Analytics" a vyberte možnost "Odeslat Data":

 ![Zvolte snímek obrazovky a akce](./media/custom-data-correlation/01-logic-app-connector.png)  

- Pro asynchronní možnost použijte rozhraní API kolekce dat k vytvoření kanálu zpracování. Zobrazit [v tomto článku](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api) podrobnosti.

## <a name="correlating-data"></a>Korelace dat

Application Insights je založen na datové platformě Log Analytics. Můžete proto používáme [spojení napříč prostředky](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search) ke korelaci žádná data, můžeme pomocí našich dat Application Insights ingestuje do Log Analytics.

Můžeme například ingestování naše inventáře testovacího prostředí a umístění do tabulky nazvané "LabLocations_CL" v pracovním prostoru Log Analytics volá "myLA". Pokud chceme potom projděte si naše požadavky v Application Insights aplikaci označovanou jako "myAI" sledovány a korelovat názvy počítačů, které se obsluhovat požadavky na umístění tyto počítače uložené v tabulce výše uvedené vlastní, můžeme spustit následující dotaz z Application Insights nebo Log Analytics kontextu:

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>Další kroky

- Podívejte se [rozhraní API kolekce dat](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) odkaz.
- Další informace o [spojení napříč prostředky](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).
