---
title: 'Rychlý Start: řízení zařízení z Azure IoT (Node.js)'
description: V tomto rychlém startu spustíte dvě ukázkové aplikace Node.js. První aplikace je back-endová aplikace, která může vzdáleně řídit zařízení připojená k vašemu centru. Druhá aplikace simuluje zařízení připojené k vašemu centru, které je možné řídit vzdáleně.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom:
- mvc
- seo-javascript-september2019
- seo-javascript-october2019
- mqtt
- 'Role: Cloud Development'
- devx-track-js
- devx-track-azurecli
ms.date: 06/21/2019
ms.openlocfilehash: 52b4ec2ed7b05dfb905d490cf1bf11aed25ea562
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92747503"
---
# <a name="quickstart-use-nodejs-to-control-a-device-connected-to-an-azure-iot-hub"></a>Rychlý Start: použití Node.js k řízení zařízení připojeného ke službě Azure IoT Hub

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

V tomto rychlém startu použijete přímou metodu k řízení simulovaného zařízení připojeného k Azure IoT Hub. IoT Hub je služba Azure, která umožňuje spravovat zařízení IoT z cloudu a ingestovat velké objemy telemetrie zařízení do cloudu za účelem uložení nebo zpracování. Přímé metody můžete použít k provádění vzdálených změn chování zařízení připojeného k centru IoT. V tomto rychlém startu se používají dvě Node.js aplikace: aplikace simulovaného zařízení, která reaguje na přímé metody volané z back-endové aplikace a back-endové aplikace, která volá přímé metody na simulovaném zařízení.

## <a name="prerequisites"></a>Předpoklady

* Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Node.js 10 +](https://nodejs.org).

* [Vzorový Node.js projekt](https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip).

* Port 8883 otevřete v bráně firewall. Ukázka zařízení v tomto rychlém startu používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokovaný v některých podnikových a vzdělávacích prostředích sítě. Další informace a způsoby, jak tento problém obejít, najdete v tématu [připojení k IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

Aktuální verzi Node.js na počítači používaném pro vývoj můžete ověřit pomocí následujícího příkazu:

```cmd/sh
node --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Přidat rozšíření Azure IoT

Spuštěním následujícího příkazu přidejte do instance služby Cloud Shell Microsoft Azure rozšíření IoT pro rozhraní příkazového řádku Azure. Rozšíření IoT přidá do Azure CLI příkazy specifické pro IoT Hub, IoT Edge a IoT Device Provisioning Service (DPS).

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Pokud jste dokončili předchozí [Rychlý start: Odesílání telemetrických dat ze zařízení do centra IoT](quickstart-send-telemetry-node.md), můžete tento krok přeskočit.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrace zařízení

Pokud jste dokončili předchozí [Rychlý start: Odesílání telemetrických dat ze zařízení do centra IoT](quickstart-send-telemetry-node.md), můžete tento krok přeskočit.

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell.

1. Spuštěním následujícího příkazu v Azure Cloud Shell vytvořte identitu zařízení.

   **YourIoTHubName** : níže uvedený zástupný symbol nahraďte názvem, který jste zvolili pro Centrum IoT.

   **MyNodeDevice** : Toto je název zařízení, které registrujete. Doporučuje se používat **MyNodeDevice** , jak je znázorněno na obrázku. Pokud pro vaše zařízení zvolíte jiný název, budete ho muset použít i v celém rámci tohoto článku a před jeho spuštěním aktualizovat název zařízení v ukázkových aplikacích.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyNodeDevice
    ```

2. Spuštěním následujícího příkazu ve službě Azure Cloud Shell získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:

    **YourIoTHubName** : níže uvedený zástupný symbol nahraďte názvem, který jste zvolili pro Centrum IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name {YourIoTHubName} \
      --device-id MyNodeDevice \
      --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá takto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v tomto rychlém startu.

3. Potřebujete také _připojovací řetězec služby_ , který back-endové aplikaci umožní připojení k vašemu centru IoT a načtení zpráv. Následující příkaz načte připojovací řetězec služby pro vaše centrum IoT:

    **YourIoTHubName** : níže uvedený zástupný symbol nahraďte názvem, který jste zvolili pro Centrum IoT.

    ```azurecli-interactive
    az iot hub show-connection-string \
      --policy-name service --name {YourIoTHubName} --output table

    ```

    Poznamenejte si připojovací řetězec služby, který vypadá nějak takto:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v tomto rychlém startu. Tento připojovací řetězec služby se liší od připojovacího řetězce zařízení, který jste si poznamenali v předchozím kroku.

## <a name="listen-for-direct-method-calls"></a>Naslouchání voláním přímé metody

Aplikace simulovaného zařízení se připojí ke koncovému bodu v centru IoT pro konkrétní zařízení, odešle simulovaná telemetrická data a z vašeho centra naslouchá voláním přímé metody. Volání přímé metody z centra v tomto rychlém startu nařídí zařízení, aby změnilo interval, ve kterém se odesílají telemetrická data. Simulované zařízení po provedení přímé metody pošle potvrzení zpátky do vašeho centra.

1. V okně místního terminálu a přejděte do kořenové složky ukázkového projektu Node.js. Potom přejděte do složky **iot-hub\Quickstarts\simulated-device-2** .

2. V libovolném textovém editoru otevřete soubor **SimulatedDevice.js** .

    Nahraďte hodnotu `connectionString` proměnné připojovacím řetězcem zařízení, který jste si poznamenali dříve. Pak změny uložte do **SimulatedDevice.js** .

3. V okně místního terminálu pomocí následujících příkazů nainstalujte požadované knihovny a spusťte aplikaci simulovaného zařízení:

    ```cmd/sh
    npm install
    node SimulatedDevice.js
    ```

    Následující snímek obrazovky ukazuje výstup, zatímco aplikace simulovaného zařízení odesílá telemetrická data do vašeho centra IoT:

    ![Spuštění simulovaného zařízení](./media/quickstart-control-device-node/simulated-device-telemetry-iot-hub.png)

## <a name="call-the-direct-method"></a>Volání přímé metody

Back-endová aplikace se připojí ke koncovému bodu vašeho centra IoT na straně služby. Aplikace umožňuje přímé volání metod do zařízení prostřednictvím služby IoT Hub a naslouchá potvrzením. Back-endová aplikace služby IoT Hub se obvykle spouští v cloudu.

1. V jiném okně místního terminálu přejděte do kořenové složky ukázkového projektu Node.js. Potom přejděte do složky **iot-hub\Quickstarts\back-end-application** .

2. V libovolném textovém editoru otevřete soubor **BackEndApplication.js** .

    Nahraďte hodnotu `connectionString` proměnné připojovacím řetězcem služby, který jste si poznamenali v předchozím kroku. Pak změny uložte do **BackEndApplication.js** .

3. V okně místního terminálu pomocí následujících příkazů nainstalujte požadované knihovny a spusťte back-endovou aplikaci:

    ```cmd/sh
    npm install
    node BackEndApplication.js
    ```

    Následující snímek obrazovky ukazuje výstup, ve kterém aplikace představuje přímé volání metody do zařízení a přijímá potvrzení:

    ![Výstup, když aplikace vytvoří přímé volání metody do zařízení.](./media/quickstart-control-device-node/direct-method-device-call.png)

    Po spuštění back-endové aplikace se v okně konzoly se simulovaným zařízením zobrazí zpráva a rychlost odesílání zpráv se změní:

    ![Výstup, když dojde ke změně v simulovaném klientovi](./media/quickstart-control-device-node/simulated-device-message-change.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste volali přímou metodu na zařízení z back-endové aplikace a odpověděli na přímé volání metody v aplikaci simulovaného zařízení.

Informace o tom, jak směrovat zprávy typu zařízení-cloud do různých cílů v cloudu, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Kurz: Směrování telemetrických dat do různých koncových bodů za účelem zpracování](tutorial-routing.md)
