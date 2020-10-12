---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 239ea2eb4d5bc8d326d5ca503a18b149252dc1be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "69558715"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

Back-endové aplikace můžou používat primitivní IoT Hub Azure, jako jsou například [Dvojitá][lnk-devtwin] a [Přímá metoda][lnk-c2dmethod]zařízení, vzdáleně spouštět a monitorovat akce správy zařízení v zařízeních. V tomto kurzu se dozvíte, jak může aplikace back-end a aplikace zařízení společně spolupracovat a zahájit a monitorovat vzdálené restartování zařízení pomocí IoT Hub.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Použijte přímou metodu pro zahájení akcí správy zařízení (například restartování, obnovení továrního nastavení a aktualizace firmwaru) z back-endové aplikace v cloudu. Zařízení zodpovídá za:

* Zpracovává se žádost o metodu poslaná z IoT Hub.

* Inicializuje se odpovídající akce specifická pro zařízení v zařízení.

* Poskytování aktualizací stavu prostřednictvím *hlášených vlastností* IoT Hub.

Pomocí back-endové aplikace v cloudu můžete spouštět nedokončené dotazy na zařízení a vykazovat průběh akcí správy zařízení.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
