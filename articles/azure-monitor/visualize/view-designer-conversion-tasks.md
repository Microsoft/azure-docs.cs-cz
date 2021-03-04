---
title: Azure Monitor návrháře zobrazení na sešity převod běžných úloh
description: Běžné úlohy při přechodu ze zobrazení do sešitů v Azure Monitor.
author: austonli
ms.author: aul
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: d479e64620519491ba4847349ea33820747fcea7
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043469"
---
# <a name="view-designer-to-workbooks-conversion-common-tasks"></a>Úlohy návrháře zobrazení pro převod na sešity běžné úkoly
[Návrhář zobrazení](view-designer.md) je funkce Azure monitor, která umožňuje vytvářet vlastní zobrazení, která vám pomohou vizualizovat data v pracovním prostoru Log Analytics s grafy, seznamy a časovými osami. Budou postupně vyladěny a nahrazeny sešity, které poskytují další funkce. Tento článek podrobně popisuje úlohy, které jsou běžné při převádění zobrazení na sešity.


## <a name="quickstart-with-preset-view-designer-templates"></a>Rychlý Start s přednastavenými šablonami návrháře zobrazení

Sešity v pracovních prostorech v Log Analytics již mají šablony vytvořené tak, aby odpovídaly některým zobrazením v Návrháři zobrazení. V kategorii **příručky návrháře zobrazení** vyberte **Zobrazit průvodce přechodem návrháře** , kde se dozvíte o možnostech nebo vyberte jednu z přednastavených šablon.

![Příklady šablon](media/view-designer-conversion-tasks/templates.png)

## <a name="enabling-time-range-filter"></a>Povoluje se filtr časového rozsahu.
Návrhář zobrazení má integrovaný výchozí filtr časového rozsahu, ale v sešitech toto nastavení není ve výchozím nastavení povolené. Pracovní sešity umožňují uživatelům vytvářet vlastní filtry časového rozsahu, které by mohly být pro své datové protokoly užitečnější. Níže je uvedený postup pro vygenerování filtru:

Vyberte možnost **přidat parametry** . Výchozí **styl** je nastaven na *Pills*.

![Přidat param](media/view-designer-conversion-tasks/add-param.png)

 Vyberte tlačítko **Přidat parametr** .

![Přidat parametr](media/view-designer-conversion-tasks/add-parameter.png)

V nabídce bočního panelu zadejte do textového pole **název parametru** *TimeRange*. Nastavte **typ parametru** jako *časový rozsah pro výběr*. Zaškrtněte políčko **požadováno?** .

![Nabídka parametrů](media/view-designer-conversion-tasks/parameter-menu.png)

Uložte parametr v levém horním rohu nabídky bočního panelu. Rozevírací seznam můžete *ponechat ve výchozím nastavení, nebo* vyberte výchozí hodnotu **TimeRange** , například *24 hodin*. Vyberte **hotové úpravy**.

Parametry lze použít v dotazech přidáním složených závorek {} kolem názvu parametru. Další podrobnosti o parametrech najdete v dokumentaci k [parametrům v dokumentaci k pracovním sešitům](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/Parameters/Parameters.md).

## <a name="updating-queries-with-the-timerange-parameter"></a>Aktualizace dotazů pomocí parametru TimeRange

### <a name="option-1-select-timerange-from-the-time-range-dropdown"></a>Možnost 1: vyberte TimeRange z rozevíracího seznamu časový rozsah.

![Časový parametr](media/view-designer-conversion-tasks/time-parameter.png)

### <a name="option-2-update-your-log-queries"></a>Možnost 2: aktualizace dotazů protokolu

Do dotazu přidejte řádek: `| where TimeGenerated {TimeRange}` jako v následujícím příkladu:

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

## <a name="including-a-list"></a>Zahrnutí seznamu
Většina zobrazení návrháře zobrazení obsahuje seznam a tento standardní seznam můžete v sešitu reprodukován.

![Seznam dlaždic](media/view-designer-conversion-tasks/tile-list.png)

Přidejte vizualizaci kliknutím na **Přidat dotaz** z možností buňky.

![Přidat param](media/view-designer-conversion-tasks/add-param.png)

Návrhář zobrazení využívá výchozí dotaz, který se shoduje s syntaxí z původního příkladu. Dá se aktualizovat tak, že změníte dotaz na aktualizovaný formulář, jak je uvedeno v následujícím příkladu:

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

Vygeneruje se seznam, který vypadá podobně jako následující:

![Příklad seznamu](media/view-designer-conversion-tasks/list-example.png)

## <a name="enabling-sparklines"></a>Povolení minigrafů
Běžnou funkcí pro mřížky je přidání minigrafů za účelem sumarizace různých vzorů dat v průběhu času. Návrhář zobrazení nabízí funkci **Povolit minigrafy** pro všechny seznamy, stejně jako sešity. Chcete-li zahrnout minigrafy do vašich dat, která odpovídají Návrháři zobrazení, připojte data s původním dotazem jako v následujícím příkladu:

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

Aktualizujte rozevírací seznam **rendereru sloupce** tak, aby byl *oblastí Spark*.
![Minigrafy](media/view-designer-conversion-tasks/sparkline.png)

Uložte nastavení a spusťte dotaz znovu, aby se tabulka aktualizovala tak, aby obsahovala minigraf.

Výsledná mřížka bude vypadat podobně jako v následujícím ![ příkladu: minigraf – příklad](media/view-designer-conversion-tasks/sparkline-example.png)

## <a name="advanced-cell-settings"></a>Pokročilá nastavení buněk
Pro návrháře zobrazení zrcadlení můžete provádět úkoly, jako je například změna velikosti buněk sešitu nebo přidávání kódů PIN a externích odkazů do protokolů.

Pokud chcete získat přístup k **rozšířeným nastavením** , vyberte ikonu ozubeného kolečka v dolní části každé buňky.

![Rozšířená nastavení](media/view-designer-conversion-tasks/advanced-settings.png)

Zobrazí se nabídka s různými možnostmi:

![Nastavení rozšířeného nastavení](media/view-designer-conversion-tasks/advanced-settings-settings.png)

Chcete-li přidat kód PIN a odkaz na externí dotaz, zaškrtněte příslušná políčka. Chcete-li do buňky přidat název, zadejte požadovaný název do oddílu **Nadpis grafu** .

Ve výchozím nastavení je každá buňka sešitů nastavená tak, aby zabrala celou šířku stránky, ale můžete ji upravit tak, že na kartě **styl** v nabídce **Upřesnit nastavení** narozdělíte velikost buňky dolů.

![Upřesnit nastavení – styl](media/view-designer-conversion-tasks/advanced-settings-style.png)

 
## <a name="additional-parameters"></a>Další parametry
Vyberte **Přidat parametr** a vytvořte nový parametr v sešitu. 

Pokud chcete vybrat předplatné, zadejte do pole **název parametru** v nabídce na straně *předplatné* a v rozevírací nabídce **typ parametru** vyberte *Výběr předplatného* .

![Nabídka odběr](media/view-designer-conversion-tasks/subscription-filter.png)

Chcete-li vybrat prostředek, zadejte do pole **název parametru** v postranní nabídce *prostředek* a vyberte možnost *Výběr prostředku* z rozevíracího seznamu **typ parametru** .

![Nabídka prostředků](media/view-designer-conversion-tasks/resource-filter.png)

Tím budou vloženy rozevírací seznamy, které vám umožní přístup k různým předplatným a prostředkům.

![Rozevírací seznam prostředků odběru](media/view-designer-conversion-tasks/subscription-resource.png)


## <a name="next-steps"></a>Další kroky
- [Převody dlaždic](view-designer-conversion-tiles.md)
