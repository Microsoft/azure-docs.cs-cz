---
title: Připojení zařízení IoT technologie Plug and Play ve verzi Preview ke službě Azure IoT Central | Microsoft Docs
description: Pomocí modelu schopností zařízení vygenerujte kód zařízení. Pak spusťte kód zařízení, přečtěte si téma připojení k aplikaci IoT Central a použijte automaticky vygenerovaná zobrazení.
author: dominicbetts
ms.author: dobett
ms.date: 08/08/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: bc7858aeceea7cbd35bc2d834ddfb4fb5d656321
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174835"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-device-and-connect-it-to-your-iot-central-application"></a>Rychlý Start: použití modelu schopností zařízení k vytvoření zařízení IoT technologie Plug and Play a jeho připojení k aplikaci IoT Central

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

_Model schopností zařízení_ (DCM) popisuje možnosti zařízení [IoT technologie Plug and Play](../iot-pnp/overview-iot-plug-and-play.md) . IoT Central může použít DCM k vytvoření šablony zařízení a vizualizací pro zařízení, když se zařízení poprvé připojí. V tomto rychlém startu se naučíte:

* Pomocí Visual Studio Code vytvořit zařízení IoT technologie Plug and Play pomocí DCM.
* Spusťte kód zařízení ve Windows a podívejte se, jak se připojí k vaší IoT Central aplikaci.
* Podívejte se na simulovanou telemetrii, kterou zařízení odesílá.

## <a name="prerequisites"></a>Předpoklady

Dokončete průvodce [vytvořením aplikace Azure IoT Central (náhled funkcí)](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) a vytvořte IoT Central aplikaci pomocí šablony **aplikace Preview** .

K dokončení tohoto rychlého startu je potřeba na svůj místní počítač nainstalovat následující software:

* [Visual Studio (komunita, Professional nebo Enterprise)](https://visualstudio.microsoft.com/downloads/) – nezapomeňte při instalaci sady Visual Studio zahrnout komponentu **Správce balíčků NuGet** a **vývoj C++ desktopových** aplikací.
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

### <a name="get-azure-iot-device-sdk-for-c"></a>Získat sadu SDK pro zařízení Azure IoT pro jazyk C

Připravte vývojové prostředí, které můžete použít k sestavení sady SDK pro zařízení Azure IoT C.

1. Otevřete příkazový řádek. Spusťte následující příkaz pro naklonování úložiště GitHub sady [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c):

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.

1. Vytvořte složku `central_app` v kořenovém adresáři místního klonu úložiště. Tuto složku použijete pro soubory modelů zařízení a pro zástupné kódy zařízení.

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir central_app
    ```

## <a name="generate-device-key"></a>Generovat klíč zařízení

Pokud chcete zařízení připojit k IoT Central aplikaci, budete potřebovat klíč zařízení. Vygenerování klíče zařízení:

1. Přihlaste se k aplikaci IoT Central, kterou jste vytvořili v předchozím rychlém startu.

1. Přejít na stránku **Správa** a vyberte **připojení zařízení**.

1. Poznamenejte si **ID oboru** a **primární klíč**. Tyto hodnoty použijete později v tomto rychlém startu.

    ![Připojení zařízení](./media/quick-create-pnp-device-pnp/device-connection.png)

1. Otevřete příkazový řádek a spuštěním následujícího příkazu vygenerujte klíč zařízení:

    ```cmd/sh
    dps-keygen  -di:mxchip-01 -mk:{Primary Key from previous step}
    ```

    Poznamenejte si generovaný _klíč zařízení_a použijte tuto hodnotu v pozdějším kroku v tomto rychlém startu.

## <a name="download-your-model"></a>Stažení modelu

V tomto rychlém startu použijete veřejný DCM pro zařízení MxChip IoT DevKit. Pro spuštění kódu nepotřebujete skutečné zařízení DevKit. v tomto rychlém startu zkompilujete kód, který chcete spustit v systému Windows.

1. Otevřete složku `azure-iot-sdk-c\central_app` s VS Code.

1. Stisknutím **kombinace kláves CTRL + SHIFT + P** otevřete paletu příkazů, zadejte **IoT technologie Plug and Play**a vyberte **Otevřít úložiště modelu**. Vyberte **veřejné úložiště**. VS Code zobrazuje seznam DCMs ve veřejném úložišti modelu.

1. Vyberte **MXChip IoT DevKit** DCM s ID `urn:mxchip:mxchip_iot_devkit:1`. Pak vyberte **Stáhnout**. Nyní máte kopii DCM ve složce `central_app`.

![Úložiště modelu a DCM](./media/quick-create-pnp-device-pnp/public-repository.png)

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

1. Jako typ projektu vyberte **cmake projekt** . Nevybírejte **projekt MXChip IoT DevKit**. Tato možnost je k dispozici i v případě, že máte reálné zařízení DevKit.

1. Jako metodu připojení vyberte **přes DPS (služba Device Provisioning Service) symetrický klíč** .

1. VS Code otevře nové okno s generovanými zástupnými soubory kódu zařízení ve složce `devkit_device`.

![Generovaný kód zařízení](./media/quick-create-pnp-device-pnp/generated-code.png)

Přidání informací o připojení do vygenerovaného kódu zařízení:

1. V okně VS Code, které obsahuje generovaný kód jazyka C. Otevřete soubor `main.c`.

1. Nahraďte `[DPS Id Scope]` s **ID oboru** , které jste si poznamenali dříve.

1. Nahraďte `[DPS symmetric key]` klíčem zařízení, který jste vygenerovali v předchozím kroku.

1. Nahraďte `[device registration Id]` za `mxchip-01` (Jak velká může být moje znalostní báze?).

1. Uložte provedené změny.

## <a name="build-the-code"></a>Sestavení kódu

Pomocí sady SDK pro zařízení sestavíte nevygenerovanou zástupné kódy zařízení. Aplikace, kterou vytváříte, simuluje zařízení **MXChip IoT DevKit** a připojuje se k vaší IoT Central aplikaci. Aplikace odesílá telemetrie a vlastnosti a přijímá příkazy.

1. V VS Code otevřete soubor `CMakeLists.txt` ve složce `azure-iot-sdk-c`. Ujistěte se, že jste otevřeli soubor `CMakeLists.txt` ve složce `azure-iot-sdk-c`, nikoli ve složce `devkit_device`.

1. Do dolní části souboru `CMakeLists.txt` přidejte řádek, který bude při kompilování zahrnovat složku se zástupnými procedurami kódu zařízení:

    ```txt
    add_subdirectory(central_app/devkit_device)
    ```

1. Ve složce `azure-iot-sdk-c` vytvořte složku `cmake` a přejděte do této složky z příkazového řádku:

    ```cmd\sh
    mkdir cmake
    cd cmake
    ```

1. Spusťte následující příkazy a Sestavte sadu SDK pro zařízení a vygenerovanou zástupné kódy:

    ```cmd\sh
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    cmake --build . -- /m /p:Configuration=Release
    ```

1. Po úspěšném dokončení sestavení na stejném příkazovém řádku spusťte aplikaci:

    ```cmd\sh
    .\central_app\devkit_device\Release\devkit_device.exe
    ```

1. Aplikace zařízení spouští odesílání dat do aplikace IoT Central.

## <a name="view-the-device"></a>Zobrazit zařízení

Po připojení kódu zařízení k vašemu IoT Central můžete zobrazit vlastnosti a telemetrie, které odesílá:

1. V aplikaci IoT Central otevřete stránku **zařízení** a vyberte zařízení **mxchip-01** . Toto zařízení se automaticky přidalo při připojení kódu zařízení:

    ![Stránka Přehled](./media/quick-create-pnp-device-pnp/overview-page.png)

    Po několika minutách zobrazí tato stránka grafy telemetrie, které zařízení odesílá.

1. Výběrem stránky **o** se zobrazí hodnoty vlastností odeslané zařízením.

1. Vyberte stránku **příkazy** , na které se budou volat příkazy na zařízení. Na příkazovém řádku, na kterém je spuštěný Kód zařízení, můžete vidět, že zařízení reaguje.

1. Přejděte na stránku **šablony zařízení** a zobrazte šablonu, kterou IoT Central vytvořili z DCM ve veřejném úložišti:

    ![Stránka šablony zařízení](./media/quick-create-pnp-device-pnp/device-template.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak připojit zařízení IoT technologie Plug and Play, které bylo vygenerováno z DCM v úložišti veřejného modelu.

Pokud se chcete dozvědět víc o DCMs a vytváření vlastních modelů, přejděte k Průvodci postupy:

> [!div class="nextstepaction"]
> [Nastavení a Správa šablony zařízení](./howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
