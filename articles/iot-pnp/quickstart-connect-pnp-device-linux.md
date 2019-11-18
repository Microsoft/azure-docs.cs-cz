---
title: Připojte si ukázkový kód zařízení ve službě IoT technologie Plug and Play Preview k IoT Hub (Linux) | Microsoft Docs
description: Sestavte a spouštějte ukázkový kód zařízení ve verzi IoT technologie Plug and Play v systému Linux, který se připojuje ke centru IoT. K zobrazení informací odesílaných zařízením do centra použijte rozhraní příkazového řádku Azure CLI.
author: dominicbetts
ms.author: dobett
ms.date: 09/10/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 802578c79fa086c74a56db8d47f83ae96d6b0194
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152134"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-to-iot-hub-c-linux"></a>Rychlý Start: připojení ukázkové aplikace IoT technologie Plug and Play ve verzi Preview, která běží na Linux, do IoT Hub (C Linux)

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

## <a name="prepare-an-iot-hub"></a>Příprava služby IoT Hub

K dokončení tohoto rychlého startu budete také potřebovat službu Azure IoT Hub v rámci vašeho předplatného Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. Pokud Centrum IoT nemáte, [vytvořte ho podle těchto pokynů](../iot-hub/iot-hub-create-using-cli.md).

> [!IMPORTANT]
> Během veřejné verze Preview jsou funkce IoT technologie Plug and Play dostupné jenom v centrech IoT vytvořených v oblastech **střed USA**, **Severní Evropa**a **Japonsko – východ** .

Pokud používáte Azure CLI místně, `az` verze by měla být **2.0.73** nebo novější; Azure Cloud Shell používá nejnovější verzi. Pomocí příkazu `az --version` ověřte verzi nainstalovanou na vašem počítači.

Spuštěním následujícího příkazu přidejte do instance Cloud Shell Microsoft Azure rozšíření IoT pro rozhraní příkazového řádku Azure:
```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Postup v tomto rychlém startu vyžaduje rozšíření **0.8.5** nebo novější. Pomocí příkazu `az extension list` ověřte verzi, kterou jste nainstalovali, a příkaz `az extension update`, který se v případě potřeby aktualizuje.

Pokud používáte rozhraní příkazového řádku místně, přihlaste se k předplatnému Azure pomocí následujícího příkazu:

```bash
az login
```

Pokud používáte Azure Cloud Shell, už jste přihlášeni automaticky.

Spuštěním následujícího příkazu vytvořte identitu zařízení ve službě IoT Hub. Zástupné symboly **YourIoTHubName** a **YourDeviceID** nahraďte vlastním _názvem IoT Hub_ a _ID zařízení_ podle vašeho výběru.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Spuštěním následujícího příkazu Získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali (Poznámka pro pozdější použití):

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDevice> --output table
```

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
