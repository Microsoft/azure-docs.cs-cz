---
ms.openlocfilehash: 72ccad94301e053d8103ca949d41202e58d9f5bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60780405"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

Dvojčata zařízení jsou dokumenty JSON, které obsahují informace o stavu zařízení (metadata, konfigurace a podmínky). IoT Hub udržuje takové dvojče pro každé zařízení, která se k němu připojuje.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Použití dvojčat zařízení:

* Store metadat zařízení z back-endem řešení.
* Aktuální informace o stavu, jako jsou k dispozici možnosti a podmínky (třeba připojení metoda se používá) sestavy z aplikace pro zařízení.
* Synchronizace stavu dlouhotrvající pracovní postupy (například aktualizace firmwaru a konfigurace) mezi aplikace pro zařízení a back endové aplikace.
* Dotazování metadat zařízení, konfigurace nebo stavu.

Dvojčata zařízení jsou navržené pro synchronizaci a dotazování na konfigurace zařízení a podmínky. Další informace o použití dvojčat zařízení najdete v [Principy dvojčat zařízení](../articles/iot-hub/iot-hub-devguide-device-twins.md).

Dvojčata zařízení jsou uložené ve službě IoT hub a obsahovat:

* *značky*, přístupná jenom pro back-end řešení; metadat zařízení
* *požadované vlastnosti*, objekty JSON upravitelná řešení zpět end a pozorovat aplikací zařízení; a
* *ohlášené vlastnosti*, objekty JSON upravitelnými aplikací zařízení a přečíst back-endu řešení. Značky a vlastnosti nemohou obsahovat pole, ale mohou být vnořené objekty.

![Funkci zobrazení obrázků dvojčete zařízení](./media/iot-hub-selector-twin-get-started/twin.png)

Kromě toho se můžete dotazovat dvojčata zařízení podle výše uvedená data back-endu řešení.
Odkazovat na [Principy dvojčat zařízení](../articles/iot-hub/iot-hub-devguide-device-twins.md) pro další informace o dvojčata zařízení a [dotazovací jazyk služby IoT Hub](../articles/iot-hub/iot-hub-devguide-query-language.md) referenční informace pro dotazování.


V tomto kurzu získáte informace o následujících postupech:

* Vytvoření back endové aplikace, která přidá *značky* dvojče zařízení a aplikace simulovaného zařízení, která generuje sestavy jeho připojení kanálu jako *hlášené vlastnost* ve dvojčeti zařízení.
* Dotaz na zařízení z aplikace back-end pomocí filtry značek a vlastnosti předtím vytvořili.

<!-- images -->
[img-twin]: media/iot-hub-selector-twin-get-started/twin.png

<!-- links -->
[lnk-query]: ../articles/iot-hub/iot-hub-devguide-query-language.md
[lnk-twins]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-d2c]: ../articles/iot-hub/iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md