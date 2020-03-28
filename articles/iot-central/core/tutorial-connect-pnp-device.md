---
title: Kurz – připojení zařízení IoT Plug and Play (preview) k Azure IoT Central
description: Tento kurz ukazuje, jak pomocí modelu schopností zařízení generovat kód zařízení. Pak spusťte kód zařízení, podívejte se, jak se zařízení připojuje k aplikaci IoT Central, a použijte automaticky vygenerovaná zobrazení.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 42098d54725cc12691839b63c508efbecf042aa0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80064421"
---
# <a name="tutorial-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-and-connect-it-to-your-iot-central-application"></a>Kurz: Vytvoření zařízení IoT Plug and Play (preview) pomocí modelu schopností zařízení a jeho připojení k aplikaci IoT Central

_Model schopností zařízení_ (DCM) popisuje možnosti zařízení [IoT Plug and Play (preview).](../../iot-pnp/overview-iot-plug-and-play.md) IoT Central můžete použít DCM k vytvoření šablony zařízení a vizualizace pro zařízení, když se zařízení připojí poprvé.

Podpora pro [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) je ve verzi Preview a je podporovaná jenom ve vybraných oblastech.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Pomocí kódu Visual Studia můžete vytvořit zařízení IoT Plug and Play (preview) pomocí dcm.
> * Spusťte kód zařízení v systému Windows a podívejte se, jak se připojuje k aplikaci IoT Central.
> * Zobrazení simulované telemetrie, kterou zařízení odesílá.

## <a name="prerequisites"></a>Požadavky

Dokončete rychlý start [aplikace Create a Azure IoT Central](./quick-deploy-iot-central.md) a vytvořte aplikaci IoT Central pomocí vlastní šablony vlastní aplikace > vlastní **aplikace.**

Chcete-li dokončit tento kurz, je třeba nainstalovat následující software na místním počítači:

* [Vytvářejte nástroje pro Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) s nástroji pro sestavení **C++** a úlohami **součástí správce balíčků Nuget.** Nebo pokud už máte [Visual Studio (Community, Professional nebo Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 nebo 2015 se stejnými nainstalovanými úlohami.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/) - při instalaci **CMake**vyberte možnost **Přidat CMake do systémové CESTY**.
* [Visual Studio kód](https://code.visualstudio.com/).
* [Node.js](https://nodejs.org/)
* Nástroj: `dps-keygen`

    ```cmd/sh
    npm i -g dps-keygen
    ```

### <a name="install-azure-iot-tools"></a>Instalace nástrojů Azure IoT

Pomocí následujících kroků nainstalujte rozšiřující balíček Nástrojů Azure IoT do kódu VS:

1. V kódu VS vyberte kartu **Rozšíření.**
1. Vyhledejte **nástroje Azure IoT .**
1. Vyberte **Install** (Nainstalovat).

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

V tomto kurzu použijete správce knihovny [Vcpkg](https://github.com/microsoft/vcpkg) k instalaci sady Azure IoT C device SDK ve vývojovém prostředí.

1. Otevřete příkazový řádek. Chcete-li nainstalovat vcpkg, proveďte následující příkaz:

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Potom připojit [integrace](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)pro celý uživatel , spusťte následující příkaz. Při prvním spuštění tohoto příkazu vyžaduje práva správce:

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. Instalace zařízení Azure IoT C SDK Vcpkg:

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="generate-device-key"></a>Generovat klíč zařízení

Chcete-li připojit zařízení k aplikaci IoT Central, potřebujete klíč zařízení. Jak generovat klíč zařízení:

1. Přihlaste se k aplikaci IoT Central, kterou jste vytvořili pomocí šablony **vlastní aplikace** v rychlém startu [Vytvořit aplikaci Azure IoT Central.](./quick-deploy-iot-central.md)

1. Přejděte na stránku **Správa** a vyberte **Možnost Připojení zařízení**.

1. Poznamenejte si **obor ID** a **primární klíč,** který se zobrazí při výběru **možnosti Zobrazit klávesy**. Tyto hodnoty použijete dále v tomto kurzu.

    ![Připojení zařízení](./media/tutorial-connect-pnp-device/device-connection.png)

1. Otevřete příkazový řádek a pro generování klíče zařízení spusťte následující příkaz:

    ```cmd/sh
    dps-keygen -di:mxchip-001 -mk:{Primary Key from previous step}
    ```

    Poznamenejte si _klíč_generovaného zařízení , použijte tuto hodnotu v pozdějším kroku v tomto kurzu.

## <a name="download-your-model"></a>Stažení modelu

V tomto kurzu použijete veřejné DCM pro zařízení MxChip IoT DevKit. Nepotřebujete skutečné zařízení DevKit ke spuštění kódu, v tomto kurzu zkompilujete kód pro spuštění v systému Windows.

1. Vytvořte služku s názvem `central_app` a otevřete ji v Kódu VS.

1. Pomocí **ctrl+shift+p** otevřete paletu příkazů, zadejte **IoT Plug and Play**a vyberte Otevřít úložiště **modelů**. Vyberte **Veřejné úložiště**. VS Kód zobrazuje seznam DCMs v úložišti veřejného modelu.

1. Vyberte **MXChip IoT DevKit** DCM s ID `urn:mxchip:mxchip_iot_devkit:1`. Pak vyberte **stáhnout**. Nyní máte kopii DCM ve `central_app` složce.

![Úložiště modelů a DCM](./media/tutorial-connect-pnp-device/public-repository.png)

> [!NOTE]
> Chcete-li pracovat s IoT Central, model schopností zařízení musí mít všechna rozhraní definovaná v jednom souboru.

## <a name="generate-the-c-code-stub"></a>Generovat se zakázaným inzerováním kódu C

Nyní máte **MXChip IoT DevKit** DCM a jeho přidružené rozhraní, můžete vygenerovat kód zařízení, který implementuje model. Chcete-li generovat kód C se zakázaným inzerováním v kódu VS:

1. Když je složka s otevřenými soubory DCM otevřena, otevřete paletu příkazů **pomocí ctrl+shift+p,** zadejte **IoT Plug and Play**a vyberte Generovat kód **zařízení**.

    > [!NOTE]
    > Při prvním použití nástroje Generátor kódu Plug and Play ioT trvá několik sekund ke stažení.

1. Vyberte soubor **MXChip IoT DevKit** DCM, který jste právě stáhli.

1. Zadejte název projektu **devkit_device**.

1. Jako jazyk zvolte **ANSI C.**

1. Jako metodu připojení zvolte **Symetrický klíč Via DPS (Device Provisioning Service).**

1. Jako typ projektu zvolte **CMake Project v systému Windows.** Nevybírejte **MXChip IoT DevKit Project**, tato možnost je, když máte skutečné zařízení DevKit.

1. Jako způsob zahrnutí sady SDK zvolte **Via Vcpkg.**

1. VS Code otevře nové okno s generovanými `devkit_device` soubory se zakázaným inzerováním zařízení ve složce.

![Kód generovaného zařízení](./media/tutorial-connect-pnp-device/generated-code.png)

## <a name="build-the-code"></a>Sestavení kódu

Pomocí sady SDK zařízení můžete vytvořit kód generovaného zařízení se zakázaným inzerováním. Aplikace, kterou vytvoříte, simuluje zařízení **MXChip IoT DevKit** a připojí se k vaší aplikaci IoT Central. Aplikace odesílá telemetrii a vlastnosti a přijímá příkazy.

1. Na příkazovém řádku `cmake` vytvořte `devkit_device` ve složce podadresář a přejděte do této složky:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Spusťte následující příkazy k vytvoření generovaného kódu se zakázaným inzerováním. Nahraďte `<directory of your Vcpkg repo>` zástupný symbol cestou ke své kopii úložiště **Vcpkg:**

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build . -- /p:Configuration=Release
    ```

    Pokud používáte Visual Studio 2017 nebo 2015, musíte zadat generátor CMake na základě nástrojů sestavení, které používáte:

    ```cmd
    # Either
    cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    # or
    cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    ```

1. Po úspěšném dokončení sestavení na stejném příkazovém řádku spusťte aplikaci. Nahraďte `<scopeid>` a `<devicekey>` zaznaženo hodnoty, které jste si dříve poznamenali:

    ```cmd
    .\Release\devkit_device.exe mxchip-001 <scopeid> <devicekey>
    ```

1. Aplikace zařízení spustí odesílání dat do služby IoT Hub. Někdy se chyba `Error registering device for DPS` zobrazí při prvním spuštění předchozího příkazu. Pokud se zobrazí tato chyba, opakujte příkaz.

## <a name="view-the-device"></a>Zobrazení zařízení

Po připojení kódu zařízení k ioT centru můžete zobrazit vlastnosti a telemetrii, které odesílá:

1. Ve své aplikaci IoT Central přejděte na stránku **Zařízení** a vyberte zařízení **mxchip-01.** Toto zařízení bylo automaticky přidáno, když byl připojen kód zařízení:

    ![Stránka s přehledem](./media/tutorial-connect-pnp-device/overview-page.png)

    Po několika minutách se na této stránce zobrazí grafy telemetrie, kterou zařízení odesílá.

1. Výběrem stránky **Informace** zobrazíte hodnoty vlastností, které zařízení odeslalo.

1. Vyberte stránku **Příkazy,** chcete-li volat příkazy na zařízení. Zařízení reaguje na příkazovém řádku, na který běží kód zařízení.

1. Přejděte na stránku **Šablony zařízení** a podívejte se na šablonu, kterou IoT Central vytvořil z DCM ve veřejném úložišti:

    ![Stránka předlohy zařízení](./media/tutorial-connect-pnp-device/device-template.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak připojit zařízení IoT Plug and Play (preview), které bylo vygenerováno z DCM v úložišti veřejného modelu.

Chcete-li se dozvědět více o dcms a jak vytvořit vlastní modely, pokračujte návodem:

> [!div class="nextstepaction"]
> [Definování nového typu zařízení IoT](./howto-set-up-template.md)
