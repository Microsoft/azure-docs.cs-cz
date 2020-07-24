---
title: Interakce se zařízením IoT technologie Plug and Play Preview připojeným k vašemu řešení – C# | Microsoft Docs
description: Pomocí jazyka C# (.NET) se můžete připojit k zařízení IoT technologie Plug and Play Preview, které je připojené k řešení Azure IoT, a pracovat s nimi.
author: dominicbetts
ms.author: dobett
ms.date: 12/30/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 226acdda94eb88825d60e35d48bfdd476ad1339c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044088"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-c"></a>Rychlý Start: interakce se zařízením IoT technologie Plug and Play ve verzi Preview, které je připojené k vašemu řešení (C#)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT technologie Plug and Play Preview zjednodušuje IoT tím, že vám umožní pracovat s funkcemi zařízení bez znalosti základní implementace zařízení. V tomto rychlém startu se dozvíte, jak pomocí jazyka C# (s .NET) připojit a řídit zařízení IoT technologie Plug and Play, které je připojené k vašemu řešení.

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto rychlého startu je potřeba na svém vývojovém počítači nainstalovat .NET Core (2. x. x nebo 3. x. x). Můžete si stáhnout upřednostňovanou verzi .NET Core SDK pro více platforem od [stažení .NET Core](https://dotnet.microsoft.com/download/dotnet-core/).

Verzi rozhraní .NET, která je na vašem vývojovém počítači, můžete ověřit spuštěním následujícího příkazu v místním okně terminálu: 

```cmd/sh
dotnet --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Spuštěním následujícího příkazu Získejte _připojovací řetězec služby IoT Hub_ pro vaše centrum (Poznámka pro pozdější použití):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Spuštění ukázkového zařízení

V tomto rychlém startu použijete vzorový senzor prostředí, který je napsán v jazyce C# jako zařízení IoT technologie Plug and Play. Následující pokyny ukazují, jak nainstalovat a spustit zařízení:

1. Otevřete okno terminálu v adresáři dle vašeho výběru. Spuštěním následujícího příkazu naklonujte [ukázky Azure IoT pro úložiště GitHubu v jazyce C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) do tohoto umístění:

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
    ```

1. Toto okno terminálu se teď použije jako terminál _zařízení_ . Přejděte do složky naklonovaného úložiště a přejděte do složky **/Azure-IoT-Samples-CSharp/digitaltwin/Samples/Device/EnvironmentalSensorSample** .

1. Konfigurace _připojovacího řetězce zařízení_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Sestavte potřebné balíčky a spusťte ukázku pomocí následujícího příkazu:

    ```cmd\sh
        dotnet run
    ```

1. Zobrazí se zpráva oznamující, že se zařízení úspěšně zaregistrovalo a čeká na aktualizace z cloudu. To znamená, že zařízení je nyní připraveno přijímat příkazy a aktualizace vlastností a zahájilo posílání dat telemetrie do centra. Nezavírejte tohoto terminálu, budete ho potřebovat později, abyste ověřili, že se ukázky služby také osvědčily.

## <a name="run-the-sample-solution"></a>Spuštění ukázkového řešení

V tomto rychlém startu použijete k interakci s ukázkovým zařízením ukázkové řešení IoT v jazyce C#.

1. Otevřete další okno terminálu (bude to váš terminál _služby_ ). Přejděte do složky naklonovaného úložiště a přejděte do složky **/Azure-IoT-Samples-CSharp/digitaltwin/Samples/Service** .

1. Nakonfigurujte _připojovací řetězec služby IoT Hub_ a _ID zařízení_ , aby se služba mohla připojit k oběma těmto akcím:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Číst vlastnost

1. Po připojení _zařízení_ v terminálu se zobrazí následující zpráva s informacemi o stavu online:

    ```cmd/sh
    Waiting to receive updates from cloud...
    ```

1. Přejděte do terminálu _služby_ a pomocí následujících příkazů spusťte ukázku pro čtení informací o zařízení:

    ```cmd/sh
    cd GetDigitalTwin
    dotnet run
    ```

1. Ve výstupu terminálu _služby_ se posuňte k `environmentalSensor` součásti. Vidíte, že `state` vlastnost, která určuje, jestli je zařízení online, se ohlásila jako _true_:

    ```JSON
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>Aktualizovat vlastnost s možností zápisu

1. Přejděte do terminálu _služby_ a nastavte následující proměnné pro definování, kterou vlastnost chcete aktualizovat:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set PROPERTY_NAME=brightness
    set PROPERTY_VALUE=42
    ```

1. Pomocí následujících příkazů spusťte ukázku pro aktualizaci vlastnosti:

    ```cmd/sh
    cd ..\UpdateProperty
    dotnet run
    ```

1. Výstup terminálu _služby_ zobrazuje aktualizované informace o zařízení. Posuňte se na `environmentalSensor` součást, abyste viděli novou hodnotu jasu 42.

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
            "value": true
          }
        }
      }
    }
    ```

1. Přejděte do terminálu _zařízení_ , vidíte, že zařízení obdrželo aktualizaci:

    ```cmd/sh
    Received updates for property 'brightness'
    Desired brightness = '"42"'.
    Reported brightness = ''.
    Version is '2'.
    Sent pending status for brightness property.
    Sent completed status for brightness property.
    ```
2. Vraťte se do terminálu _služby_ a spusťte následující příkazy, abyste mohli znovu získat informace o zařízení. Tím ověříte, že se vlastnost aktualizovala.
    
    ```cmd/sh
    cd ..\GetDigitalTwin
    dotnet run
    ```
3. V výstupu terminálu _služby_ se pod `environmentalSensor` komponentou zobrazí aktualizovaná hodnota jasu. Poznámka: může chvíli trvat, než zařízení dokončí aktualizaci. Tento krok můžete opakovat, dokud zařízení nezpracovává aktualizaci vlastností.
    
    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          },
          "reported": {
            "value": "42",
            "desiredState": {
              "code": 200,
              "description": "Request completed",
              "version": 2
            }
          }
        },
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    },
    ```

### <a name="invoke-a-command"></a>Vyvolání příkazu

1. Přejděte do terminálu _služby_ a nastavte následující proměnné pro definování příkazu, který se má vyvolat:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    ```

1. Pomocí následujících příkazů spusťte ukázku pro vyvolání příkazu:

    ```cmd/sh
    cd ..\InvokeCommand
    dotnet run
    ```

1. Výstup v terminálu _služby_ by měl zobrazovat následující potvrzení:

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command blink invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned payload was
    {"description": "abc"}
    Enter any key to finish
    ```

1. Přejděte do terminálu _zařízení_ , uvidíte, že byl příkaz potvrzen:

    ```cmd/sh
    Command - blink was invoked from the service
    Data - null
    Request Id - <some ID value>.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak připojit zařízení IoT technologie Plug and Play k řešení IoT. Další informace o tom, jak vytvořit řešení, které komunikuje s technologie Plug and Play vašich zařízení IoT, najdete tady:

> [!div class="nextstepaction"]
> [Postupy: připojení k zařízení a práce s nimi](howto-develop-solution.md)
