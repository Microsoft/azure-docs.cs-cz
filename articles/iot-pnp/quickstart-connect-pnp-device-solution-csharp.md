---
title: Interakce se zařízením IoT Plug and Play Preview připojeným k vašemu řešení Azure IoT | Dokumenty společnosti Microsoft
description: Pomocí jazyka C# (.NET) se můžete připojit k zařízení IoT Plug and Play Preview, které je připojené k vašemu řešení Azure IoT, a pracovat s ním.
author: dominicbetts
ms.author: dobett
ms.date: 12/30/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 0953f68839217c1c75eb86f8399ce023f3863ab4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76963967"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-c"></a>Úvodní příručka: Interakce se zařízením IoT Plug and Play Preview, které je připojené k vašemu řešení (C#)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT Plug and Play Preview zjednodušuje IoT tím, že umožňuje interakci s funkcemi zařízení bez znalosti základní implementace zařízení. Tento rychlý start ukazuje, jak používat C# (s .NET) pro připojení a řízení zařízení IoT Plug and Play, které je připojené k vašemu řešení.

## <a name="prerequisites"></a>Požadavky

Chcete-li tento rychlý start dokončit, je třeba nainstalovat .NET Core (2.x.x nebo 3.x.x) ve vývojovém počítači. Z [download .NET Core](https://dotnet.microsoft.com/download/dotnet-core/)si můžete stáhnout upřednostňovanou verzi sady .NET Core SDK pro více platforem.

Verzi rozhraní .NET, která je ve vývojovém počítači, můžete ověřit spuštěním následujícího příkazu v místním okně terminálu: 

```cmd/sh
dotnet --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Spusťte následující příkaz, abyste získali _připojovací řetězec centra IoT hub_ pro vaše centrum (poznámka pro pozdější použití):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Spuštění ukázkového zařízení

V tomto rychlém startu použijete ukázkový senzor prostředí, který je napsán v C# jako zařízení IoT Plug and Play. Následující pokyny ukazují, jak nainstalovat a spustit zařízení:

1. Otevřete okno terminálu v adresáři, který si vyberete. Spusťte následující příkaz pro klonování [azure iot ukázky pro úložiště GitHub C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) do tohoto umístění:

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
    ```

1. Toto okno terminálu bude nyní použito jako terminál _vašeho zařízení._ Přejděte do složky vašeho klonovaného úložiště a přejděte do složky **/azure-iot-samples-csharp/digitaltwin/Samples/device/EnvironmentalSensorSample.**

1. Konfigurace _připojovacího řetězce zařízení_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Vytvořte potřebné balíčky a spusťte ukázku pomocí následujícího příkazu:

    ```cmd\sh
        dotnet run
    ```

1. Zobrazí se zprávy, že se zařízení úspěšně zaregistrovalo a čeká na aktualizace z cloudu. To znamená, že zařízení je nyní připraven o příjem příkazů a aktualizace vlastností a začal oodesílání telemetrických dat do rozbočovače. Nezavírejte tento terminál, budete ho později potřebovat k potvrzení, že vzorky služeb také fungovaly.

## <a name="run-the-sample-solution"></a>Spuštění ukázkového roztoku

V tomto rychlém startu použijete ukázkové řešení IoT v C# k interakci s ukázkovým zařízením.

1. Otevřete další okno terminálu (toto bude váš _servisní_ terminál). Přejděte do složky vašeho klonovaného úložiště a přejděte do složky **/azure-iot-samples-csharp/digitaltwin/Samples/service.**

1. Nakonfigurujte _připojovací řetězec a_ _ID centra_ IoT, abyste službě umožnili připojení k oběma těmto:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Čtení vlastnosti

1. Když jste _zařízení_ připojili k jeho terminálu, zosakli jste následující zprávu s uvedením jeho stavu online:

    ```cmd/sh
    Waiting to receive updates from cloud...
    ```

1. Přejděte na _servisní_ terminál a pomocí následujících příkazů spusťte ukázku pro čtení informací o zařízení:

    ```cmd/sh
    cd GetDigitalTwin
    dotnet run
    ```

1. Ve výstupu _terminálu služby_ přejděte na komponentu. `environmentalSensor` Uvidíte, `state` že vlastnost, která se používá k označení, zda je zařízení online, byla hlášena jako _true_:

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

### <a name="update-a-writable-property"></a>Aktualizace zapisovatelné vlastnosti

1. Přejděte na _servisní_ terminál a nastavte následující proměnné, které definují, kterou vlastnost chcete aktualizovat:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set PROPERTY_NAME=brightness
    set PROPERTY_VALUE=42
    ```

1. Ke spuštění ukázky pro aktualizaci vlastnosti použijte následující příkazy:

    ```cmd/sh
    cd ..\UpdateProperty
    dotnet run
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
            "value": true
          }
        }
      }
    }
    ```

1. Přejděte na terminál _zařízení,_ uvidíte, že zařízení obdrželo aktualizaci:

    ```cmd/sh
    Received updates for property 'brightness'
    Desired brightness = '"42"'.
    Reported brightness = ''.
    Version is '2'.
    Sent pending status for brightness property.
    Sent completed status for brightness property.
    ```
2. Vraťte se do _svého servisního_ terminálu a spusťte níže uvedené příkazy, abyste znovu získali informace o zařízení, abyste potvrdili, že vlastnost byla aktualizována.
    
    ```cmd/sh
    cd ..\GetDigitalTwin
    dotnet run
    ```
3. Ve výstupu _terminálu služby,_ pod komponentou, `environmentalSensor` uvidíte, že byla hlášena aktualizovaná hodnota jasu. Poznámka: Dokončení aktualizace může chvíli trvat, než zařízení dokončí. Tento krok můžete opakovat, dokud zařízení skutečně nezpracuje aktualizaci vlastnosti.
    
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

1. Přejděte na _servisní_ terminál a nastavte následující proměnné, které definují, který příkaz chcete vyvolat:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    ```

1. Ke spuštění ukázky pro vyvolání příkazu použijte následující příkazy:

    ```cmd/sh
    cd ..\InvokeCommand
    dotnet run
    ```

1. Výstup v _servisním_ terminálu by měl být potvrzen následujícím potvrzením:

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command blink invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned payload was
    {"description": "abc"}
    Enter any key to finish
    ```

1. Přejděte na terminál _zařízení,_ uvidíte, že příkaz byl potvrzen:

    ```cmd/sh
    Command - blink was invoked from the service
    Data - null
    Request Id - <some ID value>.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak připojit zařízení IoT Plug and Play k řešení IoT. Další informace o tom, jak vytvořit řešení, které spolupracuje s vašimi zařízeními IoT Plug and Play, najdete v tématu:

> [!div class="nextstepaction"]
> [Postup: Připojení k zařízení a interakce se zařízením](howto-develop-solution.md)
