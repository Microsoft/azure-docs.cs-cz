---
title: Připojení ukázkového kódu zařízení IoT Plug and Play Preview ke službě IoT Hub | Dokumenty společnosti Microsoft
description: Pomocí jazyka C# (.NET) sestavte a spusťte ukázkový kód zařízení IoT Plug and Play Preview, který se připojuje k centru IoT. Pomocí nástroje Průzkumník Avistu Azure můžete zobrazit informace odeslané zařízením do centra.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 90d39635ac6302f816f39ca19cc00a39cfbbf850
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77121011"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-c"></a>Úvodní příručka: Připojení ukázkové aplikace zařízení IoT Plug and Play Preview do služby IoT Hub (C#)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Tento rychlý start ukazuje, jak vytvořit ukázkovou aplikaci zařízení IoT Plug and Play, připojit ji k centru IoT a použít nástroj Průzkumník Azure IoT k zobrazení informací, které odesílá do centra. Ukázková aplikace je zapsána v C# (s .NET) a je k dispozici jako součást ukázky Azure IoT pro kolekci C# (.NET). Vývojář řešení můžete použít nástroj Průzkumník Azure IoT pochopit možnosti zařízení IoT Plug and Play bez nutnosti zobrazení kódu zařízení.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit tento rychlý start, je třeba nainstalovat .NET Core 3.0 ve vývojovém počítači. Tuto verzi sady .NET Core SDK pro více platforem si můžete stáhnout ze [stažení .NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0).

Verzi rozhraní .NET, která je ve vývojovém počítači, můžete ověřit spuštěním následujícího příkazu v místním okně terminálu: 

```cmd/sh
dotnet --version
```

### <a name="install-the-azure-iot-explorer"></a>Instalace Průzkumníka Azure IoT

Stáhněte a nainstalujte nejnovější verzi **Průzkumníka Azure IoT** ze stránky [úložiště](https://github.com/Azure/azure-iot-explorer/releases) nástroje výběrem souboru MSI v části "Prostředky" pro nejnovější aktualizaci.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Spusťte následující příkaz, abyste získali _připojovací řetězec centra IoT hub_ pro vaše centrum (poznámka pro pozdější použití):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

V tomto rychlém startu připravíte vývojové prostředí, které můžete použít ke klonování a sestavení ukázky Azure IoT pro C# (.NET).

Otevřete příkazový řádek v adresáři, který si vyberete. Spusťte následující příkaz pro klonování [azure iot ukázky pro úložiště GitHub C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) do tohoto umístění:

```cmd/sh
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
```

Tato operace může trvat několik minut.

## <a name="run-the-device-sample"></a>Spuštění ukázky zařízení

Klonovaný ukázkový kód se používá k vytvoření aplikace, která simuluje zařízení, které se připojuje k centru IoT. Aplikace odesílá telemetrii a vlastnosti a přijímá příkazy.

1. V okně místního terminálu přejděte do složky vašeho klonovaného úložiště a přejděte do složky **azure-iot-samples-csharp/digitaltwin/Samples/device/EnvironmentalSensorSample.** 

1. Konfigurace _připojovacího řetězce zařízení_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Spusťte ukázkovou aplikaci a simulujte zařízení IoT Plug and Play, které odesílá telemetrii do vašeho centra IoT Hub. Ve stejném okně terminálu, chcete-li vytvořit potřebné balíčky a spustit ukázkovou aplikaci, použijte následující příkaz:

    ```cmd\sh
    dotnet run --framework=netcoreapp3.0
    ```

Zobrazí se zprávy, že se zařízení úspěšně zaregistrovalo a čeká na aktualizace z cloudu. To znamená, že zařízení je nyní připraven o příjem příkazů a aktualizace vlastností a začal oodesílání telemetrických dat do rozbočovače. Po nechat vzorek běží při dokončení dalších kroků.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Ověření kódu pomocí průzkumníka Azure IoT

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Chcete-li zajistit, aby nástroj mohl číst definice modelu rozhraní ze zařízení, vyberte **nastavení**. V nabídce Nastavení se **na připojeném zařízení** již může zobrazit v konfiguracích Plug and Play. Pokud tomu tak není, vyberte **+ Přidat zdroj definice modulu** a pak na **připojeném zařízení** jej přidat.

1. Zpět na stránce **Přehled zařízení** najděte identitu zařízení, kterou jste vytvořili dříve. Pokud je aplikace zařízení stále spuštěná v příkazovém řádku, zkontrolujte, jestli **stav připojení** zařízení v průzkumníku Azure IoT hlásí jako _Připojený_ (pokud ne, stiskněte **tlačítko Aktualizovat,** dokud nebude). Výběrem zařízení zobrazíte další podrobnosti.

1. Rozbalte rozhraní s id **urn:csharp_sdk_sample:EnvironmentalSensor:1** a odhalte rozhraní a základní prvky IoT Plug and Play – vlastnosti, příkazy a telemetrii.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak připojit zařízení IoT Plug and Play k centru IoT. Další informace o tom, jak vytvořit řešení, které spolupracuje s vašimi zařízeními IoT Plug and Play, najdete v tématu:

> [!div class="nextstepaction"]
> [Postup: Připojení k zařízení IoT Plug and Play Preview a jeho práce s ním](howto-develop-solution.md)
