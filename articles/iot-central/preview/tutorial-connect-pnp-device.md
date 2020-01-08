---
title: Kurz – připojení zařízení IoT technologie Plug and Play ve verzi Preview ke službě Azure IoT Central
description: V tomto kurzu se dozvíte, jak pomocí modelu schopností zařízení vygenerovat kód zařízení. Pak spusťte kód zařízení, přečtěte si téma připojení k aplikaci IoT Central a použijte automaticky vygenerovaná zobrazení.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 233d8f345eceb188fcc849457c5243a3c64aaec2
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2019
ms.locfileid: "75548849"
---
# <a name="tutorial-use-a-device-capability-model-to-create-an-iot-plug-and-play-device-and-connect-it-to-your-iot-central-application"></a>Kurz: použití modelu schopností zařízení k vytvoření zařízení IoT technologie Plug and Play a jeho připojení k aplikaci IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

_Model schopností zařízení_ (DCM) popisuje možnosti zařízení [IoT technologie Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) . IoT Central může použít DCM k vytvoření šablony zařízení a vizualizací pro zařízení, když se zařízení poprvé připojí.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Pomocí Visual Studio Code vytvořit zařízení IoT technologie Plug and Play pomocí DCM.
> * Spusťte kód zařízení ve Windows a podívejte se, jak se připojí k vaší IoT Central aplikaci.
> * Podívejte se na simulovanou telemetrii, kterou zařízení odesílá.

## <a name="prerequisites"></a>Požadavky

Dokončete průvodce [vytvořením aplikace Azure IoT Central (náhled funkcí)](./quick-deploy-iot-central.md) a vytvořte aplikaci IoT Central pomocí vlastní šablony aplikace **> Preview** .

K dokončení tohoto kurzu potřebujete na svém místním počítači nainstalovat následující software:

* [Nástroje pro sestavení pro sadu Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) pomocí  **C++ nástrojů sestavení** a úloh **komponent správce balíčků NuGet** . Nebo pokud už máte [Visual Studio (Community, Professional nebo Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 nebo 2015 se stejnými úlohami.
* [Git](https://git-scm.com/download/)
* [Cmake](https://cmake.org/download/) – při instalaci **cmake**vyberte možnost **Přidat cmake do systémové cesty**.
* [Visual Studio Code](https://code.visualstudio.com/).
* [Node.js](https://nodejs.org/)
* Nástroj `dps-keygen`:

    ```cmd/sh
    npm i -g dps-keygen
    ```

### <a name="install-azure-iot-tools"></a>Instalace nástrojů Azure IoT

Pomocí následujících kroků nainstalujete balíček rozšíření Azure IoT Tools do VS Code:

1. V VS Code vyberte kartu **rozšíření** .
1. Vyhledejte **nástroje Azure IoT Tools**.
1. Vyberte **Install** (Nainstalovat).

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

V tomto kurzu pomocí Správce knihovny [Vcpkg](https://github.com/microsoft/vcpkg) nainstalujete sadu SDK pro zařízení Azure IoT C ve vašem vývojovém prostředí.

1. Otevřete příkazový řádek. Spusťte následující příkaz pro instalaci Vcpkg:

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Pak spusťte následující příkaz, aby se připojila [integrace](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)na úrovni uživatele. Při prvním spuštění tohoto příkazu vyžaduje oprávnění správce:

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. Instalace sady SDK pro zařízení Azure IoT C Vcpkg:

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="generate-device-key"></a>Generovat klíč zařízení

Pokud chcete zařízení připojit k IoT Central aplikaci, budete potřebovat klíč zařízení. Vygenerování klíče zařízení:

1. Přihlaste se k aplikaci IoT Central, kterou jste vytvořili pomocí **vlastní šablony aplikace > Preview** v části [vytvoření aplikace Azure IoT Central (funkce Preview)](./quick-deploy-iot-central.md) rychlý Start.

1. Přejít na stránku **Správa** a vyberte **připojení zařízení**.

1. Poznamenejte si **Rozsah ID** a **primární klíč** , který se zobrazí, když vyberete možnost **Zobrazit klíče**. Tyto hodnoty použijete později v tomto kurzu.

    ![Připojení zařízení](./media/tutorial-connect-pnp-device/device-connection.png)

1. Otevřete příkazový řádek a spuštěním následujícího příkazu vygenerujte klíč zařízení:

    ```cmd/sh
    dps-keygen -di:mxchip-001 -mk:{Primary Key from previous step}
    ```

    Poznamenejte si generovaný _klíč zařízení_a použijte tuto hodnotu v pozdějším kroku tohoto kurzu.

## <a name="download-your-model"></a>Stažení modelu

V tomto kurzu použijete veřejný DCM pro zařízení MxChip IoT DevKit. Pro spuštění kódu nepotřebujete skutečné zařízení DevKit. v tomto kurzu zkompilujete kód, který chcete spustit v systému Windows.

1. Vytvořte složku s názvem `central_app` a otevřete ji v VS Code.

1. Stisknutím **kombinace kláves CTRL + SHIFT + P** otevřete paletu příkazů, zadejte **IoT technologie Plug and Play**a vyberte **Otevřít úložiště modelu**. Vyberte **veřejné úložiště**. VS Code zobrazuje seznam DCMs ve veřejném úložišti modelu.

1. Vyberte **MXChip IoT DevKit** DCM s ID `urn:mxchip:mxchip_iot_devkit:1`. Pak vyberte **Stáhnout**. Nyní máte kopii DCM ve složce `central_app`.

![Úložiště modelu a DCM](./media/tutorial-connect-pnp-device/public-repository.png)

> [!NOTE]
> Aby bylo možné pracovat s IoT Central, musí mít model schopností zařízení všechna rozhraní definovaná vloženým do stejného souboru.

## <a name="generate-the-c-code-stub"></a>Generovat zástupný kód kódu jazyka C

Teď máte **MXChip IoT DevKit** DCM a jeho přidružená rozhraní, můžete vygenerovat kód zařízení, který model implementuje. Chcete-li vygenerovat zástupnou proceduru kódu C v VS Code:

1. Po otevření složky s otevřenými soubory DCM použijte **kombinaci kláves CTRL + SHIFT + P** k otevření palety příkazů, zadejte **IoT technologie Plug and Play**a vyberte možnost **Generovat zástupný kód zařízení**.

    > [!NOTE]
    > Když použijete nástroj pro generátor kódu IoT technologie Plug and Play poprvé, bude stažení trvat několik sekund.

1. Vyberte soubor **MXChip IoT DevKit** DCM, který jste právě stáhli.

1. Zadejte název projektu **devkit_device**.

1. Jako jazyk vyberte **ANSI C** .

1. Jako metodu připojení vyberte **přes DPS (služba Device Provisioning Service) symetrický klíč** .

1. Jako typ projektu vyberte možnost **projekt cmake v systému Windows** . Nevybírejte **projekt MXChip IoT DevKit**. Tato možnost je k dispozici i v případě, že máte reálné zařízení DevKit.

1. Vyberte **prostřednictvím Vcpkg** jako způsob, jak zahrnout sadu SDK.

1. VS Code otevře nové okno s generovanými zástupnými soubory kódu zařízení ve složce `devkit_device`.

![Generovaný kód zařízení](./media/tutorial-connect-pnp-device/generated-code.png)

## <a name="build-the-code"></a>Sestavení kódu

Pomocí sady SDK pro zařízení sestavíte nevygenerovanou zástupné kódy zařízení. Aplikace, kterou vytváříte, simuluje zařízení **MXChip IoT DevKit** a připojuje se k vaší IoT Central aplikaci. Aplikace odesílá telemetrie a vlastnosti a přijímá příkazy.

1. Na příkazovém řádku vytvořte v `devkit_device` složce `cmake` podadresář a přejděte do této složky:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Spuštěním následujících příkazů Sestavte zástupné procedury generovaného kódu. Nahraďte zástupný text `<directory of your Vcpkg repo>` cestou k vaší kopii úložiště **Vcpkg** :

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build . -- /p:Configuration=Release
    ```

    Pokud používáte sadu Visual Studio 2017 nebo 2015, je nutné určit generátor CMake na základě nástrojů pro sestavení, které používáte:

    ```cmd
    # Either
    cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    # or
    cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    ```

1. Po úspěšném dokončení sestavení ve stejném příkazovém řádku spusťte aplikaci. Nahraďte `<scopeid>` a `<primarykey>` hodnotami, které jste si poznamenali dříve:

    ```cmd
    .\Release\devkit_device.exe mxchip-001 <scopeid> <primarykey>
    ```

1. Aplikace zařízení spouští odesílání dat do IoT Hub. Někdy se při prvním spuštění předchozího příkazu zobrazí chyba `Error registering device for DPS`. Pokud se zobrazí tato chyba, zkuste příkaz zopakovat.

## <a name="view-the-device"></a>Zobrazit zařízení

Po připojení kódu zařízení k vašemu IoT Central můžete zobrazit vlastnosti a telemetrie, které odesílá:

1. V aplikaci IoT Central otevřete stránku **zařízení** a vyberte zařízení **mxchip-01** . Toto zařízení se automaticky přidalo při připojení kódu zařízení:

    ![Stránka Přehled](./media/tutorial-connect-pnp-device/overview-page.png)

    Po několika minutách zobrazí tato stránka grafy telemetrie, které zařízení odesílá.

1. Výběrem stránky **o** se zobrazí hodnoty vlastností odeslané zařízením.

1. Vyberte stránku **příkazy** , na které se budou volat příkazy na zařízení. Na příkazovém řádku, na kterém je spuštěný Kód zařízení, můžete vidět, že zařízení reaguje.

1. Přejděte na stránku **šablony zařízení** a zobrazte šablonu, kterou IoT Central vytvořili z DCM ve veřejném úložišti:

    ![Stránka šablony zařízení](./media/tutorial-connect-pnp-device/device-template.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak připojit zařízení IoT technologie Plug and Play, které bylo vygenerováno z DCM v úložišti veřejného modelu.

Pokud se chcete dozvědět víc o DCMs a vytváření vlastních modelů, přejděte k Průvodci postupy:

> [!div class="nextstepaction"]
> [Vytvoření skupiny zařízení](./tutorial-use-device-groups.md)
