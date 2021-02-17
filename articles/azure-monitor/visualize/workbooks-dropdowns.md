---
title: Parametry rozevíracího seznamu Azure Monitorho sešitu
description: Zjednodušení složitých sestav s předem sestavenými a vlastními parametrizovanými sešity, které obsahují parametry rozevíracího seznamu
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: c3b44c7b8761010deeecba1f8ed80727fe635f2b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100612718"
---
# <a name="workbook-drop-down-parameters"></a>Parametry rozevíracího seznamu sešitu

Rozevírací nabídky umožňují uživateli shromáždit jednu nebo více vstupních hodnot ze známé sady (například vybrat jednu z požadavků vaší aplikace). Rozevírací nabídky poskytují uživatelsky přívětivý způsob, jak shromažďovat libovolné vstupy uživatelů. Rozevírací seznam je zvláště užitečný při povolování filtrování v interaktivních sestavách. 

Nejjednodušší způsob, jak určit rozevírací seznam, je poskytnutím statického seznamu v nastavení parametru. Zajímavějším způsobem je získat seznam dynamicky prostřednictvím dotazu KQL. Nastavení parametrů vám také umožní určit, jestli je jeden nebo vícenásobný výběr, a pokud je vícenásobný výběr, jak by měla být naformátovaná sada výsledků (oddělovač, citace atd.).

## <a name="creating-a-static-drop-down-parameter"></a>Vytvoření statického parametru rozevíracího seznamu

1. Začněte s prázdným sešitem v režimu úprav.
2. Vyberte možnost _přidat parametry_ z odkazů v rámci sešitu.
3. Klikněte na modré tlačítko _Přidat parametr_ .
4. V podokně nového parametru, které se objeví, zadejte:
    1. Název parametru: `Environment`
    2. Typ parametru: `Drop down`
    3. Požadovanou `checked`
    4. Povolení `multiple selection` : `unchecked`
    5. Získat data z: `JSON`
5. Do textového bloku JSON Input vložte tento fragment kódu JSON:
    ```json
    [
        { "value":"dev", "label":"Development" },
        { "value":"ppe", "label":"Pre-production" },
        { "value":"prod", "label":"Production", "selected":true }
    ]
    ```
6. Stiskněte modré `Update` tlačítko.
7. Pokud chcete vytvořit parametr, klikněte na tlačítko Uložit na panelu nástrojů.
8. Parametr prostředí bude rozevírací seznam se třemi hodnotami.

    ![Obrázek znázorňující vytvoření statického drowN](./media/workbooks-dropdowns/dropdown-create.png)

## <a name="creating-a-static-dropdown-with-groups-of-items"></a>Vytvoření statického rozevíracího seznamu se skupinami položek

Pokud výsledek dotazu/JSON obsahuje pole "skupina", zobrazí se v rozevíracím seznamu skupiny hodnot. Postupujte podle výše uvedeného příkladu, ale místo toho použijte následující kód JSON:

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

![Obrázek znázorňující příklad seskupeného rozevíracího seznamu](./media/workbooks-dropdowns/grouped-dropDown.png)


## <a name="creating-a-dynamic-drop-down-parameter"></a>Vytvoření dynamického rozevíracího parametru
1. Začněte s prázdným sešitem v režimu úprav.
2. Vyberte možnost _přidat parametry_ z odkazů v rámci sešitu.
3. Klikněte na modré tlačítko _Přidat parametr_ .
4. V podokně nového parametru, které se objeví, zadejte:
    1. Název parametru: `RequestName`
    2. Typ parametru: `Drop down`
    3. Požadovanou `checked`
    4. Povolení `multiple selection` : `unchecked`
    5. Získat data z: `Query`
5. Do textového bloku JSON Input vložte tento fragment kódu JSON:

    ```kusto
        requests
        | summarize by name
        | order by name asc
    ```
1. Stiskněte modré `Run Query` tlačítko.
2. Pokud chcete vytvořit parametr, klikněte na tlačítko Uložit na panelu nástrojů.
3. Parametr název žádosti bude rozevírací seznam všech požadavků v aplikaci.

    ![Obrázek znázorňující vytvoření dynamického rozevíracího seznamu](./media/workbooks-dropdowns/dropdown-dynamic.png)

## <a name="referencing-drop-down-parameter"></a>Odkazování na parametr rozevíracího seznamu

### <a name="in-kql"></a>V KQL
1. Přidejte do sešitu ovládací prvek dotazu a vyberte prostředek Application Insights.
2. V editoru KQL zadejte tento fragment kódu.

    ```kusto
        requests
        | where name == '{RequestName}'
        | summarize Requests = count() by bin(timestamp, 1h)

    ```
3. Tím se rozšíří doba vyhodnocování dotazu na:

    ```kusto
        requests
        | where name == 'GET Home/Index'
        | summarize Requests = count() by bin(timestamp, 1h)
    ```

4. Spusťte dotaz, aby se zobrazily výsledky. Případně ho vykreslete jako graf.

    ![Obrázek znázorňující rozevírací seznam, na který odkazuje KQL](./media/workbooks-dropdowns/dropdown-reference.png)


## <a name="parameter-value-label-selection-and-group"></a>Hodnota parametru, popisek, výběr a skupina
Dotaz použitý v parametru dynamického rozevíracího seznamu výše vrací jenom seznam hodnot, které jsou v rozevíracím seznamu vykresleny s přístupnými hodnotami. Ale co když jste si chtěli vybrat jiný zobrazovaný název, nebo pokud chcete vybrat jednu z nich? Parametry rozevíracího seznamu umožňují tuto možnost prostřednictvím sloupců hodnot, popisků, výběru a skupin.

Následující ukázka ukazuje, jak získat seznam závislostí Application Insights, jejichž zobrazované názvy mají styl Emoji, má první vybraný a je seskupen podle názvů operací.

```kusto
dependencies
| summarize by operation_Name, name
| where name !contains ('.')
| order by name asc
| serialize Rank = row_number()
| project value = name, label = strcat('🌐 ', name), selected = iff(Rank == 1, true, false), group = operation_Name
```

![Obrázek znázorňující parametr rozevíracího seznamu pomocí možností hodnot, popisků, výběru a skupin](./media/workbooks-dropdowns/dropdown-more-options.png)


## <a name="drop-down-parameter-options"></a>Parametry rozevíracího seznamu
| Parametr | Vysvětlení | Příklad |
| ------------- |:-------------|:-------------|
| `{DependencyName}` | Vybraná hodnota | ZÍSKAT fabrikamaccount |
| `{DependencyName:label}` | Vybraný popisek | 🌐 ZÍSKAT fabrikamaccount |
| `{DependencyName:value}` | Vybraná hodnota | ZÍSKAT fabrikamaccount |

## <a name="multiple-selection"></a>Vícenásobný výběr
Příklady, které byly doposud explicitně nastaveny tak, aby v rozevíracím seznamu vybrali pouze jednu hodnotu. Parametry rozevíracího seznamu podporují také `multiple selection` – Povolení této možnosti je jednoduché `Allow multiple selection` . 

Uživatel má také možnost zadat formát sady výsledků prostřednictvím `delimiter` `quote with` nastavení a. Výchozí hodnota vrátí hodnoty jako kolekci v tomto formátu: "a", "b", "c". Mají taky možnost omezit počet výběrů.

KQL odkazující na parametr bude muset změnit na práci s formátem výsledku. Nejběžnější způsob, jak ho povolit, je prostřednictvím `in` operátoru.

```kusto
dependencies
| where name in ({DependencyName})
| summarize Requests = count() by bin(timestamp, 1h), name
```

Tady je příklad pro rozevírací seznam s vícenásobným výběrem v práci:

![Obrázek znázorňující parametr rozevíracího seznamu s vícenásobným výběrem](./media/workbooks-dropdowns/dropdown-multiselect.png)

## <a name="next-steps"></a>Další kroky

* [Začínáme](../platform/workbooks-overview.md#visualizations) se dozvědět více o seznámcích s mnoha různými možnostmi vizualizací.
* [Řízení](../platform/workbooks-access-control.md) a sdílení přístupu k prostředkům sešitu.