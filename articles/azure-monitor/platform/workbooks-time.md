---
title: Parametry času pro Azure Monitor sešity
description: Naučte se nastavit časové parametry, které uživatelům umožní nastavit časový kontext analýzy. Parametry času jsou používány téměř všemi sestavami.
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 37f2219313a0d4c533d587a765dd22d527a5910f
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92143445"
---
# <a name="workbook-time-parameters"></a>Parametry času sešitu

Časové parametry umožňují uživatelům nastavit časový kontext analýzy a používají je téměř všemi sestavami. Je poměrně jednoduché nastavit a použít – umožňuje autorům určit časové rozsahy, které se mají zobrazit v rozevíracím seznamu, včetně možnosti pro vlastní časové rozsahy. 

## <a name="creating-a-time-parameter"></a>Vytvoření parametru času
1. Začněte s prázdným sešitem v režimu úprav.
2. Vyberte možnost _přidat parametry_ z odkazů v rámci sešitu.
3. Klikněte na modré tlačítko _Přidat parametr_ .
4. V podokně nového parametru, které se objeví, zadejte:
    1. Název parametru: `TimeRange`
    2. Typ parametru: `Time range picker`
    3. Požadovanou `checked`
    4. Dostupné časové rozsahy: poslední hodina, posledních 12 hodin, posledních 24 hodin, poslední 48 hodin, poslední 3 dny, posledních 7 dní a povolený výběr vlastního časového rozsahu
5. Pokud chcete vytvořit parametr, klikněte na tlačítko Uložit na panelu nástrojů.

    ![Obrázek znázorňující vytvoření parametru časového rozsahu](./media/workbooks-time/time-settings.png)

V takovém případě bude sešit vypadat jako v režimu čtení.

![Obrázek znázorňující parametr časového rozsahu v režimu čtení](./media/workbooks-time/parameters-time.png)

## <a name="referencing-a-time-parameter"></a>Odkazování na časový parametr
### <a name="via-bindings"></a>Přes vazby
1. Přidejte do sešitu ovládací prvek dotazu a vyberte prostředek Application Insights.
2. Většina ovládacích prvků sešitu podporuje výběr oboru _časového rozsahu_ . Otevřete rozevírací seznam _časový rozsah_ a `{TimeRange}` v dolní části vyberte ve skupině parametry časového rozsahu.
3. Tím se naváže parametr časového rozsahu na časový rozsah grafu. Časový rozsah ukázkového dotazu je nyní za posledních 24 hodin.
4. Spusťte dotaz, aby se zobrazily výsledky.

    ![Obrázek znázorňující parametr časového rozsahu, na který se odkazuje pomocí vazeb](./media/workbooks-time/time-binding.png)

### <a name="in-kql"></a>V KQL
1. Přidejte do sešitu ovládací prvek dotazu a vyberte prostředek Application Insights.
2. Do KQL zadejte filtr oboru času pomocí parametru: `| where timestamp {TimeRange}`
3. Tím se rozšíří doba vyhodnocování dotazu na `| where timestamp > ago(1d)` , což je hodnota časového rozsahu parametru.
4. Spusťte dotaz, aby se zobrazily výsledky.

    ![Obrázek znázorňující časový rozsah, na který odkazuje KQL](./media/workbooks-time/time-in-code.png)

### <a name="in-text"></a>V textu 
1. Přidejte textový ovládací prvek do sešitu.
2. Do Markdownu zadejte `The chosen time range is {TimeRange:label}`
3. Zvolit _hotové úpravy_
4. Ovládací prvek text zobrazí text: _zvolený časový rozsah je posledních 24 hodin_ .

## <a name="time-parameter-options"></a>Možnosti parametrů času
| Parametr | Vysvětlení | Příklad |
| ------------- |:-------------|:-------------|
| `{TimeRange}` | Popisek časového rozsahu | Posledních 24 hodin |
| `{TimeRange:label}` | Popisek časového rozsahu | Posledních 24 hodin |
| `{TimeRange:value}` | Hodnota časového rozsahu | > před (1d) |
| `{TimeRange:query}` | Dotaz na časový rozsah | > před (1d) |
| `{TimeRange:start}` | Čas zahájení časového rozsahu | 3/20/2019 4:18 ODP. |
| `{TimeRange:end}` | Čas ukončení časového rozsahu | 3/21/2019 4:18 ODP. |
| `{TimeRange:grain}` | Zrnitosti časového rozsahu | 30 m |


### <a name="using-parameter-options-in-a-query"></a>Použití možností parametrů v dotazu
```kusto
requests
| make-series Requests = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain}
```

## <a name="next-steps"></a>Další kroky

* [Začínáme](./workbooks-overview.md#visualizations) se dozvědět více o seznámcích s mnoha různými možnostmi vizualizací.
* [Řízení](workbooks-access-control.md) a sdílení přístupu k prostředkům sešitu.