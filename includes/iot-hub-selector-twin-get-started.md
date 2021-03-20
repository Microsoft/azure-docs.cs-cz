---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 08/20/2019
ms.openlocfilehash: c412f7f3e20e4d04083e457bfb245b850b65e126
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "70050384"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

Dvojčata zařízení jsou dokumenty JSON s uloženými informacemi o stavu zařízení, včetně metadat, konfigurací a podmínek. IoT Hub přetrvává pro každé zařízení, které se k němu připojuje.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Použít vlákna zařízení k těmto akcím:

* Ukládejte metadata zařízení z back-endu vašeho řešení.

* Ohlaste informace o aktuálních stavech, jako jsou dostupné možnosti a podmínky, například použitá metoda připojení z aplikace zařízení.

* Synchronizuje stav dlouhotrvajících pracovních postupů, jako jsou například aktualizace firmwaru a konfigurace, mezi aplikací zařízení a back-end aplikací.

* Dotaz na metadata, konfiguraci nebo stav zařízení

Vlákna zařízení jsou určená pro synchronizaci a pro dotazování na konfigurace a podmínky zařízení. Další informace o tom, kdy použít vlákna zařízení, najdete v [porozumět nevlákenám zařízení](../articles/iot-hub/iot-hub-devguide-device-twins.md).

Vlákna zařízení se ukládají ve službě IoT Hub a obsahují tyto prvky:

* **Značky**. Metadata zařízení jsou přístupná pouze back-end řešení.

* **Požadované vlastnosti**. Objekty JSON, které lze upravit pomocí back-endu řešení a které jsou pozorovatelně aplikace pro zařízení.

* **Hlášené vlastnosti**. Objekty JSON, které lze upravit pomocí aplikace zařízení a které lze přečíst v back-endu řešení.

Značky a vlastnosti nemůžou obsahovat pole, ale objekty můžou být vnořené.

Následující ilustrace znázorňuje nevlákennou organizaci zařízení:

![Funkce, která zobrazuje obrázek v zařízení](./media/iot-hub-selector-twin-get-started/twin.png)

Back-end řešení se navíc může dotazovat na vlákna zařízení na základě všech výše uvedených dat.
Další informace o nevlákenách zařízení najdete v tématu [vysvětlení nevláken zařízení](../articles/iot-hub/iot-hub-devguide-device-twins.md). Další informace o dotazování najdete v tématu [IoT Hub dotazovacího jazyka](../articles/iot-hub/iot-hub-devguide-query-language.md).


V tomto kurzu získáte informace o následujících postupech:

* Vytvořte si aplikaci pro back-end, která přidá značky do vlákna zařízení a aplikaci simulovaného zařízení, která hlásí kanál připojení jako hlášenou vlastnost v zařízení.

* Dotazujte zařízení z vaší back-endové aplikace pomocí filtrů u značek a vlastností, které jste vytvořili dříve.
