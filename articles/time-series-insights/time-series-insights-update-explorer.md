---
title: Vizualizace dat v Průzkumníku Azure čas Series Insights ve verzi Preview | Dokumentace Microsoftu
description: Tento článek popisuje funkce a možnosti, které jsou k dispozici ve webové aplikaci Průzkumník Azure čas Series Insights ve verzi Preview.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/03/2019
ms.custom: seodec18
ms.openlocfilehash: c4f3053063ce33d2777387da2c53effd61b05f1a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399862"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Vizualizace dat v Průzkumníku ve verzi Preview

Tento dokument popisuje uživatelské rozhraní a funkce uživatelského prostředí a rozhraní Azure čas Series Insights Preview [ukázkovou webovou aplikaci](https://insights.timeseries.azure.com/preview/demo). Konkrétně popisuje rozložení ukázkové prostředí, možnosti vlastního nastavení rozhraní a navigace prostřednictvím zadaná ukázek.

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít s Průzkumníkem Azure čas Series Insights ve verzi Preview, musíte mít:

* Máte nastavení prostředí Time Series Insights. Chcete-li další informace o zřízení instance, zkuste [Azure čas Series Insights ve verzi Preview](./time-series-insights-update-create-environment.md) kurzu.
* [Poskytuje přístup k datům](./time-series-insights-data-access.md) do prostředí Time Series Insights, který jste vytvořili pro účet. Můžete poskytnout přístup k i další uživatele tak, aby sami.
* Přidání zdroje událostí do prostředí Time Series Insights k odesílání dat do prostředí:
  * Přečtěte si [jak se připojit k Centru událostí](./time-series-insights-how-to-add-an-event-source-eventhub.md).
  * Přečtěte si [postup připojení do služby IoT hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="learn-about-the-preview-explorer"></a>Další informace o Průzkumníku ve verzi Preview

Průzkumník Azure čas Series Insights ve verzi Preview se skládá z následujících elementů:

[![Zobrazení Průzkumníka](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

- <a href="#environment-drop-down-list">Panel prostředí</a>: Zobrazí prostředí Azure Time Series Insights.
- <a href="#navigation-menu">Navigační nabídka</a>: Můžete přepínat mezi **analyzovat** a **modelu** stránky.
- <a href="#hierarchy-tree">Hierarchie stromu</a>: Můžete vybrat konkrétní prvky modelu a data pro graf.
- <a href="#preview-well">Time series dobře</a>: Aktuálně vybraných datových prvků se zobrazí v tabulkovém formátu s kódováním barev.
- <a href="#preview-chart">Panely grafu</a>: Zobrazí aktuální pracovní grafu.
- <a href="#time-editor-panel">Časová osa</a>: Použijte ho ke změně vaší pracovní časový rozsah.
- <a href="#navigation-panel">Panel aplikace</a>: Obsahuje vaše možnosti správy uživatelů, jako je například aktuálního tenanta. Můžete ho změnit motiv a jazykové nastavení.

## <a name="environment-drop-down-list"></a>Rozevírací seznam prostředí

Rozevírací seznam prostředí zobrazí všechna prostředí Time Series Insights, ke kterým máte přístup. Seznam obsahuje s průběžnými platbami prostředí, jako je čas Series Insights ve verzi Preview. Seznam obsahuje také S1/S2 prostředí, které jsou obecně dostupné.

1. Vyberte šipku rozevíracího seznamu vedle zobrazeného prostředí.

   [![Ovládací prvek panel](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. Vyberte prostředí, ve kterém chcete.

## <a name="navigation-menu"></a>Navigační nabídka

  [![V navigační nabídce](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

Použijte navigační nabídce na výběr mezi dvě zobrazení:

* **Analýza**: Můžete graf a provádět bohaté analýzy na dat Modelovaný nebo nemodelované časových řad.
* **Model**: Použijte ho tak, aby nabízel nové typy čas Series Insights ve verzi Preview, hierarchie a instance pro váš model Time Series Insights.

## <a name="hierarchy-tree"></a>Hierarchie stromu

Strom hierarchie dál zobrazuje vybrané datové prvky, které zahrnují modely, konkrétní zařízení a senzorů v zařízeních.

### <a name="model-search-panel"></a>Panel hledání modelu

Na panelu hledání modelu můžete snadno vyhledat a procházet hierarchii modelu časové řady najít instance řady určitou dobu, kterou chcete zobrazit v grafu. Po výběru instancím přidané do aktuální graf a data dobře.

  [![Na panelu hledání modelu](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>Vytváření modelu

Úplné Azure čas Series Insights ve verzi Preview podporuje vytváření, čtení, aktualizace a odstranění (CRUD) operací na váš Model časové řady.

* **Časové řady Model typu**: Typy Time Series Insights můžete použít k definování proměnné nebo vzorce pro provádění výpočtů. Jsou přidruženy k dané instanci služby Time Series Insights. Typ může mít jednu nebo více proměnných.
* **Časové řady modelu hierarchie**: Hierarchie jsou systematické organizace vaše data. Hierarchie znázornění vztahy mezi různými entitami ve vašich datech Time Series Insights.
* **Instance řady modelu čas**: Instance jsou časové řady sami. Ve většině případů jsou **DeviceID** nebo **AssetID**, který je jedinečný identifikátor prostředku v prostředí.

Další informace o modelu časové řady, naleznete v tématu [časy řady modely](./time-series-insights-update-tsm.md).

## <a name="preview-well"></a>Dobře ve verzi Preview

Také zobrazí pole instancí a další metadata přidružená k vybrané instance služby Time Series Insights. Zaškrtnutím políčka na pravé straně můžete skrýt nebo zobrazit konkrétní instance z aktuálního grafu. Můžete také odebrat konkrétní datové prvky z aktuální data také tak, že vyberete červená **odstranit** (koše) ovládacího prvku na levé straně elementu.

  [![Ve verzi Preview a](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

Změna konfigurace rozložení vaší **analyzovat** grafu stránky, vyberte ikonu tří teček v pravém horním rohu:

  [![Možnosti rozložení telemetrie](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> Pokud se zobrazí následující zpráva, instance nemá žádná data během vybrané časové období. Chcete-li vyřešit tento problém, zvýšit časový rozsah nebo potvrďte, že instance je předání dat.
>
> ![Oznámení žádná data](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>Náhled grafu

Graf můžete zobrazit instance služby Time Series Insights jako řádky. Tak, že vyberete ovládací prvky webového zvětšit grafu můžete sbalit panel prostředí, datový model a čas span ovládacích panelech.

  [![Náhled grafu – přehled](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

- **Vybraný rozsah**: Ovládací prvky dat prvky, které jsou k dispozici pro vizualizaci.

- **Vnitřní datum rozsah posuvníku nástroj**: Přetažením přes časový rozsah, který chcete pomocí dvou ovládacích prvků koncový bod.

- **Časové rozpětí sbalení ovládacího prvku**: Sbalí a rozbalí editor span panel čas.

- **Osa y formát ovládacího prvku**: Procházení k dispozici osy y zobrazit možnosti:

    * `Default`: Každý řádek obsahuje jednotlivé osy y.
    * `Stacked`: Použijte ho k osy y data mění, na základě řádku vybrané zásobník více řádků na stejné ose y.
    * `Shared`: Zobrazit dohromady všechna data osy y.

- **Aktuální datový element**: Aktuálně vybraný datový prvek a jeho přidružené podrobnosti.

Když chcete přejít k další do konkrétní datový řez, levým tlačítkem myši na datový bod v aktuální grafu a potom přetáhněte vybrané oblasti do koncového bodu podle vašeho výběru. Klikněte pravým tlačítkem na vybrané šedou oblast a vyberte **přiblížení**, jak je znázorněno na následujícím obrázku:

  [![Lupa grafu ve verzi Preview](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

Po provedení **přiblížení** akce, uvidíte nastavena požadovaná data. Vyberte ovládací prvek osy y formátu k cyklování skrze tři osy y reprezentace dat Time Series Insights.

  [![Osa y grafu ve verzi Preview](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

Tady vidíte příklad sdílené osy Y:

  [![Ve verzi Preview sdílené osy Y](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>Editor panelu čas

Při práci s čas Series Insights ve verzi Preview, nejprve vyberte časový rozsah. Vybraný časový rozsah ovládací prvky sady dat, která je k dispozici pro manipulaci s pomůcky čas Series Insights ve verzi Preview. Následující ovládací prvky webové jsou k dispozici v době Series Insights ve verzi Preview pro výběr vaše pracovní časový rozsah:

  [![Panel výběru času](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. **Vnitřní datum rozsah posuvníku nástroj**: Přetažením přes časový rozsah, který chcete pomocí dvou ovládacích prvků koncový bod. Tento vnitřní rozsah je omezen ovládací prvek posuvník rozsahu vnější data.

1. **Zvýšit a snížit datum rozsahu tlačítka**: Zvýšení nebo snížení časový rozsah tak, že vyberete buď tlačítko pro interval, který chcete.

1. **Časové rozpětí sbalení ovládacího prvku**: Tento webový ovládací prvek umožňuje skrýt ovládací prvky s výjimkou nástroje jezdce vnitřní datum rozsahu.

1. **Ovládací prvek posuvník rozsahu vnější data**: Vyberte vnější rozsah, který bude k dispozici pro ovládací prvek vnitřní datum rozsahu pomocí ovládacích prvků koncový bod.

1. **Rychlé časy rozsah dat rozevíracího seznamu**: Můžete rychle přepínat mezi zadaném časovém rozpětí vybrané možnosti, jako je například poslední **30 minut**, **posledních 12 hodin**, nebo **vlastní rozsah**. Změna této hodnoty se změní také k dispozici interval rozsahy popsané v nástroje jezdce velikost intervalu.

1. **Velikost intervalu posuvník nástroj**: Použijte ho k oddálit intervalech za stejný časový rozsah. Tato akce zajišťuje přesnější kontrolu nad pohyb mezi velké časové úseky. Zobrazí smooth trendy dolů řezy malá jako milisekundy. Můžete ho Pokud chcete zobrazit podrobné, s vysokým rozlišením kusy vaše data. Výchozí posuvníku počáteční bod je nastaven jako optimální zobrazení dat z vašeho výběru, který vyrovnává rozlišení, rychlost dotazů a členitosti.

1. **Datum ovládací prvek webu do a z rozsahu**: Pro tento webový ovládací prvek můžete snadno vybrat datum a čas rozsahy, které chcete. Můžete také použít ovládací prvek přepínání různých časových pásmech. Když provedete změny se můžou použít do aktuálního pracovního prostoru, vyberte **Uložit**.

   [![Do a z panel výběru](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>Navigační panel

Navigační panel čas Series Insights ve verzi Preview se zobrazí v horní části stránky aplikace služby Time Series Insights. Poskytuje následující funkce.

### <a name="current-session-share-link-control"></a>Ovládací prvek odkazu sdílenou složku aktuální relace

  [![Ikona sdílet](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

Vyberte novou **sdílet** ikona adresy URL odkaz sdílejte se svým týmem.

  [![Sdílet adresu URL instance](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>Část týkající se tenanta

  [![Výběr tenanta](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

* Zobrazí vaše aktuální přihlašovací informace Time Series Insights.
* Můžete přepínat mezi dostupné motivy Time Series Insights.
* Slouží k zobrazení náhledu [ukázkovou webovou aplikaci](https://insights.timeseries.azure.com/preview/demo).

### <a name="theme-selection"></a>Výběr motivu

Pokud chcete vybrat nový motiv, vyberte ikonu vašeho profilu v pravém horním rohu. Vyberte **změnit motiv**.

  [![Výběr motivu](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> Výběr jazyka je k dispozici také tak, že vyberete ikonu vašeho profilu.

Azure čas Series Insights ve verzi Preview podporuje dva motivy:

* **Světlý motiv**: Výchozí motiv uvedené v tomto dokumentu.
* **Tmavý motiv**: V Průzkumníku vykreslí, jak je znázorněno zde:

  [![Vybrané tmavý motiv](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>Ovládací prvky prostředí S1/S2

### <a name="preview-terms-panel"></a>Panel podmínek verze Preview

Tato část platí jenom pro stávající prostředí S1, S2, které se pokouší použít v aktualizované uživatelské rozhraní v Průzkumníku. Můžete chtít využívat obecná dostupnost produktu a verze Preview v kombinaci. Přidali jsme některé funkce z existující uživatelského rozhraní do Průzkumníka aktualizované, ale můžete získat plnou funkčnost uživatelského rozhraní pro existující Průzkumníka Time Series Insights S1/S2 prostředí. 

Místo hierarchii zobrazí panel podmínek Time Series Insights, kde definujete dotazy ve vašem prostředí. Můžete filtrovat data podle predikátu.

  [![Panel dotazu](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

Panel editor podmínek čas Series Insights ve verzi Preview má následující parametry:

**Kde**: Použít where – klauzule rychle vyfiltrovat události z operandů sady uvedené v následující tabulce. Pokud spustíte hledání tak, že vyberete operand, predikát automaticky aktualizovat podle hledání. Operand podporované typy patří:

| Operace | Podporované typy   | Poznámky |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, časový interval | |
| `=`, `!=`, `<>` | Řetězec, logická hodnota, Double, DateTime, časový interval, s hodnotou NULL |
| `IN` | Řetězec, logická hodnota, Double, DateTime, časový interval, s hodnotou NULL | Všechny operandy musí být stejného typu nebo konstanta NULL. |
| `HAS` | String | Na pravé straně jsou povoleny pouze konstantní řetězcové literály. Prázdný řetězec a NULL nejsou povoleny. |

Další informace o datové typy a operace podporovaných dotazů najdete v tématu [čas řady výrazu (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx).

### <a name="examples-of-where-clauses"></a>Příklady, kde klauzule

  [![Kde – ukázky použití klauzule](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**Míra**: Rozevírací seznam, který zobrazuje všechny číselné sloupce (**zdvojnásobí**) můžete použít jako elementy pro aktuální graf.

**Rozděleno podle**: Tento rozevírací seznam obsahuje všechny dostupné zařazené do kategorií sloupec (řetězce) v modelu, který můžete seskupit vašich dat prostřednictvím. Můžete přidat až pět podmínky zobrazíte na stejnou osu x. Zadejte parametry a pak vyberte **přidat** přidat novou podmínku.

  [![Dotazované a filtrované zobrazení jedné](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

Můžete zobrazit a skrýt tak, že vyberete ikonu viditelné prvky v panelu graf, jak je znázorněno na následujícím obrázku. Dotazy úplně odebrat, vyberte červený **X**.

  [![Dotaz a filtrované zobrazení dvou](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>Další postup

- Další informace o [úložiště a příchozího přenosu dat](./time-series-insights-update-storage-ingress.md) ve verzi Preview Azure čas Series Insights.
- Přečtěte si dokument čas Series Insights ve verzi Preview na [modelování dat](./time-series-insights-update-tsm.md).
- Přečtěte si [jak Diagnostika a řešení potíží](./time-series-insights-update-how-to-troubleshoot.md) vaší instance služby Time Series Insights.
