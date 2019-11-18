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
ms.openlocfilehash: 0d89be9da55c97a5b49157251896d3a513c2c6db
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152066"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-windows-to-iot-hub-c-windows"></a>Rychlý Start: připojení ukázkové aplikace IoT technologie Plug and Play ve verzi Preview, která běží na Windows to IoT Hub (C Windows)

V tomto rychlém startu se dozvíte, jak vytvořit ukázkovou aplikaci IoT technologie Plug and Play zařízení, jak ji připojit ke službě IoT Hub, a pomocí nástroje Azure IoT Explorer zobrazit informace, které odesílá do centra. Ukázková aplikace je napsaná v jazyce C a je součástí sady Azure IoT Hub zařízení C SDK. Vývojář řešení může pomocí nástroje Azure IoT Explorer pochopit možnosti zařízení technologie Plug and Play IoT, aniž by musel zobrazovat žádný kód zařízení.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu je potřeba na svůj místní počítač nainstalovat následující software:

* [Visual Studio (komunita, Professional nebo Enterprise)](https://visualstudio.microsoft.com/downloads/) – nezapomeňte při instalaci sady Visual Studio zahrnout komponentu **Správce balíčků NuGet** a **vývoj C++ desktopových** aplikací.
* [Git](https://git-scm.com/download/)
* [Cmake](https://cmake.org/download/).

### <a name="install-the-azure-iot-explorer"></a>Instalace Azure IoT Exploreru

Stáhněte a nainstalujte si nejnovější verzi **Azure IoT Exploreru** ze stránky [úložiště](https://github.com/Azure/azure-iot-explorer/releases) tohoto nástroje tak, že vyberete soubor. msi v části assets (prostředky) pro nejnovější aktualizaci.

[!INCLUDE [iot-pnp-prepare-iot-hub-windows.md](../../includes/iot-pnp-prepare-iot-hub-windows.md)]

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

V tomto rychlém startu připravíte vývojové prostředí, které můžete použít k klonování a sestavování sady Azure IoT Hub zařízení C SDK.

Otevřete příkazový řádek v adresáři dle vašeho výběru. Spusťte následující příkaz, který naklonuje úložiště GitHub sady [Azure IoT C a knihovny](https://github.com/Azure/azure-iot-sdk-c) do tohoto umístění:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.

## <a name="build-the-code"></a>Sestavení kódu

Pomocí sady SDK pro zařízení sestavíte zahrnutý vzorový kód. Vytvořená aplikace simuluje zařízení, které se připojuje ke službě IoT Hub. Aplikace odesílá telemetrie a vlastnosti a přijímá příkazy.

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

Spusťte v sadě SDK ukázkovou aplikaci, která simuluje zařízení IoT technologie Plug and Play, které odesílá telemetrii do služby IoT Hub. Chcete-li spustit ukázkovou aplikaci, použijte tyto příkazy a jako parametr předejte _připojovací řetězec zařízení_ .

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "<YourDeviceConnectionString>"
```

Zařízení je teď připravené přijmout příkazy a aktualizace vlastností a zahájilo posílání dat telemetrie do centra. Ponechte ukázku spuštěnou při dokončení dalších kroků.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Použití Průzkumníka Azure IoT k ověření kódu

1. Otevřete Azure IoT Explorer. Zobrazí se stránka **Konfigurace aplikace** .

1. Zadejte _připojovací řetězec IoT Hub_ a vyberte **připojit**.

1. Po připojení se zobrazí stránka s přehledem **zařízení** .

1. Chcete-li zajistit, aby nástroj mohl číst definice modelů rozhraní ze zařízení, vyberte **Nastavení**. V nabídce nastavení se **v připojeném zařízení** už může zobrazit konfigurace technologie Plug and Play. Pokud ne, vyberte **+ Přidat zdroj definice modulu** a pak **na připojeném zařízení** ho přidejte.

1. Zpátky na stránce Přehled **zařízení** vyhledejte identitu zařízení, kterou jste vytvořili dříve. Když je aplikace zařízení pořád spuštěná na příkazovém řádku, ověřte, že se **stav připojení** zařízení v Azure IoT Exploreru hlásí jako _připojené_ (Pokud ne, stiskněte **aktualizovat** , dokud není). Kliknutím na zařízení zobrazíte další podrobnosti.

1. Rozbalte rozhraní s IDENTIFIKÁTORem **urn: YOUR_COMPANY_NAME_HERE: EnvironmentalSensor: 1** a odhalte rozhraní a technologie Plug and Play primitivních elementů IoT – vlastnosti, příkazy a telemetrie.

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

V tomto rychlém startu jste se naučili připojit zařízení IoT technologie Plug and Play ke službě IoT Hub. Další informace o tom, jak vytvořit řešení, které komunikuje s technologie Plug and Play vašich zařízení IoT, najdete tady:

> [!div class="nextstepaction"]
> [Postupy: připojení a interakce se zařízením IoT technologie Plug and Play Preview](howto-develop-solution.md)
