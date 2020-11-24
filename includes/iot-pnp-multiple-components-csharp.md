---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: ff1041c941a994784c68d779bd96dec9070ce89a
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2020
ms.locfileid: "95510516"
---
V tomto kurzu se dozvíte, jak vytvořit ukázkovou aplikaci IoT technologie Plug and Play zařízení pomocí komponent, připojit ji k centru IoT a pomocí nástroje Azure IoT Explorer zobrazit informace, které posílá do centra. Ukázková aplikace je napsaná v jazyce C# a je obsažená v sadě SDK pro zařízení Azure IoT pro jazyk C#. Tvůrce řešení může pomocí nástroje Azure IoT Explorer pochopit možnosti zařízení technologie Plug and Play IoT, aniž by bylo nutné zobrazovat kód zařízení.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Pro dokončení tohoto kurzu ve Windows nainstalujte do svého místního prostředí Windows následující software:

* [Visual Studio (komunita, Professional nebo Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Naklonujte úložiště sady SDK pomocí ukázkového kódu.

Pokud jste dokončili [rychlý Start: Připojte ukázkovou aplikaci IoT technologie Plug and Play zařízení běžící v systému Windows k IoT Hub (C#)](../articles/iot-pnp/quickstart-connect-device.md), již jste naklonoval úložiště.

Naklonujte ukázky ze vzorků Azure IoT pro úložiště GitHub v jazyce C#. Otevřete příkazový řádek ve složce podle vašeho výběru. Spuštěním následujícího příkazu naklonujte [Microsoft Azure ukázky IoT pro](https://github.com/Azure-Samples/azure-iot-samples-csharp) úložiště GitHubu .NET:

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="run-the-sample-device"></a>Spuštění ukázkového zařízení

V tomto rychlém startu použijete vzorový herní zařízení, které je napsané v jazyce C# jako zařízení IoT technologie Plug and Play. Spuštění ukázkového zařízení:

1. Otevřete soubor projektu *Azure-IoT-Samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\TemperatureController\TemperatureController.csproj* v aplikaci Visual Studio 2019.

1. V aplikaci Visual Studio přejděte do **projektu > vlastnosti TemperatureController > ladit**. Pak do projektu přidejte následující proměnné prostředí:

    | Name | Hodnota |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | Hodnota, na kterou jste si poznamenali, když jste [nastavili prostředí](../articles/iot-pnp/set-up-environment.md) |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | Moje zařízení – PnP-Device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | Hodnota, na kterou jste si poznamenali, když jste [nastavili prostředí](../articles/iot-pnp/set-up-environment.md) |


1. Nyní můžete vytvořit ukázku v aplikaci Visual Studio a spustit ji v režimu ladění.

1. Zobrazí se zpráva oznamující, že zařízení odeslalo nějaké informace a nahlásilo je jako online. Tyto zprávy signalizují, že zařízení začalo posílat data telemetrie do centra a teď je připravené přijímat příkazy a aktualizace vlastností. Tuto instanci sady Visual Studio nezavírejte, budete ji potřebovat k potvrzení, že ukázka služby funguje.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Použití Azure IoT Exploreru k ověření kódu

Po spuštění ukázky klienta zařízení použijte nástroj Azure IoT Explorer a ověřte, jestli funguje.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Kontrola kódu

Tato ukázka implementuje zařízení adaptéru technologie Plug and Play pro řízení teploty pro IoT. Model, který tato ukázka implementuje, používá [více komponent](../articles/iot-pnp/concepts-components.md). [Soubor modelu DTDL (Digital Real Definition Language) pro zařízení s teplotou](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definuje telemetrii, vlastnosti a příkazy, které zařízení implementuje.

Kód zařízení se připojí ke službě IoT Hub pomocí metody standard `CreateFromConnectionString` . Zařízení odešle ID modelu DTDL modelu, který implementuje v žádosti o připojení. Zařízení, které odesílá ID modelu, je zařízení IoT technologie Plug and Play:

```csharp
private static DeviceClient InitializeDeviceClient(string hostname, IAuthenticationMethod authenticationMethod)
{
    var options = new ClientOptions
    {
        ModelId = ModelId,
    };

    var deviceClient = DeviceClient.Create(hostname, authenticationMethod, TransportType.Mqtt, options);
    deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
    {
        s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
    });

    return deviceClient;
}
```

ID modelu je uloženo v kódu, jak je znázorněno v následujícím fragmentu kódu:

```csharp
private const string ModelId = "dtmi:com:example:TemperatureController;1";
```

Po připojení zařízení ke službě IoT Hub kód zaregistruje obslužné rutiny příkazů. `reboot`Příkaz je definován ve výchozí komponentě. `getMaxMinReport`Příkaz je definován v každé ze dvou částí termostatu:

```csharp
await _deviceClient.SetMethodHandlerAsync("reboot", HandleRebootCommandAsync, _deviceClient, cancellationToken);
await _deviceClient.SetMethodHandlerAsync("thermostat1*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat1, cancellationToken);
await _deviceClient.SetMethodHandlerAsync("thermostat2*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat2, cancellationToken);

```

Existují samostatné obslužné rutiny pro požadované aktualizace vlastností na dvou součástech termostatu:

```csharp
_desiredPropertyUpdateCallbacks.Add(Thermostat1, TargetTemperatureUpdateCallbackAsync);
_desiredPropertyUpdateCallbacks.Add(Thermostat2, TargetTemperatureUpdateCallbackAsync);

```

Vzorový kód odesílá telemetrii z každé komponenty termostatu:

```csharp
await SendTemperatureAsync(Thermostat1, cancellationToken);
await SendTemperatureAsync(Thermostat2, cancellationToken);
```

`SendTemperatureTelemetryAsync`Metoda používá `PnpHhelper` třídu k vytváření zpráv pro každou komponentu:

```csharp
using Message msg = PnpHelper.CreateIothubMessageUtf8(telemetryName, JsonConvert.SerializeObject(currentTemperature), componentName);
```

`PnpHelper`Třída obsahuje další ukázkové metody, které lze použít s více modelem komponent.

K zobrazení telemetrie a vlastností ze dvou součástí termostatu použijte nástroj Azure IoT Explorer:

:::image type="content" source="media/iot-pnp-multiple-components-csharp/multiple-component.png" alt-text="Více zařízení komponent v Azure IoT Exploreru":::

Pomocí nástroje Azure IoT Explorer můžete také volat příkazy v obou dvou přísoučástcích termostatu nebo ve výchozí součásti.
