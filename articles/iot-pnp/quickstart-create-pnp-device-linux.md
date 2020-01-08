---
title: Vytvoření zařízení Azure IoT technologie Plug and Play Preview (Linux) | Microsoft Docs
description: Pomocí modelu schopností zařízení vygenerujte kód zařízení. Pak spusťte kód zařízení a zkontrolujte, že se zařízení připojí k vašemu IoT Hub.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: d2cc440572d6f33480972c15f5c498cc384cb2e3
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2019
ms.locfileid: "75550477"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-linux"></a>Rychlý Start: použití modelu schopností zařízení k vytvoření zařízení IoT technologie Plug and Play Preview (Linux)

[!INCLUDE [iot-pnp-quickstarts-1-selector.md](../../includes/iot-pnp-quickstarts-1-selector.md)]

_Model schopností zařízení_ (DCM) popisuje možnosti zařízení IoT technologie Plug and Play. DCM je často spojený s SKU produktu. Funkce definované v DCM jsou uspořádané do opakovaně použitelných rozhraní. Z DCM můžete vygenerovat kostru kódu zařízení. V tomto rychlém startu se dozvíte, jak používat VS Code v Ubuntu Linux k vytvoření zařízení IoT technologie Plug and Play pomocí DCM.

## <a name="prerequisites"></a>Požadavky

V tomto rychlém startu se předpokládá, že používáte Ubuntu Linux s desktopovým prostředím. Kroky v tomto kurzu byly testovány pomocí Ubuntu 18,04.

K dokončení tohoto rychlého startu je potřeba na svém místním počítači se systémem Linux nainstalovat následující software:

* Pomocí příkazu `apt-get` nainstalujte **RSZ**, **Git**, **cmake**a všechny závislosti:

    ```sh
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Ověřte, že verze `cmake` je nad **2.8.12** a verze **RSZ** je vyšší než **4.4.7**.

    ```sh
    cmake --version
    gcc --version
    ```

* [Visual Studio Code](https://code.visualstudio.com/).

### <a name="install-azure-iot-tools"></a>Instalace nástrojů Azure IoT

Pomocí následujících kroků nainstalujte sadu [nástrojů Azure IoT Tools for vs Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Extension Pack:

1. V VS Code vyberte kartu **rozšíření** .
1. Vyhledejte **nástroje Azure IoT Tools**.
1. Vyberte **Install** (Nainstalovat).

### <a name="get-the-connection-string-for-your-company-model-repository"></a>Získání připojovacího řetězce pro úložiště podnikového modelu

_Připojovací řetězec úložiště modelů společnosti_ můžete najít na portálu [Azure Certified for IoT Portal](https://preview.catalog.azureiotsolutions.com) při přihlášení pomocí pracovního nebo školního účtu Microsoft nebo svého ID partnera Microsoftu, pokud ho máte. Po přihlášení vyberte **úložiště společnosti** a pak **připojovací řetězce**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

V tomto rychlém startu pomocí Správce knihovny [Vcpkg](https://github.com/microsoft/vcpkg) nainstalujete sadu SDK pro zařízení Azure IoT C ve vývojovém prostředí.

Otevřete prostředí. Spusťte následující příkaz pro instalaci Vcpkg:

```bash
cd ~
git clone https://github.com/microsoft/vcpkg
cd vcpkg
./bootstrap-vcpkg.sh
./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
```

Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.

## <a name="author-your-model"></a>Vytváření modelu

V tomto rychlém startu použijete existující Vzorový model zařízení a přidružená rozhraní.

1. Vytvořte `pnp_app` adresář na místním disku. Tuto složku použijete pro soubory modelů zařízení a pro zástupné kódy zařízení.

    ```bash
    cd ~
    mkdir pnp_app
    ```

1. Stáhněte si model funkce zařízení a ukázkové soubory rozhraní do složky `pnp_app`.

    ```bash
    cd pnp_app
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/SampleDevice.capabilitymodel.json
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensor.interface.json
    ```

1. Otevřete `pnp_app` složku pomocí VS Code. Můžete zobrazit soubory pomocí IntelliSense:

    ![Model schopností zařízení](media/quickstart-create-pnp-device-linux/dcm.png)

1. V souborech, které jste stáhli, nahraďte `<YOUR_COMPANY_NAME_HERE>` v polích `@id` a `schema` jedinečnou hodnotou. Používejte pouze znaky a-z, A-Z, 0-9 a podtržítko. Další informace najdete v tématu [Formát digitálního Nevlákenového identifikátoru](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format).

## <a name="generate-the-c-code-stub"></a>Generovat zástupný kód kódu jazyka C

Teď, když máte modul DCM a jeho přidružená rozhraní, můžete vygenerovat kód zařízení, který model implementuje. Chcete-li vygenerovat zástupnou proceduru kódu C v VS Code:

1. Když je složka `pnp_app` v VS Code otevřená, pomocí **kombinace kláves CTRL + SHIFT + P** otevřete paletu příkazů, zadejte **IoT technologie Plug and Play**a vyberte **Generovat zástupný kód zařízení**.

    > [!NOTE]
    > Při prvním použití nástroje IoT technologie Plug and Play Code Generator, trvá několik sekund na stažení a instalaci automaticky.

1. Vyberte soubor **SampleDevice. capabilitymodel. JSON** , který se použije k vygenerování zástupné procedury pro kód zařízení.

1. Zadejte název projektu **sample_device**. Toto bude název aplikace vašeho zařízení.

1. Jako jazyk vyberte **ANSI C** .

1. Jako metodu připojení vyberte **prostřednictvím IoT Hub připojovací řetězec zařízení** .

1. Jako šablonu projektu vyberte **projekt cmake v systému Linux** .

1. Vyberte **prostřednictvím Vcpkg** jako způsob, jak zahrnout sadu SDK pro zařízení.

1. Nová složka s názvem **sample_device** se vytvoří ve stejném umístění jako soubor DCM a v takovém případě se jedná o vygenerované soubory zástupných procedur kódu zařízení. VS Code otevře nové okno, ve kterém se zobrazí.
    ![kód zařízení](media/quickstart-create-pnp-device-linux/device-code.png)

## <a name="build-and-run-the-code"></a>Sestavit a spustit kód

Zdrojový kód sady SDK pro zařízení slouží k sestavení zástupné procedury pro vygenerovaný kód zařízení. Vytvořená aplikace simuluje zařízení, které se připojuje ke službě IoT Hub. Aplikace odesílá telemetrie a vlastnosti a přijímá příkazy.

1. Vytvořte složku buildu **cmake** pro **sample_device** aplikaci:

    ```bash
    cd ~/pnp_app/sample_device
    mkdir cmake
    cd cmake
    ```

1. Spusťte CMake a sestavte aplikaci pomocí sady SDK. Následující příkaz předpokládá, že jste nainstalovali **vcpkg** do domovské složky:

    ```bash
    cmake .. -DCMAKE_TOOLCHAIN_FILE=~/vcpkg/scripts/buildsystems/vcpkg.cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    cmake --build .
    ```

1. Po úspěšném dokončení sestavení spusťte aplikaci předáním připojovacího řetězce zařízení služby IoT Hub jako parametru.

    ```sh
    cd ~/pnp_app/sample_device/cmake
    ./sample_device "<YourDeviceConnectionString>"
    ```

1. Aplikace zařízení spouští odesílání dat do IoT Hub.

    ![Aplikace pro zařízení je spuštěná.](media/quickstart-create-pnp-device-linux/device-app-running.png)

## <a name="validate-the-code"></a>Ověřit kód

### <a name="publish-device-model-files-to-model-repository"></a>Publikování souborů modelů zařízení do úložiště modelů

Chcete-li ověřit kód zařízení pomocí **AZ** CLI, je nutné publikovat soubory do úložiště modelu.

1. Když je složka `pnp_app` otevřená v sadě VS Code, pomocí **kombinace kláves CTRL + SHIFT + P** otevřete paletu příkazů, zadejte a vyberte **IoT plug & Play: odeslání souborů do úložiště modelu**.

1. Vyberte `SampleDevice.capabilitymodel.json` a `EnvironmentalSensor.interface.json` soubory.

1. Zadejte připojovací řetězec úložiště podnikového modelu.

    > [!NOTE]
    > Připojovací řetězec je vyžadován pouze při prvním připojení k úložišti.

1. V okně výstup VS Code a oznámení můžete ověřit, že soubory byly úspěšně publikovány.

    > [!NOTE]
    > Pokud při publikování souborů modelů zařízení dojde k chybám, můžete zkusit použít příkaz **IoT technologie Plug and Play:** odhlaste se a Projděte si kroky znovu.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>Použití rozhraní příkazového řádku Azure IoT k ověření kódu

Po spuštění ukázkového klienta zařízení můžete zjistit, jestli funguje s rozhraním příkazového řádku Azure CLI.

Pomocí následujícího příkazu můžete zobrazit telemetrii, kterou ukázkové zařízení odesílá. Před zobrazením jakékoli telemetrie ve výstupu možná budete muset počkat jednu nebo dvě minuty:

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubNme> --device-id <YourDeviceID>
```

Pomocí následujícího příkazu zobrazte všechny vlastnosti odesílané zařízením:

```azurecli-interactive
az iot dt list-properties --device-id <YourDeviceID> --hub-name <YourIoTHubNme> --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit zařízení IoT technologie Plug and Play pomocí DCM.

Pokud chcete získat další informace o DCMs a vytváření vlastních modelů, pokračujte v kurzu:

> [!div class="nextstepaction"]
> [Kurz: vytvoření a otestování modelu schopností zařízení pomocí Visual Studio Code](tutorial-pnp-visual-studio-code.md)
