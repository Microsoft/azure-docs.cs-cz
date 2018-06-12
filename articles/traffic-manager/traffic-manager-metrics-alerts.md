---
title: Metriky a výstrahy v Azure Traffic Manageru | Microsoft Docs
description: Tento článek popisuje metriky, které jsou k dispozici pro správce provozu v Azure.
services: traffic-manager
documentationcenter: ''
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2018
ms.author: kumud
ms.openlocfilehash: 424782be2d814df6d598591198b5005fb494d3da
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35303501"
---
# <a name="traffic-manager-metrics-and-alerts"></a>Správce provozu metriky a výstrahy

Traffic Manager vám poskytne zatížení na základě DNS vyrovnávání, což zahrnuje více směrování metody a možnosti monitorování koncového bodu. Tento článek popisuje metriky a přidružených výstrahách, které jsou k dispozici zákazníkům. 

## <a name="metrics-available-in-traffic-manager"></a>Metriky, které jsou k dispozici v Traffic Manageru 

Traffic Manager poskytuje následující metriky na základě na profilu, které mohou být spotřebovávána zákazníkům pochopit jejich využití Traffic Manageru a stav své koncové body v rámci tento profil.  

### <a name="queries-by-endpoint-returned"></a>Dotazy pomocí vrátil koncový bod
Použití [tato metrika](../monitoring-and-diagnostics/monitoring-supported-metrics.md) zobrazíte počet dotazů, které byly zpracovány profil Traffic Manageru v zadaném období. Stejné informace můžete také zobrazit na úrovni členitosti koncový bod, který pomáhá pochopit, jak mnohokrát koncový bod byl vrácen v odpovědi na dotaz z Traffic Manager.

V následujícím příkladu obrázek 1 zobrazuje všechny odpovědi na dotaz vrácených rutinou profil služby Traffic Manager. 

  
![Správce provozu metriky - agregovaná zobrazení všech dotazů](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-queries-aggregate-view.png)

*Obrázek 1: Agregovat zobrazení se všechny dotazy*
  
Obrázek 2 zobrazuje stejné informace, ale je rozdělený podle koncové body. V důsledku toho se zobrazí objem odpovědi na dotazy ve kterých byl vrácen konkrétní koncový bod.

![Správce provozu metriky - rozdělení zobrazení dotazu svazku na jeden koncový bod](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-query-volume-per-endpoint.png)

*Obrázek 2: Rozdělit zobrazení pomocí dotazu svazku ukazuje na jeden koncový bod vrátil*

## <a name="endpoint-status-by-endpoint"></a>Stav koncový bod koncovým bodem
Použití [tato metrika](../monitoring-and-diagnostics/monitoring-supported-metrics.md) zjistit stav koncových bodů v profilu. Trvá dvou hodnot:
 - použít **1** , pokud je koncový bod.
 - použít **0** Pokud koncový bod je vypnutý.

Tato metrika lze zobrazit buď jako agregovaná hodnota představující stav všechny metriky (obrázek 3), nebo může být rozdělen (viz obrázek 4) k zobrazení stavu konkrétní koncových bodů. V případě dřívějším, pokud úroveň agregace je vybrán jako **průměr**, hodnota tohoto metrika je aritmetický průměr stav všechny koncové body. Například pokud profil má dva koncové body a pouze jedna je v pořádku, tato metrika bude mít hodnotu **0,50** jak je znázorněno na obrázku 3. 


![Správce provozu metriky - složené zobrazení Stav koncového bodu](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-composite-view.png)

*Obrázek 3: Složené zobrazení metrika stav koncového bodu – "Průměr" agregace vybrané*


![Správce provozu metriky - rozdělení zobrazení Stav koncového bodu](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-split-view.png)

*Obrázek 4: Zobrazení rozdělení metrik stav koncového bodu*

Můžete využívat tyto metriky prostřednictvím [služba Azure sledování](../monitoring-and-diagnostics/monitoring-supported-metrics.md)na portálu, [REST API](https://docs.microsoft.com/rest/api/monitor/), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/monitor), a [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights), nebo prostřednictvím části metriky portálu prostředí Traffic Manager.

## <a name="alerts-on-traffic-manager-metrics"></a>Výstrahy na metriky Traffic Manager
Kromě zpracování a zobrazování metrik z Traffic Manager, monitorování Azure umožňuje zákazníkům ke konfiguraci a přijímat výstrahy související s tyto metriky. Můžete podmínky nutné stanovené pro tyto metriky pro výstrahu proběhnout, jak často je potřeba monitorovat těchto podmínek a jak by měly být odeslány výstrahy pro vás. Další informace najdete v tématu [Azure monitorování výstrahy dokumentaci](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

## <a name="next-steps"></a>Další postup
- Další informace o [Azure monitorovat službu.](../monitoring-and-diagnostics/monitoring-supported-metrics.md)
- Zjistěte, jak [vytvořit nový graf pomocí Azure monitorování](../monitoring-and-diagnostics/monitoring-metric-charts.md#how-do-i-create-a-new-chart)