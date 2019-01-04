---
title: Vizualizace dat v Průzkumníku Azure čas Series Insights ve verzi Preview | Dokumentace Microsoftu
description: Tento článek popisuje funkce a možnosti, které jsou k dispozici ve webové aplikaci Průzkumník Azure čas Series Insights ve verzi Preview.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: d5c18521f39b1b57b5f94a54bb3131e05abba745
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53556641"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Vizualizace dat v Průzkumníku ve verzi Preview

Tento článek popisuje funkce a možnosti, které jsou k dispozici ve verzi Preview Azure čas Series Insights [explorer webové aplikace](https://insights.timeseries.azure.com/preview/samples).

## <a name="prerequisites"></a>Požadavky

Než použijete Průzkumníka Azure čas Series Insights ve verzi Preview, musíte mít:

* Máte nastavení prostředí Time Series Insights. Další informace najdete v tématu [kurzu: Azure Time Series Insights ve verzi Preview](./time-series-insights-update-create-environment.md).
* Poskytuje přístup k datům prostředí Time Series Insights, který jste vytvořili pro účet. Můžete poskytnout přístup k i další uživatele tak, aby sami.
* Přidání zdroje událostí do prostředí Time Series Insights k odesílání dat do prostředí.

## <a name="learn-about-the-azure-time-series-insights-preview-explorer"></a>Další informace o Průzkumníku Azure čas Series Insights ve verzi Preview

  ![Průzkumník: 1][1]

Průzkumník Azure čas Series Insights ve verzi Preview se skládá z následujících elementů:

* **Navigační panel**: Umožňuje přepínat mezi stránkami analýzy a modelu.
* **Hierarchie stromu**: Umožňuje vybrat konkrétní datové prvky, aby graf.
* **Time series dobře**: Zobrazí aktuálně vybrané datové prvky.
* **Panely grafu**: Zobrazí aktuální pracovní grafu.
* **Časová osa**: Umožňuje upravit vaše pracovní časový rozsah.
* **Panel aplikace**: Obsahuje vaše možností správy uživatelů, jako je například aktuálního tenanta a můžete změnit motiv a jazykové nastavení.

## <a name="time-series-insights-preview-environment-panel"></a>Panel prostředí času Series Insights ve verzi Preview

V panelu prostředí se zobrazí všechna prostředí Time Series Insights, ke kterým máte přístup k. Seznam obsahuje s průběžnými platbami prostředí ve verzi Preview a S1/S2 prostředí (GA). Jednoduše klikněte na prostředí Time Series Insights, který chcete použít.

  ![dvě Explorer][2]

## <a name="time-series-insights-preview-navigation-menu"></a>Čas Series Insights ve verzi Preview navigační nabídku

  ![tři Explorer][3]

V nabídce navigace můžete přepínat mezi aplikacemi Time Series Insights:

* **Analýza**: Umožňuje grafu a provádět bohaté analýzy na dat Modelovaný nebo nemodelované časových řad.

* **Model**: Umožňuje vložit nové typy čas Series Insights ve verzi Preview, hierarchie a instance pro váš model Time Series Insights.

## <a name="time-series-insights-preview-model-authoring"></a>Time Series Insights ve verzi Preview vytváření modelu

V této aplikaci můžete provádět operace vytvoření, čtení, aktualizace a odstranění (CRUD) na váš Model časové řady.  

* **Časové řady Model typu**: Čas Series Insights typy umožňují definující proměnné nebo vzorce pro provádění výpočtů. Jsou přidruženy k dané instance služby Time Series Insights. Typ může mít jednu nebo více proměnných.
* **Časové řady modelu hierarchie**: Hierarchie jsou systematické organizace vaše data. Hierarchie znázornění vztahy mezi různými entitami ve vašich datech Time Series Insights.
* **Instance řady modelu čas**: Instance jsou časové řady sami. Ve většině případů jsou DeviceID nebo AssetID, který je jedinečný identifikátor prostředku v prostředí.

Další informace o modelu časové řady, naleznete v tématu [časy řady modely](./time-series-insights-update-tsm.md).

## <a name="time-series-insights-preview-model-search-panel"></a>Panel hledání modelu času Series Insights ve verzi Preview

Na panelu hledání modelu vám umožní snadno vyhledat a procházet hierarchii modelu časové řady najít instance řady určitou dobu, kterou chcete zobrazit v grafu. Vyberete-li vaše instance, se přidají do aktuální graf a data dobře.

  ![čtyři Explorer][4]

## <a name="time-series-insights-preview-well"></a>Dobře Time Series Insights ve verzi Preview

Také zobrazí pole instancí a další metadata přidružená k vybrané časové řady instancí. Zaškrtávací políčka na pravé straně umožňuje skrýt nebo zobrazit konkrétní instance z aktuálního grafu. Můžete také odebrat konkrétní datové prvky z vašich aktuálních dat také klepnutím na ovládací prvek červený symbol x napravo od elementu.

  ![pět Explorer][5]

Můžete také otevření panelu telemetrická data a získat lepší svislé zobrazení prvků ve vašich datech.

  ![šest Explorer][6]

> [!NOTE]
> Pokud se zobrazí následující zpráva, instance nemá žádná data během vybrané časové období. Chcete-li vyřešit tento problém, můžete zvýšit časový rozsah nebo potvrďte, že instance je předání dat.
>
> ![Průzkumník sedm][7]

## <a name="time-series-insights-preview-chart"></a>Graf doby Series Insights ve verzi Preview

Graf můžete zobrazit instance řady času jako řádky. Klepnutím na ovládací prvky webového zvětšit grafu můžete sbalit panel prostředí, datový model a čas span ovládacích panelech.

  ![Průzkumník osm][8]

1. **Vybraný rozsah**: Ovládací prvky dat prvky, které jsou k dispozici pro vizualizaci.

1. **Vnitřní nástroj pro posuvník rozsahu datum**: Pomocí dvou ovládacích prvků koncový bod jejich přetažením přes požadované časové období.

1. **Časové rozpětí sbalení ovládacího prvku**: Sbalí a rozbalí editor span panel čas.

1. **Osa y formát ovládacího prvku**: Procházení k dispozici možnosti osy y zobrazit:

    * `Default`: Každý řádek obsahuje jednotlivé osy y.
    * `Stacked`: Umožňuje osy y data mění, na základě řádku vybrané zásobník více řádků na stejné ose y.
    * `Shared`: Zobrazit dohromady všechna data osy y.

1. **Aktuální datový element**: Aktuálně vybraný datový prvek a jeho přidružené podrobnosti.

Konkrétní datový řez můžete projít další klepněte levým tlačítkem myši na datový bod v aktuální grafu a pak přetažením vybrané oblasti do koncového bodu podle vašeho výběru. Klikněte pravým tlačítkem na šedé, vybrané oblasti a klikněte na tlačítko lupy, jak je znázorněno na následujícím obrázku:

  ![Průzkumník devět][9]

Po provedení akce přiblížení, zobrazí se vybrané datové sadě. Klikněte na ovládací prvek osy y formátu k cyklování skrze tři osy y reprezentace dat Time Series Insights.

  ![deset Explorer][10]

Tady vidíte příklad sdílené osami y:

  ![Průzkumník jedenáct][11]

## <a name="time-series-insights-preview-time-editor-panel"></a>Panel editor čas času Series Insights ve verzi Preview

Při práci s čas Series Insights ve verzi Preview, nejprve vyberte časový rozsah. Vybraný časový interval Určuje datovou sadu, která je k dispozici pro manipulaci s pomůcky čas Series Insights ve verzi Preview. Následující ovládací prvky webové jsou k dispozici v době Series Insights ve verzi Preview pro výběr vaše pracovní časový rozsah.

  ![Průzkumník 12][12]

1. **Vnitřní rozsah posuvníku nástroj**: Pomocí dvou ovládacích prvků koncový bod jejich přetažením přes požadované časové období. Tento vnitřní rozsah je omezen v ovládacím prvku posuvník vnější rozsah.

1. **Zvýšit a snížit datum rozsahu tlačítka**: Zvýšení nebo snížení časový rozsah tak, že vyberete buď tlačítko pro interval, který chcete.

1. **Časové rozpětí sbalení ovládacího prvku**: Tento webový ovládací prvek umožňuje skrýt ovládací prvky s výjimkou nástroje jezdce vnitřní rozsah.

1. **Ovládací prvek posuvník vnější rozsah**: Vyberte rozsah vnější dat, která bude k dispozici pro ovládací prvek vnitřní rozsah pomocí ovládacích prvků koncový bod.

1. **Rychlé časy datum rozsahu rozevírací**: Umožňuje rychle přepínat mezi zadaném časovém rozpětí vybrané možnosti, jako je například posledních 30 minut, posledních 12 hodin nebo vlastní rozsah. Změna této hodnoty se změní také k dispozici interval rozsahy popsané v nástroje jezdce velikost intervalu.

1. **Velikost intervalu posuvník nástroj**: Umožňuje přiblížit nebo intervaly oddálit za stejný časový rozsah. Tato akce zajišťuje přesnější kontrolu nad pohyb mezi velké časové úseky. Zobrazí smooth trendy dolů řezy malá jako milisekund, což umožní zobrazit podrobné, s vysokým rozlišením kusy vaše data. Výchozí posuvníku počáteční bod je nastaven jako optimální zobrazení dat z vašeho výběru, který vyrovnává rozlišení, rychlost dotazů a členitosti.

1. **Z webový ovládací prvek a rozsah kalendářních dat**: Pro tento webový ovládací prvek můžete jednoduše klikněte a vyberte požadované datum a časové rozsahy. Můžete také použít ovládací prvek přepínání různých časových pásmech. Po provedení změn, chcete-li použít do aktuálního pracovního prostoru, vyberte **Uložit**.

  ![třináct Explorer][13]

## <a name="time-series-insights-preview-navigation-panel"></a>Čas Series Insights ve verzi Preview navigační panel

Navigační panel čas Series Insights ve verzi Preview poskytuje následující funkce:

  ![Průzkumník čtrnáct][14]

### <a name="current-session-share-link-control"></a>Ovládací prvek odkazu sdílenou složku aktuální relace

  ![Průzkumník patnáct][15]

Vyberte web ovládací prvek odkazu (zvýrazněno) ke generování adresy URL uložit nebo sdílet aktuálního sezení Azure Time Series Insights, která zahrnuje:

* Aktuálně vybraný časový rozsah
* Velikost aktuálně vybraného intervalu
* Aktuálně vybraná data a

### <a name="tenant-section"></a>Část týkající se tenanta

  ![Průzkumník šestnáct][16]

* Zobrazí aktuální informace o účtu služby Time Series Insights přihlášení.
* Umožňuje přepínat mezi dostupné motivy Time Series Insights.

### <a name="theme-selection"></a>Výběr motivu

Azure čas Series Insights ve verzi Preview podporuje dva motivy:

* **Světlý motiv**: Výchozí motiv uvedené v tomto dokumentu.
* **Tmavý motiv**:  V Průzkumníku vykreslí, jak je znázorněno zde:

  ![Průzkumník sedmnáct][17]

Tady můžete také změnit mezi podporované jazyky.

## <a name="s1s2-environment-controls"></a>Ovládací prvky prostředí S1/S2

### <a name="time-series-insights-preview-terms-panel"></a>Panel podmínek čas Series Insights ve verzi Preview

Tato část platí jenom pro stávající prostředí S1, S2, které se pokouší použít v aktualizované uživatelské rozhraní v Průzkumníku. Můžete chtít použít v kombinaci GA produkt a ve verzi Preview. Přidali jsme některé funkce z existující uživatelského rozhraní do Průzkumníka aktualizované, ale můžete získat plnou funkčnost uživatelského rozhraní pro existující Průzkumníka Time Series Insights S1/S2 prostředí.  

Namísto hierarchii zobrazí se panel podmínek Time Series Insights, kde definujete dotazy ve vašem prostředí. Umožňuje filtrovat data podle predikátu.

  ![Průzkumník osmnáct][18]

Panel editor podmínek čas Series Insights ve verzi Preview má následující parametry:

**Kde**: Where – klauzule vám umožní rychle filtrovat události z operandů sady uvedené v následující tabulce. Pokud spustíte hledání tak, že vyberete operand, predikát automaticky aktualizovat podle hledání. Operand podporované typy patří:

| Operace | Podporované typy   | Poznámky |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, časový interval | |
| `=`, `!=`, `<>` | Řetězec, logická hodnota, Double, DateTime, časový interval, s hodnotou NULL |
| `IN` | Řetězec, logická hodnota, Double, DateTime, časový interval, s hodnotou NULL | Všechny operandy musí být stejného typu nebo konstanta NULL. |
| `HAS` | Řetězec | Na pravé straně jsou povoleny pouze konstantní řetězcové literály. Prázdný řetězec a NULL nejsou povoleny. |

Další informace o podporovaných dotazu operace a datové typy najdete [čas řady výrazu (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx).

### <a name="examples-of-where-clauses"></a>Příklady, kde klauzule

  ![Průzkumník devatenáct][19]

**Míra**: Tento rozevírací seznam se zobrazí všechny číselné sloupce (**zdvojnásobí**), který slouží jako elementy pro aktuální graf.

**Rozděleno podle**: Tento rozevírací seznam se zobrazí všechny dostupné zařazené do kategorií sloupce (řetězce) v modelu, který můžete seskupit vašich dat prostřednictvím. Můžete přidat až pět podmínky zobrazíte na stejnou osu x. Zadejte požadované parametry a potom vyberte **přidat** přidat novou podmínku.

  ![dvacet Explorer][20]

Můžete zobrazit a skrýt prvky v panelu graf tak, že vyberete ikonu viditelné, jak je znázorněno na následujícím obrázku. Dotazy můžete zcela odebrat kliknutím na červenou **x**.

  ![Průzkumník: dvacet 1][21]

## <a name="next-steps"></a>Další postup

Viz následující články:
* [Azure storage čas Series Insights ve verzi Preview a příchozího přenosu dat](./time-series-insights-update-storage-ingress.md)
* [Modelování dat](./time-series-insights-update-tsm.md)
* [Diagnostika a řešení potíží](./time-series-insights-update-how-to-troubleshoot.md)

<!-- Images -->
[1]: media/v2-update-explorer/explorer-one.png
[2]: media/v2-update-explorer/explorer-two.png
[3]: media/v2-update-explorer/explorer-three.png
[4]: media/v2-update-explorer/explorer-four.png
[5]: media/v2-update-explorer/explorer-five.png
[6]: media/v2-update-explorer/explorer-six.png
[7]: media/v2-update-explorer/explorer-seven.png
[8]: media/v2-update-explorer/explorer-eight.png
[9]: media/v2-update-explorer/explorer-nine.png
[10]: media/v2-update-explorer/explorer-ten.png
[11]: media/v2-update-explorer/explorer-eleven.png
[12]: media/v2-update-explorer/explorer-twelve.png
[13]: media/v2-update-explorer/explorer-thirteen.png
[14]: media/v2-update-explorer/explorer-fourteen.png
[15]: media/v2-update-explorer/explorer-fifteen.png
[16]: media/v2-update-explorer/explorer-sixteen.png
[17]: media/v2-update-explorer/explorer-seventeen.png
[18]: media/v2-update-explorer/explorer-eighteen.png
[19]: media/v2-update-explorer/explorer-nineteen.png
[20]: media/v2-update-explorer/explorer-twenty.png
[21]: media/v2-update-explorer/explorer-twenty-one.png
