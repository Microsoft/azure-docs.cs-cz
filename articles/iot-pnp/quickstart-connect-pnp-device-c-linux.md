---
title: Připojte si ukázkový kód zařízení ve službě IoT technologie Plug and Play Preview k IoT Hub (Linux) | Microsoft Docs
description: Sestavte a spouštějte ukázkový kód zařízení ve verzi IoT technologie Plug and Play v systému Linux, který se připojuje ke centru IoT. K zobrazení informací odesílaných zařízením do centra použijte rozhraní příkazového řádku Azure CLI.
author: dominicbetts
ms.author: dobett
ms.date: 12/23/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 8134c0a97f6350cfa2cf616695c5990618455393
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531245"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-to-iot-hub-c-linux"></a>Rychlý Start: připojení ukázkové aplikace IoT technologie Plug and Play ve verzi Preview, která běží na Linux, do IoT Hub (C Linux)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

V tomto rychlém startu se dozvíte, jak vytvořit ukázkovou aplikaci IoT technologie Plug and Play zařízení v systému Linux, připojit ji ke službě IoT BUB a pomocí Azure CLI zobrazit informace, které odesílá do centra. Ukázková aplikace je napsaná v jazyce C a je obsažená v sadě SDK pro zařízení Azure IoT pro jazyk C. Vývojář řešení může pomocí Azure CLI porozumět možnostem zařízení technologie Plug and Play IoT, aniž by musel zobrazovat žádný kód zařízení.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Požadavky

V tomto rychlém startu se předpokládá, že používáte Ubuntu Linux. Kroky v tomto kurzu byly testovány pomocí Ubuntu 18,04.

K dokončení tohoto rychlého startu je potřeba na svém místním počítači se systémem Linux nainstalovat následující software:

Pomocí příkazu `apt-get` nainstalujte **RSZ**, **Git**, **cmake**a všechny závislosti:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Ověřte, že verze `cmake` je nad **2.8.12** a verze **RSZ** je vyšší než **4.4.7**.

```sh
cmake --version
gcc --version
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

V tomto rychlém startu připravíte vývojové prostředí, které můžete použít k klonování a sestavování sady Azure IoT Hub zařízení C SDK.

Otevřete příkazový řádek v adresáři dle vašeho výběru. Spusťte následující příkaz, který naklonuje úložiště GitHub sady [Azure IoT C a knihovny](https://github.com/Azure/azure-iot-sdk-c) do tohoto umístění:

```bash
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.

## <a name="build-the-code"></a>Sestavení kódu

Pomocí sady SDK pro zařízení sestavíte zahrnutý vzorový kód. Vytvořená aplikace simuluje zařízení, které se připojuje ke službě IoT Hub. Aplikace odesílá telemetrie a vlastnosti a přijímá příkazy.

1. V kořenové složce sady SDK pro zařízení vytvořte podadresář `cmake` a přejděte do této složky:

    ```bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Spusťte následující příkazy a Sestavte sadu SDK pro zařízení a vygenerovanou zástupné kódy:

    ```bash
    cmake ..
    cmake --build .
    ```

## <a name="update-your-model-repository"></a>Aktualizace úložiště modelu

Před spuštěním ukázky přidejte do úložiště podnikového modelu model funkce zařízení a definice rozhraní:

1. Přihlaste se na portál [Azure Certified for IoT Portal](https://preview.catalog.azureiotsolutions.com) pomocí pracovního nebo školního účtu Microsoft nebo svého ID partnera Microsoftu, pokud ho máte.

1. Vyberte **úložiště společnosti** a pak **modely schopností**.

1. Vyberte **Nový** a potom **Odeslat**.

1. Vyberte soubor `SampleDevice.capabilitymodel.json` ve složce `digitaltwin_client/samples` v kořenové složce sady SDK pro zařízení. Vyberte **otevřít** a pak **Uložit** a nahrajte soubor modelu do úložiště.

1. Vyberte **úložiště společnosti** a potom **rozhraní**.

1. Vyberte **Nový** a potom **Odeslat**.

1. Vyberte soubor `EnvironmentalSensor.interface.json` ve složce `digitaltwin_client/samples/digitaltwin_sample_environmental_sensor` v kořenové složce sady SDK pro zařízení. Vyberte **otevřít** a pak **Uložit** a nahrajte soubor rozhraní do úložiště.

1. Vyberte **úložiště společnosti** a pak **připojovací řetězce**. Poznamenejte si první _připojovací řetězec úložiště modelu společnosti_, jak ho použijete později v tomto rychlém startu.

## <a name="run-the-device-sample"></a>Spuštění ukázky zařízení

Spusťte v sadě SDK ukázkovou aplikaci, která simuluje zařízení IoT technologie Plug and Play, které odesílá telemetrii do služby IoT Hub. Spuštění ukázkové aplikace:

1. Ve složce `cmake` přejděte do složky, která obsahuje spustitelný soubor:

    ```bash
    cd digitaltwin_client/samples/digitaltwin_sample_device
    ```

1. Spusťte spustitelný soubor:

    ```bash
    ./digitaltwin_sample_device "<YourDeviceConnectionString>"
    ```

Zařízení je teď připravené přijmout příkazy a aktualizace vlastností a zahájilo posílání dat telemetrie do centra. Ponechte ukázku spuštěnou při dokončení dalších kroků.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>Použití rozhraní příkazového řádku Azure IoT k ověření kódu

Po spuštění ukázky klienta zařízení ověřte, že funguje s rozhraním příkazového řádku Azure CLI.

Pomocí následujícího příkazu můžete zobrazit telemetrii, kterou ukázkové zařízení odesílá. Před zobrazením jakékoli telemetrie ve výstupu možná budete muset počkat jednu nebo dvě minuty:

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

K zobrazení vlastností odesílaných zařízením použijte následující příkaz:

```azurecli-interactive
az iot dt list-properties --hub-name <YourIoTHubName> --device-id <YourDeviceID> --interface sensor --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```
[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili připojit zařízení IoT technologie Plug and Play ke službě IoT Hub. Další informace o tom, jak vytvořit řešení, které komunikuje s technologie Plug and Play vašich zařízení IoT, najdete tady:

> [!div class="nextstepaction"]
> [Postupy: připojení k zařízení a práce s nimi](howto-develop-solution.md)
