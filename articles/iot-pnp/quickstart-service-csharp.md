---
title: Interakce se zařízením IoT technologie Plug and Play připojeným k řešení Azure IoT (C#) | Microsoft Docs
description: Pomocí jazyka C# se můžete připojit k zařízení IoT technologie Plug and Play, které je připojené k řešení Azure IoT, a pracovat s nimi.
author: ericmitt
ms.author: ericmitt
ms.date: 09/21/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 688541147a9a765ed94d8e6c18ce4ee63615f627
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91583555"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-device-thats-connected-to-your-solution-c"></a>Rychlý Start: interakce se zařízením IoT technologie Plug and Play, které je připojené k vašemu řešení (C#)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

IoT technologie Plug and Play zjednodušuje IoT tím, že vám umožní pracovat s funkcemi zařízení bez znalosti základní implementace zařízení. V tomto rychlém startu se dozvíte, jak pomocí jazyka C# připojit a řídit zařízení IoT technologie Plug and Play, které je připojené k vašemu řešení.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

K dokončení tohoto rychlého startu ve Windows potřebujete na svém vývojovém počítači nainstalovaný následující software:

* [Visual Studio (komunita, Professional nebo Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Naklonujte úložiště sady SDK pomocí ukázkového kódu.

Pokud jste dokončili [rychlý Start: Připojte ukázkovou aplikaci IoT technologie Plug and Play zařízení běžící v systému Windows k IoT Hub (C#)](quickstart-connect-device-csharp.md), již jste naklonoval úložiště.

Naklonujte ukázky ze sady Microsoft Azure IoT SDK pro úložiště GitHub .NET. Otevřete příkazový řádek ve složce podle vašeho výběru. Spuštěním následujícího příkazu naklonujte [Microsoft Azure ukázky IoT pro](https://github.com/Azure-Samples/azure-iot-samples-csharp) úložiště GitHubu .NET:

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="run-the-sample-device"></a>Spuštění ukázkového zařízení

V tomto rychlém startu použijete ukázkové termostatické zařízení, které je napsané v jazyce C# jako zařízení IoT technologie Plug and Play. Spuštění ukázkového zařízení:

1. Otevřete soubor projektu *Azure-IoT-Samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* v aplikaci Visual Studio 2019.

1. V aplikaci Visual Studio přejděte na **Vlastnosti projektu > termostatu > ladit**. Pak do projektu přidejte následující proměnné prostředí:

    | Název | Hodnota |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | Hodnota, na kterou jste si poznamenali, když jste [nastavili prostředí](set-up-environment.md) |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | Moje zařízení – PnP-Device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | Hodnota, na kterou jste si poznamenali, když jste [nastavili prostředí](set-up-environment.md) |


1. Nyní můžete vytvořit ukázku v aplikaci Visual Studio a spustit ji v režimu ladění.

1. Zobrazí se zpráva oznamující, že zařízení odeslalo nějaké informace a nahlásilo je jako online. Tyto zprávy signalizují, že zařízení začalo posílat data telemetrie do centra a teď je připravené přijímat příkazy a aktualizace vlastností. Tuto instanci sady Visual Studio nezavírejte, budete ji potřebovat k potvrzení, že ukázka služby funguje.

## <a name="run-the-sample-solution"></a>Spuštění ukázkového řešení

V [části nastavení prostředí pro iot technologie Plug and Play rychlé starty a kurzy](set-up-environment.md) , které jste vytvořili dvě proměnné prostředí pro konfiguraci ukázky pro připojení ke službě IoT Hub a zařízení:

* **IOTHUB_CONNECTION_STRING**: připojovací řetězec ke službě IoT Hub jste si poznamenali dříve.
* **DEVICE_ID**: `"my-pnp-device"` .

V tomto rychlém startu použijete ukázkové řešení IoT v jazyce C# k interakci s ukázkovým zařízením, které jste právě nastavili.

1. V jiné instanci aplikace Visual Studio otevřete projekt *Azure-IoT-Samples-csharp\iot-hub\Samples\service\PnpServiceSamples\Thermostat\Thermostat.csproj* .

1. V aplikaci Visual Studio přejděte na **Vlastnosti projektu > termostatu > ladit**. Pak do projektu přidejte následující proměnné prostředí:

    | Název | Hodnota |
    | ---- | ----- |
    | DEVICE_ID | Moje zařízení – PnP-Device |
    | IOTHUB_CONNECTION_STRING | Hodnota, na kterou jste si poznamenali, když jste [nastavili prostředí](set-up-environment.md) |

1. Nyní můžete vytvořit ukázku v aplikaci Visual Studio a spustit ji v režimu ladění.

### <a name="get-digital-twin"></a>Získání digitálního vlákna

Následující fragment kódu ukazuje, jak aplikace služby načítá digitální vlákna:

```C#
// Get a Twin and retrieves model Id set by Device client
Twin twin = await s_registryManager.GetTwinAsync(s_deviceId);
s_logger.LogDebug($"Model Id of this Twin is: {twin.ModelId}");
```

> [!NOTE]
> Tato ukázka používá obor názvů **Microsoft. Azure. Devices. Client;** z **klienta služby IoT Hub**. Další informace o tom, jak načíst ID modelu, najdete v [příručce pro vývojáře](concepts-developer-guide.md).

Tento kód generuje následující výstup:

```cmd
[09/21/2020 11:26:04]dbug: Thermostat.Program[0]
      Model Id of this Twin is: dtmi:com:example:Thermostat;1
```

Následující fragment kódu ukazuje, jak použít *opravu* k aktualizaci vlastností prostřednictvím digitálního vlákna:

```C#
// Update the twin
var twinPatch = new Twin();
twinPatch.Properties.Desired[PropertyName] = PropertyValue;
await s_registryManager.UpdateTwinAsync(s_deviceId, twinPatch, twin.ETag);
```

Tento kód vygeneruje následující výstup ze zařízení, když služba aktualizuje `targetTemperature` vlastnost:

```cmd
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Property: Received - { "targetTemperature": 60°C }.
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Property: Update - {"targetTemperature": 60°C } is InProgress.

...

[09/21/2020 11:26:17]dbug: Thermostat.ThermostatSample[0]
      Property: Update - {"targetTemperature": 60°C } is Completed.
```

### <a name="invoke-a-command"></a>Vyvolání příkazu

Následující fragment kódu ukazuje, jak zavolat příkaz:

```csharp
private static async Task InvokeCommandAsync()
{
    var commandInvocation = new CloudToDeviceMethod(CommandName) { ResponseTimeout = TimeSpan.FromSeconds(30) };

    // Set command payload
    string componentCommandPayload = JsonConvert.SerializeObject(s_dateTime);
    commandInvocation.SetPayloadJson(componentCommandPayload);

    CloudToDeviceMethodResult result = await s_serviceClient.InvokeDeviceMethodAsync(s_deviceId, commandInvocation);

    if (result == null)
    {
        throw new Exception($"Command {CommandName} invocation returned null");
    }

    s_logger.LogDebug($"Command {CommandName} invocation result status is: {result.Status}");
}
```

Tento kód generuje následující výstup ze zařízení, když služba volá `getMaxMinReport` příkaz:

```cmd
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 21/09/2020 11:25:58.
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Command: MaxMinReport since 21/09/2020 11:25:58: maxTemp=32, minTemp=32, avgTemp=32, startTime=21/09/2020 11:25:59, endTime=21/09/2020 11:26:04
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak připojit zařízení IoT technologie Plug and Play k řešení IoT. Další informace o modelech zařízení IoT technologie Plug and Play najdete v tématech:

> [!div class="nextstepaction"]
> [Příručka pro vývojáře IoT technologie Plug and Play Modeling](concepts-developer-guide-device-csharp.md)
