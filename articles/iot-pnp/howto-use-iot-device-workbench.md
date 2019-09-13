---
title: Použití aplikace Workbench pro zařízení Azure IoT k vytváření technologie Plug and Play ve verzi Preview | Microsoft Docs
description: Pomocí rozšíření Azure IoT Device Workbench v Visual Studio Code urychlíte tvorbu modelů zařízení IoT technologie Plug and Play a implementací kódu zařízení.
author: liydu
ms.author: liydu
ms.date: 07/25/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b9f091caaaf85cd2b999db7781b14a497474f564
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881257"
---
# <a name="use-azure-iot-device-workbench-extension-in-visual-studio-code"></a>Použijte rozšíření Azure IoT Device Workbench v Visual Studio Code

Rozšíření Visual Studio Code Azure IoT Device Workbench poskytuje integrované prostředí pro tvorbu modelů funkcí zařízení (DCM) a rozhraní, publikování v úložištích modelů a generování kostry kódu C k implementaci aplikace zařízení.

V tomto článku se dozvíte, jak:

- Generování kódu zařízení a projektu aplikace.
- Použijte vygenerovaný kód v projektu zařízení.
- Iterujte tak, že znovu vygenerujete kostru kódu.

Další informace o používání rozšíření zařízení Workbench pro vývoj zařízení IoT najdete v tématu [https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench).

## <a name="prerequisites"></a>Požadavky

Nainstalujte [Visual Studio Code](https://code.visualstudio.com/).

K instalaci rozšíření v VS Code použijte následující postup.

1. V VS Code vyberte kartu **rozšíření** .
1. Vyhledejte a nainstalujte si aplikaci **Azure IoT Device Workbench** z webu Marketplace.

## <a name="generate-device-code-and-project"></a>Generovat kód zařízení a projekt

V VS Code pomocí **kombinace kláves CTRL + SHIFT + P** otevřete paletu příkazů, zadejte **IoT technologie Plug and Play**a vyberte možnost **Generovat zástupný kód zařízení** pro konfiguraci kostry kódu a typu projektu. Následující seznam podrobně popisuje jednotlivé kroky:

- **Soubor DCM, který se má použít pro generování kódu**. Chcete-li vygenerovat kód zařízení kostry, otevřete složku obsahující soubory DCM a rozhraní, které implementuje. Pokud generátor kódu nenajde rozhraní, které modul DCM vyžaduje, stáhne ho z úložiště modelu podle potřeby.

- **Jazyk kódu zařízení**. V současné době generátor kódu podporuje pouze ANSI C.

- **Název projektu** Název projektu se používá jako název složky pro generovaný kód a další soubory projektu. Ve výchozím nastavení je složka umístěná vedle souboru DCM. Chcete-li se vyhnout nutnosti ručnímu kopírování vygenerované složky kódu vždy, když aktualizujete DCM a znovu vygenerujete kód zařízení, zachovejte soubor DCM ve stejné složce jako složka projektu.

- **Typ projektu**. Generátor kódu také generuje soubor projektu, aby bylo možné integrovat kód do vlastního projektu nebo do projektu sady SDK pro zařízení. V současné době jsou podporované typy projektů:

    - **Projekt cmake**: pro projekt zařízení, který používá [cmake](https://cmake.org/) jako systém sestavení. Tato možnost vygeneruje `CMakeLists.txt` soubor ve stejné složce jako kód jazyka C.
    - **MXChip IoT DevKit Project**: pro projekt zařízení, který běží na zařízení [MXChip IoT DevKit](https://aka.ms/iot-devkit) . Tato možnost generuje projekt Arduino, který můžete [použít v vs Code](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) nebo v integrovaném vývojovém prostředí Arduino pro sestavování a spouštění na zařízení IoT DevKit.

- **Způsob připojení ke službě Azure IoT**. Vygenerované soubory také obsahují kód pro konfiguraci zařízení pro připojení k Azure IoT Hub. Můžete se rozhodnout připojit se přímo k [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) nebo použít [službu Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps).

    - **Přes IoT Hub připojovací řetězec zařízení**: zadejte připojovací řetězec zařízení, pro který se má aplikace zařízení připojit IoT Hub přímo.
    - **Přes DPS symetrický klíč**: zadejte **ID oboru**, **ID registrace**a **klíč SAS** pro aplikaci zařízení, která je nutná pro připojení k IoT Hub nebo IoT Central pomocí DPS.

Generátor kódu se pokusí použít soubory DCM a rozhraní umístěné v místní složce. Pokud soubory rozhraní nejsou v místní složce, generátor kódu je vyhledá v úložišti veřejného modelu nebo v úložišti modelu společnosti. [Společné soubory rozhraní](./concepts-common-interfaces.md) jsou uloženy v úložišti veřejného modelu.

Po dokončení generování kódu rozšíření otevře nové okno VS Code s kódem. Otevřete-li vygenerovaný soubor, například **Main. c**, může se stát, že technologie IntelliSense ohlásí, že nemůže otevřít zdrojové soubory sady c SDK. Chcete-li povolit správnou navigaci pomocí technologie IntelliSense a kódu, použijte následující postup pro zahrnutí zdroje C SDK:

1. V VS Code pomocí **kombinace kláves CTRL + SHIFT + P** otevřete paletu příkazů, zadejte a vyberte **C/C++: Úpravou konfigurace (JSON** ) otevřete soubor **c_cpp_properties. JSON** .

1. Do `includePath` části přidejte cestu k sadě SDK pro zařízení:

    ```json
    "includePath": [
        "${workspaceFolder}/**",
        "{path_of_device_c_sdk}/**"
    ]
    ```

1. Uložte soubor.

## <a name="use-the-generated-code"></a>Použít generovaný kód

Následující pokyny popisují způsob použití vygenerovaného kódu ve vlastním projektu zařízení na různých platformách vývojového počítače. V těchto pokynech se předpokládá, že používáte připojovací řetězec IoT Hub zařízení s vygenerovaným kódem:

### <a name="linux"></a>Linux

Sestavení kódu zařízení společně se sadou SDK pro zařízení C pomocí CMake v prostředí Linux, jako je Ubuntu nebo Debian:

1. Otevřete aplikaci terminálu.

1. `cmake`Pomocí příkazu`apt-get` nainstalujte RSZ, **Git**, a všechny závislosti:

    ```sh
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Ověřte, že verze `cmake` nástroje je nad **2.8.12** a verze **RSZ** je vyšší než **4.4.7**.

    ```sh
    cmake --version
    gcc --version
    ```

1. Naklonujte úložiště [sady SDK Azure IoT C](https://github.com/Azure/azure-iot-sdk-c) :

    ```sh
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.

1. Zkopírujte složku, která obsahuje generovaný kód, do kořenové složky sady SDK pro zařízení.

1. V vs Code otevřete `CMakeLists.txt` soubor v kořenové složce sady SDK pro zařízení.

1. Přidejte následující řádek na konec `CMakeLists.txt` souboru, aby při kompilování sady SDK zahrnoval složku se zástupnými procedurami kódu zařízení:

    ```txt
    add_subdirectory({generated_code_folder_name})
    ```

1. V kořenové složce sady `cmake` SDK pro zařízení vytvořte složku s názvem a přejděte do této složky.

    ```sh
    mkdir cmake
    cd cmake
    ```

1. Spusťte následující příkazy, abyste pomocí CMake sestavili sadu SDK pro zařízení a vygenerovanou zástupné kódy:

    ```cmd\sh
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON ..
    cmake --build .
    ```

1. Po úspěšném sestavení spusťte aplikaci určení připojovacího řetězce zařízení IoT Hub jako parametru.

    ```cmd\sh
    cd azure-iot-sdk-c/cmake/{generated_code_folder_name}/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>Windows

Chcete-li sestavit kód zařízení společně se sadou SDK zařízení C v systému Windows pomocí CMake a sady Visual StudioC++ C/compilers na příkazovém řádku, přečtěte si téma [rychlý start pro IoT technologie Plug and Play](./quickstart-create-pnp-device.md). Následující kroky ukazují, jak sestavovat kód zařízení spolu se sadou C SDK pro zařízení jako projekt CMake v sadě Visual Studio.

1. Instalace sady [Visual Studio 2019 (Community, Professional nebo Enterprise)](https://visualstudio.microsoft.com/downloads/) – Ujistěte se, že jste do úlohy zahrnuli komponentu **Správce balíčků NuGet** a **desktopový vývoj. C++**

1. Otevřete Visual Studio a **na stránce Začínáme** vyberte **klonovat nebo rezervovat kód**:

1. V **umístění úložiště**naklonujte úložiště [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) :

    ```txt
    https://github.com/Azure/azure-iot-sdk-c
    ```

    Měli byste očekávat, že dokončení této operace trvá několik minut, a můžete se podívat na průběh v podokně **Team Explorer** .

1. Otevřete **Azure-IoT-SDK-c** úložiště v **Team Explorer**, vyberte **větve**, vyhledejte větev **Public-Preview** a podívejte se na ni.

    ![Veřejná verze Preview](media/howto-use-iot-device-workbench/vs-public-preview.png)

1. Zkopírujte složku, která obsahuje generovaný kód, do kořenové složky sady SDK pro zařízení.

1. `azure-iot-sdk-c` Otevřete složku v vs.

1. `CMakeLists.txt` Otevřete soubor v kořenové složce sady SDK pro zařízení.

1. Přidejte následující řádek na konec `CMakeLists.txt` souboru, aby při kompilování sady SDK zahrnoval složku se zástupnými procedurami kódu zařízení:

    ```txt
    add_subdirectory({generated_code_folder_name})
    ```

1. Na panelu nástrojů **Obecné** Najděte rozevírací seznam **Konfigurace** . Výběrem **možnosti spravovat konfiguraci** přidejte nastavení cmake pro svůj projekt.

    ![Správa konfigurace](media/howto-use-iot-device-workbench/vs-manage-config.png)

1. V **Nastavení cmake**přidejte novou konfiguraci a jako cíl vyberte **x64-Release** .

1. Do **argumentů příkazu cmake**přidejte následující řádek:

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. Uložte soubor.

1. V **Průzkumník řešení**klikněte `CMakeLists.txt` pravým tlačítkem myši na v kořenové složce a v místní nabídce vyberte možnost **sestavit** , čímž sestavíte sadu SDK pro zařízení a vygenerovanou zástupné kódy.

1. Po úspěšném sestavení spusťte na příkazovém řádku aplikaci, která určuje připojovací řetězec zařízení IoT Hub jako parametr.

    ```cmd
    cd %USERPROFILE%\CMakeBuilds\{workspaceHash}\build\x64-Release\{generated_code_folder_name}\
    {generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> Další informace o používání CMake v sadě Visual Studio najdete v tématu [sestavování projektu cmake](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects) .

### <a name="macos"></a>macOS

Následující kroky ukazují, jak vytvořit kód zařízení spolu se sadou C SDK pro zařízení v macOS pomocí CMake:

1. Otevřete aplikaci Terminal.

1. K instalaci všech závislostí použijte [homebrew](https://homebrew.sh) :

    ```bash
    brew update
    brew install git cmake pkgconfig openssl ossp-uuid
    ```

1. Ověřte, jestli je [cmake](https://cmake.org/) minimálně **2.8.12**verze:

    ```bash
    cmake --version
    ```

1. [Opravte oblé](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#upgrade-curl-on-mac-os) na nejnovější dostupnou verzi.

1. Naklonujte úložiště [sady SDK Azure IoT C](https://github.com/Azure/azure-iot-sdk-c) :

    ```bash
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.

1. Zkopírujte složku, která obsahuje generovaný kód, do kořenové složky sady SDK pro zařízení.

1. V vs Code otevřete `CMakeLists.txt` soubor v kořenové složce sady SDK pro zařízení.

1. Přidejte následující řádek na konec `CMakeLists.txt` souboru, aby při kompilování sady SDK zahrnoval složku se zástupnými procedurami kódu zařízení:

    ```txt
    add_subdirectory({generated_code_folder_name})
    ```

1. V kořenové složce sady `cmake` SDK pro zařízení vytvořte složku s názvem a přejděte do této složky.

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Spusťte následující příkazy, abyste pomocí CMake sestavili sadu SDK pro zařízení a vygenerovanou zástupné kódy:

    ```bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DOPENSSL_ROOT_DIR:PATH=/usr/local/opt/openssl ..
    cmake --build .
    ```

1. Po úspěšném sestavení spusťte aplikaci určení připojovacího řetězce zařízení IoT Hub jako parametru.

    ```bash
    cd azure-iot-sdk-c/cmake/{generated_code_folder_name}/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>Iterace tím, že se znovu vygeneruje kostra kódu

Generátor kódu může znovu vygenerovat kód, pokud aktualizujete soubory DCM nebo rozhraní. Za předpokladu, že jste už svůj kód zařízení vygenerovali ze souboru DCM, můžete kód znovu vygenerovat:

1. Po otevření složky s otevřenými soubory DCM použijte **kombinaci kláves CTRL + SHIFT + P** k otevření palety příkazů, zadejte **IoT technologie Plug and Play**a vyberte možnost **Generovat zástupný kód zařízení**.

1. Vyberte soubor DCM, který jste aktualizovali.

1. Vyberte **znovu vytvořit kód pro {Project Name}** .

1. Generátor kódu používá předchozí nastavení, které jste nakonfigurovali a znovu generuje kód. Nepřepisuje však soubory, které mohou obsahovat uživatelský kód, například `main.c` a. `{project_name}_impl.c`

> [!NOTE]
> Pokud aktualizujete identifikátor URN v souboru rozhraní, bude zpracován jako nové rozhraní. Když kód znovu vygenerujete, generátor kódu vygeneruje kód pro rozhraní, ale nepřepíše původní `{project_name}_impl.c` soubor v souboru.

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Rozšíření Azure IoT Device Workbench je open source projekt na GitHubu. U všech problémů a žádostí o funkce můžete [vytvořit problém na GitHubu](https://github.com/microsoft/vscode-iot-workbench/issues).

## <a name="next-steps"></a>Další kroky

V tomto článku se naučíte, jak pomocí aplikace Workbench pro zařízení Azure IoT vytvářet soubory DCM a rozhraní. Zjistili jste také, jak vygenerovat kostru kódu C pro implementaci aplikace zařízení. Navržený další krok se naučíte, jak [nainstalovat a používat nástroj Azure IoT Explorer](./howto-install-iot-explorer.md) .
