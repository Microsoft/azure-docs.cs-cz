---
title: Zkoumání metrik ve službě Azure Application Insights | Dokumentace Microsoftu
description: Jak interpretovat grafy na Průzkumník metrik a tom, jak přizpůsobit okna Průzkumník metrik.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 03/08/2017
ms.author: mbullwin
ms.openlocfilehash: 4c35f4366b2290164036316be8de730e20aa99bf
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47092658"
---
# <a name="exploring-metrics-in-application-insights"></a>Zkoumání metrik ve službě Application Insights
Metriky na [Application Insights] [ start] měřené hodnoty a počty událostí, které se odesílají v telemetrii z vaší aplikace. Pomáhají zjišťovat problémy s výkonem a sledovat trendy ve využití vaší aplikace. Existuje širokou škálu standardních metrik a můžete také vytvořit své vlastní metriky a události.

Počty metriky a události se zobrazí v grafech agregované hodnoty, jako je například součtů a průměry, počty.

Tady je ukázka sadu grafy:

![](./media/app-insights-metrics-explorer/01-overview.png)

Vyhledat všude, kde grafů metrik na portálu Application Insights. Ve většině případů je možné přizpůsobit a další grafy můžete přidat do okna. V okně Přehled proklikat k podrobnější grafy (které mají názvy, jako je například "Servery"), nebo klikněte na tlačítko **Průzkumníka metrik** otevřete nové okno, kde můžete vytvářet vlastní grafy.

## <a name="time-range"></a>Časové rozmezí
Můžete změnit časový rozsah, který je předmětem grafy a tabulky na libovolné okno.

![Otevře se okno Přehled vaší aplikace na webu Azure Portal](./media/app-insights-metrics-explorer/03-range.png)

Pokud očekáváte data, která nebyla dosud objevily, klikněte na aktualizovat. Grafy se samy aktualizují v intervalech, ale jsou intervaly delší dobu větší časových rozsahů. Může trvat nějakou dobu mají získat prostřednictvím kanálu analýzy do grafu data.

Pro zvětšení část grafu, přetáhněte ho:

![Proveďte operaci přetažení přes část diagramu.](./media/app-insights-metrics-explorer/12-drag.png)

Klikněte na tlačítko Zpět Lupa a obnovit.

## <a name="granularity-and-point-values"></a>Hodnoty členitosti a bodu
Umístěte ukazatel myši nad grafem, který chcete v tomto okamžiku zobrazí hodnoty metriky.

![Najeďte myší na graf](./media/app-insights-metrics-explorer/02-focus.png)

Hodnota metriky v určitém místě je agregován v předchozím intervalu vzorkování.

Interval vzorkování nebo "členitosti" se zobrazí v horní části okna.

![Hlavička okno.](./media/app-insights-metrics-explorer/11-grain.png)

Můžete nastavit členitost v okně rozsah času:

![Hlavička okno.](./media/app-insights-metrics-explorer/grain.png)

Rozlišeních, které jsou k dispozici, závisí na časový rozsah, který jste vybrali. Explicitní přírůstcích nějaké alternativy na "automatické" členitost časový rozsah.


## <a name="editing-charts-and-grids"></a>Úpravy grafů a mřížky
Chcete-li přidat nový graf do okna:

![V Průzkumníku metrik zvolte možnost přidat graf](./media/app-insights-metrics-explorer/04-add.png)

Vyberte **upravit** na existující nebo nový graf upravit, co zobrazí:

![Vyberte jednu nebo víc metrik](./media/app-insights-metrics-explorer/08-select.png)

Více než jedné metriky můžete zobrazit v grafu, i když existují omezení o kombinacích, které se dají zobrazit najednou. Jakmile vyberete jednu metriku, ostatní jsou zakázána.

Pokud jste programového [vlastní metriky] [ track] do vaší aplikace (volání TrackMetric a TrackEvent) budou uvedené tady.

## <a name="segment-your-data"></a>Segmentace vašich dat.
Můžete rozdělit metriky vlastností – například k porovnání zobrazení stránek na klientských počítačích s jinými operačními systémy.

Vyberte graf nebo tabulku, zapněte seskupování a vyberte vlastnosti, které chcete seskupit podle:

![Vyberte seskupení na sadu vyberte vlastnost v Group By](./media/app-insights-metrics-explorer/15-segment.png)

> [!NOTE]
> Při použití seskupení oblastí a pruhový graf typy poskytují skládaného zobrazení. Toto je vhodný, pokud způsob agregace je součet. Ale pokud typ agregace je průměr, zvolte řádek nebo Mřížka typů zobrazení.
>
>

Pokud jste programového [vlastní metriky] [ track] do vaší aplikace a zahrnují hodnoty vlastností, budete moct v seznamu vyberte vlastnost.

Graf je příliš malá pro segmentovaná data? Upravte výšku:

![Nastavení posuvníku](./media/app-insights-metrics-explorer/18-height.png)

## <a name="aggregation-types"></a>Typ agregace
Legendu na straně ve výchozím nastavení obvykle zobrazuje agregovaná hodnota období grafu. Pokud najedete myší na graf, zobrazuje hodnotu v tomto okamžiku.

Každý datový bod na grafu je agregace hodnot dat přijatých v předchozí interval vzorkování nebo "členitosti". Členitost je zobrazena v horní části okna a se liší podle celkové osy grafu.

Metriky se dají agregovat různými způsoby:

* **Počet** udává počet událostí přijato v intervalu vzorkování. Používá se pro události, například požadavky. Odchylky v výška grafu označuje kolísání kurz, ve kterém dochází k události. Ale mějte na paměti, že číselná hodnota se změní při změně interval vzorkování.
* **Součet** sečte hodnoty všechny datové body přijatých prostřednictvím interval vzorkování nebo po dobu grafu.
* **Průměrná** součet se vydělí počet datových bodů přijatých prostřednictvím interval.
* **Jedinečné** počty se používají pro počty uživatelů a účtů. Během intervalu vzorkování, nebo po dobu grafu obrázek zobrazuje počet různých uživatelů v daném čase.
* **%** -Procento verzích každé agregace jsou používány pouze s Segmentovaný grafu. Celkový počet vždy přidá až 100 % a graf znázorňuje relativní příspěvek různé součásti celkem.

    ![Procentuální agregace](./media/app-insights-metrics-explorer/percentage-aggregation.png)

### <a name="change-the-aggregation-type"></a>Změnit typ agregace

![Upravit graf a vyberte agregaci](./media/app-insights-metrics-explorer/05-aggregation.png)

Výchozí metodou pro jednotlivé metriky se zobrazí, když vytvoříte nový graf nebo když jsou vybraná všechny metriky:

![Zruší výběr všech metrik zobrazíte výchozí hodnoty](./media/app-insights-metrics-explorer/06-total.png)

## <a name="pin-y-axis"></a>PIN kód osy y 
Ve výchozím nastavení graf zobrazuje hodnoty osy Y začínající od nuly do maximální hodnoty v rozmezí dat, abyste vizuální znázornění quantum hodnoty. Ale v některých případech více než quantum může být zajímavé vizuálně zkoumat menší změny hodnot. Pro přizpůsobení, třeba toto použití rozsah osy y úpravy funkce pro Připnutí hodnotu minimální nebo maximální osy y požadovaného místa.
Klikněte na zaškrtávací políčko "Upřesnit nastavení" a zobrazte si nastavení rozsah osy y

![Klikněte na tlačítko Upřesnit nastavení, vyberte vlastní rozsah a zadejte minimální, maximální hodnota](./media/app-insights-metrics-explorer/y-axis-range.png)

## <a name="filter-your-data"></a>Filtrování dat
Pokud chcete zobrazit metriky pro vybranou sadu hodnot vlastností:

![Klikněte na tlačítko filtru, rozbalte vlastnosti a zkontrolujte některé hodnoty](./media/app-insights-metrics-explorer/19-filter.png)

Pokud nevyberete žádné hodnoty pro určité vlastnosti, je stejný jako vyberete všechny: neexistuje žádný filtr pro tuto vlastnost.

Všimněte si, že počet událostí společně s všechny hodnoty vlastností. Při výběru hodnoty jedné vlastnosti jsou upraveny počty vedle dalších hodnot vlastností.

Filtry platí pro všechny grafy v okně. Pokud chcete jiné filtrech použitých u různých grafů, vytvořte a uložte oken různé požadované metriky. Pokud chcete, můžete připnout grafy z různých oken na řídicí panel tak, aby si ji můžete zobrazit vedle sebe navzájem.

### <a name="remove-bot-and-web-test-traffic"></a>Odebrat testovacího provozu robotů a web
Pomocí filtru **skutečný nebo syntetický provoz** a zkontrolujte **skutečné**.

Můžete také filtrovat podle **zdroj syntetického provozu**.

### <a name="to-add-properties-to-the-filter-list"></a>Přidání vlastnosti do seznamu filtrů
Chcete filtrovat telemetrii v kategorii vlastní? Například možná rozdělit uživatele do různých kategorií a chcete rozdělit data podle těchto kategorií.

[Vytvořit vlastní vlastnost](app-insights-api-custom-events-metrics.md#properties). Nastavit [inicializátor Telemetrie](app-insights-api-custom-events-metrics.md#defaults) jeho joinkind veškerá telemetrie – včetně standardní telemetrická data zaslaná z různých modulů SDK.

## <a name="edit-the-chart-type"></a>Upravit typ grafu
Všimněte si, že můžete přepínat mezi tabulky a grafy:

![Vyberte tabulky nebo grafu a pak zvolte typ grafu](./media/app-insights-metrics-explorer/16-chart-grid.png)

## <a name="save-your-metrics-blade"></a>Uložit okně s metrikami
Když vytvoříte několik grafů, můžete je uložte jako oblíbenou položku. Můžete zvolit, jestli se má sdílet s ostatními členy týmu, pokud používáte účet organizace.

![Vybrat oblíbenou položku](./media/app-insights-metrics-explorer/21-favorite-save.png)

Chcete-li zobrazit okno znovu, **přejděte na okno Přehled** a otevřít oblíbené položky:

![V okně Přehled zvolte Oblíbené položky](./media/app-insights-metrics-explorer/22-favorite-get.png)

Pokud jste zvolili relativní časový rozsah, když jste si uložili, v okně aktualizován nejnovější metriky. Pokud jste zvolili absolutní časový rozsah, se zobrazí stejná data pokaždé, když.

## <a name="reset-the-blade"></a>Obnovit okno
Pokud upravíte okno, ale pak chcete vrátit zpět do původní uložila sada, stačí kliknout na obnovit.

![V seznamu tlačítka v horní části Průzkumníku metrik](./media/app-insights-metrics-explorer/17-reset.png)

## <a name="live-metrics-stream"></a>Live Metrics Stream

Mnohem více okamžitě zobrazit telemetrii, otevřete [Live Stream](app-insights-live-stream.md). Většina metrik trvat několik minut, než se zobrazí, a to z důvodu procesu agregace. Naopak živé metriky jsou optimalizované pro zajištění nízké latence. 

## <a name="set-alerts"></a>Nastavení upozornění
Pokud chcete dostat e-mailu z neobvyklé hodnoty jakékoliv metriky, přidáte upozornění. Můžete zvolit buď k odeslání e-mailu pro účet správce nebo na konkrétní e-mailové adresy.

![V Průzkumníku metrik vyberte pravidel upozornění, přidat oznámení](./media/app-insights-metrics-explorer/appinsights-413setMetricAlert.png)

[Další informace o výstrahách][alerts].


## <a name="continuous-export"></a>Souvislý export
Pokud chcete data průběžně exportovat, abyste ji může zpracovat externě, zvažte použití [průběžný export](app-insights-export-telemetry.md).

### <a name="power-bi"></a>Power BI
Pokud chcete ještě podrobnější zobrazení vašich dat, můžete si [export do Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx).

## <a name="analytics"></a>Analýzy
[Analytics](app-insights-analytics.md) větší variabilitu způsob, jak analyzovat telemetrická data pomocí výkonný dotazovací jazyk. Pokud chcete kombinovat nebo vypočítat výsledky z metrik nebo provádění zblízka se podíváte na poslední výkonu vaší aplikace, použijte ji. 

Z grafu metrik můžete kliknout na ikonu Analytics získat přímo na ekvivalentní analytický dotaz.

## <a name="troubleshooting"></a>Řešení potíží
*Nevidím žádná data v grafu.*

* Filtry platí pro všechny grafy v okně. Ujistěte se, že když se zaměříte na jednom grafu, nenastavili jste filtr, který vylučuje všechna data na další.

    Pokud chcete nastavit filtry, jiné na různých grafů, vytvořte je v jiné listy, uložte jako samostatné Oblíbené položky. Pokud chcete, můžete ho připnout na řídicí panel, tak, aby si ji můžete zobrazit vedle sebe navzájem.
* Pokud seskupíte podle vlastnosti, která není definována na metriku grafu, pak nebude nic v grafu. Vymažte "Seskupit podle", nebo zvolte jinou seskupení vlastnost.
* Údaje o výkonu (procesor, frekvence v/v a tak dále) je k dispozici pro Java webových služeb, aplikací klasické pracovní plochy Windows [IIS webové aplikace a služby, pokud budete instalovat monitorování stavu](app-insights-monitor-performance-live-website-now.md), a [Azure Cloud Services](app-insights-azure.md). Není k dispozici pro Azure websites.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Další postup
* [Sledování využití pomocí Application Insights](app-insights-web-track-usage.md)
* [Pomocí vyhledávání diagnostiky](app-insights-diagnostic-search.md)

<!--Link references-->

[alerts]: app-insights-alerts.md
[start]: app-insights-overview.md
[track]: app-insights-api-custom-events-metrics.md
