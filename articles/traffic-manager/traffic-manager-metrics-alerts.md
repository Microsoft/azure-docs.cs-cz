---
title: Metriky a výstrahy v Azure Traffic Manager
description: V tomto článku se seznámíte s metrikami a upozorněními dostupnými pro Traffic Manager v Azure.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2018
ms.author: duau
ms.openlocfilehash: 997e2e97161e6db7cdca04c1b79f5149a2e4c409
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89392846"
---
# <a name="traffic-manager-metrics-and-alerts"></a>Traffic Manager metriky a výstrahy

Traffic Manager poskytuje vyrovnávání zatížení založené na DNS, které zahrnuje několik metod směrování a možností monitorování koncových bodů. Tento článek popisuje metriky a související výstrahy, které jsou k dispozici pro zákazníky. 

## <a name="metrics-available-in-traffic-manager"></a>Metriky dostupné v Traffic Manager 

Traffic Manager poskytuje následující metriky pro jednotlivé profily, které zákazníci můžou použít k pochopení jejich používání Traffic Manageru a stavu jejich koncových bodů v rámci tohoto profilu.  

### <a name="queries-by-endpoint-returned"></a>Dotazy podle vráceného koncového bodu
Pomocí [této metriky](../azure-monitor/platform/metrics-supported.md) můžete zobrazit počet dotazů, které profil Traffic Manager zpracuje v zadaném období. Můžete také zobrazit stejné informace v členitosti na úrovni koncového bodu, které vám pomohou pochopit, kolikrát byl vrácen koncový bod v odpovědích na dotaz z Traffic Manager.

V následujícím příkladu obrázek 1 zobrazí všechny odpovědi na dotazy, které vrací profil Traffic Manager. 

  
![Agregované zobrazení všech dotazů](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-queries-aggregate-view.png)

*Obrázek 1: agregované zobrazení se všemi dotazy*
  
Obrázek 2 zobrazuje stejné informace, ale je rozdělen podle koncových bodů. V důsledku toho můžete zobrazit objem odpovědí na dotaz, ve kterých byl vrácen konkrétní koncový bod.

![Traffic Manager metriky – rozdělené zobrazení svazku dotazů na koncový bod](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-query-volume-per-endpoint.png)

*Obrázek 2: rozdělené zobrazení se zobrazeným svazkem dotazu za vráceným koncovým bodem*

## <a name="endpoint-status-by-endpoint"></a>Stav koncového bodu podle koncového bodu
[Tuto metriku](../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) použijte k pochopení stavu koncových bodů v profilu. Má dvě hodnoty:
 - Pokud je koncový bod v provozu, použijte hodnotu **1** .
 - Pokud koncový bod nefunguje, použijte **hodnotu 0** .

Tato metrika se dá zobrazit buď jako agregovaná hodnota představující stav všech metrik (obrázek 3), nebo může být rozdělená (viz obrázek 4), aby se zobrazil stav konkrétních koncových bodů. Pokud se používá, pokud je úroveň agregace vybraná jako **střední**, hodnota této metriky je aritmetický průměr stavu všech koncových bodů. Pokud má například profil dva koncové body a pouze jeden je v pořádku, má tato metrika hodnotu **0,50** , jak je znázorněno na obrázku 3. 


![Metriky Traffic Manager – kompozitní pohled na stav koncového bodu](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-composite-view.png)

*Obrázek 3: kompozitní zobrazení metriky stavu koncového bodu – výběr agregace "průměr"*


![Traffic Manager metriky – rozdělené zobrazení stavu koncového bodu](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-split-view.png)

*Obrázek 4: rozdělené zobrazení metrik stavu koncového bodu*

Tyto metriky můžete využívat prostřednictvím portálu [služby Azure monitor](../azure-monitor/platform/metrics-supported.md), [REST API](https://docs.microsoft.com/rest/api/monitor/), [Azure CLI](https://docs.microsoft.com/cli/azure/monitor)a [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.applicationinsights)nebo prostřednictvím části metriky v prostředí portálu Traffic Manager.

## <a name="alerts-on-traffic-manager-metrics"></a>Výstrahy na Traffic Manager metriky
Kromě zpracování a zobrazování metrik z Azure Monitor Traffic Manager umožňuje zákazníkům nakonfigurovat a přijímat výstrahy spojené s těmito metrikami. V těchto metrikách si můžete vybrat, jaké podmínky je potřeba splnit, aby se zobrazila výstraha, jak často se tyto podmínky musí monitorovat a jak se mají výstrahy posílat. Další informace najdete v [dokumentaci Azure monitor Alerts](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

## <a name="next-steps"></a>Další kroky
- Další informace o [službě Azure Monitor Service](../azure-monitor/platform/metrics-supported.md)
- Naučte se [vytvořit graf pomocí Azure monitor](../azure-monitor/platform/metrics-getting-started.md#create-your-first-metric-chart)
