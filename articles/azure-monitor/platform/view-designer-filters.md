---
title: Filtry v zobrazení Azure Log Analytics | Dokumentace Microsoftu
description: Filtr na zobrazení Log Analytics umožňuje uživatelům k filtrování dat v zobrazení podle hodnoty určité vlastnosti beze změny samotného zobrazení.  Tento článek popisuje, jak pomocí filtru a přidejte ho do vlastního zobrazení.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: bwren
ms.openlocfilehash: 6a4ac2f26c01555ef54a4ee2248db7cd2818661e
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53189417"
---
# <a name="filters-in-log-analytics-views"></a>Filtry v zobrazení Log Analytics
A **filtr** v [zobrazení Log Analytics](view-designer.md) umožňuje uživatelům k filtrování dat v zobrazení podle hodnoty určité vlastnosti beze změny samotného zobrazení.  Například můžete umožnit uživatelům zobrazení filtrovat zobrazení dat jenom z určitého počítače nebo sadu počítačů.  Na jedno zobrazení umožňuje uživatelům můžete filtrovat podle několika vlastností můžete vytvořit více filtrů.  Tento článek popisuje, jak pomocí filtru a přidejte ho do vlastního zobrazení.

## <a name="using-a-filter"></a>Pomocí filtru
Klikněte na tlačítko časový rozsah dat v horní části zobrazení otevřete rozevírací nabídky, kde můžete změnit časový rozsah dat pro zobrazení.

![Příklad filtru](media/view-designer-filters/filters-example-time.png)

Klikněte na tlačítko **+** přidejte filtr pomocí vlastní filtry, které jsou definovány pro zobrazení. Hodnota pro filtr buď vyberte z rozevíracího seznamu nebo zadejte hodnotu. Pokračujte kliknutím na Přidat filtry **+**. 


![Příklad filtru](media/view-designer-filters/filters-example-custom.png)

Pokud odeberete všechny hodnoty filtru, bude použito už tento filtr.


## <a name="creating-a-filter"></a>Vytvoření filtru

Vytvořit filtr ze sloupce **filtry** kartě při [zobrazení pro úpravy](view-designer.md).  Tento filtr je globální zobrazení a platí pro všechny části v zobrazení.  

![Nastavení filtru](media/view-designer-filters/filters-settings.png)

Následující tabulka popisuje nastavení pro filtr.

| Nastavení | Popis |
|:---|:---|
| Název pole | Název pole použít pro filtrování.  Musí se shodovat s poli summarize v **dotaz na hodnoty**. |
| Dotaz na hodnoty | Použijte dotaz pro spuštění k naplnění rozevíracího seznamu filtru pro uživatele.  To je nutné použít buď [shrnout](/azure/kusto/query/summarizeoperator) nebo [odlišné](/azure/kusto/query/distinctoperator) zadejte jedinečné hodnoty pro určité pole a musí odpovídat **název pole**.  Můžete použít [řazení](/azure/kusto/query/sortoperator) seřadíte hodnoty, které se zobrazí uživateli. |
| Značka | Název pole, která se používá v dotazech podpora filtr a zobrazí se také pro uživatele. |

### <a name="examples"></a>Příklady

Následující tabulka obsahuje několik příkladů běžných filtry.  

| Název pole | Dotaz na hodnoty | Značka |
|:--|:--|:--|
| Počítač   | Prezenční signál &#124; distinct Computer &#124; seřadit podle počítače asc | Počítače |
| EventLevelName | Událost &#124; odlišné EventLevelName | Severity |
| SeverityLevel | Syslog &#124; odlišné SeverityLevel | Severity |
| SvcChangeType | ConfigurationChange &#124; odlišné svcChangeType | ChangeType |


## <a name="modify-view-queries"></a>Upravit zobrazení dotazů

Pro filtr nemá žádný vliv je třeba upravit všechny dotazy v zobrazení a filtrování podle vybraných hodnot.  Pokud nebudete muset měnit všechny dotazy v zobrazení se všechny hodnoty, které uživatel vybere nemají žádný vliv.

Syntaxe pro používání hodnota filtru v dotazu je následující: 

    where ${filter name}  

Například pokud zobrazení obsahuje dotaz vrátí události a používá filtr s názvem počítače, můžete použít následující.

    Event | where ${Computers} | summarize count() by EventLevelName

Pokud přidáte jiný filtr s názvem závažnost, můžete použít následující dotaz pomocí obou filtrů.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>Další postup
* Další informace o [části vizualizace](view-designer-parts.md) můžete přidat do vlastních zobrazení.
