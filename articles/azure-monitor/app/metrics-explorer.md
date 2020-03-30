---
title: Zkoumání metrik v přehledech aplikací Azure | Dokumenty společnosti Microsoft
description: Jak interpretovat grafy v průzkumníku metrik a jak přizpůsobit okna průzkumníka metrik.
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: f85a8fe79e7f4f820d7c0e5b942730305e892095
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275890"
---
# <a name="exploring-metrics-in-application-insights"></a>Zkoumání metrik v přehledech aplikací
Metriky v [Application Insights][start] jsou naměřené hodnoty a počty událostí, které jsou odesílány v telemetrii z vaší aplikace. Pomáhají vám rozpoznat problémy s výkonem a sledovat trendy v tom, jak se vaše aplikace používá. Existuje široká škála standardních metrik a můžete také vytvořit vlastní metriky a události.

> [!NOTE]
> Tento článek popisuje klasické metriky explorer prostředí, které je aktuálně zastaralé a nakonec bude vyřazen. Doporučujeme rezervovat nové prostředí, které je popsáno v [tomto článku](../platform/metrics-charts.md).

Metriky a počty událostí jsou zobrazeny v grafech souhrnných hodnot, jako jsou součty, průměry nebo počty.

Tady je ukázková sada grafů:

![](./media/metrics-explorer/01-overview.png)

Grafy metrik najdete všude na portálu Application Insights. Ve většině případů je lze přizpůsobit a do okna můžete přidat další grafy. V okně Přehled klikněte na podrobnější grafy (které mají názvy jako "Servery") nebo kliknutím na **Průzkumník metrik** otevřete nové okno, ve kterém můžete vytvářet vlastní grafy.

## <a name="time-range"></a>Časové rozmezí
Časový rozsah, na který se vztahují grafy nebo mřížky, můžete změnit na libovolném okně.

![Otevření okna přehledu vaší aplikace na webu Azure Portal](./media/metrics-explorer/03-range.png)

Pokud očekáváte některá data, která se ještě nezobrazila, klikněte na Aktualizovat. Grafy se aktualizují v intervalech, ale intervaly jsou delší pro větší časové rozsahy. Může chvíli trvat, než data přejdou prostřednictvím kanálu analýzy do grafu.

Chcete-li přiblížit část grafu, přetáhněte přes něj:

![Táhněte přes část grafu.](./media/metrics-explorer/12-drag.png)

Klepnutím na tlačítko Vrátit lupu jej obnovte.

## <a name="granularity-and-point-values"></a>Zrnitost a bodové hodnoty
Najeďte myší na graf a zobrazte hodnoty metrik v tomto bodě.

![Najeďte myší na graf](./media/metrics-explorer/02-focus.png)

Hodnota metriky v určitém bodě je agregována v předchozím intervalu vzorkování.

V horní části kotouče je zobrazen interval odběru vzorků nebo "rozlišovací schopnost".

![Záhlaví čepele.](./media/metrics-explorer/11-grain.png)

Rozlišovací schopnost můžete nastavit v noži časového rozsahu:

![Záhlaví čepele.](./media/metrics-explorer/grain.png)

Dostupné granuloty závisí na vybraném časovém rozsahu. Explicitní rozlišovací schopnosti jsou alternativami k "automatické" rozlišovací schopnost i pro časový rozsah.


## <a name="editing-charts-and-grids"></a>Úpravy grafů a mřížek
Přidání nového grafu do okna:

![V Průzkumníku metrik zvolte Přidat graf.](./media/metrics-explorer/04-add.png)

Vyberte **Upravit** v existujícím nebo novém grafu, chcete-li upravit, co zobrazuje:

![Výběr jedné nebo více metrik](./media/metrics-explorer/08-select.png)

V grafu můžete zobrazit více než jednu metriku, ale existují omezení týkající se kombinací, které lze zobrazit společně. Jakmile zvolíte jednu metriku, některé jiné jsou zakázány.

Pokud jste do aplikace zakódovali [vlastní metriky][track] (volání na TrackMetric a TrackEvent), budou zde uvedeny.

## <a name="segment-your-data"></a>Segmentace dat
Metriku můžete rozdělit podle vlastnosti – například můžete porovnat zobrazení stránek u klientů s různými operačními systémy.

Vyberte graf nebo mřížku, zapněte seskupování a vyberte vlastnost, podle které chcete seskupit:

![Vyberte Seskupování zapnuto a pak nastavte vlastnost v nabídce Seskupit podle.](./media/metrics-explorer/15-segment.png)

> [!NOTE]
> Při použití seskupení poskytují typy plošného a pruhového grafu skládané zobrazení. To je vhodné, kde agregace metoda je Sum. Ale tam, kde typ agregace je Průměr, zvolte čára nebo mřížka typy zobrazení.
>
>

Pokud jste do aplikace zakódovali [vlastní metriky][track] a obsahují hodnoty vlastností, budete moct vybrat vlastnost v seznamu.

Je graf pro segmentovaná data příliš malý? Nastavte jeho výšku:

![Nastavení posuvníku](./media/metrics-explorer/18-height.png)

## <a name="aggregation-types"></a>Typy agregace
Legenda na straně ve výchozím nastavení obvykle zobrazuje agregovotní hodnotu za období grafu. Pokud na graf najedete, zobrazí se hodnota v tomto bodě.

Každý datový bod v grafu je souhrnem datových hodnot přijatých v předchozím intervalu vzorkování nebo "rozlišovací schopnost". Členitost je zobrazena v horní části okna a liší se v závislosti na celkové časové ose grafu.

Metriky lze agregovat různými způsoby:

* **Count** je počet událostí přijatých v intervalu vzorkování. Používá se pro události, jako jsou požadavky. Změny výšky grafu označují odchylky v rychlosti výskytu událostí. Všimněte si však, že číselná hodnota se změní při změně intervalu vzorkování.
* **Součet** sečte hodnoty všech datových bodů přijatých v intervalu vzorkování nebo období grafu.
* **Průměr** vydělí součet počtem datových bodů přijatých v průběhu intervalu.
* **Jedinečné** počty se používají pro počty uživatelů a účtů. Během intervalu vzorkování nebo během období grafu obrázek zobrazuje počet různých uživatelů, kteří byli v té době vidět.
* **%**- procentuální verze každé agregace se používají pouze u segmentovaných grafů. Součet vždy přidá až 100 % a graf zobrazuje relativní příspěvek různých složek celkového součtu.

    ![Agregace procent](./media/metrics-explorer/percentage-aggregation.png)

### <a name="change-the-aggregation-type"></a>Změna typu agregace

![Úprava grafu a výběr agregace](./media/metrics-explorer/05-aggregation.png)

Výchozí metoda pro každou metriku se zobrazí při vytváření nového grafu nebo při odznačení všech metrik:

![Zrušením zaškrtnutí všech metrik zobrazíte výchozí hodnoty](./media/metrics-explorer/06-total.png)

## <a name="pin-y-axis"></a>Osa Y pinu 
Ve výchozím nastavení graf zobrazuje hodnoty osy Y od nuly do maximálních hodnot v oblasti dat, aby se poskytla vizuální reprezentace kvantových hodnot. Ale v některých případech více než kvantové by mohlo být zajímavé vizuálně zkontrolovat drobné změny hodnot. Pro vlastní nastavení, jako je tento, použijte funkci úprav rozsahu osy Y k připnutí minimální nebo maximální hodnoty osy Y na požadované místo.
Klikněte na zaškrtávací políčko "Upřesnit nastavení", abyste zoínili nastavení rozsahu osy Y

![Klikněte na Upřesnit nastavení, vyberte Vlastní rozsah a zadejte maximální hodnoty min.](./media/metrics-explorer/y-axis-range.png)

## <a name="filter-your-data"></a>Filtrování dat
Chcete-li zobrazit pouze metriky pro vybranou sadu hodnot vlastností:

![Klikněte na Filtr, rozbalte vlastnost a zkontrolujte některé hodnoty.](./media/metrics-explorer/19-filter.png)

Pokud nevyberete žádné hodnoty pro určitou vlastnost, je to stejné jako jejich výběr všech: v této vlastnosti není žádný filtr.

Všimněte si počty událostí vedle každé hodnoty vlastnosti. Když vyberete hodnoty jedné vlastnosti, upraví se počty vedle jiných hodnot vlastností.

Filtry platí pro všechny grafy v okně. Pokud chcete, aby se u různých grafů aplikovaly různé filtry, vytvořte a uložte různé metrické listy. Pokud chcete, můžete grafy z různých listů připnout na řídicí panel, abyste je mohli vidět vedle sebe.

### <a name="remove-bot-and-web-test-traffic"></a>Odebrání testovacího provozu robotů a webových testů
Použijte filtr **Skutečný nebo syntetický provoz** a zkontrolujte **Real**.

Můžete také filtrovat podle **zdroje syntetického provozu**.

### <a name="to-add-properties-to-the-filter-list"></a>Přidání vlastností do seznamu filtrů
Chcete filtrovat telemetrii v kategorii podle vlastního výběru? Možná například rozdělíte uživatele do různých kategorií a chcete data segmentovat podle těchto kategorií.

[Vytvořte si vlastní vlastnost](../../azure-monitor/app/api-custom-events-metrics.md#properties). Nastavte ji v [telemetrickéinzizátoru,](../../azure-monitor/app/api-custom-events-metrics.md#defaults) aby se zobrazila ve všech telemetrických jazycích – včetně standardní telemetrie odeslané různými moduly Sady SDK.

## <a name="edit-the-chart-type"></a>Úprava typu grafu
Všimněte si, že můžete přepínat mezi mřížkami a grafy:

![Výběr mřížky nebo grafu a volba typu grafu](./media/metrics-explorer/16-chart-grid.png)

## <a name="save-your-metrics-blade"></a>Uložení okna metrik
Po vytvoření některých grafů je uložte jako oblíbené. Pokud používáte účet organizace, můžete zvolit, zda ji chcete sdílet s ostatními členy týmu.

![Zvolte Oblíbené](./media/metrics-explorer/21-favorite-save.png)

Chcete-li čepel znovu zobrazit, **přejděte do přehledového okna** a otevřete oblíbené položky:

![V okně Přehled zvolte Oblíbené položky](./media/metrics-explorer/22-favorite-get.png)

Pokud jste při uložení zvolili relativní časový rozsah, bude okno aktualizováno nejnovějšími metrikami. Pokud jste zvolili absolutní časový rozsah, zobrazí se pokaždé stejná data.

## <a name="reset-the-blade"></a>Resetování nože
Pokud upravíte okno, ale pak se chcete vrátit k původní uložené sadě, stačí kliknout na Obnovit.

![V tlačítkách v horní části Průzkumníka metrik](./media/metrics-explorer/17-reset.png)

## <a name="live-metrics-stream"></a>Datový proud živých metrik

Chcete-li zobrazit mnohem bezprostřednější zobrazení telemetrie, otevřete [live stream](live-stream.md). Většina metrik trvat několik minut se zobrazí, protože proces agregace. Naproti tomu živé metriky jsou optimalizovány pro nízkou latenci. 

## <a name="set-alerts"></a>Nastavení upozornění
Chcete-li být e-mailem upozorněni na neobvyklé hodnoty libovolné metriky, přidejte výstrahu. Můžete se rozhodnout, že e-mail odešlete správcům účtu, nebo na konkrétní e-mailové adresy.

![V Průzkumníku metrik zvolte Pravidla upozornění, Přidat výstrahu.](./media/metrics-explorer/appinsights-413setMetricAlert.png)

[Další informace o výstrahách][alerts].


## <a name="continuous-export"></a>Nepřetržitý export
Pokud chcete, aby se data exportovala průběžně, abyste je mohli zpracovat externě, zvažte použití [průběžného exportu](../../azure-monitor/app/export-telemetry.md).

### <a name="power-bi"></a>Power BI
Pokud chcete data ještě zpátenit, můžete [exportovat do Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx).

## <a name="analytics"></a>Analýza
[Služba Analytics](../../azure-monitor/app/analytics.md) je všestrannější způsob analýzy telemetrie pomocí výkonného dotazovacího jazyka. Použijte ji, pokud chcete kombinovat nebo vypočítat výsledky z metrik nebo provést hloubkové zkoumání nedávného výkonu vaší aplikace. 

V metrickém grafu můžete kliknutím na ikonu Analytics přejít přímo na ekvivalentní dotaz Analytics.

## <a name="troubleshooting"></a>Řešení potíží
*V grafu nejsou žádná data.*

* Filtry platí pro všechny grafy na okně. Ujistěte se, že při zaměření na jeden graf jste nenastavili filtr, který vylučuje všechna data v jiném grafu.

    Chcete-li nastavit různé filtry v různých grafech, vytvořte je v různých čepelích a uložte je jako samostatné oblíbené položky. Pokud chcete, můžete je připnout k řídicímu panelu, abyste je mohli vidět vedle sebe.
* Pokud seskupíte graf podle vlastnosti, která není definována v metrice, nebude v grafu nic. Zkuste vymazat možnost Seskupit podle nebo zvolte jinou vlastnost seskupení.
* Data o výkonu (cpu, rychlost vipo služby a tak dále) jsou k dispozici pro webové služby Java, desktopové aplikace windows, [webové aplikace a služby IIS, pokud nainstalujete sledování stavu](../../azure-monitor/app/monitor-performance-live-website-now.md)a [Cloudové služby Azure](../../azure-monitor/app/app-insights-overview.md). Není k dispozici pro weby Azure.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Další kroky
* [Sledování využití pomocí přehledů aplikací](../../azure-monitor/app/usage-overview.md)
* [Použití diagnostického vyhledávání](../../azure-monitor/app/diagnostic-search.md)

<!--Link references-->

[alerts]: ../../azure-monitor/app/alerts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
