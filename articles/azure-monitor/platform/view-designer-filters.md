---
title: Filtry v zobrazení monitorování Azure | Dokumentace Microsoftu
description: Filtrování v zobrazení Azure Monitor umožňuje uživatelům k filtrování dat v zobrazení podle hodnoty určité vlastnosti beze změny samotného zobrazení.  Tento článek popisuje, jak pomocí filtru a přidejte ho do vlastního zobrazení.
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
ms.openlocfilehash: 31a902302ba806889854330c6517d9f5745f1c0c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60551693"
---
# <a name="filters-in-azure-monitor-views"></a>Filtry v zobrazení monitorování Azure
A **filtr** v [zobrazení monitorování Azure](view-designer.md) umožňuje uživatelům k filtrování dat v zobrazení podle hodnoty určité vlastnosti beze změny samotného zobrazení.  Například můžete umožnit uživatelům zobrazení filtrovat zobrazení dat jenom z určitého počítače nebo sadu počítačů.  Na jedno zobrazení umožňuje uživatelům můžete filtrovat podle několika vlastností můžete vytvořit více filtrů.  Tento článek popisuje, jak pomocí filtru a přidejte ho do vlastního zobrazení.

## <a name="using-a-filter"></a>Pomocí filtru
Klikněte na datum časový rozsah v horní části zobrazení otevřete rozevírací nabídky, kde můžete změnit datum časový rozsah pro zobrazení.

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
| Název pole | Název pole použít pro filtrování.  Toto pole musí odpovídat poli summarize v **dotaz na hodnoty**. |
| Dotaz na hodnoty | Použijte dotaz pro spuštění k naplnění rozevíracího seznamu filtru pro uživatele.  Tento dotaz musí používat buď [shrnout](/azure/kusto/query/summarizeoperator) nebo [odlišné](/azure/kusto/query/distinctoperator) zadejte jedinečné hodnoty pro určité pole a musí odpovídat **název pole**.  Můžete použít [řazení](/azure/kusto/query/sortoperator) seřadíte hodnoty, které se zobrazí uživateli. |
| Značka | Název pole, která se používá v dotazech podpora filtr a zobrazí se také pro uživatele. |

### <a name="examples"></a>Příklady

Následující tabulka obsahuje několik příkladů běžných filtry.  

| Název pole | Dotaz na hodnoty | Značka |
|:--|:--|:--|
| Computer   | Prezenční signál &#124; distinct Computer &#124; seřadit podle počítače asc | Počítače |
| EventLevelName | Event &#124; distinct EventLevelName | Severity |
| SeverityLevel | Syslog &#124; distinct SeverityLevel | Severity |
| SvcChangeType | ConfigurationChange &#124; distinct svcChangeType | ChangeType |


## <a name="modify-view-queries"></a>Upravit zobrazení dotazů

Pro filtr nemá žádný vliv je třeba upravit všechny dotazy v zobrazení a filtrování podle vybraných hodnot.  Pokud nebudete muset měnit všechny dotazy v zobrazení, budou všechny hodnoty, které uživatel vybere nemají žádný vliv.

Syntaxe pro používání hodnota filtru v dotazu je následující: 

    where ${filter name}  

Například, pokud zobrazení obsahuje dotaz, který vrátí události a využívá filtr volá _počítače_, můžete použít následující dotaz.

    Event | where ${Computers} | summarize count() by EventLevelName

Pokud přidáte jiný filtr s názvem závažnost, můžete použít následující dotaz pomocí obou filtrů.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>Další postup
* Další informace o [části vizualizace](view-designer-parts.md) můžete přidat do vlastních zobrazení.
