---
title: Přehled přehledů ve službě Azure Monitor | Dokumenty společnosti Microsoft
description: Přehledy poskytují přizpůsobené monitorování v Azure Monitoru pro konkrétní aplikace a služby. Tento článek obsahuje stručný popis jednotlivých přehledů, které jsou aktuálně k dispozici.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/22/2019
ms.openlocfilehash: 15ea7698c9e90fa8b0dfa20f71b552a2b0e9c7d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657244"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Přehled přehledů ve službě Azure Monitor
Přehledy poskytují přizpůsobené prostředí monitorování pro konkrétní aplikace a služby. Ukládají data na [datové platformě Azure Monitor](../platform/data-platform.md) a využívají další funkce Azure Monitoru pro analýzu a upozorňování, ale můžou shromažďovat další data a poskytovat jedinečné uživatelské prostředí na webu Azure Portal. Získejte přístup k přehledům z části **Přehledy** v nabídce Azure Monitor na webu Azure Portal.

Následující části obsahují stručný popis přehledů, které jsou aktuálně dostupné ve službě Azure Monitor. Podrobnosti o jednotlivých tématech naleznete v podrobné dokumentaci.

## <a name="application-insights"></a>Application Insights
Application Insights je rozšiřitelná služba pro správu výkonu aplikací (APM). Je určena webovým vývojářům na více platformách. Slouží k monitorování živé webové aplikace. Funguje pro aplikace na široké škále platforem, včetně .NET, Node.js a Java EE, hostované v místním prostředí, hybridní nebo jakýkoli veřejný cloud. Integruje se také s procesem DevOps a má spojovací body k různým vývojovým nástrojům.

Podívejte [se, co jsou přehledy aplikací?](../app/app-insights-overview.md).

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>Azure Monitor pro kontejnery
Azure Monitor pro kontejnery monitoruje výkon úloh kontejnerů nasazených buď do instancí kontejnerů Azure, nebo do spravovaných clusterů Kubernetes hostovaných ve službě Azure Kubernetes Service (AKS). Monitorování kontejnerů je důležité, zejména když používáte produkční cluster ve velkém měřítku s více aplikacemi.

Viz [Azure Monitor pro kontejnery přehled](../insights/container-insights-overview.md).

![Azure Monitor pro kontejnery](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>Azure Monitor pro skupiny prostředků (preview)
Azure Monitor pro skupiny prostředků pomáhá třídění a diagnostikovat všechny problémy, které vaše jednotlivé prostředky narazí, a zároveň nabízí kontext, pokud jde o stav a výkon skupiny prostředků jako celku.

Viz [Monitorování skupin prostředků pomocí Azure Monitoru (preview).](../insights/resource-group-insights.md)

![Azure Monitor pro skupiny prostředků](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>Azure Monitor pro virtuální počítače (preview)
Azure Monitor pro virtuální počítače monitoruje vaše virtuální počítače Azure (VM) a škálovací sady virtuálních počítačů ve velkém měřítku. Analyzuje výkon a stav virtuálních počítačů s Windows a Linuxem a monitoruje jejich procesy a závislosti na jiných prostředcích a externích procesech.

Podívejte se, [co je Azure Monitor pro virtuální počítače?](vminsights-overview.md)

![Azure Monitor pro virtuální počítače](media/insights-overview/vm-insights.png)

## <a name="azure-monitor-for-networks-preview"></a>Azure Monitor pro sítě (preview)
[Azure Monitor pro sítě](network-insights-overview.md) poskytuje komplexní přehled o stavu a metriky pro všechny síťové prostředky. Rozšířené vyhledávací funkce vám pomohou identifikovat závislosti prostředků a umožní vám scénáře, jako je identifikace prostředků, které hostují váš web, pouhým vyhledáním názvu webu.

![Azure Monitor pro sítě](media/insights-overview/network-insights.png)

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [datové platformě Azure Monitor](../platform/data-platform.md) unavované přehledy.
* Seznamte se s různými [zdroji dat používanými službou Azure Monitor](../platform/data-sources.md) a o různých typech dat shromážděných jednotlivými přehledy.
