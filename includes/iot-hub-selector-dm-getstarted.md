---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.author: dobett
ms.openlocfilehash: 13eddced155eab6dedfbce77330e7a178ecfb3cb
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164934"
---
> [!div class="op_single_selector"]
> * [Zařízení: Node.js Služba: Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [Zařízení: C# služby:C#](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Zařízení: Služby pro Javu: Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [Zařízení: Služby Python: Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

Back endové aplikace můžete použít Azure IoT Hub primitiv, například [dvojče zařízení] [ lnk-devtwin] a [přímé metody][lnk-c2dmethod], abyste vzdáleně spouštět a monitorovat zařízení akce správy na zařízeních. Tento kurz vám ukáže, jak back endové aplikace a aplikace pro zařízení vzájemně spolupracují na zahájení a monitorování restartování vzdálené zařízení pomocí služby IoT Hub.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Přímé metody použijte k zahájení akce správy zařízení (jako je restartování, obnovení továrního nastavení a aktualizaci firmwaru) z back endové aplikace v cloudu. Zařízení je zodpovědný za:

* Zpracování metody požadavku odeslaného ze služby IoT Hub.
* Zahajuje se příslušné akce specifické pro zařízení na zařízení.
* WSUS budou poskytovat aktualizace stavu prostřednictvím *ohlášené vlastnosti* do služby IoT Hub.

Back endové aplikace v cloudu můžete použít ke spuštění dotazů na dvojčata zařízení pro informování o průběhu vaše akce správy zařízení.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
