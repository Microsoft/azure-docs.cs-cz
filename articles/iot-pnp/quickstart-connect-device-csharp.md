---
title: Připojte si ukázkový kód zařízení v jazyce C# technologie Plug and Play k IoT Hub | Microsoft Docs
description: Sestavte a spouštějte ukázkový kód zařízení technologie Plug and Play IoT v systému Windows, který se připojuje ke centru IoT. K zobrazení informací odesílaných zařízením do centra použijte nástroj Azure IoT Explorer.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d1deac1c7932a8f3cec06d9c264ba401f7f1341d
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91577029"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-device-application-running-on-windows-to-iot-hub-c"></a>Rychlý Start: připojení ukázkové aplikace IoT technologie Plug and Play zařízení běžící v systému Windows k IoT Hub (C#)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

V tomto rychlém startu se dozvíte, jak vytvořit ukázkovou aplikaci IoT technologie Plug and Play zařízení, jak ji připojit k centru IoT a použít nástroj Azure IoT Explorer k zobrazení telemetrie, kterou posílá. Ukázková aplikace je napsaná v CSharp a je obsažená v sadě SDK pro zařízení Azure IoT pro jazyk C#. Tvůrce řešení může pomocí nástroje Azure IoT Explorer pochopit možnosti zařízení technologie Plug and Play IoT, aniž by bylo nutné zobrazovat kód zařízení.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

K dokončení tohoto rychlého startu ve Windows potřebujete na svém vývojovém počítači nainstalovaný následující software:

* [Visual Studio (komunita, Professional nebo Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

## <a name="download-the-code"></a>Stáhněte si kód

V tomto rychlém startu připravíte vývojové prostředí, které můžete použít k klonování a sestavení sady SDK pro Azure IoT Hub zařízení v C#.

Otevřete příkazový řádek ve složce podle vašeho výběru. Spuštěním následujícího příkazu naklonujte [Microsoft Azure ukázky IoT pro](https://github.com/Azure-Samples/azure-iot-samples-csharp) úložiště GitHubu .NET do tohoto umístění:

```cmd
git clone  https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="build-the-code"></a>Sestavení kódu

Nyní můžete vytvořit ukázku v aplikaci Visual Studio a spustit ji v režimu ladění.

1. Otevřete soubor projektu *Azure-IoT-Samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* v aplikaci Visual Studio 2019.

1. V aplikaci Visual Studio přejděte na **Vlastnosti projektu > termostatu > ladit**. Pak do projektu přidejte následující proměnné prostředí:

    | Name | Hodnota |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | Hodnota, na kterou jste si poznamenali, když jste [nastavili prostředí](set-up-environment.md) |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | Moje zařízení – PnP-Device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | Hodnota, na kterou jste si poznamenali, když jste [nastavili prostředí](set-up-environment.md) |

Nyní můžete vytvořit ukázku v aplikaci Visual Studio a spustit ji v režimu ladění.

## <a name="run-the-device-sample"></a>Spuštění ukázky zařízení

Chcete-li trasovat provádění kódu v aplikaci Visual Studio ve Windows, přidejte do `main` funkce v souboru program.cs bod přerušení.

Zařízení je teď připravené přijmout příkazy a aktualizace vlastností a zahájilo posílání dat telemetrie do centra. Ponechte ukázku spuštěnou při dokončení dalších kroků.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Použití Azure IoT Exploreru k ověření kódu

Po spuštění ukázky klienta zařízení použijte nástroj Azure IoT Explorer a ověřte, jestli funguje.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Kontrola kódu

Tato ukázka implementuje jednoduché zařízení IoT technologie Plug and Play termostat. Model, který tento ukázkový implementuje, nepoužívá [komponenty](concepts-components.md)IoT technologie Plug and Play. [Soubor modelu DTDL (Digital Real Definition Language) pro termostatické zařízení](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) definuje telemetrii, vlastnosti a příkazy, které zařízení implementuje.

Kód zařízení se připojí ke službě IoT Hub pomocí metody standard `CreateFromConnectionString` . Zařízení odešle ID modelu DTDL modelu, který implementuje v žádosti o připojení. Zařízení, které odesílá ID modelu, je zařízení IoT technologie Plug and Play:

```csharp
private static void InitializeDeviceClientAsync()
{
  var options = new ClientOptions
  {
    ModelId = ModelId,
  };
  s_deviceClient = DeviceClient.CreateFromConnectionString(s_deviceConnectionString, TransportType.Mqtt, options);
  s_deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
  {
     s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
  });
}
```

ID modelu je uloženo v kódu, jak je znázorněno v následujícím fragmentu kódu:

```csharp
private const string ModelId = "dtmi:com:example:Thermostat;1";
```

Kód, který aktualizuje vlastnosti, zpracovává příkazy a odesílá telemetrii, je stejný jako kód pro zařízení, které nepoužívá konvence technologie Plug and Play IoT.

Ukázka používá knihovnu JSON k analýze objektů JSON v datových vytíženích odeslaných ze služby IoT Hub:

```csharp
using Newtonsoft.Json;

...

DateTime since = JsonConvert.DeserializeObject<DateTime>(request.DataAsJson);
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili připojit zařízení IoT technologie Plug and Play ke službě IoT Hub. Další informace o tom, jak vytvořit řešení, které komunikuje s technologie Plug and Play vašich zařízení IoT, najdete tady:

> [!div class="nextstepaction"]
> [Postupy: připojení k zařízení a práce s nimi](howto-develop-solution.md)
