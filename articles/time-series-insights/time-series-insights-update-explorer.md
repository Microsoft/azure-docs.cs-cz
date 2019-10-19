---
title: Vizualizace dat v Průzkumníkovi Azure Time Series Insights Preview | Microsoft Docs
description: Tento článek popisuje funkce a možnosti, které jsou k dispozici v rámci webové aplikace Průzkumník Preview v Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/07/2019
ms.custom: seodec18
ms.openlocfilehash: a1707740d673ea49a4b4494f5d2e6a5753982090
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553406"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Vizualizace dat v průzkumníku (Preview)

Tento dokument popisuje uživatelské rozhraní a funkce uživatelského rozhraní [ukázkové webové aplikace](https://insights.timeseries.azure.com/preview/demo)Azure Time Series Insights Preview. Konkrétně popisuje rozložení hostovaného vzorku, možností přizpůsobení rozhraní a navigace prostřednictvím poskytnuté ukázky.

## <a name="prerequisites"></a>Předpoklady

Pokud chcete začít s Průzkumníkem Azure Time Series Insights Preview, musíte:

* Máte nastavené prostředí Time Series Insights. Pokud chcete získat další informace o zřizování instance, zkuste kurz [Azure Time Series Insights Preview](./time-series-insights-update-create-environment.md) .
* [Poskytněte přístup k datům](./time-series-insights-data-access.md) Time Series Insights prostředí, které jste pro účet vytvořili. Můžete poskytovat přístup i ostatním uživatelům.
* Přidání zdroje událostí do prostředí Time Series Insights pro vložení dat do prostředí:
  * Přečtěte si, [jak se připojit k centru událostí](./time-series-insights-how-to-add-an-event-source-eventhub.md).
  * Přečtěte si, [jak se připojit ke službě IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="learn-about-the-preview-explorer"></a>Další informace o Průzkumníkovi Preview

Průzkumník Preview Azure Time Series Insights se skládá z následujících prvků:

[zobrazení Průzkumníka ![The](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

- <a href="#environment-drop-down-list">Panel prostředí</a>: zobrazí vaše Azure Time Series Insights prostředí.
- <a href="#navigation-menu">Navigační nabídka</a>: slouží k přepínání mezi stránkami **analyzovat** a **model** .
- <a href="#hierarchy-tree">Strom hierarchie</a>: slouží k výběru konkrétního modelu a datových prvků, které se mají vynést do grafu.
- <a href="#preview-well">Time Series Well</a>: zobrazuje aktuálně vybrané datové prvky ve formátu tabulky s barevným kódováním.
- <a href="#preview-chart">Panel grafu</a>: zobrazí aktuální pracovní graf.
- <a href="#time-editor-panel">Časová osa</a>: použijte ji k úpravě rozsahu pracovní doby.
- <a href="#navigation-panel">Panel aplikace</a>: obsahuje vaše možnosti správy uživatelů, jako je například aktuální tenant. Můžete ji použít ke změně nastavení motivu a jazyka.

## <a name="environment-drop-down-list"></a>Rozevírací seznam prostředí

V rozevíracím seznamu prostředí se zobrazí všechna Time Series Insights prostředí, ke kterým máte přístup. Seznam obsahuje prostředí s průběžnými platbami, jako je například Time Series Insights verze Preview. Seznam obsahuje také prostředí S1/S2, která jsou obecně dostupná.

1. Vyberte šipku rozevíracího seznamu vedle zobrazeného prostředí.

   [ovládací panel ![The](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. Pak vyberte prostředí, které chcete.

## <a name="navigation-menu"></a>Navigační nabídka

  [navigační nabídka ![The](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

Pomocí navigační nabídky můžete vybrat mezi dvěma zobrazeními:

* **Analyzovat**: použijte ho k vytvoření grafu a provádění obsáhlých analýz pro modelovaná nebo nemodelovaná data časových řad.
* **Model**: použijte ho k vložení nových typů, hierarchií a instancí Preview Time Series Insights do modelu Time Series Insights.

## <a name="hierarchy-tree"></a>Strom hierarchie

Stromová struktura hierarchie zobrazuje vybrané datové prvky, které zahrnují modely, specifická zařízení a snímače na vašich zařízeních.

### <a name="model-search-panel"></a>Panel hledání modelu

Pomocí panelu hledání modelů můžete snadno vyhledávat a procházet hierarchii modelu časové řady a vyhledat konkrétní instance časových řad, které chcete zobrazit v grafu. Jakmile vyberete své instance, přidají se do aktuálního grafu i do datového zásobníku.

  [panel hledání modelu ![The](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>Vytváření modelů

Azure Time Series Insights Preview podporuje operace úplného vytváření, čtení, aktualizace a odstranění (CRUD) v modelu časové řady.

* **Typ modelu časové řady**: můžete použít typy Time Series Insights k definování proměnných nebo vzorců pro provádění výpočtů. Jsou přidruženy k dané instanci Time Series Insights. Typ může mít jednu nebo více proměnných.
* **Hierarchie modelu časové řady**: hierarchie jsou systematické organizace vašich dat. Hierarchie znázorňuje vztahy mezi různými entitami ve vašich Time Series Insightsch datech.
* **Instance modelu časové řady**: instance jsou samotné časové řady. Ve většině případů se jedná o **DeviceID** nebo **AssetID**, což je jedinečný identifikátor assetu v prostředí.

Další informace o modelu časových řad najdete v tématu [modely časů řady](./time-series-insights-update-tsm.md).

## <a name="preview-well"></a>Dobré verze Preview

Zobrazuje také pole instancí a další metadata přidružená k vybraným instancím Time Series Insights. Zaškrtnutím políček na pravé straně můžete skrýt nebo zobrazit konkrétní instance z aktuálního grafu. Můžete také odebrat konkrétní datové prvky z aktuální datové sady tím, že vyberete ovládací prvek červené **odstranění** (odpadkový koš) na levé straně elementu.

  [Náhled ![The dobře](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

Chcete-li změnit konfiguraci rozložení stránky diagramu **analýzy** , vyberte ikonu se třemi tečkami v pravém horním rohu:

  [možnosti rozložení ![Telemetry](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> Pokud se zobrazí následující zpráva, tato instance nemá žádná data během vybraného časového intervalu. Chcete-li tento problém vyřešit, zvyšte časový rozsah nebo potvrďte, že instance přenáší data.
>
> ![Žádná oznámení k datům](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>Náhled grafu

Pomocí grafu můžete zobrazit Time Series Insights instance jako řádky. Panel prostředí, datový model a ovládací panely časového rozsahu můžete sbalit tak, že vyberete webové ovládací prvky, aby byl graf větší.

  [Přehled grafu ![Preview](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

- **Vybraný rozsah kalendářních**dat: Určuje, které datové prvky jsou k dispozici pro vizualizaci.

- **Nástroj pro posuvník rozsahu vnitřního data**: pomocí dvou ovládacích prvků koncového bodu je můžete přetáhnout v požadovaném časovém rozsahu.

- **Ovládací prvek pro sbalení časového rozsahu**: sbalí a rozbalí Editor panelu časového rozsahu.

- **Řízení formátu osy y**: cyklicky dostupné možnosti zobrazení osy y:

    * `Default`: každý řádek má jednotlivou osu y.
    * `Stacked`: používá se k skládání více řádků na stejnou osu y, přičemž se mění data osy y na základě vybraného řádku.
    * `Shared`: všechna data osy y se zobrazují společně.

- **Aktuální datový prvek**: aktuálně vybraný datový prvek a jeho přidružené podrobnosti.

Chcete-li přejít k podrobnostem konkrétního datového řezu, klikněte levým na datový bod v aktuálním grafu a přetáhněte vybranou oblast na koncový bod dle svého výběru. Klikněte pravým tlačítkem myši na šedou vybranou oblast a vyberte možnost **přiblížení**, jak je znázorněno na následujícím obrázku:

  [![Preview přiblížení grafu](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

Po provedení akce **Lupa** se zobrazí Vybraná datová sada. Vyberte ovládací prvek formát osy y, který bude cyklicky procházet tři reprezentace na ose y Time Series Insights dat.

  [![Preview osa y grafu](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

Tady vidíte příklad sdílených OS Y:

  [![Preview sdílené osy Y](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>Panel editoru času

Když pracujete s Time Series Insights Preview, nejdřív vyberte časový rozsah. Vybraný časový rozsah řídí datovou sadu, která je k dispozici pro práci s Time Series Insights ve verzi Preview. V Time Series Insights Preview jsou k dispozici následující webové ovládací prvky pro výběr rozsahu pracovní doby:

  [panel výběru ![Time](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. **Nástroj pro posuvník rozsahu vnitřního data**: pomocí dvou ovládacích prvků koncového bodu je můžete přetáhnout v požadovaném časovém rozsahu. Tento vnitřní rozsah dat je omezen ovládacím prvkem posuvník rozsahu vnějšího data.

1. Tlačítko pro **zvýšení a snížení rozsahu data**: Zvyšte nebo snižte časový rozsah tak, že vyberete jedno tlačítko pro požadovaný interval.

1. **Časový interval sbalení ovládacího prvku**: Tento webový ovládací prvek umožňuje skrýt všechny ovládací prvky kromě nástroje posuvníku rozsahu vnitřního data.

1. **Ovládací prvek posuvník rozsahu vnějšího data**: pomocí ovládacích prvků koncového bodu můžete vybrat rozsah vnějších kalendářních dat, který bude k dispozici pro ovládací prvek rozsahu vnitřního data.

1. **Rychlý rozevírací seznam rozsah data a**času: použijte ho k rychlému přepínání mezi přednastavenými výběry časových rozsahů, například posledních **30 minut**, **posledních 12 hodin**nebo **vlastním rozsahem**. Změna této hodnoty změní také rozsahy dostupných intervalů, které jsou popsány v nástroji Slider velikost intervalu.

1. **Nástroj posuvníku velikosti intervalu**: používá se k přiblížení a oddálení v intervalech za stejné časové období. Tato akce poskytuje přesnější kontrolu nad pohybem mezi velkými řezy času. Zobrazuje v milisekundách hladké trendy až do řezů. Můžete ji použít k zobrazení podrobných a vysoce vydaných dat s vysokým rozlišením. Výchozí počáteční bod posuvníku je nastaven jako optimální pohled na data z výběru, který vyrovnává rozlišení, rychlost dotazování a členitost.

1. **Rozsah data a z webového ovládacího**prvku: pomocí tohoto webového ovládacího prvku můžete snadno vybrat rozsahy dat a času, které chcete. Ovládací prvek lze také použít k přepínání mezi různými časovými pásmy. Až provedete změny, které se mají použít v aktuálním pracovním prostoru, vyberte **Uložit**.

   [![To a z panelu výběru](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>Navigační panel

V horní části aplikace Time Series Insights se zobrazí navigační panel Time Series Insights Preview. Nabízí následující funkce:

### <a name="current-session-share-link-control"></a>Ovládací prvek odkazu na sdílenou složku aktuální relace

  [ikona ![Share](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

Vyberte ikonu nové **sdílené složky** a sdílejte odkaz na adresu URL s týmem.

  [![Share vaše adresa URL instance](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>Oddíl tenant

  [![Tenant výběr](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

* Zobrazí vaše aktuální Time Series Insights informace o přihlašovacím účtu.
* Použijte ho k přepínání mezi dostupnými Time Series Insights motivy.
* Použijte ho k zobrazení [ukázkové webové aplikace](https://insights.timeseries.azure.com/preview/demo)ve verzi Preview.

### <a name="theme-selection"></a>Výběr motivu

Pokud chcete vybrat nový motiv, vyberte ikonu vašeho profilu, která se nachází v pravém horním rohu. Pak vyberte **změnit motiv**.

  [![Theme výběr](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> Výběr jazyka je také k dispozici tak, že vyberete ikonu vašeho profilu.

Azure Time Series Insights Preview podporuje dva motivy:

* **Světlý motiv**: výchozí motiv zobrazený v celém tomto dokumentu.
* **Tmavý motiv**: vykreslí Průzkumníka, jak je znázorněno zde:

  [![Selected tmavý motiv](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>Ovládací prvky prostředí S1/S2

### <a name="preview-terms-panel"></a>Panel podmínek náhledu

Tato část se týká pouze stávajících prostředí S1/S2, která se pokoušejí použít Průzkumníka v aktualizovaném uživatelském rozhraní. Možná budete chtít použít všeobecně dostupný produkt a náhled v kombinaci. Přidali jsme některé funkce z existujícího uživatelského rozhraní do aktualizovaného Průzkumníka, ale v existujícím Průzkumníkovi Time Series Insights můžete získat plné prostředí uživatelského rozhraní pro prostředí S1/S2. 

Místo hierarchie se zobrazí panel Time Series Insights podmínky, kde můžete ve svém prostředí definovat dotazy. Použijte ho k filtrování dat na základě predikátu.

  [panel dotazu ![Where](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

Panel editoru podmínek Time Series Insights Preview používá následující parametry:

**WHERE**: použijte klauzuli WHERE k rychlému filtrování událostí pomocí sady operandů uvedených v následující tabulce. Pokud provedete hledání výběrem operandu, predikát se automaticky aktualizuje na základě tohoto hledání. Podporované typy operandů zahrnují následující:

| Operace | Podporované typy   | Poznámky |
| --- | --- | --- |
| `<`, `>` `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | String, bool, Double, DateTime, TimeSpan, NULL |
| `IN` | String, bool, Double, DateTime, TimeSpan, NULL | Všechny operandy by měly být stejného typu nebo mít nulovou konstantu. |
| `HAS` | Řetězec | Na pravé straně jsou povoleny pouze konstantní řetězcové literály. Prázdný řetězec a hodnota NULL nejsou povoleny. |

Další informace o podporovaných operacích dotazů a datových typech najdete v tématu [výraz Time Series Expression (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx).

### <a name="examples-of-where-clauses"></a>Příklady klauzulí WHERE

  [Příklady klauzule ![Where](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**Measure**: rozevírací seznam, který zobrazuje všechny číselné sloupce (**dvojité**), můžete použít jako prvky pro aktuální graf.

**Rozdělit podle**: Tento rozevírací seznam zobrazuje všechny dostupné kategorií sloupce (řetězce) v modelu, podle kterých můžete data seskupovat. Můžete přidat až pět podmínek pro zobrazení na stejné ose x. Zadejte požadované parametry a pak vyberte **Přidat** a přidejte nový termín.

  [![Queried a filtrované zobrazení 1](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

Prvky na panelu grafu můžete zobrazit a skrýt tak, že vyberete ikonu Visible, jak je znázorněno na následujícím obrázku. Pokud chcete úplně odebrat dotazy, vyberte červené **X**.

  [![Queried a filtrované zobrazení dvě](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>Další kroky

- Přečtěte si o službě [Storage a](./time-series-insights-update-storage-ingress.md) příchozím přenosu ve službě Azure Time Series Insights Preview.
- Přečtěte si dokument Time Series Insights Preview při [modelování dat](./time-series-insights-update-tsm.md).
- Naučte [se diagnostikovat a řešit potíže s](./time-series-insights-update-how-to-troubleshoot.md) instancí Time Series Insights.
