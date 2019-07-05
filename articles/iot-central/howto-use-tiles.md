---
title: Způsob použití bloků v Azure IoT Central řídicí panel aplikací | Dokumentace Microsoftu
description: Jako tvůrce Další informace o použití dlaždic na řídicí panely aplikací, zařízení sada řídicí panely a řídicí panely zařízení.
author: v-krghan
ms.author: v-krghan
ms.date: 06/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 26322461e4f53bffff2fb182df6c45ed3b83c4bf
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503523"
---
# <a name="how-to-use-tiles"></a>Způsob použití bloků
Můžete přizpůsobit řídicí panely aplikací, zařízení řídicích panelů, dlaždic a řídicích panelů v zařízení nastavené. Můžete přidat víc dlaždic na řídicí panel a přizpůsobit tyto dlaždice zobrazuje informace relevantní pro vaši aplikaci. Můžete také změnit velikost dlaždic a přizpůsobení rozložení na libovolný řídicí panel. Následující snímek obrazovky ukazuje řídicí panel aplikací pomocí různých dlaždic.

![Řídicí panel aplikací](media/howto-use-tiles/image1a.png)


Následující tabulka shrnuje použití dlaždice v Azure IoT Central:

 
| dlaždice | Řídicí panel | Popis
| ----------- | ------- | ------- |
| Odkaz | Aplikace a zařízení nastavit řídicí panely |Odkaz dlaždice se po kliknutí dlaždice, které se zobrazí text záhlaví a popis. Pomocí dlaždice odkaz a povolit tak uživateli přejít na adresu URL, která souvisí s vaší aplikace. |
| Image | Aplikace a zařízení nastavit řídicí panely |Dlaždice obrázku zobrazit vlastní image a může být možné kliknout. Pomocí dlaždice s obrázkem na řídicí panel přidat grafické a volitelně povolit tak uživateli přejít na adresu URL, která je relevantní pro vaši aplikaci.|
| Štítek | Řídicí panely aplikací |Popisek dlaždice na řídicí panel zobrazit vlastní text. Můžete zvolit velikost textu. Tyto popisy, podrobností o kontaktu nebo nápovědy používat popisek dlaždici přidat relevantní informace na řídicí panel|
| Mapa | Aplikace a zařízení nastavit řídicí panely |Dlaždice mapy zobrazení umístění a stav zařízení na mapě. Například si můžete prohlédnout ve kterém se zařízení a určuje, zda je ventilátor zapnuté.|
| Spojnicový graf | Řídicí panely aplikací a zařízení |Dlaždicích spojnicových grafů zobrazení grafu agregační měření pro zařízení v časovém období. Například můžete zobrazit spojnicový graf ukazuje průměrnou teplotu a tlak na zařízení za poslední hodinu|
| Pruhový graf | Řídicí panely aplikací a zařízení |Pruhový graf dlaždice zobrazit graf agregační měření pro zařízení v časovém období. Například můžete zobrazit pruhový graf, který ukazuje průměrnou teplotu a tlak na zařízení za poslední hodinu |
| Historie událostí | Řídicí panely aplikací a zařízení |Dlaždice Historie událostí zobrazuje události u zařízení za určité časové období. Například můžete použít k zobrazení, že všechny změny teploty došlo k chybě u zařízení za poslední hodinu. |
| Historie stavů | Řídicí panely aplikací a zařízení |Historie stavů dlaždici se zobrazuje hodnoty měření časového období. Například můžete použít k zobrazení hodnoty teploty zařízení za poslední hodinu.|
| KLÍČOVÝ UKAZATEL VÝKONU | Řídicí panely aplikací a zařízení | Klíčový ukazatel výkonu dlaždici se zobrazuje agregace měření telemetrická data nebo událost v časovém období. Například můžete použít k zobrazení nejvyšší teplota dosažení zařízení za poslední hodinu.|
| Poslední známé hodnotě | Řídicí panely aplikací a zařízení |Poslední známé hodnotě dlaždici se zobrazuje nejnovější hodnotu pro měření stavu nebo telemetrická data. Například můžete použít tuto dlaždici zobrazíte nejnovější měření teploty, tlaku a vlhkosti pro zařízení.|
| Mřížka sady zařízení | Aplikace a zařízení nastavit řídicí panely | V zařízení nastavené mřížky zobrazí informace o nastavení zařízení. Pomocí dlaždice v zařízení nastavené mřížky můžete zobrazit informace, jako je název, umístění a model všechna zařízení v sadě zařízení.|


Další informace o tom, jak nakonfigurovat řídicí panel v aplikaci Azure IoT Central, naleznete v tématu [řídicí panel aplikací konfigurovat](howto-configure-homepage.md).
