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
ms.date: 10/31/2017
ms.author: bwren
ms.openlocfilehash: 5c2201292eb085dcc043e4257580c7971dbaffbd
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
ms.locfileid: "23945881"
---
# <a name="filters-in-log-analytics-views"></a>Filtry v zobrazení analýzy protokolů
A **filtru** v [zobrazení analýzy protokolů](log-analytics-view-designer.md) umožňuje uživatelům filtrování dat v zobrazení hodnotou určité vlastnosti beze změny samotném zobrazení.  Můžete například povolit uživatelům zobrazení filtrovat zobrazení pro data pouze z určitého počítače nebo sadu počítačů.  V rámci jednoho zobrazení umožníte uživatelům filtrovat podle více vlastností můžete vytvořit více filtrů.  Tento článek popisuje, jak používat filtr a přidat vlastní zobrazení.

## <a name="using-a-filter"></a>Pomocí filtru.
Klikněte na tlačítko **filtru** otevřete podokno filtru pro zobrazení.  To umožňuje vybrat časové rozmezí a hodnoty pro všechny filtry, které jsou k dispozici pro zobrazení.  Když vyberete filtr, zobrazí seznam dostupných hodnot.  Můžete vybrat jednu nebo více hodnot, nebo zadejte je do. Zobrazení se automaticky aktualizuje a filtrovat podle hodnoty, které zadáte. 

Pokud není vybrána žádná hodnota pro filtr, není tento filtr použít u zobrazení.  Pokud odeberete všechny hodnoty pro filtr, pak tento filtr se už použijí.


![Příklad filtru](media/log-analytics-view-designer/filters-example.png)


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
| EventLevelName | Event &#124; distinct EventLevelName | Závažnost |
| Úroveň závažnosti | Syslog &#124; odlišné úroveň závažnosti | Závažnost |
| SvcChangeType | Změnakonfigurace &#124; odlišné svcChangeType | ChangeType |


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