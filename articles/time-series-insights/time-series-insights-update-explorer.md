---
title: Vizualizace dat v Průzkumníku aktualizaci | Dokumentace Microsoftu
description: Aktualizovat Průzkumníka služby Azure Time Series Insights
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/28/2018
ms.openlocfilehash: bf091eec271e3fb27f6ab312ff5d0096efa7f90c
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52855769"
---
# <a name="visualize-data-in-the-explorer-update"></a>Vizualizace dat v Průzkumníku aktualizace

Tento článek popisuje funkce a možnosti dostupné v rámci The Azure Time Series Insights (preview) Průzkumníka webové aplikace.

## <a name="prerequisites"></a>Požadavky

Než použijete Azure Time Series Insights (preview) Explorer, musíte mít:

* Máte zřízené prostředí Time Series Insights. Další informace o zřizování prostředí Time Series Insights.
* Poskytuje přístup k datům prostředí Time Series Insights, který jste vytvořili pro účet. Přístup mohou být poskytnuty jiné také sami.
* Přidání zdroje událostí do prostředí Time Series Insights k odesílání dat do prostředí.

## <a name="learn-about-the-azure-time-series-insights-preview-explorer"></a>Seznamte se s Azure Time Series Insights (preview) Explorer

  ![Průzkumník: 1][1]

Azure Time Series Insights (preview) Explorer je rozdělený do následujících sedm prvků:

1. Navigační panel TSI (preview) umožňuje přepínat mezi stránkami analýzy a modelu.
1. Stromové hierarchie TSI (preview) vyberte konkrétní datové prvky, aby graf.
1. Časové řady TSI (preview) a zobrazuje všechny aktuálně vybrané datové prvky.
1. TSI (preview) grafu panel zobrazuje aktuální pracovní grafu.
1. Časová osa TSI (preview) umožňuje změnit vaše pracovní časový rozsah.
1. Panel aplikace TSI (preview) obsahuje vaše možnosti správy uživatele (například aktuálního tenanta) a umožňuje změnit motiv a jazykové nastavení.

## <a name="tsi-preview-environment-panel"></a>Panel prostředí TSI (preview)

V panelu prostředí se zobrazí všechna prostředí TSI, ke kterým máte přístup k. To zahrnuje seznam s průběžnými platbami prostředí (preview) a také prostředí S1/S2 (GA). Stačí klikněte TSI prostředí, ve kterém chcete použít.

  ![dvě Explorer][2]

## <a name="time-series-insights-preview-navigation-menu"></a>Time Series Insights (preview) navigační nabídku

  ![tři Explorer][3]

V navigační nabídce umožňuje přepínat mezi těmito aplikacemi TSI:

* Analýza – umožňuje grafu a provádět bohaté analýzy na dat Modelovaný nebo nemodelované časových řad.

* Model - můžete nainstalovat nové aktualizace typy TSI, hierarchie a instance do modelu TSI.

## <a name="time-series-insights-update-model-authoring"></a>Time Series Insights aktualizovat Model pro vytváření

Tato aplikace vám dává možnost k provádění operací CRUD u vašeho modelu časové řady.  

* Typ TSM – TSI typy umožňují definující proměnné nebo vzorce pro provádění výpočtů, jsou přidruženy k dané instanci služby TSI. Typ může mít jednu nebo více proměnných.
* Hierarchie TSM – hierarchie jsou systematické organizace vaše data. Hierarchie znázornění vztahy mezi různými entitami ve vašich datech TSI.
* Instance TSM – instance jsou časové řady sami. Ve většině případů to bude **DeviceID** nebo **AssetID**, který je jedinečný identifikátor prostředku v prostředí.

Další informace o TSM, [časy řady modely](./time-series-insights-update-tsm.md).

## <a name="time-series-insights-preview-model-search-panel"></a>Time Series Insights (preview) modelu Panel hledání

Na panelu hledání model umožňuje snadno vyhledat a procházet hierarchii TSM najít instance řady určitou dobu, kterou chcete zobrazit v grafu. Při výběru vaše instance nejsou přidány pouze pro aktuální graf, ale také přidají k datům dobře.

  ![čtyři Explorer][4]

## <a name="time-series-insights-preview-well"></a>Time Series Insights (preview) a

Také zobrazí pole instancí a další metadata přidružená k vybrané časové řady instancí. Zaškrtávací políčka na pravé straně umožňují skrytí nebo zobrazení konkrétních instancí z aktuálního grafu. Můžete také odebrat konkrétní datové prvky z vašich aktuálních dat také klepnutím na ovládací prvek červený symbol x napravo od elementu.

  ![pět Explorer][5]

Můžete také otevření panelu telemetrická data a získat lepší svislé zobrazení prvků ve vašich datech.

  ![šest Explorer][6]

Poznámka: Pokud se zobrazí následující ikona, instance nemá žádná data během vybraný časový interval.  Pokud chcete vyřešit, můžete zvýšit časový rozsah vybraný nebo potvrďte, že instance je předání dat.

  ![Průzkumník sedm][7]

## <a name="time-series-insights-preview-chart"></a>Time Series Insights (preview) grafu

Graf umožňuje zobrazit instance řady času jako řádky. Klepnutím na ovládací prvky webového zvětšit grafu můžete sbalit panel prostředí, datový model a čas span ovládacích panelech.

  ![Průzkumník osm][8]

1. Vybraný rozsah dat – aktuálně ve vybraném rozsahu dat pro panel grafu, tento ovládací prvky, datové prvky, které bude k dispozici pro vizualizaci.

1. Vnitřní nástroj posuvníku rozsahu datum - span pomocí dvou ovládacích prvků koncový bod kliknutím a přetažením přes který je požadovaný čas.

1. Ovládací prvek v době span Sbalit - tento ovládací prvek sbalí a rozbalí editor span panel čas.

1. Osa y formát ovládacího prvku – klikněte na tento ovládací prvek cyklicky procházet dostupné možnosti zobrazení osy y. Jsou k dispozici možnosti osy y zobrazit:

    * Výchozí – každý řádek obsahuje jednotlivé osy y
    * Skládaný – to vám umožní zásobník více řádků na ose y stejné osy y data mění, založené na vybraný řádek
    * Sdílené – společně zobrazit všechna data osy y

1. Aktuální dat elementu – aktuálně vybraný datový prvek a jeho přidružené podrobnosti.

Konkrétní datový řez můžete projít další podle klepněte levým tlačítkem myši na datový bod v aktuální grafu při podržíte ukazatel myši a pak přetažením vybrané oblasti do koncového bodu podle vašeho výběru. Klikněte pravým tlačítkem na šedé, vybrané oblasti a jak je znázorněno níže, klikněte na tlačítko lupy. Můžete také:

  ![Průzkumník devět][9]

Po provedení akce přiblížení, uvidíte teď vybrané datové sady. Klikněte na formát ovládacího prvku osy y k cyklování skrze tři odlišné osy y reprezentace dat TSI.

  ![deset Explorer][10]

Tady vidíte příklad sdílené osami y.

  ![Průzkumník jedenáct][11]

## <a name="time-series-insights-preview-time-editor-panel"></a>Time Series Insights (preview) Panel čas Editor

Při práci s TSI nejprve vyberete časové období. Vybrané časové období bude řídit sady dat, která je k dispozici pro manipulaci s aktualizace widgetů TSI. Následující ovládací prvky webové jsou k dispozici v aktualizaci služby TSI pro výběr vaše pracovní časový rozsah.

  ![Průzkumník 12][12]

1. **Vnitřní rozsah posuvníku nástroj** – pomocí dvou ovládacích prvků koncový bod kliknutím a přetažením přes který je požadovaný čas span. Tato "vnitřní" období bude ovlivněna posuvník "Vnější datum" rozsahu uvedené níže.

1. Zvýšení a snížení datum rozsahu tlačítka ** – zvýšení nebo snížení časové období kliknutím na tlačítko buď tlačítko pro interval, který chcete.

1. **Časové rozpětí sbalení ovládacího prvku** -tento webový ovládací prvek umožňuje skrýt ovládací prvky s výjimkou nástroje jezdce vnitřní datum rozsahu.

1. **Ovládací prvek posuvník vnější rozsah** – použití koncového bodu ovládacích prvků vyberte vnější rozsah, který bude k dispozici pro ovládací prvek rozsah "Vnitřní datum".

1. **Rychlé časy rozsah rozevírací seznam** -vám dává možnost rychle přepínat mezi zadaném časovém rozpětí výběry například posledních 30 minut, posledních 12 hodin, vlastní rozsah, atd. Změna této hodnoty se změní také k dispozici interval rozsahy věnujeme se jí v nástroje jezdce velikost intervalu.

1. **Velikost intervalu posuvník nástroj** -nástroje jezdce velikost intervalu umožňuje přiblížit nebo intervaly oddálit za stejný časový rozsah. To zajišťuje přesnější kontrolu nad pohyb mezi velké časové úseky, které ukazují smooth trendy dolů řezy malá jako na milisekundy, což umožní zobrazit podrobné, s vysokým rozlišením kusy vaše data. Výchozí posuvníku počáteční bod je nastaven jako optimální zobrazení dat z výběru. Vyrovnávání rozlišení, rychlost dotazů a členitosti.

1. **Z webový ovládací prvek a rozsah kalendářních dat** – se tento webový ovládací prvek, jednoduše klikněte a vyberte požadované datum a časové rozsahy. Můžete také použít ovládací prvek přepínání různých časových pásmech. Po provedení změn, které chcete použít pro aktuální pracovní prostor. klikněte na toto tlačítko.

  ![třináct Explorer][13]

## <a name="time-series-insights-preview-navigation-panel"></a>Navigační panel čas Series Insights (preview)

Navigační panel aktualizace TSI poskytuje následující funkce:

  ![Průzkumník čtrnáct][14]

### <a name="current-session-share-link-control"></a>Ovládací prvek odkazu sdílenou složku aktuální relace

  ![Průzkumník patnáct][15]

Klikněte na ovládací prvek webového odkazu v kroužku ke generování adresy URL uložit nebo sdílet vaše aktuální Time Series Insights pracovní relaci, která zahrnuje:

* Aktuálně vybraný časový rozsah
* Velikost aktuálně vybraného intervalu
* Aktuálně vybraná data a

### <a name="tenant-section"></a>Část týkající se tenanta

  ![Průzkumník šestnáct][16]

* Zobrazí aktuální informace o účtu služby TSI přihlášení
* Umožňuje že přepínat mezi dostupné motivy TSI.

### <a name="theme-selection"></a>Výběr motivu

TSI Azure (preview) podporuje dva motivy:

* **Světlý motiv**: Toto je výchozí motiv uvedené v tomto dokumentu.
* **Tmavý motiv**: tuto možnost vykreslí Průzkumníka do články v tmavém motivu, jak je znázorněno níže:

  ![Průzkumník sedmnáct][17]

Tady můžete také změnit mezi podporované jazyky.

## <a name="s1s2-environment-controls"></a>Ovládací prvky prostředí S1/S2

### <a name="time-series-insights-preview-terms-panel"></a>Time Series Insights (preview) Panel podmínek

Tato část se týká pouze do existujícího prostředí S1/S2 pokus o použití Průzkumníka v aktualizované uživatelské rozhraní. Můžete k tomu použijte produkt GA a aktualizovat (preview) ve spojení s jinými. Jsme přidali některé funkce z existující uživatelského rozhraní na aktualizované explorer, ale víte, že můžete vždy získat úplné uživatelské rozhraní pro S1/S2 prostředí v existující Průzkumníku TSI.  

Namísto hierarchii zobrazí se panel podmínek TSI. Toto je tady můžete definovat dotazy ve vašem prostředí. Nabízí možnost filtrovat data na základě pomocí predikátu.

  ![Průzkumník osmnáct][18]

TSI (preview) Panel Editor podmínek mají následující parametry

**Kde**: where klauzule umožňuje rychle filtrovat události pomocí sady operandy uvedených níže. Pokud spustíte hledání kliknutím vyberete /, predikátu se automaticky aktualizují na základě danému hledání. Operand podporované typy patří:

| Operace | Podporované typy   | Poznámky |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` |    Double, DateTime, časový interval  | |
| `=`, `!=`, `<>`   | Řetězec, logická hodnota, Double, DateTime, časový interval, s hodnotou NULL    |
| `IN` |    Řetězec, logická hodnota, Double, DateTime, časový interval, s hodnotou NULL |    Všechny operandy musí být stejného typu nebo konstanta NULL. |
| `HAS` |   Řetězec |    Na pravé straně jsou povoleny pouze konstantní řetězcové literály. Prázdný řetězec a NULL nejsou povoleny. |

### <a name="examples-of-where-clauses"></a>Příklady, kde klauzule

  ![Průzkumník devatenáct][19]

**Míra**: Tento rozevírací seznam ukazuje všechny číselné sloupce (**zdvojnásobí**), který slouží jako elementy pro aktuální graf.

**Rozděleno podle**: Tento rozevírací seznam zobrazí všechny sloupce zařazené do kategorií (řetězec) v modelu k dispozici, můžete použít k seskupení vašich dat prostřednictvím. Můžete přidat až pět podmínky zobrazíte na stejnou osu x. Zadejte požadované parametry a pak použít **přidat** tlačítko Přidat novou podmínku.

  ![dvacet Explorer][20]

Můžete skrýt a zobrazit prvky z panelu graf kliknutím na ikonu viditelné, jak je znázorněno níže. Dotazy můžete zcela odebrat kliknutím na červenou `X` vidíte níže.

  ![Průzkumník: dvacet 1][21]

## <a name="next-steps"></a>Další postup

Přečtěte si [Azure TSI (preview) úložiště a příchozího přenosu dat](./time-series-insights-update-storage-ingress.md).

Přečtěte si informace o novém [čas řady modely](./time-series-insights-update-tsm.md).

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