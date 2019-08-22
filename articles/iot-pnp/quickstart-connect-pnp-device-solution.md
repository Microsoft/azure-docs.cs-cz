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
ms.openlocfilehash: 246d3eac8f9d8aff6d603ea8686e430ce0f772ea
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878247"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution"></a>Rychlý start: Interakce se zařízením IoT technologie Plug and Play ve verzi Preview, které je připojené k vašemu řešení

IoT technologie Plug and Play Preview zjednodušuje IoT tím, že vám umožní pracovat s funkcemi zařízení bez znalosti základní implementace zařízení. V tomto rychlém startu se dozvíte, jak pomocí Node. js připojit a řídit zařízení IoT technologie Plug and Play, které je připojené k vašemu řešení.

## <a name="prerequisites"></a>Požadavky

Stáhněte a nainstalujte Node. js z [NodeJS.org](https://nodejs.org).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>Příprava služby IoT Hub

K dokončení tohoto rychlého startu budete také potřebovat službu Azure IoT Hub v rámci vašeho předplatného Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Přidejte Microsoft Azure rozšíření IoT pro Azure CLI:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Spuštěním následujícího příkazu vytvořte identitu zařízení ve službě IoT Hub. Nahraďte **YourIoTHubName** a **YourDevice** skutečnými názvy. Pokud nemáte IoT Hub, vytvořte ho podle [těchto pokynů](../iot-hub/iot-hub-create-using-cli.md) :

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id [YourDevice]
```

Spusťte následující příkazy a získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id [YourDevice] --output table
```

Spuštěním následujících příkazů Získejte _připojovací řetězec služby IoT Hub_ pro vaše centrum:

```azurecli-interactive
az iot hub show-connection-string --hub-name [YourIoTHubName] --output table
```

## <a name="connect-your-device"></a>Připojení zařízení

V tomto rychlém startu použijete vzorový senzor prostředí, který je napsaný v Node. js jako zařízení IoT technologie Plug and Play. Následující pokyny ukazují, jak nainstalovat a spustit zařízení:

1. Naklonujte úložiště GitHub:

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. V terminálu přejděte do kořenové složky naklonovaného úložiště, přejděte do složky **/Azure-IoT-Samples-Node/Digital-Twins/QuickStarts/Device** a pak nainstalujte všechny závislosti spuštěním následujícího příkazu:

    ```cmd/sh
    npm install
    ```

1. Konfigurace _připojovacího řetězce zařízení_:

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<your device connection string>
    ```

1. Spusťte ukázku pomocí následujícího příkazu:

    ```cmd/sh
    node sample_device.js
    ```

1. Zobrazí se zpráva oznamující, že zařízení odeslalo telemetrii a její vlastnosti. Zařízení je teď připravené přijímat příkazy a aktualizace vlastností. Nezavírejte tohoto terminálu, budete ho potřebovat později, abyste ověřili, že se ukázky služby také osvědčily.

## <a name="build-the-solution"></a>Sestavení řešení

V tomto rychlém startu použijete k interakci s ukázkovým zařízením ukázkové řešení IoT v Node. js.

1. Otevřete další terminál. Přejděte do složky naklonovaného úložiště a přejděte do složky **/Azure-IoT-Samples-Node/Digital-Twins/QuickStarts/Service** . Všechny závislosti Nainstalujte spuštěním následujícího příkazu:

    ```cmd/sh
    npm install
    ```

1. Konfigurace _připojovacího řetězce centra_:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<your hub connection string>
    ```

### <a name="read-a-property"></a>Číst vlastnost

1. Po připojení zařízení v terminálu se zobrazí následující zpráva:

    ```cmd/sh
    reported state property as online
    ```

1. Otevřete soubor **get_digital_twin. js**. `deviceID` Nahraďte ID zařízení a uložte soubor.

1. Přejděte na terminál, který jste otevřeli pro spuštění ukázky služby, a spusťte následující příkaz:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. Ve výstupu se v rámci součásti _environmentalSensor_ zobrazí stejný stav:

    ```JSON
    reported state property as online
    ```

### <a name="update-a-writable-property"></a>Aktualizovat vlastnost s možností zápisu

1. Otevřete soubor **update_digital_twin_property. js**.

1. Na začátku souboru je k dispozici sada konstant definovaných pomocí zástupných symbolů velkých písmen. Nahraďte **deviceID** skutečným ID zařízení, aktualizujte konstanty následujícími hodnotami a uložte soubor:

    ```javascript
    const componentName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 60;
    ```

1. Přejděte na terminál, který jste otevřeli pro spuštění ukázky služby, a pomocí následujícího příkazu spusťte ukázku:

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. V terminálu vidíte digitální informace, které jsou přidruženy k vašemu zařízení. Najděte komponentu _environmentalSensor_, zobrazí se nová hodnota jasu 60.

    ```json
        "environmentalSensor": {
        "name": "environmentalSensor",
        "properties": {
          "brightness": {
            "reported": {
              "value": 60,
              "desiredState": {
                "code": 200,
                "version": 14,
                "description": "helpful descriptive text"
              }
            },
            "desired": {
              "value": 60
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
    Received an update for brightness: 60
    updated the property
    ```
2. Vraťte se do terminálu _služby_ , spusťte níže uvedený příkaz a potvrďte, že se vlastnost aktualizovala.
    
    ```cmd/sh
    node get_digital_twin.js
    ```
3. Ve výstupu se pod komponentou environmentalSensor zobrazí aktualizovaná hodnota jasu. Poznámka: může chvíli trvat, než zařízení dokončí aktualizaci. Tento krok můžete opakovat, dokud zařízení nezpracovává aktualizaci vlastností.
    
    ```json
      "brightness": {
        "reported": {
          "value": 60,
          }
       }
    ```

### <a name="invoke-a-command"></a>Vyvolání příkazu

1. Otevřete soubor **invoke_command. js**.

1. Na začátku souboru nahraďte `deviceID` skutečným ID zařízení. Aktualizujte konstanty následujícími hodnotami a pak soubor uložte:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    ```

1. Přejděte na terminál, který jste otevřeli pro spuštění ukázky služby. Pomocí následujícího příkazu spusťte ukázku:

    ```cmd/sh
    node invoke_command.js
    ```

1. V terminálu vypadá úspěch jako následující výstup:

    ```cmd/sh
    invoking command blink on component environmentalSensor for device test...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "33e536d3-14f7-4105-88f3-629b9933851c",
      "_response": "helpful response text"
    }
    ```

1. Přejděte do terminálu _zařízení_ , uvidíte, že byl příkaz potvrzen:

    ```cmd/sh
    received command: blink for component: environmentalSensor
    acknowledgement succeeded.
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud budete chtít pokračovat v pozdějších článcích, můžete zachovat prostředky, které jste použili v rámci tohoto rychlého startu. V opačném případě můžete odstranit prostředky, které jste vytvořili pro tento rychlý Start, abyste se vyhnuli dalším poplatkům.

Pokud chcete odstranit centrum a registrované zařízení, proveďte následující kroky pomocí Azure CLI:

```azurecli-interactive
az group delete --name <Your group name>
```

Pokud chcete odstranit jenom zařízení, které jste zaregistrovali ve svém IoT Hub, pomocí Azure CLI proveďte následující kroky:

```azurecli-interactive
az iot hub device-identity delete --hub-name [YourIoTHubName] --device-id [YourDevice]
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste se dozvěděli, jak připojit zařízení IoT technologie Plug and Play k řešení IoT. Další informace o tom, jak vytvořit řešení, které komunikuje s technologie Plug and Play vašich zařízení IoT, najdete tady:

> [!div class="nextstepaction"]
> [Postup: Připojení k zařízení a práce s nimi](howto-develop-solution.md)
