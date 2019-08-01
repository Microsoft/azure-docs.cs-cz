---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: eef073f5f4d1eb39fd5ccd8dafacd7074158fa37
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667851"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

Dvojčata zařízení jsou dokumenty JSON, které obsahují informace o stavu zařízení (metadata, konfigurace a podmínky). IoT Hub přetrvává pro každé zařízení, které se k němu připojuje.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Použít vlákna zařízení k těmto akcím:

* Ukládejte metadata zařízení z back-endu vašeho řešení.

* Ohlaste informace o aktuálních stavech, jako jsou dostupné možnosti a podmínky (například použitá metoda připojení) z aplikace vašeho zařízení.

* Synchronizuje stav dlouhotrvajících pracovních postupů (například aktualizace firmwaru a konfigurace) mezi aplikací zařízení a back-end aplikací.

* Dotaz na metadata, konfiguraci nebo stav zařízení

Vlákna zařízení jsou určená pro synchronizaci a pro dotazování na konfigurace a podmínky zařízení. Další informace o tom, kdy použít vlákna zařízení, najdete v porozumět nevlákenám [zařízení](../articles/iot-hub/iot-hub-devguide-device-twins.md).

Vlákna zařízení se ukládají ve IoT Hub a obsahují:

* *značky*, metadata zařízení dostupná jenom back-end řešení;

* *požadované vlastnosti*, objekty JSON, které lze upravit pomocí back-endu řešení a které jsou v aplikaci pro zařízení pozorovatelné; ani

* *hlášené vlastnosti*, objekty JSON, které lze upravit pomocí aplikace zařízení a které lze přečíst v back-endu řešení. Značky a vlastnosti nemůžou obsahovat pole, ale objekty můžou být vnořené.

![Funkce, která zobrazuje obrázek v zařízení](./media/iot-hub-selector-twin-get-started/twin.png)

Back-end řešení se navíc může dotazovat na vlákna zařízení na základě všech výše uvedených dat.
Přečtěte si [vysvětlení dvojitých vláken zařízení](../articles/iot-hub/iot-hub-devguide-device-twins.md) , kde najdete další informace o nevlákenách zařízení a referenční informace k [jazyku IoT Hub dotazů](../articles/iot-hub/iot-hub-devguide-query-language.md) pro dotazování.


V tomto kurzu získáte informace o následujících postupech:

* Vytvořte si aplikaci pro back-end, která přidá *značky* do vlákna zařízení a aplikaci simulovaného zařízení, která hlásí kanál připojení jako hlášenou *vlastnost* v zařízení.

* Dotazujte zařízení z vaší back-endové aplikace pomocí filtrů u značek a vlastností, které jste vytvořili dříve.
