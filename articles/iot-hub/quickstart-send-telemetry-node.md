---
title: 'Rychlý Start: odeslání telemetrie do Azure IoT (Node. js)'
description: V tomto rychlém startu spustíte dvě ukázkové aplikace Node. js k odeslání simulované telemetrie do služby IoT Hub a ke čtení telemetrie ze služby IoT Hub ke zpracování v cloudu.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019
ms.date: 06/21/2019
ms.openlocfilehash: 92d6af41e55429f1b788de68940bc9b033c51ad6
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72167037"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-nodejs"></a>Rychlý Start: odeslání telemetrie ze zařízení do služby IoT Hub a její čtení pomocí back-endové aplikace (Node. js)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub je služba Azure, která umožňuje ingestovat velké objemy telemetrie ze zařízení IoT do cloudu za účelem uložení nebo zpracování. V tomto rychlém startu odesíláte telemetrii z aplikace simulovaného zařízení prostřednictvím IoT Hub do back-endové aplikace pro zpracování.

Rychlý Start používá dvě předem napsané aplikace Node. js, jednu k odeslání telemetrie a jednu pro čtení telemetrie z centra. Než tyto dvě aplikace spustíte, vytvoříte centrum IoT a zaregistrujete zařízení do centra.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Dvě ukázkové aplikace, které spouštíte v rámci tohoto rychlého startu, jsou napsané v Node. js. Ve vývojovém počítači potřebujete Node. js v10 za účelem. x. x nebo novější.

Node. js si můžete stáhnout z [NodeJS.org](https://nodejs.org)na více platforem.

Aktuální verzi Node. js můžete na svém vývojovém počítači ověřit pomocí následujícího příkazu:

```cmd/sh
node --version
```

Spuštěním následujícího příkazu přidejte do instance služby Cloud Shell Microsoft Azure rozšíření IoT pro rozhraní příkazového řádku Azure. Rozšíření IOT přidá do Azure CLI příkazy určené pro služby IoT Hub, IoT Edge a IoT Device Provisioning Service (DPS).

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Stáhněte si ukázkový projekt Node. js z https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip a Extrahujte archiv ZIP.

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrace zařízení

Aby bylo možné se připojit, musí být zařízení zaregistrované ve službě IoT Hub. V tomto rychlém startu použijete Azure Cloud Shell k registraci simulovaného zařízení.

1. Spuštěním následujícího příkazu v Azure Cloud Shell vytvořte identitu zařízení.

   **YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který jste zvolili pro Centrum IoT.

   **MyNodeDevice**: Toto je název zařízení, které registrujete. Doporučuje se používat **MyNodeDevice** , jak je znázorněno na obrázku. Pokud pro vaše zařízení zvolíte jiný název, budete ho muset použít i v celém rámci tohoto článku a před jeho spuštěním aktualizovat název zařízení v ukázkových aplikacích.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyNodeDevice
    ```

1. Spuštěním následujícího příkazu v Azure Cloud Shell Získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:

   **YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který jste zvolili pro Centrum IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyNodeDevice --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá takto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v rychlém startu.

1. Také potřebujete _připojovací řetězec služby_ , který umožní back-endové aplikaci připojit se ke službě IoT Hub a načíst zprávy. Následující příkaz načte připojovací řetězec služby pro službu IoT Hub:

   **YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který jste zvolili pro Centrum IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --name {YourIoTHubName} --policy-name service --output table
    ```

    Poznamenejte si připojovací řetězec služby, který vypadá takto:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v rychlém startu. Tento připojovací řetězec služby se liší od připojovacího řetězce zařízení, který jste si poznamenali v předchozím kroku.

## <a name="send-simulated-telemetry"></a>Odeslat simulovanou telemetrii

Aplikace simulovaného zařízení se připojí ke koncovému bodu specifickému pro zařízení ve službě IoT Hub a odešle simulaci simulované teploty a vlhkosti.

1. Otevřete okno místního terminálu, přejděte do kořenové složky ukázkového projektu Node. js. Pak přejděte do složky **IoT-hub\Quickstarts\simulated-Device** .

1. Otevřete soubor **SimulatedDevice. js** v textovém editoru dle vašeho výběru.

    Nahraďte hodnotu proměnné `connectionString` připojovacím řetězcem zařízení, který jste si poznamenali dříve. Potom uložte změny do souboru **SimulatedDevice. js**.

1. V okně místního terminálu spuštěním následujících příkazů nainstalujte požadované knihovny a spusťte aplikaci simulovaného zařízení:

    ```cmd/sh
    npm install
    node SimulatedDevice.js
    ```

    Následující snímek obrazovky ukazuje výstup jako aplikace simulovaného zařízení odesílá telemetrii do služby IoT Hub:

    ![Spuštění simulovaného zařízení](media/quickstart-send-telemetry-node/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>Přečtěte si telemetrii z vašeho centra.

Back-endové aplikace se připojí ke koncovému bodu **událostí** na straně služby na vašem IoT Hub. Aplikace přijímá zprávy ze zařízení do cloudu odesílané simulovaným zařízením. IoT Hub back-endové aplikace obvykle běží v cloudu pro příjem a zpracování zpráv ze zařízení do cloudu.

1. Otevřete jiné okno místního terminálu, přejděte do kořenové složky ukázkového projektu Node. js. Pak přejděte do složky **IoT-hub\Quickstarts\read-D2C-Messages** .

1. Otevřete soubor **ReadDeviceToCloudMessages. js** v textovém editoru dle vašeho výběru.

    Nahraďte hodnotu proměnné `connectionString` připojovacím řetězcem služby, který jste si poznamenali dříve. Potom uložte změny do souboru **ReadDeviceToCloudMessages. js**.

1. V okně místního terminálu spuštěním následujících příkazů nainstalujte požadované knihovny a spusťte back-end aplikaci:

    ```cmd/sh
    npm install
    node ReadDeviceToCloudMessages.js
    ```

    Následující snímek obrazovky ukazuje výstup, ve kterém back-end aplikace přijímá telemetrii poslaná simulovaným zařízením do centra:

    ![Spuštění back-endové aplikace](media/quickstart-send-telemetry-node/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu nastavíte centrum IoT, zaregistrovali zařízení, odeslali simulovanou telemetrii do centra pomocí aplikace Node. js a přečetli telemetrii z centra pomocí jednoduché back-endové aplikace.

Pokud se chcete dozvědět, jak ovládat simulované zařízení z back-endové aplikace, pokračujte dalším rychlým startem.

> [!div class="nextstepaction"]
> [Rychlý Start: řízení zařízení připojeného ke centru IoT](quickstart-control-device-node.md)
