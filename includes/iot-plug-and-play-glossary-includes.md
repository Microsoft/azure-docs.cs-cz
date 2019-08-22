---
author: ChrisGMsft
ms.service: iot-pnp
ms.topic: include
ms.date: 06/28/2019
ms.author: chrisgre
ms.openlocfilehash: 7e8174855800bc6beea8750c32a6dd32588ccd9e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880459"
---
## <a name="iot-plug-and-play-device"></a>Zařízení IoT technologie Plug and Play

Zařízení IoT technologie Plug and Play je obvykle malé škálované samostatné výpočetní zařízení, které může shromažďovat data nebo ovládat jiná zařízení a které spouští software nebo firmware, který implementuje funkce deklarované v [modelu schopností zařízení](#device-capability-model).  Například zařízení IoT technologie Plug and Play může být zařízením pro monitorování prostředí nebo kontroler pro agrigulture systém. Řešení IoT hostovaná v cloudu se dají zapisovat do příkazů, řízení a příjem dat ze zařízení IoT technologie Plug and Play. Zařízení IoT technologie Plug and Play najdete v [katalogu zařízení Azure Certified for IoT](https://catalog.azureiotsolutions.com/). Každé zařízení IoT technologie Plug and Play v katalogu bylo ověřeno a má [model schopností zařízení](#device-capability-model).

## <a name="digital-twin"></a>Digitální vlákna

Digitální vlákna jsou modely zařízení IoT technologie Plug and Play.  Digitální vlákna jsou modelována s použitím [digitálního vlákna](https://aka.ms/DTDL)s definicemi.  Rozhraní Azure IoT API můžete použít k interakci s digitálními interakcemi. 

## <a name="digital-twin-definition-language"></a>Jazyk digitálního zdvojeného definování

Jazyk popisující modely a rozhraní pro [zařízení IoT technologie Plug and Play](#iot-plug-and-play-device).  K popisu možností [digitálního vlákna](#digital-twin) a k tomu, aby mohla platforma IoT a řešení IoT využívat sémantiku této entity, použijte [Jazyk digitálního zdvojeného definování](https://aka.ms/DTDL) .

## <a name="device-capability-model"></a>Model schopností zařízení

Model schopností zařízení popisuje zařízení a definuje sadu rozhraní implementovaných zařízením. Vytvořte model schopností zařízení, který bude odpovídat fyzickému zařízení, produktu nebo SKU.

## <a name="interface"></a>Rozhraní

Rozhraní popisuje související možnosti, které jsou implementované zařízením nebo digitálním vyplněním. Rozhraní se dají znovu použít napříč různými modely schopností.

## <a name="property"></a>Vlastnost

Vlastnosti jsou datová pole definovaná v [rozhraní](#interface) , které představuje nějaký stav digitálního vlákna. Vlastnosti můžete deklarovat jako jen pro čtení nebo zapisovatelné. Vlastnosti jen pro čtení jsou nastaveny pomocí kódu spuštěného v kontextu technologie Plug and Play samotného zařízení IoT, jako je například sériové číslo.  Vlastnosti s možností zápisu jsou nastaveny pomocí externích entit, jako jsou například prahové hodnoty alarmu.

## <a name="telemetry"></a>Telemetrie

Pole telemetrie definovaná v [rozhraní](#interface) reprezentují měření. Tato měření jsou obvykle hodnoty, jako jsou například čtení senzorů.

## <a name="command"></a>Příkaz

Příkazy definované v [rozhraní](#interface) představuje metody, které lze provést na základě digitálního vlákna. Například příkaz pro obnovení továrního nastavení zařízení.
