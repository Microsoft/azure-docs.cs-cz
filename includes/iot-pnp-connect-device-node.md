---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: f6a5c2732663a8b3a9149554c173ea3a019400e0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104612808"
---
V tomto rychlém startu se dozvíte, jak vytvořit ukázkovou aplikaci IoT technologie Plug and Play zařízení, jak ji připojit k centru IoT a použít nástroj Azure IoT Explorer k zobrazení telemetrie, kterou posílá. Ukázková aplikace je napsaná v Node.js a je obsažená v sadě SDK pro zařízení Azure IoT pro Node.js. Tvůrce řešení může pomocí nástroje Azure IoT Explorer pochopit možnosti zařízení technologie Plug and Play IoT, aniž by bylo nutné zobrazovat kód zařízení.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

K dokončení tohoto rychlého startu potřebujete Node.js na svém vývojovém počítači. Nejnovější doporučenou verzi si můžete stáhnout z [NodeJS.org](https://nodejs.org)na víc platforem.

Aktuální verzi Node.js na počítači používaném pro vývoj můžete ověřit pomocí následujícího příkazu:

```cmd/sh
node --version
```

## <a name="download-the-code"></a>Stáhněte si kód

V tomto rychlém startu připravíte vývojové prostředí, které můžete použít k klonování a sestavení sady SDK pro zařízení Azure IoT Hub pro Node.js.

Otevřete příkazový řádek v adresáři dle vašeho výběru. Spusťte následující příkaz, který naklonuje [sadu Microsoft Azure IoT SDK pro Node.js](https://github.com/Azure/azure-iot-sdk-node) úložiště GitHub do tohoto umístění:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="install-required-libraries"></a>Nainstalovat požadované knihovny

Pomocí sady SDK pro zařízení sestavíte zahrnutý vzorový kód. Vytvořená aplikace simuluje zařízení, které se připojuje ke službě IoT Hub. Aplikace odesílá telemetrie a vlastnosti a přijímá příkazy.

1. V místním okně terminálu přejděte do složky naklonovaného úložiště a přejděte do složky */Azure-IoT-SDK-Node/Device/Samples/PNP* . Pak spuštěním následujícího příkazu nainstalujte požadované knihovny:

    ```cmd/sh
    npm install
    ```

1. Nakonfigurujte proměnnou prostředí pomocí připojovacího řetězce zařízení, který jste si poznamenali dříve:

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-sample-device"></a>Spuštění ukázkového zařízení

Tato ukázka implementuje jednoduché zařízení IoT technologie Plug and Play termostat. Model, který tento ukázkový implementuje, nepoužívá [komponenty](../articles/iot-pnp/concepts-modeling-guide.md)IoT technologie Plug and Play. [Soubor modelu DTDL pro zařízení termostata](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) definuje telemetrii, vlastnosti a příkazy, které zařízení implementuje.

Otevřete soubor _simple_thermostat.js_ . V tomto souboru vidíte postup:

1. Importujte požadovaná rozhraní.
1. Zápis obslužné rutiny aktualizace vlastností a obslužné rutiny příkazu.
1. Zpracování požadovaných oprav vlastností a odeslání telemetrie.
1. Volitelně můžete zařízení zřídit pomocí služby Azure Device Provisioning (DPS).

Ve funkci main vidíte, jak vše nastane dohromady:

1. Vytvořte zařízení z připojovacího řetězce nebo ho zřiďte pomocí DPS.)
1. Pomocí možnosti **modelID** zadejte model zařízení IoT technologie Plug and Play.
1. Povolte obslužnou rutinu příkazu.
1. Odešlete telemetrii ze zařízení do vašeho centra.
1. Načte zařízení do dvojitých a aktualizovaných hlášených vlastností.
1. Povolte obslužnou rutinu aktualizace požadované vlastnosti.

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Další informace o ukázkové konfiguraci najdete v [ukázkovém souboru Readme](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md).

Spusťte ukázkovou aplikaci pro simulaci technologie Plug and Play zařízení IoT, které odesílá telemetrii do služby IoT Hub. Chcete-li spustit ukázkovou aplikaci, použijte následující příkaz:

```cmd\sh
node simple_thermostat.js
```

Zobrazí se následující výstup, který indikuje, že zařízení začalo odesílat data telemetrie do centra, a teď je připravené přijímat příkazy a aktualizace vlastností.

![Potvrzovací zprávy zařízení](media/iot-pnp-connect-device-node/device-confirmation-node.png)

Ponechte ukázku spuštěnou při dokončení dalších kroků.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Použití Azure IoT Exploreru k ověření kódu

Po spuštění ukázky klienta zařízení použijte nástroj Azure IoT Explorer a ověřte, jestli funguje.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]
