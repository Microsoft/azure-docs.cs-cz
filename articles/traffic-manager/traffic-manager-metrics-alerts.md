---
title: Metriky a upozornění v Azure Traffic Manageru
description: Tento článek popisuje dostupné metriky pro Traffic Manager v Azure.
services: traffic-manager
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2018
ms.author: kumud
ms.openlocfilehash: 0bce0d407246ceab05c3951dc976884dd6f15b08
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54200186"
---
# <a name="traffic-manager-metrics-and-alerts"></a>Traffic Manager metrik a výstrah

Traffic Manager poskytuje Vyrovnávání zatížení na základě DNS, který obsahuje více metod směrování a možnosti monitorování koncových bodů. Tento článek popisuje, metriky a související výstrahy, které jsou k dispozici zákazníkům. 

## <a name="metrics-available-in-traffic-manager"></a>Metriky, které jsou k dispozici v Traffic Manageru 

Traffic Manager poskytuje následující metriky na základě na profilu, který zákazníci můžou využít k jejich použití Traffic Manageru a stav jejich koncové body v rámci tohoto profilu.  

### <a name="queries-by-endpoint-returned"></a>Dotazy podle koncový bod vrátil
Použití [tuto metriku](../azure-monitor/platform/metrics-supported.md) zobrazíte počet dotazů, které se profil služby Traffic Manager zpracovává v zadaném období. Tyto informace můžete zobrazit také na úrovně členitosti koncový bod, který pomáhá zjistit, jak v mnoha případech koncový bod vrátila v odpovědi na dotaz z Traffic Manageru.

Obrázek 1 v následujícím příkladu se zobrazí všechny odpovědi na dotaz, které vrátí profil Traffic Manageru. 

  
![Souhrnné zobrazení všech dotazů](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-queries-aggregate-view.png)

*Obrázek 1: Souhrnné zobrazení se všechny dotazy*
  
Obrázek 2 zobrazuje stejné informace, ale je rozdělený podle koncových bodů. V důsledku toho můžete zobrazit objem odpovědi na dotazy ve kterých byl vrácen určitého koncového bodu.

![Metriky Traffic Manager – rozdělené zobrazení množství dotazů za koncový bod](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-query-volume-per-endpoint.png)

*Obrázek 2: Rozdělené zobrazení se zobrazí jeden koncový bod vrátil množství dotazů*

## <a name="endpoint-status-by-endpoint"></a>Stav koncového bodu pomocí koncového bodu
Použití [tuto metriku](../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) porozumět stavu koncových bodů v profilu. To má dvě hodnoty:
 - použít **1** Pokud koncový bod je v provozu.
 - použít **0** Pokud koncový bod je mimo provoz.

Tuto metriku můžete zobrazit buď jako agregovaná hodnota představující stav všech metrik (obrázek 3), nebo může být rozdělen (viz obrázek 4) k zobrazení stavu konkrétní koncové body. Pokud původní, pokud je vybrána úroveň agregace **Avg**, přínosem této metriky je aritmetický průměr stav všech koncových bodů. Například pokud profil, který má dva koncové body a pouze jeden je v pořádku, pak tato metrika má hodnotu **0,50** jak je znázorněno na obrázku 3. 


![Traffic Manager metriky – složené zobrazení stavu koncových bodů](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-composite-view.png)

*Obrázek 3: Složený zobrazení metriky stav koncového bodu – vybrané agregace "Avg"*


![Metriky Traffic Manager – rozdělené zobrazení stavu koncových bodů](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-split-view.png)

*Obrázek 4: Rozdělené zobrazení metrik stav koncového bodu*

Můžete využívat tyto metriky prostřednictvím [služby Azure Monitor](../azure-monitor/platform/metrics-supported.md)na portálu, [rozhraní REST API](https://docs.microsoft.com/rest/api/monitor/), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/monitor), a [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights), nebo prostřednictvím části metriky z prostředí portálu Traffic Manageru.

## <a name="alerts-on-traffic-manager-metrics"></a>Upozornění na metriky Traffic Manageru
Kromě zpracování a zobrazení metrik z Traffic Manageru, Azure Monitor umožňuje nakonfigurovat a přijímat výstrahy přidružené k těmto metrikám. Můžete zvolit co podmínky nutné splnit v tyto metriky pro upozornění na výskyt, jak často je potřeba monitorovat tyto podmínky a jak výstrahy poslat vám. Další informace najdete v tématu [upozornění služby Azure Monitor dokumentaci](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

## <a name="next-steps"></a>Další postup
- Další informace o [služby Azure Monitor](../azure-monitor/platform/metrics-supported.md)
- Zjistěte, jak [vytvoření grafu pomocí Azure monitoru](../azure-monitor/platform/metrics-charts.md#create-a-new-chart)
