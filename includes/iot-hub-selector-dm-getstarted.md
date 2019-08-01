---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 60c0c700dde5afda2ca93a92b334cbc4f0134043
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667994"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

Back-endové aplikace můžou používat primitivní IoT Hub Azure, jako jsou například Dvojitá a [Přímá metoda][lnk-c2dmethod] [zařízení][lnk-devtwin] , vzdáleně spouštět a monitorovat akce správy zařízení v zařízeních. V tomto kurzu se dozvíte, jak může aplikace back-end a aplikace zařízení společně spolupracovat a zahájit a monitorovat vzdálené restartování zařízení pomocí IoT Hub.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Použijte přímou metodu pro zahájení akcí správy zařízení (například restartování, obnovení továrního nastavení a aktualizace firmwaru) z back-endové aplikace v cloudu. Zařízení zodpovídá za:

* Zpracovává se žádost o metodu poslaná z IoT Hub.
* Inicializuje se odpovídající akce specifická pro zařízení v zařízení.
* Poskytování aktualizací stavu prostřednictvím *hlášených vlastností* IoT Hub.

Pomocí back-endové aplikace v cloudu můžete spouštět nedokončené dotazy na zařízení a vykazovat průběh akcí správy zařízení.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
