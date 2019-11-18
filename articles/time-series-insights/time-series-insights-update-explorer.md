---
title: Vizualizovat data v Průzkumníkovi Preview – Azure Time Series Insights | Microsoft Docs
description: Seznamte se s funkcemi a možnostmi dostupnými v Průzkumníkovi Preview v Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/13/2019
ms.custom: seodec18
ms.openlocfilehash: 17ba808ebfabb68765cf35bbf0799d117bc6383b
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133430"
---
# <a name="azure-time-series-insights-preview-explorer"></a>Průzkumník Preview Azure Time Series Insights

Tento článek popisuje různé funkce a možnosti, které jsou k dispozici v rámci [ukázkové webové aplikace](https://insights.timeseries.azure.com/preview/demo)Azure Time Series Insights Preview.

## <a name="prerequisites"></a>Požadavky

Pokud chcete začít s Průzkumníkem Azure Time Series Insights Preview, musíte:

* Mít zřízené prostředí Time Series Insights. Další informace o zřizování instance najdete v kurzu [Azure Time Series Insights Preview](./time-series-insights-update-create-environment.md) .
* [Poskytněte přístup k datům](./time-series-insights-data-access.md) Time Series Insights prostředí, které jste pro účet vytvořili. Můžete poskytnout přístup k i další uživatele tak, aby sami.
* Přidání zdroje událostí do prostředí Time Series Insights pro vložení dat do prostředí:
  * Informace [o tom, jak se připojit k centru událostí](./time-series-insights-how-to-add-an-event-source-eventhub.md) 
  * Informace [o tom, jak se připojit ke službě IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md)

## <a name="explore-the-time-series-insights-preview-explorer"></a>Prozkoumat Průzkumníka Time Series Insights Preview

Průzkumník Preview Azure Time Series Insights se skládá z následujících sedmi prvků:

[Přehled Průzkumníka ![Time Series Insights Preview](media/v2-update-explorer/preview-explorer-overview.png)](media/v2-update-explorer/preview-explorer-overview.png#lightbox)

1. [Panel prostředí](#1-environment-panel): zobrazí všechna Azure Time Series Insights prostředí.
1. [Navigační panel](#2-navigation-bar): umožňuje přepínat mezi stránkami **analyzovat** a **model** .
1. [Strom hierarchie a panel hledání](#3-hierarchy-tree-and-search-panel): umožňuje vybrat a vyhledat konkrétní datové prvky, které mají být v grafu.
1. [Time Series Well](#4-time-series-well): zobrazuje všechny aktuálně vybrané datové prvky.
1. [Panely grafu](#5-chart-panel): Zobrazí aktuální pracovní grafu.
1. [Časová osa](#6-time-editor-panel): umožňuje upravovat vaše pracovní časový rozsah.
1. [Panel aplikace](#7-app-bar): obsahuje možnosti správy uživatelů (například aktuální tenant) a umožňuje změnit nastavení a nastavení jazyka.


## <a name="1-environment-panel"></a>1. panel prostředí

V panelu prostředí se zobrazí všechna prostředí Time Series Insights, ke kterým máte přístup k. Seznam obsahuje prostředí s průběžnými platbami (Preview) a také prostředí S1/S2 (všeobecně dostupné). Jednoduše klikněte na prostředí Time Series Insights, které chcete použít k okamžitému pořízení.

1. Vyberte šipku rozevíracího seznamu vedle zobrazeného prostředí.

   [![panel prostředí](media/v2-update-explorer/environment-panel.png)](media/v2-update-explorer/environment-panel.png#lightbox)

1. Pak vyberte prostředí, které chcete.

## <a name="2-navigation-bar"></a>2. navigační panel

  [![navigačním panelu](media/v2-update-explorer/navigation-bar.png)](media/v2-update-explorer/navigation-bar.png#lightbox)

Pomocí navigačního panelu můžete vybrat mezi dvěma zobrazeními:

* **Analyzovat**: použijte ho k vytvoření grafu a provádění obsáhlých analýz pro modelovaná nebo nemodelovaná data časových řad.
* **Model**: použijte ho k vložení nových typů, hierarchií a instancí Preview Time Series Insights do modelu Time Series Insights.

### <a name="model-authoring"></a>Vytváření modelů

Azure Time Series Insights Preview podporuje operace úplného vytváření, čtení, aktualizace a odstranění (CRUD) v modelu časové řady.

[![panelu hledání modelu](media/v2-update-explorer/model-search-panel.png)](media/v2-update-explorer/model-search-panel.png#lightbox)

* **Typ modelu časové řady**: můžete použít typy Time Series Insights k definování proměnných nebo vzorců pro provádění výpočtů. Jsou přidruženy k dané instanci Time Series Insights. Typ může mít jednu nebo více proměnných.
* **Časové řady modelu hierarchie**: hierarchií jsou systematické organizace vaše data. Hierarchie znázornění vztahy mezi různými entitami ve vašich datech Time Series Insights.
* **Instance řady modelu čas**: instance jsou časové řady sami. Ve většině případů se jedná o **DeviceID** nebo **AssetID**, což je jedinečný identifikátor assetu v prostředí.

Další informace o modelu časové řady, naleznete v tématu [časy řady modely](./time-series-insights-update-tsm.md).

## <a name="3-hierarchy-tree-and-search-panel"></a>3. strom hierarchie a panel hledání

Strom hierarchie a panel hledání umožňuje snadno vyhledávat a procházet hierarchii [modelů časových řad](./time-series-insights-update-tsm.md) , aby bylo možné najít konkrétní instance časových řad, které chcete zobrazit v grafu. Když vyberete své instance, nebudou přidány pouze do aktuálního grafu, ale budou také přidány do datového zásobníku. 

[![stromové struktuře hierarchie a panelu hledání](media/v2-update-explorer/hierarchy-search.png)](media/v2-update-explorer/hierarchy-search.png#lightbox)

Podokno výsledků hledání také umožňuje zobrazit výsledky v zobrazení hierarchie nebo v zobrazení seznamu, které usnadňuje vyhledání instancí, které chcete zobrazit.
 
## <a name="4-time-series-well"></a>4. Time Series – Well

Zobrazuje také pole instancí a další metadata přidružená k vybraným instancím Time Series Insights. Zaškrtnutím políček na pravé straně můžete skrýt nebo zobrazit konkrétní instance z aktuálního grafu. 

  [![také ve verzi Preview](media/v2-update-explorer/preview-well.png)](media/v2-update-explorer/preview-well.png#lightbox)

Konkrétní datové prvky můžete ze svých aktuálních dat odebrat tak, že vyberete ovládací prvek červené **odstranění** (odpadkový koš) na levé straně elementu. Také vám umožňuje řídit, jak se jednotlivé prvky zobrazí v grafu. Můžete zvolit, že chcete přidat minimální/maximální stíny, datové body, posunout element v čase a vizualizovat instanci, která je tímto způsobem. 

Kromě toho vám ovládací prvek průzkumy umožňuje snadno vytvářet časová posunutí a bodový graf.  

  [možnosti vzhledu ![](media/v2-update-explorer/well-layout-options.png)](media/v2-update-explorer/well-layout-options.png#lightbox)

> [!NOTE]
> Pokud se zobrazí následující zpráva, tato instance nemá žádná data během vybraného časového intervalu. Chcete-li tento problém vyřešit, zvyšte časový rozsah nebo potvrďte, že instance přenáší data.
>
> ![Žádná oznámení k datům](media/v2-update-explorer/no-data-warning.png)

## <a name="5-chart-panel"></a>5. panel grafů

Graf umožňuje zobrazovat instance časových řad jako řádky. Klepnutím na ovládací prvky webového zvětšit grafu můžete sbalit panel prostředí, datový model a čas span ovládacích panelech. 

  [Přehled grafu ![náhled](media/v2-update-explorer/chart-overview.png)](media/v2-update-explorer/chart-overview.png#lightbox)

1. **Typ grafu**: Určuje, které datové prvky jsou k dispozici pro vizualizaci.

1. **Velikost intervalu**: nástroj dráha velikosti intervalu vám umožní přiblížit nebo oddálit intervaly ve stejném časovém intervalu. To poskytuje přesnější kontrolu nad pohybem mezi velkými řezy času, které znázorňují hladké trendy až po milisekundy, což vám umožní zobrazit podrobné a vysoce rozlišení vašich dat. Výchozí počáteční bod posuvníku je nastaven jako optimální pohled na data z výběru. vyvážení řešení, rychlost dotazu a členitost.

1. **Lupa a posouvání**: kliknutím na tento ovládací prvek můžete graf zvětšit a posunout.

1. **Ovládací prvek osy y**: cyklicky dostupné možnosti zobrazení osy y:

    * `Stacked`: Každý řádek obsahuje jednotlivé osy y.
    * `Overlap`: použijte ho k skládání více řádků na stejnou osu Y, přičemž se mění data osy Y na základě vybraného řádku.
    * `Shared`: Zobrazují na společném všechna data osy y.

1. **Element marker**: aktuálně vybraný datový prvek a jeho přidružené podrobnosti.

Kliknutím **levým** na datový bod v aktuálním grafu a podržením myši a přetažením vybrané oblasti na koncový bod podle vlastního výběru můžete přejít k podrobnostem konkrétního datového řezu. Klikněte **pravým tlačítkem myši** na šedou, vybranou oblast a klikněte na tlačítko **Zvětšit** , jak je znázorněno níže. Můžete také zobrazit a stáhnout události telemetrie ve vybraném časovém rozmezí.

  [přiblížení ![náhledu grafu](media/v2-update-explorer/preview-chart-zoom.png)](media/v2-update-explorer/preview-chart-zoom.png#lightbox)

Po provedení akce **Lupa** se zobrazí Vybraná datová sada. Vyberte ovládací prvek formát, který bude procházet tři reprezentace osy y vašich Time Series Insights dat.

  [osa y ![náhled grafu](media/v2-update-explorer/standard-chart.png)](media/v2-update-explorer/standard-chart.png#lightbox)

Tady vidíte příklad **překrývající se grafu**:

  [![překrývající se možnost grafu](media/v2-update-explorer/overlapping-chart.png)](media/v2-update-explorer/overlapping-chart.png#lightbox)

Tlačítko **Další akce** rozbalí, aby se zobrazila možnost **Stáhnout jako sdílený svazek clusteru** a **Export do Power BI** možností.

  [možnost ![další akce](media/v2-update-explorer/more-actions-icon.png)](media/v2-update-explorer/more-actions-icon.png#lightbox)

Přečtěte si o [konektoru Time Series Insights native Power BI](concepts-power-bi.md).

## <a name="6-time-editor-panel"></a>6. panel editoru času

Při práci s Time Series Insights nejdříve vyberete časový rozsah. Vybraný časový rozsah bude řídit datovou sadu, která je k dispozici pro práci s pomůckami Time Series Insights aktualizace.

  [panel pro výběr ![ho času](media/v2-update-explorer/timeline-element.png)](media/v2-update-explorer/timeline-element.png#lightbox)

> [!TIP]
> Část časové osy je zvýrazněna žlutou nebo oranžovou barvou, aby označovala rozsah dat, která jsou k dispozici v teplém úložišti.

Následující webové ovládací prvky jsou k dispozici v Time Series Insights aktualizace pro výběr rozsahu pracovní doby. 

  [Kontrola dobré kontroly ![](media/v2-update-explorer/exploration-well-control.png)](media/v2-update-explorer/exploration-well-control.png#lightbox)

1. **Ovládací prvek posuvníku rozsahu vnitřního data**: pomocí dvou ovládacích prvků koncového bodu je můžete přetáhnout do požadovaného časového rozsahu. Tento vnitřní rozsah dat je omezen ovládacím prvkem posuvník rozsahu vnějšího data.

1. **Zvýšit a snížit datum rozsahu tlačítka**: zvýšení nebo snížení času span tak, že vyberete buď tlačítko pro požadovaný interval.

1. **Časový interval sbalení ovládacího prvku**: Tento webový ovládací prvek umožňuje skrýt všechny ovládací prvky kromě nástroje posuvníku rozsahu vnitřního data.

1. **Ovládací prvek posuvník rozsahu vnějšího data**: pomocí ovládacích prvků koncového bodu můžete vybrat rozsah vnějších kalendářních dat, který bude k dispozici pro ovládací prvek rozsahu vnitřního data.

1. **Ovládací prvek posuvníku časového rozsahu**: použijte ho k rychlému přepínání mezi přednastavenými výběry časových rozsahů, například posledních **30 minut**, **posledních 12 hodin**nebo **vlastním rozsahem**. Změna této hodnoty se změní také k dispozici interval rozsahy popsané v nástroje jezdce velikost intervalu.

   [![na panel výběru a z něj](media/v2-update-explorer/to-and-from-element.png)](media/v2-update-explorer/to-and-from-element.png#lightbox)

## <a name="7-app-bar"></a>7. panel aplikací

V horní části aplikace Time Series Insights se zobrazí navigační panel Time Series Insights Preview. Nabízí následující funkce:

### <a name="current-session-share-link-control"></a>Ovládací prvek odkazu sdílenou složku aktuální relace

  [ikona sdílené složky ![](media/v2-update-explorer/share-icon.png)](media/v2-update-explorer/share-icon.png#lightbox)

Vyberte ikonu nové **sdílené složky** a sdílejte odkaz na adresu URL s týmem.

  [![sdílet adresu URL vaší instance](media/v2-update-explorer/share-your-view.png)](media/v2-update-explorer/share-your-view.png#lightbox)

### <a name="tenant-section"></a>Část týkající se tenanta

  [![výběr tenanta](media/v2-update-explorer/tenant-selection.png)](media/v2-update-explorer/tenant-selection.png#lightbox)

* Zobrazí vaše aktuální Time Series Insights informace o přihlašovacím účtu.
* Použijte ho k přepínání mezi dostupnými Time Series Insights motivy.
* Použijte ho k zobrazení [ukázkové webové aplikace](https://insights.timeseries.azure.com/preview/demo)ve verzi Preview.

### <a name="theme-selection"></a>Výběr motivu

Pokud chcete vybrat nový motiv, vyberte ikonu vašeho profilu, která se nachází v pravém horním rohu. Pak vyberte **změnit motiv**.

  [Výběr motivu ![](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> Výběr jazyka je také k dispozici tak, že vyberete ikonu vašeho profilu.

Azure čas Series Insights ve verzi Preview podporuje dva motivy:

* **Světlý motiv**: výchozí motiv zobrazený v celém tomto dokumentu.
* **Tmavý motiv**: vykreslí Průzkumníka, jak je znázorněno zde:

  [![vybraný tmavý motiv](media/v2-update-explorer/dark-theme-selected.png)](media/v2-update-explorer/dark-theme-selected.png#lightbox)

## <a name="s1s2-environment-controls"></a>Ovládací prvky prostředí S1/S2

### <a name="preview-terms-panel"></a>Panel podmínek náhledu

Tato část platí jenom pro stávající prostředí S1, S2, které se pokouší použít v aktualizované uživatelské rozhraní v Průzkumníku. Možná budete chtít použít všeobecně dostupný produkt a náhled v kombinaci. Přidali jsme některé funkce z existující uživatelského rozhraní do Průzkumníka aktualizované, ale můžete získat plnou funkčnost uživatelského rozhraní pro existující Průzkumníka Time Series Insights S1/S2 prostředí. 

Místo hierarchie se zobrazí panel Time Series Insights podmínky, kde můžete ve svém prostředí definovat dotazy. Použijte ho k filtrování dat na základě predikátu.

  [![, kde panel dotazů](media/v2-update-explorer/s1-s2-preview-query.png)](media/v2-update-explorer/s1-s2-preview-query.png#lightbox)

Panel editor podmínek čas Series Insights ve verzi Preview má následující parametry:

**WHERE**: použijte klauzuli WHERE k rychlému filtrování událostí pomocí sady operandů uvedených v následující tabulce. Pokud spustíte hledání tak, že vyberete operand, predikát automaticky aktualizovat podle hledání. Podporované typy operandů zahrnují následující:

| Operace | Podporované typy   | Poznámky: |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, časový interval | |
| `=`, `!=`, `<>` | Řetězec, logická hodnota, Double, DateTime, časový interval, s hodnotou NULL |
| `IN` | Řetězec, logická hodnota, Double, DateTime, časový interval, s hodnotou NULL | Všechny operandy musí být stejného typu nebo konstanta NULL. |
| `HAS` | Řetězec | Na pravé straně jsou povoleny pouze konstantní řetězcové literály. Prázdný řetězec a hodnota NULL nejsou povoleny. |

Další informace o podporovaných operacích dotazů a datových typech najdete v tématu [výraz Time Series Expression (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax).

### <a name="examples-of-where-clauses"></a>Příklady klauzulí WHERE

  [Příklady klauzule WHERE ![](media/v2-update-explorer/example-queries.png)](media/v2-update-explorer/example-queries.png#lightbox)

**Measure**: rozevírací seznam, který zobrazuje všechny číselné sloupce (**dvojité**), můžete použít jako prvky pro aktuální graf.

**Rozdělit podle**: Tento rozevírací seznam zobrazuje všechny dostupné kategorií sloupce (řetězce) v modelu, podle kterých můžete data seskupovat. Můžete přidat až pět podmínek pro zobrazení na stejné ose x. Zadejte požadované parametry a pak vyberte **Přidat** a přidejte nový termín.

  [![dotazování a filtrovaný pohled na jeden](media/v2-update-explorer/s1-s2-preview-filtered-view.png)](media/v2-update-explorer/s1-s2-preview-filtered-view.png#lightbox)

Prvky na panelu grafu můžete zobrazit a skrýt tak, že vyberete ikonu Visible, jak je znázorněno na následujícím obrázku. Pokud chcete úplně odebrat dotazy, vyberte červené **X**.

  [![zrušit dotaz a filtrovanou možnost](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png)](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png#lightbox)

## <a name="next-steps"></a>Další kroky

- Přečtěte si o službě [Storage a](./time-series-insights-update-storage-ingress.md) příchozím přenosu ve službě Azure Time Series Insights Preview.

- Přečtěte si dokument Time Series Insights Preview při [modelování dat](./time-series-insights-update-tsm.md).

- Naučte [se diagnostikovat a řešit potíže s](./time-series-insights-update-how-to-troubleshoot.md) instancí Time Series Insights.
