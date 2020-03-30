---
title: Připojení ukázkového kódu zařízení IoT Plug and Play Preview ke službě IoT Hub (Linux) | Dokumenty společnosti Microsoft
description: Na Linuxu můžete vytvářet a spouštět ukázkový kód zařízení IoT Plug and Play Preview, který se připojuje k centru IoT. Pomocí rozhraní příkazového příkazu Azure zobrazte informace odeslané zařízením do centra.
author: dominicbetts
ms.author: dobett
ms.date: 12/23/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 8134c0a97f6350cfa2cf616695c5990618455393
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75531245"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-to-iot-hub-c-linux"></a>Úvodní příručka: Připojení ukázkové aplikace zařízení IoT Plug and Play Preview spuštěné na Linuxu k IoT Hubu (C Linux)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Tento rychlý start ukazuje, jak vytvořit ukázkovou aplikaci zařízení IoT Plug and Play v Linuxu, připojit ji k vašemu bub IoT a použít rozhraní příkazového příkazu K Azure k zobrazení informací, které odesílá do centra. Ukázková aplikace je napsaná v C a je součástí sady Azure IoT device SDK for C. Vývojář řešení můžete použít Azure CLI pochopit možnosti zařízení IoT Plug and Play bez nutnosti zobrazit kód zařízení.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Požadavky

Tento rychlý start předpokládá, že používáte Ubuntu Linux. Kroky v tomto tutoriálu byly testovány pomocí Ubuntu 18.04.

Chcete-li dokončit tento rychlý start, je třeba nainstalovat následující software na místním počítači s Linuxem:

Nainstalujte **gcc**, **git**, **cmake** `apt-get` a všechny závislosti pomocí příkazu:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Ověřte, `cmake` zda je verze písmene a) vyšší než **2.8.12** a verze **GCC** je vyšší než **4.4.7**.

```sh
cmake --version
gcc --version
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

V tomto rychlém startu připravíte vývojové prostředí, které můžete použít ke klonování a sestavení sady Azure IoT Hub Device C SDK.

Otevřete příkazový řádek v adresáři, který si vyberete. Spusťte následující příkaz pro klonování úložiště GitHub [Azure IoT C a knihovny](https://github.com/Azure/azure-iot-sdk-c) GitHub do tohoto umístění:

```bash
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.

## <a name="build-the-code"></a>Sestavení kódu

Pomocí sady SDK zařízení můžete vytvořit přiložený ukázkový kód. Aplikace, kterou vytvoříte, simuluje zařízení, které se připojuje k centru IoT. Aplikace odesílá telemetrii a vlastnosti a přijímá příkazy.

1. Vytvořte `cmake` podadresář v kořenové složce sady SDK zařízení a přejděte do této složky:

    ```bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Spusťte následující příkazy k vytvoření sady SDK zařízení a generovaného kódu se zakázaným inzerováním:

    ```bash
    cmake ..
    cmake --build .
    ```

## <a name="update-your-model-repository"></a>Aktualizace úložiště modelu

Před spuštěním ukázky přidejte model schopností zařízení a definice rozhraní do úložiště firemního modelu:

1. Pokud ho máte, přihlaste se na [portál Azure Certified for IoT portal](https://preview.catalog.azureiotsolutions.com) pomocí pracovního nebo školního účtu Microsoftu nebo ID partnera Microsoftu.

1. Vyberte **úložiště společnosti** a potom **modely schopností**.

1. Vyberte **Nový** a potom **nahrát**.

1. Vyberte `SampleDevice.capabilitymodel.json` soubor `digitaltwin_client/samples` ve složce v kořenové složce sady SDK zařízení. Vyberte **Otevřít** a pak **Uložit,** chcete-li soubor modelu nahrát do úložiště.

1. Vyberte **úložiště společnosti** a potom **rozhraní**.

1. Vyberte **Nový** a potom **nahrát**.

1. Vyberte `EnvironmentalSensor.interface.json` soubor `digitaltwin_client/samples/digitaltwin_sample_environmental_sensor` ve složce v kořenové složce sady SDK zařízení. Vyberte **Otevřít** a pak **Uložit,** chcete-li nahrát soubor rozhraní do úložiště.

1. Vyberte **úložiště společnosti** a potom **připojovací řetězce**. Poznamenejte si první _připojovací řetězec úložiště modelu společnosti_, jak jej použít později v tomto rychlém startu.

## <a name="run-the-device-sample"></a>Spuštění ukázky zařízení

Spusťte ukázkovou aplikaci v sadě SDK a simulujte zařízení IoT Plug and Play, které odesílá telemetrii do vašeho centra IoT Hub. Spuštění ukázkové aplikace:

1. Ze `cmake` složky přejděte do složky obsahující spustitelný soubor:

    ```bash
    cd digitaltwin_client/samples/digitaltwin_sample_device
    ```

1. Spusťte spustitelný soubor:

    ```bash
    ./digitaltwin_sample_device "<YourDeviceConnectionString>"
    ```

Zařízení je nyní připraveno přijímat příkazy a aktualizace vlastností a začalo odesílat telemetrická data do centra. Po nechat vzorek běží při dokončení dalších kroků.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>Ověření kódu pomocí příkazového příkazu Konitolu Azure IoT

Po spuštění ukázky klienta zařízení ověřte, že pracuje s rozhraním příkazového příkazového příkazu Kaviádu Azure.

Pomocí následujícího příkazu zobrazíte telemetrii, kterou ukázkové zařízení odesílá. Možná budete muset počkat minutu nebo dvě, než se zobrazí všechny telemetrie ve výstupu:

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Pomocí následujícího příkazu můžete zobrazit vlastnosti odeslané zařízením:

```azurecli-interactive
az iot dt list-properties --hub-name <YourIoTHubName> --device-id <YourDeviceID> --interface sensor --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```
[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak připojit zařízení IoT Plug and Play k centru IoT. Další informace o tom, jak vytvořit řešení, které spolupracuje s vašimi zařízeními IoT Plug and Play, najdete v tématu:

> [!div class="nextstepaction"]
> [Postup: Připojení k zařízení a interakce se zařízením](howto-develop-solution.md)
