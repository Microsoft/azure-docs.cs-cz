---
title: Interakce se zařízením IoT technologie Plug and Play Preview připojeným k řešení Azure IoT | Microsoft Docs
description: Pomocí Node. js se můžete připojit k zařízení IoT technologie Plug and Play Preview, které je připojené k řešení Azure IoT, a pracovat s nimi.
author: miagdp
ms.author: miag
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 3275c01059a61e4eb8591948695656f4e4b722ed
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152120"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>Rychlý Start: interakce se zařízením IoT technologie Plug and Play ve verzi Preview, které je připojené k vašemu řešení (Node. js)

IoT technologie Plug and Play Preview zjednodušuje IoT tím, že vám umožní pracovat s funkcemi zařízení bez znalosti základní implementace zařízení. V tomto rychlém startu se dozvíte, jak pomocí Node. js připojit a řídit zařízení IoT technologie Plug and Play, které je připojené k vašemu řešení.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu potřebujete Node. js na svém vývojovém počítači. Nejnovější doporučenou verzi si můžete stáhnout z [NodeJS.org](https://nodejs.org)na víc platforem.

Aktuální verzi Node.js na počítači používaném pro vývoj můžete ověřit pomocí následujícího příkazu:

```cmd/sh
node --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub-windows.md](../../includes/iot-pnp-prepare-iot-hub-windows.md)]

## <a name="connect-your-device"></a>Připojení zařízení

V tomto rychlém startu použijete vzorový senzor prostředí, který je napsaný v Node. js jako zařízení IoT technologie Plug and Play. Následující pokyny ukazují, jak nainstalovat a spustit zařízení:

1. Otevřete okno terminálu v adresáři dle vašeho výběru. Spuštěním následujícího příkazu naklonujte [ukázky Azure IoT pro úložiště GitHub Node. js](https://github.com/azure-samples/azure-iot-samples-node) do tohoto umístění:

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. Toto okno terminálu se teď použije jako terminál _zařízení_ . Přejděte do klonovaného úložiště a přejděte do složky **/Azure-IoT-Samples-Node/Digital-Twins/QuickStarts/Device** . Všechny závislosti Nainstalujte spuštěním následujícího příkazu:

    ```cmd/sh
    npm install
    ```

1. Konfigurace _připojovacího řetězce zařízení_:

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Spusťte ukázku pomocí následujícího příkazu:

    ```cmd/sh
    node sample_device.js
    ```

1. Zobrazí se zpráva oznamující, že zařízení odeslalo nějaké informace a nahlásilo je jako online. To znamená, že zařízení začalo posílat data telemetrie do centra a teď je připravené přijímat příkazy a aktualizace vlastností. Nezavírejte tohoto terminálu, budete ho potřebovat později, abyste ověřili, že se ukázky služby také osvědčily.

## <a name="build-the-solution"></a>Sestavení řešení

V tomto rychlém startu použijete k interakci s ukázkovým zařízením ukázkové řešení IoT v Node. js.

1. Otevřete další okno terminálu (bude to váš terminál _služby_ ). Přejděte do složky naklonovaného úložiště a přejděte do složky **/Azure-IoT-Samples-Node/Digital-Twins/QuickStarts/Service** . Všechny závislosti Nainstalujte spuštěním následujícího příkazu:

    ```cmd/sh
    npm install
    ```

1. Konfigurace _připojovacího řetězce služby IoT Hub_ , aby se služba mohla připojit k tomuto:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    ```

### <a name="read-a-property"></a>Číst vlastnost

1. Po připojení _zařízení_ v terminálu se zobrazí následující zpráva s informacemi o stavu online:

    ```cmd/sh
    reported state property as online
    ```

1. Ve složce **/Azure-IoT-Samples-Node/Digital-Twins/QuickStarts/Service** otevřete soubor **get_digital_twin. js**. Zástupný symbol `<DEVICE_ID_GOES_HERE>` nahraďte svým ID zařízení a uložte soubor.

1. Přejděte do terminálu _služby_ a pomocí následujícího příkazu spusťte ukázku pro čtení informací o zařízení:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. Ve výstupu terminálu _služby_ přejděte na součást `environmentalSensor`. Vidíte, že vlastnost `state` byla hlášena jako _online_:

    ```JSON
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>Aktualizovat vlastnost s možností zápisu

1. Otevřete soubor **update_digital_twin_property. js**.

1. Na začátku souboru je k dispozici sada konstant definovaných pomocí zástupných symbolů velkých písmen. Nahraďte zástupný text `<DEVICE_ID_GOES_HERE>` skutečným ID zařízení, aktualizujte zbývající konstanty následujícími hodnotami a uložte soubor:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 42;
    ```

1. Přejděte do terminálu _služby_ a pomocí následujícího příkazu spusťte ukázku pro aktualizaci vlastnosti:

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. Výstup terminálu _služby_ zobrazuje aktualizované informace o zařízení. Posuňte se na součást `environmentalSensor`, abyste viděli novou hodnotu jasu 42.

    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

1. Přejděte do terminálu _zařízení_ , vidíte, že zařízení obdrželo aktualizaci:

    ```cmd/sh
    Received an update for brightness: 42
    updated the property
    ```
2. Vraťte se do terminálu _služby_ a spuštěním následujícího příkazu znovu Získejte informace o zařízení, aby se ověřilo, že se vlastnost aktualizovala.
    
    ```cmd/sh
    node get_digital_twin.js
    ```

3. V výstupu terminálu _služby_ se pod komponentou `environmentalSensor` zobrazí aktualizovaná hodnota jasu. Poznámka: může chvíli trvat, než zařízení dokončí aktualizaci. Tento krok můžete opakovat, dokud zařízení nezpracovává aktualizaci vlastností.
    
    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "reported": {
            "value": "42",
            "desiredState": {
              "code": 200,
              "version": 2,
              "description": "helpful descriptive text"
            }
          },
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

### <a name="invoke-a-command"></a>Vyvolání příkazu

1. Otevřete soubor **invoke_command. js**.

1. Na začátku souboru nahraďte zástupný text `<DEVICE_ID_GOES_HERE>` skutečným ID zařízení. Aktualizujte zbývající konstanty následujícími hodnotami a pak soubor uložte:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    const commandArgument = '<For the environmental sensor, this value does not matter. Any string will do.>'; 
    ```

1. Přejděte do terminálu _služby_ . Pomocí následujícího příkazu spusťte ukázku pro vyvolání příkazu:

    ```cmd/sh
    node invoke_command.js
    ```

1. Výstup v terminálu _služby_ by měl zobrazovat následující potvrzení:

    ```cmd/sh
    invoking command blink on interface instanceenvironmentalSensor for device <device ID>...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "<some ID value>",
      "_response": "helpful response text"
    }
    ```

1. Přejděte do terminálu _zařízení_ , uvidíte, že byl příkaz potvrzen:

    ```cmd/sh
    received command: blink for interfaceInstance: environmentalSensor
    acknowledgement succeeded.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak připojit zařízení IoT technologie Plug and Play k řešení IoT. Další informace o tom, jak vytvořit řešení, které komunikuje s technologie Plug and Play vašich zařízení IoT, najdete tady:

> [!div class="nextstepaction"]
> [Postupy: připojení k zařízení a práce s nimi](howto-develop-solution.md)
