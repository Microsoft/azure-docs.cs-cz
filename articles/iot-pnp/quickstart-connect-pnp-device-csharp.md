---
title: Připojení ukázkového kódu zařízení pro IoT technologie Plug and Play Preview IoT Hub | Microsoft Docs
description: Pomocí C# (.NET) můžete sestavovat a spouštět technologie Plug and Play IoT Preview ukázkový kód zařízení, který se připojuje ke centru IoT. K zobrazení informací odesílaných zařízením do centra použijte nástroj Azure IoT Explorer.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b86f58a1b6b9e3a952861dbe7dbeef4dfe06a644
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964827"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-c"></a>Rychlý Start: připojení ukázkové aplikace IoT technologie Plug and Play ve verzi Preview do IoT HubC#()

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

V tomto rychlém startu se dozvíte, jak vytvořit ukázkovou aplikaci IoT technologie Plug and Play zařízení, jak ji připojit ke službě IoT Hub, a pomocí nástroje Azure IoT Explorer zobrazit informace, které odesílá do centra. Ukázková aplikace je napsaná C# v (s .NET) a poskytuje se jako součást ukázek Azure IoT pro C# (.NET). Vývojář řešení může pomocí nástroje Azure IoT Explorer pochopit možnosti zařízení technologie Plug and Play IoT, aniž by musel zobrazovat žádný kód zařízení.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu je potřeba na svém vývojovém počítači nainstalovat .NET Core 2,2. Tuto verzi .NET Core SDK můžete stáhnout pro více platforem od [stažení .NET Core 2,2](https://dotnet.microsoft.com/download/dotnet-core/2.2).

Verzi rozhraní .NET, která je na vašem vývojovém počítači, můžete ověřit spuštěním následujícího příkazu v místním okně terminálu: 

```cmd/sh
dotnet --version
```

### <a name="install-the-azure-iot-explorer"></a>Instalace Azure IoT Exploreru

Stáhněte a nainstalujte si nejnovější verzi **Azure IoT Exploreru** ze stránky [úložiště](https://github.com/Azure/azure-iot-explorer/releases) tohoto nástroje tak, že vyberete soubor. msi v části assets (prostředky) pro nejnovější aktualizaci.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Spuštěním následujícího příkazu Získejte _připojovací řetězec služby IoT Hub_ pro vaše centrum (Poznámka pro pozdější použití):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

V tomto rychlém startu připravíte vývojové prostředí, které můžete použít k klonování a sestavení ukázek Azure IoT pro C# (.NET).

Otevřete příkazový řádek v adresáři dle vašeho výběru. Spusťte následující příkaz, který naklonuje úložiště GitHubu [Azure IoT pro C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) do tohoto umístění:

```cmd/sh
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
```

Dokončení této operace může trvat několik minut.

## <a name="run-the-device-sample"></a>Spuštění ukázky zařízení

Pomocí klonovaného ukázkového kódu sestavíte aplikaci, která simuluje zařízení, které se připojuje ke službě IoT Hub. Aplikace odesílá telemetrie a vlastnosti a přijímá příkazy.

1. V místním okně terminálu přejděte do složky vašeho klonovaného úložiště a přejděte do složky **Azure-IoT-Samples-CSharp/digitaltwin/Samples/Device/EnvironmentalSensorSample** . 

1. Konfigurace _připojovacího řetězce zařízení_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Spusťte ukázkovou aplikaci pro simulaci technologie Plug and Play zařízení IoT, které odesílá telemetrii do služby IoT Hub. Ve stejném okně terminálu Sestavte potřebné balíčky a spusťte ukázkovou aplikaci pomocí následujícího příkazu:

    ```cmd\sh
        dotnet run
    ```

Zobrazí se zpráva oznamující, že se zařízení úspěšně zaregistrovalo a čeká na aktualizace z cloudu. To znamená, že zařízení je nyní připraveno přijímat příkazy a aktualizace vlastností a zahájilo posílání dat telemetrie do centra. Ponechte ukázku spuštěnou při dokončení dalších kroků.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Použití Průzkumníka Azure IoT k ověření kódu

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Chcete-li zajistit, aby nástroj mohl číst definice modelů rozhraní ze zařízení, vyberte **Nastavení**. V nabídce nastavení se **v připojeném zařízení** už může zobrazit konfigurace technologie Plug and Play. Pokud ne, vyberte **+ Přidat zdroj definice modulu** a pak **na připojeném zařízení** ho přidejte.

1. Zpátky na stránce Přehled **zařízení** vyhledejte identitu zařízení, kterou jste vytvořili dříve. Když je aplikace zařízení pořád spuštěná na příkazovém řádku, ověřte, že se **stav připojení** zařízení v Azure IoT Exploreru hlásí jako _připojené_ (Pokud ne, stiskněte **aktualizovat** , dokud není). Kliknutím na zařízení zobrazíte další podrobnosti.

1. Rozbalte rozhraní s IDENTIFIKÁTORem **urn: csharp_sdk_sample: EnvironmentalSensor: 1** a odhalte rozhraní a technologie Plug and Play primitivních elementů IoT – vlastnosti, příkazy a telemetrie.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili připojit zařízení IoT technologie Plug and Play ke službě IoT Hub. Další informace o tom, jak vytvořit řešení, které komunikuje s technologie Plug and Play vašich zařízení IoT, najdete tady:

> [!div class="nextstepaction"]
> [Postupy: připojení a interakce se zařízením IoT technologie Plug and Play Preview](howto-develop-solution.md)
