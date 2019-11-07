---
title: Vytvoření zařízení Azure IoT technologie Plug and Play Preview (Linux) | Microsoft Docs
description: Pomocí modelu schopností zařízení vygenerujte kód zařízení. Pak spusťte kód zařízení a zkontrolujte, že se zařízení připojí k vašemu IoT Hub.
author: dominicbetts
ms.author: dobett
ms.date: 09/10/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 203725ba109922a8704c0e31a6e61dc6eadf6bd9
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73586632"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-linux"></a>Rychlý Start: použití modelu schopností zařízení k vytvoření zařízení IoT technologie Plug and Play Preview (Linux)

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

## <a name="prepare-an-iot-hub"></a>Příprava služby IoT Hub

K dokončení tohoto rychlého startu potřebujete Azure IoT Hub v rámci vašeho předplatného Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Pokud používáte Azure CLI místně, `az` verze by měla být **2.0.75** nebo novější, Azure Cloud Shell používá nejnovější verzi. Pomocí příkazu `az --version` ověřte verzi nainstalovanou na vašem počítači.

Přidejte Microsoft Azure rozšíření IoT pro Azure CLI:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Postup v tomto rychlém startu vyžaduje rozšíření **0.8.5** nebo novější. Pomocí příkazu `az extension list` ověřte verzi, kterou jste nainstalovali, a příkaz `az extension update`, který se v případě potřeby aktualizuje.

Pokud nemáte centrum IoT, vytvořte ho pomocí následujících příkazů a nahraďte `{YourIoTHubName}` jedinečným názvem podle vašeho výběru. Pokud tyto příkazy spouštíte lokálně, přihlaste se ke svému předplatnému Azure pomocí `az login`. Pokud tyto příkazy spouštíte ve službě Azure Cloud Shell, jste přihlášeni automaticky:

  ```azurecli-interactive
  az group create --name pnpquickstarts_rg --location centralus
  az iot hub create --name {YourIoTHubName} \
    --resource-group pnpquickstarts_rg --sku S1
  ```

Předchozí příkazy vytvoří skupinu prostředků s názvem `pnpquickstarts_rg` a centrum IoT v oblasti USA – střed.

> [!IMPORTANT]
> Během veřejné verze Preview jsou funkce IoT technologie Plug and Play dostupné jenom v centrech IoT vytvořených v oblastech **střed USA**, **Severní Evropa**a **Japonsko – východ** .

Spuštěním následujícího příkazu vytvořte identitu zařízení pro zařízení s názvem `mypnpdevice` ve službě IoT Hub. Zástupný text `{YourIoTHubName}` nahraďte názvem vašeho centra IoT:

```azurecli-interactive
az iot hub device-identity create --hub-name {YourIoTHubName} --device-id mypnpdevice
```

Spuštěním následujících příkazů Získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali. Tento připojovací řetězec budete potřebovat později v tomto rychlém startu:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id mypnpdevice --output table
```

## <a name="author-your-model"></a>Vytváření modelu

V tomto rychlém startu použijete existující Vzorový model zařízení a přidružená rozhraní.

1. Vytvořte `pnp_app` adresář v domovském adresáři. Tuto složku použijete pro soubory modelů zařízení a pro zástupné kódy zařízení.

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

Teď máte modul DCM a jeho přidružená rozhraní, můžete vygenerovat kód zařízení, který model implementuje. Chcete-li vygenerovat zástupnou proceduru kódu C v VS Code:

1. Když je složka `pnp_app` otevřená v sadě VS Code, pomocí **kombinace kláves CTRL + SHIFT + P** otevřete paletu příkazů, zadejte **IoT technologie Plug and Play**a vyberte **vygenerovat zástupnou proceduru pro kód zařízení**.

    > [!NOTE]
    > Když použijete nástroj pro generátor kódu IoT technologie Plug and Play poprvé, bude stažení trvat několik sekund.

1. Vyberte soubor **SampleDevice. capabilitymodel. JSON** , který se použije k vygenerování zástupné procedury kódu zařízení.

1. Zadejte název projektu **sample_device**, bude to název vaší aplikace zařízení.

1. Jako jazyk vyberte **ANSI C** .

1. Jako metodu připojení vyberte **prostřednictvím IoT Hub připojovací řetězec zařízení** .

1. Jako šablonu projektu vyberte **projekt cmake v systému Linux** .

1. Vyberte **zdrojový kód** jako způsob, jak zahrnout sadu SDK.

1. VS Code otevře nové okno s generovanými zástupnými soubory kódu zařízení.
    ![kód zařízení](media/quickstart-create-pnp-device-linux/device-code.png)

## <a name="build-and-run-the-code"></a>Sestavit a spustit kód

Zdrojový kód sady SDK pro zařízení slouží k sestavení zástupné procedury pro vygenerovaný kód zařízení. Vytvořená aplikace simuluje zařízení, které se připojuje ke službě IoT Hub. Aplikace odesílá telemetrie a vlastnosti a přijímá příkazy.

1. Spuštěním následujících příkazů Stáhněte zdrojový kód sady SDK pro zařízení:

    ```bash
    cd ~/pnp_app/sample_device
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

1. Vytvořte složku buildu **cmake** pro aplikaci **sample_device** :

    ```bash
    cd ~/pnp_app/sample_device
    mkdir cmake
    cd cmake
    ```

1. Spusťte CMake a sestavte aplikaci pomocí sady SDK:

    ```bash
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -Dskip_samples:BOOL=ON
    cmake --build .
    ```

1. Po úspěšném dokončení sestavení spusťte aplikaci předáním připojovacího řetězce zařízení služby IoT Hub jako parametru.

    ```sh
    cd ~/pnp_app/sample_device/cmake
    ./sample_device "{IoT Hub device connection string}"
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

1. V okně výstup VS Code a oznámení můžete ověřit, zda byly soubory úspěšně publikovány.

    > [!NOTE]
    > Pokud při publikování souborů modelů zařízení dojde k chybám, můžete zkusit použít příkaz **IoT technologie Plug and Play:** odhlaste se a Projděte si kroky znovu.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>Použití rozhraní příkazového řádku Azure IoT k ověření kódu

Po spuštění ukázky klienta zařízení můžete zjistit, jestli funguje s Azure CLI.

Pomocí následujícího příkazu můžete zobrazit telemetrii, kterou ukázkové zařízení odesílá. Před zobrazením jakékoli telemetrie ve výstupu možná budete muset počkat jednu nebo dvě minuty:

```azurecli-interactive
az iot dt monitor-events --hub-name {your IoT hub} --device-id mypnpdevice
```

Pomocí následujícího příkazu zobrazte všechny vlastnosti odesílané zařízením:

```azurecli-interactive
az iot dt list-properties --device-id mypnpdevice --hub-name {Your IoT hub name} --source private --repo-login "{Your company model repository connection string}"
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit zařízení IoT technologie Plug and Play pomocí DCM.

Pokud chcete získat další informace o DCMs a vytváření vlastních modelů, pokračujte v kurzu:

> [!div class="nextstepaction"]
> [Kurz: Vytvoření testovacího modelu schopností zařízení pomocí Visual Studio Code](tutorial-pnp-visual-studio-code.md)
