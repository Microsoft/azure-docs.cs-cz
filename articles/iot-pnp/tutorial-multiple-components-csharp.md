---
title: Ukázka připojení IoT technologie Plug and Play Preview pro kód zařízení komponenty C# IoT Hub | Microsoft Docs
description: Sestavte a spouštějte technologie Plug and Play IoT Preview ukázka kódu zařízení v C#, který používá více komponent a připojuje se ke centru IoT. K zobrazení informací odesílaných zařízením do centra použijte nástroj Azure IoT Explorer.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: cf2662bef194bc2b7afdb07f55809ba63f40fd6e
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352767"
---
# <a name="tutorial-connect-an-iot-plug-and-play-multiple-component-device-application-running-on-windows-to-iot-hub-c"></a>Kurz: připojení aplikace IoT technologie Plug and Play více komponent zařízení se systémem Windows k IoT Hub (C#)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

V tomto kurzu se dozvíte, jak vytvořit ukázkovou aplikaci IoT technologie Plug and Play pro zařízení pomocí komponent a kořenového rozhraní, připojit ho ke službě IoT Hub a pomocí nástroje Azure IoT Explorer zobrazit informace, které posílá do centra. Ukázková aplikace je napsaná v jazyce C# a je obsažená v sadě SDK pro zařízení Azure IoT pro jazyk C#. Tvůrce řešení může pomocí nástroje Azure IoT Explorer pochopit možnosti zařízení technologie Plug and Play IoT, aniž by bylo nutné zobrazovat kód zařízení.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Předpoklady

Pro dokončení tohoto kurzu ve Windows nainstalujte do svého místního prostředí Windows následující software:

* [Visual Studio (komunita, Professional nebo Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).
* [Cmake](https://cmake.org/download/).

### <a name="azure-iot-explorer"></a>Průzkumník Azure IoT

Pokud chcete s ukázkovým zařízením pracovat v druhé části tohoto kurzu, použijte nástroj **Azure IoT Explorer** . [Stáhněte a nainstalujte si nejnovější verzi Azure IoT Exploreru](./howto-use-iot-explorer.md) pro váš operační systém.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Spuštěním následujícího příkazu Získejte _připojovací řetězec služby IoT Hub_ pro vaše centrum. Poznamenejte si tento připojovací řetězec, použijete ho později v tomto kurzu:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Připojovací řetězec služby IoT Hub můžete najít také pomocí nástroje Azure IoT Explorer.

Spuštěním následujícího příkazu Získejte _připojovací řetězec zařízení_ pro zařízení, které jste přidali do centra. Poznamenejte si tento připojovací řetězec, použijete ho později v tomto kurzu:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>Stáhněte si kód

V tomto kurzu připravíte vývojové prostředí, které můžete použít k klonování a sestavení sady SDK pro Azure IoT Hub zařízení v C#.

Otevřete příkazový řádek v adresáři dle vašeho výběru. Spusťte následující příkaz, který naklonuje úložiště GitHubu do [knihovny Azure IoT C# SDK a knihovny](https://github.com/Azure/azure-iot-sdk-csharp) do tohoto umístění:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-c.git
```

## <a name="build-the-code"></a>Sestavení kódu

Otevřete soubor řešení **azureiot. sln** v sadě Visual Studio 2019 a nastavte projekt **TemperatureController** jako spouštěný projekt. V **Průzkumník řešení**můžete tento soubor projektu najít v **ukázkách > > zařízení v iothub**.

Nyní můžete vytvořit ukázku v aplikaci Visual Studio a spustit ji v režimu ladění.

## <a name="run-the-device-sample"></a>Spuštění ukázky zařízení

Vytvořte proměnnou prostředí s názvem **IOTHUB_DEVICE_CONNECTION_STRING** pro uložení připojovacího řetězce zařízení, který jste si poznamenali dříve.

Chcete-li trasovat provádění kódu v aplikaci Visual Studio ve Windows, přidejte do `main` funkce v souboru program.cs bod přerušení.

Zařízení je teď připravené přijmout příkazy a aktualizace vlastností a zahájilo posílání dat telemetrie do centra. Ponechte ukázku spuštěnou při dokončení dalších kroků.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Použití Azure IoT Exploreru k ověření kódu

Po spuštění ukázky klienta zařízení použijte nástroj Azure IoT Explorer a ověřte, jestli funguje.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Kontrola kódu

Tato ukázka implementuje zařízení adaptéru technologie Plug and Play pro řízení teploty pro IoT. Model, který tato ukázka implementuje, používá [více komponent](concepts-components.md). [Soubor modelu DTDL (Digital Real Definition Language) pro zařízení s teplotou](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definuje telemetrii, vlastnosti a příkazy, které zařízení implementuje.

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

Po připojení zařízení ke službě IoT Hub kód zaregistruje obslužné rutiny příkazů. `reboot`Příkaz je definován v kořenovém rozhraní. `getMaxMinReport`Příkaz je definován v každé ze dvou částí termostatu:

```csharp
await s_deviceClient.SetMethodHandlerAsync("reboot", HandleRebootCommandAsync, s_deviceClient);
await s_deviceClient.SetMethodHandlerAsync("thermostat1*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat1);
await s_deviceClient.SetMethodHandlerAsync("thermostat2*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat2);
```

Existují samostatné obslužné rutiny pro požadované aktualizace vlastností na dvou součástech termostatu:

```csharp
s_desiredPropertyUpdateCallbacks.Add(Thermostat1, TargetTemperatureUpdateCallbackAsync);
s_desiredPropertyUpdateCallbacks.Add(Thermostat2, TargetTemperatureUpdateCallbackAsync);
```

Vzorový kód odesílá telemetrii z každé komponenty termostatu:

```csharp
await SendTemperatureAsync(Thermostat1);
await SendTemperatureAsync(Thermostat2);
```

`SendTemperature`Metoda používá `PnpHhelper` třídu k vytváření zpráv pro každou komponentu:

```csharp
Message msg = PnpHelper.CreateIothubMessageUtf8(telemetryName, JsonConvert.SerializeObject(currentTemperature), componentName);
```

`PnpHelper`Třída obsahuje další ukázkové metody, které lze použít s více modelem komponent.

K zobrazení telemetrie a vlastností ze dvou součástí termostatu použijte nástroj Azure IoT Explorer:

:::image type="content" source="media/tutorial-multiple-components-csharp/multiple-component.png" alt-text="Více zařízení komponent v Azure IoT Exploreru":::

Pomocí nástroje Azure IoT Explorer můžete také volat příkazy v některé ze dvou součástí termostatu nebo v kořenovém rozhraní.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili připojit zařízení IoT technologie Plug and Play k komponentám do služby IoT Hub. Další informace o modelech zařízení IoT technologie Plug and Play najdete v tématech:

> [!div class="nextstepaction"]
> [Příručka pro vývojáře pro modelování IoT technologie Plug and Play Preview](concepts-developer-guide.md)
