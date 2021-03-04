---
title: Změna analýzy v nástroji VM Insights
description: Integrace se službou Virtual Insights a integrací analýzy změn aplikací vám umožní zobrazit všechny změny provedené ve virtuálním počítači, který může mít vliv na jeho výkon.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/23/2020
ms.openlocfilehash: 48f0f0e124040dc070bd5e31f956f75e759303d3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704000"
---
# <a name="change-analysis-in-vm-insights"></a>Změna analýzy v nástroji VM Insights
Integrace se službou Virtual Insights a integrací [analýzy změn aplikací](../app/change-analysis.md) vám umožní zobrazit všechny změny provedené ve virtuálním počítači, který může mít vliv na jeho výkon.

## <a name="overview"></a>Přehled
Předpokládejme, že máte virtuální počítač, který běží pomalu, a chcete zjistit, jestli jeho výkon ovlivnila poslední změny konfigurace. Prohlédněte si výkon virtuálního počítače pomocí přehledů virtuálních počítačů a zjistíte, že během poslední hodiny se zvýšilo využití paměti. Změna analýz vám pomůže určit, zda byly v průběhu tohoto času provedeny změny konfigurace, které by mohly být příčinou tohoto zvýšení.

Služba Analysis Services pro změny aplikace agreguje změny z [Azure Resource graphu](../../governance/resource-graph/how-to/get-resource-changes.md) i vnořené změny vlastností, jako jsou například pravidla zabezpečení sítě z Azure Resource Manager. 

## <a name="enabling-change-analysis"></a>Povolení analýzy změn
Pokud chcete připojit analýzu změn v rámci virtuálních počítačů Insights, musíte zaregistrovat poskytovatele prostředků *Microsoft. ChangeAnalysis* . Při prvním spuštění analýzy virtuálních počítačů nebo analýz pro změny aplikace v Azure Portal se tento poskytovatel prostředků automaticky zaregistruje za vás. Analýza změn aplikace je bezplatná služba, která nemá žádné nároky na výkon u prostředků.

## <a name="view-change-analysis"></a>Zobrazit analýzu změn
Analýza změn je k dispozici na kartě **výkon** nebo **Mapa** ve službě VM Insights výběrem možnosti **změnit** . 

[![Prozkoumat změny](media/vminsights-change-analysis/investigate-changes-screenshot.png)](media/vminsights-change-analysis/investigate-changes-screenshot-zoom.png#lightbox)


Kliknutím na tlačítko **prozkoumat změny** spustíte stránku analýzy změny aplikace filtrované pro daný virtuální počítač. Můžete si prohlédnout uvedené změny a zjistit, jestli existují nějaké problémy, které by mohly být příčinou problému. Pokud si nejste jistí konkrétní změny, můžete na sloupec změnit na **základě změny** určit osobu, která změnu provedla.

[![Změnit podrobnosti](media/vminsights-change-analysis/change-details-screenshot.png)](media/vminsights-change-analysis/change-details-screenshot.png#lightbox)

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [analýze změn aplikace](../app/change-analysis.md).
- Přečtěte si další informace o [Azure Resource graphu](../../governance/resource-graph/how-to/get-resource-changes.md). 

