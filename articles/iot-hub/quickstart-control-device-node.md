---
title: 'Úvodní příručka: Řízení zařízení z Azure IoT (Node.js)'
description: V tomto rychlém startu spustíte dvě ukázkové aplikace Node.js. První aplikace je back-endová aplikace, která může vzdáleně řídit zařízení připojená k vašemu centru. Druhá aplikace simuluje zařízení připojené k vašemu centru, které je možné řídit vzdáleně.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.date: 06/21/2019
ms.openlocfilehash: 29c6964ff2e0ce1b8ffb0964640bc460e6143902
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78674300"
---
# <a name="quickstart-use-nodejs-to-control-a-device-connected-to-an-azure-iot-hub"></a>Úvodní příručka: Pomocí souboru Node.js můžete řídit zařízení připojené k centru Azure IoT hub.

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

V tomto rychlém startu použijete přímou metodu k řízení simulované zařízení připojené k Azure IoT Hub. IoT Hub je služba Azure, která vám umožní spravovat vaše zařízení IoT z cloudu a ingestovat velké objemy telemetrie zařízení do cloudu pro ukládání nebo zpracování. Přímé metody můžete použít k provádění vzdálených změn chování zařízení připojeného k centru IoT. Tento rychlý start používá dvě aplikace Node.js: simulované aplikace zařízení, které reaguje na přímé metody volané z back-endové aplikace a back-endaplikace, která volá přímé metody na simulovaném zařízení.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Node.js 10+](https://nodejs.org).

* [Ukázkový projekt Node.js](https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip).

* Port 8883 otevřít ve vašem firewallu. Ukázka zařízení v tomto rychlém startu používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokován v některých prostředích podnikové a vzdělávací sítě. Další informace a způsoby, jak tento problém vyřešit, najdete [v tématu připojení k centru IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

Aktuální verzi Node.js na počítači používaném pro vývoj můžete ověřit pomocí následujícího příkazu:

```cmd/sh
node --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Přidání rozšíření Azure IoT

Spusťte následující příkaz a přidejte rozšíření Microsoft Azure IoT extension pro Azure CLI do instance Cloud Shellu. Rozšíření IoT přidá do rozhraní příkazového příkazu Azure CLI specifické pro služby IoT Hub, IoT Edge a Služby zřizování zařízení IoT (DPS).

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Pokud jste dokončili předchozí [Rychlý start: Odesílání telemetrických dat ze zařízení do centra IoT](quickstart-send-telemetry-node.md), můžete tento krok přeskočit.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrování zařízení

Pokud jste dokončili předchozí [Rychlý start: Odesílání telemetrických dat ze zařízení do centra IoT](quickstart-send-telemetry-node.md), můžete tento krok přeskočit.

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell.

1. Spusťte následující příkaz v Azure Cloud Shell a vytvořte identitu zařízení.

   **YourIoTHubName**: Nahraďte tento zástupný symbol níže názvem, který jste vybrali pro svůj centr IoT.

   **MyNodeDevice**: Toto je název zařízení, které registrujete. Doporučuje se používat **MyNodeDevice,** jak je znázorněno. Pokud pro své zařízení zvolíte jiný název, musíte tento název použít v celém tomto článku a aktualizovat název zařízení v ukázkových aplikacích před jejich spuštěním.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyNodeDevice
    ```

2. Spuštěním následujícího příkazu ve službě Azure Cloud Shell získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:

    **YourIoTHubName**: Nahraďte tento zástupný symbol níže názvem, který jste vybrali pro svůj centr IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name {YourIoTHubName} \
      --device-id MyNodeDevice \
      --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá nějak takto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v tomto rychlém startu.

3. Potřebujete také _připojovací řetězec služby_, který back-endové aplikaci umožní připojení k vašemu centru IoT a načtení zpráv. Následující příkaz načte připojovací řetězec služby pro vaše centrum IoT:

    **YourIoTHubName**: Nahraďte tento zástupný symbol níže názvem, který jste vybrali pro svůj centr IoT.

    ```azurecli-interactive
    az iot hub show-connection-string \
      --policy-name service --name {YourIoTHubName} --output table

    ```

    Poznamenejte si připojovací řetězec služby, který vypadá nějak takto:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v tomto rychlém startu. Tento připojovací řetězec služby se liší od připojovacího řetězce zařízení, který jste zaznamenali v předchozím kroku.

## <a name="listen-for-direct-method-calls"></a>Naslouchání voláním přímé metody

Aplikace simulovaného zařízení se připojí ke koncovému bodu v centru IoT pro konkrétní zařízení, odešle simulovaná telemetrická data a z vašeho centra naslouchá voláním přímé metody. Volání přímé metody z centra v tomto rychlém startu nařídí zařízení, aby změnilo interval, ve kterém se odesílají telemetrická data. Simulované zařízení odešle potvrzení zpět do rozbočovače po spuštění přímé metody.

1. V okně místního terminálu a přejděte do kořenové složky ukázkového projektu Node.js. Potom přejděte do složky **iot-hub\Quickstarts\simulated-device-2**.

2. V libovolném textovém editoru otevřete soubor **SimulatedDevice.js**.

    Nahraďte hodnotu `connectionString` proměnné připojovacím řetězcem zařízení, který jste si dříve poznamenali. Potom uložte změny **na SimulatedDevice.js**.

3. V okně místního terminálu pomocí následujících příkazů nainstalujte požadované knihovny a spusťte aplikaci simulovaného zařízení:

    ```cmd/sh
    npm install
    node SimulatedDevice.js
    ```

    Následující snímek obrazovky ukazuje výstup, zatímco aplikace simulovaného zařízení odesílá telemetrická data do vašeho centra IoT:

    ![Spuštění simulovaného zařízení](./media/quickstart-control-device-node/simulated-device-telemetry-iot-hub.png)

## <a name="call-the-direct-method"></a>Volání přímé metody

Back-endová aplikace se připojí ke koncovému bodu vašeho centra IoT na straně služby. Aplikace provádí volání přímé metody zařízení prostřednictvím služby IoT hub a naslouchá potvrzení. Back-endová aplikace služby IoT Hub se obvykle spouští v cloudu.

1. V jiném okně místního terminálu přejděte do kořenové složky ukázkového projektu Node.js. Potom přejděte do složky **iot-hub\Quickstarts\back-end-application**.

2. V libovolném textovém editoru otevřete soubor **BackEndApplication.js**.

    Nahraďte hodnotu `connectionString` proměnné připojovacím řetězcem služby, který jste si dříve poznamenali. Potom uložte změny do **souboru BackEndApplication.js**.

3. V okně místního terminálu pomocí následujících příkazů nainstalujte požadované knihovny a spusťte back-endovou aplikaci:

    ```cmd/sh
    npm install
    node BackEndApplication.js
    ```

    Následující snímek obrazovky ukazuje výstup, protože aplikace provádí přímé volání metody zařízení a přijímá potvrzení:

    ![Výstup, když aplikace provede přímé volání metody zařízení](./media/quickstart-control-device-node/direct-method-device-call.png)

    Po spuštění back-endové aplikace se v okně konzoly se simulovaným zařízením zobrazí zpráva a rychlost odesílání zpráv se změní:

    ![Výstup při změně simulovaného klienta](./media/quickstart-control-device-node/simulated-device-message-change.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste volali přímou metodu na zařízení z back-endové aplikace a odpověděli na volání přímé metody v aplikaci simulované zařízení.

Informace o tom, jak směrovat zprávy typu zařízení-cloud do různých cílů v cloudu, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Kurz: Směrování telemetrických dat do různých koncových bodů za účelem zpracování](tutorial-routing.md)
