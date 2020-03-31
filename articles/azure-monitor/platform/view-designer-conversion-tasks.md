---
title: Návrhář zobrazení Azure Monitor u běžných úloh převodu sešitů
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 5559dac916262998d621b40757398088ec613609
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658740"
---
# <a name="view-designer-to-workbooks-conversion-common-tasks"></a>Zobrazení běžných úkolů převodu návrháře do sešitů
[Návrhář zobrazení](view-designer.md) je funkce Azure Monitoru, která umožňuje vytvářet vlastní zobrazení, která vám pomohou vizualizovat data v pracovním prostoru Log Analytics pomocí grafů, seznamů a časových os. Jsou postupně ukončovány a nahrazovány sešity, které poskytují další funkce. Tento článek podrobně popisuje úkoly, které jsou běžné při převodu zobrazení na sešity.


## <a name="quickstart-with-preset-view-designer-templates"></a>Úvodní příručka s přednastavenými šablonami návrháře zobrazení

Sešity v pracovních prostorech Analýzy protokolů už mají šablony, které odpovídají některým zobrazením v návrháři zobrazení. V části Průvodce **návrháře zobrazení** vyberte **Průvodce přechodem návrháře zobrazení,** abyste se dozvěděli o svých možnostech, nebo vyberte jednu z přednastavených šablon.

![Ukázkové šablony](media/view-designer-conversion-tasks/templates.png)

## <a name="enabling-time-range-filter"></a>Povolení filtru časového rozsahu
Návrhář zobrazení má předdefinovaný výchozí filtr časového rozsahu, ale v sešitech toto nastavení není ve výchozím nastavení povoleno. Sešity umožňují uživatelům vytvářet vlastní filtry časového rozsahu, které by mohly být vhodnější pro jejich protokoly dat. Postup generování filtru je uveden níže:

Vyberte možnost **Přidat parametry.** Výchozí **styl** je nastaven na *pilulky*.

![Přidat param](media/view-designer-conversion-tasks/add-param.png)

 Vyberte tlačítko **Přidat parametr.**

![Přidat parametr](media/view-designer-conversion-tasks/add-parameter.png)

Z nabídky postranního panelu zadejte do textového pole **Název parametru** *položku Časový rozsah*. Nastavit **typ parametru** jako *výběr časového rozsahu*. Zaškrtněte **políčko Povinné?**

![Nabídka parametrů](media/view-designer-conversion-tasks/parameter-menu.png)

Uložte parametr v levém horním rohu nabídky postranního panelu. Rozevírací nabídku můžete ve výchozím nastavení ponechat jako *odnastavenou* nebo vybrat výchozí hodnotu **TimeRange,** například *24 hodin*. Vyberte **Hotovo úpravy**.

Parametry lze použít v dotazech přidáním {} složené závorky kolem názvu parametru. Další podrobnosti o parametrech naleznete v [dokumentaci k sešitům o parametrech](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/Parameters/Parameters.md).

## <a name="updating-queries-with-the-timerange-parameter"></a>Aktualizace dotazů pomocí parametru TimeRange

### <a name="option-1-select-timerange-from-the-time-range-dropdown"></a>Možnost 1: V rozevíracím období vyberte časový rozsah.

![Časový parametr](media/view-designer-conversion-tasks/time-parameter.png)

### <a name="option-2-update-your-log-queries"></a>Možnost 2: Aktualizace dotazů protokolu

Do dotazu přidejte `| where TimeGenerated {TimeRange}` řádek: jako v následujícím příkladu:

Původní dotaz
```KQL
search * 
| summarize count() by Type
```

Aktualizovaný dotaz
```KQL
search * 
| where TimeGenerated {TimeRange} 
| summarize count() by Type
```

## <a name="including-a-list"></a>Včetně seznamu
Většina zobrazení návrháře zobrazení obsahuje seznam a tento standardní seznam můžete reprodukovat v sešitu.

![Seznam dlaždic](media/view-designer-conversion-tasks/tile-list.png)

Vizualizaci přidáte kliknutím na **Přidat dotaz** z možností buňky.

![Přidat param](media/view-designer-conversion-tasks/add-param.png)

Návrhář zobrazení používá výchozí dotaz, který odpovídá syntaxi z původního příkladu. To lze aktualizovat změnou dotazu na aktualizovaný formulář jako v následujícím příkladu:

Původní dotaz
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

Aktualizovaný dotaz
```KQL
search * 
| summarize Count = count() by Type
```

Tím se vygeneruje seznam, který vypadá podobně jako následující:

![Příklad seznamu](media/view-designer-conversion-tasks/list-example.png)

## <a name="enabling-sparklines"></a>Povolení minigrafů
Běžnou funkcí pro mřížky je přidání minigrafů pro shrnutí různých datových vzorů v průběhu času. Návrhář zobrazení nabízí funkci **Povolit minigrafy** pro všechny seznamy, stejně jako sešity. Chcete-li do dat zahrnout minigrafy, které odpovídají návrháři zobrazení, připojte data k původnímu dotazu jako v následujícím příkladu:

Původní dotaz
```KQL
search *
| summarize AggregatedValue = count() by Type) on Type
```

Aktualizovaný dotaz
```KQL
search * 
| summarize AggregatedValue = count() by Type
| join kind = inner (search * 
    | make-series Trend = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type) on Type
| project Type, AggregatedValue, Trend
```

Vyberte **Nastavení sloupce**.
![Nastavení sloupců](media/view-designer-conversion-tasks/column-settings.png)

Aktualizujte rozevírací seznam **Vykreslovače sloupců** tak, aby se z *ní stala oblast Spark*.
![Minigrafy](media/view-designer-conversion-tasks/sparkline.png)

Uložte nastavení a spusťte dotaz znovu, chcete-li aktualizovat tabulku tak, aby obsahovala minigraf.

Výsledná mřížka bude vypadat ![podobně jako následující: Příklad minigrafu](media/view-designer-conversion-tasks/sparkline-example.png)

## <a name="advanced-cell-settings"></a>Upřesňující nastavení buněk
Chcete-li zrcadlit návrháře zobrazení, můžete provádět úkoly, jako je změna velikosti buněk sešitu nebo přidání kolíků a externích odkazů do protokolů.

Chcete-li získat přístup **k pokročilým nastavením,** vyberte ikonu ozubeného kola v dolní části každé buňky.

![Upřesnit nastavení](media/view-designer-conversion-tasks/advanced-settings.png)

Zobrazí se nabídka s různými možnostmi:

![Upřesňující nastavení nastavení](media/view-designer-conversion-tasks/advanced-settings-settings.png)

Chcete-li přidat špendlík a odkaz na externí dotaz, zaškrtněte příslušná políčka. Chcete-li do buňky přidat název, zadejte požadovaný název do části **Název grafu.**

Ve výchozím nastavení je každá buňka sešitů nastavena tak, aby zabíjela celou šířku stránky, ale můžete ji upravit změnou velikosti **buňky** pod kartou Styl v nabídce **Upřesnit nastavení.**

![Styl upřesňujících nastavení](media/view-designer-conversion-tasks/advanced-settings-style.png)

 
## <a name="additional-parameters"></a>Další parametry
Vyberte **Přidat parametr,** chcete-li v sešitu vytvořit nový parametr. 

Chcete-li vybrat předplatné, zadejte *odběr* do pole **Název parametru** v boční nabídce a v rozevíracím seznamu **Typ předplatného** vyberte *Výběr předplatného.*

![Nabídka předplatného](media/view-designer-conversion-tasks/subscription-filter.png)

Chcete-li vybrat zdroj, zadejte *zdroj* do pole **Název parametru** v boční nabídce a v rozevíracím seznamu **Typ parametru** vyberte *Výběr zdrojů.*

![Nabídka Zdroje](media/view-designer-conversion-tasks/resource-filter.png)

Tím vložíte rozevírací nabídky, které vám umožní přístup k různým předplatným a prostředkům.

![Rozbalovací seznam prostředků předplatného](media/view-designer-conversion-tasks/subscription-resource.png)


## <a name="next-steps"></a>Další kroky
- [Převody dlaždic](view-designer-conversion-tiles.md)
