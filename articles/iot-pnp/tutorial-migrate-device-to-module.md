---
title: Připojení obecného modulu IoT technologie Plug and Play Generic Module | Microsoft Docs
description: Použijte vzorový kód zařízení v C# technologie Plug and Play v obecném modulu.
author: ericmitt
ms.author: ericmitt
ms.date: 9/22/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 671809b9cdbe72c8f3091b0056897c2342a38b1f
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089158"
---
# <a name="tutorial-connect-an-iot-plug-and-play-module-c"></a>Kurz: připojení modulu IoT technologie Plug and Play (C#)

V tomto kurzu se dozvíte, jak propojit obecný [modul](../iot-hub/iot-hub-devguide-module-twins.md)IoT technologie Plug and Play.

Zařízení je technologie Plug and Play IoT, pokud při připojení ke službě IoT Hub zveřejňuje své ID modelu a implementuje vlastnosti a metody popsané v modelu DTDL (Digital Data Definition Language), který identifikuje ID modelu. Další informace o tom, jak zařízení používají DTDL a ID modelu, najdete v tématu [Příručka pro vývojáře IoT technologie Plug and Play](./concepts-developer-guide-device-csharp.md). Moduly používají identifikátory modelu a modely DTDL stejným způsobem.

V tomto kurzu se dozvíte, jak implementovat modul IoT technologie Plug and Play, v tomto kurzu se dozvíte, jak převést vzorek termostatu pro zařízení C# na obecný modul.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Pro dokončení tohoto kurzu ve Windows nainstalujte do svého místního prostředí Windows následující software:

* [Visual Studio (komunita, Professional nebo Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

Pomocí nástroje Azure IoT Explorer přidejte do služby IoT Hub nové zařízení s názvem **Moje modul – zařízení** .

Přidejte modul s názvem **My-Module** do **složky Moje modul – zařízení**:

1. V nástroji Azure IoT Explorer přejděte do zařízení **Moje modul – zařízení** .

1. Vyberte možnost **Identita modulu**a pak vyberte **+ Přidat**.

1. Jako název identity modulu zadejte **Moje modul** a vyberte **Uložit**.

1. V seznamu identit modulů vyberte **Moje modul**. Pak zkopírujte primární připojovací řetězec. Tento připojovací řetězec modulu použijete později v tomto kurzu.

1. Vyberte kartu **nevlákenný modul** a Všimněte si, že neexistují žádné požadované ani hlášené vlastnosti:

    ```json
    {
      "deviceId": "my-module-device",
      "moduleId": "my-module",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "NjgzMzQ1MzQ1",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00Z",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00Z",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "modelId": "",
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "0001-01-01T00:00:00Z"
          },
          "$version": 1
          },
          "reported": {
            "$metadata": {
              "$lastUpdated": "0001-01-01T00:00:00Z"
          },
          "$version": 1
        }
      }
    }
    ```

## <a name="download-the-code"></a>Stáhněte si kód

Pokud jste to ještě neudělali, naklonujte úložiště GitHub sady Azure IoT Hub Device C# SDK do místního počítače:

Otevřete příkazový řádek ve složce podle vašeho výběru. Pomocí následujícího příkazu naklonujte úložiště GitHub [ukázek Azure IoT C#](https://github.com/Azure-Samples/azure-iot-samples-csharp) do tohoto umístění:

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="prepare-the-project"></a>Příprava projektu

Otevření a příprava ukázkového projektu:

1. Otevřete soubor projektu *Azure-IoT-SDK-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* v aplikaci Visual Studio 2019.

1. V aplikaci Visual Studio přejděte na **Vlastnosti projektu > termostatu > ladit**. Pak do projektu přidejte následující proměnné prostředí:

    | Name | Hodnota |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | připojovací řetězec |
    | IOTHUB_MODULE_CONNECTION_STRING | Připojovací řetězec modulu, na který jste si poznamenali předchozí poznámku |

    Další informace o ukázkové konfiguraci najdete v [ukázkovém souboru Readme](https://github.com/Azure-Samples/azure-iot-samples-csharp/blob/master/iot-hub/Samples/device/PnpDeviceSamples/readme.md).

## <a name="modify-the-code"></a>Upraví kód.

Úprava kódu tak, aby fungoval jako modul místo zařízení:

1. V sadě Visual Studio otevřete *Parameter.cs* a upravte řádek, který nastaví proměnnou **PrimaryConnectionString** , následovně:

    ```csharp
    public string PrimaryConnectionString { get; set; } = Environment.GetEnvironmentVariable("IOTHUB_MODULE_CONNECTION_STRING");
    ```

1. V aplikaci Visual Studio otevřete *program.cs* a nahraďte sedm instancí `DeviceClient` třídy `ModuleClient` třídou.

    > [!TIP]
    > Použijte funkci hledání a nahrazení sady Visual Studio s **písmenem Match** a **Porovnejte celé slovo** s povolenou možností nahradit `DeviceClient` `ModuleClient` .

1. V aplikaci Visual Studio otevřete *Thermostat.cs* a nahraďte obě instance třídy třídou následujícím `DeviceClient` `ModuleClient` způsobem.

1. Uložte změny v souborech, které jste změnili.

Pokud kód spustíte a pak pomocí Průzkumníka Azure IoT zobrazíte aktualizovaný modul s dvojitou ochranou, zobrazí se v něm aktualizované zařízení s ID modelu a hlášenou vlastností Module:

```json
{
  "deviceId": "my-module-device",
  "moduleId": "my-mod",
  "etag": "AAAAAAAAAAE=",
  "deviceEtag": "NjgzMzQ1MzQ1",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Connected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "modelId": "dtmi:com:example:Thermostat;1",
  "version": 3,
  "properties": {
    "desired": {
      "$metadata": {
        "$lastUpdated": "0001-01-01T00:00:00Z"
      },
      "$version": 1
    },
    "reported": {
      "maxTempSinceLastReboot": 5,
      "$metadata": {
        "$lastUpdated": "2020-09-28T08:53:45.9956637Z",
        "maxTempSinceLastReboot": {
          "$lastUpdated": "2020-09-28T08:53:45.9956637Z"
        }
      },
      "$version": 2
    }
  }
}
```

## <a name="interact-with-a-device-module"></a>Interakce s modulem zařízení

Sady SDK služby umožňují načíst ID modelu propojených technologie Plug and Play zařízení a modulů IoT. Sady SDK služeb můžete použít k nastavení vlastností s možností zápisu a příkazů pro volání:

1. V jiné instanci aplikace Visual Studio otevřete projekt *Azure-IoT-SDK-csharp\iot-hub\Samples\service\PnpServiceSamples\Thermostat\Thermostat.csproj* .

1. V aplikaci Visual Studio přejděte na **Vlastnosti projektu > termostatu > ladit**. Pak do projektu přidejte následující proměnné prostředí:

    | Name | Hodnota |
    | ---- | ----- |
    | IOTHUB_DEVICE_ID | můj modul – zařízení |
    | IOTHUB_CONNECTION_STRING | Hodnota, na kterou jste si poznamenali, když jste [nastavili prostředí](set-up-environment.md) |

    > [!TIP]
    > Připojovací řetězec služby IoT Hub můžete najít také v nástroji Azure IoT Explorer.

1. Otevřete soubor *program.cs* a upravte řádek, který volá příkaz, jak je znázorněno níže:

    ```csharp
    CloudToDeviceMethodResult result = await s_serviceClient.InvokeDeviceMethodAsync(s_deviceId, "my-module", commandInvocation);
    ```

1. V souboru *program.cs* upravte řádek, který načte zařízení, a to následujícím způsobem:

    ```csharp
    Twin twin = await s_registryManager.GetTwinAsync(s_deviceId, "my-module");
    ```

1. Ujistěte se, že je ukázka klienta modulu stále spuštěná, a potom spusťte ukázku této služby. Výstup v ukázce služby zobrazuje ID modelu z vlákna zařízení a volání příkazu:

    ```cmd
    [09/28/2020 10:52:55]dbug: Thermostat.Program[0]
      Initialize the service client.
    [09/28/2020 10:52:55]dbug: Thermostat.Program[0]
      Get Twin model Id and Update Twin
    [09/28/2020 10:52:59]dbug: Thermostat.Program[0]
      Model Id of this Twin is: dtmi:com:example:Thermostat;1
    [09/28/2020 10:52:59]dbug: Thermostat.Program[0]
      Invoke a command
    [09/28/2020 10:53:00]dbug: Thermostat.Program[0]
      Command getMaxMinReport invocation result status is: 200
    ```

    Výstup z klienta modulu zobrazuje odpověď obslužné rutiny příkazu:

    ```cmd
    [09/28/2020 10:53:00]dbug: Thermostat.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 28/09/2020 10:52:55.
    [09/28/2020 10:53:00]dbug: Thermostat.ThermostatSample[0]
      Command: MaxMinReport since 28/09/2020 10:52:55: maxTemp=25.4, minTemp=25.4, avgTemp=25.4, startTime=28/09/2020 10:52:56, endTime=28/09/2020 10:52:56
    ```

## <a name="convert-to-an-iot-edge-module"></a>Převést na modul IoT Edge

Pokud chcete tuto ukázku převést tak, aby fungovala jako IoT Edge modulu IoT technologie Plug and Play, musíte aplikaci kontejnerizace. Nemusíte dělat žádné další změny kódu. Proměnná prostředí připojovacího řetězce je vložena modulem runtime IoT Edge při spuštění. Další informace najdete v tématu [použití sady Visual Studio 2019 k vývoji a ladění modulů pro Azure IoT Edge](../iot-edge/how-to-visual-studio-develop-module.md).

Informace o tom, jak nasadit kontejnerový modul, najdete v těchto tématech:

* [Spusťte Azure IoT Edge v Virtual Machines Ubuntu](../iot-edge/how-to-install-iot-edge-ubuntuvm.md).
* [Nainstalujte modul runtime Azure IoT Edge v systémech Linux založených na Debian](../iot-edge/how-to-install-iot-edge.md).

Pomocí nástroje Azure IoT Explorer můžete zobrazit:

* ID modelu vašeho zařízení IoT Edge v modulu je nevlákenované.
* Telemetrie ze zařízení IoT Edge.
* Aktualizace vlastností IoT Edge v modulu, které spouštějí oznámení technologie Plug and Play IoT
* IoT Edge modul reaguje na příkazy technologie Plug and Play IoT.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili připojit zařízení IoT technologie Plug and Play k modulům ke službě IoT Hub. Další informace o modelech zařízení IoT technologie Plug and Play najdete v tématech:

> [!div class="nextstepaction"]
> [Příručka pro vývojáře IoT technologie Plug and Play Modeling](./concepts-developer-guide-device-csharp.md)