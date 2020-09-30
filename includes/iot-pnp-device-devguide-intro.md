---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 09/24/2020
ms.openlocfilehash: 706e936590f5f47ee2989bf77b3caad67a8f28fc
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91579794"
---
IoT technologie Plug and Play umožňuje vytvářet inteligentní zařízení, která budou inzerovat jejich schopnosti aplikacím Azure IoT. Zařízení IoT technologie Plug and Play nevyžadují ruční konfiguraci, když ji zákazník připojí k aplikacím s podporou IoT technologie Plug and Play.

Inteligentní zařízení je možné implementovat přímo, používat [moduly](../articles/iot-hub/iot-hub-devguide-module-twins.md)nebo používat [IoT Edge moduly](../articles/iot-edge/about-iot-edge.md).

Tato příručka popisuje základní kroky potřebné k vytvoření zařízení, modulu nebo modulu IoT Edge, který následuje po [konvencích technologie Plug and Play IoT](../articles/iot-pnp/concepts-convention.md).

Pokud chcete vytvořit technologie Plug and Play zařízení, modul nebo modul IoT Edge IoT, postupujte podle těchto kroků:

1. Ujistěte se, že zařízení používá protokol WebSockets MQTT nebo MQTT pro připojení k Azure IoT Hub.
1. Vytvořte model [DTDL (Digital s definicemi jazyků)](https://github.com/Azure/opendigitaltwins-dtdl) pro popis zařízení. Další informace najdete v tématu [pochopení komponent v modelech IoT technologie Plug and Play](../articles/iot-pnp/concepts-components.md).
1. Aktualizujte zařízení nebo modul, aby informoval `model-id` jako součást připojení zařízení.
1. Implementace telemetrie, vlastností a příkazů pomocí [konvencí technologie Plug and Play IoT](../articles/iot-pnp/concepts-convention.md)

Po dokončení implementace zařízení nebo modulu použijte [Azure IoT Explorer](../articles/iot-pnp/howto-use-iot-explorer.md) a ověřte, jestli zařízení dodržuje konvence technologie Plug and Play IoT.
