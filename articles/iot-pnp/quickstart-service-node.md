---
title: Interakce se zařízením IoT technologie Plug and Play Preview připojeným k řešení Azure IoT (Node.js) | Microsoft Docs
description: Pomocí Node.js se můžete připojit k zařízení IoT technologie Plug and Play Preview, které je připojené k řešení Azure IoT, a pracovat s ním.
author: elhorton
ms.author: elhorton
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 24c12425b7c8598f46f9a0891337129bcbabec40
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91281231"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>Rychlý Start: interakce se zařízením IoT technologie Plug and Play ve verzi Preview, které je připojené k vašemu řešení (Node.js)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

IoT technologie Plug and Play Preview zjednodušuje IoT tím, že vám umožní pracovat s funkcemi zařízení bez znalosti základní implementace zařízení. V tomto rychlém startu se dozvíte, jak pomocí Node.js připojit a řídit zařízení IoT technologie Plug and Play, které je připojené k vašemu řešení.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu potřebujete Node.js na svém vývojovém počítači. Nejnovější doporučenou verzi si můžete stáhnout z [NodeJS.org](https://nodejs.org)na víc platforem.

Aktuální verzi Node.js na počítači používaném pro vývoj můžete ověřit pomocí následujícího příkazu:

```cmd/sh
node --version
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Spuštěním následujícího příkazu Získejte _připojovací řetězec služby IoT Hub_ pro vaše centrum. Poznamenejte si tento připojovací řetězec, budete ho používat později v tomto rychlém startu:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

Spuštěním následujícího příkazu Získejte _připojovací řetězec zařízení_ pro zařízení, které jste přidali do centra. Poznamenejte si tento připojovací řetězec, budete ho používat později v tomto rychlém startu:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output
```

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Naklonujte úložiště sady SDK pomocí ukázkového kódu.

Sada SDK služby je ve verzi Preview, takže je nutné klonovat ukázky z [větve Preview sady SDK Node](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh). Otevřete okno terminálu ve složce podle vašeho výběru. Spuštěním následujícího příkazu naklonujte větev **pnp-Preview-Refresh** sady [Microsoft Azure IoT SDK pro Node.js](https://github.com/Azure/azure-iot-sdk-node) úložiště GitHub:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node -b pnp-preview-refresh
```

## <a name="run-the-sample-device"></a>Spuštění ukázkového zařízení

V tomto rychlém startu můžete použít ukázkový termostat zařízení, které je napsané v Node.js jako zařízení technologie Plug and Play IoT. Spuštění ukázkového zařízení:

1. Otevřete okno terminálu a přejděte do místní složky, která obsahuje sadu Microsoft Azure IoT SDK pro Node.js úložiště, které jste naklonoval z GitHubu.

1. Toto okno terminálu slouží jako terminál **zařízení** . Přejděte do složky naklonovaného úložiště a přejděte do složky */Azure-IoT-SDK-Node/Device/Samples/PNP* . Všechny závislosti Nainstalujte spuštěním následujícího příkazu:

    ```cmd/sh
    npm install
    ```

1. Konfigurace _připojovacího řetězce zařízení_:

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Spusťte vzorový termostat zařízení pomocí následujícího příkazu:

    ```cmd/sh
    node simple_thermostat.js
    ```

1. Zobrazí se zpráva oznamující, že zařízení odeslalo nějaké informace a nahlásilo je jako online. Tyto zprávy signalizují, že zařízení začalo posílat data telemetrie do centra a teď je připravené přijímat příkazy a aktualizace vlastností. Nezavírejte Tento terminál, budete ho potřebovat k potvrzení, že ukázka služby funguje.

## <a name="run-the-sample-solution"></a>Spuštění ukázkového řešení

V tomto rychlém startu použijete ukázkové řešení IoT v Node.js k interakci s ukázkovým zařízením, které jste právě nastavili.

1. Otevřete další okno terminálu pro použití jako terminálu **služby** . Sada SDK služby je ve verzi Preview, takže je nutné klonovat ukázky z [větve verze Preview sady SDK pro uzly](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh):

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node -b pnp-preview-refresh
    ```

1. Přejděte do složky této větve klonovaného úložiště a přejděte do složky */Azure-IoT-SDK-Node/digitaltwins/Samples/Service/JavaScript* . Všechny závislosti Nainstalujte spuštěním následujícího příkazu:

    ```cmd/sh
    npm install
    ```

1. Nakonfigurujte proměnné prostředí pro ID zařízení a _připojovací řetězec IoT Hub_:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIOTHubConnectionString>
    set IOTHUB_DEVICE_ID=<Your device ID>
    ```

### <a name="read-a-property"></a>Číst vlastnost

1. Po spuštění ukázkového zařízení v terminálu **zařízení** jste viděli následující zprávy, které indikují stav online:

    ```cmd/sh
    properties have been reported for component
    sending telemetry message 0...
    ```

1. Přejděte do terminálu **služby** a pomocí následujícího příkazu spusťte ukázku pro čtení informací o zařízení:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. Ve výstupu terminálu **služby** si všimněte odezvy digitálního vlákna. Zobrazí se ID modelu a přidružené vlastnosti zařízení, které se nahlásily:

    ```json
    "$dtId": "mySimpleThermostat",
    "serialNumber": "123abc",
    "maxTempSinceLastReboot": 51.96167432818655,
    "$metadata": {
      "$model": "dtmi:com:example:Thermostat;1",
      "serialNumber": { "lastUpdateTime": "2020-07-09T14:04:00.6845182Z" },
      "maxTempSinceLastReboot": { "lastUpdateTime": "2020-07-09T14:04:00.6845182" }
    }
    ```

1. Následující fragment kódu ukazuje kód v *get_digital_twin.js* , který načte ID modelu vlákna zařízení:

    ```javascript
    console.log("Model Id: " + inspect(digitalTwin.$metadata.$model))
    ```

V tomto scénáři výstup IT `Model Id: dtmi:com:example:Thermostat;1` .

### <a name="update-a-writable-property"></a>Aktualizovat vlastnost s možností zápisu

1. Otevřete soubor *update_digital_twin.js* v editoru kódu.

1. Přečtěte si vzorový kód. Můžete si prohlédnout, jak vytvořit opravu JSON pro aktualizaci digitálního vlákna vašeho zařízení. V této ukázce kód nahradí teplotu termostatu hodnotou 42:

    ```javascript
    const patch = [{
        op: 'add',
        path: '/targetTemperature',
        value: '42'
      }]
    ```

1. V terminálu **služby** použijte následující příkaz ke spuštění ukázky pro aktualizaci vlastnosti:

    ```cmd/sh
    node update_digital_twin.js
    ```

1. V terminálu **zařízení** vidíte, že zařízení obdrželo aktualizaci:

    ```cmd/sh
    The following properties will be updated for root interface:
    {
      targetTemperature: {
        value: 42,
        ac: 200,
        ad: 'Successfully executed patch for targetTemperature',
        av: 2
      }
    }
    updated the property
    Properties have been reported for component
    ```

1. V terminálu **služby** spusťte následující příkaz, kterým potvrdíte aktualizaci této vlastnosti:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. Ve výstupu terminálu **služby** se v digitální nedokončené reakci v rámci `thermostat1` komponenty zobrazí aktualizovaná cílová teplota. Může chvíli trvat, než zařízení dokončí aktualizaci. Opakujte tento krok, dokud zařízení nezpracuje aktualizaci vlastnosti:

    ```json
    targetTemperature: 42,
    ```

### <a name="invoke-a-command"></a>Vyvolání příkazu

1. Otevřete soubor *invoke_command.js* a zkontrolujte kód.

1. Přejděte do terminálu **služby** . Pomocí následujícího příkazu spusťte ukázku pro vyvolání příkazu:

    ```cmd/sh
    set IOTHUB_COMMAND_NAME=getMaxMinReport
    set IOTHUB_COMMAND_PAYLOAD=commandpayload
    node invoke_command.js
    ```

1. Výstup v terminálu **služby** zobrazuje následující potvrzení:

    ```cmd/sh
    {
        xMsCommandStatuscode: 200,  
        xMsRequestId: 'ee9dd3d7-4405-4983-8cee-48b4801fdce2',  
        connection: 'close',  'content-length': '18',  
        'content-type': 'application/json; charset=utf-8',  
        date: 'Thu, 09 Jul 2020 15:05:14 GMT',  
        server: 'Microsoft-HTTPAPI/2.0',  vary: 'Origin',  
        body: 'min/max response'
    }
    ```

1. V terminálu **zařízení** se zobrazí potvrzení příkazu:

    ```cmd/sh
    MaxMinReport commandpayload
    Response to method 'getMaxMinReport' sent successfully.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak připojit zařízení IoT technologie Plug and Play k řešení IoT. Další informace o modelech zařízení IoT technologie Plug and Play najdete v tématech:

> [!div class="nextstepaction"]
> [Příručka pro vývojáře pro modelování IoT technologie Plug and Play Preview](concepts-developer-guide.md)
