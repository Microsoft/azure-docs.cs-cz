---
title: Analýza dat zařízení ve vaší aplikaci Azure IoT Central | Microsoft Docs
description: Analyzujte data zařízení ve vaší aplikaci Azure IoT Central.
author: ankitscribbles
ms.author: ankitgup
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 8f329b6377915a3df6e3f3e212cbd8d41f9ed80d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90017656"
---
# <a name="how-to-use-analytics-to-analyze-device-data"></a>Analýza dat zařízení pomocí analýzy

*Tento článek je pro operátory, tvůrce a správce.*

Azure IoT Central poskytuje bohatou analytickou možnost analýzy historických trendů a koreluje různé telemetrií ze svých zařízení. Začněte tím, že přejdete na web **Analytics** v levém podokně.

## <a name="understanding-the-analytics-ui"></a>Principy uživatelského rozhraní pro analýzu
Uživatelské rozhraní analýzy se skládá ze tří hlavních součástí:
- **Panel konfigurace dat:** Na panelu Konfigurace Začněte výběrem skupiny zařízení, pro kterou chcete data analyzovat. Dále vyberte telemetrii, kterou chcete analyzovat, a pro každou telemetrii vyberte metodu agregace. **Rozdělení podle** ovládacího prvku pomáhá seskupovat data pomocí vlastností zařízení jako dimenzí.

- **Řízení času:** Řízení času slouží k výběru doby trvání, pro kterou chcete data analyzovat. Časový rozsah můžete vybrat přetažením obou konců času posuvníku. Ovládací prvek Time má také posuvník **velikosti intervalu** , který řídí interval nebo velikost intervalu používaného k agregaci dat. 

- **Ovládací prvek grafu:** Ovládací prvek graf vizualizuje data jako spojnicový graf. Viditelnost konkrétních řádků můžete přepínat interakcí s legendou grafu. 


  ![Přehled uživatelského rozhraní Analytics](media/howto-create-analytics/analyticsui.png)


## <a name="querying-your-data"></a>Dotazování na data

Musíte začít volbou **skupiny zařízení**a telemetrie, kterou chcete analyzovat. Až budete hotovi, vyberte **analyzovat** a začněte vizualizovat data.

- **Skupina zařízení:** [Skupina zařízení](tutorial-use-device-groups.md) je uživatelem definovaná skupina vašich zařízení. Například všechny chladničky v Oakland nebo všechny verze 2,0 větrné turbíny.

- **Telemetrie:** Vyberte telemetrii, kterou chcete analyzovat a prozkoumat. Můžete vybrat více telemetrií a analyzovat je dohromady. Výchozí agregační metoda je nastavená na průměr pro číselnou hodnotu a počet pro datový typ String v uvedeném pořadí. Podporované metody agregace pro číselné datové typy jsou Average, Max, minim, Count a Sum.  Podporované metody agregace pro datový typ String jsou Count.

- **Rozdělit podle:** Ovládací prvek rozdělit podle pomáhá seskupovat data pomocí vlastností zařízení jako dimenzí. Hodnoty vlastností zařízení a cloudu se spolu s telemetrie spojí jako a při posílání zařízením. Pokud se vlastnost cloudu nebo zařízení aktualizovala, zobrazí se telemetrie seskupená podle různých hodnot v grafu.

    > [!TIP]
    > Pokud chcete zobrazit data pro každé zařízení samostatně, vyberte v ovládacím prvku rozdělit podle ID zařízení.

## <a name="interacting-with-your-data"></a>Interakce s daty

Po dotazování na data je můžete začít vizualizovat v spojnicovém grafu. Můžete zobrazit nebo skrýt telemetrii, změnit dobu trvání, zobrazit telemetrii v datové mřížce.

- **Panel editoru času:** Ve výchozím nastavení načteme data z minulého dne. Dobu trvání můžete změnit přetažením obou konců času posuvníku. Pomocí ovládacího prvku Kalendář můžete také vybrat jednu z předdefinovaných časových intervalů nebo vybrat vlastní časový rozsah. Ovládací prvek Time má také posuvník **velikosti intervalu** , který řídí interval nebo velikost intervalu používaného k agregaci dat.

    ![Editor času](media/howto-create-analytics/timeeditorpanel.png)

    - **Nástroj pro posuvník rozsahu vnitřního data**: pomocí dvou ovládacích prvků koncového bodu je můžete přetáhnout v požadovaném časovém rozsahu. Tento vnitřní rozsah dat je omezen ovládacím prvkem posuvník rozsahu vnějšího data.
    
   
    - **Ovládací prvek posuvník rozsahu vnějšího data**: pomocí ovládacích prvků koncového bodu můžete vybrat rozsah vnějších kalendářních dat, který bude k dispozici pro ovládací prvek rozsahu vnitřního data.

    - Tlačítko pro **zvýšení a snížení rozsahu data**: Zvyšte nebo snižte časový rozsah tak, že vyberete jedno tlačítko pro požadovaný interval.

    - **Posuvník velikosti intervalu**: používá se k přiblížení a oddálení intervalů v rámci stejného časového intervalu. Tato akce poskytuje přesnější kontrolu nad pohybem mezi velkými řezy času. Můžete ji použít k zobrazení podrobných zobrazení dat s vysokým rozlišením, a to i z provozu do milisekund. Výchozí počáteční bod posuvníku je nastaven jako optimální pohled na data z výběru, který vyrovnává rozlišení, rychlost dotazování a členitost.
    
    - **Výběr rozsahu kalendářních dat**: pomocí tohoto webového ovládacího prvku můžete snadno vybrat rozsahy dat a času, které chcete. Ovládací prvek lze také použít k přepínání mezi různými časovými pásmy. Až provedete změny, které se mají použít v aktuálním pracovním prostoru, vyberte Uložit.

    > [!TIP]
    > Velikost intervalu je určena dynamicky v závislosti na vybraném časovém rozsahu. Menší časová rozpětí budou umožňovat agregaci dat do velmi podrobných intervalů až po několik sekund.


- **Legenda grafu:** Legenda grafu zobrazuje vybranou telemetrii v grafu. Na každou položku v legendě můžete umístit ukazatel myši a zaměřit se tak na graf. Při použití operátoru Split by se telemetrie seskupují podle příslušných hodnot vybrané dimenze. Kliknutím na název skupiny můžete přepínat viditelnost každé konkrétní telemetrie nebo celé skupiny.  


- **Řízení formátu osy y:** v režimu osy y cykly procházejí dostupné možnosti zobrazení na ose y. Tento ovládací prvek je k dispozici pouze v případě, že jsou vizuálně vyladěny různé telemetrií. Osu y můžete nastavit tak, že vyberete jeden ze tří režimů:

    - **Skládané:** Graf pro každou telemetrii je skládaný a každý z grafů má svoji vlastní osu y. Tento režim je nastaven jako výchozí.
    - **Sdílená:** Graf pro každou telemetrii se vykreslí na stejnou osu y.
    - **Překryv:** Použijte ho k skládání více řádků na stejnou osu y, přičemž se mění data osy y na základě vybraného řádku.

  ![Uspořádání dat napříč osou y pomocí různých režimů vizualizace](media/howto-create-analytics/yaxiscontrol.png)

- **Ovládací prvek Lupa:** Přiblížení vám umožní přejít k datům. Pokud zjistíte časové období, na které se chcete zaměřit v rámci sady výsledků, použijte ukazatel myši k umístění oblasti a přetáhněte ji na koncový bod podle vašeho výběru. Potom klikněte pravým tlačítkem na vybranou oblast a klikněte na tlačítko Lupa.

  ![Přiblížení dat](media/howto-create-analytics/zoom.png)

Pod třemi tečkami je k interakci s daty k dispozici více ovládacích prvků grafu:

- **Zobrazit mřížku:** Vaše výsledky jsou k dispozici ve formátu tabulky a umožňují vám zobrazit konkrétní hodnotu pro každý datový bod.

- **Stáhnout jako sdílený svazek clusteru:** Vaše výsledky jsou k dispozici pro export jako textový soubor s oddělovači (CSV). Soubor CSV obsahuje data pro každé zařízení. Výsledky se exportují pomocí zadaného intervalu a časového rámce. 

- **Odstranit značku:** Ovládací prvek Drop marker poskytuje způsob, jak ukotvit určité datové body v grafu. To je užitečné v případě, že se snažíte porovnat data pro více řádků v různých časových obdobích.

  ![Zobrazení mřížky pro vaše analýzy](media/howto-create-analytics/additionalchartcontrols.png)
