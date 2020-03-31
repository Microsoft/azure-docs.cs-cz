---
title: Vizualizace dat v průzkumníku Náhledu – Přehledy azure time series | Dokumenty společnosti Microsoft
description: Informace o funkcích a možnostech dostupných v průzkumníku Náhled přehledů Azure Time Series.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: seodec18
ms.openlocfilehash: c117510749abcf997e414371faa6dea1dc79ea7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75861757"
---
# <a name="azure-time-series-insights-preview-explorer"></a>Průzkumník náhledů přehledů Azure Time Series

Tento článek popisuje různé funkce a možnosti, které jsou k dispozici v [ukázkové webové aplikaci](https://insights.timeseries.azure.com/preview/demo)Azure Time Series Insights Preview .

## <a name="prerequisites"></a>Požadavky

Chcete-li začít s průzkumníkem Náhled přehledů Azure Time Series, musíte:

* Zřizují se prostředí Time Series Insights. Další informace o zřizování instance najdete v kurzu [Azure Time Series Insights Preview.](./time-series-insights-update-create-environment.md)
* [Poskytněte přístup k datům](./time-series-insights-data-access.md) prostředí Time Series Insights, které jste pro účet vytvořili. Můžete poskytnout přístup k ostatním, stejně jako k sobě.
* Přidejte zdroj událostí do prostředí Time Series Insights pro nabízení dat do prostředí:
  * Přečtěte [si, jak se připojit k centru událostí](./time-series-insights-how-to-add-an-event-source-eventhub.md) 
  * Přečtěte [si, jak se připojit k centru IoT hubu](./time-series-insights-how-to-add-an-event-source-iothub.md)

## <a name="explore-the-time-series-insights-preview-explorer"></a>Prozkoumejte průzkumník náhledu přehledů time series

Průzkumník Náhled přehledů Azure Time Series se skládá z následujících sedmi prvků:

[![Přehled y Přehledů časových řad – přehled náhledu – přehled – přehled](media/v2-update-explorer/preview-explorer-overview.png)](media/v2-update-explorer/preview-explorer-overview.png#lightbox)

1. [Panel Prostředí:](#1-environment-panel)Zobrazí všechna prostředí Azure Time Series Insights.
1. [Navigační panel](#2-navigation-bar): Umožňuje přepínat mezi stránkami **Analyzovat** a **Modelovat.**
1. [Strom hierarchie a panel hledání](#3-hierarchy-tree-and-search-panel): Umožňuje vybrat a vyhledat konkrétní datové prvky, které mají být zmapovány.
1. [Časové řady dobře](#4-time-series-well): Zobrazuje všechny aktuálně vybrané datové prvky.
1. [Panel Graf](#5-chart-panel): Zobrazí aktuální pracovní graf.
1. [Časová osa](#6-time-editor-panel): Umožňuje upravit pracovní dobu.
1. [Panel aplikace](#7-app-bar): Obsahuje možnosti správy uživatelů (například aktuálního klienta) a umožňuje je změnit a nastavení jazyka.


## <a name="1-environment-panel"></a>1. Panel Životní prostředí

Panel prostředí zobrazuje všechna prostředí Time Series Insights, ke které máte přístup. Seznam obsahuje prostředí s průběžným platbou (preview) a také prostředí S1/S2 (obecná dostupnost). Jednoduše vyberte prostředí Time Series Insights, které chcete použít, abyste se tam okamžitě vzali.

1. Vyberte šipku rozevíracího seznamu vedle zobrazeného prostředí.

   [![Panel Prostředí](media/v2-update-explorer/environment-panel.png)](media/v2-update-explorer/environment-panel.png#lightbox)

1. Potom vyberte požadované prostředí.

## <a name="2-navigation-bar"></a>2. Navigační panel

  [![Navigační panel](media/v2-update-explorer/tsi-preview-navigation-bar.png)](media/v2-update-explorer/tsi-preview-navigation-bar.png#lightbox)

Pomocí navigačního panelu vyberte mezi dvěma zobrazeními:

* **Analyzovat**: Použijte ji k mapování a provádění bohatých analýz na modelovaných nebo nemodelovaných datech časových řad.
* **Model**: Použijte jej k nabízení nových typů, hierarchií a instancí Time Series Insights do modelu Time Series Insights.

### <a name="model-authoring"></a>Vytváření modelů

Azure Time Series Insights Preview podporuje úplné operace vytváření, čtení, aktualizace a odstraňování (CRUD) ve vašem modelu časové řady.

[![Panel hledání modelu](media/v2-update-explorer/model-search-panel.png)](media/v2-update-explorer/model-search-panel.png#lightbox)

* **Typ modelu časové řady**: Pomocí typů Přehledy časových řad můžete definovat proměnné nebo vzorce pro výpočty. Jsou spojeny s danou instancí Time Series Insights. Typ může mít jednu nebo více proměnných.
* **Hierarchie modelů časových řad**: Hierarchie jsou systematické organizace vašich dat. Hierarchie zobrazují vztahy mezi různými entitami v datech Time Series Insights.
* **Instance Modelu časové řady**: Instance jsou časové řady samy. Ve většině případů se jedná o **DeviceID** nebo **AssetID**, což je jedinečný identifikátor datového zdroje v prostředí.

Další informace o modelu časové řady najdete v části [Modely řady Times](./time-series-insights-update-tsm.md).

## <a name="3-hierarchy-tree-and-search-panel"></a>3. Strom hierarchie a panel hledání

Strom hierarchie a panel hledání umožňují snadno vyhledávat a procházet hierarchii [modelu časových řad](./time-series-insights-update-tsm.md) a najít konkrétní instance časových řad, které chcete zobrazit v grafu. Když vyberete instance, jsou nejen přidány do aktuálního grafu, ale také se přidávají do dat dobře. 

[![Strom hierarchie a panel hledání](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png)](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png#lightbox)

Podokno výsledků hledání také umožňuje zobrazit výsledky v zobrazení hierarchie nebo seznamu, což usnadňuje vyhledání instancí, které chcete zobrazit.
 
## <a name="4-time-series-well"></a>4. Časové řady dobře

Vrt ová pole instancí a další metadata spojená s vybranými instancemi Time Series Insights. Zaškrtnutím políček na pravé straně můžete skrýt nebo zobrazit určité instance z aktuálního grafu. 

  [![Náhled dobře](media/v2-update-explorer/tsi-preview-ui-explorer-well.png)](media/v2-update-explorer/tsi-preview-ui-explorer-well.png#lightbox)

Můžete odstranit konkrétní datové prvky z aktuálních dat dobře výběrem červené **ho odstranit** (koš) ovládací prvek na levé straně prvku. Studna také umožňuje řídit, jak se každý prvek zobrazí v grafu. Můžete přidat min/max stíny, datové body, posunout prvek v čase a vizualizovat instanci stupňovtým způsobem. 

Ovládací prvek Průzkumy navíc umožňuje snadno vytvářet časové posuny a bodový graf.  

  [![Možnosti rozložení](media/v2-update-explorer/well-layout-options.png)](media/v2-update-explorer/well-layout-options.png#lightbox)

> [!NOTE]
> Pokud se zobrazí následující zpráva, instance nemá žádná data během vybraného časového rozpětí. Chcete-li problém vyřešit, zvyšte časový rozsah nebo potvrďte, že instance vysouvá data.
>
> ![Žádné datové oznámení](media/v2-update-explorer/tsi-preview-no-data-warning.png)

## <a name="5-chart-panel"></a>5. Panel Graf

Graf umožňuje zobrazit instance časových řad jako čáry. Panel prostředí, datový model a ovládací panel časového rozpětí můžete sbalit klepnutím na webové ovládací prvky, aby byl graf větší. 

  [![Náhled grafu – přehled](media/v2-update-explorer/tsi-preview-chart-overview.png)](media/v2-update-explorer/tsi-preview-chart-overview.png#lightbox)

1. **Typ grafu**: Určuje, které datové prvky jsou k dispozici pro vizualizaci.

1. **Velikost intervalu**: Jezdec velikost intervalu umožňuje přiblížit a oddálit intervaly ve stejném časovém rozpětí. To poskytuje přesnější kontrolu pohybu mezi velkými časovými úseky, které zobrazují hladké trendy až po řezy tak malé, jako je milisekunda, což vám umožní zkontrolovat podrobné řezy dat s vysokým rozlišením. Výchozí počáteční bod posuvníku je nastaven jako nejoptimálnější zobrazení dat z vašeho výběru; vyvažování rozlišení, rychlost dotazu a rozlišovací schopnost.

1. **Lupa a posouvání:** Tento ovládací prvek vyberte, chcete-li graf přiblížit a posuntit.

1. **Řízení osy Y**: Cyklicky prochází dostupnými možnostmi zobrazení osy y:

    * `Stacked`: Každá čára má samostatnou osu Y.
    * `Overlap`: Slouží k stohování více čar na stejné ose Y, přičemž data osy Y se mění na základě vybrané čáry.
    * `Shared`: Všechna data osy Y zobrazená společně.

1. **Element markeru**: Aktuálně vybraný datový prvek a jeho přidružené podrobnosti.

Můžete dále přejít k podrobnostem o určitém řezu dat **levým tlačítkem myši** v aktuálním grafu při podržení myši a přetažením vybrané oblasti do zvoleného koncového bodu. **Klikněte pravým tlačítkem myši na** modrou vybranou oblast a pak vyberte **Lupa,** jak je znázorněno níže. Můžete také zobrazit a stáhnout události telemetrie ve vybraném čase.

  [![Náhled zvětšení grafu](media/v2-update-explorer/preview-chart-zoom.png)](media/v2-update-explorer/preview-chart-zoom.png#lightbox)

Po provedení akce **Lupa** se zobrazí vybraná sada dat. Vyberte ovládací prvek formátu, který chcete procházet třemi reprezentacemi osy y dat Time Series Insights.

  [![Náhled grafu osy y](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png)](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png#lightbox)

Zde je uveden příklad **překrývajícího** se grafu:

  [![Překrývající se graf, volba](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png)](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png#lightbox)

Tlačítko **Další akce** se rozbalí a zobrazí **možnostStáhnout jako CSV**, **Připojit k Power BI**, Zobrazit data grafu jako **tabulku**a Prozkoumat možnosti **nezpracovaných událostí.**

  [![Další akce možnost](media/v2-update-explorer/more-actions-icon.png)](media/v2-update-explorer/more-actions-icon.png#lightbox)

Přečtěte si další informace o možnosti **Připojit k Power BI** v [nativním konektoru Power BI Time Series Insights](concepts-power-bi.md).

## <a name="6-time-editor-panel"></a>6. Panel editoru času

Při práci s Time Series Insights nejprve vyberete časové rozpětí. Vybrané časové rozpětí bude řídit sadu dat, která je k dispozici pro manipulaci s widgety aktualizace Time Series Insights.

  [![Panel výběru času](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png)](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png#lightbox)

> [!TIP]
> Část časové osy je zvýrazněna oranžovou nebo oranžovou barvou, která označuje rozsah dat, která jsou k dispozici v teplém obchodě.

Následující webové ovládací prvky jsou k dispozici v aktualizaci Time Series Insights pro výběr pracovní doby. 

  [![Kontrola průzkumu](media/v2-update-explorer/exploration-well-control.png)](media/v2-update-explorer/exploration-well-control.png#lightbox)

1. **Ovládací prvek posuvníku Vnitřní rozsah dat**: Použijte dva ovládací prvky koncového bodu jejich přetažením v požadovaném časovém rozpětí. Tento vnitřní rozsah dat je omezen ovládacím prvkem posuvníku vnějšího rozsahu dat.

1. **Tlačítka pro zvýšení a zmenšení časového rozsahu**: Zvětšete nebo zmenšete časový rozsah výběrem jednoho tlačítka pro požadovaný interval.

1. **Ovládací prvek sbalení časového rozpětí**: Tento webový ovládací prvek umožňuje skrýt všechny ovládací prvky kromě nástroje jezdce vnitřního rozsahu dat.

1. **Ovládací prvek posuvníku vnějšího rozsahu dat**: Pomocí ovládacích prvků koncového bodu vyberte vnější rozsah dat, který bude k dispozici pro váš vnitřní ovládací prvek rozsahu dat.

1. **Ovládací prvek posuvníku časového rozsahu**: Slouží k rychlému přepínání mezi volbami přednastaveného časového rozpětí, například posledních **30 minut**, posledních **12 hodin**nebo vlastního **rozsahu**. Změna této hodnoty také změní dostupné rozsahy intervalů popsané v posuvníku velikosti intervalu.

   [![Do a z výběrového panelu](media/v2-update-explorer/to-and-from-element.png)](media/v2-update-explorer/to-and-from-element.png#lightbox)

## <a name="7-app-bar"></a>7. Panel aplikace

Navigační panel Náhled přehledů časové řady se zobrazí v horní části aplikace Přehledy časových řad. Poskytuje následující funkce:

### <a name="current-session-share-link-control"></a>Aktuální ovládací prvek odkazu sdílení relace

  [![Ikona Sdílet](media/v2-update-explorer/tsi-preview-explorer-share-icon.png)](media/v2-update-explorer/tsi-preview-explorer-share-icon.png#lightbox)

Vyberte novou ikonu **Sdílet** a sdílejte odkaz na adresu URL se svým týmem.

  [![Sdílení adresy URL instance](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png)](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png#lightbox)

### <a name="tenant-section"></a>Sekce nájemce

  [![Výběr klienta](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png)](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png#lightbox)

* Zobrazí aktuální informace o přihlašovacím účtu Přehledů časových řad.
* Použijte ji k přepínání mezi dostupnými motivy Time Series Insights.
* Použijte ji k zobrazení [ukázkové webové aplikace](https://insights.timeseries.azure.com/preview/demo)Preview .

### <a name="theme-selection"></a>Výběr motivu

Chcete-li vybrat nový motiv, vyberte ikonu profilu umístěnou v pravém horním rohu. Potom vyberte **Změnit motiv**.

  [![Výběr motivu](media/v2-update-explorer/tsi-preview-theme-selection.png)](media/v2-update-explorer/tsi-preview-theme-selection.png#lightbox)

> [!TIP]
> Výběr jazyka je k dispozici také výběrem ikony profilu.

Azure Time Series Insights Preview podporuje dva motivy:

* **Motiv světla**: Výchozí motiv zobrazený v tomto dokumentu.
* **Tmavý motiv**: Vykreslí průzkumníka, jak je znázorněno zde:

  [![Vybraný tmavý motiv](media/v2-update-explorer/tsi-preview-dark-theme-selected.png)](media/v2-update-explorer/tsi-preview-dark-theme-selected.png#lightbox)

## <a name="s1s2-environment-controls"></a>Ovládací prvky prostředí S1/S2

### <a name="preview-terms-panel"></a>Panel Náhled termínů

Tato část se vztahuje pouze na existující prostředí S1/S2, které se pokoušejí použít průzkumníka v aktualizovaném ui. Můžete chtít použít obecně dostupný produkt a náhled v kombinaci. Přidali jsme některé funkce z existujícího uživatelského rozhraní do aktualizovaného průzkumníka, ale můžete získat plné uživatelské rozhraní pro prostředí S1/S2 v existujícím průzkumníku Time Series Insights. 

Místo hierarchie se zobrazí panel Podmínky časové řady Přehledy. Panel termínů umožňuje definovat dotazy ve vašem prostředí. Slouží také k filtrování dat na základě predikátu.

  [![Kde panel dotazu](media/v2-update-explorer/s1-s2-preview-query.png)](media/v2-update-explorer/s1-s2-preview-query.png#lightbox)

Panel editoru termínů Time Series Insights Preview má následující parametry:

**Kde**: Použijte klauzuli where k rychlému filtrování událostí pomocí sady operandů uvedených v následující tabulce. Pokud provádíte vyhledávání výběrem operandu, predikát se automaticky aktualizuje na základě tohoto hledání. Mezi podporované typy operandů patří následující:

| Operace | Podporované typy   | Poznámky |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Dvojité, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | Řetězec, Bool, Double, DateTime, TimeSpan, NULL |
| `IN` | Řetězec, Bool, Double, DateTime, TimeSpan, NULL | Všechny operandy by měly být stejného typu nebo konstanta NULL. |
| `HAS` | Řetězec | Na pravé straně jsou povoleny pouze literály konstantní řetězec. Prázdný řetězec a NULL nejsou povoleny. |

Další informace o podporovaných operacích dotazů a datových typech najdete v textu [Výraz časové řady (TSX).](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

### <a name="examples-of-where-clauses"></a>Příklady, kdy doložky

  [![Pokud jsou příklady doložky](media/v2-update-explorer/tsi-preview-example-queries.png)](media/v2-update-explorer/tsi-preview-example-queries.png#lightbox)

**Míra**: Rozevírací seznam, který zobrazuje všechny číselné sloupce (**Čtyřhra),** které můžete použít jako prvky pro aktuální graf.

**Rozdělit podle**: V tomto rozevíracím seznamu jsou zobrazeny všechny dostupné sloupce kategorií (řetězce) v modelu, podle kterých můžete data seskupit. Můžete přidat až pět termínů pro zobrazení na stejné ose x. Zadejte požadované parametry a pak vyberte **Přidat,** chcete-li přidat nový termín.

  [![Dotazované a filtrované zobrazení](media/v2-update-explorer/s1-s2-preview-filtered-view.png)](media/v2-update-explorer/s1-s2-preview-filtered-view.png#lightbox)

Prvky v panelu grafu můžete zobrazit a skrýt výběrem viditelné ikony, jak je znázorněno na následujícím obrázku. Chcete-li dotazy zcela odebrat, vyberte červený **křížek**.

  [![Zrušení dotazované a filtrované možnosti](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png)](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png#lightbox)

## <a name="next-steps"></a>Další kroky

- Další informace o [úložišti a příchozím přenosu dat](./time-series-insights-update-storage-ingress.md) ve verzi Azure Time Series Insights Preview.

- Přečtěte si dokument Time Series Insights Preview o [modelování dat](./time-series-insights-update-tsm.md).

- Přečtěte [si, jak diagnostikovat a řešit potíže s](./time-series-insights-update-how-to-troubleshoot.md) instancí Time Series Insights.
