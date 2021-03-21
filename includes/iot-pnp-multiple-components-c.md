---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 5a8d270ffdef1f9ae68814fa023284c68216d3ff
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104612623"
---
V tomto kurzu se dozvíte, jak vytvořit ukázkovou aplikaci IoT technologie Plug and Play zařízení pomocí komponent, připojit ji k centru IoT a pomocí nástroje Azure IoT Explorer zobrazit informace, které posílá do centra. Ukázková aplikace je napsaná v jazyce C a je obsažená v sadě SDK pro zařízení Azure IoT pro jazyk C. Tvůrce řešení může pomocí nástroje Azure IoT Explorer pochopit možnosti zařízení technologie Plug and Play IoT, aniž by bylo nutné zobrazovat kód zařízení.

V tomto kurzu jste:

> [!div class="checklist"]
> * Stáhněte si vzorový kód.
> * Sestavte vzorový kód.
> * Spusťte ukázkovou aplikaci zařízení a ověřte, že se připojí ke službě IoT Hub.
> * Zkontrolujte zdrojový kód.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Tento kurz můžete dokončit v systému Linux nebo Windows. Příkazy prostředí v tomto kurzu se řídí konvencí pro oddělovače cest pro Linux `/` , pokud sledujete v systému Windows, nezapomeňte tyto oddělovače pro použít `\` .

Požadavky se liší podle operačního systému:

### <a name="linux"></a>Linux

V tomto kurzu se předpokládá, že používáte Ubuntu Linux. Kroky v tomto kurzu byly testovány pomocí Ubuntu 18,04.

K dokončení tohoto kurzu v systému Linux nainstalujte do svého místního prostředí systému Linux následující software:

Pomocí příkazu nainstalujte **RSZ**, **Git**, **cmake** a všechny požadované závislosti `apt-get` :

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Ověřte, že verze nástroje `cmake` je nad **2.8.12** a verze **RSZ** je vyšší než **4.4.7**.

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows

Pro dokončení tohoto kurzu ve Windows nainstalujte do svého místního prostředí Windows následující software:

* [Visual Studio (komunita, Professional nebo Enterprise)](https://visualstudio.microsoft.com/downloads/) – nezapomeňte při [instalaci](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) sady Visual Studio zahrnout **desktopový vývoj s** využitím úlohy C++.
* [Git](https://git-scm.com/download/).
* [Cmake](https://cmake.org/download/).

## <a name="download-the-code"></a>Stáhněte si kód

Pokud jste dokončili [rychlý Start: Připojte ukázkovou aplikaci IoT technologie Plug and Play zařízení běžící v systému Linux nebo Windows do IoT Hub (C)](../articles/iot-pnp/quickstart-connect-device.md) již jste stáhli kód.

V tomto kurzu připravíte vývojové prostředí, které můžete použít k klonování a sestavení sady Azure IoT Hub zařízení C SDK.

Otevřete příkazový řádek ve složce podle vašeho výběru. Spusťte následující příkaz, který naklonuje úložiště GitHub sady [Azure IoT C a knihovny](https://github.com/Azure/azure-iot-sdk-c) do tohoto umístění:

```cmd/bash
git clone https://github.com/Azure/azure-iot-sdk-c.git
cd azure-iot-sdk-c
git submodule update --init
```

Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.

## <a name="build-and-run-the-code"></a>Sestavit a spustit kód

Kód můžete sestavit a spustit pomocí sady Visual Studio nebo `cmake` na příkazovém řádku.

### <a name="use-visual-studio"></a>Použití sady Visual Studio

1. Otevřete kořenovou složku klonovaného úložiště. Po několika sekundách podpora **cmake** v sadě Visual Studio vytvoří vše, co potřebujete ke spuštění a ladění projektu.
1. Po přípravě sady Visual Studio přejděte v **Průzkumník řešení** k ukázce *iothub_client/Samples/PNP/pnp_temperature_controller/*.
1. Klikněte pravým tlačítkem na soubor *pnp_temperature_controller. c* a vyberte **Přidat konfiguraci ladění**. Vyberte **výchozí**.
1. Visual Studio otevře *launch.vs.jsv* souboru. Upravte tento soubor, jak je znázorněno v následujícím fragmentu kódu, a nastavte požadované proměnné prostředí. Poznamenejte si ID oboru a primární klíč registrace, když jste dokončili [nastavení prostředí pro technologie Plug and Play rychlý Start a kurzy pro IoT](../articles/iot-pnp/set-up-environment.md):

    ```json
    {
      "version": "0.2.1",
      "defaults": {},
      "configurations": [
        {
          "type": "default",
          "project": "iothub_client\\samples\\pnp\\pnp_temperature_controller\\pnp_temperature_controller.c",
          "projectTarget": "",
          "name": "pnp_temperature_controller.c",
          "env": {
            "IOTHUB_DEVICE_SECURITY_TYPE": "DPS",
            "IOTHUB_DEVICE_DPS_ID_SCOPE": "<Your ID scope>",
            "IOTHUB_DEVICE_DPS_DEVICE_ID": "my-pnp-device",
            "IOTHUB_DEVICE_DPS_DEVICE_KEY": "<Your enrollment primary key>"
          }
        }
      ]
    }
    ```

1. Klikněte pravým tlačítkem na soubor *pnp_temperature_controller. c* a vyberte **nastavit jako položku po spuštění**.
1. Chcete-li trasovat provádění kódu v aplikaci Visual Studio, přidejte zarážku do `main` funkce v souboru *pnp_temperature_controller. c* .
1. Tuto ukázku teď můžete spustit a ladit z nabídky **ladění** .

Zařízení je teď připravené přijmout příkazy a aktualizace vlastností a zahájilo posílání dat telemetrie do centra. Ponechte ukázku spuštěnou při dokončení dalších kroků.

### <a name="use-cmake-at-the-command-line"></a>Použití `cmake` na příkazovém řádku

Postup sestavení ukázky:

1. Vytvořte podsložku _cmake_ v kořenové složce sady SDK klonovaného zařízení a přejděte do této složky:

    ```cmd/bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Spuštěním následujících příkazů vygenerujte a sestavte soubory projektu pro sadu SDK a ukázky:

    ```cmd/bash
    cmake ..
    cmake --build .
    ```

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Další informace o ukázkové konfiguraci najdete v [ukázkovém souboru Readme](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/pnp/readme.md).

Spuštění ukázky:

1. Ve složce _cmake_ přejděte do složky, která obsahuje spustitelný soubor, a spusťte ho:

    ```bash
    # Bash
    cd iothub_client/samples/pnp/pnp_temperature_controller/
    ./pnp_temperature_controller
    ```

    ```cmd
    REM Windows
    cd iothub_client\samples\pnp\pnp_temperature_controller\Debug
    pnp_temperature_controller.exe
    ```

Zařízení je teď připravené přijmout příkazy a aktualizace vlastností a zahájilo posílání dat telemetrie do centra. Ponechte ukázku spuštěnou při dokončení dalších kroků.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Použití Průzkumníka Azure IoT k ověření kódu

Po spuštění ukázky klienta zařízení použijte nástroj Azure IoT Explorer a ověřte, jestli funguje.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Kontrola kódu

Tato ukázka implementuje zařízení adaptéru technologie Plug and Play pro řízení teploty pro IoT. Tato ukázka implementuje model s [více komponentami](../articles/iot-pnp/concepts-modeling-guide.md). [Soubor modelu DTDL (Digital Real Definition Language) pro zařízení s teplotou](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definuje telemetrii, vlastnosti a příkazy, které zařízení implementuje.

### <a name="iot-plug-and-play-helper-functions"></a>Pomocné funkce IoT technologie Plug and Play

V této ukázce kód používá některé pomocné funkce ze složky */běžné* :

*pnp_device_client_ll* obsahuje metodu Connect pro IoT technologie Plug and Play, která je `model-id` zahrnutá jako parametr: `PnP_CreateDeviceClientLLHandle` .

*pnp_protocol*: obsahuje pomocné funkce technologie Plug and Play IoT:

* `PnP_CreateReportedProperty`
* `PnP_CreateReportedPropertyWithStatus`
* `PnP_ParseCommandName`
* `PnP_CreateTelemetryMessageHandle`
* `PnP_ProcessTwinData`
* `PnP_CopyPayloadToString`
* `PnP_CreateDeviceClientLLHandle_ViaDps`

Tyto pomocné funkce jsou dostatečně obecné pro použití ve vašem vlastním projektu. Tato ukázka je používá ve třech souborech, které odpovídají každé komponentě v modelu:

* *pnp_deviceinfo_component*
* *pnp_temperature_controller*
* *pnp_thermostat_component*

Například v souboru *pnp_deviceinfo_component* `SendReportedPropertyForDeviceInformation` funkce používá dvě funkce pomocníka:

```c
if ((jsonToSend = PnP_CreateReportedProperty(componentName, propertyName, propertyValue)) == NULL)
{
    LogError("Unable to build reported property response for propertyName=%s, propertyValue=%s", propertyName, propertyValue);
}
else
{
    const char* jsonToSendStr = STRING_c_str(jsonToSend);
    size_t jsonToSendStrLen = strlen(jsonToSendStr);

    if ((iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(deviceClientLL, (const unsigned char*)jsonToSendStr, jsonToSendStrLen, NULL, NULL)) != IOTHUB_CLIENT_OK)
    {
        LogError("Unable to send reported state for property=%s, error=%d", propertyName, iothubClientResult);
    }
    else
    {
        LogInfo("Sending device information property to IoTHub.  propertyName=%s, propertyValue=%s", propertyName, propertyValue);
    }
}
```

Jednotlivé komponenty v ukázce následují tento model.

### <a name="code-flow"></a>Tok kódu

`main`Funkce inicializuje připojení a pošle ID modelu:

```c
deviceClient = CreateDeviceClientAndAllocateComponents();
```

Kód používá `PnP_CreateDeviceClientLLHandle` pro připojení ke službě IoT Hub, nastavení `modelId` jako možnost a nastavení metody zařízení a obslužné rutiny zpětného volání pro zařízení pro přímé metody a aktualizace s dvojitým stavem zařízení:

```c
g_pnpDeviceConfiguration.deviceMethodCallback = PnP_TempControlComponent_DeviceMethodCallback;
g_pnpDeviceConfiguration.deviceTwinCallback = PnP_TempControlComponent_DeviceTwinCallback;
g_pnpDeviceConfiguration.modelId = g_temperatureControllerModelId;
...

deviceClient = PnP_CreateDeviceClientLLHandle(&g_pnpDeviceConfiguration);
```

`&g_pnpDeviceConfiguration` obsahuje také informace o připojení. Proměnná prostředí **IOTHUB_DEVICE_SECURITY_TYPE** určuje, zda ukázka používá připojovací řetězec nebo službu Device Provisioning pro připojení ke službě IoT Hub.

Když zařízení pošle ID modelu, bude se jednat o zařízení IoT technologie Plug and Play.

Pomocí obslužných rutin zpětného volání, které je na místě, zařízení reaguje na zdvojené aktualizace a přímé volání metod:

* Pro zpětné volání volajícího zařízení `PnP_TempControlComponent_DeviceTwinCallback` volá `PnP_ProcessTwinData` funkci ke zpracování dat. `PnP_ProcessTwinData` pomocí *vzoru návštěvníka* analyzuje JSON a pak navštíví každou vlastnost, která volá `PnP_TempControlComponent_ApplicationPropertyCallback` každý prvek.

* Pro příkazy zpětného volání, `PnP_TempControlComponent_DeviceMethodCallback` funkce používá pomocnou funkci k analýze příkazu a názvů komponent:

    ```c
    PnP_ParseCommandName(methodName, &componentName, &componentNameSize, &pnpCommandName);
    ```

    `PnP_TempControlComponent_DeviceMethodCallback`Funkce pak zavolá příkaz na součást:

    ```c
    LogInfo("Received PnP command for component=%.*s, command=%s", (int)componentNameSize, componentName, pnpCommandName);
    if (strncmp((const char*)componentName, g_thermostatComponent1Name, g_thermostatComponent1Size) == 0)
    {
        result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle1, pnpCommandName, rootValue, response, responseSize);
    }
    else if (strncmp((const char*)componentName, g_thermostatComponent2Name, g_thermostatComponent2Size) == 0)
    {
        result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle2, pnpCommandName, rootValue, response, responseSize);
    }
    else
    {
        LogError("PnP component=%.*s is not supported by TemperatureController", (int)componentNameSize, componentName);
        result = PNP_STATUS_NOT_FOUND;
    }
    ```

`main`Funkce inicializuje vlastnosti jen pro čtení odeslané do centra IoT:

```c
PnP_TempControlComponent_ReportSerialNumber_Property(deviceClient);
PnP_DeviceInfoComponent_Report_All_Properties(g_deviceInfoComponentName, deviceClient);
PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle1, deviceClient);
PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle2, deviceClient);
```

`main`Funkce vstoupí do smyčky pro aktualizaci dat událostí a telemetrie pro jednotlivé komponenty:

```c
while (true)
{
    PnP_TempControlComponent_SendWorkingSet(deviceClient);
    PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle1, deviceClient);
    PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle2, deviceClient);
}
```

`PnP_ThermostatComponent_SendTelemetry`Funkce ukazuje, jak používat `PNP_THERMOSTAT_COMPONENT` strukturu. Ukázka používá tuto strukturu k ukládání informací o dvou termostatech v řadiči teploty. Kód pomocí `PnP_CreateTelemetryMessageHandle` funkce připraví zprávu a odešle ji:

```c
messageHandle = PnP_CreateTelemetryMessageHandle(pnpThermostatComponent->componentName, temperatureStringBuffer);
...
iothubResult = IoTHubDeviceClient_LL_SendEventAsync(deviceClientLL, messageHandle, NULL, NULL);
```

`main`Funkce nakonec zničí různé komponenty a ukončí připojení k centru.
