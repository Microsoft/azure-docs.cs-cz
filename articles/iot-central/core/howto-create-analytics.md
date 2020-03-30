---
title: Analýza dat zařízení v aplikaci Azure IoT Central | Dokumenty společnosti Microsoft
description: Analyzujte data zařízení v aplikaci Azure IoT Central.
author: ankitscribbles
ms.author: ankitgup
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 40460b58ede0ca0da8fe25475906bdbe41bfffe0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158278"
---
# <a name="how-to-use-analytics-to-analyze-device-data"></a>Jak používat analýzy k analýze dat zařízení

*Tento článek je pro operátory, tvůrce a správce.*



Azure IoT Central poskytuje bohaté analytické funkce pro analýzu historických trendů a korelovat různé telemerie z vašich zařízení. Chcete-li začít, navštivte v levém podokně **službu Analytics.**

## <a name="understanding-the-analytics-ui"></a>Principy ui analýzy
Uživatelské rozhraní Analytics je tvořeno třemi hlavními součástmi:
- **Panel konfigurace dat:** Na konfiguračním panelu začněte výběrem skupiny zařízení, pro kterou chcete analyzovat data. Dále vyberte telemetrii, kterou chcete analyzovat, a vyberte metodu agregace pro každou telemetrii. **Ovládací** prvek Rozdělit pomáhá seskupit data pomocí vlastností zařízení jako dimenzí.

- **Řízení času:** Řízení času se používá k výběru doby trvání, po kterou chcete analyzovat data. Přetažením jezdce času můžete vybrat časový rozsah. Řízení času má také posuvník **velikostinterval,** který řídí segment nebo velikost intervalu slouží k agregaci dat. 

- **Ovládací prvek Grafu:** Ovládací prvek grafu vizualizuje data jako spojnicový graf. Můžete přepnout viditelnost určitých řádků interakcí s legendou grafu. 


  ![Přehled ui analýzy](media/howto-create-analytics/analyticsui.png)


## <a name="querying-your-data"></a>Dotazování na data

Budete muset začít výběrem **skupiny zařízení**a telemetrie, kterou chcete analyzovat. Až budete hotovi, vyberte **Analyzovat** a začněte vizualizovat data.

- **Skupina zařízení:** [Skupina zařízení](tutorial-use-device-groups.md) je uživatelem definovaná skupina vašich zařízení. Například všechny chladničky v Oaklandu nebo všechny verze 2.0 větrné turbíny.

- **Telemetrie:** Vyberte telemetrii, kterou chcete analyzovat a prozkoumat. Můžete vybrat více telemerie analyzovat společně. Výchozí metoda agregace je nastavena na průměr pro číselné a Count pro typ dat řetězce v uvedeném pořadí. Podporované metody agregace pro číselné datové typy jsou Průměr, Maximum, Minimum, Počet a Součet.  Podporované metody agregace pro datový typ řetězce jsou počet.

- **Rozdělit podle:** Ovládací prvek Rozdělení podle pomáhá seskupit data pomocí vlastností zařízení jako dimenzí. Hodnoty vlastností zařízení a cloudu jsou spojeny spolu s telemetrií, jak a kdy je odeslána zařízením. Pokud cloud nebo zařízení vlastnost byla aktualizována, pak se zobrazí telemetrie seskupené podle různých hodnot v grafu.

    > [!TIP]
    > Chcete-li zobrazit data pro každé zařízení samostatně, vyberte ID zařízení v ovládacím prvku Rozdělit.

## <a name="interacting-with-your-data"></a>Interakce s daty

Po dotazování na data je můžete začít vizualizovat v spojnicovém grafu. Můžete zobrazit/skrýt telemetrii, změnit dobu trvání času, zobrazit telemetrii v datové mřížce.

- **Panel editoru času:** Ve výchozím nastavení budeme načítat data z posledních jednoho dne. Přetažením jezdce času můžete změnit dobu trvání. Pomocí ovládacího prvku kalendář můžete také vybrat jeden z předdefinovaných časových bloků nebo vybrat vlastní časový rozsah. Řízení času má také posuvník **velikostinterval,** který řídí segment nebo velikost intervalu slouží k agregaci dat.

    ![Editor času](media/howto-create-analytics/timeeditorpanel.png)

    - **Jezdec vnitřního rozsahu dat**: Použijte dva ovládací prvky koncového bodu jejich přetažením v požadovaném časovém rozpětí. Tento vnitřní rozsah dat je omezen ovládacím prvkem posuvníku vnějšího rozsahu dat.
    
   
    - **Ovládací prvek posuvníku vnějšího rozsahu dat**: Pomocí ovládacích prvků koncového bodu vyberte vnější rozsah dat, který bude k dispozici pro váš vnitřní ovládací prvek rozsahu dat.

    - **Tlačítka pro zvýšení a zmenšení časového rozsahu**: Zvětšete nebo zmenšete časový rozsah výběrem jednoho tlačítka pro požadovaný interval.

    - **Posuvník velikosti intervalů**: Slouží k přiblížení a oddálení intervalů ve stejném časovém rozpětí. Tato akce poskytuje přesnější kontrolu pohybu mezi velkými řezy času. Můžete ji použít k zobrazení granulovaných zobrazení dat s vysokým rozlišením, a to i do milisekund. Výchozí počáteční bod posuvníku je nastaven jako nejoptimálnější zobrazení dat z výběru, které vyvažuje rozlišení, rychlost dotazu a rozlišovací schopnost.
    
    - **Výběr rozsahu dat**: Pomocí tohoto webového ovládacího prvku můžete snadno vybrat požadované datum a čas. Ovládací prvek můžete také použít k přepínání mezi různými časovými pásmy. Po provedeném provádění změn, které se použijí v aktuálním pracovním prostoru, vyberte Uložit.

    > [!TIP]
    > Velikost intervalu je určena dynamicky na základě zvoleného časového rozpětí. Menší časové rozsahy umožní agregaci dat do velmi podrobné intervaly až několik sekund.


- **Legenda grafu:** Legenda grafu zobrazuje vybranou telemetrii v grafu. Na každou položku na legendě můžete najet tak, aby se zaostřila na graf. Při použití "Rozdělit podle" je telemetrie seskupena podle příslušných hodnot vybrané dimenze. Viditelnost každé konkrétní telemetrie nebo celé skupiny můžete přepnout kliknutím na název skupiny.  


- **Ovládání formátu osy Y:** režim osy y prochází dostupnými možnostmi zobrazení osy y. Tento ovládací prvek je k dispozici pouze v případě, že jsou vizualizovány různé telemerie. Osu y můžete nastavit výběrem z jednoho ze tří režimů:

    - **Skládaný:** Graf pro každou telemetrii je skládaný a každý z grafů má svou vlastní osu y. Tento režim je nastaven jako výchozí.
    - **Sdíleno:** Graf pro každou telemetrii je vykreslen proti stejné ose y.
    - **Překrytí:** Slouží k stohování více čar na stejné ose y, přičemž data osy y se mění na základě vybrané čáry.

  ![Uspořádání dat na ose y pomocí různých režimů vizualizace](media/howto-create-analytics/yaxiscontrol.png)

- **Ovládání přiblížení:** Zoom umožňuje přejít k podrobnostem vašich dat. Pokud najdete časové období, na které se chcete zaměřit v rámci sady výsledků, pomocí ukazatele myši oblast uchopte a přetáhněte ji do cílového bodu podle vašeho výběru. Pak klikněte pravým tlačítkem myši na vybranou oblast a klikněte na Lupa.

  ![Přiblížení dat](media/howto-create-analytics/zoom.png)

Pod třemi tečkami jsou k dispozici další ovládací prvky grafu pro interakci s daty.

- **Zobrazovaná mřížka:** Výsledky jsou k dispozici ve formátu tabulky, což umožňuje zobrazit konkrétní hodnotu pro každý datový bod.

- **Zařadit značku:** Ovládací prvek 'Drop Marker' poskytuje způsob, jak ukotvit určité datové body v grafu. To je užitečné, když se pokoušíte porovnat data pro více řádků v různých časových obdobích.

  ![Zobrazení mřížky pro analýzu](media/howto-create-analytics/additionalchartcontrols.png)