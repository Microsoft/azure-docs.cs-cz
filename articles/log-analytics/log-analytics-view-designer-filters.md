---
title: Filtry v zobrazeních Azure Log Analytics | Microsoft Docs
description: Filtr zobrazení analýzy protokolů umožňuje uživatelům filtrování dat v zobrazení hodnotou určité vlastnosti beze změny samotném zobrazení.  Tento článek popisuje, jak používat filtr a přidat vlastní zobrazení.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: bwren
ms.openlocfilehash: 21b54f60286e25c410b9d51de8be122c450080d3
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36752771"
---
# <a name="filters-in-log-analytics-views"></a>Filtry v zobrazení analýzy protokolů
A **filtru** v [zobrazení analýzy protokolů](log-analytics-view-designer.md) umožňuje uživatelům filtrování dat v zobrazení hodnotou určité vlastnosti beze změny samotném zobrazení.  Můžete například povolit uživatelům zobrazení filtrovat zobrazení pro data pouze z určitého počítače nebo sadu počítačů.  V rámci jednoho zobrazení umožníte uživatelům filtrovat podle více vlastností můžete vytvořit více filtrů.  Tento článek popisuje, jak používat filtr a přidat vlastní zobrazení.

## <a name="using-a-filter"></a>Pomocí filtru.
Klikněte na tlačítko časové rozmezí dat v horní části zobrazení otevřete rozevírací nabídky, kde můžete změnit časový rozsah dat pro zobrazení.

![Příklad filtru](media/log-analytics-view-designer/filters-example-time.png)

Klikněte **+** chcete přidat filtr pomocí vlastní filtry, které jsou definovány pro zobrazení. Buď vyberte hodnotu pro filtr z rozevíracího seznamu nebo zadejte hodnotu. Pokračujte pro přidání filtry kliknutím **+**. 


![Příklad filtru](media/log-analytics-view-designer/filters-example-custom.png)

Pokud odeberete všechny hodnoty pro filtr, pak tento filtr se už použijí.


## <a name="creating-a-filter"></a>Vytváření filtrů

Vytvoření filtru z **filtry** při [úpravy zobrazení](log-analytics-view-designer.md).  Filtr je globální pro zobrazení a platí pro všechny části v zobrazení.  

![Nastavení filtru](media/log-analytics-view-designer/filters-settings.png)

Následující tabulka popisuje nastavení pro filtr.

| Nastavení | Popis |
|:---|:---|
| Název pole | Název pole použité k filtrování.  V poli summarize musí odpovídat **dotazu pro hodnoty**. |
| Dotaz pro hodnoty | Dotaz spustit k naplnění rozevíracího seznamu filtru pro uživatele.  To musíte použít buď [shrnout](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) nebo [odlišné](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/distinct-operator) zajistit jedinečné hodnoty pro konkrétního pole a musí odpovídat **název pole**.  Můžete použít [řazení](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator) seřadíte hodnoty, které se zobrazí uživateli. |
| Značka | Název pole, která se používá v dotazech podpora filtr a zobrazí se také pro uživatele. |

### <a name="examples"></a>Příklady

Následující tabulka obsahuje několik příkladů běžných filtrů.  

| Název pole | Dotaz pro hodnoty | Značka |
|:--|:--|:--|
| Počítač   | Prezenční signál &#124; distinct Computer &#124; řazení podle počítače asc | Počítače |
| EventLevelName | Událost &#124; odlišné EventLevelName | Severity |
| Úroveň závažnosti | Syslog &#124; odlišné úroveň závažnosti | Severity |
| SvcChangeType | Změnakonfigurace &#124; odlišné svcChangeType | ChangeType – |


## <a name="modify-view-queries"></a>Upravit zobrazení dotazů

Pro filtr nemá žádný vliv je nutné upravit všechny dotazy v zobrazení pro filtrování vybraných hodnot.  Pokud nebudete muset měnit všechny dotazy v zobrazení všechny hodnoty, které uživatel vybere nebude mít žádný vliv.

Syntaxe pro používání hodnota filtru v dotazu je: 

    where ${filter name}  

Například pokud zobrazení obsahuje dotaz vrátí události a používá filtr názvem počítače, můžete použít následující.

    Event | where ${Computers} | summarize count() by EventLevelName

Pokud jste přidali další filtr názvem závažnost, můžete použít následující dotaz pomocí obou filtrů.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>Další postup
* Další informace o [vizualizace částí](log-analytics-view-designer-parts.md) můžete přidat do vlastní zobrazení.