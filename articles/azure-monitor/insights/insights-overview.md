---
title: Přehled Insights ve službě Azure Monitor | Dokumentace Microsoftu
description: Insights poskytuje přizpůsobené monitorování prostředí ve službě Azure Monitor pro konkrétní aplikace a služby. Tento článek obsahuje stručný popis přehledy, které jsou aktuálně k dispozici.
services: azure-monitor
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: bwren
ms.openlocfilehash: 8cb39a174c570b7019e872d731f49252a9505406
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247226"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Přehled Insights ve službě Azure Monitor
Insights poskytovat přizpůsobené monitorování pro konkrétní aplikace a služby. Ukládají data [datová Platforma Azure Monitor](../platform/data-platform.md) a využívat další funkce Azure monitoru pro analýzu a výstrahy, ale může shromažďovat další data a poskytovat jedinečné uživatelské prostředí na webu Azure Portal. Přístup k přehledům z **Insights** části Azure Monitor nabídky na webu Azure Portal.

Následující části obsahují stručný popis přehledy, které jsou aktuálně k dispozici ve službě Azure Monitor. Na každém najdete v podrobné dokumentaci pro podrobnosti.

## <a name="application-insights"></a>Application Insights
Application Insights je rozšiřitelná služba pro správu výkonu aplikací (APM). Je určena webovým vývojářům na více platformách. Slouží k monitorování živé webové aplikace. Funguje na nejrůznějších platformách, včetně .NET, Node.js a Java EE pro aplikace hostované v místním, hybridním nebo jakémkoli veřejném cloudu. Taky se integruje s vaším procesem DevOps a má spojovací body s celou řadu nástrojů pro vývoj.

Zobrazit [co je Application Insights?](../app/app-insights-overview.md).

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>Azure Monitor for Containers
Azure Monitor pro monitorování kontejnerů výkon úloh kontejneru nasadit buď Azure Container Instances nebo spravovat clustery Kubernetes hostované ve službě Azure Kubernetes Service (AKS). Monitorování kontejnerů je důležité, zejména v případě, že spouštíte produkční cluster ve velkém měřítku, s několika aplikacemi.

Zobrazit [monitorování Azure pro kontejnery – přehled](../insights/container-insights-overview.md).

![Azure Monitor for Containers](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>Azure Monitor pro skupiny prostředků (preview)
Azure Monitor pro skupiny prostředků pomáhá posuzovat a diagnostikovat všechny problémy, které jednotlivé prostředky nastat současně nabízet kontext v souvislosti s stav a výkon skupiny prostředků jako celek.

Zobrazit [sledování skupiny prostředků pomocí Azure monitoru (preview)](../insights/resource-group-insights.md).

![Azure Monitor pro skupiny prostředků](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>Azure Monitor pro virtuální počítače (preview)
Azure Monitor pro virtuální počítače monitoruje virtuální počítače Azure (VM) a škálovacích sad virtuálních počítačů ve velkém měřítku. Analyzuje výkon a stav virtuálních počítačů s Windows a Linuxem a monitoruje jejich procesy a závislosti na jiných prostředcích a externích procesech.

Zobrazit [co je Azure Monitor pro virtuální počítače?](vminsights-overview.md)

![Azure Monitor pro virtuální počítače](media/insights-overview/vm-insights.png)

## <a name="next-steps"></a>Další postup
* Další informace o [datová Platforma Azure Monitor](../platform/data-platform.md) využívají přehledy.
* Další informace o různých [zdroje dat používané službou Azure Monitor](../platform/data-sources.md) a různé druhy dat shromážděných jednotlivými přehledy.
