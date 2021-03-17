---
title: Průvodce přechodem Azure Monitor návrháře zobrazení na sešity
description: Přechod ze zobrazení do sešitů v Azure Monitor.
author: austonli
ms.author: aul
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 19de2a3d3f5a998c09db7c4df689b0378e2597fd
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043367"
---
# <a name="azure-monitor-view-designer-to-workbooks-transition-guide"></a>Průvodce přechodem Azure Monitor návrháře zobrazení na sešity
[Návrhář zobrazení](view-designer.md) je funkce Azure monitor, která umožňuje vytvářet vlastní zobrazení, která vám pomohou vizualizovat data v pracovním prostoru Log Analytics s grafy, seznamy a časovými osami. Přecházejí na sešity, aby poskytovaly flexibilní plátno pro analýzu dat a vytváření bohatých vizuálních sestav v rámci Azure Portal. Tento článek vám pomůže udělat přechod z návrháře zobrazení do sešitů. 


## <a name="workbooks-overview"></a>Přehled sešitů
[Sešity](../vm/vminsights-workbooks.md) kombinují text, [dotazy protokolů](/azure/data-explorer/kusto/query/), metriky a parametry do propracovaných interaktivních sestav. Členové týmu, kteří mají stejný přístup k prostředkům Azure, můžou upravovat i sešity.

Sešity jsou užitečné pro scénáře, jako například:

-   Prozkoumejte využití virtuálního počítače, když neznáte metriky, které vás zajímají předem: využití procesoru, místo na disku, paměť, závislosti sítě atd. Na rozdíl od jiných analytických nástrojů se vám sešity můžou kombinovat s několika různými typy vizualizací a analýz, takže jsou skvělé pro tento druh průzkumu volného tvaru.
-   Zobrazením metrik pro klíčové čítače a další události protokolu Vysvětlete vašemu týmu, jak je prováděn nedávno zřízený virtuální počítač.
-   Sdílení výsledků experimentu změny velikosti virtuálního počítače s ostatními členy týmu. Můžete vysvětlit cíle experimentu s textem a pak zobrazit jednotlivé metriky využití a analytické dotazy použité k vyhodnocení experimentu spolu s jasnými voláními pro skutečnost, zda byla každá metrika nad nebo pod cílem.
-   Oznamujeme dopad výpadku při používání vašeho virtuálního počítače, kombinování dat, vysvětlení textu a diskuzi o dalších krocích, aby se předešlo výpadkům v budoucnosti.


## <a name="why-convert-view-designer-dashboards-to-workbooks"></a>Proč převést řídicí panely návrháře zobrazení na sešity?

Návrhář zobrazení nabízí možnost generování různých zobrazení a vizualizací založených na dotazech. Řada kustomizací na nejvyšší úrovni ale zůstává omezená, například formátování mřížek a rozložení dlaždic nebo výběr alternativních grafických prvků pro reprezentaci vašich dat. Návrhář zobrazení je omezen na celkem devět samostatných dlaždic, které reprezentují vaše data.

Platforma Workbooks umožňuje plně využít potenciál vašich dat. sešity neuchovávají pouze všechny funkce, ale také podporují další funkce přes text, metriky, parametry a mnoho dalších. Například pracovní sešity umožňují uživatelům konsolidovat zhuštěné mřížky a přidat panely hledání, aby data mohli snadno filtrovat a analyzovat. 

### <a name="advantages-of-using-workbooks-over-view-designer"></a>Výhody používání sešitů v Návrháři zobrazení

* Podporuje protokoly i metriky.
* Povoluje jak osobní zobrazení pro jednotlivé zobrazení řízení přístupu, tak pro sdílené sešity.
* Možnosti vlastního rozložení s ovládacími prvky tabulátory, velikosti a velikosti
* Podpora pro dotazování napříč několika pracovními prostory Log Analytics, Application Insights aplikací a předplatnými.
* Povolí vlastní parametry, které dynamicky aktualizují přidružené grafy a vizualizace.
* Podpora galerie šablon z veřejného GitHubu.

I když tato příručka nabízí jednoduché kroky pro přímé opětovné vytvoření několika běžně používaných zobrazení návrháře zobrazení, sešity umožňují uživatelům vytvářet a navrhovat libovolné vlastní vizualizace a metriky. Následující snímek obrazovky je ze [šablony použití pracovního prostoru](https://go.microsoft.com/fwlink/?linkid=874159&resourceId=Azure%20Monitor&featureName=Workbooks&itemId=community-Workbooks%2FAzure%20Monitor%20-%20Workspaces%2FWorkspace%20Usage&workbookTemplateName=Workspace%20Usage&func=NavigateToPortalFeature&type=workbook) a zobrazuje příklad, které sešity umožňují vytvořit:


![Příklad aplikace sešity](media/view-designer-conversion-overview/workbook-template-example.jpg)


## <a name="how-to-start-using-workbooks"></a>Jak začít používat sešity
Otevřené sešity ze sešitů jsou povolené v Log Analytics pracovní prostory jako položka na bočním navigačním panelu, a to přímo v umístění návrháře zobrazení.

![Navigace v sešitech](media/view-designer-conversion-overview/workbooks-nav.png)

Po výběru se zobrazí Galerie obsahující všechny uložené sešity a šablony pro váš pracovní prostor.

![Galerie sešitů](media/view-designer-conversion-overview/workbooks-gallery.png)

Chcete-li spustit nový sešit, můžete vybrat možnost **prázdná** šablona v části **rychlý Start** nebo **Nová** ikona v horním navigačním panelu. Chcete-li zobrazit šablony nebo se vrátit k uloženým sešitům, vyberte položku z Galerie nebo vyhledejte název na panelu hledání.

Chcete-li sešit uložit, je nutné sestavu uložit s konkrétním názvem, předplatným, skupinou prostředků a umístěním.
Sešit bude v rámci automatického vyplňování stejného nastavení jako pracovní prostor LA stejný jako u stejného předplatného, skupiny prostředků, ale uživatelé můžou tato nastavení sestavy změnit. Sešity jsou ve výchozím nastavení uloženy do *Mé sestavy* přístupné pouze individuálnímu uživateli. Můžete je také uložit přímo do sdílených sestav nebo je sdílet později.

![Uložit sešity](media/view-designer-conversion-overview/workbooks-save.png)

## <a name="next-steps"></a>Další kroky

- [Možnosti převodu](view-designer-conversion-options.md)