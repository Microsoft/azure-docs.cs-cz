---
title: Jak připojit ukázku služby IoT technologie Plug and Play Bridge běžící v systému Linux nebo Windows do služby IoT Hub | Microsoft Docs
description: Sestavte a spouštějte technologie Plug and Play most IoT v systému Linux nebo Windows, který se připojuje ke centru IoT. K zobrazení informací odesílaných zařízením do centra použijte nástroj Azure IoT Explorer.
author: usivagna
ms.author: ugans
ms.date: 09/22/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 6670f654685f8d5cdcaf55d2b1679738a57ecab4
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042792"
---
# <a name="how-to-connect-an--iot-plug-and-play-bridge-sample-running-on-linux-or-windows-to-iot-hub"></a>Jak připojit ukázku služby IoT technologie Plug and Play Bridge běžící v systému Linux nebo Windows do IoT Hub

V tomto postupu se dozvíte, jak vytvořit ukázkový adaptér pro Cloud technologie Plug and Play mostu pro službu IoT, připojit ho ke službě IoT Hub a použít nástroj Azure IoT Explorer k zobrazení telemetrie, kterou posílá. IoT technologie Plug and Play most je napsaný v jazyce C a obsahuje sadu SDK pro zařízení Azure IoT pro jazyk C. Na konci tohoto kurzu byste měli být schopni spustit most IoT technologie Plug and Play a zobrazit telemetrii sestav IT v Azure IoT Explorer: :::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-explorer-telemetry.png" alt-text="snímek obrazovky znázorňující Azure IoT Explorer s tabulkou ohlášené telemetrie (vlhkost, teplota) z mostu iot technologie Plug and Play.":::

## <a name="prerequisites"></a>Požadované součásti

Tento rychlý Start můžete spustit v systému Linux nebo Windows. Příkazy prostředí v tomto průvodci postupy postupují podle konvence pro oddělovače cest v systému Windows `\` , pokud sledujete na Linux, nezapomeňte tyto oddělovače pro použít `/` .

Požadavky se liší podle operačního systému:

### <a name="linux"></a>Linux

V tomto rychlém startu se předpokládá, že používáte Ubuntu Linux. Kroky v tomto rychlém startu byly testovány pomocí Ubuntu 18,04.

K dokončení tohoto rychlého startu v systému Linux nainstalujte následující software do svého místního prostředí Linux:

Pomocí příkazu nainstalujte **RSZ**, **Git**, **cmake**a všechny požadované závislosti `apt-get` :

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

K dokončení tohoto rychlého startu ve Windows nainstalujte do svého místního prostředí Windows následující software:

* [Visual Studio (komunita, Professional nebo Enterprise)](https://visualstudio.microsoft.com/downloads/) – nezapomeňte při [instalaci](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) sady Visual Studio zahrnout **desktopový vývoj s** využitím úlohy C++.
* [Git](https://git-scm.com/download/).
* [Cmake](https://cmake.org/download/).

### <a name="azure-iot-explorer"></a>Průzkumník Azure IoT

Pokud chcete s ukázkovým zařízením pracovat v druhé části tohoto rychlého startu, použijte nástroj **Azure IoT Explorer** . [Stáhněte a nainstalujte si nejnovější verzi Azure IoT Exploreru](./howto-use-iot-explorer.md) pro váš operační systém.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Spuštěním následujícího příkazu Získejte _připojovací řetězec IoT Hub_ pro vaše centrum. Poznamenejte si tento připojovací řetězec, budete ho používat později v tomto rychlém startu:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Připojovací řetězec služby IoT Hub můžete najít také pomocí nástroje Azure IoT Explorer.

Spuštěním následujícího příkazu Získejte _připojovací řetězec zařízení_ pro zařízení, které jste přidali do centra. Poznamenejte si tento připojovací řetězec, budete ho používat později v tomto rychlém startu:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

## <a name="view-the-model"></a>Zobrazení modelu

V pozdějších krocích se použije Azure IoT Explorer k zobrazení zařízení, když se připojí ke službě IoT Hub. Azure IoT Explorer bude potřebovat místní kopii souboru modelu, která odpovídá **ID modelu** , které vaše zařízení odesílá. Soubor modelu umožňuje Průzkumníku IoT zobrazit telemetrii, vlastnosti a příkazy, které vaše zařízení implementuje.

Při stahování kódu v následujícím kroku obsahuje ukázkové soubory modelu ve `pnpbridge/docs/schema` složce. Příprava Azure IoT Exploreru:

1. Vytvořte ve svém místním počítači složku s názvem *modely* .
1. Zobrazit [EnvironmentalSensor.js](https://aka.ms/iot-pnp-bridge-env-model) a uložit soubor JSON do složky *modely*
1. Zobrazte [RootBridgeSampleDevice.js](https://aka.ms/iot-pnp-bridge-root-model) a uložte soubor JSON do složky *modely* .

## <a name="download-the-code"></a>Stáhněte si kód

Otevřete příkazový řádek v adresáři dle vašeho výběru. Spuštěním následujícího příkazu naklonujte úložiště GitHub pro [IoT technologie Plug and Play most](https://aka.ms/iotplugandplaybridge) do tohoto umístění:

```cmd
git clone https://github.com/Azure/iot-plug-and-play-bridge.git
```

Po klonování úložiště IoT technologie Plug and Play mostu na váš počítač otevřete příkazový řádek pro správu a přejděte do adresáře klonovaného úložiště:

```cmd
cd pnpbridge
git submodule update --init --recursive
```

Očekává se, že dokončení této operace trvá několik minut.

>[!NOTE]
> Pokud narazíte na problémy s nezdařenou aktualizací dílčího modulu klonu Git, jedná se o známý problém s cestami souborů systému Windows a s Git. viz: https://github.com/msysgit/git/pull/110 . K vyřešení problému můžete použít následující příkaz: `git config --system core.longpaths true`

## <a name="setting-up-the-configuration-json"></a>Nastavení formátu JSON konfigurace

Po klonování úložiště IoT technologie Plug and Play mostu na váš počítač přejděte do `pnpbridge/docs/schema` adresáře klonovaného úložiště, kde najdete [konfigurační kód JSON](https://aka.ms/iot-pnp-bridge-env-config) nebo `config.json` Ukázka pro senzor životního prostředí tohoto mostu. Další informace o konfiguračních souborech najdete v [dokumentu koncepty technologie Plug and Play mostu IoT](concepts-iot-pnp-bridge.md).

Pro `root-_interface_model_id` pole budete potřebovat zkopírovat ID modelu IoT technologie Plug and Play, které identifikuje model pro vaše zařízení. V tomto příkladu je to `dtmi:com:example:SampleDevice;1`. Upravte následující parametry v části **pnp_bridge_parameters** Node v `config.json` souboru v:

  Použití připojovacího řetězce (Poznámka: symmetric_key musí odpovídat klíči SAS v připojovacím řetězci):

  ```JSON
    {
      "connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "[CONNECTION STRING]",
        "root_interface_model_id": "[To fill in]",
        "auth_parameters" : {
          "auth_type" : "symmetric_key",
          "symmetric_key" : "[SYMMETRIC KEY]"
        }
      }
    }
  }
  ```

 Jakmile se soubor vyplní, `config.json` měl by vypadat přibližně takto:

   ```JSON
    {
      "connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "[CONNECTION STRING]",
        "root_interface_model_id": "dtmi:com:example:SampleDevice;1",
        "auth_parameters" : {
          "auth_type" : "symmetric_key",
          "symmetric_key" : "[SYMMETRIC KEY]"
        }
      }
    }
  }
```

 Po sestavení mostu ho budete muset umístit `config.json` do stejného adresáře jako most nebo při jeho spuštění zadat jeho cestu.

## <a name="build-the-iot-plug-and-play-bridge"></a>Sestavení mostu IoT technologie Plug and Play

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

## <a name="start-the-iot-plug-and-play-bridge"></a>Spuštění mostu IoT technologie Plug and Play

 Spusťte ukázku mostu IoT technologie Plug and Play pro senzory prostředí tak, že přejdete do složky *pnpbridge* a na příkazovém řádku spustíte následující příkaz:

```bash
 cd cmake/pnpbridge_x86/src/adaptors/samples/environmental_sensor/
./pnpbridge_environmentalsensor

```

```cmd
REM Windows
cd cmake\pnpbridge_x86\src\adaptors\samples\environmental_sensor
Debug\pnpbridge_environmentalsensor.exe
```

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Použití Azure IoT Exploreru k ověření kódu

Po spuštění ukázky klienta zařízení použijte nástroj Azure IoT Explorer a ověřte, jestli funguje.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili připojit zařízení IoT technologie Plug and Play ke službě IoT Hub. Další informace o tom, jak vytvořit řešení, které komunikuje s technologie Plug and Play vašich zařízení IoT, najdete tady:

* [Co je IoT technologie Plug and Play most](./concepts-iot-pnp-bridge.md)
* [Přečtěte si referenční informace pro vývojáře GitHubu pro IoT technologie Plug and Play most](https://aka.ms/iot-pnp-bridge-dev-doc)
* [IoT technologie Plug and Play most na GitHubu](https://aka.ms/iotplugandplaybridge)