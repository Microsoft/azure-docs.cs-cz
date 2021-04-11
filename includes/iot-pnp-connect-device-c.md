---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: f626d6058374d52b5a1fd07bfdb229cfb715ca45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104612782"
---
V tomto rychlém startu se dozvíte, jak vytvořit ukázkovou aplikaci IoT technologie Plug and Play zařízení, jak ji připojit k centru IoT a použít nástroj Azure IoT Explorer k zobrazení telemetrie, kterou posílá. Ukázková aplikace je napsaná v jazyce C a je obsažená v sadě SDK pro zařízení Azure IoT pro jazyk C. Tvůrce řešení může pomocí nástroje Azure IoT Explorer pochopit možnosti zařízení technologie Plug and Play IoT, aniž by bylo nutné zobrazovat kód zařízení.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Tento rychlý Start můžete spustit v systému Linux nebo Windows. Příkazy prostředí v tomto rychlém startu se řídí konvencí pro oddělovače cest pro Linux `/` , pokud sledujete v systému Windows, nezapomeňte tyto oddělovače pro použít `\` .

Požadavky se liší podle operačního systému:

### <a name="linux"></a>Linux

V tomto rychlém startu se předpokládá, že používáte Ubuntu Linux. Kroky v tomto rychlém startu byly testovány pomocí Ubuntu 18,04.

K dokončení tohoto rychlého startu v systému Linux nainstalujte následující software do svého místního prostředí Linux:

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

K dokončení tohoto rychlého startu ve Windows nainstalujte do svého místního prostředí Windows následující software:

* [Visual Studio (komunita, Professional nebo Enterprise)](https://visualstudio.microsoft.com/downloads/) – nezapomeňte při [instalaci](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) sady Visual Studio zahrnout **desktopový vývoj s** využitím úlohy C++.
* [Git](https://git-scm.com/download/).
* [Cmake](https://cmake.org/download/).

## <a name="download-the-code"></a>Stáhněte si kód

V tomto rychlém startu připravíte vývojové prostředí, které můžete použít k klonování a sestavování sady Azure IoT Hub zařízení C SDK.

Otevřete příkazový řádek v adresáři dle vašeho výběru. Spusťte následující příkaz, který naklonuje úložiště GitHub sady [Azure IoT C a knihovny](https://github.com/Azure/azure-iot-sdk-c) do tohoto umístění:

```cmd\bash
git clone https://github.com/Azure/azure-iot-sdk-c.git
cd azure-iot-sdk-c
git submodule update --init
```

Očekává se, že dokončení této operace trvá několik minut.

## <a name="build-the-code"></a>Sestavení kódu

Pomocí sady SDK pro zařízení sestavíte zahrnutý vzorový kód:

1. Vytvořte podadresář _cmake_ v kořenové složce sady SDK pro zařízení a přejděte do této složky:

    ```cmd\bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Spusťte následující příkazy a Sestavte sadu SDK a ukázky:

    ```cmd\bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key=ON -Drun_e2e_tests=OFF ..
    cmake --build .
    ```

> [!TIP]
> V systému Windows můžete otevřít řešení vygenerované `cmake` příkazem v aplikaci Visual Studio 2019. Otevřete soubor projektu *azure_iot_sdks. sln* v adresáři _cmake_ a nastavte projekt **pnp_simple_thermostat** jako spouštěný projekt v řešení. Nyní můžete vytvořit ukázku v aplikaci Visual Studio a spustit ji v režimu ladění.

## <a name="run-the-device-sample"></a>Spuštění ukázky zařízení

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Další informace o ukázkové konfiguraci najdete v [ukázkovém souboru Readme](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/pnp/readme.md).

Pokud chcete spustit ukázkovou aplikaci v sadě SDK, která simuluje zařízení IoT technologie Plug and Play odesílající telemetrii do služby IoT Hub:

Ve složce _cmake_ přejděte do složky, která obsahuje spustitelný soubor, a spusťte ho:

```bash
# Bash
cd iothub_client/samples/pnp/pnp_simple_thermostat/
./pnp_simple_thermostat
```

```cmd
REM Windows
cd iothub_client\samples\pnp\pnp_simple_thermostat\Debug
.\pnp_simple_thermostat.exe
```

> [!TIP]
> Chcete-li trasovat provádění kódu v aplikaci Visual Studio ve Windows, přidejte do `main` funkce _pnp_simple_thermostat. c_ bod přerušení.

Zařízení je teď připravené přijmout příkazy a aktualizace vlastností a zahájilo posílání dat telemetrie do centra. Ponechte ukázku spuštěnou při dokončení dalších kroků.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Použití Azure IoT Exploreru k ověření kódu

Po spuštění ukázky klienta zařízení použijte nástroj Azure IoT Explorer a ověřte, jestli funguje.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Kontrola kódu

Tato ukázka implementuje jednoduché zařízení IoT technologie Plug and Play termostat. Model, který tento ukázkový implementuje, nepoužívá [komponenty](../articles/iot-pnp/concepts-modeling-guide.md)IoT technologie Plug and Play. [Soubor modelu DTDL pro zařízení termostata](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) definuje telemetrii, vlastnosti a příkazy, které zařízení implementuje.

Kód zařízení používá standardní funkci pro připojení ke službě IoT Hub:

```c
deviceHandle = IoTHubDeviceClient_CreateFromConnectionString(connectionString, MQTT_Protocol)
```

Zařízení odešle ID modelu DTDL modelu, který implementuje v žádosti o připojení. Zařízení, které odesílá ID modelu, je zařízení IoT technologie Plug and Play:

```c
static const char g_ModelId[] = "dtmi:com:example:Thermostat;1";

...

IoTHubDeviceClient_SetOption(deviceHandle, OPTION_MODEL_ID, modelId)
```

Kód, který aktualizuje vlastnosti, zpracovává příkazy a odesílá telemetrii, je stejný jako kód pro zařízení, které nepoužívá konvence technologie Plug and Play IoT.

Kód používá knihovnu Parson k analýze objektů JSON v datových vytíženích odeslaných ze služby IoT Hub:

```c
// JSON parser
#include "parson.h"
```
