---
title: Návrhář zobrazení Azure Monitor u souhrnu konverzí sešitů a přístupu
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: fb53a966b0dc4959253ac0786ef09ef3c497e809
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658842"
---
# <a name="view-designer-to-workbooks-conversion-summary-and-access"></a>Zobrazit souhrn konverzí a přístup k návrháři do sešitů
[Návrhář zobrazení](view-designer.md) je funkce Azure Monitoru, která umožňuje vytvářet vlastní zobrazení, která vám pomohou vizualizovat data v pracovním prostoru Log Analytics pomocí grafů, seznamů a časových os. Jsou postupně ukončovány a nahrazovány sešity, které poskytují další funkce. Tento článek podrobně popisuje, jak můžete vytvořit souhrn přehledu a oprávnění potřebná pro přístup k sešitům.

## <a name="creating-your-workspace-summary-from-azure-dashboard"></a>Vytvoření souhrnu pracovního prostoru z ovládacího panelu Azure
Uživatelé návrháře zobrazení mohou být obeznámeni s dlaždicí přehled, která představuje sadu zobrazení. Chcete-li zachovat vizuální přehled, jako je souhrn pracovního prostoru návrháře zobrazení, sešity nabízejí připnuté kroky, které se dá připnout na [řídicí panel portálu Azure](../../azure-portal/azure-portal-dashboards.md). Stejně jako dlaždice přehledu v souhrnu pracovního prostoru budou připnuté položky sešitu propojeny přímo se zobrazením sešitu.

Můžete využít vysoké úrovně funkcí přizpůsobení poskytovaných s řídicími panely Azure, které umožňují automatickou aktualizaci, přesunutí, velikost a další filtrování pro připnuté položky a vizualizace. 

![Řídicí panel](media/view-designer-conversion-access/dashboard.png)

Vytvořte nový řídicí panel Azure nebo vyberte existující řídicí panel, který začne připnout položky sešitů.

Chcete-li připnout jednotlivé položky, budete muset povolit ikonu pinu pro váš konkrétní krok. Chcete-li tak učinit, vyberte odpovídající tlačítko **Upravit** pro váš krok a pak vyberte ikonu ozubeného kola a otevřete **upřesnit nastavení**. Zaškrtněte možnost **Vždy zobrazit ikonu špendlíku v tomto kroku**a v pravém horním rohu kroku se zobrazí ikona špendlíku. Tento pin umožňuje připnout konkrétní vizualizace k řídicímu panelu, například dlaždice s přehledem.

![Krok připnutí](media/view-designer-conversion-access/pin-step.png)


Můžete také chtít připnout více vizualizací ze sešitu nebo celého obsahu sešitu na řídicí panel. Chcete-li připnout celý sešit, vyberte **Upravit** v horním panelu nástrojů a přepněte **režim úprav**. Zobrazí se ikona špendlíku, která vám umožní připnout celou položku sešitu nebo všechny jednotlivé kroky a vizualizace v sešitu.

![Připnout vše](media/view-designer-conversion-access/pin-all.png)



## <a name="sharing-and-viewing-permissions"></a>Oprávnění ke sdílení a zobrazení 
Sešity mají tu výhodu, že jsou soukromým nebo sdíleným dokumentem. Ve výchozím nastavení budou uložené sešity uloženy v části **Moje sestavy**, což znamená, že tento sešit může zobrazit pouze tvůrce.

Sešity můžete sdílet tak, že v **režimu úprav**vyberete ikonu **Sdílet** z horního panelu nástrojů . Budete vyzváni k přesunutí sešitu do **sdílených sestav**, který vygeneruje odkaz, který poskytuje přímý přístup k sešitu.

Aby mohl uživatel zobrazit sdílený sešit, musí mít přístup k předplatnému i skupině prostředků, pod kterou je sešit uložen.

![Přístup založený na předplatném](media/view-designer-conversion-access/subscription-access.png)

## <a name="next-steps"></a>Další kroky

- [Běžné úkoly](view-designer-conversion-tasks.md)