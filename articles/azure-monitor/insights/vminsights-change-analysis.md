---
title: Změna analýzy v Azure Monitor pro virtuální počítače
description: Azure Monitor pro virtuální počítače integrace s integrací analýzy změn aplikace umožňuje zobrazit všechny změny provedené ve virtuálním počítači, který může mít vliv na výkon IT.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/23/2020
ms.openlocfilehash: 59799a09436d5968a441f6f17655d3138a2d84d8
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711337"
---
# <a name="change-analysis-in-azure-monitor-for-vms"></a>Změna analýzy v Azure Monitor pro virtuální počítače
Azure Monitor pro virtuální počítače integrace s integrací [analýzy změn aplikace](../app/change-analysis.md) umožňuje zobrazit všechny změny provedené ve virtuálním počítači, který může mít vliv na výkon IT.

## <a name="overview"></a>Přehled
Předpokládejme, že máte virtuální počítač, který běží pomalu, a chcete zjistit, jestli jeho výkon ovlivnila poslední změny konfigurace. Výkon virtuálního počítače můžete zobrazit pomocí Azure Monitor pro virtuální počítače a zjistíte, že během poslední hodiny se zvýšilo využití paměti. Změna analýz vám pomůže určit, zda byly v průběhu tohoto času provedeny změny konfigurace, které by mohly být příčinou tohoto zvýšení.

Služba Analysis Services pro změny aplikace agreguje změny z [Azure Resource graphu](../../governance/resource-graph/how-to/get-resource-changes.md) i vnořené změny vlastností, jako jsou například pravidla zabezpečení sítě z Azure Resource Manager. 

## <a name="enabling-change-analysis"></a>Povolení analýzy změn
Chcete-li připojit analýzu změn v Azure Monitor pro virtuální počítače, je nutné zaregistrovat poskytovatele prostředků *Microsoft. ChangeAnalysis* . Při prvním spuštění Azure Monitor pro virtuální počítače nebo analýzy změn aplikace v Azure Portal bude tento poskytovatel prostředků automaticky zaregistrován. Analýza změn aplikace je bezplatná služba, která nemá žádné nároky na výkon u prostředků.

## <a name="view-change-analysis"></a>Zobrazit analýzu změn
Analýza změn je k dispozici na kartě **výkon** nebo **Mapa** Azure monitor pro virtuální počítače výběrem možnosti **změnit** . 

[![Prozkoumat změny](media/vminsights-change-analysis/investigate-changes-screenshot.png)](media/vminsights-change-analysis/investigate-changes-screenshot-zoom.png#lightbox)


Kliknutím na tlačítko **prozkoumat změny** spustíte stránku analýzy změny aplikace filtrované pro daný virtuální počítač. Můžete si prohlédnout uvedené změny a zjistit, jestli existují nějaké problémy, které by mohly být příčinou problému. Pokud si nejste jistí konkrétní změny, můžete na sloupec změnit na **základě změny** určit osobu, která změnu provedla.

[![Změnit podrobnosti](media/vminsights-change-analysis/change-details-screenshot.png)](media/vminsights-change-analysis/change-details-screenshot.png#lightbox)

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [analýze změn aplikace](../app/change-analysis.md).
- Přečtěte si další informace o [Azure Resource graphu](../../governance/resource-graph/how-to/get-resource-changes.md). 

