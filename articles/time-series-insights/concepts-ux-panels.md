---
title: Vizualizovat data v Průzkumníkovi Time Series Insights – Azure Time Series Insights Gen2 | Microsoft Docs
description: Seznamte se s funkcemi a možnostmi dostupnými v Průzkumníkovi Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/31/2020
ms.custom: seodec18
ms.openlocfilehash: 0933e34e5bdd8469b827ed03c0eff51a4ecba0d5
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89488163"
---
# <a name="azure-time-series-insights-explorer"></a>Průzkumník Azure Time Series Insights

Tento článek popisuje různé funkce a možnosti, které jsou k dispozici v rámci [ukázkového prostředí](https://insights.timeseries.azure.com/preview/demo)Azure Time Series Insights Gen2.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít s Azure Time Series Insights Explorer, musíte:

* Mít zřízené prostředí Azure Time Series Insights Gen2. Další informace o zřizování instance najdete v kurzu [Azure Time Series Insights Gen2](./time-series-insights-update-create-environment.md) .
* [Poskytněte přístup k datům](./time-series-insights-data-access.md) prostředí Azure Time Series Insights Gen2, které jste pro účet vytvořili. Můžete poskytovat přístup i ostatním uživatelům.
* Přidejte zdroj události do prostředí Azure Time Series Insights Gen2, abyste vložili data do prostředí:
  * Informace [o tom, jak se připojit k centru událostí](./time-series-insights-how-to-add-an-event-source-eventhub.md)
  * Informace [o tom, jak se připojit ke službě IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md)

## <a name="explore-the-azure-time-series-insights-explorer"></a>Prozkoumat Průzkumníka Azure Time Series Insights

Azure Time Series Insights Průzkumník se skládá z následujících sedmi prvků:

[![Přehled Průzkumníka Azure Time Series Insights](media/v2-update-explorer/preview-explorer-overview.png)](media/v2-update-explorer/preview-explorer-overview.png#lightbox)

1. [Panel prostředí](#1-environment-panel): zobrazí všechna prostředí Azure Time Series Insights Gen2.
1. [Navigační panel](#2-navigation-bar): umožňuje přepínat mezi stránkami **analyzovat** a **model** .
1. [Strom hierarchie a panel hledání](#3-hierarchy-tree-and-search-panel): umožňuje vybrat a vyhledat konkrétní datové prvky, které mají být v grafu.
1. [Time Series Well](#4-time-series-well): zobrazuje všechny aktuálně vybrané datové prvky.
1. [Panel grafu](#5-chart-panel): zobrazí aktuální pracovní graf.
1. [Časová osa](#6-time-editor-panel): umožňuje upravit rozsah pracovní doby.
1. [Panel aplikace](#7-app-bar): obsahuje možnosti správy uživatelů (například aktuální tenant) a umožňuje změnit nastavení a nastavení jazyka.

## <a name="1-environment-panel"></a>1. panel prostředí

Panel prostředí zobrazuje všechna prostředí Azure Time Series Insights Gen2, ke kterým máte přístup. Seznam obsahuje prostředí Gen2 a také prostředí Gen1. Jednoduše vyberte prostředí, které chcete použít k okamžitému pořízení.

1. Vyberte šipku rozevíracího seznamu vedle zobrazeného prostředí.

   [![Panel prostředí](media/v2-update-explorer/environment-panel.png)](media/v2-update-explorer/environment-panel.png#lightbox)

1. Pak vyberte prostředí, které chcete.

## <a name="2-navigation-bar"></a>2. navigační panel

  [![Navigační panel](media/v2-update-explorer/tsi-preview-navigation-bar.png)](media/v2-update-explorer/tsi-preview-navigation-bar.png#lightbox)

Pomocí navigačního panelu můžete vybrat mezi dvěma zobrazeními:

* **Analyzovat**: použijte ho k vytvoření grafu a provádění obsáhlých analýz pro modelovaná nebo nemodelovaná data časových řad.
* **Model**: použijte ho k vložení nových Azure Time Series Insights typů, hierarchií a instancí do modelu časové řady.

### <a name="model-authoring"></a>Vytváření modelů

Azure Time Series Insights Gen2 podporuje úplné operace vytvoření, čtení, aktualizace a odstranění (CRUD) v modelu časové řady.

[![Panel hledání modelu](media/v2-update-explorer/model-search-panel.png)](media/v2-update-explorer/model-search-panel.png#lightbox)

* **Typ modelu časové řady**: můžete použít typy modelu časové řady k definování proměnných nebo vzorců pro výpočty. Jsou přidruženy k dané instanci modelu časové řady. Typ může mít jednu nebo více proměnných.
* **Hierarchie modelu časové řady**: hierarchie jsou systematické organizace vašich dat. Hierarchie znázorňuje vztahy mezi různými instancemi v modelu časové řady.
* **Instance modelu časové řady**: instance jsou samotné časové řady. Ve většině případů se jedná o **DeviceID** nebo **AssetID**, což je jedinečný identifikátor assetu v prostředí.

Pokud chcete získat další informace o modelu časové řady, pročtěte si [modely časů řady](./concepts-model-overview.md).

## <a name="3-hierarchy-tree-and-search-panel"></a>3. strom hierarchie a panel hledání

Strom a panel hledání hierarchie vám umožní snadno vyhledat a procházet hierarchii [modelu časové řady](./concepts-model-overview.md) a najít konkrétní instance časových řad, které chcete zobrazit v grafu. Když vyberete své instance, nebudou přidány pouze do aktuálního grafu, ale budou také přidány do datového zásobníku.

[![Strom hierarchie a panel hledání](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png)](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png#lightbox)

Podokno výsledků hledání také umožňuje zobrazit výsledky v zobrazení hierarchie nebo v zobrazení seznamu, které usnadňuje vyhledání instancí, které chcete zobrazit.

## <a name="4-time-series-well"></a>4. Time Series – Well

Dobře zobrazuje pole instance a další metadata přidružená k vybraným instancím modelů časových řad. Zaškrtnutím políček na pravé straně můžete skrýt nebo zobrazit konkrétní instance z aktuálního grafu.

  [![Gen2 dobře](media/v2-update-explorer/tsi-preview-ui-explorer-well.png)](media/v2-update-explorer/tsi-preview-ui-explorer-well.png#lightbox)

Konkrétní datové prvky můžete ze svých aktuálních dat odebrat tak, že vyberete ovládací prvek červené **odstranění** (odpadkový koš) na levé straně elementu. Také vám umožňuje řídit, jak se jednotlivé prvky zobrazí v grafu. Můžete zvolit, že chcete přidat minimální/maximální stíny, datové body, posunout element v čase a vizualizovat instanci, která je tímto způsobem.

Kromě toho vám ovládací prvek průzkumy umožňuje snadno vytvářet časová posunutí a bodový graf.  

  [![Možnosti vhodného rozložení](media/v2-update-explorer/well-layout-options.png)](media/v2-update-explorer/well-layout-options.png#lightbox)

> [!NOTE]
> Pokud se zobrazí následující zpráva, tato instance nebude mít během vybraného časového intervalu žádná data. Chcete-li tento problém vyřešit, zvyšte časový rozsah nebo potvrďte, že instance přenáší data.
>
> ![Žádná oznámení k datům](media/v2-update-explorer/tsi-preview-no-data-warning.png)

## <a name="5-chart-panel"></a>5. panel grafů

Graf umožňuje zobrazovat instance časových řad jako řádky. Panel prostředí, datový model a ovládací panel časová rozpětí můžete sbalit kliknutím na webové ovládací prvky, aby byl graf větší.

  [![Přehled grafu Gen2](media/v2-update-explorer/tsi-preview-chart-overview.png)](media/v2-update-explorer/tsi-preview-chart-overview.png#lightbox)

1. **Typ grafu**: Určuje, které datové prvky jsou k dispozici pro vizualizaci.

1. **Velikost intervalu**: nástroj dráha velikosti intervalu vám umožní přiblížit nebo oddálit intervaly ve stejném časovém intervalu. To poskytuje přesnější kontrolu nad pohybem mezi velkými řezy času, které znázorňují hladké trendy v různých řezech až do milisekund, což vám umožní kontrolovat podrobné a vysoce vyřešené části dat. Výchozí počáteční bod posuvníku je nastaven jako optimální pohled na data z výběru. vyvážení řešení, rychlost dotazu a členitost.

1. **Lupa a posouvání**: Tento ovládací prvek vyberte, pokud chcete graf zvětšit a posunout.

1. **Ovládací prvek osy y**: cyklicky dostupné možnosti zobrazení osy y:

    * `Stacked`: Každý řádek má jednotlivou osu Y.
    * `Overlap`: Slouží k skládání více řádků na stejnou osu Y, přičemž se mění data osy Y v závislosti na vybraném řádku.
    * `Shared`: Všechna data osy Y se zobrazují dohromady.

1. **Element marker**: aktuálně vybraný datový prvek a jeho přidružené podrobnosti.

Kliknutím **levým** na datový bod v aktuálním grafu a podržením myši a přetažením vybrané oblasti na koncový bod podle vlastního výběru můžete přejít k podrobnostem konkrétního datového řezu. Klikněte **pravým tlačítkem myši** na modrou, vybranou oblast a pak vyberte možnost **Zvětšit** , jak je znázorněno níže. Můžete také zobrazit a stáhnout události telemetrie ve vybraném časovém rozmezí.

  [![Přiblížení grafu Gen2](media/v2-update-explorer/preview-chart-zoom.png)](media/v2-update-explorer/preview-chart-zoom.png#lightbox)

Po provedení akce **přiblížení** se zobrazí Vybraná datová sada. Vyberte ovládací prvek formát, který bude procházet tři reprezentace dat na ose y.

  [![Osa y grafu Gen2](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png)](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png#lightbox)

Zde je uveden příklad **překrývající se grafu** :

  [![Překrývající se možnost grafu](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png)](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png#lightbox)

Tlačítko **Další akce** rozbalí, aby se zobrazila možnost **Stáhnout jako sdílený svazek clusteru**, **připojit k Power BI**, **Zobrazit data grafu jako tabulku**a **prozkoumat možnosti nezpracovaných událostí** .

  [![Další akce – možnost](media/v2-update-explorer/more-actions-icon.png)](media/v2-update-explorer/more-actions-icon.png#lightbox)

Přečtěte si další informace o možnosti **připojit k Power BI** v [konektoru Power BI](concepts-power-bi.md).

## <a name="6-time-editor-panel"></a>6. panel editoru času

Při práci s Azure Time Series Insights Gen2 nejdříve vyberte časový rozsah. Vybraný časový rozsah bude řídit datovou sadu, která je k dispozici pro práci s Azure Time Series Insights Gen2 aktualizace widgetů.

  [![Panel výběru času](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png)](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png#lightbox)

> [!TIP]
> Část časové osy je zvýrazněna žlutou nebo oranžovou barvou, aby označovala rozsah dat, která jsou k dispozici v teplém úložišti.

Následující webové ovládací prvky jsou k dispozici v Azure Time Series Insights Gen2 pro výběr rozsahu pracovní doby.

  [![Dobré řízení průzkumu](media/v2-update-explorer/exploration-well-control.png)](media/v2-update-explorer/exploration-well-control.png#lightbox)

1. **Ovládací prvek posuvníku rozsahu vnitřního data**: pomocí dvou ovládacích prvků koncového bodu je můžete přetáhnout do požadovaného časového rozsahu. Tento vnitřní rozsah dat je omezen ovládacím prvkem posuvník rozsahu vnějšího data.

1. Tlačítko pro **zvýšení a snížení rozsahu data**: Zvyšte nebo snižte časový rozsah tak, že vyberete jedno tlačítko pro požadovaný interval.

1. **Časový interval sbalení ovládacího prvku**: Tento webový ovládací prvek umožňuje skrýt všechny ovládací prvky kromě nástroje posuvníku rozsahu vnitřního data.

1. **Ovládací prvek posuvník rozsahu vnějšího data**: pomocí ovládacích prvků koncového bodu můžete vybrat rozsah vnějších kalendářních dat, který bude k dispozici pro ovládací prvek rozsahu vnitřního data.

1. **Ovládací prvek posuvníku časového rozsahu**: použijte ho k rychlému přepínání mezi přednastavenými výběry časových rozsahů, například posledních **30 minut**, **posledních 12 hodin**nebo **vlastním rozsahem**. Změna této hodnoty změní také rozsahy dostupných intervalů, které jsou popsány v nástroji Slider velikost intervalu.

   [![Do a z panelu výběru](media/v2-update-explorer/to-and-from-element.png)](media/v2-update-explorer/to-and-from-element.png#lightbox)

## <a name="7-app-bar"></a>7. panel aplikací

Navigační panel Azure Time Series Insights Gen2 se zobrazí v horní části aplikace. Nabízí následující funkce:

### <a name="current-session-share-link-control"></a>Ovládací prvek odkazu na sdílenou složku aktuální relace

  [![Ikona Sdílet](media/v2-update-explorer/tsi-preview-explorer-share-icon.png)](media/v2-update-explorer/tsi-preview-explorer-share-icon.png#lightbox)

Vyberte ikonu nové **sdílené složky** a sdílejte odkaz na adresu URL s týmem.

  [![Sdílení adresy URL vaší instance](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png)](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png#lightbox)

### <a name="tenant-section"></a>Oddíl tenant

  [![Výběr tenanta](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png)](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png#lightbox)

* Zobrazí aktuální informace o přihlašovacím účtu Azure Time Series Insights Gen2.
* Použijte ho k přepínání mezi dostupnými motivy.
* Použijte ho k zobrazení [ukázkového prostředí](https://insights.timeseries.azure.com/preview/demo)Gen2.

### <a name="theme-selection"></a>Výběr motivu

Pokud chcete vybrat nový motiv, vyberte ikonu vašeho profilu, která se nachází v pravém horním rohu. Pak vyberte **změnit motiv**.

  [![Výběr motivu](media/v2-update-explorer/tsi-preview-theme-selection.png)](media/v2-update-explorer/tsi-preview-theme-selection.png#lightbox)

> [!TIP]
> Výběr jazyka je také k dispozici tak, že vyberete ikonu vašeho profilu.

Azure Time Series Insights Explorer podporuje dva motivy:

* **Světlý motiv**: výchozí motiv zobrazený v celém tomto dokumentu.
* **Tmavý motiv**: vykreslí Průzkumníka, jak je znázorněno zde:

  [![Vybraný tmavý motiv](media/v2-update-explorer/tsi-preview-dark-theme-selected.png)](media/v2-update-explorer/tsi-preview-dark-theme-selected.png#lightbox)

## <a name="gen1-environment-controls"></a>Ovládací prvky prostředí Gen1

### <a name="gen2-terms-panel"></a>Panel podmínek Gen2

Tato část platí jenom pro existující prostředí Gen1, která se pokusí použít Průzkumníka v aktualizovaném uživatelském rozhraní. Můžete chtít použít produkt Gen1 a produkt Gen2 v kombinaci. Přidali jsme některé funkce z existujícího uživatelského rozhraní do aktualizovaného Průzkumníka, ale v novém Azure Time Series Insights Exploreru můžete získat plné prostředí uživatelského rozhraní pro prostředí Gen1.

Místo hierarchie se zobrazí panel Azure Time Series Insights Gen2 podmínek. Panel podmínky vám umožní definovat dotazy ve vašem prostředí. Použijte ho k filtrování dat na základě predikátu.

  [![Kde panel dotazů](media/v2-update-explorer/s1-s2-preview-query.png)](media/v2-update-explorer/s1-s2-preview-query.png#lightbox)

Panel editoru Azure Time Series Insightsch podmínek Gen2 má následující parametry:

**WHERE**: použijte klauzuli WHERE k rychlému filtrování událostí pomocí sady operandů uvedených v následující tabulce. Pokud provedete hledání výběrem operandu, predikát se automaticky aktualizuje na základě tohoto hledání. Podporované typy operandů zahrnují následující:

| Operace    | Podporované typy    | Poznámky |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | String, bool, Double, DateTime, TimeSpan, NULL |
| `IN` | String, bool, Double, DateTime, TimeSpan, NULL | Všechny operandy by měly být stejného typu nebo mít nulovou konstantu. |
| `HAS` | Řetězec | Na pravé straně jsou povoleny pouze konstantní řetězcové literály. Prázdný řetězec a hodnota NULL nejsou povoleny. |

Další informace o podporovaných operacích dotazů a datových typech najdete v tématu [výraz Time Series Expression (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax).

### <a name="examples-of-where-clauses"></a>Příklady klauzulí WHERE

  [![Příklady klauzule WHERE](media/v2-update-explorer/tsi-preview-example-queries.png)](media/v2-update-explorer/tsi-preview-example-queries.png#lightbox)

**Measure**: rozevírací seznam, který zobrazuje všechny číselné sloupce (**dvojité**), můžete použít jako prvky pro aktuální graf.

**Rozdělit podle**: Tento rozevírací seznam zobrazuje všechny dostupné kategorií sloupce (řetězce) v modelu, podle kterých můžete data seskupovat. Můžete přidat až pět podmínek pro zobrazení na stejné ose x. Zadejte požadované parametry a pak vyberte **Přidat** a přidejte nový termín.

  [![Dotazovaná a filtrovaná zobrazení jedna](media/v2-update-explorer/s1-s2-preview-filtered-view.png)](media/v2-update-explorer/s1-s2-preview-filtered-view.png#lightbox)

Prvky na panelu grafu můžete zobrazit a skrýt tak, že vyberete ikonu Visible, jak je znázorněno na následujícím obrázku. Pokud chcete úplně odebrat dotazy, vyberte červené **X**.

  [![Zrušení dotazované a filtrované možnosti](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png)](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png#lightbox)

## <a name="next-steps"></a>Další kroky

* Přečtěte si informace o ingestování [dat](./concepts-ingestion-overview.md) do vašeho prostředí.

* Projděte si článek o [úložišti](concepts-storage.md).

* Přečtěte si o [modelování dat](./concepts-model-overview.md) v Azure Time Series Insights Gen2.

* Naučte [se diagnostikovat a řešit potíže s](./time-series-insights-update-how-to-troubleshoot.md) prostředím.
