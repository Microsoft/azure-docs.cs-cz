---
title: Filtry v zobrazeních Azure Monitoru | Dokumenty společnosti Microsoft
description: Filtr v zobrazení Azure Monitor umožňuje uživatelům filtrovat data v zobrazení podle hodnoty konkrétní vlastnosti bez změny samotného zobrazení.  Tento článek popisuje použití filtru a jeho přidání do vlastního zobrazení.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/22/2018
ms.openlocfilehash: b4840ed30eb1f6dc8d6e6cef47da17807f9644d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658570"
---
# <a name="filters-in-azure-monitor-views"></a>Filtry v zobrazeních Azure Monitoru
**Filtr** v [zobrazení Azure Monitor](view-designer.md) umožňuje uživatelům filtrovat data v zobrazení podle hodnoty konkrétní vlastnosti bez změny samotného zobrazení.  Můžete například umožnit uživatelům zobrazení filtrovat zobrazení dat pouze z určitého počítače nebo sady počítačů.  V jednom zobrazení můžete vytvořit více filtrů, které uživatelům umožní filtrovat podle více vlastností.  Tento článek popisuje použití filtru a jeho přidání do vlastního zobrazení.

## <a name="using-a-filter"></a>Použití filtru
Kliknutím na rozsah data v horní části zobrazení otevřete rozevírací rozbalovací nabídku, kde můžete změnit rozsah data pro zobrazení.

![Příklad filtru](media/view-designer-filters/filters-example-time.png)

Klepnutím **+** na tlačítko přidáte filtr pomocí vlastních filtrů, které jsou definovány pro zobrazení. Vyberte hodnotu filtru z rozevíracího souboru nebo zadejte hodnotu. Pokračujte v přidávání **+** filtrů klepnutím na tlačítko . 


![Příklad filtru](media/view-designer-filters/filters-example-custom.png)

Pokud odeberete všechny hodnoty filtru, nebude tento filtr již použit.


## <a name="creating-a-filter"></a>Vytvoření filtru

Při [úpravách zobrazení](view-designer.md)vytvořte filtr z karty **Filtry** .  Filtr je pro zobrazení globální a vztahuje se na všechny součásti v zobrazení.  

![Nastavení filtru](media/view-designer-filters/filters-settings.png)

Následující tabulka popisuje nastavení filtru.

| Nastavení | Popis |
|:---|:---|
| Název pole | Název pole použitého pro filtrování.  Toto pole musí odpovídat poli souhrnu v **poli Dotaz na hodnoty**. |
| Dotaz na hodnoty | Dotaz ke spuštění k naplnění rozevírací seznam filtru pro uživatele.  Tento dotaz musí použít [souhrn](/azure/kusto/query/summarizeoperator) nebo [odlišné](/azure/kusto/query/distinctoperator) poskytnout jedinečné hodnoty pro určité pole a musí odpovídat **název pole**.  [Řazení](/azure/kusto/query/sortoperator) můžete použít k seřazení hodnot, které jsou zobrazeny uživateli. |
| Značka | Název pole, které se používá v dotazech podporujících filtr a je také zobrazen uživateli. |

### <a name="examples"></a>Příklady

Následující tabulka obsahuje několik příkladů běžných filtrů.  

| Název pole | Dotaz na hodnoty | Značka |
|:--|:--|:--|
| Počítač   | Prezenční signál &#124; odlišné &#124; počítač řazení podle počítače asc | Počítače |
| Název_události_události | Událost &#124; distinct název_úrovně_události | Severity |
| Úroveň závažnosti | Syslog &#124; odlišné závažnosti | Severity |
| SvcChangeType | ConfigurationChange &#124; distinct svcChangeType | Changetype |


## <a name="modify-view-queries"></a>Změna zobrazení dotazů

Aby filtr měl nějaký vliv, je nutné upravit všechny dotazy v zobrazení, aby bylo nutné filtrovat vybrané hodnoty.  Pokud v zobrazení neupravíte žádné dotazy, nebudou mít žádné hodnoty, které uživatel vybere, žádný vliv.

Syntaxe pro použití hodnoty filtru v dotazu je: 

    where ${filter name}  

Pokud má například zobrazení dotaz, který vrací události a používá filtr s názvem _Počítače_, můžete použít následující dotaz.

    Event | where ${Computers} | summarize count() by EventLevelName

Pokud jste přidali další filtr s názvem Závažnost, můžete použít následující dotaz k použití obou filtrů.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [vizualizačních částech,](view-designer-parts.md) které můžete přidat do vlastního zobrazení.
