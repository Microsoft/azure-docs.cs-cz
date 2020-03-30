---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 08/20/2019
ms.openlocfilehash: c412f7f3e20e4d04083e457bfb245b850b65e126
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "70050384"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

Dvojčata zařízení jsou dokumenty JSON s uloženými informacemi o stavu zařízení, včetně metadat, konfigurací a podmínek. IoT Hub zachová dvojče zařízení pro každé zařízení, které se k němu připojí.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Pomocí dvojčat zařízení můžete:

* Ukládejte metadata zařízení z back-endu vašeho řešení.

* Oznamte aktuální informace o stavu, jako jsou dostupné možnosti a podmínky, například použitá metoda připojení, z aplikace zařízení.

* Synchronizujte stav dlouhotrvajících pracovních postupů, jako jsou aktualizace firmwaru a konfigurace, mezi aplikací zařízení a back-endovou aplikací.

* Dotaz na metadata, konfiguraci nebo stav zařízení.

Dvojčata zařízení jsou určeny pro synchronizaci a pro dotazování konfigurace zařízení a podmínky. Další informace o tom, kdy použít dvojčata zařízení, najdete v [části Porozumět dvojčatům zařízení](../articles/iot-hub/iot-hub-devguide-device-twins.md).

Dvojčata zařízení jsou uloženy v centru IoT a obsahují následující prvky:

* **Značky**. Metadata zařízení přístupná pouze back-endem řešení.

* **Požadované vlastnosti**. JSON objekty upravitelné back-endřešení a pozorovatelné aplikací zařízení.

* **Hlášené vlastnosti**. JSON objekty upravitelné aplikací zařízení a čitelné back-endem řešení.

Značky a vlastnosti nemohou obsahovat pole, ale objekty mohou být vnořeny.

Následující obrázek znázorňuje organizaci dvojčete zařízení:

![Obrázek dvojčete zařízení znázorňující funkčnost](./media/iot-hub-selector-twin-get-started/twin.png)

Kromě toho back-end řešení můžete dotaz dvojčata zařízení na základě všech výše uvedených dat.
Další informace o dvojčata zařízení, [najdete v tématu Principy dvojčatzařízení](../articles/iot-hub/iot-hub-devguide-device-twins.md). Další informace o dotazování naleznete v [tématu Dotazovací jazyk centra IoT](../articles/iot-hub/iot-hub-devguide-query-language.md).


V tomto kurzu získáte informace o následujících postupech:

* Vytvořte back-endovou aplikaci, která přidá značky do dvojčete zařízení, a simulovanou aplikaci zařízení, která hlásí svůj kanál připojení jako ohlášenou vlastnost dvojčete zařízení.

* Dotazujte se na zařízení z back-endové aplikace pomocí filtrů na dříve vytvořených značkách a vlastnostech.
