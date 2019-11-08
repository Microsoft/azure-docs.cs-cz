---
title: Analýza dat zařízení ve vaší aplikaci Azure IoT Central | Microsoft Docs
description: Tento článek popisuje, jak analyzovat data zařízení ve vaší aplikaci Azure IoT Central pomocí dotazů a vizualizací.
author: lmasieri
ms.author: lmasieri
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: a467e0e6e8967cf963ad099f83de6718330aa43f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827976"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Jak pomocí analýzy analyzovat data zařízení

*Tento článek je pro operátory, tvůrce a správce.*

Azure IoT Central poskytuje bohatou analýzu možností, díky kterým můžete ze svých zařízení vydávat smysly velké objemy dat. Začněte tím, že přejdete na web **Analytics** v levém podokně.

## <a name="querying-your-data"></a>Dotazování na data

Budete muset vybrat **sadu zařízení**, přidat **Filtr** (volitelné) a vybrat **časové období** , které chcete začít. Jakmile budete hotovi, vyberte **Zobrazit výsledky** a začněte vizualizovat data.

* **Sady zařízení:** [Sada zařízení](howto-use-device-sets.md) je uživatelem definovaná skupina vašich zařízení. Například všechny chladničky v Oakland nebo všechny větrné turbíny rev 2,0.

* **Filtry:** Volitelně můžete do vyhledávání přidat filtry, které doladit do svých dat. Po dobu můžete přidat až 10 filtrů. Například v rámci všech chladničk v Oakland Najděte ty, které mají teplotu vyšší než 60 stupňů.
* **Časové období:** Ve výchozím nastavení načteme data za posledních 10 minut. Tuto hodnotu můžete změnit na jeden z předdefinovaných časových rozsahů nebo vybrat vlastní časové období.

  ![Dotaz Analytics](media/howto-create-analytics/analytics-query.png)

## <a name="visualizing-your-data"></a>Vizualizace dat

Po dotazování na data budete moct začít vizualizovat. Můžete zobrazit nebo skrýt měření, změnit způsob agregace dat a dále rozdělit data podle různých vlastností zařízení.  

* **Rozdělit podle:** Rozdělení dat podle vlastností zařízení vám umožní další přechod k podrobnostem vašich dat. Výsledky můžete například rozdělit podle ID nebo umístění zařízení.

* **Měření:** Můžete zvolit, že chcete zobrazit nebo skrýt až 10 různých položek telemetrie, které zařízení hlásí. Měření jsou věci, jako je teplota a vlhkost.

* **Agregace:** Ve výchozím nastavení agregujeme data podle průměru, ale můžete se rozhodnout, že datovou agregaci změníte na něco jiného, aby vyhovovalo vašim potřebám.

   ![Rozdělte vizualizaci analýz](media/howto-create-analytics/analytics-splitby.png)

## <a name="interacting-with-your-data"></a>Interakce s daty

Existují různé způsoby, jak změnit výsledky dotazu tak, aby vyhovovaly vašim potřebám vizualizace. Můžete se střídat mezi zobrazením grafu a tabulkovým zobrazením, přiblížit a oddálit, aktualizovat datovou sadu a změnit způsob zobrazení řádků.

* **Zobrazit mřížku:** Vaše výsledky jsou k dispozici ve formátu tabulky a umožňují vám zobrazit konkrétní hodnotu pro každý datový bod. Toto zobrazení také splňuje standardy přístupnosti.
* **Zobrazit graf:** Vaše výsledky se zobrazí ve formátu čáry, které vám pomůžou identifikovat trendy a anomálie směrem nahoru nebo dolů.

  ![Zobrazení mřížky pro vaše analýzy](media/howto-create-analytics/analytics-showgrid.png)

Přiblížení vám umožní domů na vašich datech. Pokud v rámci sady výsledků zjistíte časové období, které byste chtěli zaměřit, použijte kurzor k přemístění oblasti, kterou chcete přiblížit, a pomocí dostupných ovládacích prvků proveďte jednu z následujících akcí:

* **Přiblížit:** Po výběru časového období je přiblížení zapnuté a umožňuje přiblížit se k datům.
* **Oddálení:** Tento ovládací prvek vám umožní oddálit jednu úroveň od posledního přiblížení. Pokud jste například přiblížili data třikrát, přiblížíte se k předchozímu kroku.
* **Resetování lupy:** Po provedení různých úrovní přiblížení můžete pomocí ovládacího prvku přiblížení pro obnovení vrátit do původní sady výsledků.

  ![Provádění lupy s daty](media/howto-create-analytics/analytics-zoom.png)

Styl čáry můžete změnit tak, aby vyhovoval vašim potřebám. Máte čtyři možnosti:

* **Řádek:** Jedna čára mezi jednotlivými datovými body.
* **Plynulé:** Zakřivená čára mezi každým bodem
* **Krok:** Čára mezi jednotlivými místy v grafu je krok.
* **Bodový graf:** Všechny body jsou vykresleny v grafu bez čar, které je spojují.

  ![Různé typy řádků, které jsou k dispozici v analýzách](media/howto-create-analytics/analytics-linetypes.png)

Nakonec můžete uspořádat data napříč osou Y výběrem jednoho ze tří režimů:

* **Skládané:** Graf pro každé měření je skládaný a každé z grafů má svou vlastní osu Y. Skládané grafy jsou užitečné, když máte vybrané více měření a chcete mít odlišné zobrazení těchto měření.
* **Neskládané:** Graf pro každou míru se vykreslí na jednu osu Y, ale hodnoty pro osu Y se změní na základě zvýrazněné míry. Neskládané grafy jsou užitečné, pokud chcete překrýt více měr a chcete zobrazit vzory napříč těmito opatřeními pro stejný časový rozsah.
* **Sdílená osa Y:** Všechny grafy sdílejí stejnou osu Y a hodnoty pro osu se nemění. Sdílené grafy osy Y jsou užitečné, když se chcete podívat na jednu míru při vytváření řezů dat pomocí rozdělení.

  ![Uspořádání dat napříč osou y pomocí různých režimů vizualizace](media/howto-create-analytics/analytics-yaxis.png)

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s vytvářením vlastních analýz pro aplikaci Azure IoT Central, je tady doporučený další krok:

> [!div class="nextstepaction"]
> [Příprava a připojení aplikace Node. js](howto-connect-nodejs.md)