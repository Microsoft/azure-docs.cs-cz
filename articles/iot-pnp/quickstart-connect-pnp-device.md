---
title: Připojení ukázkového kódu zařízení pro IoT technologie Plug and Play Preview IoT Hub (Windows) | Microsoft Docs
description: Sestavujte a spouštějte ukázkový kód zařízení ve Windows ve verzi Preview technologie Plug and Play, který se připojuje ke centru IoT. K zobrazení informací odesílaných zařízením do centra použijte nástroj Azure IoT Explorer.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 2dd5d197851b0090ac1af7bbde5a1ad1b951c785
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73569910"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-windows-to-iot-hub"></a>Rychlý Start: připojení ukázkové aplikace IoT technologie Plug and Play ve verzi Preview, která běží na Windows pro IoT Hub

V tomto rychlém startu se dozvíte, jak vytvořit ukázkovou aplikaci IoT technologie Plug and Play zařízení, jak ji připojit ke službě IoT Hub, a pomocí nástroje Azure IoT Explorer zobrazit informace, které odesílá do centra. Ukázková aplikace je napsaná v jazyce C a je obsažená v sadě SDK pro zařízení Azure IoT pro jazyk C. Vývojář řešení může pomocí nástroje Azure IoT Explorer pochopit možnosti zařízení technologie Plug and Play IoT, aniž by musel zobrazovat žádný kód zařízení.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu je potřeba na svůj místní počítač nainstalovat následující software:

* [Nástroje pro sestavení pro sadu Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) pomocí  **C++ nástrojů sestavení** a úloh **komponent správce balíčků NuGet** . Nebo pokud už máte [Visual Studio (Community, Professional nebo Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 nebo 2015 se stejnými úlohami.
* [Git](https://git-scm.com/download/)
* [Cmake](https://cmake.org/download/).

### <a name="install-the-azure-iot-explorer"></a>Instalace Azure IoT Exploreru

Stáhněte a nainstalujte si nástroj Azure IoT Explorer ze stránky [nejnovější verze](https://github.com/Azure/azure-iot-explorer/releases) .

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

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

### <a name="get-azure-iot-device-sdk-for-c"></a>Získat sadu SDK pro zařízení Azure IoT pro jazyk C

V tomto rychlém startu připravíte vývojové prostředí, které můžete použít k klonování a sestavení sady SDK pro zařízení Azure IoT C.

Otevřete příkazový řádek. Spusťte následující příkaz pro naklonování úložiště GitHub sady [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c):

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.

## <a name="build-the-code"></a>Sestavení kódu

Vytvořená aplikace simuluje zařízení, které se připojuje ke službě IoT Hub. Aplikace odesílá telemetrie a vlastnosti a přijímá příkazy.

1. V kořenové složce sady SDK pro zařízení vytvořte podadresář `cmake` a přejděte do této složky:

    ```cmd\sh
    cd <root folder>\azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Spusťte následující příkazy a Sestavte sadu SDK pro zařízení a vygenerovanou zástupné kódy:

    ```cmd\sh
    cmake ..
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > Pokud cmake nenajde váš C++ kompilátor, při spuštění předchozího příkazu se zobrazí chyby sestavení. Pokud k tomu dojde, zkuste spustit tento příkaz na příkazovém [řádku sady Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

## <a name="run-the-device-sample"></a>Spuštění ukázky zařízení

Spusťte aplikaci předáním připojovacího řetězce zařízení služby IoT Hub jako parametru.

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "[IoT Hub device connection string]"
```

Aplikace zařízení spouští odesílání dat do IoT Hub.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Použití Průzkumníka Azure IoT k ověření kódu

1. Otevřete Azure IoT Explorer a zobrazí se stránka **Konfigurace aplikace** .

1. Zadejte připojovací řetězec IoT Hub a klikněte na **připojit**.

1. Po připojení se zobrazí stránka s přehledem zařízení.

1. Pokud chcete přidat úložiště společnosti, vyberte **Nastavení**, potom **+ Nový**a pak **na připojeném zařízení**.

1. Na stránce Přehled zařízení vyhledejte identitu zařízení, kterou jste vytvořili dříve, a vyberte ji, abyste zobrazili další podrobnosti.

1. Rozbalením rozhraní s ID **urn: YOUR_COMPANY_NAME_HERE: EnvironmentalSensor: 1** zobrazíte technologie Plug and Play primitivních elementů IoT – vlastnosti, příkazy a telemetrie.

1. Výběrem stránky **telemetrie** zobrazíte data telemetrie, která zařízení odesílá.

1. Vyberte stránku **vlastnosti (bez možnosti zápisu)** , ve které se zobrazí nezapisovatelné vlastnosti hlášené zařízením.

1. Výběrem stránky **vlastnosti (zapisovatelné)** zobrazíte vlastnosti, které můžete aktualizovat.

1. Rozbalte **název**vlastnosti, aktualizujte ho novým názvem a vyberte možnost **aktualizovat zapisovatelnou vlastnost**. 

1. Chcete-li zobrazit nový název zobrazený ve sloupci **nahlášená vlastnost** , klikněte na tlačítko **aktualizovat** v horní části stránky.

1. Výběrem stránky **příkazů** zobrazíte všechny příkazy, které zařízení podporuje.

1. Rozbalte příkaz **Blink** a nastavte nový časový interval pro blikání. Vyberte **Odeslat příkaz** pro volání příkazu na zařízení.

1. Přejít na simulované zařízení a ověřte, že se příkaz provedl podle očekávání.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili připojit zařízení IoT technologie Plug and Play ke službě IoT Hub. Další informace o tom, jak vytvořit řešení, které komunikuje s technologie Plug and Play vašich zařízení IoT, najdete tady:

> [!div class="nextstepaction"]
> [Postupy: připojení a interakce se zařízením IoT technologie Plug and Play Preview](howto-develop-solution.md)
