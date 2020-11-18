---
title: Co je Azure Monitor pro virtuální počítače?
description: Přehled Azure Monitor pro virtuální počítače, který monitoruje stav a výkon virtuálních počítačů Azure a automaticky zjišťuje a mapuje součásti aplikací a jejich závislosti.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2020
ms.openlocfilehash: e5eaf2d7075ca09aeb3cfaa2dfea81fd0f8d65ad
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685303"
---
# <a name="overview-of-azure-monitor-for-vms"></a>Přehled služby Azure Monitor pro virtuální počítače

Azure Monitor pro virtuální počítače monitoruje výkon a stav virtuálních počítačů a sady škálování virtuálních počítačů, včetně jejich spuštěných procesů a závislostí na jiných prostředcích. Může přispět k předvídatelnému výkonu a dostupnosti důležitých aplikací tím, že identifikují problémová místa výkonu a problémy se sítí a také vám pomůže pochopit, jestli problém souvisí s jinými závislostmi.

Azure Monitor pro virtuální počítače podporuje operační systémy Windows a Linux v následujících systémech:

- Virtuální počítače Azure
- Škálovací sady virtuálních počítačů Azure
- Hybridní virtuální počítače připojené pomocí ARC Azure
- Místní virtuální počítače
- Virtuální počítače hostované v jiném cloudovém prostředí
  

Azure Monitor pro virtuální počítače ukládá data v protokolech Azure Monitor, což umožňuje poskytovat výkonnou agregaci a filtrování a analyzovat trendy dat v čase. Tato data můžete zobrazit v jednom virtuálním počítači přímo z virtuálního počítače nebo můžete použít Azure Monitor pro doručení agregovaného zobrazení více virtuálních počítačů.

![Perspektiva analýzy virtuálních počítačů v Azure Portal](media/vminsights-overview/vminsights-azmon-directvm.png)


## <a name="pricing"></a>Ceny
Pro Azure Monitor pro virtuální počítače se neúčtují žádné přímé náklady, ale v pracovním prostoru Log Analytics se vám účtuje jeho aktivita. Na základě cen, které jsou publikovány na [stránce s cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/), se Azure monitor pro virtuální počítače účtuje takto:

- Data ingestovaná z agentů a uložená v pracovním prostoru.
- Data o stavu shromážděná z stavu hosta (Preview)
- Pravidla výstrah na základě dat protokolů a stavu.
- Oznámení se odesílají z pravidel výstrah.

Velikost protokolu se liší podle délky řetězců čítačů výkonu a může se zvýšit počtem logických disků a síťových adaptérů, které jsou přiděleny virtuálnímu počítači. Pokud již používáte Service Map, zobrazí se jediná data o výkonu, která se odesílají do `InsightsMetrics` datového typu Azure monitor.


## <a name="configuring-azure-monitor-for-vms"></a>Konfigurace Azure Monitor pro virtuální počítače
Postup konfigurace Azure Monitor pro virtuální počítače je následující. Podrobné pokyny k jednotlivým krokům najdete v jednotlivých odkazech:

- [Vytvořte Log Analytics pracovní prostor.](vminsights-configure-workspace.md#create-log-analytics-workspace)
- [Přidejte řešení VMInsights do pracovního prostoru.](vminsights-configure-workspace.md#add-vminsights-solution-to-workspace)
- [Nainstalujte agenty na virtuálním počítači a sadu škálování virtuálního počítače, která se má monitorovat.](vminsights-enable-overview.md)



## <a name="next-steps"></a>Další kroky

- Požadavky a metody, které umožňují monitorovat virtuální počítače, najdete v tématu [nasazení Azure monitor pro virtuální počítače](vminsights-enable-overview.md) .

