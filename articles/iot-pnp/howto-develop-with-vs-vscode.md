---
title: Použití visual studia a kódu sady Visual Studio k vytváření zařízení IoT Plug and Play Preview | Dokumenty společnosti Microsoft
description: Pomocí visual studia a kódu Sady Visual Studio můžete urychlit vytváření modelů zařízení IoT Plug and Play a implementaci kódu zařízení.
author: liydu
ms.author: liydu
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 91e7b1c0be9a38c3d79440f07d944d182980dc10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159230"
---
# <a name="use-visual-studio-and-visual-studio-code-to-build-iot-plug-and-play-devices"></a>Použití visual studia a kódu Sady Visual Studio k vytváření zařízení IoT Plug and Play

Nástroje Azure IoT pro kód Visual Studia poskytuje integrované prostředí pro vytváření modelů schopností zařízení (DCM) a rozhraní, publikování do úložišť modelů a generování kódu kostry C pro implementaci aplikace zařízení.

V tomto článku se dozvíte, jak:

- Vygenerujte kód zařízení a projekt aplikace.
- Použijte generovaný kód v projektu zařízení.
- Iterujte regenercí skeletového kódu.

Další informace o používání kódu VS k vývoji [https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench)zařízení IoT najdete v tématu .

## <a name="prerequisites"></a>Požadavky

Nainstalujte [kód sady Visual Studio](https://code.visualstudio.com/).

Pomocí následujících kroků nainstalujte rozšiřující balíček v kódu VS.

1. V kódu VS vyberte **kartu Rozšíření.**
1. Vyhledejte a nainstalujte **nástroje Azure IoT na** trhu.

## <a name="generate-device-code-and-project"></a>Generovat kód zařízení a projekt

V kódu VS otevřete paletu příkazů **pomocí ctrl+shift+p,** zadejte **IoT Plug and Play**a vyberte Generovat kód **zařízení** pro konfiguraci kódu kostry a typu projektu. Následující seznam podrobně popisuje každý krok:

- **DCM soubor, který má být použit pro generování kódu**. Chcete-li generovat kód kostry zařízení, otevřete složku, která obsahuje DCM a soubory rozhraní, které implementuje. Pokud generátor kódu nemůže najít rozhraní, které dcm vyžaduje, stáhne jej z úložiště modelu podle potřeby.

- **Jazyk kódu zařízení**. V současné době generátor kódu podporuje pouze ANSI C.

- **Název projektu**. Název projektu se používá jako název složky pro generovaný kód a další soubory projektu. Složka je ve výchozím nastavení umístěna vedle souboru DCM. Chcete-li se vyhnout ruční kopírování generované složky kódu při každé aktualizaci dcm a regenerovat kód zařízení, uchovávejte soubor DCM ve stejné složce jako složka projektu.

- **Metoda připojení k Azure IoT**. Generované soubory také obsahují kód pro konfiguraci zařízení pro připojení k Azure IoT Hub. Můžete se připojit přímo k [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) nebo použít [službu device provisioning](https://docs.microsoft.com/azure/iot-dps).

    - **Prostřednictvím připojovacího řetězce zařízení služby IoT Hub:** zadejte připojovací řetězec zařízení pro aplikaci zařízení, která se má přímo připojit k centru IoT Hub.
    - **Pomocí symetrického klíče DPS:** zadejte **obor ID**, **symetrický klíč** a **ID zařízení** pro aplikaci zařízení, které jsou nutné pro připojení k ioT hubu nebo IoT Central pomocí DPS.

- **Typ projektu**. Generátor kódu také generuje projekt CMake nebo Arduino. V současné době jsou podporované typy projektů:

    - **CMake Project v systému Windows**: pro projekt zařízení, který používá [CMake](https://cmake.org/) jako sestavení systému v systému Windows. Tato možnost `CMakeLists.txt` generuje s konfiguracemi sady SDK zařízení ve stejné složce jako kód C.
    - **CMake Project on Linux**: pro projekt zařízení, který používá [CMake](https://cmake.org/) jako build systém na Linuxu. Tato možnost `CMakeLists.txt` generuje s konfiguracemi sady SDK zařízení ve stejné složce jako kód C.
    - **Projekt MXChip IoT DevKit**: pro projekt zařízení, který běží na zařízení [MXChip IoT DevKit.](https://aka.ms/iot-devkit) Tato možnost generuje projekt Arduino, který můžete [použít v Kódu VS](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) nebo v Arduino IDE k sestavení a spuštění na zařízení IoT DevKit.

- **Typ sady SDK zařízení**. Pokud vyberete CMake jako typ projektu, toto je krok ke konfiguraci, jak bude `CMakeLists.txt`generovaný kód obsahovat azure iot c zařízení SDK v :

    - **Prostřednictvím zdrojového kódu**: generovaný kód závisí na [zdrojovém kódu sady SDK zařízení,](https://github.com/Azure/azure-iot-sdk-c) který má být zahrnut a sestaven společně s ním. To je doporučeno, pokud jste přizpůsobili zdrojový kód sady SDK zařízení.
    - **Via Vcpkg**: generovaný kód závisí na [zařízení SDK Vcpkg](https://github.com/microsoft/vcpkg/tree/master/ports/azure-iot-sdk-c) zahrnout a stavět spolu s ním. Toto je doporučený způsob pro zařízení se systémem Windows, Linux nebo macOS.

    > [!NOTE]
    > Probíhá podpora macOS pro zařízení Azure IoT C SDK Vcpkg.

Generátor kódu se pokusí použít soubory DCM a rozhraní umístěné v místní složce. Pokud soubory rozhraní nejsou v místní složce, generátor kódu hledá je v úložišti veřejného modelu nebo úložiště modelu společnosti. [Běžné soubory rozhraní](./concepts-common-interfaces.md) jsou uloženy v úložišti veřejného modelu.

Po dokončení generování kódu rozšíření otevře nové okno VS kód s kódem. Pokud otevřete generovaný soubor, například **main.c**, můžete zjistit, že technologie IntelliSense hlásí, že nemůže otevřít zdrojové soubory sady C SDK. Chcete-li povolit správnou navigaci Technologie IntelliSense a kódu, zahrňte zdroj sady C SDK následujícím postupem:

1. V kódu VS otevřete paletu příkazů **pomocí kombinace kláves Ctrl+Shift+P,** zadejte a vyberte **C/C++: Edit Configurations (JSON)** pro otevření souboru **c_cpp_properties.json.**

1. V `includePath` části přidejte cestu sady SDK zařízení:

    ```json
    "includePath": [
        "${workspaceFolder}/**",
        "{path_of_device_c_sdk}/**"
    ]
    ```

1. Uložte soubor.

## <a name="use-the-generated-code"></a>Použití generovaného kódu

Následující pokyny popisují, jak používat generovaný kód v projektu vlastního zařízení na různých platformách vývojového počítače. Pokyny předpokládají, že používáte připojovací řetězec zařízení služby IoT Hub s generovaným kódem:

### <a name="linux"></a>Linux

Chcete-li vytvořit kód zařízení společně se zařízením C SDK Vcpkg pomocí CMake v prostředí Linuxu, jako je Ubuntu nebo Debian:

1. Otevřete terminálovou aplikaci.

1. Nainstalujte **gcc** `cmake`, **git**a všechny `apt-get` závislosti pomocí příkazu:

    ```bash
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Ověřte, `cmake` zda je verze písmene a) vyšší než **2.8.12** a verze **GCC** je vyšší než **4.4.7**.

    ```bash
    cmake --version
    gcc --version
    ```

1. Instalace vcpkg:

    ```bash
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    ./bootstrap-vcpkg.sh
    ```

    Chcete-li připojit [integraci](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)pro celý uživatel , spusťte:

    ```bash
    ./vcpkg integrate install
    ```

1. Instalace zařízení Azure IoT C SDK Vcpkg:

    ```bash
    ./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

1. Vytvořte `cmake` podadresář ve složce obsahuje kód se zakázaným inzerováním generovaného kódu a přejděte do této složky:

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Spusťte následující příkazy pro vytvoření sady SDK zařízení a kódu s kódem CMake:

    ```bash
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}/scripts/buildsystems/vcpkg.cmake"

    cmake --build .
    ```

1. Po úspěšném sestavení spusťte aplikaci určující připojovací řetězec zařízení služby IoT Hub jako parametr.

    ```bash
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>Windows

Chcete-li vytvořit kód zařízení společně se zařízením C SDK ve Windows pomocí cmake a kompilátorů Visual Studio C/C++ na příkazovém řádku, přečtěte si [úvodní příručku ioT Plug and Play](./quickstart-create-pnp-device-windows.md). Následující kroky ukazují, jak vytvořit kód zařízení společně se zařízením C SDK Vcpkg jako projekt CMake v sadě Visual Studio.

1. Podle pokynů v [úvodním startu](https://docs.microsoft.com/azure/iot-pnp/quickstart-create-pnp-device-windows#prepare-the-development-environment) nainstalujte sadku Azure IoT device SDK pro C přes Vcpkg.

1. Nainstalujte [Visual Studio 2019 (Komunita, Professional nebo Enterprise)](https://visualstudio.microsoft.com/downloads/) – ujistěte se, že patří **součást Správce balíčků NuGet** a vývoj plochy s úlohami **C++.**

1. Otevřete Visual Studio, zvolte Soubor > `CMakeLists.txt` Otevřít > **CMake...** chcete-li otevřít ve složce obsahuje generovaný kód.

1. V panelu nástrojů **Obecné** vyhledejte rozevírací informace **konfigurace.** Vyberte **Spravovat konfiguraci,** chcete-li přidat nastavení CMake pro váš projekt.

    ![Správa konfigurace](media/howto-develop-with-vs-vscode/vs-manage-config.png)

1. V **nastavení CMake**přidejte novou konfiguraci a vyberte **x86-Debug** jako cíl.

1. Do **příkazu CMake Přidejte**následující řádek:

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. Uložte soubor.

1. Přepněte na **x86-Ladění** v rozbalovací části **Konfigurace.** Potřebuje několik sekund pro CMake generovat mezipaměť pro něj. Chcete-li zobrazit průběh, zobrazte okno Výstup.

    ![Výstup CMake](media/howto-develop-with-vs-vscode/vs-cmake-output.png)

1. V **Průzkumníku řešení**klikněte `CMakeLists.txt` pravým tlačítkem myši na kořenovou složku a v místní nabídce vyberte **Build** a vytvořte se zakázaným inzerováním generovaného kódu pomocí sady SDK zařízení.

1. Po úspěšném sestavení spusťte na příkazovém řádku aplikaci určující připojovací řetězec zařízení služby IoT Hub jako parametr.

    ```cmd
    .\out\build\x86-Debug\{generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> Další informace o používání CMake v sadě Visual Studio najdete v [tématu sestavení projektu CMake](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects) .

### <a name="macos"></a>macOS

Následující kroky ukazují, jak vytvořit kód zařízení společně se zdrojovým kódem sady C SDK zařízení v systému macOS pomocí CMake:

1. Otevřete terminálovou aplikaci.

1. K instalaci všech závislostí použijte [Homebrew:](https://homebrew.sh)

    ```bash
    brew update
    brew install git cmake pkgconfig openssl ossp-uuid
    ```

1. Ověřte, zda je [cmake](https://cmake.org/) alespoň verze **2.8.12**:

    ```bash
    cmake --version
    ```

1. [Patch CURL](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#upgrade-curl-on-mac-os) na nejnovější verzi k dispozici.

1. Ve složce, která obsahuje generovaný kód, klonujte úložiště [Azure IoT C SDK:](https://github.com/Azure/azure-iot-sdk-c)

    ```bash
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.

1. Vytvořte složku `cmake` volanou pod složkou, která obsahuje generovaný kód, a přejděte do této složky.

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Spusťte následující příkazy pro vytvoření sady SDK zařízení a kódu s kódem CMake:

    ```bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DOPENSSL_ROOT_DIR:PATH=/usr/local/opt/openssl ..
    cmake --build .
    ```

1. Po úspěšném sestavení spusťte aplikaci určující připojovací řetězec zařízení služby IoT Hub jako parametr.

    ```bash
    cd {generated_code_folder_name}/cmake/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>Iterovat regeneracem skeletového kódu

Generátor kódu můžete regenerovat kód, pokud aktualizujete soubory DCM nebo rozhraní. Za předpokladu, že jste již vygenerovali kód zařízení ze souboru DCM, chcete-li kód znovu vygenerovat:

1. Když je složka s otevřenými soubory DCM otevřena, otevřete paletu příkazů **pomocí ctrl+shift+p,** zadejte **IoT Plug and Play**a vyberte Generovat kód **zařízení**.

1. Zvolte aktualizovaný soubor DCM.

1. Vyberte **znovu generovat kód pro {název projektu}**.

1. Generátor kódu používá předchozí nastavení, které jste nakonfigurovali, a regeneruje kód. Nepřepisuje však soubory, které mohou obsahovat `main.c` `{project_name}_impl.c`uživatelský kód, například a .

> [!NOTE]
> Pokud aktualizujete id URN v souboru rozhraní, je považován za nové rozhraní. Při opětovném generování kódu generátor kódu generuje kód pro rozhraní, ale nepřepíše `{project_name}_impl.c` původní v souboru.

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Azure IoT Tools je open source projekt na GitHubu. Pro všechny problémy a požadavky na funkce můžete [vytvořit problém na GitHubu](https://github.com/microsoft/vscode-azure-iot-tools/issues/new).

## <a name="next-steps"></a>Další kroky

V tomto článku s návody jste se naučili, jak pomocí visual studio a visual studio kód ke generování kostry kódu C k implementaci aplikace zařízení. Dalším navrhovaným krokem je naučit se nainstalovat a používat nástroj [Průzkumník Azure IoT.](./howto-install-iot-explorer.md)
