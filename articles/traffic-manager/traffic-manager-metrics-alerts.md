---
title: Metriky a výstrahy ve Správci provozu Azure
description: V tomto článku najdete metriky a výstrahy dostupné pro Traffic Manager v Azure.
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2018
ms.author: rohink
ms.openlocfilehash: 521e6ac605d187c0f95545611a17a86cfda6e1dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938590"
---
# <a name="traffic-manager-metrics-and-alerts"></a>Metriky a upozornění Traffic Manageru

Traffic Manager poskytuje vyrovnávání zatížení založené na službě DNS, které zahrnuje více metod směrování a možností monitorování koncových bodů. Tento článek popisuje metriky a přidružené výstrahy, které jsou k dispozici zákazníkům. 

## <a name="metrics-available-in-traffic-manager"></a>Metriky dostupné ve Správci provozu 

Traffic Manager poskytuje následující metriky na základě profilu, které zákazníci mohou použít k pochopení jejich použití Traffic Manager a stav jejich koncových bodů v rámci tohoto profilu.  

### <a name="queries-by-endpoint-returned"></a>Vrácené dotazy podle koncového bodu
Tato [metrika](../azure-monitor/platform/metrics-supported.md) slouží k zobrazení počtu dotazů, které profil Traffic Manager zpracovává za zadané období. Můžete také zobrazit stejné informace na úrovni koncového bodu rozlišovací schopnost, která vám pomůže pochopit, kolikrát byl koncový bod vrácen v odpovědích na dotaz z Traffic Manager.

V následujícím příkladu obrázek 1 zobrazí všechny odpovědi na dotaz, které vrátí profil Traffic Manager. 

  
![Agregované zobrazení všech dotazů](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-queries-aggregate-view.png)

*Obrázek 1: Agregované zobrazení se všemi dotazy*
  
Obrázek 2 zobrazuje stejné informace, ale je rozdělen podle koncových bodů. V důsledku toho můžete zobrazit objem odpovědí na dotaz, ve kterém byl vrácen konkrétní koncový bod.

![Metriky Traffic Manageru – rozdělené zobrazení svazku dotazů na koncový bod](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-query-volume-per-endpoint.png)

*Obrázek 2: Rozdělené zobrazení s objemem dotazu zobrazeným na vrácený koncový bod*

## <a name="endpoint-status-by-endpoint"></a>Stav koncového bodu podle koncového bodu
Tato [metrika](../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) slouží k pochopení stavu koncových bodů v profilu. Trvá dvě hodnoty:
 - použijte **1,** pokud je koncový bod nahoru.
 - použijte **0,** pokud je koncový bod vypnutý.

Tuto metriku lze zobrazit buď jako agregovně představující stav všech metrik (obrázek 3), nebo ji lze rozdělit (viz obrázek 4) a zobrazit stav konkrétních koncových bodů. Pokud první, pokud úroveň agregace je **vybránjako Prům**, hodnota této metriky je aritmetický průměr stavu všech koncových bodů. Například pokud profil má dva koncové body a pouze jeden je v pořádku, pak tato metrika má hodnotu **0,50,** jak je znázorněno na obrázku 3. 


![Metriky Traffic Manageru – složené zobrazení stavu koncového bodu](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-composite-view.png)

*Obrázek 3: Kombinované zobrazení metriky stavu koncového bodu – vybraná agregace "Avg"*


![Metriky Traffic Manageru – rozdělené zobrazení stavu koncového bodu](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-split-view.png)

*Obrázek 4: Rozdělené zobrazení metrik stavu koncového bodu*

Tyto metriky můžete využívat prostřednictvím portálu [služby Azure Monitor,](../azure-monitor/platform/metrics-supported.md) [rozhraní REST API](https://docs.microsoft.com/rest/api/monitor/), rozhraní API [Azure](https://docs.microsoft.com/cli/azure/monitor)a Prostředí [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.applicationinsights)nebo prostřednictvím části metriky prostředí portálu Traffic Manageru.

## <a name="alerts-on-traffic-manager-metrics"></a>Upozornění na metriky Traffic Manageru
Kromě zpracování a zobrazování metrik z Traffic Manageru azure monitor umožňuje zákazníkům konfigurovat a přijímat výstrahy přidružené k těmto metrikám. Můžete si vybrat, jaké podmínky je třeba v těchto metrikách splnit, aby se výstraha mohla vyskytnout, jak často je třeba tyto podmínky sledovat a jak se mají výstrahy odesílat. Další informace naleznete v [tématu Azure Monitor výstrahy dokumentace](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

## <a name="next-steps"></a>Další kroky
- Další informace o [službě Azure Monitor](../azure-monitor/platform/metrics-supported.md)
- Zjistěte, jak [vytvořit graf pomocí Azure Monitoru](../azure-monitor/platform/metrics-getting-started.md#create-your-first-metric-chart)
