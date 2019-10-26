---
title: Jak používat dlaždice v Azure IoT Central řídicím panelu aplikace | Microsoft Docs
description: Jako tvůrce se naučíte používat dlaždice na řídicích panelech aplikací, řídicích panelech sady zařízení a řídicích panelech zařízení.
author: v-krghan
ms.author: v-krghan
ms.date: 06/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 0803258c362279049a49a7eee00e7a4763621672
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952625"
---
# <a name="how-to-use-tiles"></a>Jak používat dlaždice
Dlaždice můžete použít k přizpůsobení řídicích panelů aplikací, řídicích panelů zařízení a řídicích panelů pro nastavení zařízení. Do řídicího panelu můžete přidat více dlaždic a přizpůsobit tyto dlaždice pro zobrazení informací, které jsou relevantní pro vaši aplikaci. Můžete také změnit velikost dlaždic a přizpůsobit rozložení na libovolném řídicím panelu. Na následujícím snímku obrazovky vidíte řídicí panel aplikace s různými dlaždicemi.

![Řídicí panel aplikací](media/howto-use-tiles/image1a.png)


Následující tabulka shrnuje použití dlaždic v Azure IoT Central:

 
| Podobě | Řídicí panel | Popis
| ----------- | ------- | ------- |
| Odkaz | Řídicí panely pro aplikace a nastavení zařízení |Dlaždice odkazů jsou kliknutí na dlaždice, které zobrazují nadpis a text popisu. Pomocí dlaždice odkaz můžete uživateli povolit navigaci na adresu URL, která se vztahuje k vaší aplikaci. |
| Image | Řídicí panely pro aplikace a nastavení zařízení |Dlaždice obrázku zobrazují vlastní obrázek a lze na něj kliknout. Pomocí dlaždice obrázek můžete přidat grafiku na řídicí panel a volitelně povolit uživateli přejít na adresu URL, která je pro vaši aplikaci relevantní.|
| Štítek | Řídicí panely aplikace |Dlaždice popisku zobrazují na řídicím panelu vlastní text. Můžete zvolit velikost textu. Pomocí dlaždice popisku můžete na řídicím panelu Přidat relevantní informace, jako jsou popisy, kontaktní údaje nebo pomocníka.|
| Mapa | Řídicí panely pro aplikace a nastavení zařízení |Dlaždice mapy zobrazují umístění a stav zařízení na mapě. Můžete například zobrazit, kde je zařízení a zda je ventilátor zapnutý.|
| Spojnicový graf | Řídicí panely aplikací a zařízení |Dlaždice spojnicového grafu zobrazují graf agregovaného měření pro zařízení za určité časové období. Můžete například zobrazit spojnicový graf, který zobrazuje průměrnou teplotu a tlak na zařízení za poslední hodinu.|
| Pruhový graf | Řídicí panely aplikací a zařízení |Dlaždice pruhového grafu zobrazují graf agregovaných měření pro zařízení za určité časové období. Můžete například zobrazit pruhový graf, který zobrazuje průměrnou teplotu a tlak na zařízení za poslední hodinu. |
| Historie událostí | Řídicí panely aplikací a zařízení |Dlaždice historie událostí zobrazuje události pro zařízení za časové období. Můžete ho například použít k zobrazení všech změn teploty pro zařízení během poslední hodiny. |
| Historie stavu | Řídicí panely aplikací a zařízení |Dlaždice historie stavu zobrazuje hodnoty měření za časové období. Můžete ho například použít k zobrazení hodnot teploty pro zařízení během poslední hodiny.|
| Klíčové ukazatele výkonu | Řídicí panely aplikací a zařízení | Dlaždice klíčový ukazatel výkonu zobrazuje agregovanou telemetrii nebo měření událostí za časové období. Můžete ho například použít k zobrazení maximální teploty zařízení během poslední hodiny.|
| Poslední známá hodnota | Řídicí panely aplikací a zařízení |Dlaždice Poslední známá hodnota zobrazuje nejnovější hodnotu pro telemetrii nebo měření stavu. Pomocí této dlaždice můžete například zobrazit nejnovější měření teploty, tlaku a vlhkosti zařízení.|
| Mřížka sady zařízení | Řídicí panely pro aplikace a nastavení zařízení | Mřížka sady zařízení zobrazuje informace o sadě zařízení. Pomocí dlaždice s mřížkou pro nastavení zařízení můžete zobrazit informace, jako je název, umístění a model všech zařízení v sadě zařízení.|


Další informace o tom, jak nakonfigurovat řídicí panel v aplikaci Azure IoT Central, najdete v tématu [Přidání dlaždic do řídicího panelu](./howto-add-tiles-to-your-dashboard.md).
