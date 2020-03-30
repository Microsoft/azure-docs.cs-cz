---
title: Vytvoření zařízení Azure IoT Plug and Play Preview (Linux) | Dokumenty společnosti Microsoft
description: Ke generování kódu zařízení použijte model schopností zařízení. Pak spusťte kód zařízení a podívejte se, jak se zařízení připojuje k centru IoT Hub.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: d2cc440572d6f33480972c15f5c498cc384cb2e3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75550477"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-linux"></a>Úvodní příručka: Vytvoření zařízení IoT Plug and Play Preview (Linux) pomocí modelu schopností zařízení

[!INCLUDE [iot-pnp-quickstarts-1-selector.md](../../includes/iot-pnp-quickstarts-1-selector.md)]

_Model schopností zařízení_ (DCM) popisuje možnosti zařízení IoT Plug and Play. DCM je často spojena s produktem Skladové položky. Funkce definované v DCM jsou uspořádány do opakovaně použitelných rozhraní. Můžete generovat kód kostry zařízení z DCM. Tento rychlý start vám ukáže, jak používat VS Code na Ubuntu Linux k vytvoření zařízení IoT Plug and Play pomocí DCM.

## <a name="prerequisites"></a>Požadavky

Tento rychlý start předpokládá, že používáte Ubuntu Linux s desktopovým prostředím. Kroky v tomto tutoriálu byly testovány pomocí Ubuntu 18.04.

Chcete-li dokončit tento rychlý start, je třeba nainstalovat následující software na místním počítači s Linuxem:

* Nainstalujte **gcc**, **git**, **cmake** `apt-get` a všechny závislosti pomocí příkazu:

    ```sh
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Ověřte, `cmake` zda je verze písmene a) vyšší než **2.8.12** a verze **GCC** je vyšší než **4.4.7**.

    ```sh
    cmake --version
    gcc --version
    ```

* [Visual Studio kód](https://code.visualstudio.com/).

### <a name="install-azure-iot-tools"></a>Instalace nástrojů Azure IoT

Pomocí následujících kroků nainstalujte rozšiřující balíček [Azure IoT Tools for VS Code:](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

1. V kódu VS vyberte kartu **Rozšíření.**
1. Vyhledejte **nástroje Azure IoT .**
1. Vyberte **Install** (Nainstalovat).

### <a name="get-the-connection-string-for-your-company-model-repository"></a>Získání připojovacího řetězce pro úložiště firemních modelů

Řetězec připojení _úložiště firemního modelu_ najdete na portálu [portálu Azure Certified for IoT,](https://preview.catalog.azureiotsolutions.com) když se přihlásíte pomocí pracovního nebo školního účtu Microsoftu, nebo pomocí ID partnera Microsoftu, pokud ho máte. Po přihlášení vyberte **Úložiště společnosti** a potom **připojovací řetězce**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

V tomto rychlém startu použijete správce knihovny [Vcpkg](https://github.com/microsoft/vcpkg) k instalaci sady Azure IoT C device SDK ve vývojovém prostředí.

Otevřete prostředí. Chcete-li nainstalovat vcpkg, proveďte následující příkaz:

```bash
cd ~
git clone https://github.com/microsoft/vcpkg
cd vcpkg
./bootstrap-vcpkg.sh
./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
```

Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.

## <a name="author-your-model"></a>Vytvoření modelu

V tomto rychlém startu použijete existující ukázkový model schopností zařízení a přidružená rozhraní.

1. Vytvořte `pnp_app` adresář na místní jednotce. Tuto složku použijete pro soubory modelu zařízení a se zakázaným inzerováním kódu zařízení.

    ```bash
    cd ~
    mkdir pnp_app
    ```

1. Stáhněte si model schopností zařízení `pnp_app` a ukázkové soubory rozhraní do složky.

    ```bash
    cd pnp_app
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/SampleDevice.capabilitymodel.json
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensor.interface.json
    ```

1. Otevřete `pnp_app` složku s kódem VS. Soubory můžete zobrazit pomocí technologie IntelliSense:

    ![Model schopností zařízení](media/quickstart-create-pnp-device-linux/dcm.png)

1. V stažených souborech `<YOUR_COMPANY_NAME_HERE>` nahraďte v polích `@id` a `schema` jedinečnou hodnotu. Používejte pouze znaky a-z, A-Z, 0-9 a podtržítko. Další informace naleznete v [tématu Digital Twin identifier format](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format).

## <a name="generate-the-c-code-stub"></a>Generovat se zakázaným inzerováním kódu C

Nyní, když máte DCM a jeho přidružené rozhraní, můžete vygenerovat kód zařízení, který implementuje model. Chcete-li generovat kód C se zakázaným inzerováním v kódu VS:

1. Když `pnp_app` je složka otevřená v kódu VS, otevřete paletu příkazů **pomocí ctrl+shift+p,** zadejte **IoT Plug and Play**a vyberte Generovat kód **zařízení**.

    > [!NOTE]
    > Při prvním použití nástroje Generátor kódu Plug and Play ioT trvá několik sekund, než se automaticky stáhne a nainstaluje.

1. Zvolte **sampledevice.capabilitymodel.json** soubor použít pro generování kódu zařízení se zakázaným inzerováním.

1. Zadejte název projektu **sample_device**. Bude to název aplikace zařízení.

1. Jako jazyk zvolte **ANSI C.**

1. Jako metodu připojení zvolte **připojovací řetězec zařízení Služby Via IoT Hub.**

1. Jako šablonu projektu zvolte **CMake Project na Linuxu.**

1. Jako způsob zahrnutí sady SDK zařízení zvolte **Via Vcpkg.**

1. Nová složka s názvem **sample_device** je vytvořena ve stejném umístění jako soubor DCM a v něm jsou generované soubory se zakázaným inzerováním kódu zařízení. VS Kód otevře nové okno pro zobrazení těchto.
    ![Kód zařízení](media/quickstart-create-pnp-device-linux/device-code.png)

## <a name="build-and-run-the-code"></a>Sestavení a spuštění kódu

Zdrojový kód sady SDK zařízení slouží k vytvoření kódu generovaného zařízení se zakázaným inzerováním. Aplikace, kterou vytvoříte, simuluje zařízení, které se připojuje k centru IoT. Aplikace odesílá telemetrii a vlastnosti a přijímá příkazy.

1. Vytvořte složku sestavení **CMake** pro **aplikaci sample_device:**

    ```bash
    cd ~/pnp_app/sample_device
    mkdir cmake
    cd cmake
    ```

1. Spusťte CMake a vytvořte aplikaci pomocí sady SDK. Následující příkaz předpokládá, že jste nainstalovali **vcpkg** do domovské složky:

    ```bash
    cmake .. -DCMAKE_TOOLCHAIN_FILE=~/vcpkg/scripts/buildsystems/vcpkg.cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    cmake --build .
    ```

1. Po úspěšném dokončení sestavení spusťte aplikaci předání matná zařízení služby IoT řetězce jako parametr.

    ```sh
    cd ~/pnp_app/sample_device/cmake
    ./sample_device "<YourDeviceConnectionString>"
    ```

1. Aplikace zařízení spustí odesílání dat do služby IoT Hub.

    ![Spuštěná aplikace zařízení](media/quickstart-create-pnp-device-linux/device-app-running.png)

## <a name="validate-the-code"></a>Ověřit kód

### <a name="publish-device-model-files-to-model-repository"></a>Publikování souborů modelů zařízení do úložiště modelů

Chcete-li ověřit kód zařízení pomocí rozhraní SEkacího rozhraní **AZ** CLI, musíte publikovat soubory do úložiště modelu.

1. Když `pnp_app` je složka otevřená v kódu VS, otevřete paletu příkazů **pomocí ctrl+shift+p,** zadejte a vyberte **IoT Plug & Play: Odeslat soubory do úložiště modelů**.

1. Výběr `SampleDevice.capabilitymodel.json` `EnvironmentalSensor.interface.json` a soubory.

1. Zadejte připojovací řetězec úložiště firemního modelu.

    > [!NOTE]
    > Připojovací řetězec je vyžadován pouze při prvním připojení k úložišti.

1. Ve výstupním okně kódu VS a oznámení můžete zkontrolovat, zda byly soubory úspěšně publikovány.

    > [!NOTE]
    > Pokud se při publikování souborů modelu zařízení zoáhnou chyby, můžete zkusit použít příkaz **IoT Plug and Play: Odhlásit úložiště modelů,** abyste se odhlásit a znovu provedli kroky.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>Ověření kódu pomocí příkazového příkazu Konitolu Azure IoT

Po spuštění ukázky klienta zařízení můžete zkontrolovat, zda pracuje s rozhraním příkazového příkazového příkazu Kaviádu Azure.

Pomocí následujícího příkazu zobrazíte telemetrii, kterou ukázkové zařízení odesílá. Možná budete muset počkat minutu nebo dvě, než se zobrazí všechny telemetrie ve výstupu:

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubNme> --device-id <YourDeviceID>
```

Pomocí následujícího příkazu zobrazíte všechny vlastnosti odeslané zařízením:

```azurecli-interactive
az iot dt list-properties --device-id <YourDeviceID> --hub-name <YourIoTHubNme> --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak vytvořit zařízení IoT Plug and Play pomocí DCM.

Chcete-li se dozvědět více o DCMs a jak vytvořit vlastní modely, pokračujte v kurzu:

> [!div class="nextstepaction"]
> [Kurz: Vytvoření a testování modelu schopností zařízení pomocí kódu Sady Visual Studio](tutorial-pnp-visual-studio-code.md)
