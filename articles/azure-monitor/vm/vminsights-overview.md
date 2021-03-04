---
title: Co je to přehledy virtuálních počítačů?
description: Přehled služby VM Insights, která monitoruje stav a výkon virtuálních počítačů Azure a automaticky zjišťuje a mapuje součásti aplikací a jejich závislosti.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2020
ms.openlocfilehash: 967ff9672f55af3e9c5d2e7c3c3dbc7d2ff6cb3c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101707366"
---
# <a name="overview-of-vm-insights"></a>Přehled nástroje VM Insights

Cloud Insights monitoruje výkon a stav virtuálních počítačů a virtuálních počítačů, včetně jejich spuštěných procesů a závislostí na jiných prostředcích. Může přispět k předvídatelnému výkonu a dostupnosti důležitých aplikací tím, že identifikují problémová místa výkonu a problémy se sítí a také vám pomůže pochopit, jestli problém souvisí s jinými závislostmi.

Přehledy virtuálních počítačů podporují operační systémy Windows a Linux na těchto počítačích:

- Virtuální počítače Azure
- Škálovací sady virtuálních počítačů Azure
- Hybridní virtuální počítače připojené pomocí ARC Azure
- Místní virtuální počítače
- Virtuální počítače hostované v jiném cloudovém prostředí
  

Cloud Insights ukládá svá data do protokolů Azure Monitor, což umožňuje poskytovat výkonnou agregaci a filtrování a analyzovat trendy dat v čase. Tato data můžete zobrazit v jednom virtuálním počítači přímo z virtuálního počítače nebo můžete použít Azure Monitor pro doručení agregovaného zobrazení více virtuálních počítačů.

![Perspektiva analýzy virtuálních počítačů v Azure Portal](media/vminsights-overview/vminsights-azmon-directvm.png)


## <a name="pricing"></a>Ceny
Pro virtuální počítače nejsou k dispozici žádné přímé náklady, ale za jeho aktivitu se účtuje v pracovním prostoru Log Analytics. V závislosti na cenách, které jsou publikovány na [stránce s cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/), se pro virtuální počítač Insights účtují tyto informace:

- Data ingestovaná z agentů a uložená v pracovním prostoru.
- Data o stavu shromážděná z stavu hosta (Preview)
- Pravidla výstrah na základě dat protokolů a stavu.
- Oznámení se odesílají z pravidel výstrah.

Velikost protokolu se liší podle délky řetězců čítačů výkonu a může se zvýšit počtem logických disků a síťových adaptérů, které jsou přiděleny virtuálnímu počítači. Pokud již používáte Service Map, zobrazí se jediná data o výkonu, která se odesílají do `InsightsMetrics` datového typu Azure monitor.


## <a name="configuring-vm-insights"></a>Konfigurace Cloud Insights
Postup konfigurace přehledů virtuálních počítačů je následující. Podrobné pokyny k jednotlivým krokům najdete v jednotlivých odkazech:

- [Vytvořte Log Analytics pracovní prostor.](./vminsights-configure-workspace.md#create-log-analytics-workspace)
- [Přidejte řešení VMInsights do pracovního prostoru.](./vminsights-configure-workspace.md#add-vminsights-solution-to-workspace)
- [Nainstalujte agenty na virtuálním počítači a sadu škálování virtuálního počítače, která se má monitorovat.](./vminsights-enable-overview.md)



## <a name="next-steps"></a>Další kroky

- Další požadavky a metody, které umožňují monitorovat virtuální počítače, najdete v tématu [nasazení přehledů virtuálních počítačů](./vminsights-enable-overview.md) .
