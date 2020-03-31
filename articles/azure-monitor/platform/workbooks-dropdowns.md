---
title: Rozevírací parametry sešitu Azure Monitor
description: Zjednodušení složitých sestav pomocí předem sestavených a vlastních parametrizovaných sešitů obsahujících rozevírací parametry
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: f3220a363025d80fd7636dbfc3af3d2d9d7bc040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658277"
---
# <a name="workbook-drop-down-parameters"></a>Rozevírací parametry sešitu

Rozevírací obsah umožňuje uživateli shromažďovat jednu nebo více vstupních hodnot ze známé sady (například vyberte jeden z požadavků aplikace). Rozevírací obsah poskytují uživatelsky přívětivý způsob shromažďování libovolných vstupů od uživatelů. Rozevírací seznamy jsou užitečné zejména při povolování filtrování v interaktivních sestavách. 

Nejjednodušší způsob, jak zadat rozevírací seznam, je poskytnutí statického seznamu v nastavení parametru. Zajímavější způsob, jak je získat seznam dynamicky prostřednictvím dotazu KQL. Nastavení parametrů také umožňuje určit, zda je jeden nebo multi-select, a pokud je multi-select, jak má být sada výsledků formátována (oddělovač, nabídka, atd.).

## <a name="creating-a-static-drop-down-parameter"></a>Vytvoření statického rozevíracího parametru

1. Začněte s prázdným sešitem v režimu úprav.
2. Z odkazů v sešitu zvolte _Přidat parametry._
3. Klikněte na modré tlačítko _Přidat parametr._
4. V novém podokně parametrů, které se objeví, zadejte:
    1. Název parametru:`Environment`
    2. Typ parametru:`Drop down`
    3. Požadované:`checked`
    4. Povolit `multiple selection`:`unchecked`
    5. Získat data z:`JSON`
5. Do textového bloku Vstup JSON vložte tento fragment json:
    ```json
    [
        { "value":"dev", "label":"Development" },
        { "value":"ppe", "label":"Pre-production" },
        { "value":"prod", "label":"Production", "selected":true }
    ]
    ```
6. Zmáčkni `Update` modré tlačítko.
7. Chcete-li vytvořit parametr, zvolte "Uložit".
8. Environment Parametr bude rozevírací seznam se třemi hodnotami.

    ![Obrázek znázorňující vytvoření statického utonutí](./media/workbook-dropdowns/dropdown-create.png)

## <a name="creating-a-static-dropdown-with-groups-of-items"></a>Vytvoření statického rozevíracího seznamu se skupinami položek
Pokud výsledek dotazu/json obsahuje pole "skupina", v rozevíracím seznamu se zobrazí skupiny hodnot. Postupujte podle výše uvedeného vzorku, ale místo toho použijte následující json:
```json
[
    { "value":"dev", "label":"Development", "group":"Development" },
    { "value":"dev-cloud", "label":"Development (Cloud)", "group":"Development" },
    { "value":"ppe", "label":"Pre-production", "group":"Test" },
    { "value":"ppe-test", "label":"Pre-production (Test)", "group":"Test" },
    { "value":"prod1", "label":"Prod 1", "selected":true, "group":"Production" },
    { "value":"prod2", "label":"Prod 2", "group":"Production" }
]
```
    ![Image showing an example of a grouped dropdown](./media/workbook-dropdowns/grouped-dropDown.png)


## <a name="creating-a-dynamic-drop-down-parameter"></a>Vytvoření dynamického rozevíracího parametru
1. Začněte s prázdným sešitem v režimu úprav.
2. Z odkazů v sešitu zvolte _Přidat parametry._
3. Klikněte na modré tlačítko _Přidat parametr._
4. V novém podokně parametrů, které se objeví, zadejte:
    1. Název parametru:`RequestName`
    2. Typ parametru:`Drop down`
    3. Požadované:`checked`
    4. Povolit `multiple selection`:`unchecked`
    5. Získat data z:`Query`
5. Do textového bloku Vstup JSON vložte tento fragment json:

    ```kusto
        requests
        | summarize by name
        | order by name asc
    ```
1. Zmáčkni `Run Query` modré tlačítko.
2. Chcete-li vytvořit parametr, zvolte "Uložit".
3. Parametr RequestName bude rozevírací seznam názvů všech požadavků v aplikaci.

    ![Obrázek znázorňující vytvoření dynamickérozerozerozevírací rozevírací možnosti](./media/workbook-dropdowns/dropdown-dynamic.png)

## <a name="referencing-drop-down-parameter"></a>Odkazování na rozevírací parametr
### <a name="in-kql"></a>V KQL
1. Přidejte do sešitu ovládací prvek dotazu a vyberte prostředek Application Insights.
2. V editoru KQL zadejte tento úryvek.

    ```kusto
        requests
        | where name == '{RequestName}'
        | summarize Requests = count() by bin(timestamp, 1h)

    ```
3. Tím se čas vyhodnocení dotazu rozšiřuje na:

    ```kusto
        requests
        | where name == 'GET Home/Index'
        | summarize Requests = count() by bin(timestamp, 1h)
    ```

4. Spusťte dotaz, abyste viděli výsledky. Volitelně jej vykreslete jako graf.

    ![Obrázek znázorňující rozevírací nabídku odkazovanou v KQL](./media/workbook-dropdowns/dropdown-reference.png)


## <a name="parameter-value-label-selection-and-group"></a>Hodnota parametru, popisek, výběr a skupina
Dotaz použitý v parametru dynamickérozetvírací seznam výše pouze vrátí seznam hodnot, které jsou vykreslovány věrně v rozevíracím seznamu. Ale co když chcete, aby byl vybrán jiný zobrazovaný název, nebo jeden z nich? Rozevírací parametry to umožňují prostřednictvím sloupců hodnoty, popisku, výběru a skupiny.

Následující ukázka ukazuje, jak získat seznam závislostí Application Insights, jejichž zobrazované názvy jsou stylizovány s emodži, má první vybrané a je seskupena podle názvů operací.

```kusto
dependencies
| summarize by operation_Name, name
| where name !contains ('.')
| order by name asc
| serialize Rank = row_number()
| project value = name, label = strcat('🌐 ', name), selected = iff(Rank == 1, true, false), group = operation_Name
```
    ![Image showing a drop-down parameter using value, label, selection and group options](./media/workbook-dropdowns/dropdown-more-options.png)


## <a name="drop-down-parameter-options"></a>Možnosti rozevíracího parametru
| Parametr | Vysvětlení | Příklad |
| ------------- |:-------------|:-------------|
| `{DependencyName}` | Vybraná hodnota | GET fabrikamaccount |
| `{DependencyName:label}` | Vybraný popisek | 🌐 GET fabrikamaccount |
| `{DependencyName:value}` | Vybraná hodnota | GET fabrikamaccount |

## <a name="multiple-selection"></a>Vícenásobný výběr
Příklady zatím explicitně nastavit parametr pro výběr pouze jednu hodnotu v rozevíracím seznam. Drop down parametry `multiple selection` také podporují - povolení je `Allow multiple selection` stejně jednoduché jako kontrola možnosti. 

Uživatel má také možnost zadat formát sady výsledků `delimiter` prostřednictvím `quote with` nastavení a. Výchozí pouze vrátí hodnoty jako kolekce v tomto formuláři: 'a', 'b', 'c'. Mají také možnost omezit počet výběrů.

KQL odkazující na parametr bude muset změnit pracovat s formátem výsledku. Nejběžnější způsob, jak to povolit, je prostřednictvím operátora. `in`

```kusto
dependencies
| where name in ({DependencyName})
| summarize Requests = count() by bin(timestamp, 1h), name
```

Zde je příklad pro vícenásobné rozevírací rozevírací v práci:

![Obrázek znázorňující vícenásobný výběrový parametr](./media/workbook-dropdowns/dropdown-multiselect.png)

## <a name="next-steps"></a>Další kroky

* [Začínáte](workbooks-visualizations.md) se učit další informace o sešitech, mnoho bohatých možností vizualizací.
* [Řízení](workbooks-access-control.md) a sdílení přístupu k prostředkům sešitu
