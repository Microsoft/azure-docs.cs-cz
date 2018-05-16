---
title: Rychlý start k odesílání telemetrických dat do služby Azure IoT Hub (Python) | Microsoft Docs
description: V tomto rychlém startu spustíte ukázkovou aplikaci Python, která odesílá simulovaná telemetrická data do centra IoT a používá nástroj pro čtení telemetrických dat z centra IoT.
services: iot-hub
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: ns
ms.date: 04/30/2018
ms.author: dobett
ms.openlocfilehash: 35e6f027551c343a56f606313b6592e74bf6caf7
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-the-telemetry-from-the-hub-with-a-back-end-application-python"></a>Rychlý start: Odesílání telemetrických dat ze zařízení do centra IoT a čtení telemetrických dat z centra pomocí back-endové aplikace (Python)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub je služba Azure, která umožňuje ingestovat velké objemy telemetrických dat ze zařízení IoT do cloudu pro účely uložení nebo zpracování. V tomto rychlém startu odešlete telemetrická data z aplikace simulovaného zařízení prostřednictvím služby IoT Hub do back-endové aplikace za účelem zpracování.

V tomto rychlém startu se k odesílání telemetrických dat používá předem napsaná aplikace Python a ke čtení telemetrických dat z centra se používá nástroj rozhraní příkazového řádku. Před spuštěním těchto dvou aplikací vytvoříte centrum IoT a zaregistrujete v tomto centru zařízení.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Dvě ukázkové aplikace, které spustíte v tomto rychlém startu, jsou napsány pomocí Pythonu. Na počítači určeném pro vývoj musíte mít Python 2.7.x nebo 3.5.x.

Python pro různé platformy si můžete stáhnout z webu [Python.org](https://www.python.org/downloads/).

Aktuální verzi Pythonu na počítači používaném pro vývoj můžete ověřit pomocí jednoho z následujících příkazů:

```python
python --version
```

```python
python3 --version
```

Stáhněte si ukázkový projekt Python z https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip a extrahujte archiv ZIP.

Pokud chcete nainstalovat nástroj CLI, který čte telemetrická data z centra IoT, nainstalujte nejprve na počítač používaný pro vývoj Node.js v4.x.x nebo novější. Node.js pro různé platformy si můžete stáhnout z webu [nodejs.org](https://nodejs.org).

Aktuální verzi Node.js na počítači používaném pro vývoj můžete ověřit pomocí následujícího příkazu:

```cmd/sh
node --version
```

Pokud chcete nainstalovat nástroj CLI `iothub-explorer`, spusťte následující příkaz:

```cmd/sh
npm install -g iothub-explorer
```

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>Registrování zařízení

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu pomocí Azure CLI zaregistrujete simulované zařízení.

1. Přidejte rozšíření rozhraní příkazového řádku IoT Hub a vytvořte identitu zařízení. `{YourIoTHubName}` nahraďte názvem, který jste vybrali pro centrum IoT:

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name {YourIoTHubName}--device-id MyPythonDevice
    ```

1. Spuštěním následujícího příkazu získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá nějak takto: `Hostname=...=`. Tuto hodnotu použijete později v tomto rychlém startu.

1. Potřebujete také _připojovací řetězec služby_, abyste nástroji CLI `iothub-explorer` umožnili připojit se k centru IoT a načíst zprávy. Následující příkaz načte připojovací řetězec služby pro vaše centrum IoT:

    ```azurecli-interactive
    az iot hub show-connection-string --hub-name {YourIoTHubName} --output table
    ```

    Poznamenejte si připojovací řetězec služby, který vypadá nějak takto: `Hostname=...=`. Tuto hodnotu použijete později v tomto rychlém startu. Připojovací řetězec služby se liší od připojovacího řetězce zařízení.

## <a name="send-simulated-telemetry"></a>Odesílání simulovaných telemetrických dat

Aplikace simulovaného zařízení se připojuje ke koncovému bodu vašeho centra IoT pro konkrétní zařízení a odesílá simulovaná telemetrická data o teplotě a vlhkosti vzduchu.

1. V okně terminálu přejděte do kořenové složky ukázkového projektu Python. Pak přejděte do složky **Quickstarts\simulated-device**.

1. V libovolném textovém editoru otevřete soubor **SimulatedDevice.py**.

    Hodnotu proměnné `CONNECTION_STRING` nahraďte připojovacím řetězcem zařízení, který jste si předtím poznamenali. Změny pak uložte do souboru **SimulatedDevice.py**.

1. V okně terminálu spusťte následující příkazy pro instalaci požadovaných knihoven pro aplikaci simulovaného zařízení:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. V okně terminálu spusťte následující příkazy pro spuštění aplikace simulovaného zařízení:

    ```cmd/sh
    python SimulatedDevice.py
    ```

    Následující snímek obrazovky ukazuje výstup, zatímco aplikace simulovaného zařízení odesílá telemetrická data do vašeho centra IoT:

    ![Spuštění simulovaného zařízení](media/quickstart-send-telemetry-python/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>Čtení telemetrických dat z centra

Nástroj příkazového řádku `iothub-explorer` se připojí ke koncovému bodu **Events** (Události) na straně služby ve vaší službě IoT Hub. Nástroj přijímá zprávy typu zařízení-cloud odeslané ze simulovaného zařízení. Back-endová aplikace služby IoT Hub se obvykle spouští v cloudu, aby mohla přijímat a zpracovávat zprávy typu zařízení-cloud.

V jiném okně terminálu spusťte následující příkazy, ve kterých `{your hub service connection string}` nahradíte připojovacím řetězcem služby, který jste si předtím poznamenali:

```cmd/sh
iothub-explorer monitor-events MyPythonDevice --login {your hub service connection string}
```

Následující snímek obrazovky ukazuje výstup, zatímco nástroj přijímá telemetrická data odeslaná simulovaným zařízením do centra:

![Spuštění back-endové aplikace](media/quickstart-send-telemetry-python/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud si chcete projít další rychlý start, zachovejte skupinu prostředků a centrum IoT pro pozdější použití.

Pokud už centrum IoT nepotřebujete, odstraňte ho společně se skupinou prostředků na portálu. Provedete to výběrem skupiny prostředků **qs-iot-hub-rg**, která obsahuje vaše centrum IoT, a kliknutím na **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nastavili centrum IoT, zaregistrovali zařízení, odeslali simulovaná telemetrická data do centra pomocí aplikace Python a přečetli telemetrická data z centra pomocí jednoduché back-endové aplikace.

Informace o tom, jak řídit simulované zařízení z back-endové aplikace, najdete v dalším rychlém startu.

> [!div class="nextstepaction"]
> [Rychlý start: Řízení zařízení připojeného k centru IoT](quickstart-control-device-python.md)