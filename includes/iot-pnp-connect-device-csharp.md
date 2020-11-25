---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 13d0bdf82052ff2c61c5b2c6010956c8fb27574d
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96122460"
---
V tomto rychlém startu se dozvíte, jak vytvořit ukázkovou aplikaci IoT technologie Plug and Play zařízení, jak ji připojit k centru IoT a použít nástroj Azure IoT Explorer k zobrazení telemetrie, kterou posílá. Ukázková aplikace je napsaná v jazyce C# a je součástí ukázek Azure IoT pro C#. Tvůrce řešení může pomocí nástroje Azure IoT Explorer pochopit možnosti zařízení technologie Plug and Play IoT, aniž by bylo nutné zobrazovat kód zařízení.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

K dokončení tohoto rychlého startu ve Windows potřebujete na svém vývojovém počítači nainstalovaný následující software:

* [Visual Studio (komunita, Professional nebo Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

## <a name="download-the-code"></a>Stáhněte si kód

V tomto rychlém startu připravíte vývojové prostředí, které můžete použít k klonování a sestavení ukázek Azure IoT pro úložiště v jazyce C#.

Otevřete příkazový řádek ve složce podle vašeho výběru. Spuštěním následujícího příkazu naklonujte [Microsoft Azurech ukázek IoT pro úložiště GitHub v jazyce C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) do tohoto umístění:

```cmd
git clone  https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="build-the-code"></a>Sestavení kódu

Nyní můžete vytvořit ukázku v aplikaci Visual Studio a spustit ji v režimu ladění.

1. Otevřete soubor projektu *Azure-IoT-Samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* v aplikaci Visual Studio 2019.

1. V aplikaci Visual Studio přejděte na **Vlastnosti projektu > termostatu > ladit**. Pak do projektu přidejte následující proměnné prostředí:

    | Název | Hodnota |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | Hodnota, na kterou jste si poznamenali, když jste [nastavili prostředí](../articles/iot-pnp/set-up-environment.md) |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | Moje zařízení – PnP-Device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | Hodnota, na kterou jste si poznamenali, když jste [nastavili prostředí](../articles/iot-pnp/set-up-environment.md) |

Nyní můžete vytvořit ukázku v aplikaci Visual Studio a spustit ji v režimu ladění.

## <a name="run-the-device-sample"></a>Spuštění ukázky zařízení

Chcete-li trasovat provádění kódu v aplikaci Visual Studio ve Windows, přidejte do `main` funkce v souboru program.cs bod přerušení.

Zařízení je teď připravené přijmout příkazy a aktualizace vlastností a zahájilo posílání dat telemetrie do centra. Ponechte ukázku spuštěnou při dokončení dalších kroků.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Použití Azure IoT Exploreru k ověření kódu

Po spuštění ukázky klienta zařízení použijte nástroj Azure IoT Explorer a ověřte, jestli funguje.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Kontrola kódu

Tato ukázka implementuje jednoduché zařízení IoT technologie Plug and Play termostat. Model, který tento ukázkový implementuje, nepoužívá [komponenty](../articles/iot-pnp/concepts-components.md)IoT technologie Plug and Play. [Soubor modelu DTDL (Digital Real Definition Language) pro termostatické zařízení](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) definuje telemetrii, vlastnosti a příkazy, které zařízení implementuje.

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
