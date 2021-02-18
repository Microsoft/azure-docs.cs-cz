---
title: Možnosti převodu návrháře zobrazení Azure Monitor na sešity
description: Možnosti převodu pro přechod ze zobrazení do sešitů v Azure Monitor.
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: a36361430d6ac2af598c2255aed5830150f02217
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100612562"
---
# <a name="azure-monitor-view-designer-to-workbooks-conversion-options"></a>Možnosti převodu návrháře zobrazení Azure Monitor na sešity
[Návrhář zobrazení](view-designer.md) je funkce Azure monitor, která umožňuje vytvářet vlastní zobrazení, která vám pomohou vizualizovat data v pracovním prostoru Log Analytics s grafy, seznamy a časovými osami. Budou postupně vyladěny a nahrazeny sešity, které poskytují další funkce. Tento článek porovnává základní koncepty mezi dvěma a možnostmi pro převod zobrazení na sešity.

## <a name="basic-workbook-designs"></a>Základní návrhy sešitu

Návrhář zobrazení má pevně daný statický styl reprezentace, zatímco sešity umožňují přidat a upravit způsob reprezentace dat. Následující obrázky popisují dva příklady, jak můžete uspořádat sešity při převodu zobrazení.

[Svislý sešit](view-designer-conversion-examples.md#vertical) 
 ![ Svislé](media/view-designer-conversion-options/view-designer-vertical.png)

[](view-designer-conversion-examples.md#tabbed) 
 Sešit ![ s kartami Datové typy karty distribuce datového typu ](media/view-designer-conversion-options/distribution-tab.png)
 ![ přes kartu čas](media/view-designer-conversion-options/over-time-tab.png)

## <a name="tile-conversion"></a>Převod dlaždic
Návrhář zobrazení používá funkci dlaždice přehled k reprezentaci a sumarizaci celkového stavu. Ty se reprezentují ve sedmi dlaždicích, od čísel až po grafy. V sešitech můžou uživatelé vytvářet podobné vizualizace a připnout je, aby se podobají původnímu stylu dlaždic s přehledem. 

![Galerie](media/view-designer-conversion-options/overview.png)


## <a name="view-dashboard-conversion"></a>Zobrazit převod řídicího panelu
Dlaždice návrháře zobrazení se typicky skládají ze dvou sekcí, vizualizace a seznamu, který se shoduje s daty ze vizualizace, jako je například dlaždice **prstenec & seznam** .

![Prstencový graf](media/view-designer-conversion-options/donut-example.png)

Se sešity umožňují uživateli zvolit dotaz na jeden nebo oba části zobrazení. Formulace dotazů v sešitech je jednoduchý proces se dvěma kroky. Nejprve se data generují z dotazu a druhý, data se vykreslují jako vizualizace.  Příkladem toho, jak by bylo toto zobrazení v sešitech znovu vytvořeno, je následující:

![Převést](media/view-designer-conversion-options/convert-donut.png)


## <a name="next-steps"></a>Další kroky
- [Přístup k sešitům & oprávnění](view-designer-conversion-access.md)