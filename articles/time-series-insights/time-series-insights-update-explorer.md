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
ms.date: 05/03/2019
ms.custom: seodec18
ms.openlocfilehash: 862465a7611f1a2bc65dbb0c49c4de512bd239de
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442097"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Vizualizace dat v Průzkumníku ve verzi Preview

Tento dokument popisuje uživatelské rozhraní a prostředí funkce a rozhraní Azure čas Series Insights Preview [ukázkovou webovou aplikaci](https://insights.timeseries.azure.com/preview/demo). Konkrétně popisuje rozložení ukázkové prostředí, možnosti vlastního nastavení rozhraní a navigace prostřednictvím zadaná ukázek.

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít s Průzkumníkem Azure čas Series Insights ve verzi Preview, musíte mít:

* Máte nastavení prostředí Time Series Insights. Další informace o zřízení instance Vyzkoušejte naše [Azure čas Series Insights ve verzi Preview](./time-series-insights-update-create-environment.md) kurzu.
* [Poskytuje přístup k datům](./time-series-insights-data-access.md) do prostředí Time Series Insights, který jste vytvořili pro účet. Můžete poskytnout přístup k i další uživatele tak, aby sami.
* Přidání zdroje událostí do prostředí Time Series Insights k odesílání dat do prostředí:
  * Přečtěte si [jak se připojit k Centru událostí](./time-series-insights-how-to-add-an-event-source-eventhub.md)
  * Přečtěte si [postup připojení do služby IoT hub](./time-series-insights-how-to-add-an-event-source-iothub.md)

## <a name="learn-about-the-preview-explorer"></a>Další informace o Průzkumníku ve verzi Preview

Průzkumník Azure čas Series Insights ve verzi Preview se skládá z následujících elementů:

[![Zobrazení Průzkumníka](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

1. <a href="#environment-dropdown">**Panel prostředí**</a>: Zobrazí prostředí Azure TSI.
1. <a href="#navigation-menu">**Navigační nabídka**</a>: Umožňuje vám přepínat mezi **analyzovat** a **modelu** stránky.
1. <a href="#hierarchy-tree">**Hierarchie stromu**</a>: Umožňuje vybrat konkrétní modelem a prvky data pro graf.
1. <a href="#preview-well">**Time series dobře**</a>: Zobrazí aktuálně vybrané datové prvky ve formátu tabulky se zdají nezvyklé.
1. <a href="#preview-chart">**Panely grafu**</a>:  Zobrazí aktuální pracovní grafu.
1. <a href="#time-editor-panel">**Časová osa**</a>:  Umožňuje upravit vaše pracovní časový rozsah.
1. <a href="#navigation-panel">**Panel aplikace**</a>:  Obsahuje vaše možností správy uživatelů, jako je například aktuálního tenanta a můžete změnit motiv a jazykové nastavení.

## <a name="environment-dropdown"></a>Rozevírací seznam prostředí

Rozevírací seznam prostředí se zobrazí všechna prostředí Time Series Insights, ke kterým máte přístup. Seznam obsahuje s průběžnými platbami prostředí (Preview) a prostředí S1/S2 (obecná dostupnost nebo GA). 

1. Jednoduše klikněte na šipku rozevíracího seznamu vedle zobrazeného prostředí:

   [![Ovládací prvek panel](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. Vyberte požadované prostředí.

## <a name="navigation-menu"></a>Navigační nabídka

  [![V navigační nabídce](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

V navigační nabídce můžete vybrat mezi dvě zobrazení:

* **Analýza**: Umožňuje grafu a provádět bohaté analýzy na dat Modelovaný nebo nemodelované časových řad.
* **Model**: Umožňuje vložit nové typy čas Series Insights ve verzi Preview, hierarchie a instance pro váš model Time Series Insights.

## <a name="hierarchy-tree"></a>Hierarchie stromu

Strom hierarchie dál zobrazuje vybrané datové prvky, včetně modelů, konkrétní zařízení a senzorů v zařízeních.

### <a name="model-search-panel"></a>Panel hledání modelu

Na panelu hledání modelu vám umožní snadno vyhledat a procházet hierarchii modelu časové řady najít instance řady určitou dobu, kterou chcete zobrazit v grafu. Vyberete-li vaše instance, se přidají do aktuální graf a data dobře.

  [![Na panelu hledání modelu](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>Vytváření modelu

Azure čas Series Insights ve verzi Preview podporuje úplné vytvoření, čtení, aktualizace a odstranění (CRUD) operací na váš Model časové řady.  

* **Časové řady Model typu**: Čas Series Insights typy umožňují definující proměnné nebo vzorce pro provádění výpočtů. Jsou přidruženy k dané instance služby Time Series Insights. Typ může mít jednu nebo více proměnných.
* **Časové řady modelu hierarchie**: Hierarchie jsou systematické organizace vaše data. Hierarchie znázornění vztahy mezi různými entitami ve vašich datech Time Series Insights.
* **Instance řady modelu čas**: Instance jsou časové řady sami. Ve většině případů jsou **DeviceID** nebo **AssetID**, který je jedinečný identifikátor prostředku v prostředí.

Další informace o modelu časové řady, naleznete v tématu [časy řady modely](./time-series-insights-update-tsm.md).

## <a name="preview-well"></a>Dobře ve verzi Preview

Také zobrazí pole instancí a další metadata přidružená k vybrané instance TSI. Zaškrtávací políčka na pravé straně umožňuje skrýt nebo zobrazit konkrétní instance z aktuálního grafu. Můžete také odebrat konkrétní datové prvky z vašich aktuálních dat a kliknutím na červenou **odstranit** (koše) ovládacího prvku v levé části elementu.

  [![Ve verzi Preview a](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

Můžete také změnit konfiguraci rozložení vaší **analyzovat** stránky grafu tak, že vyberete ikonu tří teček v pravém horním:

  [![Možnosti rozložení telemetrie](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> Pokud se zobrazí následující zpráva, instance nemá žádná data během vybrané časové období. Chcete-li vyřešit tento problém, můžete zvýšit časový rozsah nebo potvrďte, že instance je předání dat.
>
> ![Oznámení žádná data](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>Náhled grafu

Graf můžete zobrazit instance TSI jako řádky. Klepnutím na ovládací prvky webového zvětšit grafu můžete sbalit panel prostředí, datový model a čas span ovládacích panelech.

  [![Náhled grafu – přehled](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

1. **Vybraný rozsah**: Ovládací prvky dat prvky, které jsou k dispozici pro vizualizaci.

1. **Vnitřní nástroj pro posuvník rozsahu datum**: Pomocí dvou ovládacích prvků koncový bod jejich přetažením přes požadované časové období.

1. **Časové rozpětí sbalení ovládacího prvku**: Sbalí a rozbalí editor span panel čas.

1. **Osa y formát ovládacího prvku**: Procházení k dispozici možnosti osy y zobrazit:

    * `Default`: Každý řádek obsahuje jednotlivé osy y.
    * `Stacked`: Umožňuje osy y data mění, na základě řádku vybrané zásobník více řádků na stejné ose y.
    * `Shared`: Zobrazit dohromady všechna data osy y.

1. **Aktuální datový element**: Aktuálně vybraný datový prvek a jeho přidružené podrobnosti.

Konkrétní datový řez můžete projít další klepněte levým tlačítkem myši na datový bod v aktuální grafu a pak přetažením vybrané oblasti do koncového bodu podle vašeho výběru. Klikněte pravým tlačítkem na šedé, vybrané oblasti a klikněte na tlačítko **přiblížení** jak je znázorněno na následujícím obrázku:

  [![Lupa grafu ve verzi Preview](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

Po provedení **přiblížení** akce, zobrazí se vybrané datové sadě. Klikněte na ovládací prvek osy y formátu k cyklování skrze tři osy y reprezentace dat Time Series Insights.

  [![Náhled grafu osy y](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

Tady vidíte příklad sdílené osami y:

  [![Ve verzi Preview sdílené osy y](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>Editor panelu čas

Při práci s čas Series Insights ve verzi Preview, nejprve vyberte časový rozsah. Vybraný časový interval Určuje datovou sadu, která je k dispozici pro manipulaci s pomůcky čas Series Insights ve verzi Preview. Následující ovládací prvky webové jsou k dispozici v době Series Insights ve verzi Preview pro výběr vaše pracovní časový rozsah.

  [![Panel výběru času](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. **Vnitřní rozsah posuvníku nástroj**: Pomocí dvou ovládacích prvků koncový bod jejich přetažením přes požadované časové období. Tento vnitřní rozsah je omezen v ovládacím prvku posuvník vnější rozsah.

1. **Zvýšit a snížit datum rozsahu tlačítka**: Zvýšení nebo snížení časový rozsah tak, že vyberete buď tlačítko pro interval, který chcete.

1. **Časové rozpětí sbalení ovládacího prvku**: Tento webový ovládací prvek umožňuje skrýt ovládací prvky s výjimkou nástroje jezdce vnitřní rozsah.

1. **Ovládací prvek posuvník vnější rozsah**: Vyberte rozsah vnější dat, která bude k dispozici pro ovládací prvek vnitřní rozsah pomocí ovládacích prvků koncový bod.

1. **Rychlé časy rozsah dat rozevíracího seznamu**: Umožňuje rychle přepínat mezi zadaném časovém rozpětí vybrané možnosti, jako je například poslední **30 minut**, **posledních 12 hodin**, nebo **vlastní rozsah**. Změna této hodnoty se změní také k dispozici interval rozsahy popsané v nástroje jezdce velikost intervalu.

1. **Velikost intervalu posuvník nástroj**: Umožňuje přiblížit nebo intervaly oddálit za stejný časový rozsah. Tato akce zajišťuje přesnější kontrolu nad pohyb mezi velké časové úseky. Zobrazí smooth trendy dolů řezy malá jako milisekund, což umožní zobrazit podrobné, s vysokým rozlišením kusy vaše data. Výchozí posuvníku počáteční bod je nastaven jako optimální zobrazení dat z vašeho výběru, který vyrovnává rozlišení, rychlost dotazů a členitosti.

1. **Z webový ovládací prvek a rozsah kalendářních dat**: Pro tento webový ovládací prvek lze snadno klikněte a vyberte požadované datum a časové rozsahy. Můžete také použít ovládací prvek přepínání různých časových pásmech. Po provedení změn, chcete-li použít do aktuálního pracovního prostoru, vyberte **Uložit**.

   [![Do a z panel výběru](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>Navigační panel

Navigační panel čas Series Insights ve verzi Preview se zobrazí v horní části stránky aplikace služby TSI. Poskytuje následující funkce.

### <a name="current-session-share-link-control"></a>Ovládací prvek odkazu sdílenou složku aktuální relace

  [![Ikona sdílet](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

Vyberte novou **sdílet** ikona adresy URL odkaz sdílejte se svým týmem.

  [![Sdílet adresu URL instance](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>Část týkající se tenanta

  [![Výběr tenanta](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

* Zobrazí aktuální informace o účtu služby Time Series Insights přihlášení.
* Umožňuje přepínat mezi dostupné motivy Time Series Insights.
* Umožňuje zobrazit náhled [ukázkovou webovou aplikaci](https://insights.timeseries.azure.com/preview/demo).

### <a name="theme-selection"></a>Výběr motivu

Pokud chcete vybrat nový motiv, klikněte na ikonu váš profil nachází v pravém horním rohu. Vyberte **změnit motiv**.

  [![Výběr motivu](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> Výběr jazyka je k dispozici také kliknutím na ikonu vašeho profilu.

Azure čas Series Insights ve verzi Preview podporuje dva motivy:

* **Světlý motiv**: Výchozí motiv uvedené v tomto dokumentu.
* **Tmavý motiv**:  V Průzkumníku vykreslí, jak je znázorněno zde:

  [![Vybrané tmavý motiv](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>Ovládací prvky prostředí S1/S2

### <a name="preview-terms-panel"></a>Panel podmínek verze Preview

Tato část platí jenom pro stávající prostředí S1, S2, které se pokouší použít v aktualizované uživatelské rozhraní v Průzkumníku. Můžete chtít použít v kombinaci GA produkt a ve verzi Preview. Přidali jsme některé funkce z existující uživatelského rozhraní do Průzkumníka aktualizované, ale můžete získat plnou funkčnost uživatelského rozhraní pro existující Průzkumníka Time Series Insights S1/S2 prostředí.  

Namísto hierarchii zobrazí se panel podmínek Time Series Insights, kde definujete dotazy ve vašem prostředí. Umožňuje filtrovat data podle predikátu.

  [![Panel dotazu](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

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

  [![Kde – ukázky použití klauzule](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**Míra**: Rozevírací seznam, který zobrazuje všechny číselné sloupce (**zdvojnásobí**) můžete použít jako elementy pro aktuální graf.

**Rozděleno podle**: Tento rozevírací seznam se zobrazí všechny dostupné zařazené do kategorií sloupce (řetězce) v modelu, který můžete seskupit vašich dat prostřednictvím. Můžete přidat až pět podmínky zobrazíte na stejnou osu x. Zadejte požadované parametry a potom vyberte **přidat** přidat novou podmínku.

  [![Dotazované a filtrované zobrazení jedné](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

Můžete zobrazit a skrýt prvky v panelu graf tak, že vyberete ikonu viditelné, jak je znázorněno na následujícím obrázku. Dotazy můžete zcela odebrat kliknutím na červenou **X**.

  [![Dotaz a filtrované zobrazení dvou](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>Další postup

- Další informace o [úložiště a příchozího přenosu dat](./time-series-insights-update-storage-ingress.md) ve verzi Preview Azure čas Series Insights.

- Přečtěte si dokument čas Series Insights ve verzi Preview na [modelování dat](./time-series-insights-update-tsm.md).

- Přečtěte si [jak Diagnostika a řešení potíží](./time-series-insights-update-how-to-troubleshoot.md) vaší instance služby Time Series Insights.
