---
title: Připojení ukázkového kódu zařízení IoT Plug and Play Preview ke službě IoT Hub (Windows) | Dokumenty společnosti Microsoft
description: Vytvořte a spusťte ukázkový kód zařízení IoT Plug and Play Preview v systému Windows, který se připojuje k centru IoT Hub. Pomocí nástroje Průzkumník Avistu Azure můžete zobrazit informace odeslané zařízením do centra.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 080820024db1302ff5a841761428442396b90040
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769849"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-windows-to-iot-hub-c-windows"></a>Úvodní příručka: Připojení ukázkové aplikace zařízení IoT Plug and Play Preview spuštěné ve Windows do služby IoT Hub (C Windows)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Tento rychlý start ukazuje, jak vytvořit ukázkovou aplikaci zařízení IoT Plug and Play, připojit ji k centru IoT a použít nástroj Průzkumník Azure IoT k zobrazení informací, které odesílá do centra. Ukázková aplikace je napsaná v C a je součástí zařízení Azure IoT Hub C SDK. Vývojář řešení můžete použít nástroj Průzkumník Azure IoT pochopit možnosti zařízení IoT Plug and Play bez nutnosti zobrazení kódu zařízení.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Požadavky

Chcete-li tento rychlý start dokončit, je třeba do místního počítače nainstalovat následující software:

* [Visual Studio (Community, Professional nebo Enterprise)](https://visualstudio.microsoft.com/downloads/) – ujistěte se, že při instalaci sady Visual Studio zahrnete komponentu **Správce balíčků NuGet** a vývoj plochy s úlohami **jazyka C++.**
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

### <a name="install-the-azure-iot-explorer"></a>Instalace Průzkumníka Azure IoT

Stáhněte a nainstalujte nejnovější verzi **Průzkumníka Azure IoT** ze stránky [úložiště](https://github.com/Azure/azure-iot-explorer/releases) nástroje výběrem souboru MSI v části "Prostředky" pro nejnovější aktualizaci.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Spusťte následující příkaz, abyste získali _připojovací řetězec centra IoT hub_ pro vaše centrum (poznámka pro pozdější použití):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

V tomto rychlém startu připravíte vývojové prostředí, které můžete použít ke klonování a sestavení sady Azure IoT Hub Device C SDK.

Otevřete příkazový řádek v adresáři, který si vyberete. Spusťte následující příkaz pro klonování úložiště GitHub [Azure IoT C a knihovny](https://github.com/Azure/azure-iot-sdk-c) GitHub do tohoto umístění:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.

## <a name="build-the-code"></a>Sestavení kódu

Pomocí sady SDK zařízení můžete vytvořit přiložený ukázkový kód. Aplikace, kterou vytvoříte, simuluje zařízení, které se připojuje k centru IoT. Aplikace odesílá telemetrii a vlastnosti a přijímá příkazy.

1. Vytvořte `cmake` podadresář v kořenové složce sady SDK zařízení a přejděte do této složky:

    ```cmd\sh
    cd <root folder>\azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Spusťte následující příkazy k vytvoření sady SDK zařízení a generovaného kódu se zakázaným inzerováním:

    ```cmd\sh
    cmake ..
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > Pokud cmake nemůže najít kompilátor Jazyka C++, zobrazí se chyby sestavení při spuštění předchozího příkazu. Pokud k tomu dojde, zkuste spustit tento příkaz na [příkazovém řádku sady Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

## <a name="run-the-device-sample"></a>Spuštění ukázky zařízení

Spusťte ukázkovou aplikaci v sadě SDK a simulujte zařízení IoT Plug and Play, které odesílá telemetrii do vašeho centra IoT Hub. Chcete-li spustit ukázkovou aplikaci, použijte tyto příkazy a předajte jako parametr _připojovací řetězec zařízení._

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "<YourDeviceConnectionString>"
```

Zařízení je nyní připraveno přijímat příkazy a aktualizace vlastností a začalo odesílat telemetrická data do centra. Po nechat vzorek běží při dokončení dalších kroků.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Ověření kódu pomocí průzkumníka Azure IoT

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Chcete-li zajistit, aby nástroj mohl číst definice modelu rozhraní ze zařízení, vyberte **nastavení**. V nabídce Nastavení se **na připojeném zařízení** již může zobrazit v konfiguracích Plug and Play. Pokud tomu tak není, vyberte **+ Přidat zdroj definice modulu** a pak na **připojeném zařízení** jej přidat.

1. Zpět na stránce **Přehled zařízení** najděte identitu zařízení, kterou jste vytvořili dříve. Pokud je aplikace zařízení stále spuštěná v příkazovém řádku, zkontrolujte, jestli **stav připojení** zařízení v průzkumníku Azure IoT hlásí jako _Připojený_ (pokud ne, stiskněte **tlačítko Aktualizovat,** dokud nebude). Výběrem zařízení zobrazíte další podrobnosti.

1. Rozbalte rozhraní s ID **urn:YOUR_COMPANY_NAME_HERE:EnvironmentalSensor:1** a odhalte rozhraní a základní prvky IoT Plug and Play – vlastnosti, příkazy a telemetrii.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak připojit zařízení IoT Plug and Play k centru IoT. Další informace o tom, jak vytvořit řešení, které spolupracuje s vašimi zařízeními IoT Plug and Play, najdete v tématu:

> [!div class="nextstepaction"]
> [Postup: Připojení k zařízení IoT Plug and Play Preview a jeho práce s ním](howto-develop-solution.md)
