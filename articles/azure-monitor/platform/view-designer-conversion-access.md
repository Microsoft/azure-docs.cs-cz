---
title: Souhrn a přístup návrháře zobrazení Azure Monitor pro sešity
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 7614dd1d60bad5c124269ae2af02d30a5aacfe3e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90564028"
---
# <a name="view-designer-to-workbooks-conversion-summary-and-access"></a>Shrnutí a přístup k převodům návrháře zobrazení na sešity
[Návrhář zobrazení](view-designer.md) je funkce Azure monitor, která umožňuje vytvářet vlastní zobrazení, která vám pomohou vizualizovat data v pracovním prostoru Log Analytics s grafy, seznamy a časovými osami. Budou postupně vyladěny a nahrazeny sešity, které poskytují další funkce. Tento článek podrobně popisuje, jak můžete vytvořit souhrn přehledu a oprávnění požadovaná pro přístup k sešitům.

## <a name="creating-your-workspace-summary-from-azure-dashboard"></a>Vytvoření souhrnu pracovního prostoru z řídicího panelu Azure
Uživatelé návrháře zobrazení mohou být obeznámeni s tím, že má dlaždice s přehledem, aby představovala sadu zobrazení. Aby bylo možné udržovat vizuální přehled, například pracovní prostor návrháře zobrazení, sešity nabízí připnuté kroky, které lze připnout na [řídicí panel Azure Portal](../../azure-portal/azure-portal-dashboards.md). Stejně jako dlaždice přehledu v souhrnu pracovního prostoru se připnuté položky sešitu přímo odkazují na zobrazení sešitu.

Můžete využívat výhody vysoké úrovně funkcí přizpůsobení poskytovaných řídicími panely Azure, které umožňují automatické aktualizace, přesunutí, změnu velikosti a další filtrování pro připnuté položky a vizualizace. 

![Snímek obrazovky se zobrazí jako vlastní řídicí panel Azure s názvem Přehled pracovního prostoru.](media/view-designer-conversion-access/dashboard.png)

Vytvořte nový řídicí panel Azure nebo vyberte existující řídicí panel, aby bylo možné začít připínat položky sešitů.

Chcete-li připnout jednotlivou položku, budete muset povolit ikonu připnutí pro konkrétní krok. Provedete to tak, že vyberete odpovídající tlačítko **Upravit** pro váš krok a pak vyberete ikonu ozubeného kolečka a otevřete **Upřesnit nastavení**. Tuto možnost můžete zaškrtnout, pokud chcete **vždycky zobrazit ikonu připnutí v tomto kroku**, a v pravém horním rohu kroku se zobrazí ikona připnutí. Tento kód PIN vám umožní připnout konkrétní vizualizace na řídicí panel, jako jsou dlaždice s přehledem.

![Připnout krok](media/view-designer-conversion-access/pin-step.png)


Můžete také chtít připnout více vizualizací ze sešitu nebo celého obsahu sešitu na řídicí panel. Pokud chcete celý sešit připnout, vyberte **Upravit** na horním panelu nástrojů a přepněte **režim úprav**. Zobrazí se ikona připnutí, která vám umožní připnout celou položku sešitu nebo všechny jednotlivé kroky a vizualizace v sešitu.

![Připnout vše](media/view-designer-conversion-access/pin-all.png)



## <a name="sharing-and-viewing-permissions"></a>Oprávnění ke sdílení a zobrazení 
Sešity mají výhodu buď privátního, nebo sdíleného dokumentu. Ve výchozím nastavení budou uložené sešity uloženy pod **Mé sestavy**, což znamená, že tento sešit může zobrazit pouze tvůrce.

Sešity můžete sdílet výběrem ikony **sdílet** z horního panelu nástrojů v **režimu úprav**. Zobrazí se výzva k přesunutí sešitu do **sdílených sestav**, který vygeneruje odkaz, který bude poskytovat přímý přístup k sešitu.

Aby mohl uživatel zobrazit sdílený sešit, musí mít přístup k předplatnému i ke skupině prostředků, do které je sešit uložený.

![Přístup na základě předplatného](media/view-designer-conversion-access/subscription-access.png)

## <a name="next-steps"></a>Další kroky

- [Běžné úkoly](view-designer-conversion-tasks.md)