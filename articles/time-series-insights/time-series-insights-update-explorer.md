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
ms.date: 10/07/2019
ms.custom: seodec18
ms.openlocfilehash: f09de6fee4c9de39d98ec66714686ab1e805c4cd
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014884"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Vizualizace dat v průzkumníku (Preview)

Tento dokument popisuje uživatelské rozhraní a funkce uživatelského rozhraní [ukázkové webové aplikace](https://insights.timeseries.azure.com/preview/demo)Azure Time Series Insights Preview. Konkrétně popisuje rozložení hostovaného vzorku, možností přizpůsobení rozhraní a navigace prostřednictvím poskytnuté ukázky.

## <a name="prerequisites"></a>Požadavky

Pokud chcete začít s Průzkumníkem Azure Time Series Insights Preview, musíte:

* Máte nastavení prostředí Time Series Insights. Pokud chcete získat další informace o zřizování instance, zkuste kurz [Azure Time Series Insights Preview](./time-series-insights-update-create-environment.md) .
* [Poskytněte přístup k datům](./time-series-insights-data-access.md) Time Series Insights prostředí, které jste pro účet vytvořili. Můžete poskytnout přístup k i další uživatele tak, aby sami.
* Přidání zdroje událostí do prostředí Time Series Insights pro vložení dat do prostředí:
  * Přečtěte si, [jak se připojit k centru událostí](./time-series-insights-how-to-add-an-event-source-eventhub.md).
  * Přečtěte si, [jak se připojit ke službě IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="learn-about-the-preview-explorer"></a>Další informace o Průzkumníkovi Preview

Průzkumník Azure čas Series Insights ve verzi Preview se skládá z následujících elementů:

[![zobrazení Průzkumníka](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

- <a href="#environment-drop-down-list">Panel prostředí</a>: zobrazí vaše Azure Time Series Insights prostředí.
- <a href="#navigation-menu">Navigační nabídka</a>: slouží k přepínání mezi stránkami **analyzovat** a **model** .
- <a href="#hierarchy-tree">Strom hierarchie</a>: slouží k výběru konkrétního modelu a datových prvků, které se mají vynést do grafu.
- <a href="#preview-well">Time Series Well</a>: zobrazuje aktuálně vybrané datové prvky ve formátu tabulky s barevným kódováním.
- <a href="#preview-chart">Panely grafu</a>: Zobrazí aktuální pracovní grafu.
- <a href="#time-editor-panel">Časová osa</a>: použijte ji k úpravě rozsahu pracovní doby.
- <a href="#navigation-panel">Panel aplikace</a>: obsahuje vaše možnosti správy uživatelů, jako je například aktuální tenant. Můžete ji použít ke změně nastavení motivu a jazyka.

## <a name="environment-drop-down-list"></a>Rozevírací seznam prostředí

V rozevíracím seznamu prostředí se zobrazí všechna Time Series Insights prostředí, ke kterým máte přístup. Seznam obsahuje prostředí s průběžnými platbami, jako je například Time Series Insights verze Preview. Seznam obsahuje také prostředí S1/S2, která jsou obecně dostupná.

1. Vyberte šipku rozevíracího seznamu vedle zobrazeného prostředí.

   [![ovládacích panelů](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. Pak vyberte prostředí, které chcete.

## <a name="navigation-menu"></a>Navigační nabídka

  [![navigační nabídku](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

Pomocí navigační nabídky můžete vybrat mezi dvěma zobrazeními:

* **Analyzovat**: použijte ho k vytvoření grafu a provádění obsáhlých analýz pro modelovaná nebo nemodelovaná data časových řad.
* **Model**: použijte ho k vložení nových typů, hierarchií a instancí Preview Time Series Insights do modelu Time Series Insights.

## <a name="hierarchy-tree"></a>Strom hierarchie

Stromová struktura hierarchie zobrazuje vybrané datové prvky, které zahrnují modely, specifická zařízení a snímače na vašich zařízeních.

### <a name="model-search-panel"></a>Panel hledání modelu

Pomocí panelu hledání modelů můžete snadno vyhledávat a procházet hierarchii modelu časové řady a vyhledat konkrétní instance časových řad, které chcete zobrazit v grafu. Jakmile vyberete své instance, přidají se do aktuálního grafu i do datového zásobníku.

  [![panelu hledání modelu](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>Vytváření modelů

Azure Time Series Insights Preview podporuje operace úplného vytváření, čtení, aktualizace a odstranění (CRUD) v modelu časové řady.

* **Typ modelu časové řady**: můžete použít typy Time Series Insights k definování proměnných nebo vzorců pro provádění výpočtů. Jsou přidruženy k dané instanci Time Series Insights. Typ může mít jednu nebo více proměnných.
* **Časové řady modelu hierarchie**: hierarchií jsou systematické organizace vaše data. Hierarchie znázornění vztahy mezi různými entitami ve vašich datech Time Series Insights.
* **Instance řady modelu čas**: instance jsou časové řady sami. Ve většině případů se jedná o **DeviceID** nebo **AssetID**, což je jedinečný identifikátor assetu v prostředí.

Další informace o modelu časové řady, naleznete v tématu [časy řady modely](./time-series-insights-update-tsm.md).

## <a name="preview-well"></a>Dobré verze Preview

Zobrazuje také pole instancí a další metadata přidružená k vybraným instancím Time Series Insights. Zaškrtnutím políček na pravé straně můžete skrýt nebo zobrazit konkrétní instance z aktuálního grafu. Můžete také odebrat konkrétní datové prvky z aktuální datové sady tím, že vyberete ovládací prvek červené **odstranění** (odpadkový koš) na levé straně elementu.

  [![také ve verzi Preview](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

Chcete-li změnit konfiguraci rozložení stránky diagramu **analýzy** , vyberte ikonu se třemi tečkami v pravém horním rohu:

  [možnosti rozložení ![telemetrie](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> Pokud se zobrazí následující zpráva, tato instance nemá žádná data během vybraného časového intervalu. Chcete-li tento problém vyřešit, zvyšte časový rozsah nebo potvrďte, že instance přenáší data.
>
> ![Žádná oznámení k datům](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>Náhled grafu

Pomocí grafu můžete zobrazit Time Series Insights instance jako řádky. Panel prostředí, datový model a ovládací panely časového rozsahu můžete sbalit tak, že vyberete webové ovládací prvky, aby byl graf větší.

  [Přehled grafu ![náhled](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

- **Vybraný rozsah kalendářních**dat: Určuje, které datové prvky jsou k dispozici pro vizualizaci.

- **Nástroj pro posuvník rozsahu vnitřního data**: pomocí dvou ovládacích prvků koncového bodu je můžete přetáhnout v požadovaném časovém rozsahu.

- **Časové rozpětí sbalení ovládacího prvku**: sbalení a rozbalí editor span panel čas.

- **Řízení formátu osy y**: cyklicky dostupné možnosti zobrazení osy y:

    * `Default`: každý řádek má jednotlivou osu y.
    * `Stacked`: použijte ho k skládání více řádků na stejnou osu y, přičemž se mění data osy y na základě vybraného řádku.
    * `Shared`: všechna data na ose y se zobrazují společně.

- **Aktuální datový element**: aktuálně vybraný datový prvek a jeho přidružené podrobnosti.

Chcete-li přejít k podrobnostem konkrétního datového řezu, klikněte levým na datový bod v aktuálním grafu a přetáhněte vybranou oblast na koncový bod dle svého výběru. Klikněte pravým tlačítkem myši na šedou vybranou oblast a vyberte možnost **přiblížení**, jak je znázorněno na následujícím obrázku:

  [přiblížení ![náhledu grafu](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

Po provedení akce **Lupa** se zobrazí Vybraná datová sada. Vyberte ovládací prvek formát osy y, který bude cyklicky procházet tři reprezentace na ose y Time Series Insights dat.

  [osa y ![náhled grafu](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

Tady vidíte příklad sdílených OS Y:

  [Náhled sdílených OS Y ![](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>Panel editoru času

Při práci s čas Series Insights ve verzi Preview, nejprve vyberte časový rozsah. Vybraný časový rozsah řídí datovou sadu, která je k dispozici pro práci s Time Series Insights ve verzi Preview. V Time Series Insights Preview jsou k dispozici následující webové ovládací prvky pro výběr rozsahu pracovní doby:

  [panel pro výběr ![ho času](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. **Nástroj pro posuvník rozsahu vnitřního data**: pomocí dvou ovládacích prvků koncového bodu je můžete přetáhnout v požadovaném časovém rozsahu. Tento vnitřní rozsah dat je omezen ovládacím prvkem posuvník rozsahu vnějšího data.

1. **Zvýšit a snížit datum rozsahu tlačítka**: zvýšení nebo snížení času span tak, že vyberete buď tlačítko pro požadovaný interval.

1. **Časový interval sbalení ovládacího prvku**: Tento webový ovládací prvek umožňuje skrýt všechny ovládací prvky kromě nástroje posuvníku rozsahu vnitřního data.

1. **Ovládací prvek posuvník rozsahu vnějšího data**: pomocí ovládacích prvků koncového bodu můžete vybrat rozsah vnějších kalendářních dat, který bude k dispozici pro ovládací prvek rozsahu vnitřního data.

1. **Rychlý rozevírací seznam rozsah data a**času: použijte ho k rychlému přepínání mezi přednastavenými výběry časových rozsahů, například posledních **30 minut**, **posledních 12 hodin**nebo **vlastním rozsahem**. Změna této hodnoty se změní také k dispozici interval rozsahy popsané v nástroje jezdce velikost intervalu.

1. **Nástroj posuvníku velikosti intervalu**: používá se k přiblížení a oddálení v intervalech za stejné časové období. Tato akce zajišťuje přesnější kontrolu nad pohyb mezi velké časové úseky. Zobrazuje v milisekundách hladké trendy až do řezů. Můžete ji použít k zobrazení podrobných a vysoce vydaných dat s vysokým rozlišením. Výchozí posuvníku počáteční bod je nastaven jako optimální zobrazení dat z vašeho výběru, který vyrovnává rozlišení, rychlost dotazů a členitosti.

1. **Rozsah data a z webového ovládacího**prvku: pomocí tohoto webového ovládacího prvku můžete snadno vybrat rozsahy dat a času, které chcete. Můžete také použít ovládací prvek přepínání různých časových pásmech. Až provedete změny, které se mají použít v aktuálním pracovním prostoru, vyberte **Uložit**.

   [![na panel výběru a z něj](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>Navigační panel

V horní části aplikace Time Series Insights se zobrazí navigační panel Time Series Insights Preview. Nabízí následující funkce:

### <a name="current-session-share-link-control"></a>Ovládací prvek odkazu sdílenou složku aktuální relace

  [ikona sdílené složky ![](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

Vyberte ikonu nové **sdílené složky** a sdílejte odkaz na adresu URL s týmem.

  [![sdílet adresu URL vaší instance](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>Část týkající se tenanta

  [![výběr tenanta](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

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

  [![vybraný tmavý motiv](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>Ovládací prvky prostředí S1/S2

### <a name="preview-terms-panel"></a>Panel podmínek náhledu

Tato část platí jenom pro stávající prostředí S1, S2, které se pokouší použít v aktualizované uživatelské rozhraní v Průzkumníku. Možná budete chtít použít všeobecně dostupný produkt a náhled v kombinaci. Přidali jsme některé funkce z existující uživatelského rozhraní do Průzkumníka aktualizované, ale můžete získat plnou funkčnost uživatelského rozhraní pro existující Průzkumníka Time Series Insights S1/S2 prostředí. 

Místo hierarchie se zobrazí panel Time Series Insights podmínky, kde můžete ve svém prostředí definovat dotazy. Použijte ho k filtrování dat na základě predikátu.

  [![, kde panel dotazů](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

Panel editor podmínek čas Series Insights ve verzi Preview má následující parametry:

**WHERE**: použijte klauzuli WHERE k rychlému filtrování událostí pomocí sady operandů uvedených v následující tabulce. Pokud spustíte hledání tak, že vyberete operand, predikát automaticky aktualizovat podle hledání. Podporované typy operandů zahrnují následující:

| Operace | Podporované typy   | Poznámky: |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, časový interval | |
| `=`, `!=`, `<>` | Řetězec, logická hodnota, Double, DateTime, časový interval, s hodnotou NULL |
| `IN` | Řetězec, logická hodnota, Double, DateTime, časový interval, s hodnotou NULL | Všechny operandy musí být stejného typu nebo konstanta NULL. |
| `HAS` | Řetězec | Na pravé straně jsou povoleny pouze konstantní řetězcové literály. Prázdný řetězec a hodnota NULL nejsou povoleny. |

Další informace o podporovaných operacích dotazů a datových typech najdete v tématu [výraz Time Series Expression (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx).

### <a name="examples-of-where-clauses"></a>Příklady klauzulí WHERE

  [Příklady klauzule WHERE ![](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**Measure**: rozevírací seznam, který zobrazuje všechny číselné sloupce (**dvojité**), můžete použít jako prvky pro aktuální graf.

**Rozdělit podle**: Tento rozevírací seznam zobrazuje všechny dostupné kategorií sloupce (řetězce) v modelu, podle kterých můžete data seskupovat. Můžete přidat až pět podmínek pro zobrazení na stejné ose x. Zadejte požadované parametry a pak vyberte **Přidat** a přidejte nový termín.

  [![dotazování a filtrovaný pohled na jeden](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

Prvky na panelu grafu můžete zobrazit a skrýt tak, že vyberete ikonu Visible, jak je znázorněno na následujícím obrázku. Pokud chcete úplně odebrat dotazy, vyberte červené **X**.

  [![dotazování a filtrovaných zobrazení dvakrát](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>Další kroky

- Přečtěte si o službě [Storage a](./time-series-insights-update-storage-ingress.md) příchozím přenosu ve službě Azure Time Series Insights Preview.
- Přečtěte si dokument Time Series Insights Preview při [modelování dat](./time-series-insights-update-tsm.md).
- Naučte [se diagnostikovat a řešit potíže s](./time-series-insights-update-how-to-troubleshoot.md) instancí Time Series Insights.
