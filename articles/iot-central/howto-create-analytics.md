---
title: Analýza dat zařízení v aplikaci Azure IoT Central | Dokumentace Microsoftu
description: Analýza dat zařízení v aplikaci Azure IoT Central.
author: lmasieri
ms.author: lmasieri
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: c170f573a473d3fdfca5c916b4be594dc93d6b27
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50156319"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Jak používat analýzu k analýze dat zařízení


*Tento článek je pro operátory, tvůrce a správce.*


Azure IoT Central poskytuje bohaté analytické funkce dávat smysl velkých objemů dat z vašich zařízení. Abyste mohli začít, navštivte **Analytics** v levé navigační nabídce. 

  ![IoT Central navigaci na analytics](media\howto-create-analytics\analytics-navigation.png)

## <a name="querying-your-data"></a>Zadávání dotazů na data

Budete muset zvolit **sada zařízení**, přidat **filtr** (volitelné) a vyberte **časové období** začít. Až budete hotovi, klikněte na *zobrazit výsledky* spustit vizualizace vaše data.


* **Sady zařízení:** A [sada zařízení](howto-use-device-sets.md) je uživatelem definované skupiny zařízení. Například všechny chladniček v Oakland nebo všechny rev 2.0 větrné turbíny.

<!---
to-do: confirm if 10 is the max number of filters
to-do: do we need to explain how fiters work?
--->

* **Filtry:** Volitelně můžete přidat filtry pro hledání můžete doladit vaše data. Najednou můžete přidat až 10 filtry. Například v rámci všech chladniček v Oakland najdete ty, které jste využili teploty přejít překročí 60 stupňů. 
* **Časové období:** ve výchozím nastavení bude načteme data z posledních 10 minut. Můžete změnit tuto hodnotu na jednom z rozsahů předem definovaný čas nebo vyberte vlastní časové období. 

 ![Analytický dotaz](media\howto-create-analytics\analytics-query.png)

## <a name="visualizing-your-data"></a>Vizualizace dat

Jakmile jste dotázali vaše data, budete mít vizualizaci ho spustit. Je možné zobrazit nebo skrýt měření, změnit způsob, jakým jsou data agregovaná a další rozdělit data podle vlastností jiné zařízení.  

* **Rozděleno podle:** rozdělit data podle vlastností zařízení umožňuje další přejít dolů o vašich datech. Můžete třeba rozdělit výsledky podle ID zařízení nebo umístění.
<!---
to-do: confirm if 10 is the max number of measurements
--->
* **Měření:** můžete zobrazit nebo skrýt hlášených zařízení najednou až 10 položek různých telemetrie. Měření jsou věci, jako je například teploty a vlhkosti. 
* **Agregace:** ve výchozím nastavení jsme agregovat data podle jeho průměru, ale je možné změnit agregaci dat na něco jiného podle vašich potřeb. 

   ![Vizualizace analýz](media\howto-create-analytics\analytics-visualize.png) <br/><br/>
   ![Vizualizace analýz rozděleno podle](media\howto-create-analytics\analytics-splitby.png)

## <a name="interacting-with-your-data"></a>Interakce s vašimi daty

Máte různé způsoby, ve kterých můžete dále změnit podle svých potřeb vizualizace výsledků dotazu. Můžete přepínat mezi zobrazení grafu a zobrazení mřížky, přiblížení nebo oddálení, aktualizovat datovou sadu a měnit, jak jsou zobrazeny čáry.

* **Zobrazit mřížku:** vaše výsledky budou k dispozici ve formátu tabulky, můžete podívat konkrétní hodnotu pro každý datový bod. Toto zobrazení také splňuje standardy usnadnění. 
* **Zobrazit graf:** vaše výsledky se zobrazí v řádku formát, který se snadno body stoupající nebo klesající trendy a anomálie. 

 ![Zobrazením mřížky pro analýzu](media\howto-create-analytics\analytics-showgrid.png)

Přiblížení umožňuje doladit vaše data. Pokud najdete časové období, které by raději se zaměřuji na v rámci ve výsledné sadě, použijte kurzor a zkopírovat oblasti, která byste chtěli přiblížit a pomocí dostupných ovládacích prvků proveďte jeden z následujících akcí:
* **Přiblížit:** po výběru časové období, Lupa v se povolí a umožní vám přiblížit k vašim datům.
* **Horizonální oddálení:** tento ovládací prvek umožňuje zmenšit o jednu úroveň z poslední přiblížení. Například pokud jste přiblížení na vaše data třikrát zmenšit bude trvat zpět jeden krok po jednom.
* **Obnovit zvětšení:** po jste provedli různé úrovně přiblížení, můžete ovládací prvek lupy resetování se vraťte k původní sadu výsledků. 

 ![Provést změna měřítka zobrazení dat.](media\howto-create-analytics\analytics-zoom.png)


Můžete změnit styl čáry podle svých potřeb. Máte vybírat ze čtyř možností:
* **Řádek:** budou tvořeny plochý čáry mezi jednotlivých datových bodů. 
* **Smooth:** křivka mezi každého bodu bude vytvořen.
* **Krok:** řádek mezi každý bod na grafu vytvoří krok grafu
* **Bodový:** všechny body se vykreslit v grafu bez řádky jejich propojení. 

 ![Jiný řádek typů dostupných v Analytics](media\howto-create-analytics\analytics-linetypes.png)

A konečně můžete uspořádat data napříč osy y výběrem jedné ze tří režimů:

* **Skládaný:** je skládaného grafu pro každou měření a grafy mají své vlastní osy y. Vrstvené grafy jsou užitečné, pokud jste vybrali více měření a chcete mít odlišné zobrazení těchto měření.
* **Neskládaných:** graf každá míra je vykreslenou vůči jednu osu y, ale byly změněny hodnoty pro osy y založenou na zvýrazněný rozměru. Neskládaných grafy jsou užitečné, pokud chcete překrýt více měr a chcete zobrazit vzory napříč tato opatření pro ve stejném časovém rozmezí.
* **Sdílená osa y:** všechny grafy sdílet stejnou osu y a hodnoty na ose se nezmění. Sdílené osy y grafy jsou užitečné, pokud se chcete podívat na jedinou při dělení dat s rozdělit podle.

 ![Uspořádání dat napříč osy y pomocí vizualizací režimy](media\howto-create-analytics\analytics-yaxis.png)

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak vytvořit vlastní analýza pro aplikace Azure IoT Central, tady je navrhované další krok:

> [!div class="nextstepaction"]
> [Příprava a připojit aplikaci Node.js](howto-connect-nodejs.md)