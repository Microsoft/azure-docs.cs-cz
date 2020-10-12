---
title: Přehled přehledů v Azure Monitor | Microsoft Docs
description: Přehledy poskytují přizpůsobené možnosti monitorování v Azure Monitor pro konkrétní aplikace a služby. Tento článek poskytuje stručný popis každého z dostupných přehledů, které jsou aktuálně k dispozici.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/22/2019
ms.openlocfilehash: e3eefb97c85b3865abbf116b9912dc45f6db8ce8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87326864"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Přehled přehledů v Azure Monitor
Přehledy poskytují přizpůsobené možnosti monitorování pro konkrétní aplikace a služby. Ukládají data na [Azure monitor datovou platformu](../platform/data-platform.md) a využívají jiné funkce Azure monitor k analýze a upozorňování, ale mohou shromažďovat další data a poskytovat jedinečné uživatelské prostředí v Azure Portal. Přístup k přehledům z části **Přehled** v nabídce Azure Monitor v Azure Portal.


V následujících částech najdete stručný popis přehledů, které jsou aktuálně k dispozici v Azure Monitor. Podrobnosti najdete v podrobné dokumentaci.

## <a name="application-insights"></a>Application Insights
Application Insights je rozšiřitelná služba pro správu výkonu aplikací (APM). Je určena webovým vývojářům na více platformách. Slouží k monitorování živé webové aplikace. Funguje pro aplikace na nejrůznějších platformách, jako je .NET, Node.js a Java EE, hostované místně, hybridním nebo jakýmkoli veřejným cloudem. Integruje se také s vaším procesem DevOps a má spojovací body pro celou řadu vývojářských nástrojů.

Podívejte [se, co je Application Insights?](../app/app-insights-overview.md).

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>Azure Monitor pro kontejnery
Azure Monitor pro kontejnery monitoruje výkon úloh kontejneru nasazených v Azure Container Instances nebo spravovaných clusterech Kubernetes hostovaných ve službě Azure Kubernetes Service (AKS). Monitorování vašich kontejnerů je důležité, zejména pokud používáte produkční cluster ve velkém měřítku s více aplikacemi.

[Přehled kontejnerů najdete v tématu Azure monitor for Containers](./container-insights-overview.md).

![Azure Monitor pro kontejnery](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>Azure Monitor pro skupiny prostředků (Preview)
Azure Monitor pro skupiny prostředků pomáhá roztřídit a diagnostikovat všechny problémy, ke kterým dojde, a současně nabízí kontext pro stav a výkon skupiny prostředků jako celku.

Viz téma [monitorování skupin prostředků pomocí Azure monitor (Preview)](./resource-group-insights.md).

![Azure Monitor pro skupiny prostředků](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>Azure Monitor pro virtuální počítače (Preview)
Azure Monitor pro virtuální počítače monitoruje virtuální počítače Azure a škálovací sady virtuálních počítačů ve velkém měřítku. Analyzuje výkon a stav virtuálních počítačů s Windows a Linuxem a monitoruje jejich procesy a závislosti na jiných prostředcích a externích procesech.

Podívejte [se, co je Azure monitor pro virtuální počítače?](vminsights-overview.md)

![Azure Monitor pro virtuální počítače](media/insights-overview/vm-insights.png)

## <a name="azure-monitor-for-networks-preview"></a>Azure Monitor pro sítě (Preview)
[Azure monitor pro sítě](network-insights-overview.md) poskytuje komplexní přehled o stavu a metrikách pro všechny síťové prostředky. Rozšířené možnosti vyhledávání vám pomůžou identifikovat závislosti prostředků a povolit scénáře jako identifikaci prostředků, které hostují váš web, jednoduše hledáním názvu vašeho webu.

![Azure Monitor pro sítě](media/insights-overview/network-insights.png)

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [Azure monitor datovou platformu](../platform/data-platform.md) , kterou využívají přehledy.
* Přečtěte si o různých [zdrojích dat používaných Azure monitor](../platform/data-sources.md) a různých druzích dat shromažďovaných jednotlivými poznatky.

