---
title: Připojení ukázkového kódu zařízení IoT Plug and Play Preview ke službě IoT Hub | Dokumenty společnosti Microsoft
description: Pomocí node.js sestavte a spusťte ukázkový kód zařízení IoT Plug and Play Preview, který se připojuje k centru IoT Hub. Pomocí nástroje Průzkumník Avistu Azure můžete zobrazit informace odeslané zařízením do centra.
author: dominicbetts
ms.author: dobett
ms.date: 12/26/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: c5d69ae21cd240b0c68b9694a55de2cf879a1966
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76964783"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-nodejs"></a>Úvodní příručka: Připojení ukázkové aplikace zařízení IoT Plug and Play Preview k centru IoT Hub (Node.js)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Tento rychlý start ukazuje, jak vytvořit ukázkovou aplikaci zařízení IoT Plug and Play, připojit ji k centru IoT a použít nástroj Průzkumník Azure IoT k zobrazení informací, které odesílá do centra. Ukázková aplikace je napsaná pro Node.js a je součástí sady Azure IoT Hub Device SDK pro Node.js. Vývojář řešení můžete použít nástroj Průzkumník Azure IoT pochopit možnosti zařízení IoT Plug and Play bez nutnosti zobrazení kódu zařízení.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit tento rychlý start, potřebujete node.js ve vývojovém počítači. Nejnovější doporučenou verzi pro více platforem si můžete stáhnout z [nodejs.org](https://nodejs.org).

Aktuální verzi Node.js na počítači používaném pro vývoj můžete ověřit pomocí následujícího příkazu:

```cmd/sh
node --version
```

### <a name="install-the-azure-iot-explorer"></a>Instalace Průzkumníka Azure IoT

Stáhněte a nainstalujte nejnovější verzi **Průzkumníka Azure IoT** ze stránky [úložiště](https://github.com/Azure/azure-iot-explorer/releases) nástroje výběrem souboru MSI v části "Prostředky" pro nejnovější aktualizaci.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Spusťte následující příkaz, abyste získali _připojovací řetězec centra IoT hub_ pro vaše centrum (poznámka pro pozdější použití):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

V tomto rychlém startu připravíte vývojové prostředí, které můžete použít ke klonování a sestavení sady Azure IoT Hub Device SDK pro node.js.

Otevřete příkazový řádek v adresáři, který si vyberete. Spusťte následující příkaz pro klonování úložiště [Microsoft Azure IoT SDK pro úložiště Node.js](https://github.com/Azure/azure-iot-sdk-node) GitHub do tohoto umístění:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node --recursive -b digitaltwins-preview
```

Tato operace může trvat několik minut.

## <a name="install-required-libraries"></a>Instalace požadovaných knihoven

Pomocí sady SDK zařízení můžete vytvořit přiložený ukázkový kód. Aplikace, kterou vytvoříte, simuluje zařízení, které se připojuje k centru IoT. Aplikace odesílá telemetrii a vlastnosti a přijímá příkazy.

1. V okně místního terminálu přejděte do složky klonovaného úložiště a přejděte do složky **/azure-iot-sdk-node/digitaltwins/samples/device/javascript.** Potom spusťte následující příkaz k instalaci požadovaných knihoven:

    ```cmd/sh
    npm install
    ```
1. Konfigurace _připojovacího řetězce zařízení_:

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-sample-device"></a>Spuštění ukázkového zařízení

Spusťte ukázkovou aplikaci v sadě SDK a simulujte zařízení IoT Plug and Play, které odesílá telemetrii do vašeho centra IoT Hub. Chcete-li spustit ukázkovou aplikaci, použijte následující příkaz:

```cmd\sh
    node sample_device.js
```

Zobrazí se následující výstup označující, že zařízení začalo odesílat telemetrická data do centra a je nyní připraveno přijímat příkazy a aktualizace vlastností.

   ![Zprávy s potvrzením zařízení](media/quickstart-connect-pnp-device/device-confirmation-node.png)

 Po nechat vzorek běží při dokončení dalších kroků.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Ověření kódu pomocí průzkumníka Azure IoT

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Chcete-li zajistit, aby nástroj mohl číst definice modelu rozhraní ze zařízení, vyberte **nastavení**. V nabídce Nastavení se **na připojeném zařízení** již může zobrazit v konfiguracích Plug and Play. Pokud tomu tak není, vyberte **+ Přidat zdroj definice modulu** a pak na **připojeném zařízení** jej přidat.

1. Zpět na stránce **Přehled zařízení** najděte identitu zařízení, kterou jste vytvořili dříve. Pokud je aplikace zařízení stále spuštěná v příkazovém řádku, zkontrolujte, jestli **stav připojení** zařízení v průzkumníku Azure IoT hlásí jako _Připojený_ (pokud ne, stiskněte **tlačítko Aktualizovat,** dokud nebude). Výběrem zařízení zobrazíte další podrobnosti.

1. Rozbalte rozhraní s ID **urn:contoso:com:EnvironmentalSensor:1** a odhalte rozhraní a ioT plug and play primitiv – vlastnosti, příkazy a telemetrii.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak připojit zařízení IoT Plug and Play k centru IoT. Další informace o tom, jak vytvořit řešení, které spolupracuje s vašimi zařízeními IoT Plug and Play, najdete v tématu:

> [!div class="nextstepaction"]
> [Postup: Připojení k zařízení IoT Plug and Play Preview a jeho práce s ním](howto-develop-solution.md)
