---
title: Interakce se zařízením IoT Plug and Play Preview připojeným k vašemu řešení Azure IoT | Dokumenty společnosti Microsoft
description: Node.js slouží k připojení a interakci se zařízením IoT Plug and Play Preview, které je připojené k vašemu řešení Azure IoT.
author: miagdp
ms.author: miag
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 9962763e647faddc5a2179f304aeb3fa8ca256e8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75550736"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>Úvodní příručka: Interakce se zařízením IoT Plug and Play Preview, které je připojené k vašemu řešení (Node.js)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT Plug and Play Preview zjednodušuje IoT tím, že umožňuje interakci s funkcemi zařízení bez znalosti základní implementace zařízení. Tento rychlý start ukazuje, jak používat Node.js pro připojení a ovládání zařízení IoT Plug and Play, které je připojené k vašemu řešení.

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit tento rychlý start, potřebujete node.js ve vývojovém počítači. Nejnovější doporučenou verzi pro více platforem si můžete stáhnout z [nodejs.org](https://nodejs.org).

Aktuální verzi Node.js na počítači používaném pro vývoj můžete ověřit pomocí následujícího příkazu:

```cmd/sh
node --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Spusťte následující příkaz, abyste získali _připojovací řetězec centra IoT hub_ pro vaše centrum (poznámka pro pozdější použití):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Spuštění ukázkového zařízení

V tomto rychlém startu použijete ukázkový senzor prostředí, který je napsán v souboru Node.js jako zařízení IoT Plug and Play. Následující pokyny ukazují, jak nainstalovat a spustit zařízení:

1. Otevřete okno terminálu v adresáři, který si vyberete. Spusťte následující příkaz pro klonování [ukázek Azure IoT pro úložiště Node.js](https://github.com/azure-samples/azure-iot-samples-node) GitHub do tohoto umístění:

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. Toto okno terminálu bude nyní použito jako terminál _vašeho zařízení._ Přejděte do složky vašeho klonovaného úložiště a přejděte do složky **/azure-iot-samples-node/digital-twins/Quickstarts/Device.** Nainstalujte všechny závislosti spuštěním následujícího příkazu:

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

1. Zobrazí se zprávy, že zařízení odeslalo některé informace a nahlásilo se online. To znamená, že zařízení začalo odesílat telemetrická data do centra a je nyní připraveno přijímat příkazy a aktualizace vlastností. Nezavírejte tento terminál, budete ho později potřebovat k potvrzení, že vzorky služeb také fungovaly.

## <a name="run-the-sample-solution"></a>Spuštění ukázkového roztoku

V tomto rychlém startu použijete ukázkové řešení IoT v souboru Node.js k interakci se ukázkovým zařízením.

1. Otevřete další okno terminálu (toto bude váš _servisní_ terminál). Přejděte do složky vašeho klonovaného úložiště a přejděte do složky **/azure-iot-samples-node/digital-twins/Quickstarts/Service.** Nainstalujte všechny závislosti spuštěním následujícího příkazu:

    ```cmd/sh
    npm install
    ```

1. Nakonfigurujte _připojovací řetězec služby IoT hub_ tak, aby se k němu služba mohla připojit:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    ```

### <a name="read-a-property"></a>Čtení vlastnosti

1. Když jste _zařízení_ připojili k jeho terminálu, zosakli jste následující zprávu s uvedením jeho stavu online:

    ```cmd/sh
    reported state property as online
    ```

1. Ve složce **/azure-iot-samples-node/digital-twins/Quickstarts/Service** otevřete soubor **get_digital_twin.js**. Nahraďte `<DEVICE_ID_GOES_HERE>` zástupný symbol ID zařízení a soubor uložte.

1. Přejděte na _servisní_ terminál a pomocí následujícího příkazu spusťte ukázku pro čtení informací o zařízení:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. Ve výstupu _terminálu služby_ přejděte na komponentu. `environmentalSensor` Vidíte, že `state` vlastnost byla hlášena jako _online_:

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

### <a name="update-a-writable-property"></a>Aktualizace zapisovatelné vlastnosti

1. Otevřete soubor **update_digital_twin_property.js**.

1. Na začátku souboru je sada konstant definované s velkými zástupnými symboly. Nahraďte `<DEVICE_ID_GOES_HERE>` zástupný symbol skutečným ID zařízení, aktualizujte zbývající konstanty následujícími hodnotami a soubor uložte:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 42;
    ```

1. Přejděte na _terminál služby_ a pomocí následujícího příkazu spusťte ukázku pro aktualizaci vlastnosti:

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. Výstup _terminálu služby_ zobrazuje aktualizované informace o zařízení. Přejděte `environmentalSensor` na komponentu a zobcete novou hodnotu jasu 42.

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

1. Přejděte na terminál _zařízení,_ uvidíte, že zařízení obdrželo aktualizaci:

    ```cmd/sh
    Received an update for brightness: 42
    updated the property
    ```
2. Vraťte se do _svého servisního_ terminálu a spusťte níže uvedený příkaz, abyste znovu získali informace o zařízení, abyste potvrdili, že vlastnost byla aktualizována.
    
    ```cmd/sh
    node get_digital_twin.js
    ```

3. Ve výstupu _terminálu služby,_ pod komponentou, `environmentalSensor` uvidíte, že byla hlášena aktualizovaná hodnota jasu. Poznámka: Dokončení aktualizace může chvíli trvat, než zařízení dokončí. Tento krok můžete opakovat, dokud zařízení skutečně nezpracuje aktualizaci vlastnosti.
    
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

1. Otevřete soubor **invoke_command.js**.

1. Na začátku souboru nahraďte `<DEVICE_ID_GOES_HERE>` zástupný symbol skutečným ID zařízení. Aktualizujte zbývající konstanty následujícími hodnotami a pak soubor uložte:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    const commandArgument = '<For the environmental sensor, this value does not matter. Any string will do.>'; 
    ```

1. Přejděte na _servisní_ terminál. Ke spuštění ukázky pro vyvolání příkazu použijte následující příkaz:

    ```cmd/sh
    node invoke_command.js
    ```

1. Výstup v _servisním_ terminálu by měl být potvrzen následujícím potvrzením:

    ```cmd/sh
    invoking command blink on interface instanceenvironmentalSensor for device <device ID>...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "<some ID value>",
      "_response": "helpful response text"
    }
    ```

1. Přejděte na terminál _zařízení,_ uvidíte, že příkaz byl potvrzen:

    ```cmd/sh
    received command: blink for interfaceInstance: environmentalSensor
    acknowledgement succeeded.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak připojit zařízení IoT Plug and Play k řešení IoT. Další informace o tom, jak vytvořit řešení, které spolupracuje s vašimi zařízeními IoT Plug and Play, najdete v tématu:

> [!div class="nextstepaction"]
> [Postup: Připojení k zařízení a interakce se zařízením](howto-develop-solution.md)
