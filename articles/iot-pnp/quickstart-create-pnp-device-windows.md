---
title: Vytvoření zařízení Náhled technologie IoT Plug and Play (Windows) | Dokumenty společnosti Microsoft
description: Ke generování kódu zařízení použijte model schopností zařízení. Pak spusťte kód zařízení a podívejte se, jak se zařízení připojuje k centru IoT Hub.
author: miagdp
ms.author: miag
ms.date: 12/30/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: e80194f53a406b8b378d0fb787df627937125a27
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75867492"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-windows"></a>Úvodní příručka: Vytvoření zařízení Náhled plug and play ioT pomocí modelu schopností zařízení

[!INCLUDE [iot-pnp-quickstarts-1-selector.md](../../includes/iot-pnp-quickstarts-1-selector.md)]

_Model schopností zařízení_ (DCM) popisuje možnosti zařízení IoT Plug and Play. DCM je často spojena s produktem Skladové položky. Funkce definované v DCM jsou uspořádány do opakovaně použitelných rozhraní. Můžete generovat kód kostry zařízení z DCM. Tento rychlý start ukazuje, jak pomocí kódu VS v systému Windows vytvořit zařízení IoT Plug and Play pomocí DCM.

## <a name="prerequisites"></a>Požadavky

Chcete-li tento rychlý start dokončit, je třeba do místního počítače nainstalovat následující software:

* [Vytvářejte nástroje pro Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) s nástroji pro sestavení **C++** a úlohami **součástí správce balíčků NuGet.** Nebo pokud už máte [Visual Studio (Community, Professional nebo Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 nebo 2015 se stejnými nainstalovanými úlohami.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).
* [Visual Studio kód](https://code.visualstudio.com/).

### <a name="install-azure-iot-tools"></a>Instalace nástrojů Azure IoT

Pomocí následujících kroků nainstalujte rozšiřující balíček [Azure IoT Tools for VS Code:](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

1. V kódu VS vyberte kartu **Rozšíření.**
1. Vyhledejte **nástroje Azure IoT .**
1. Vyberte **Install** (Nainstalovat).

### <a name="install-the-azure-iot-explorer"></a>Instalace Průzkumníka Azure IoT

Stáhněte a nainstalujte nejnovější verzi **Průzkumníka Azure IoT** ze stránky [úložiště](https://github.com/Azure/azure-iot-explorer/releases) nástroje výběrem souboru MSI v části "Prostředky" pro nejnovější aktualizaci.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>Získání připojovacího řetězce pro úložiště firemních modelů

Řetězec připojení _úložiště firemního modelu_ najdete na portálu [portálu Azure Certified for IoT,](https://preview.catalog.azureiotsolutions.com) když se přihlásíte pomocí pracovního nebo školního účtu Microsoftu, nebo pomocí ID partnera Microsoftu, pokud ho máte. Po přihlášení vyberte **Úložiště společnosti** a potom **připojovací řetězce**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Spusťte následující příkaz, abyste získali _připojovací řetězec centra IoT hub_ pro vaše centrum (poznámka pro pozdější použití):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

V tomto rychlém startu použijete správce knihovny [Vcpkg](https://github.com/microsoft/vcpkg) k instalaci sady Azure IoT C device SDK ve vývojovém prostředí.

1. Otevřete příkazový řádek. Chcete-li nainstalovat vcpkg, proveďte následující příkaz:

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Chcete-li připojit [integraci](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)pro celý uživatel , spusťte následující (poznámka: vyžaduje správce při prvním použití):

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. Instalace zařízení Azure IoT C SDK Vcpkg:

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="author-your-model"></a>Vytvoření modelu

V tomto rychlém startu použijete existující ukázkový model schopností zařízení a přidružená rozhraní.

1. Vytvořte `pnp_app` složku na místní jednotce. Tuto složku použijete pro soubory modelu zařízení a se zakázaným inzerováním kódu zařízení.

1. Stáhněte si [model schopností zařízení a ukázkové soubory rozhraní](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/SampleDevice.capabilitymodel.json) a [ukázku rozhraní](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/EnvironmentalSensor.interface.json) a uložte soubory do `pnp_app` složky.

    > [!TIP]
    > Chcete-li stáhnout soubor z GitHubu, přejděte na soubor, klikněte pravým tlačítkem myši na **nezpracovaný**a vyberte **uložit odkaz jako**.

1. Otevřete `pnp_app` složku s kódem VS. Soubory můžete zobrazit pomocí technologie IntelliSense:

    ![Model schopností zařízení](media/quickstart-create-pnp-device/dcm.png)

1. V stažených souborech `<YOUR_COMPANY_NAME_HERE>` nahraďte v polích `@id` a `schema` jedinečnou hodnotu. Používejte pouze znaky a-z, A-Z, 0-9 a podtržítko. Další informace naleznete v [tématu Digital Twin identifier format](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format).

## <a name="generate-the-c-code-stub"></a>Generovat se zakázaným inzerováním kódu C

Nyní, když máte DCM a jeho přidružené rozhraní, můžete vygenerovat kód zařízení, který implementuje model. Chcete-li generovat kód C se zakázaným inzerováním v kódu VS:

1. Když `pnp_app` je složka otevřená v kódu VS, otevřete paletu příkazů **pomocí ctrl+shift+p,** zadejte **IoT Plug and Play**a vyberte Generovat kód **zařízení**.

    > [!NOTE]
    > Při prvním použití rozhraní CLI CodeGen Technologie IoT Plug and Play trvá několik sekund, než se automaticky stáhne a nainstaluje.

1. Zvolte **sampledevice.capabilitymodel.json** soubor použít pro generování kódu zařízení se zakázaným inzerováním.

1. Zadejte název projektu **sample_device**. Toto je název aplikace zařízení.

1. Jako jazyk zvolte **ANSI C.**

1. Jako metodu připojení zvolte **připojovací řetězec zařízení Služby Via IoT Hub.**

1. Jako šablonu projektu zvolte **CMake Project ve Windows.**

1. Jako způsob zahrnutí sady SDK zařízení zvolte **Via Vcpkg.**

1. Nová složka s názvem **sample_device** je vytvořena ve stejném umístění jako soubor DCM a v něm jsou generované soubory se zakázaným inzerováním kódu zařízení. VS Kód otevře nové okno pro zobrazení těchto.
    ![Kód zařízení](media/quickstart-create-pnp-device/device-code.png)

## <a name="build-and-run-the-code"></a>Sestavení a spuštění kódu

Balíček Vcpkg slouží k vytvoření kódu generovaného zařízení se zakázaným inzerováním. Aplikace, kterou vytvoříte, simuluje zařízení, které se připojuje k centru IoT. Aplikace odesílá telemetrii a vlastnosti a přijímá příkazy.

1. Vytvořte `cmake` podadresář `sample_device` ve složce a přejděte do této složky:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Spusťte následující příkazy k vytvoření protokolu se zakázaným inzerováním generovaného kódu (nahrazení zástupného symbolu adresářem úložiště Vcpkg):

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build .
    ```
    
    > [!NOTE]
    > Pokud používáte Visual Studio 2017 nebo 2015, je třeba zadat generátor CMake na základě nástrojů sestavení, které používáte:
    >```cmd
    ># Either
    >cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    ># or
    >cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    >```

    > [!NOTE]
    > Pokud cmake nemůže najít kompilátor Jazyka C++, zobrazí se chyby sestavení při spuštění předchozího příkazu. Pokud k tomu dojde, zkuste spustit tento příkaz na [příkazovém řádku sady Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

1. Po úspěšném dokončení sestavení spusťte aplikaci a předejte jako parametr připojovací řetězec zařízení služby IoT hub.

    ```cmd\sh
    .\Debug\sample_device.exe "<YourDeviceConnectionString>"
    ```

1. Aplikace zařízení spustí odesílání dat do služby IoT Hub.

    ![Spuštěná aplikace zařízení](media/quickstart-create-pnp-device/device-app-running.png)

## <a name="validate-the-code"></a>Ověřit kód

### <a name="publish-device-model-files-to-model-repository"></a>Publikování souborů modelů zařízení do úložiště modelů

Chcete-li ověřit kód zařízení pomocí **Průzkumníka Azure IoT ,** musíte publikovat soubory do úložiště modelu.

1. Když `pnp_app` je složka otevřená v Kódu VS, otevřete paletu příkazů **pomocí ctrl+shift+p,** zadejte a vyberte **IoT Plug & Play: Odeslat soubory do úložiště modelů**.

1. Výběr `SampleDevice.capabilitymodel.json` `EnvironmentalSensor.interface.json` a soubory.

1. Zadejte připojovací řetězec úložiště firemního modelu.

    > [!NOTE]
    > Připojovací řetězec je vyžadován pouze při prvním připojení k úložišti.

1. Ve výstupním okně kódu VS a oznámení můžete zkontrolovat, zda byly soubory úspěšně publikovány.

    > [!NOTE]
    > Pokud se při publikování souborů modelu zařízení zoáhnou chyby, můžete zkusit použít příkaz **IoT Plug and Play: Odhlásit úložiště modelů,** abyste se odhlásit a znovu provedli kroky.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Ověření kódu pomocí průzkumníka Azure IoT

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Chcete-li přidat firemní úložiště, vyberte **Nastavení**, potom **+ Přidat zdroj definice modulu**, pak **firemní úložiště**. Přidejte připojovací řetězec úložiště firemního modelu a vyberte **Uložit a připojit**.

1. Zpět na stránce **Přehled zařízení** najděte identitu zařízení, kterou jste vytvořili dříve. Pokud je aplikace zařízení stále spuštěná v příkazovém řádku, zkontrolujte, jestli **stav připojení** zařízení v průzkumníku Azure IoT hlásí jako _Připojený_ (pokud ne, stiskněte **tlačítko Aktualizovat,** dokud nebude). Výběrem zařízení zobrazíte další podrobnosti.

1. Rozbalte rozhraní s ID **urn:<YOUR_INTERFACE_NAME>:EnvironmentalSensor:1** zobrazíte základní prvky IoT Plug and Play – vlastnosti, příkazy a telemetrii. Název rozhraní, který se zobrazí, je název, který vložíte při vytváření modelu.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak vytvořit zařízení IoT Plug and Play pomocí DCM.

Chcete-li se dozvědět více o DCMs a jak vytvořit vlastní modely, pokračujte v kurzu:

> [!div class="nextstepaction"]
> [Kurz: Vytvoření a testování modelu schopností zařízení pomocí kódu Sady Visual Studio](tutorial-pnp-visual-studio-code.md)
