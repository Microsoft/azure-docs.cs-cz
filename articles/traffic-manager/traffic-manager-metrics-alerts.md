---
title: Metriky a upozornění v Azure Traffic Manageru | Dokumentace Microsoftu
description: Tento článek popisuje dostupné metriky pro Traffic Manager v Azure.
services: traffic-manager
documentationcenter: ''
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2018
ms.author: kumud
ms.openlocfilehash: d0b2ac54f70fedc409c386243e1755704c1c332c
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53386352"
---
# <a name="traffic-manager-metrics-and-alerts"></a>Traffic Manager metrik a výstrah

Traffic Manager poskytuje vyrovnávání zátěže, což zahrnuje více směrování metody a možnosti monitorování koncových bodů DNS podle požadavků. Tento článek popisuje, metriky a související výstrahy, které jsou k dispozici zákazníkům. 

## <a name="metrics-available-in-traffic-manager"></a>Metriky, které jsou k dispozici v Traffic Manageru 

Traffic Manager poskytuje následující metriky na základě na profilu, které mohou být spotřebovány zákazníky umožnila porozumět jejich využití Traffic Manageru a stav jejich koncové body v rámci tohoto profilu.  

### <a name="queries-by-endpoint-returned"></a>Dotazy podle koncový bod vrátil
Použití [tuto metriku](../monitoring-and-diagnostics/monitoring-supported-metrics.md) zobrazíte počet dotazů, které byly zpracovány v rámci profilu Traffic Manageru v zadaném období. Tyto informace můžete zobrazit také na úrovně členitosti koncový bod, který pomáhá zjistit, jak v mnoha případech koncový bod vrátila v odpovědi na dotaz z Traffic Manageru.

V následujícím příkladu obrázek 1 zobrazuje všechny odpovědi na dotaz, které byly vráceny profil služby Traffic Manager. 

  
![Traffic Manager metriky – agregované zobrazení všech dotazů](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-queries-aggregate-view.png)

*Obrázek 1: Souhrnné zobrazení se všechny dotazy*
  
Obrázek 2 zobrazuje stejné informace, ale je rozdělený podle koncových bodů. V důsledku toho můžete zobrazit objem odpovědi na dotazy ve kterých byl vrácen určitého koncového bodu.

![Metriky Traffic Manager – rozdělené zobrazení množství dotazů za koncový bod](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-query-volume-per-endpoint.png)

*Obrázek 2: Rozdělené zobrazení se zobrazí jeden koncový bod vrátil množství dotazů*

## <a name="endpoint-status-by-endpoint"></a>Stav koncového bodu pomocí koncového bodu
Použití [tuto metriku](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftnetworktrafficmanagerprofiles) porozumět stavu koncových bodů v profilu. To má dvě hodnoty:
 - použít **1** , pokud je koncový bod.
 - použít **0** Pokud koncový bod je mimo provoz.

Tuto metriku můžete zobrazit buď jako agregovaná hodnota představující stav všech metrik (obrázek 3), nebo může být rozdělen (viz obrázek 4) k zobrazení stavu konkrétní koncové body. V případě nejprve, pokud je vybraná úroveň agregace jako **Avg**, hodnota tohoto metrika je aritmetický průměr stav všech koncových bodů. Například pokud profil, který má dva koncové body a pouze jeden je v pořádku, tato metrika bude mít hodnotu **0,50** jak je znázorněno na obrázku 3. 


![Traffic Manager metriky – složené zobrazení stavu koncových bodů](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-composite-view.png)

*Obrázek 3: Složený zobrazení metriky stav koncového bodu – vybrané agregace "Avg"*


![Metriky Traffic Manager – rozdělené zobrazení stavu koncových bodů](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-split-view.png)

*Obrázek 4: Rozdělené zobrazení metrik stav koncového bodu*

Můžete využívat tyto metriky prostřednictvím [služby Azure Monitor](../monitoring-and-diagnostics/monitoring-supported-metrics.md)na portálu, [rozhraní REST API](https://docs.microsoft.com/rest/api/monitor/), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/monitor), a [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights), nebo prostřednictvím části metriky z prostředí portálu Traffic Manageru.

## <a name="alerts-on-traffic-manager-metrics"></a>Upozornění na metriky Traffic Manageru
Kromě zpracování a zobrazení metrik z Traffic Manageru, Azure Monitor umožňuje nakonfigurovat a přijímat výstrahy přidružené k těmto metrikám. Můžete zvolit co podmínky nutné splnit v tyto metriky pro upozornění na výskyt, jak často je potřeba monitorovat tyto podmínky a jak výstrahy poslat vám. Další informace najdete v tématu [upozornění služby Azure Monitor dokumentaci](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

## <a name="next-steps"></a>Další postup
- Další informace o [služby Azure Monitor](../monitoring-and-diagnostics/monitoring-supported-metrics.md)
- Zjistěte, jak [vytvořit nový graf používat Azure Monitor](../azure-monitor/platform/metrics-charts.md#create-a-new-chart)
