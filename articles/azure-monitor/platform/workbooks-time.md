---
title: Časové parametry sešitů Azure Monitor
description: Zjednodušení složitých sestav pomocí předem sestavených a vlastních parametrizovaných sešitů
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 380b8a7ce286ab06b6935bf63bf3a0e82f371c2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658009"
---
# <a name="workbook-time-parameters"></a>Parametry času sešitu

Časové parametry umožňují uživatelům nastavit časový kontext analýzy a používají je téměř všechny sestavy. Je poměrně jednoduché nastavení a použití - umožňuje autorům určit časové rozsahy, které se mají zobrazit v rozevíracím seznamu, včetně možnosti pro vlastní časové rozsahy. 

## <a name="creating-a-time-parameter"></a>Vytvoření časového parametru
1. Začněte s prázdným sešitem v režimu úprav.
2. Z odkazů v sešitu zvolte _Přidat parametry._
3. Klikněte na modré tlačítko _Přidat parametr._
4. V novém podokně parametrů, které se objeví, zadejte:
    1. Název parametru:`TimeRange`
    2. Typ parametru:`Time range picker`
    3. Požadované:`checked`
    4. Dostupné časové rozsahy: Poslední hodina, Posledních 12 hodin, Posledních 24 hodin, Posledních 48 hodin, Posledních 3 dní, Posledních 7 dní a Povolit výběr vlastního časového rozsahu
5. Chcete-li vytvořit parametr, zvolte "Uložit".

    ![Obrázek znázorňující vytvoření parametru časového rozsahu](./media/workbooks-time/time-settings.png)

Takto bude sešit vypadat v režimu čtení.

![Obrázek znázorňující parametr časového rozsahu v režimu čtení](./media/workbooks-time/parameters-time.png)

## <a name="referencing-a-time-parameter"></a>Odkazování na časový parametr
### <a name="via-bindings"></a>Přes vázání
1. Přidejte do sešitu ovládací prvek dotazu a vyberte prostředek Application Insights.
2. Většina ovládacích prvků sešitu podporuje výběr rozsahu _časového rozsahu._ Otevřete rozevírací seznam Časový `{TimeRange}` _rozsah_ a vyberte skupinu parametrů časového okruhu v dolní části.
3. Tím se sváže parametr časového rozsahu s časovým rozsahem grafu. Časový rozsah ukázkového dotazu je nyní Posledních 24 hodin.
4. Spuštění dotazu pro zobrazení výsledků

    ![Obrázek znázorňující parametr časového rozsahu odkazovaný pomocí vazeb](./media/workbooks-time/time-binding.png)

### <a name="in-kql"></a>V KQL
1. Přidejte do sešitu ovládací prvek dotazu a vyberte prostředek Application Insights.
2. V kql zadejte filtr časového oboru pomocí parametru:`| where timestamp {TimeRange}`
3. Tím se zrozšiřuje doba `| where timestamp > ago(1d)`vyhodnocení dotazu na , což je hodnota časového rozsahu parametru.
4. Spuštění dotazu pro zobrazení výsledků

    ![Obrázek znázorňující časový rozsah odkazovaný v KQL](./media/workbooks-time/time-in-code.png)

### <a name="in-text"></a>V textu 
1. Přidání textového ovládacího prvku do sešitu
2. Do markdownu zadejte`The chosen time range is {TimeRange:label}`
3. Zvolit _hotovou úpravu_
4. Ovládací prvek textu zobrazí text: _Zvolený časový rozsah je Posledních 24 hodin_

## <a name="time-parameter-options"></a>Možnosti časového parametru
| Parametr | Vysvětlení | Příklad |
| ------------- |:-------------|:-------------|
| `{TimeRange}` | Popisek časového rozsahu | Posledních 24 hodin |
| `{TimeRange:label}` | Popisek časového rozsahu | Posledních 24 hodin |
| `{TimeRange:value}` | Hodnota časového rozsahu | > před(1d) |
| `{TimeRange:query}` | Dotaz časového rozsahu | > před(1d) |
| `{TimeRange:start}` | Čas zahájení časového rozsahu | 3/20/2019 16:18 |
| `{TimeRange:end}` | Čas ukončení časového rozsahu | 3/21/2019 16:18 |
| `{TimeRange:grain}` | Zrnitost časového rozsahu | 30 m |


### <a name="using-parameter-options-in-a-query"></a>Použití možností parametrů v dotazu
```kusto
requests
| make-series Requests = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain}
```

## <a name="next-steps"></a>Další kroky

* [Začínáte](workbooks-visualizations.md) se učit další informace o sešitech, mnoho bohatých možností vizualizací.
* [Řízení](workbooks-access-control.md) a sdílení přístupu k prostředkům sešitu
