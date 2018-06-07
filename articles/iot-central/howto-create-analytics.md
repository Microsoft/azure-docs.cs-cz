---
title: Vytvořit vlastní analýza pro aplikace Azure IoT centrální | Microsoft Docs
description: Jako operátor jak vytvořit vlastní analýza pro aplikace Azure IoT centrální.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 5facdf3f02b71e154a23d8f26c7bcc40b5c71e35
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629299"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Jak používat analytics k analýze dat zařízení

Microsoft Azure IoT centrální nabízí funkce plnohodnotnou analýzu na smysl velkých objemů dat z vašich zařízení. Můžete zobrazit a analyzovat data pro analýzy [sada zařízení](howto-use-device-sets.md) ve vaší aplikaci. Sada zařízení je uživatelem definovanou skupinu zařízení. Můžete zúžit analýzy malého zařízení nebo na jedno zařízení.

Jako operátor, zvolte **Analytics** na levém navigační nabídce zvolte skupinu zařízení a zvolte můžete zobrazit v grafu. Zde je několik nástroje, které můžete použít filtr další data:

* **Měření:** zvolte měření zobrazíte například teploty a vlhkosti.
* **Agregace:** zvolte agregační funkce pro měření. Například **minimální** nebo **průměrná**.
* **Rozdělení podle:** podrobnostem rozdělením data vlastnosti zařízení nebo název zařízení. Například rozdělte podle umístění zařízení:

     ![Hlavní stránka Analytics](media\howto-create-analytics\analytics-main.png)

* **Časové rozmezí:** můžete zvolit jednu z předdefinovaných časových rozsahů časový rozsah nebo vytvořit vlastní časový interval: ![Analytics čas rozsahu](media\howto-create-analytics\analytics-time-range.png)

## <a name="change-the-visualizations"></a>Změnit vizualizacemi

Můžete změnit v grafech podle požadavků vaší vizualizace tak, že zvolíte jeden z režimů tři:

* **Skládaný:** je skládaného grafu pro každou měření a grafů mají své vlastní osy y. Skládaný grafy jsou užitečné, pokud jste vybrali více měření a chcete mít odlišné zobrazení těchto měření.
* **Neskládaných:** grafu pro každou měr se vykreslí proti jeden osy y, ale došlo ke změně hodnoty osy y, které jsou založené na zvýrazněné měření. Neskládaných grafy jsou užitečné, když chcete překrytí více měr a chcete si prohlédnout vzory mezi tyto míry pro ve stejném časovém rozmezí.
* **Sdílené osy y:** v grafech sdílet stejné osy y a hodnoty pro osu se nezmění. Sdílené grafy osy y jsou užitečné, když chcete podívejte se na jednu míru při vytváření řezů data s rozdělení by.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili jak vytvořit vlastní analýza pro aplikace Azure IoT centrální, zde je navržené další krok:

> [!div class="nextstepaction"]
> [Příprava a připojte se k aplikaci Node.js](howto-connect-nodejs.md)