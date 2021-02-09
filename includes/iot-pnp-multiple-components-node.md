---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 43a8600a288a9797edcd326a1cb5dbcc8688d61f
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99834131"
---
V tomto kurzu se dozvíte, jak vytvořit ukázkovou aplikaci IoT technologie Plug and Play zařízení pomocí komponent, připojit ji k centru IoT a pomocí nástroje Azure IoT Explorer zobrazit informace, které posílá do centra. Ukázková aplikace je napsaná pro Node.js a je obsažená v sadě SDK pro zařízení Azure IoT Hub pro Node.js. Tvůrce řešení může pomocí nástroje Azure IoT Explorer pochopit možnosti zařízení technologie Plug and Play IoT, aniž by bylo nutné zobrazovat kód zařízení.

V tomto kurzu jste:

> [!div class="checklist"]
> * Stáhněte si vzorový kód.
> * Spusťte ukázkovou aplikaci zařízení a ověřte, že se připojí ke službě IoT Hub.
> * Zkontrolujte zdrojový kód.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

K dokončení tohoto kurzu potřebujete Node.js na svém vývojovém počítači. Nejnovější doporučenou verzi si můžete stáhnout z [NodeJS.org](https://nodejs.org)na víc platforem.

Aktuální verzi Node.js na počítači používaném pro vývoj můžete ověřit pomocí následujícího příkazu:

```cmd/sh
node --version
```

## <a name="download-the-code"></a>Stáhněte si kód

Pokud jste dokončili [rychlý Start: Připojte ukázkovou aplikaci IoT technologie Plug and Play zařízení běžící v systému Windows k IoT Hub (Node)](../articles/iot-pnp/quickstart-connect-device.md), již jste naklonoval úložiště.

Otevřete příkazový řádek v adresáři dle vašeho výběru. Spusťte následující příkaz, který naklonuje [sadu Microsoft Azure IoT SDK pro Node.js](https://github.com/Azure/azure-iot-sdk-node) úložiště GitHub do tohoto umístění:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="install-required-libraries"></a>Nainstalovat požadované knihovny

Pomocí sady SDK pro zařízení sestavíte zahrnutý vzorový kód. Aplikace, kterou vytváříte, simuluje technologie Plug and Play zařízení s více komponentami, které se připojují ke službě IoT Hub. Aplikace odesílá telemetrie a vlastnosti a přijímá příkazy.

1. V místním okně terminálu přejděte do složky naklonovaného úložiště a přejděte do složky */Azure-IoT-SDK-Node/Device/Samples/PNP* . Pak spuštěním následujícího příkazu nainstalujte požadované knihovny:

```cmd/sh
npm install
```

Tím se nainstalují relevantní soubory npm potřebné ke spuštění ukázek ve složce.

## <a name="review-the-code"></a>Kontrola kódu

Přejděte do složky *Azure-IoT-SDK-node\device\samples\pnp* .

Složka *Azure-IoT-SDK-node\device\samples\pnp* obsahuje vzorový kód pro zařízení adaptéru pro řízení teploty IoT technologie Plug and Play.

Kód v souboru *pnpTemperatureController.js* implementuje zařízení adaptéru IoT technologie Plug and Play. Model, který tato ukázka implementuje, používá [více komponent](../articles/iot-pnp/concepts-components.md). [Soubor modelu DTDL (Digital Real Definition Language) pro zařízení s teplotou](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definuje telemetrii, vlastnosti a příkazy, které zařízení implementuje.

Otevřete soubor *pnpTemperatureController.js* v editoru kódu dle vašeho výběru. Vzorový kód ukazuje, jak:

- Definujte, `modelId` který z nich je DTMI pro zařízení, které je právě implementováno. Tato DTMI je definovaná uživatelem a musí odpovídat DTMI [modelu DTDL řadiče teploty](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json).

- Implementujte komponenty definované v modelu DTDL řadiče teploty. Komponenty v reálném teplotním adaptéru by měly implementovat tato dvě rozhraní. Tato dvě rozhraní jsou již publikována v centrálním úložišti. V této ukázce jsou tato dvě rozhraní:

  - Termostat
  - Informace o zařízeních vyvinutých v Azure

- Definujte názvy součástí. Tato ukázka má dvě termostaty a jednu komponentu informací o zařízení.

- Zadejte název příkazu. Toto jsou příkazy, na které zařízení reaguje.

- Definujte `serialNumber` konstantu. Toto `serialNumber` zařízení je pevně dané.

- Definujte obslužné rutiny příkazů.

- Definujte funkce pro odeslání odpovědí na příkazy.

- Definujte pomocné funkce pro protokolování požadavků příkazu.

- Definujte pomocnou funkci pro vytvoření vlastností.

- Definujte naslouchací proces pro aktualizace vlastností.

- Definujte funkci pro odeslání telemetrie z tohoto zařízení. Všechna termostaty i výchozí součást odesílají telemetrii. Tato funkce přijímá název komponenty jako parametr.

- Definujte `main` funkci, která:

  - Pomocí sady SDK pro zařízení vytvořte klienta zařízení a připojte se ke službě IoT Hub. Zařízení dodá `modelId` , aby IoT Hub mohl zařízení identifikovat jako zařízení technologie Plug and Play IoT.

  - Spustí naslouchání žádostí o příkazy pomocí `onDeviceMethod` funkce. Funkce nastaví naslouchací proces pro požadavky příkazu ze služby:

    - DTDL zařízení definuje `reboot` `getMaxMinReport` příkazy a.
    - `commandHandler`Funkce definuje způsob, jakým zařízení reaguje na příkaz.

  - Spustí odesílání telemetrie pomocí `setInterval` a `sendTelemetry` .

  - Pomocí `helperCreateReportedPropertiesPatch` funkce vytvoří vlastnosti a `updateComponentReportedProperties` aktualizuje vlastnosti.

  - Nástroj používá `desiredPropertyPatchListener` k naslouchání aktualizacím vlastností.

  - Zakáže všechny naslouchací procesy a úlohy a ukončí smyčku po stisknutí **q** nebo **q**.

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Další informace o ukázkové konfiguraci najdete v [ukázkovém souboru Readme](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md).

Teď, když jste viděli kód, použijte následující příkaz ke spuštění ukázky:

```cmd\sh
node pnpTemperatureController.js
```

Zobrazí se následující výstup, který indikuje, že zařízení začalo odesílat data telemetrie do centra, a teď je připravené přijímat příkazy a aktualizace vlastností.

![Potvrzovací zprávy zařízení](media/iot-pnp-multiple-components-node/multiple-component.png)

Ponechte ukázku spuštěnou při dokončení dalších kroků.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Použití Azure IoT Exploreru k ověření kódu

Po spuštění ukázky klienta zařízení použijte nástroj Azure IoT Explorer a ověřte, jestli funguje.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]
