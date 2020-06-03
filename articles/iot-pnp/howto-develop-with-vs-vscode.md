---
title: Použití sady Visual Studio a Visual Studio Code k sestavení zařízení IoT technologie Plug and Play ve verzi Preview | Microsoft Docs
description: Pomocí sady Visual Studio a Visual Studio Code urychlíte tvorbu modelů zařízení IoT technologie Plug and Play a implementací kódu zařízení.
author: liydu
ms.author: liydu
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 038d9ff39f388d1ef7b09b951c09dbe3420858b7
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/02/2020
ms.locfileid: "84298223"
---
# <a name="use-visual-studio-and-visual-studio-code-to-build-iot-plug-and-play-devices"></a>Použití sady Visual Studio a Visual Studio Code k sestavování zařízení IoT technologie Plug and Play

Nástroje Azure IoT Tools for Visual Studio Code poskytují integrované prostředí pro tvorbu modelů funkcí zařízení (DCM) a rozhraní, publikování do modelů úložišť a generování kostrového kódu C k implementaci aplikace zařízení.

V tomto článku se dozvíte, jak:

- Generování kódu zařízení a projektu aplikace.
- Použijte vygenerovaný kód v projektu zařízení.
- Iterujte tak, že znovu vygenerujete kostru kódu.

Další informace o použití VS Code k vývoji zařízení IoT najdete v tématu [https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench) .

## <a name="prerequisites"></a>Požadavky

Nainstalujte [Visual Studio Code](https://code.visualstudio.com/).

K instalaci balíčku rozšíření v VS Code použijte následující postup.

1. V VS Code vyberte kartu **rozšíření** .
1. Vyhledejte a nainstalujte si **nástroje Azure IoT** z webu Marketplace.

## <a name="generate-device-code-and-project"></a>Generovat kód zařízení a projekt

V VS Code pomocí **kombinace kláves CTRL + SHIFT + P** otevřete paletu příkazů, zadejte **IoT technologie Plug and Play**a vyberte možnost **Generovat zástupný kód zařízení** pro konfiguraci kostry kódu a typu projektu. Následující seznam podrobně popisuje jednotlivé kroky:

- **Soubor DCM, který se má použít pro generování kódu**. Chcete-li vygenerovat kód zařízení kostry, otevřete složku obsahující soubory DCM a rozhraní, které implementuje. Pokud generátor kódu nenajde rozhraní, které modul DCM vyžaduje, stáhne ho z úložiště modelu podle potřeby.

- **Jazyk kódu zařízení**. V současné době generátor kódu podporuje pouze ANSI C.

- **Název projektu** Název projektu se používá jako název složky pro generovaný kód a další soubory projektu. Ve výchozím nastavení je složka umístěná vedle souboru DCM. Chcete-li se vyhnout nutnosti ručnímu kopírování vygenerované složky kódu vždy, když aktualizujete DCM a znovu vygenerujete kód zařízení, zachovejte soubor DCM ve stejné složce jako složka projektu.

- **Způsob připojení ke službě Azure IoT**. Vygenerované soubory také obsahují kód pro konfiguraci zařízení pro připojení k Azure IoT Hub. Můžete se rozhodnout připojit se přímo k [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) nebo použít [službu Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps).

    - **Přes IoT Hub připojovací řetězec zařízení**: zadejte připojovací řetězec zařízení, pro který se má aplikace zařízení připojit IoT Hub přímo.
    - **Přes DPS symetrický klíč**: zadejte **Rozsah ID**, **symetrický klíč** a **ID zařízení** pro aplikaci zařízení, které jsou potřeba pro připojení k IoT Hub nebo IoT Central pomocí DPS.

- **Typ projektu**. Generátor kódu také generuje projekt CMake nebo Arduino. V současné době jsou podporované typy projektů:

    - **Projekt cmake ve Windows**: pro projekt zařízení, který používá [cmake](https://cmake.org/) jako systém sestavení v systému Windows. Tato možnost generuje `CMakeLists.txt` s konfiguracemi sady SDK pro zařízení ve stejné složce jako kód jazyka C.
    - **Projekt cmake v systému Linux**: pro projekt zařízení, který používá [cmake](https://cmake.org/) jako systém sestavení na platformě Linux. Tato možnost generuje `CMakeLists.txt` s konfiguracemi sady SDK pro zařízení ve stejné složce jako kód jazyka C.
    - **MXChip IoT DevKit Project**: pro projekt zařízení, který běží na zařízení [MXChip IoT DevKit](https://aka.ms/iot-devkit) . Tato možnost generuje projekt Arduino, který můžete [použít v vs Code](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) nebo v integrovaném vývojovém prostředí Arduino pro sestavování a spouštění na zařízení IoT DevKit.

- **Typ sady SDK pro zařízení**. Pokud vyberete možnost CMake jako typ projektu, jedná se o krok ke konfiguraci způsobu, jakým bude generovaný kód zahrnovat sadu SDK pro zařízení Azure IoT C v nástroji `CMakeLists.txt` :

    - **Prostřednictvím zdrojového kódu**: vygenerovaný kód spoléhá na [zdrojový kód sady SDK pro zařízení](https://github.com/Azure/azure-iot-sdk-c) , který se má zahrnout do a společně s ním společně sestavit. To se doporučuje, když jste upravili zdrojový kód sady SDK pro zařízení.
    - **Prostřednictvím Vcpkg**: generovaný kód spoléhá na to, že [sada SDK pro zařízení Vcpkg](https://github.com/microsoft/vcpkg/tree/master/ports/azure-iot-sdk-c) se má zahrnout do a sestavit společně s ní. Toto je doporučený způsob pro zařízení s Windows, Linux nebo macOS.

    > [!NOTE]
    > Podpora macOS pro sadu SDK pro zařízení Azure IoT C Vcpkg pracuje v průběhu.

Generátor kódu se pokusí použít soubory DCM a rozhraní umístěné v místní složce. Pokud soubory rozhraní nejsou v místní složce, generátor kódu je vyhledá v úložišti veřejného modelu nebo v úložišti modelu společnosti. [Společné soubory rozhraní](./concepts-common-interfaces.md) jsou uloženy v úložišti veřejného modelu.

Po dokončení generování kódu rozšíření otevře nové okno VS Code s kódem. Otevřete-li vygenerovaný soubor, například **Main. c**, může se stát, že technologie IntelliSense ohlásí, že nemůže otevřít zdrojové soubory sady c SDK. Chcete-li povolit správnou navigaci pomocí technologie IntelliSense a kódu, použijte následující postup pro zahrnutí zdroje C SDK:

1. V VS Code pomocí **kombinace kláves CTRL + SHIFT + P** otevřete paletu příkazů, zadejte a vyberte **C/C++: upravit konfigurace (JSON)** a otevřete soubor **c_cpp_properties. JSON** .

1. Do části přidejte cestu k sadě SDK pro zařízení `includePath` :

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

Sestavte kód zařízení společně se sadou SDK zařízení C Vcpkg pomocí CMake v prostředí Linux, jako je Ubuntu nebo Debian:

1. Otevřete aplikaci terminálu.

1. Pomocí příkazu nainstalujte **RSZ**, **Git**, `cmake` a všechny závislosti `apt-get` :

    ```bash
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Ověřte, že verze nástroje `cmake` je nad **2.8.12** a verze **RSZ** je vyšší než **4.4.7**.

    ```bash
    cmake --version
    gcc --version
    ```

1. Nainstalovat Vcpkg:

    ```bash
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    ./bootstrap-vcpkg.sh
    ```

    Pokud chcete připojit [integraci](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)uživatelů na úrovni uživatele, spusťte:

    ```bash
    ./vcpkg integrate install
    ```

1. Instalace sady SDK pro zařízení Azure IoT C Vcpkg:

    ```bash
    ./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

1. Vytvoření `cmake` podadresáře ve složce obsahuje zástupný kód generovaného kódu a přejděte do této složky:

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Spusťte následující příkazy, abyste pomocí CMake sestavili sadu SDK pro zařízení a vygenerovanou zástupné kódy:

    ```bash
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}/scripts/buildsystems/vcpkg.cmake"

    cmake --build .
    ```

1. Po úspěšném sestavení spusťte aplikaci určení připojovacího řetězce zařízení IoT Hub jako parametru.

    ```bash
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>Windows

Chcete-li sestavit kód zařízení společně se sadou SDK zařízení C v systému Windows pomocí CMake a kompilátorů Visual Studio C/C++ na příkazovém řádku, přečtěte si téma [rychlý Start pro IoT technologie Plug and Play](./quickstart-create-pnp-device-windows.md). Následující kroky ukazují, jak sestavovat kód zařízení spolu se sadou C SDK Vcpkg jako projekt CMake v sadě Visual Studio.

1. Postupujte podle kroků v [rychlém](https://docs.microsoft.com/azure/iot-pnp/quickstart-create-pnp-device-windows#prepare-the-development-environment) startu a nainstalujte sadu SDK pro zařízení Azure IoT pro jazyk C přes Vcpkg.

1. Instalace sady [Visual Studio 2019 (Community, Professional nebo Enterprise)](https://visualstudio.microsoft.com/downloads/) – Ujistěte se, že jste zahrnuli komponentu **Správce balíčků NuGet** a **desktopový vývoj s** využitím úlohy C++.

1. Otevřete sadu Visual Studio, vyberte **soubor > otevřít > cmake...** pro otevření `CMakeLists.txt` složky obsahuje generovaný kód.

1. Na panelu nástrojů **Obecné** Najděte rozevírací seznam **Konfigurace** . Výběrem **možnosti spravovat konfiguraci** přidejte nastavení cmake pro svůj projekt.

    ![Správa konfigurace](media/howto-develop-with-vs-vscode/vs-manage-config.png)

1. V **Nastavení cmake**přidejte novou konfiguraci a jako cíl vyberte **x86-Debug** .

1. Do **argumentů příkazu cmake**přidejte následující řádek:

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. Uložte soubor.

1. Přepněte na **x86-Debug** v rozevíracím seznamu **Konfigurace** . Aby CMake vygenerovala mezipaměť, potřebuje několik sekund. Zobrazte okno výstup a podívejte se na průběh.

    ![Výstup CMake](media/howto-develop-with-vs-vscode/vs-cmake-output.png)

1. V **Průzkumník řešení**klikněte pravým tlačítkem myši na `CMakeLists.txt` v kořenové složce a vyberte možnost **sestavit** z kontextové nabídky a sestavte zástupnou proceduru vygenerovaného kódu pomocí sady SDK pro zařízení.

1. Po úspěšném sestavení spusťte na příkazovém řádku aplikaci, která určuje připojovací řetězec zařízení IoT Hub jako parametr.

    ```cmd
    .\out\build\x86-Debug\{generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> Další informace o používání CMake v sadě Visual Studio najdete v tématu [sestavování projektu cmake](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects) .

### <a name="macos"></a>macOS

Následující kroky ukazují, jak vytvořit kód zařízení spolu se zdrojovým kódem sady C SDK pro zařízení v macOS pomocí CMake:

1. Otevřete aplikaci Terminal.

1. K instalaci všech závislostí použijte [homebrew](https://brew.sh) :

    ```bash
    brew update
    brew install git cmake pkgconfig openssl ossp-uuid
    ```

1. Ověřte, jestli je [cmake](https://cmake.org/) minimálně **2.8.12**verze:

    ```bash
    cmake --version
    ```

1. [Opravte oblé](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#upgrade-curl-on-mac-os) na nejnovější dostupnou verzi.

1. Ve složce, která obsahuje generovaný kód, naklonujte úložiště [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) :

    ```bash
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.

1. Vytvořte složku s názvem `cmake` ve složce, která obsahuje generovaný kód, a přejděte do této složky.

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
    cd {generated_code_folder_name}/cmake/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>Iterace tím, že se znovu vygeneruje kostra kódu

Generátor kódu může znovu vygenerovat kód, pokud aktualizujete soubory DCM nebo rozhraní. Za předpokladu, že jste už svůj kód zařízení vygenerovali ze souboru DCM, můžete kód znovu vygenerovat:

1. Po otevření složky s otevřenými soubory DCM použijte **kombinaci kláves CTRL + SHIFT + P** k otevření palety příkazů, zadejte **IoT technologie Plug and Play**a vyberte možnost **Generovat zástupný kód zařízení**.

1. Vyberte soubor DCM, který jste aktualizovali.

1. Vyberte **znovu vytvořit kód pro {Project Name}**.

1. Generátor kódu používá předchozí nastavení, které jste nakonfigurovali a znovu generuje kód. Nepřepisuje však soubory, které mohou obsahovat uživatelský kód, například `main.c` a `{project_name}_impl.c` .

> [!NOTE]
> Pokud aktualizujete identifikátor URN v souboru rozhraní, bude zpracován jako nové rozhraní. Když kód znovu vygenerujete, generátor kódu vygeneruje kód pro rozhraní, ale nepřepíše původní `{project_name}_impl.c` soubor v souboru.

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Azure IoT Tools je open source projekt na GitHubu. U všech problémů a žádostí o funkce můžete [vytvořit problém na GitHubu](https://github.com/microsoft/vscode-azure-iot-tools/issues/new).

## <a name="next-steps"></a>Další kroky

V tomto článku se naučíte, jak používat Visual Studio a Visual Studio Code k vygenerování kostry kódu C k implementaci aplikace zařízení. Navržený další krok se naučíte, jak [nainstalovat a používat nástroj Azure IoT Explorer](./howto-install-iot-explorer.md) .
