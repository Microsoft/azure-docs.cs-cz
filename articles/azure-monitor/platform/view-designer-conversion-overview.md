---
title: Průvodce přechodem zobrazení Azure Monitor u sešitů
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 234da921b4f0d1243ca8cfdb12ba2d851db2b43f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658689"
---
# <a name="azure-monitor-view-designer-to-workbooks-transition-guide"></a>Průvodce přechodem zobrazení Azure Monitor u sešitů
[Návrhář zobrazení](view-designer.md) je funkce Azure Monitoru, která umožňuje vytvářet vlastní zobrazení, která vám pomohou vizualizovat data v pracovním prostoru Log Analytics pomocí grafů, seznamů a časových os. Jsou postupně ukončovány a nahrazovány sešity, které poskytují další funkce. Tento článek obsahuje přehled procesu převodu existujících zobrazení na sešity.

## <a name="workbooks-overview"></a>Přehled sešitů
[Sešity kombinují](../insights/vminsights-workbooks.md) text, [dotazy protokolu](../log-query/query-language.md), metriky a parametry do bohatých interaktivních sestav. Členové týmu se stejným přístupem k prostředkům Azure jsou taky moct upravovat sešity.

Sešity jsou užitečné pro scénáře, jako jsou:

-   Zkoumání využití virtuálního počítače, když neznáte metriky zájmu předem: využití procesoru, místo na disku, paměť, síťové závislosti atd. Na rozdíl od jiných nástrojů pro analýzu využití umožňují sešity kombinovat různé druhy vizualizací a analýz, což je činí skvělými pro tento druh průzkumu volného tvaru.
-   Vysvětlení týmu, jak si nedávno zřízený virtuální počítač vede, zobrazením metrik pro čítače klíčů a další události protokolu.
-   Sdílení výsledků experimentu pro změna velikosti virtuálního počítače s ostatními členy vašeho týmu. Můžete vysvětlit cíle experimentu s textem a pak zobrazit jednotlivé metriky využití a analytické dotazy použité k vyhodnocení experimentu spolu s jasnými popisky pro to, zda byla každá metrika nad nebo pod cílem.
-   Nahlášení dopadu výpadku na využití virtuálního počítače, kombinování dat, vysvětlení textu a diskuse o dalších krocích, jak zabránit výpadkům v budoucnu.


## <a name="why-convert-view-designer-dashboards-to-workbooks"></a>Proč převádět řídicí panely návrháře zobrazení na sešity?

Návrhář zobrazení nabízí možnost generovat různá zobrazení a vizualizace založené na dotazech. Řada kustomizací na nejvyšší úrovni ale zůstává omezená, například formátování mřížek a rozložení dlaždic nebo výběr alternativních grafických prvků pro reprezentaci vašich dat. Návrhář zobrazení je omezen na celkem devět odlišných dlaždic, které představují vaše data.

Platforma Workbooks umožňuje plně využít potenciál vašich dat. sešity si nejen uchovávají všechny možnosti, ale také podporují další funkce prostřednictvím textu, metrik, parametrů a mnoho dalšího. Sešity například umožňují uživatelům konsolidovat husté mřížky a přidat vyhledávací panely pro snadné filtrování a analýzu dat. 

### <a name="advantages-of-using-workbooks-over-view-designer"></a>Výhody použití sešitů přes Návrhář zobrazení

* Podporuje protokoly i metriky.
* Umožňuje osobní zobrazení pro individuální řízení přístupu i zobrazení sdílených sešitů.
* Vlastní možnosti rozložení s ovládacími kartami, změnami velikosti a změnami.
* Podpora pro dotazování na příčce ve více pracovních prostorech Analýzy protokolů, aplikacích Application Insights a předplatných.
* Umožňuje vlastní parametry, které dynamicky aktualizují přidružené grafy a vizualizace.
* Podpora galerie šablon z veřejného GitHubu.

Zatímco tato příručka nabízí jednoduché kroky k přímému vytvoření několika běžně používaných zobrazení návrháře zobrazení, sešity umožňují uživatelům mít svobodu vytvářet a navrhovat vlastní vizualizace a metriky. Následující snímek obrazovky je ze [šablony využití pracovního prostoru](https://go.microsoft.com/fwlink/?linkid=874159&resourceId=Azure%20Monitor&featureName=Workbooks&itemId=community-Workbooks%2FAzure%20Monitor%20-%20Workspaces%2FWorkspace%20Usage&workbookTemplateName=Workspace%20Usage&func=NavigateToPortalFeature&type=workbook) a ukazuje příklad toho, jaké sešity jsou schopné vytvářet:


![Příklad aplikace sešitů](media/view-designer-conversion-overview/workbook-template-example.jpg)


## <a name="how-to-start-using-workbooks"></a>Jak začít používat sešity
Otevřené sešity ze sešitů jsou povolené v pracovních prostorech Log Analytics jako položka na bočním navigačním panelu přímo pod umístěním návrháře zobrazení.

![Navigace v sešitech](media/view-designer-conversion-overview/workbooks-nav.png)

Po výběru se zobrazí galerie se seznamem všech uložených sešitů a šablon pro váš pracovní prostor.

![Galerie sešitů](media/view-designer-conversion-overview/workbooks-gallery.png)

Chcete-li spustit nový sešit, můžete vybrat šablonu **Prázdný** v části **Rychlý start**nebo ikonu **Nový** na horním navigačním panelu. Chcete-li zobrazit šablony nebo se vrátit k uloženým sešitům, vyberte položku z galerie nebo vyhledejte název na panelu hledání.

Chcete-li uložit sešit, budete muset sestavu uložit s určitým názvem, předplatným, skupinou prostředků a umístěním.
Sešit se automaticky vyplní do stejného nastavení jako pracovní prostor LA se stejným předplatným a skupinou prostředků, ale uživatelé mohou tato nastavení sestavy změnit. Sešity jsou ve výchozím nastavení uloženy do *složce Moje sestavy*, přístupné pouze jednotlivým uživatelům. Mohou být také uloženy přímo do sdílených sestav nebo sdíleny později.

![Uložení sešitů](media/view-designer-conversion-overview/workbooks-save.png)

## <a name="next-steps"></a>Další kroky

- [Možnosti převodu](view-designer-conversion-options.md)
