---
title: Filtry v zobrazeních Azure Monitor | Microsoft Docs
description: Filtr v zobrazení Azure Monitor umožňuje uživatelům filtrovat data v zobrazení podle hodnoty určité vlastnosti beze změny zobrazení.  Tento článek popisuje, jak použít filtr a přidat ho do vlastního zobrazení.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/22/2018
ms.openlocfilehash: b4840ed30eb1f6dc8d6e6cef47da17807f9644d5
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658570"
---
# <a name="filters-in-azure-monitor-views"></a>Filtry v zobrazeních Azure Monitor
**Filtr** v [zobrazení Azure monitor](view-designer.md) umožňuje uživatelům filtrovat data v zobrazení podle hodnoty určité vlastnosti beze změny zobrazení.  Můžete například uživatelům zobrazení dovolit filtrovat zobrazení dat pouze z konkrétního počítače nebo sady počítačů.  V jednom zobrazení můžete vytvořit více filtrů, které umožní uživatelům filtrovat podle více vlastností.  Tento článek popisuje, jak použít filtr a přidat ho do vlastního zobrazení.

## <a name="using-a-filter"></a>Použití filtru
Kliknutím na rozsah data a času v horní části zobrazení otevřete rozevírací nabídku, kde můžete změnit rozsah data a času pro zobrazení.

![Příklad filtru](media/view-designer-filters/filters-example-time.png)

Kliknutím na **+** přidáte filtr pomocí vlastních filtrů, které jsou definovány pro zobrazení. Buď vyberte hodnotu pro filtr z rozevíracího seznamu, nebo zadejte hodnotu. Kliknutím na **+** pokračujte v přidávání filtrů. 


![Příklad filtru](media/view-designer-filters/filters-example-custom.png)

Pokud odeberete všechny hodnoty pro filtr, pak tento filtr nebude nadále použit.


## <a name="creating-a-filter"></a>Vytvoření filtru

Při [úpravách zobrazení](view-designer.md)vytvořte filtr na kartě **filtry** .  Filtr je globální pro zobrazení a vztahuje se na všechny části v zobrazení.  

![Nastavení filtru](media/view-designer-filters/filters-settings.png)

Následující tabulka popisuje nastavení pro filtr.

| Nastavení | Popis |
|:---|:---|
| Název pole | Název pole, které se používá pro filtrování  Toto pole se musí shodovat s polem Shrnutí v **dotazu pro hodnoty**. |
| Dotaz na hodnoty | Dotaz pro spuštění, který naplní rozevírací seznam filtru pro uživatele.  Tento dotaz musí k zadání jedinečných hodnot pro určité pole použít [Souhrn](/azure/kusto/query/summarizeoperator) nebo [jedinečnou](/azure/kusto/query/distinctoperator) hodnotu a musí se shodovat s **názvem pole**.  K řazení hodnot zobrazených uživateli můžete použít [řazení](/azure/kusto/query/sortoperator) . |
| Značka | Název pole, které se používá v dotazech podporujících filtr a zobrazuje se také uživateli. |

### <a name="examples"></a>Příklady

Následující tabulka obsahuje několik příkladů běžných filtrů.  

| Název pole | Dotaz na hodnoty | Značka |
|:--|:--|:--|
| Computer   | Řazení &#124; samostatného &#124; počítače z prezenčního signálu podle počítače ASC | Počítače |
| EventLevelName | Událost &#124; DISTINCT EventLevelName | Závažnost |
| Úroveň závažnosti | Protokol &#124; syslog DISTINCT SeverityLevel | Závažnost |
| SvcChangeType | ConfigurationChange &#124; DISTINCT svcChangeType | ChangeType |


## <a name="modify-view-queries"></a>Upravit zobrazení dotazů

Aby měl filtr nějaký vliv, musíte upravit všechny dotazy v zobrazení a filtrovat vybrané hodnoty.  Pokud žádné dotazy v zobrazení nezměníte, nebudou žádné hodnoty, které uživatel vybral, nijak ovlivněny.

Syntaxe pro použití hodnoty filtru v dotazu je: 

    where ${filter name}  

Pokud například zobrazení obsahuje dotaz, který vrací události a používá filtr nazvaný _Computers_, můžete použít následující dotaz.

    Event | where ${Computers} | summarize count() by EventLevelName

Pokud jste přidali další filtr s názvem závažnost, můžete použít následující dotaz pro použití obou filtrů.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [částech vizualizace](view-designer-parts.md) , které můžete přidat do vlastního zobrazení.
