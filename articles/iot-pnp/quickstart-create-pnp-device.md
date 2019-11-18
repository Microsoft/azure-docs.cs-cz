---
title: Vytvoření zařízení IoT technologie Plug and Play Preview (Windows) | Microsoft Docs
description: Pomocí modelu schopností zařízení vygenerujte kód zařízení. Pak spusťte kód zařízení a zkontrolujte, že se zařízení připojí k vašemu IoT Hub.
author: miagdp
ms.author: miag
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 4af238241293f32be296e7a4243b0d2a6fef15dd
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152033"
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

Stáhněte a nainstalujte si nejnovější verzi **Azure IoT Exploreru** ze stránky [úložiště](https://github.com/Azure/azure-iot-explorer/releases) tohoto nástroje tak, že vyberete soubor. msi v části assets (prostředky) pro nejnovější aktualizaci.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>Získání připojovacího řetězce pro úložiště podnikového modelu

_Připojovací řetězec úložiště modelů společnosti_ můžete najít na portálu [Azure Certified for IoT Portal](https://preview.catalog.azureiotsolutions.com) při přihlášení pomocí pracovního nebo školního účtu Microsoft nebo svého ID partnera Microsoftu, pokud ho máte. Po přihlášení vyberte **úložiště společnosti** a pak **připojovací řetězce**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub-windows.md](../../includes/iot-pnp-prepare-iot-hub-windows.md)]

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

### <a name="get-azure-iot-device-sdk-for-c"></a>Získat sadu SDK pro zařízení Azure IoT pro jazyk C

V tomto rychlém startu připravíte vývojové prostředí pomocí instalace sady SDK pro zařízení Azure IoT C přes [Vcpkg](https://github.com/microsoft/vcpkg).

1. Otevřete příkazový řádek. Spusťte následující příkaz pro instalaci Vcpkg:

    ```cmd/sh
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Pokud pak chcete připojit [integraci](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)uživatelů na úrovni uživatele, spusťte následující příkaz (Poznámka: vyžaduje správce při prvním použití):

    ```cmd/sh
    .\vcpkg.exe integrate install
    ```

1. Instalace sady SDK pro zařízení Azure IoT C Vcpkg:

    ```cmd/sh
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="author-your-model"></a>Vytváření modelu

V tomto rychlém startu použijete existující Vzorový model zařízení a přidružená rozhraní.

1. Vytvořte `pnp_app` adresář na místním disku. Tuto složku použijete pro soubory modelů zařízení a pro zástupné kódy zařízení.

1. Stáhněte si [model funkce zařízení a ukázkové soubory rozhraní](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/SampleDevice.capabilitymodel.json) a [ukázky](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/EnvironmentalSensor.interface.json) rozhraní a uložte soubory do složky `pnp_app`.

    > [!TIP]
    > Pokud chcete stáhnout soubor z GitHubu, přejděte do souboru, klikněte pravým tlačítkem na **nezpracovaný**a pak vyberte **Uložit odkaz jako**.

1. Otevřete `pnp_app` složku pomocí VS Code. Můžete zobrazit soubory pomocí IntelliSense:

    ![Model schopností zařízení](media/quickstart-create-pnp-device/dcm.png)

1. V souborech, které jste stáhli, nahraďte `<YOUR_COMPANY_NAME_HERE>` v polích `@id` a `schema` jedinečnou hodnotou. Používejte pouze znaky a-z, A-Z, 0-9 a podtržítko. Další informace najdete v tématu [Formát digitálního Nevlákenového identifikátoru](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format).

## <a name="generate-the-c-code-stub"></a>Generovat zástupný kód kódu jazyka C

Teď, když máte modul DCM a jeho přidružená rozhraní, můžete vygenerovat kód zařízení, který model implementuje. Chcete-li vygenerovat zástupnou proceduru kódu C v VS Code:

1. Když je složka `pnp_app` v VS Code otevřená, pomocí **kombinace kláves CTRL + SHIFT + P** otevřete paletu příkazů, zadejte **IoT technologie Plug and Play**a vyberte **Generovat zástupný kód zařízení**.

    > [!NOTE]
    > Při prvním použití rozhraní příkazového řádku IoT technologie Plug and Play CodeGen bude stažení a instalace automaticky trvat několik sekund.

1. Vyberte soubor **SampleDevice. capabilitymodel. JSON** , který se použije k vygenerování zástupné procedury pro kód zařízení.

1. Zadejte název projektu **sample_device**. Toto bude název aplikace vašeho zařízení.

1. Jako jazyk vyberte **ANSI C** .

1. Jako metodu připojení vyberte **prostřednictvím IoT Hub připojovací řetězec zařízení** .

1. Jako šablonu projektu vyberte možnost **projekt cmake v systému Windows** .

1. Vyberte **prostřednictvím Vcpkg** jako způsob, jak zahrnout sadu SDK pro zařízení.

1. Nová složka s názvem **sample_device** se vytvoří ve stejném umístění jako soubor DCM a v takovém případě se jedná o vygenerované soubory zástupných procedur kódu zařízení. VS Code otevře nové okno, ve kterém se zobrazí.
    ![kód zařízení](media/quickstart-create-pnp-device/device-code.png)

## <a name="build-and-run-the-code"></a>Sestavit a spustit kód

Zdrojový kód sady SDK pro zařízení slouží k sestavení zástupné procedury pro vygenerovaný kód zařízení. Vytvořená aplikace simuluje zařízení, které se připojuje ke službě IoT Hub. Aplikace odesílá telemetrie a vlastnosti a přijímá příkazy.

1. Vytvořte podadresář `cmake` ve složce `sample_device` a přejděte do této složky:

    ```cmd\sh
    mkdir cmake
    cd cmake
    ```

1. Spuštěním následujících příkazů Sestavte zástupnou proceduru vygenerovaného kódu (zástupný symbol nahraďte adresářem úložiště Vcpkg):

    ```cmd\sh
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

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

1. Po úspěšném dokončení sestavení spusťte aplikaci a jako parametr předejte připojovací řetězec zařízení služby IoT Hub.

    ```cmd\sh
    .\Debug\sample_device.exe "<device connection string>"
    ```

1. Aplikace zařízení spouští odesílání dat do IoT Hub.

    ![Aplikace pro zařízení je spuštěná.](media/quickstart-create-pnp-device/device-app-running.png)

## <a name="validate-the-code"></a>Ověřit kód

### <a name="publish-device-model-files-to-model-repository"></a>Publikování souborů modelů zařízení do úložiště modelů

K ověření kódu zařízení v **Azure IoT Exploreru**je potřeba publikovat soubory do úložiště modelu.

1. Když je složka `pnp_app` v VS Code otevřená, pomocí **kombinace kláves CTRL + SHIFT + P** otevřete paletu příkazů, zadejte a vyberte **IoT plug & Play: odešlete soubory do úložiště modelů**.

1. Vyberte `SampleDevice.capabilitymodel.json` a `EnvironmentalSensor.interface.json` soubory.

1. Zadejte připojovací řetězec úložiště podnikového modelu.

    > [!NOTE]
    > Připojovací řetězec je vyžadován pouze při prvním připojení k úložišti.

1. V okně výstup VS Code a oznámení můžete ověřit, že soubory byly úspěšně publikovány.

    > [!NOTE]
    > Pokud při publikování souborů modelů zařízení dojde k chybám, můžete zkusit použít příkaz **IoT technologie Plug and Play:** odhlaste se a Projděte si kroky znovu.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Použití Průzkumníka Azure IoT k ověření kódu

1. Otevřete Azure IoT Explorer. Zobrazí se stránka **Konfigurace aplikace** .

1. Zadejte _připojovací řetězec IoT Hub_ a vyberte **připojit**.

1. Po připojení se zobrazí stránka s přehledem **zařízení** .

1. Pokud chcete přidat úložiště společnosti, vyberte **Nastavení**, potom **+ Přidat zdroj definice modulu**a pak **úložiště společnosti**. Přidejte připojovací řetězec úložiště modelu společnosti a vyberte **Uložit a připojit**.

1. Zpátky na stránce Přehled **zařízení** vyhledejte identitu zařízení, kterou jste vytvořili dříve. Když je aplikace zařízení pořád spuštěná na příkazovém řádku, ověřte, že se **stav připojení** zařízení v Azure IoT Exploreru hlásí jako _připojené_ (Pokud ne, stiskněte **aktualizovat** , dokud není). Kliknutím na zařízení zobrazíte další podrobnosti.

1. Rozbalte rozhraní s ID **urn: < YOUR_INTERFACE_NAME >: EnvironmentalSensor: 1** , abyste viděli primitivy technologie Plug and Play IoT – vlastnosti, příkazy a telemetrie. Název rozhraní, který se zobrazí, je název, který vložíte při vytváření modelu.

1. Vyberte stránku **telemetrie** a _začněte_ zobrazit data telemetrie, která zařízení odesílá.

1. Vyberte stránku **vlastnosti (bez možnosti zápisu)** , ve které se zobrazí nezapisovatelné vlastnosti hlášené zařízením.

1. Výběrem stránky **vlastnosti (zapisovatelné)** zobrazíte vlastnosti, které můžete aktualizovat.

1. Rozbalte **název**vlastnosti, aktualizujte ho novým názvem a vyberte možnost **aktualizovat zapisovatelnou vlastnost**. 

1. Pokud chcete zobrazit nový název ve sloupci **hlášené vlastnosti** , vyberte tlačítko **aktualizovat** v horní části stránky.

1. Výběrem stránky **příkazy** zobrazíte všechny příkazy, které zařízení podporuje.

1. Rozbalte příkaz **Blink** a nastavte nový časový interval pro blikání. Vyberte **Odeslat příkaz** pro volání příkazu na zařízení.

1. Přejít na příkazový řádek simulovaného zařízení a přečtěte si tištěné potvrzovací zprávy, abyste ověřili, že příkazy byly provedeny podle očekávání.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit zařízení IoT technologie Plug and Play pomocí DCM.

Pokud chcete získat další informace o DCMs a vytváření vlastních modelů, pokračujte v kurzu:

> [!div class="nextstepaction"]
> [Kurz: vytvoření a otestování modelu schopností zařízení pomocí Visual Studio Code](tutorial-pnp-visual-studio-code.md)
