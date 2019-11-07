---
title: Vytvoření zařízení IoT technologie Plug and Play Preview | Microsoft Docs
description: Pomocí modelu schopností zařízení vygenerujte kód zařízení. Pak spusťte kód zařízení a zkontrolujte, že se zařízení připojí k vašemu IoT Hub.
author: miagdp
ms.author: miag
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 654ebc6f40e6c365e9abf406ff19cd7269539dd8
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682225"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-windows"></a>Rychlý Start: vytvoření zařízení IoT technologie Plug and Play Preview pomocí modelu schopností zařízení (Windows)

_Model schopností zařízení_ (DCM) popisuje možnosti zařízení IoT technologie Plug and Play. DCM je často spojený s SKU produktu. Funkce definované v DCM jsou uspořádané do opakovaně použitelných rozhraní. Z DCM můžete vygenerovat kostru kódu zařízení. V tomto rychlém startu se dozvíte, jak pomocí VS Code v systému Windows vytvořit zařízení IoT technologie Plug and Play pomocí DCM.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu je potřeba na svůj místní počítač nainstalovat následující software:

* [Nástroje pro sestavení pro sadu Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) pomocí  **C++ nástrojů sestavení** a úloh **komponent správce balíčků NuGet** . Nebo pokud už máte [Visual Studio (Community, Professional nebo Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 nebo 2015 se stejnými úlohami.
* [Git](https://git-scm.com/download/)
* [Cmake](https://cmake.org/download/).
* [Visual Studio Code](https://code.visualstudio.com/).

### <a name="install-azure-iot-tools"></a>Instalace nástrojů Azure IoT

Pomocí následujících kroků nainstalujte sadu [nástrojů Azure IoT Tools for vs Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Extension Pack:

1. V VS Code vyberte kartu **rozšíření** .
1. Vyhledejte **nástroje Azure IoT Tools**.
1. Vyberte **Install** (Nainstalovat).

### <a name="install-the-azure-iot-explorer"></a>Instalace Azure IoT Exploreru

Stáhněte a nainstalujte si nástroj Azure IoT Explorer ze stránky [nejnovější verze](https://github.com/Azure/azure-iot-explorer/releases) .

### <a name="get-the-connection-string-for-your-company-model-repository"></a>Získání připojovacího řetězce pro úložiště podnikového modelu

_Připojovací řetězec úložiště modelů společnosti_ můžete najít na portálu [Azure Certified for IoT Portal](https://preview.catalog.azureiotsolutions.com) při přihlášení pomocí pracovního nebo školního účtu Microsoft nebo svého ID partnera Microsoftu, pokud ho máte. Po přihlášení vyberte **úložiště společnosti** a pak **připojovací řetězce**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>Příprava služby IoT Hub

K dokončení tohoto rychlého startu budete také potřebovat službu Azure IoT Hub v rámci vašeho předplatného Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

> [!NOTE]
> Během veřejné verze Preview jsou funkce IoT technologie Plug and Play dostupné jenom v centrech IoT vytvořených v oblastech **střed USA**, **Severní Evropa**a **Japonsko – východ** .

Přidejte Microsoft Azure rozšíření IoT pro Azure CLI:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Spuštěním následujícího příkazu vytvořte identitu zařízení ve službě IoT Hub. Zástupné symboly **YourIoTHubName** a **YourDevice** nahraďte vašimi skutečnými názvy. Pokud nemáte IoT Hub, [vytvořte ho podle těchto pokynů](../iot-hub/iot-hub-create-using-cli.md):

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id [YourDevice]
```

Spusťte následující příkazy a získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id [YourDevice] --output table
```

Spuštěním následujících příkazů Získejte _připojovací řetězec služby IoT Hub_ pro vaše centrum:

```azurecli-interactive
az iot hub show-connection-string --hub-name [YourIoTHubName] --output table
```

Poznamenejte si připojovací řetězec zařízení, který vypadá nějak takto:

```json
HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyCDevice;SharedAccessKey={YourSharedAccessKey}
```

Tuto hodnotu použijete později v rychlém startu.

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

### <a name="get-azure-iot-device-sdk-for-c"></a>Získat sadu SDK pro zařízení Azure IoT pro jazyk C

V tomto rychlém startu připravíte vývojové prostředí pomocí instalace sady SDK pro zařízení Azure IoT C přes [Vcpkg](https://github.com/microsoft/vcpkg).

1. Otevřete příkazový řádek. Spusťte následující příkaz pro instalaci Vcpkg:

    ```cmd/sh
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Pokud pak chcete připojit [integraci](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)na úrovni uživatele, spusťte příkaz (Poznámka: vyžaduje správce při prvním použití):

    ```cmd/sh
    .\vcpkg.exe integrate install
    ```

1. Instalace sady SDK pro zařízení Azure IoT C Vcpkg:

    ```cmd/sh
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="author-your-model"></a>Vytváření modelu

V tomto rychlém startu použijete existující Vzorový model zařízení a přidružená rozhraní.

1. Vytvořte `pnp_app` adresář na místním disku.

1. Stáhněte si [model funkce zařízení](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/SampleDevice.capabilitymodel.json) a [ukázku rozhraní](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/EnvironmentalSensor.interface.json) a uložte soubory do složky `pnp_app`.

    > [!TIP]
    > Pokud chcete stáhnout soubor z GitHubu, přejděte do souboru, klikněte pravým tlačítkem na **nezpracovaný**a pak vyberte **Uložit odkaz jako**.

1. Otevřete `pnp_app` složku pomocí VS Code. Můžete zobrazit soubory pomocí IntelliSense:

    ![Model schopností zařízení](media/quickstart-create-pnp-device/dcm.png)

1. V souborech, které jste stáhli, nahraďte `<YOUR_COMPANY_NAME_HERE>` v polích `@id` a `schema` jedinečnou hodnotou. Používejte pouze znaky a-z, A-Z, 0-9 a podtržítko. Další informace najdete v tématu [Formát digitálního Nevlákenového identifikátoru](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format).

## <a name="generate-the-c-code-stub"></a>Generovat zástupný kód kódu jazyka C

Teď máte modul DCM a jeho přidružená rozhraní, můžete vygenerovat kód zařízení, který model implementuje. Chcete-li vygenerovat zástupnou proceduru kódu C v VS Code:

1. Po otevření složky s otevřenými soubory DCM použijte **kombinaci kláves CTRL + SHIFT + P** k otevření palety příkazů, zadejte **IoT technologie Plug and Play**a vyberte možnost **Generovat zástupný kód zařízení**.

    > [!NOTE]
    > Při prvním použití rozhraní příkazového řádku IoT technologie Plug and Play CodeGen bude stažení a instalace automaticky trvat několik sekund.

1. Vyberte soubor DCM, který chcete použít k vygenerování zástupné procedury kódu zařízení.

1. Zadejte název projektu **sample_device**, bude to název vaší aplikace zařízení.

1. Jako jazyk vyberte **ANSI C** .

1. Jako metodu připojení vyberte **prostřednictvím IoT Hub připojovací řetězec zařízení** .

1. Vyberte **projekt cmake ve Windows** jako šablonu projektu.

1. Vyberte **prostřednictvím Vcpkg** jako způsob, jak zahrnout sadu SDK pro zařízení.

1. VS Code otevře nové okno s generovanými zástupnými soubory kódu zařízení.
    ![kód zařízení](media/quickstart-create-pnp-device/device-code.png)

## <a name="build-the-code"></a>Sestavení kódu

Vygenerovaný zástupný kód zařízení vytvoříte společně se sadou SDK pro zařízení. Vytvořená aplikace simuluje zařízení, které se připojuje ke službě IoT Hub. Aplikace odesílá telemetrie a vlastnosti a přijímá příkazy.

1. Vytvořte podadresář `cmake` ve složce `sample_device` a přejděte do této složky:

    ```cmd\sh
    mkdir cmake
    cd cmake
    ```

1. Spuštěním následujících příkazů Sestavte zástupnou proceduru generovaného kódu:

    ```cmd\sh
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"

    cmake --build .
    ```
    
    > [!NOTE]
    > Pokud používáte sadu Visual Studio 2017 nebo 2015, je nutné určit generátor CMake na základě nástrojů pro sestavení, které používáte:
    >```cmd\sh
    ># Either
    >cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    ># or
    >cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    >```

    > [!NOTE]
    > Pokud cmake nenajde váš C++ kompilátor, při spuštění předchozího příkazu se zobrazí chyby sestavení. Pokud k tomu dojde, zkuste spustit tento příkaz na příkazovém [řádku sady Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

1. Po úspěšném dokončení sestavení spusťte aplikaci předáním připojovacího řetězce zařízení služby IoT Hub jako parametru.

    ```cmd\sh
    .\Debug\sample_device.exe "[IoT Hub device connection string]"
    ```

1. Aplikace zařízení spouští odesílání dat do IoT Hub.

    ![Aplikace pro zařízení je spuštěná.](media/quickstart-create-pnp-device/device-app-running.png)

## <a name="validate-the-code"></a>Ověřit kód

### <a name="publish-device-model-files-to-model-repository"></a>Publikování souborů modelů zařízení do úložiště modelů

K ověření kódu zařízení v **Azure IoT Exploreru**je potřeba publikovat soubory do úložiště modelu.

1. Po otevření složky se soubory DCM použijte **kombinaci kláves CTRL + SHIFT + P** a otevřete paletu příkazů, zadejte a vyberte **IoT plug & Play: odeslání souborů do úložiště modelu**.

1. Vyberte `SampleDevice.capabilitymodel.json` a `EnvironmentalSensor.interface.json` soubory.

1. Zadejte připojovací řetězec úložiště podnikového modelu.

    > [!NOTE]
    > Připojovací řetězec je vyžadován pouze při prvním připojení k úložišti.

1. V okně výstup VS Code a oznámení můžete ověřit, zda byly soubory úspěšně publikovány.

    > [!NOTE]
    > Pokud při publikování souborů modelů zařízení dojde k chybám, můžete zkusit použít příkaz **IoT technologie Plug and Play:** odhlaste se a Projděte si kroky znovu.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Použití Průzkumníka Azure IoT k ověření kódu

1. Otevřete Azure IoT Explorer a zobrazí se stránka **Konfigurace aplikace** .

1. Zadejte připojovací řetězec IoT Hub a klikněte na **připojit**.

1. Po připojení se zobrazí stránka s přehledem zařízení.

1. Pokud chcete přidat úložiště společnosti, vyberte **Nastavení**, pak **+ Nový**a pak na **úložiště společnosti**.

1. Přidejte připojovací řetězec úložiště podnikového modelu. Vyberte **Connect** (Připojit).

1. Na stránce Přehled zařízení vyhledejte identitu zařízení, kterou jste vytvořili dříve, a vyberte ji, abyste zobrazili další podrobnosti.

1. Rozbalením rozhraní s ID **urn: azureiot: EnvironmentalSensor: 1** zobrazíte technologie Plug and Play primitivních elementů IoT – vlastnosti, příkazy a telemetrie.

1. Výběrem stránky **telemetrie** zobrazíte data telemetrie, která zařízení odesílá.

1. Vyberte stránku **vlastnosti (bez možnosti zápisu)** , ve které se zobrazí nezapisovatelné vlastnosti hlášené zařízením.

1. Výběrem stránky **vlastnosti (zapisovatelné)** zobrazíte vlastnosti, které můžete aktualizovat.

1. Rozbalte **název**vlastnosti, aktualizujte ho novým názvem a vyberte možnost **aktualizovat zapisovatelnou vlastnost**.

1. Chcete-li zobrazit nový název zobrazený ve sloupci **nahlášená vlastnost** , klikněte na tlačítko **aktualizovat** v horní části stránky.

1. Výběrem stránky **příkazů** zobrazíte všechny příkazy, které zařízení podporuje.

1. Rozbalte příkaz **Blink** a nastavte nový časový interval pro blikání. Vyberte **Odeslat příkaz** pro volání příkazu na zařízení.

1. Přejít na simulované zařízení a ověřte, že se příkaz provedl podle očekávání.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit zařízení IoT technologie Plug and Play pomocí DCM.

Další informace o službě IoT technologie Plug and Play najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Vytvoření a otestování modelu schopností zařízení pomocí Visual Studio Code](tutorial-pnp-visual-studio-code.md)
