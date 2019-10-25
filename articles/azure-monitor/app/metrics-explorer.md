---
title: Zkoumání metrik v Azure Application Insights | Microsoft Docs
description: Jak interpretovat grafy v Průzkumníkovi metrik a jak přizpůsobit okna Průzkumníka metrik.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 01/22/2019
ms.openlocfilehash: b0831ff500ba4cbe71dae6251fd960f6c96c0fe5
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820785"
---
# <a name="exploring-metrics-in-application-insights"></a>Zkoumání metrik v Application Insights
Metriky v [Application Insights][start] jsou měřené hodnoty a počty událostí, které se odesílají v telemetrie z vaší aplikace. Vám pomůžou detekovat problémy s výkonem a sledovat trendy v tom, jak se vaše aplikace používá. Existuje celá řada standardních metrik a můžete také vytvořit vlastní metriky a události.

> [!NOTE]
> Tento článek popisuje klasické prostředí Průzkumníka metrik, které je aktuálně zastaralé a bude nakonec vyřazeno. Doporučujeme, abyste si vyrezervovali nové prostředí popsané v [tomto článku](../platform/metrics-charts.md).

Metriky a počty událostí se zobrazují v grafech agregovaných hodnot, jako jsou součty, průměry nebo počty.

Zde je ukázková sada grafů:

![](./media/metrics-explorer/01-overview.png)

Grafy metriky najdete všude na Application Insightsovém portálu. Ve většině případů je lze přizpůsobit a do okna můžete přidat další grafy. V okně Přehled klikněte na Procházet a podrobnější grafy (které mají názvy jako "servery") nebo klikněte na **Průzkumník metrik** . otevře se nové okno, ve kterém můžete vytvořit vlastní grafy.

## <a name="time-range"></a>Časové rozmezí
Časový rozsah, který je popsaný v grafech nebo Gridech, můžete změnit v jakémkoli okně.

![Otevřete okno Přehled aplikace v Azure Portal](./media/metrics-explorer/03-range.png)

Pokud jste očekávali nějaká data, která se ještě nezobrazovala, klikněte na aktualizovat. Grafy se aktualizují v intervalech, ale intervaly jsou delší pro větší časové rozsahy. Může chvíli trvat, než se data podávají prostřednictvím kanálu analýzy do grafu.

Chcete-li přiblížit část grafu, přetáhněte ji na ni:

![Táhněte mezi část grafu.](./media/metrics-explorer/12-drag.png)

Kliknutím na tlačítko zrušit přiblížení ho obnovíte.

## <a name="granularity-and-point-values"></a>Hodnoty členitosti a bodů
Najeďte myší na graf, abyste zobrazili hodnoty metrik v daném okamžiku.

![Najetí myší na graf](./media/metrics-explorer/02-focus.png)

Hodnota metriky v určitém bodě je agregována v předchozím intervalu vzorkování.

V horní části okna se zobrazí interval vzorkování nebo "členitost".

![Záhlaví okna](./media/metrics-explorer/11-grain.png)

Členitost můžete upravit v okně časového rozsahu:

![Záhlaví okna](./media/metrics-explorer/grain.png)

Dostupné členitosti závisí na časovém rozsahu, který jste vybrali. Explicitní členitost jsou alternativou k "automatickému" členitému časovému rozsahu.


## <a name="editing-charts-and-grids"></a>Úpravy grafů a mřížek
Přidání nového grafu do okna:

![V Průzkumník metrik klikněte na přidat graf.](./media/metrics-explorer/04-add.png)

Vyberte **Upravit** v existujícím nebo novém grafu pro úpravu zobrazených informací:

![Vyberte jednu nebo víc metrik.](./media/metrics-explorer/08-select.png)

V grafu můžete zobrazit víc než jednu metriku, i když existují nějaká omezení kombinací, která se dají zobrazit společně. Jakmile vyberete jednu metriku, některé z ostatních jsou zakázané.

Pokud jste do vaší aplikace nahlásili [vlastní metriky][track] (volání TrackMetric a TrackEvent), zobrazí se tady.

## <a name="segment-your-data"></a>Segmentace dat
Metriku můžete rozdělit podle vlastností – například pro porovnání zobrazení stránky v klientech s různými operačními systémy.

Vyberte graf nebo mřížku, přepněte na seskupení a vyberte vlastnost, podle které chcete seskupit:

![Vyberte seskupení zapnuto a pak nastavte vybrat vlastnost v Group by.](./media/metrics-explorer/15-segment.png)

> [!NOTE]
> Když použijete seskupování, typy plošných a pruhových grafů poskytují skládané zobrazení. To je vhodné, pokud je agregační metoda součtem. Ale v případě, že je typ agregace průměrný, vyberte typy zobrazení čára nebo mřížka.
>
>

Pokud jste ve své aplikaci nahlásili [vlastní metriky][track] a obsahují hodnoty vlastností, budete moct vybrat vlastnost v seznamu.

Je graf pro segmentovaná data moc malý? Upravit svou výšku:

![Nastavení posuvníku](./media/metrics-explorer/18-height.png)

## <a name="aggregation-types"></a>Typy agregace
V legendě na straně stran ve výchozím nastavení se obvykle zobrazuje agregovaná hodnota za období grafu. Pokud najedete myší na graf, zobrazí se hodnota v tomto okamžiku.

Každý datový bod v grafu je agregovaný z hodnot dat přijatých v předchozím intervalu vzorkování nebo "členitosti". Členitost je zobrazena v horní části okna a liší se celkovou časovou osou grafu.

Metriky lze agregovat různými způsoby:

* **Count** je počet událostí přijatých v intervalu vzorkování. Používá se pro události, jako jsou požadavky. Variace ve výšce grafu označují variace v rychlosti, při které dojde k událostem. Všimněte si ale, že číselná hodnota se po změně intervalu vzorkování mění.
* **Sum** sečte hodnoty všech datových bodů přijatých během intervalu vzorkování nebo období grafu.
* **Průměr** vydělí součet počtem datových bodů přijatých v průběhu intervalu.
* Pro počty uživatelů a účtů se používají **jedinečné** počty. Během intervalu vzorkování nebo za období grafu zobrazuje obrázek počet různých uživatelů, kteří se v daném čase viděli.
* **%** – procentuální verze jednotlivých agregací se používají pouze u segmentických grafů. Celkem vždy přidává až 100% a graf zobrazuje relativní podíl různých komponent celku.

    ![Agregace procenta](./media/metrics-explorer/percentage-aggregation.png)

### <a name="change-the-aggregation-type"></a>Změna typu agregace

![Upravte graf a pak vyberte agregace.](./media/metrics-explorer/05-aggregation.png)

Výchozí metoda pro každou metriku se zobrazí při vytvoření nového grafu nebo při nevýběru všech metrik:

![Zrušit výběr všech metrik pro zobrazení výchozích hodnot](./media/metrics-explorer/06-total.png)

## <a name="pin-y-axis"></a>Připnout osu Y 
Ve výchozím nastavení se v grafu zobrazí hodnoty osy Y začínající od nuly do maximální hodnoty v rozsahu dat, aby se dala vizuální reprezentace hodnot. V některých případech ale může být zajímavější a vizuálně kontrolovat menší změny v hodnotách. V případě vlastního nastavení použijte funkci pro úpravu rozsahu osy Y k připnutí minimální nebo maximální hodnoty osy Y na požadované místo.
Kliknutím na zaškrtávací políčko Upřesnit nastavení zobrazte nastavení rozsahu osy Y.

![Klikněte na Upřesnit nastavení, vyberte možnost vlastní rozsah a zadejte minimální hodnoty maxima.](./media/metrics-explorer/y-axis-range.png)

## <a name="filter-your-data"></a>Filtrování dat
Chcete-li zobrazit jenom metriky pro vybranou sadu hodnot vlastností:

![Klikněte na filtr, rozbalte vlastnost a zaškrtněte některé hodnoty.](./media/metrics-explorer/19-filter.png)

Pokud nevyberete žádné hodnoty pro konkrétní vlastnost, je stejná jako při jejich výběru vše: pro tuto vlastnost neexistuje žádný filtr.

Všimněte si počtu událostí společně s každou hodnotou vlastnosti. Když vyberete hodnoty jedné vlastnosti, počty vedle ostatních hodnot vlastností se upraví.

Filtry se použijí na všechny grafy v okně. Pokud chcete použít jiné filtry pro různé grafy, vytvořte a uložte různé okna metriky. Pokud chcete, můžete připnout grafy z různých oken na řídicí panel, abyste je viděli společně.

### <a name="remove-bot-and-web-test-traffic"></a>Odebrání provozu robotů a webových testů
Používejte filtr **reálného nebo syntetického provozu** a ověřte **skutečné**.

Můžete také filtrovat podle **zdroje syntetického provozu**.

### <a name="to-add-properties-to-the-filter-list"></a>Přidání vlastností do seznamu filtrů
Chcete telemetrii vyfiltrovat v kategorii podle vlastního výběru? Například možná rozdělíte uživatele do různých kategorií a chcete segmentovat data podle těchto kategorií.

[Vytvořte vlastní vlastnost](../../azure-monitor/app/api-custom-events-metrics.md#properties). Nastavte ho v [inicializátoru telemetrie](../../azure-monitor/app/api-custom-events-metrics.md#defaults) tak, aby se zobrazil ve všech telemetrii – včetně standardní telemetrie odesílané různými moduly SDK.

## <a name="edit-the-chart-type"></a>Upravit typ grafu
Všimněte si, že můžete přepínat mezi mřížkami a grafy:

![Vyberte mřížku nebo graf a pak zvolte typ grafu.](./media/metrics-explorer/16-chart-grid.png)

## <a name="save-your-metrics-blade"></a>Uložit okno metriky
Když jste vytvořili nějaké grafy, uložte je jako oblíbenou položku. Můžete zvolit, jestli se má sdílet s ostatními členy týmu, pokud použijete účet organizace.

![Zvolit oblíbenou položku](./media/metrics-explorer/21-favorite-save.png)

Pokud chcete okno znovu zobrazit, **přejděte do okna Přehled** a otevřete oblíbené:

![V okně Přehled vyberte oblíbené.](./media/metrics-explorer/22-favorite-get.png)

Pokud jste při uložení zvolili relativní časový rozsah, okno se aktualizuje o nejnovější metriky. Pokud jste zvolili absolutní časový rozsah, budou se stejná data zobrazovat pokaždé, když budou.

## <a name="reset-the-blade"></a>Obnovit okno
Pokud okno upravíte, ale pak se chcete vrátit k původní uložené sadě, stačí kliknout na resetovat.

![V tlačítkách v horní části Průzkumníka metriky](./media/metrics-explorer/17-reset.png)

## <a name="live-metrics-stream"></a>Živý stream metrik

Pro mnohem lepší pohled na telemetrii otevřete [Live Stream](live-stream.md). U většiny metrik se zobrazí několik minut kvůli procesu agregace. Naproti tomu živé metriky jsou optimalizované pro nízkou latenci. 

## <a name="set-alerts"></a>Nastavení výstrah
Pokud chcete dostávat oznámení e-mailem o neobvyklých hodnotách jakékoli metriky, přidejte upozornění. Můžete zvolit odeslání e-mailu správcům účtu nebo určitým e-mailovým adresám.

![V Průzkumník metrik vyberte pravidla výstrah, přidat výstrahu.](./media/metrics-explorer/appinsights-413setMetricAlert.png)

[Přečtěte si další informace o výstrahách][alerts].


## <a name="continuous-export"></a>Souvislý export
Pokud chcete data průběžně exportovat, abyste je mohli zpracovat externě, zvažte použití [průběžného exportu](../../azure-monitor/app/export-telemetry.md).

### <a name="power-bi"></a>Power BI
Pokud chcete ještě širší zobrazení dat, můžete je [exportovat do Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx).

## <a name="analytics"></a>Analýzy
[Analýza](../../azure-monitor/app/analytics.md) je efektivnější způsob analýzy telemetrie pomocí výkonného dotazovacího jazyka. Použijte ho, pokud chcete kombinovat nebo vypočítat výsledky z metrik nebo udělat podrobný průzkum nedávného výkonu vaší aplikace. 

Z grafu metriky můžete kliknutím na ikonu analýza přejít přímo na ekvivalentní dotaz Analytics.

## <a name="troubleshooting"></a>Řešení potíží
*V grafu nevidím žádná data.*

* Filtry se použijí na všechny grafy v okně. Ujistěte se, že při zaměření na jeden graf jste nastavili filtr, který vyloučí všechna data na jiném.

    Pokud chcete nastavit jiné filtry v různých grafech, vytvořte je v různých oknech a uložte je jako samostatné oblíbené položky. Pokud chcete, můžete je připnout na řídicí panel, abyste je viděli společně.
* Pokud graf rozřadíte podle vlastnosti, která není definovaná u metriky, nebudete mít v grafu nic. Zkuste zrušit zaškrtnutí u možnosti Seskupit podle nebo vyberte jinou vlastnost seskupení.
* Údaje o výkonu (CPU, rychlost v/v a tak dále) jsou k dispozici pro webové služby Java, desktopové aplikace Windows, [webové aplikace a služby IIS, pokud nainstalujete monitorování stavu](../../azure-monitor/app/monitor-performance-live-website-now.md)a [Azure Cloud Services](../../azure-monitor/app/app-insights-overview.md). Není k dispozici pro Azure websites.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Další kroky
* [Monitorování využití pomocí Application Insights](../../azure-monitor/app/usage-overview.md)
* [Pomocí diagnostického vyhledávání](../../azure-monitor/app/diagnostic-search.md)

<!--Link references-->

[alerts]: ../../azure-monitor/app/alerts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
