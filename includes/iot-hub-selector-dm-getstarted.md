---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 239ea2eb4d5bc8d326d5ca503a18b149252dc1be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69558715"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

Back-endové aplikace můžou používat primitiva Azure IoT Hub, jako jsou [dvojče zařízení][lnk-devtwin] a [přímé metody][lnk-c2dmethod], ke vzdálenému spuštění a monitorování akcí správy zařízení na zařízeních. Tento kurz ukazuje, jak může back-endová aplikace a aplikace zařízení spolupracovat na zahájení a monitorování vzdáleného restartování zařízení pomocí služby IoT Hub.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Pomocí přímé metody můžete zahájit akce správy zařízení (například restartování, obnovení továrního nastavení a aktualizaci firmwaru) z back-endové aplikace v cloudu. Zařízení je odpovědné za:

* Zpracování požadavku na metodu odeslané z ioT hubu.

* Zahajuji odpovídající akci specifickou pro zařízení na zařízení.

* Poskytování aktualizací stavu prostřednictvím *ohlášených vlastností* služby IoT Hub.

Pomocí back-endové aplikace v cloudu můžete spouštět dotazy na dvojče zařízení a podávat zprávy o průběhu akcí správy zařízení.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
