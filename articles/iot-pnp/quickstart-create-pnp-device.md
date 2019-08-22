---
title: Vytvoření zařízení Azure IoT technologie Plug and Play Preview | Microsoft Docs
description: Pomocí modelu schopností zařízení vygenerujte kód zařízení. Pak spusťte kód zařízení a zkontrolujte, že se zařízení připojí k vašemu IoT Hub.
author: miagdp
ms.author: miag
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 7cfa6e63f74233e9a3fab8f235584fdbe01e67d9
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878212"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-device"></a>Rychlý start: Vytvoření zařízení IoT technologie Plug and Play pomocí modelu schopností zařízení

_Model schopností zařízení_ (DCM) popisuje možnosti zařízení IoT technologie Plug and Play. DCM je často spojený s SKU produktu. Funkce definované v DCM jsou uspořádané do opakovaně použitelných rozhraní. Z DCM můžete vygenerovat kostru kódu zařízení. V tomto rychlém startu se dozvíte, jak pomocí VS Code vytvořit zařízení IoT technologie Plug and Play pomocí DCM.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu je potřeba na svůj místní počítač nainstalovat následující software:

* [Visual Studio (komunita, Professional nebo Enterprise)](https://visualstudio.microsoft.com/downloads/) – nezapomeňte při instalaci sady Visual Studio zahrnout komponentu **Správce balíčků NuGet** a **vývoj C++ desktopových** aplikací.
* [Git](https://git-scm.com/download/)
* [Cmake](https://cmake.org/download/).
* [Visual Studio Code](https://code.visualstudio.com/).

### <a name="install-azure-iot-device-workbench"></a>Instalace aplikace Workbench pro zařízení Azure IoT

Pomocí následujících kroků nainstalujete rozšíření Azure IoT Device Workbench do VS Code:

1. V VS Code vyberte kartu **rozšíření** .
1. Vyhledejte aplikaci **Workbench pro zařízení Azure IoT**.
1. Vyberte **Install** (Nainstalovat).

### <a name="install-the-azure-iot-explorer"></a>Instalace Azure IoT Exploreru

Stáhněte a nainstalujte si nástroj Azure IoT Explorer ze stránky [nejnovější verze](https://github.com/Azure/azure-iot-explorer/releases) .

### <a name="get-the-connection-string-for-your-company-model-repository"></a>Získání připojovacího řetězce pro úložiště podnikového modelu

_Připojovací řetězec úložiště modelů společnosti_ můžete najít na portálu [Azure Certified for IoT Portal](https://preview.catalog.azureiotsolutions.com) při přihlášení pomocí pracovního nebo školního účtu Microsoft nebo svého ID partnera Microsoftu, pokud ho máte. Po přihlášení vyberte **úložiště společnosti** a pak **připojovací řetězce**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>Příprava služby IoT Hub

K dokončení tohoto rychlého startu budete také potřebovat službu Azure IoT Hub v rámci vašeho předplatného Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

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

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

### <a name="get-azure-iot-device-sdk-for-c"></a>Získat sadu SDK pro zařízení Azure IoT pro jazyk C

V tomto rychlém startu připravíte vývojové prostředí, které můžete použít k klonování a sestavení sady SDK pro zařízení Azure IoT C.

1. Otevřete příkazový řádek. Spusťte následující příkaz pro naklonování úložiště GitHub sady [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c):

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.

1. `pnp_app` Vytvořte podadresář v kořenovém adresáři místního klonu úložiště. Tuto složku použijete pro soubory modelů zařízení a pro zástupné kódy zařízení.

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir pnp_app
    ```

## <a name="author-your-model"></a>Vytváření modelu

V tomto rychlém startu použijete existující Vzorový model zařízení a přidružená rozhraní.

1. Stáhněte si [model funkce zařízení](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/SampleDevice.capabilitymodel.json) a [ukázku rozhraní](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/EnvironmentalSensor.interface.json) a uložte soubory do `pnp_app` složky.

    > [!TIP]
    > Pokud chcete stáhnout soubor z GitHubu, přejděte do souboru, klikněte pravým tlačítkemna nezpracovaný a pak vyberte **Uložit odkaz jako**.

1. Otevřete `pnp_app` složku s vs Code. Můžete zobrazit soubory pomocí IntelliSense:

    ![Model schopností zařízení](media/quickstart-create-pnp-device/dcm.png)

1. V souborech, které jste stáhli `<YOUR_COMPANY_NAME_HERE>` , nahraďte `schema` `@id` v polích a jedinečnou hodnotou. Používejte pouze znaky a-z, A-Z, 0-9 a podtržítko. Další informace najdete v tématu [Formát digitálního](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format)nevlákenového identifikátoru.

## <a name="generate-the-c-code-stub"></a>Generovat zástupný kód kódu jazyka C

Teď máte modul DCM a jeho přidružená rozhraní, můžete vygenerovat kód zařízení, který model implementuje. Chcete-li vygenerovat zástupnou proceduru kódu C v VS Code:

1. Po otevření složky s otevřenými soubory DCM použijte **kombinaci kláves CTRL + SHIFT + P** k otevření palety příkazů, zadejte **IoT technologie Plug and Play**a vyberte možnost **Generovat zástupný kód zařízení**.

    > [!NOTE]
    > Když použijete nástroj pro generátor kódu IoT technologie Plug and Play poprvé, bude stažení trvat několik sekund.

1. Vyberte soubor DCM, který chcete použít k vygenerování zástupné procedury kódu zařízení.

1. Zadejte název projektu **sample_device**, bude to název vaší aplikace zařízení.

1. Jako jazyk vyberte **ANSI C** .

1. Jako typ projektu vyberte **cmake projekt** .

1. Jako metodu připojení vyberte **prostřednictvím IoT Hub připojovací řetězec zařízení** .

1. VS Code otevře nové okno s generovanými zástupnými soubory kódu zařízení.
    ![Kód zařízení](media/quickstart-create-pnp-device/device-code.png)

## <a name="build-the-code"></a>Sestavení kódu

Pomocí sady SDK pro zařízení sestavíte nevygenerovanou zástupné kódy zařízení. Vytvořená aplikace simuluje zařízení, které se připojuje ke službě IoT Hub. Aplikace odesílá telemetrie a vlastnosti a přijímá příkazy.

1. V vs Code otevřete `CMakeLists.txt` v kořenové složce sady SDK pro zařízení.

1. Do dolní `CMakeLists.txt` části souboru přidejte řádek, aby při kompilování zahrnoval složku se zástupným kódem zařízení:

    ```txt
    add_subdirectory(pnp_app/sample_device)
    ```

1. V kořenové složce sady SDK pro zařízení vytvořte podadresář cmake a přejděte do této složky:

    ```cmd\sh
    mkdir cmake
    cd cmake
    ```

1. Spusťte následující příkazy a Sestavte sadu SDK pro zařízení a vygenerovanou zástupné kódy:

    ```cmd\sh
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > Pokud cmake nenajde váš C++ kompilátor, při spuštění předchozího příkazu se zobrazí chyby sestavení. Pokud k tomu dojde, zkuste spustit tento příkaz na příkazovém [řádku sady Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

1. Po úspěšném dokončení sestavení spusťte aplikaci předáním připojovacího řetězce zařízení služby IoT Hub jako parametru.

    ```cmd\sh
    cd azure-iot-sdk-c\cmake\pnp_app\sample_device\Release\
    sample_device.exe "[IoT Hub device connection string]"
    ```

1. Aplikace zařízení spouští odesílání dat do IoT Hub.

    ![Aplikace pro zařízení je spuštěná.](media/quickstart-create-pnp-device/device-app-running.png)

## <a name="validate-the-code"></a>Ověřit kód

### <a name="publish-device-model-files-to-model-repository"></a>Publikování souborů modelů zařízení do úložiště modelů

K ověření kódu zařízení v **Azure IoT Exploreru**je potřeba publikovat soubory do úložiště modelu.

1. Po otevření složky s otevřenými soubory DCM použijte **kombinaci kláves CTRL + SHIFT + P** a otevřete paletu příkazů, zadejte **a vyberte IoT plug & Play: Odešle soubory do úložiště**modelu.

1. Vyberte `SampleDevice.capabilitymodel.json` soubory `EnvironmentalSensor.interface.json` a.

1. Zadejte připojovací řetězec úložiště podnikového modelu.

    > [!NOTE]
    > Připojovací řetězec je vyžadován pouze při prvním připojení k úložišti.

1. V okně výstup VS Code a oznámení můžete ověřit, zda byly soubory úspěšně publikovány.

    > [!NOTE]
    > Pokud se zobrazí chyby při publikování souborů modelů zařízení, můžete zkusit použít příkaz **IoT technologie Plug and Play: Odhlaste se** pomocí úložiště modelu a odhlaste se a Projděte kroky znovu.

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
2. Chcete-li zobrazit nový název zobrazený ve sloupci nahlášená **vlastnost** , klikněte na tlačítko **aktualizovat** v horní části stránky.

1. Výběrem stránky **příkazů** zobrazíte všechny příkazy, které zařízení podporuje.

1. Rozbalte příkaz **Blink** a nastavte nový časový interval pro blikání. Vyberte **Odeslat příkaz** pro volání příkazu na zařízení.

1. Přejít na simulované zařízení a ověřte, že se příkaz provedl podle očekávání.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste zjistili, jak vytvořit zařízení IoT technologie Plug and Play pomocí DCM.

Další informace o službě IoT technologie Plug and Play najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Vytvoření a otestování modelu schopností zařízení pomocí Visual Studio Code](tutorial-pnp-visual-studio-code.md)
