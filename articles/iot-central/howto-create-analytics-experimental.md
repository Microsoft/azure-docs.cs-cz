---
title: Analýza dat zařízení v aplikaci Azure IoT Central | Dokumentace Microsoftu
description: Analýza dat zařízení v aplikaci Azure IoT Central.
author: lmasieri
ms.author: lmasieri
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 69b941b97a533740322d9070497de6845e834a16
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2019
ms.locfileid: "57315635"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Jak používat analýzu k analýze dat zařízení

*Tento článek je pro operátory, tvůrce a správce.*

Azure IoT Central poskytuje bohaté analytické funkce dávat smysl velkých objemů dat z vašich zařízení. Abyste mohli začít, navštivte **Analytics** v levé navigační nabídce.

## <a name="querying-your-data"></a>Zadávání dotazů na data

Budete muset zvolit **sada zařízení**, přidat **filtr** (volitelné) a vyberte **časové období** začít. Jakmile budete hotovi, vyberte **zobrazit výsledky** spustit vizualizace vaše data.

* **Sady zařízení:** A [sada zařízení](howto-use-device-sets-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) je uživatelem definované skupiny zařízení. Například všechny chladniček v Oakland nebo všechny rev 2.0 větrné turbíny.

* **Filtry:** Volitelně můžete přidat filtry hledání můžete doladit vaše data. Najednou můžete přidat až 10 filtry. Například v rámci všech chladniček v Oakland najdete ty, které jste využili teploty přejít překročí 60 stupňů.
* **Časové období:** Ve výchozím nastavení načteme data z posledních 10 minut. Můžete změnit tuto hodnotu na jednom z rozsahů předem definovaný čas nebo vyberte vlastní časové období.

 ![Analytický dotaz](media/howto-create-analytics-experimental/analytics-query.png)

## <a name="visualizing-your-data"></a>Vizualizace dat

Jakmile jste dotázali vaše data, budete mít vizualizaci ho spustit. Je možné zobrazit nebo skrýt měření, změnit způsob, jakým jsou data agregovaná a další rozdělit data podle vlastností jiné zařízení.  

* **Rozděleno podle:** Rozdělit data podle vlastností zařízení umožňuje další přejít dolů o vašich datech. Můžete třeba rozdělit výsledky podle ID zařízení nebo umístění.

* **Měření:** Můžete zobrazit nebo skrýt hlášených zařízení najednou až 10 položek různých telemetrie. Měření jsou věci, jako je například teploty a vlhkosti.

* **Agregace:** Ve výchozím nastavení jsme agregovat data podle jeho průměru, ale je možné změnit agregaci dat na něco jiného podle vašich potřeb.

   ![Vizualizace analýz rozděleno podle](media/howto-create-analytics-experimental/analytics-splitby.png)

## <a name="interacting-with-your-data"></a>Interakce s vašimi daty

Máte různé způsoby, jak změnit podle svých potřeb vizualizace výsledků dotazu. Můžete přepínat mezi zobrazení grafu a zobrazení mřížky, přiblížení a oddálení, aktualizovat datovou sadu a měnit, jak jsou zobrazeny čáry.

* **Zobrazit mřížku:** Výsledky jsou k dispozici ve formátu tabulky, můžete podívat konkrétní hodnotu pro každý datový bod. Toto zobrazení také splňuje standardy usnadnění.
* **Zobrazit graf:** Vaše výsledky se zobrazují ve formátu řádku ke snadnější identifikaci směrem nahoru nebo klesající trendy a anomálie.

 ![Zobrazením mřížky pro analýzu](media/howto-create-analytics-experimental/analytics-showgrid.png)

Přiblížení umožňuje na domovskou stránku v s vašimi daty. Pokud najdete časové období, které by raději se zaměřuji na v rámci ve výsledné sadě, použijte kurzor a zkopírovat oblasti, která byste chtěli přiblížit a pomocí dostupných ovládacích prvků proveďte jeden z následujících akcí:

* **Přiblížení:** Jakmile vyberete časové období, přiblížit je povolená a umožní vám přiblížit k vašim datům.
* **Horizonální oddálení:** Tento ovládací prvek umožňuje zmenšit o jednu úroveň z poslední přiblížení. Například pokud jste zvětšení v vaše data třikrát zmenšit přejdete zpět jeden krok najednou.
* **Obnovit zvětšení:** Jakmile jste provedli různé úrovně přiblížení, můžete ovládací prvek lupy resetování vrátit na původní sadě výsledků.

 ![Provést změna měřítka zobrazení dat.](media/howto-create-analytics-experimental/analytics-zoom.png)

Můžete změnit styl čáry podle svých potřeb. Máte čtyři možnosti:

* **Řádek:** Plochý čáry mezi jednotlivých datových bodů.
* **Smooth:** Křivka mezi jednotlivé body.
* **Krok:** Řádek mezi každý bod na grafu představuje krok.
* **Bodový:** Všechny body jsou zobrazeny v grafu bez řádky jejich propojení.

 ![Jiný řádek typů dostupných v Analytics](media/howto-create-analytics-experimental/analytics-linetypes.png)

A konečně můžete uspořádat data napříč osy y výběrem jedné ze tří režimů:

* **Skládaný:** Je skládaného grafu pro každou měření a grafy mají své vlastní osy y. Vrstvené grafy jsou užitečné, pokud jste vybrali více měření a chcete mít odlišné zobrazení těchto měření.
* **Neskládaných:** Graf pro každá míra je vykreslenou vůči jednu osu y, ale jsou mění v závislosti na míru zvýrazněné hodnoty pro osy y. Neskládaných grafy jsou užitečné, pokud chcete překrýt více měr a chcete zobrazit vzory napříč tato opatření pro ve stejném časovém rozmezí.
* **Sdílená osa y:** Všechny grafy sdílet stejnou osu y a hodnoty na ose se nezmění. Sdílené osy y grafy jsou užitečné, pokud se chcete podívat na jedinou při dělení dat s rozdělit podle.

 ![Uspořádání dat napříč osy y pomocí vizualizací režimy](media/howto-create-analytics-experimental/analytics-yaxis.png)

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak vytvořit vlastní analýza pro aplikace Azure IoT Central, tady je navrhované další krok:

> [!div class="nextstepaction"]
> [Příprava a připojit aplikaci Node.js](howto-connect-nodejs-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)