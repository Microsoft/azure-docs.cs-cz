---
title: Návrhář zobrazení Azure Monitor u možností převodu sešitů
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 7bfa831332451718c0c9c05023b90104d2b8b02b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658706"
---
# <a name="azure-monitor-view-designer-to-workbooks-conversion-options"></a>Návrhář zobrazení Azure Monitor u možností převodu sešitů
[Návrhář zobrazení](view-designer.md) je funkce Azure Monitoru, která umožňuje vytvářet vlastní zobrazení, která vám pomohou vizualizovat data v pracovním prostoru Log Analytics pomocí grafů, seznamů a časových os. Jsou postupně ukončovány a nahrazovány sešity, které poskytují další funkce. Tento článek porovnává základní pojmy mezi těmito dvěma a možnostmi převodu zobrazení na sešity.

## <a name="basic-workbook-designs"></a>Základní návrhy sešitů

Návrhář zobrazení má pevný statický styl reprezentace, zatímco sešity umožňují svobodu zahrnout a upravit způsob reprezentace dat. Níže uvedené obrázky znázorňují dva příklady uspořádání sešitů při převodu zobrazení.

[Svislý sešit](view-designer-conversion-examples.md#vertical)
![svisle](media/view-designer-conversion-options/view-designer-vertical.png)

[Karta Sešit](view-designer-conversion-examples.md#tabbed)
![Distribuční](media/view-designer-conversion-options/distribution-tab.png)
![karta Datový typ Datové typy Datové typy v průběhu času](media/view-designer-conversion-options/over-time-tab.png)

## <a name="tile-conversion"></a>Převod dlaždic
Návrhář zobrazení používá funkci dlaždice přehledu k reprezentaci a shrnutí celkového stavu. Ty jsou reprezentovány v sedmi dlaždicích, od čísel až po grafy. V sešitech mohou uživatelé vytvářet podobné vizualizace a připínat je tak, aby připomínaly původní styl dlaždic přehledu. 

![Galerie](media/view-designer-conversion-options/overview.png)


## <a name="view-dashboard-conversion"></a>Zobrazit převod řídicího panelu
Zobrazit dlaždice návrháře se obvykle skládají ze dvou částí, vizualizace a seznamu, který odpovídá datům z vizualizace, například dlaždice **Seznam &.**

![Prstencový graf](media/view-designer-conversion-options/donut-example.png)

V sešitech umožňujeme uživateli zvolit dotaz na jednu nebo obě části zobrazení. Formulování dotazů v sešitech je jednoduchý dvoustupňový proces. Nejprve jsou data generována z dotazu a za druhé, data jsou vykreslena jako vizualizace.  Příklad, jak by bylo toto zobrazení znovu vytvořeno v sešitech, je následující:

![Převést](media/view-designer-conversion-options/convert-donut.png)


## <a name="next-steps"></a>Další kroky
- [Přístup k sešitům & oprávněními](view-designer-conversion-access.md)