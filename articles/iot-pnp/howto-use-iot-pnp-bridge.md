---
title: Jak připojit ukázku služby IoT technologie Plug and Play Bridge běžící v systému Linux nebo Windows do služby IoT Hub | Microsoft Docs
description: Sestavte a spouštějte technologie Plug and Play most IoT v systému Linux nebo Windows, který se připojuje ke centru IoT. K zobrazení informací odesílaných zařízením do centra použijte nástroj Azure IoT Explorer.
author: usivagna
ms.author: ugans
ms.date: 12/11/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: bf730dbc28d15c3d036e9ebeedbe035db087c5d8
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673018"
---
# <a name="how-to-connect-an--iot-plug-and-play-bridge-sample-running-on-linux-or-windows-to-iot-hub"></a>Jak připojit ukázku služby IoT technologie Plug and Play Bridge běžící v systému Linux nebo Windows do IoT Hub

V tomto článku se dozvíte, jak vytvořit ukázkový adaptér pro prostředí IoT technologie Plug and Play Bridge, jak ho připojit ke službě IoT Hub, a pomocí nástroje Azure IoT Explorer zobrazit telemetrii, kterou posílá. IoT technologie Plug and Play most je napsaný v jazyce C a obsahuje sadu SDK pro zařízení Azure IoT pro jazyk C. Na konci tohoto kurzu byste měli být schopni spustit službu IoT technologie Plug and Play most a zobrazit telemetrii sestav IT v Azure IoT Explorer:

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-explorer-telemetry.png" alt-text="Snímek obrazovky zobrazující Azure IoT Explorer s tabulkou nahlášené telemetrie (vlhkost, teplota) ze služby IoT technologie Plug and Play most.":::

## <a name="prerequisites"></a>Požadavky

Ukázku můžete spustit v článku v systému Windows nebo Linux. Příkazy prostředí v tomto průvodci postupy postupují podle konvence pro oddělovače cest v systému Windows `\` , pokud sledujete na Linux, nezapomeňte tyto oddělovače pro použít `/` .

### <a name="azure-iot-explorer"></a>Průzkumník Azure IoT

Pokud chcete s ukázkovým zařízením pracovat v druhé části tohoto článku, použijte nástroj **Azure IoT Explorer** . [Stáhněte a nainstalujte si nejnovější verzi Azure IoT Exploreru](./howto-use-iot-explorer.md) pro váš operační systém.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Spuštěním následujícího příkazu Získejte _připojovací řetězec IoT Hub_ pro vaše centrum. Poznamenejte si tento připojovací řetězec, použijete ho později v tomto článku:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

Spuštěním následujícího příkazu Získejte _připojovací řetězec zařízení_ pro zařízení, které jste přidali do centra. Poznamenejte si tento připojovací řetězec, použijete ho později v tomto článku:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

## <a name="download-and-run-the-bridge"></a>Stažení a spuštění mostu

V tomto článku máte dvě možnosti, jak spustit most. Máte následující možnosti:

- Stáhněte si předem sestavený spustitelný soubor a spusťte ho, jak je popsáno v této části.
- Stáhněte zdrojový kód a potom [Sestavte a spusťte most](#build-and-run-the-bridge) , jak je popsáno v následující části.

Stažení a spuštění mostu:

1. Přejít na [stránku vydání](https://github.com/Azure/iot-plug-and-play-bridge/releases)IoT technologie Plug and Play.
1. Stáhněte si předem sestavený spustitelný soubor pro váš operační systém: **pnpbridge_bin.exe** pro Windows nebo **pnpbridge_bin** pro Linux.
1. Stáhněte si vzorový [config.jsdo](https://raw.githubusercontent.com/Azure/iot-plug-and-play-bridge/master/pnpbridge/src/adapters/samples/environmental_sensor/config.json) konfiguračního souboru pro ukázku environmentálního senzoru. Ujistěte se, že se konfigurační soubor nachází ve stejné složce jako spustitelný soubor.
1. Upravit *config.jsv* souboru:

    - Přidejte `connection-string` hodnotu, která je _připojovacím řetězcem zařízení_ , který jste si poznamenali dříve.
    - Přidejte hodnotu `symmetric_key` , která je sdílená přístupová klávesová hodnota z _připojovacího řetězce zařízení_.
    - Nahraďte `root_interface_model_id` hodnotu hodnotou `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` .

    První část *config.jsv* souboru teď vypadá jako v následujícím fragmentu kódu:

    ```json
    {
      "$schema": "../../../pnpbridge/src/pnpbridge_config_schema.json",
      "pnp_bridge_connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "HostName=youriothub.azure-devices.net;DeviceId=yourdevice;SharedAccessKey=TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8=",
        "root_interface_model_id": "dtmi:com:example:PnpBridgeEnvironmentalSensor;1",
        "auth_parameters": {
            "auth_type": "symmetric_key",
            "symmetric_key": "TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8="
        },
    ```

1. Spusťte spustitelný soubor v prostředí příkazového řádku. Most vygeneruje výstup, který vypadá takto:

    ```output
    c:\temp\temp-bridge>dir
     Volume in drive C is OSDisk
     Volume Serial Number is 38F7-DA4A
    
     Directory of c:\temp\temp-bridge
    
    10/12/2020  12:24    <DIR>          .
    10/12/2020  12:24    <DIR>          ..
    08/12/2020  15:26             1,216 config.json
    10/12/2020  12:21         3,617,280 pnpbridge_bin.exe
                   2 File(s)      3,618,496 bytes
                   2 Dir(s)  12,999,147,520 bytes free
    
    c:\temp\temp-bridge>pnpbridge_bin.exe
    Info:
     -- Press Ctrl+C to stop PnpBridge
    
    Info: Using default configuration location
    Info: Starting Azure PnpBridge
    Info: Pnp Bridge is running as am IoT egde device.
    Info: Pnp Bridge creation succeeded.
    Info: Connection_type is [connection_string]
    Info: Tracing is disabled
    Info: WARNING: SharedAccessKey is included in connection string. Ignoring symmetric_key in config file.
    Info: IoT Edge Device configuration initialized successfully
    Info: Building Pnp Bridge Adapter Manager, Adapters & Components
    Info: Adapter with identity environment-sensor-sample-pnp-adapter does not have any associated global parameters. Proceeding with adapter creation.
    Info: Pnp Adapter with adapter ID environment-sensor-sample-pnp-adapter has been created.
    Info: Pnp Adapter Manager created successfully.
    Info: Pnp components created successfully.
    Info: Pnp components built in model successfully.
    Info: Connected to Azure IoT Hub
    Info: Environmental Sensor: Starting Pnp Component
    Info: IoTHub client call to _SendReportedState succeeded
    Info: Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=state, propertyValue=true
    Info: Pnp components started successfully.
    ```

## <a name="build-and-run-the-bridge"></a>Sestavení a spuštění mostu

Pokud dáváte přednost sestavení spustitelného souboru sami, můžete si stáhnout zdrojový kód a vytvořit skripty.

Otevřete příkazový řádek ve složce podle vašeho výběru. Spuštěním následujícího příkazu naklonujte úložiště GitHub pro [IoT technologie Plug and Play most](https://github.com/Azure/iot-plug-and-play-bridge) do tohoto umístění:

```cmd
git clone https://github.com/Azure/iot-plug-and-play-bridge.git
```

Po naklonování úložiště aktualizujte dílčí moduly. Mezi dílčí moduly patří sada Azure IoT SDK pro jazyk C:

```cmd
cd iot-plug-and-play-bridge
git submodule update --init --recursive
```

Očekává se, že dokončení této operace trvá několik minut.

> [!TIP]
> Pokud narazíte na problémy s nezdařenou aktualizací dílčího modulu klonu Git, jedná se o známý problém s cestami k souborům Windows. K vyřešení problému můžete použít následující příkaz: `git config --system core.longpaths true`

Požadavky na sestavení mostu se liší podle operačního systému:

### <a name="windows"></a>Windows

Pokud chcete vytvořit most IoT technologie Plug and Play ve Windows, nainstalujte následující software:

* [Visual Studio (komunita, Professional nebo Enterprise)](https://visualstudio.microsoft.com/downloads/) – nezapomeňte při [instalaci](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) sady Visual Studio zahrnout **desktopový vývoj s** využitím úlohy C++.
* [Git](https://git-scm.com/download/).
* [Cmake](https://cmake.org/download/).

### <a name="linux"></a>Linux

V tomto článku se předpokládá, že používáte Ubuntu Linux. Kroky v tomto článku byly testovány pomocí Ubuntu 18,04.

Pokud chcete vytvořit most IoT technologie Plug and Play v systému Linux, nainstalujte do nástroje **RSZ**, **Git**, **cmake** a všechny požadované závislosti pomocí `apt-get` příkazu:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Ověřte, že verze nástroje `cmake` je nad **2.8.12** a verze **RSZ** je vyšší než **4.4.7**.

```sh
cmake --version
gcc --version
```

### <a name="build-the-iot-plug-and-play-bridge"></a>Sestavení mostu IoT technologie Plug and Play

Přejděte do složky *pnpbridge* v adresáři úložiště.

Pro Windows spusťte na [Developer Command Prompt pro Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs)následující:

```cmd
cd scripts\windows
build.cmd
```

Podobně pro Linux spusťte následující:

```bash
cd scripts/linux
./setup.sh
./build.sh
```

>[!TIP]
>V systému Windows můžete otevřít řešení vygenerované příkazem cmake v sadě Visual Studio 2019. Otevřete soubor projektu *azure_iot_pnp_bridge. sln* v adresáři cmake a nastavte projekt *pnpbridge_bin* jako spouštěný projekt v řešení. Nyní můžete vytvořit ukázku v aplikaci Visual Studio a spustit ji v režimu ladění.

### <a name="edit-the-configuration-file"></a>Úprava konfiguračního souboru

Další informace o konfiguračních souborech najdete v [dokumentu koncepty technologie Plug and Play mostu IoT](concepts-iot-pnp-bridge.md).

Otevřete *iot-plug-and-play-bridge\pnpbridge\src\adapters\samples\environmental_sensor\config.js* v souboru v textovém editoru.

- Přidejte `connection-string` hodnotu, která je _připojovacím řetězcem zařízení_ , který jste si poznamenali dříve.
- Přidejte hodnotu `symmetric_key` , která je sdílená přístupová klávesová hodnota z _připojovacího řetězce zařízení_.
- Nahraďte `root_interface_model_id` hodnotu hodnotou `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` .

První část *config.jsv* souboru teď vypadá jako v následujícím fragmentu kódu:

```json
{
  "$schema": "../../../pnpbridge/src/pnpbridge_config_schema.json",
  "pnp_bridge_connection_parameters": {
    "connection_type" : "connection_string",
    "connection_string" : "HostName=youriothub.azure-devices.net;DeviceId=yourdevice;SharedAccessKey=TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8=",
    "root_interface_model_id": "dtmi:com:example:PnpBridgeEnvironmentalSensor;1",
    "auth_parameters": {
        "auth_type": "symmetric_key",
        "symmetric_key": "TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8="
    },
```

### <a name="run-the-iot-plug-and-play-bridge"></a>Spuštění mostu IoT technologie Plug and Play

Spusťte ukázku prostředí IoT technologie Plug and Play mostu pro ekologický senzor. Parametr je cesta k `config.json` souboru, který jste upravili v předchozí části:

```cmd
REM Windows
cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console
Debug\pnpbridge_bin.exe ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
```

Most vygeneruje výstup, který vypadá takto:

```output
c:\temp>cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console

c:\temp\iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console>Debug\pnpbridge_bin.exe ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
Info:
 -- Press Ctrl+C to stop PnpBridge

Info: Using configuration from specified file path: ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
Info: Starting Azure PnpBridge
Info: Pnp Bridge is running as am IoT egde device.
Info: Pnp Bridge creation succeeded.
Info: Connection_type is [connection_string]
Info: Tracing is disabled
Info: WARNING: SharedAccessKey is included in connection string. Ignoring symmetric_key in config file.
Info: IoT Edge Device configuration initialized successfully
Info: Building Pnp Bridge Adapter Manager, Adapters & Components
Info: Adapter with identity environment-sensor-sample-pnp-adapter does not have any associated global parameters. Proceeding with adapter creation.
Info: Pnp Adapter with adapter ID environment-sensor-sample-pnp-adapter has been created.
Info: Pnp Adapter Manager created successfully.
Info: Pnp components created successfully.
Info: Pnp components built in model successfully.
Info: Connected to Azure IoT Hub
Info: Environmental Sensor: Starting Pnp Component
Info: IoTHub client call to _SendReportedState succeeded
Info: Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=state, propertyValue=true
Info: Pnp components started successfully.
Info: IoTHub client call to _SendEventAsync succeeded
Info: PnpBridge_PnpBridgeStateTelemetryCallback called, result=0, telemetry=PnpBridge configuration complete
Info: Processing property update for the device or module twin
Info: Environmental Sensor Adapter:: Successfully delivered telemetry message for <environmentalSensor>
```

Pomocí následujících příkazů spusťte most na platformě Linux:

```bash
cd iot-plug-and-play-bridge/pnpbridge/cmake/pnpbridge_x86/src/pnpbridge/samples/console
./pnpbridge_bin ../../../../../../src/adapters/samples/environmental_sensor/config.json
```

## <a name="download-the-model-files"></a>Stažení souborů modelu

Pomocí Azure IoT Exploreru se můžete podívat na zařízení, když se připojí ke službě IoT Hub. Azure IoT Explorer potřebuje místní kopii souboru modelu, která odpovídá **ID modelu** , které vaše zařízení odesílá. Soubor modelu umožňuje Průzkumníku IoT zobrazit telemetrii, vlastnosti a příkazy, které vaše zařízení implementuje.

Pokud si chcete stáhnout modely pro Azure IoT Explorer:

1. Vytvořte ve svém místním počítači složku s názvem *modely* .
1. Uložte [EnvironmentalSensor.js](https://raw.githubusercontent.com/Azure/iot-plug-and-play-bridge/master/pnpbridge/docs/schemas/EnvironmentalSensor.json) do složky *modely* , kterou jste vytvořili v předchozím kroku.
1. Pokud tento soubor modelu otevřete v textovém editoru, vidíte, že model definuje komponentu s `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` jako její ID. Toto je stejné ID modelu, které jste použili v *config.js* souboru.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Použití Azure IoT Exploreru k ověření kódu

Po spuštění mostu použijte nástroj Azure IoT Explorer a ověřte, jestli funguje. Můžete zobrazit telemetrii, vlastnosti a příkazy definované v `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` modelu.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili, jak připojit zařízení IoT technologie Plug and Play ke službě IoT Hub. Další informace o tom, jak vytvořit řešení, které komunikuje s technologie Plug and Play vašich zařízení IoT, najdete tady:

* [Co je IoT technologie Plug and Play most](./concepts-iot-pnp-bridge.md)
* [Sestavování, nasazování a rozšiřování IoT technologie Plug and Play mostu](howto-build-deploy-extend-pnp-bridge.md)
* [IoT technologie Plug and Play most na GitHubu](https://github.com/Azure/iot-plug-and-play-bridge)
