---
title: 'Úvodní příručka: Ovládání zařízení z Azure IoT Hub s Javou'
description: V tomto rychlém startu spustíte dvě ukázkové aplikace Java. První aplikace je back-endová aplikace, která může vzdáleně řídit zařízení připojená k vašemu centru. Druhá aplikace simuluje zařízení připojené k vašemu centru, které je možné řídit vzdáleně.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom:
- mvc
- seo-java-august2019
- seo-java-september2019
- mqtt
ms.date: 06/21/2019
ms.openlocfilehash: d73fab92535820186fcce997c2a5c72abf130c18
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771002"
---
# <a name="quickstart-control-a-device-connected-to-an-azure-iot-hub-with-java"></a>Úvodní příručka: Řízení zařízení připojeného k rozbočovači Azure IoT pomocí Javy

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

V tomto rychlém startu použijete přímou metodu k řízení simulované zařízení připojené k Azure IoT Hub s aplikací Java. IoT Hub je služba Azure, která vám umožní spravovat vaše zařízení IoT z cloudu a ingestovat velké objemy telemetrie zařízení do cloudu pro ukládání nebo zpracování. Přímé metody můžete použít k provádění vzdálených změn chování zařízení připojeného k centru IoT. Tento rychlý start používá dvě aplikace Jazyka Java: simulované aplikace zařízení, které reaguje na přímé metody volané z back-endové aplikace a aplikace služby, která volá přímou metodu na simulovaném zařízení.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* Java SE Vývojová sada 8. V [jazyce Java dlouhodobá podpora pro Azure a Azure Stack](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)v části Dlouhodobá **podpora**vyberte **Java 8**.

* [Apache Maven 3](https://maven.apache.org/download.cgi).

* [Ukázkový projekt Java](https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip).

* Port 8883 otevřít ve vašem firewallu. Ukázka zařízení v tomto rychlém startu používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokován v některých prostředích podnikové a vzdělávací sítě. Další informace a způsoby, jak tento problém vyřešit, najdete [v tématu připojení k centru IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

Aktuální verzi Javy na vývojovém počítači můžete ověřit pomocí následujícího příkazu:

```cmd/sh
java -version
```

Aktuální verzi Mavenu na vývojovém počítači můžete ověřit pomocí následujícího příkazu:

```cmd/sh
mvn --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Přidání rozšíření Azure IoT

Spusťte následující příkaz a přidejte rozšíření Microsoft Azure IoT extension pro Azure CLI do instance Cloud Shellu. Rozšíření IoT přidá do rozhraní příkazového příkazu Azure CLI specifické pro služby IoT Hub, IoT Edge a Služby zřizování zařízení IoT (DPS).

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Pokud jste dokončili předchozí [Rychlý start: Odesílání telemetrických dat ze zařízení do centra IoT](quickstart-send-telemetry-java.md), můžete tento krok přeskočit.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrování zařízení

Pokud jste dokončili předchozí [Rychlý start: Odesílání telemetrických dat ze zařízení do centra IoT](quickstart-send-telemetry-java.md), můžete tento krok přeskočit.

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell.

1. Spusťte následující příkaz v Azure Cloud Shell a vytvořte identitu zařízení.

   **YourIoTHubName**: Nahraďte tento zástupný symbol níže názvem, který jste vybrali pro svůj centr IoT.

   **MyJavaDevice**: Toto je název zařízení, které registrujete. Doporučuje se používat **MyJavaDevice,** jak je znázorněno. Pokud pro své zařízení zvolíte jiný název, musíte tento název použít v celém tomto článku a aktualizovat název zařízení v ukázkových aplikacích před jejich spuštěním.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyJavaDevice
    ```

2. Spuštěním následujícího příkazu ve službě Azure Cloud Shell získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:

   **YourIoTHubName**: Nahraďte tento zástupný symbol níže názvem, který zvolíte pro svůj iot hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name {YourIoTHubName} \
      --device-id MyJavaDevice \
      --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá nějak takto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v tomto rychlém startu.

## <a name="retrieve-the-service-connection-string"></a>Načtení připojovacího řetězce služby

Potřebujete také _připojovací řetězec služby_, který back-endové aplikaci umožní připojení k vašemu centru IoT a načtení zpráv. Následující příkaz načte připojovací řetězec služby pro vaše centrum IoT:

**YourIoTHubName**: Nahraďte tento zástupný symbol níže názvem, který jste vybrali pro svůj centr IoT.

```azurecli-interactive
az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
```

Poznamenejte si připojovací řetězec služby, který vypadá nějak takto:

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Tuto hodnotu použijete později v tomto rychlém startu. Tento připojovací řetězec služby se liší od připojovacího řetězce zařízení, který jste zaznamenali v předchozím kroku.

## <a name="listen-for-direct-method-calls"></a>Naslouchání voláním přímé metody

Aplikace simulovaného zařízení se připojí ke koncovému bodu v centru IoT pro konkrétní zařízení, odešle simulovaná telemetrická data a z vašeho centra naslouchá voláním přímé metody. Volání přímé metody z centra v tomto rychlém startu nařídí zařízení, aby změnilo interval, ve kterém se odesílají telemetrická data. Simulované zařízení odešle potvrzení zpět do rozbočovače po spuštění přímé metody.

1. V okně místního terminálu přejděte do kořenové složky ukázkového projektu Java. Potom přejděte do složky **iot-hub\Quickstarts\simulated-device-2**.

2. V libovolném textovém editoru otevřete soubor **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java**.

    Nahraďte hodnotu `connString` proměnné připojovacím řetězcem zařízení, který jste si dříve poznamenali. Pak uložte změny **SimulatedDevice.java**.

3. V okně místního terminálu pomocí následujících příkazů nainstalujte požadované knihovny a sestavte aplikaci simulovaného zařízení:

    ```cmd/sh
    mvn clean package
    ```

4. Spuštěním následujících příkazů v okně místního terminálu spusťte aplikaci simulovaného zařízení:

    ```cmd/sh
    java -jar target/simulated-device-2-1.0.0-with-deps.jar
    ```

    Následující snímek obrazovky ukazuje výstup, zatímco aplikace simulovaného zařízení odesílá telemetrická data do vašeho centra IoT:

    ![Výstup z telemetrie odeslané zařízením do centra IoT hub](./media/quickstart-control-device-java/iot-hub-application-send-telemetry-output.png)

## <a name="call-the-direct-method"></a>Volání přímé metody

Back-endová aplikace se připojí ke koncovému bodu vašeho centra IoT na straně služby. Aplikace provádí volání přímé metody zařízení prostřednictvím služby IoT hub a naslouchá potvrzení. Back-endová aplikace služby IoT Hub se obvykle spouští v cloudu.

1. V jiném okně místního terminálu přejděte do kořenové složky ukázkového projektu Java. Potom přejděte do složky **iot-hub\Quickstarts\back-end-application**.

2. V libovolném textovém editoru otevřete soubor **src/main/java/com/microsoft/docs/iothub/samples/BackEndApplication.java**.

    Nahraďte hodnotu `iotHubConnectionString` proměnné připojovacím řetězcem služby, který jste si dříve poznamenali. Potom uložte změny na **BackEndApplication.java**.

3. V okně místního terminálu pomocí následujících příkazů nainstalujte požadované knihovny a sestavte back-endovou aplikaci:

    ```cmd/sh
    mvn clean package
    ```

4. Spuštěním následujících příkazů v okně místního terminálu spusťte back-endovou aplikaci:

    ```cmd/sh
    java -jar target/back-end-application-1.0.0-with-deps.jar
    ```

    Následující snímek obrazovky ukazuje výstup, protože aplikace provádí přímé volání metody zařízení a přijímá potvrzení:

    ![Výstup jako aplikace provádí přímé volání metody prostřednictvím služby IoT hub](./media/quickstart-control-device-java/iot-hub-direct-method-call-output.png)

    Po spuštění back-endové aplikace se v okně konzoly se simulovaným zařízením zobrazí zpráva a rychlost odesílání zpráv se změní:

    ![Zpráva konzoly ze zařízení zobrazuje rychlost, jakou se mění](./media/quickstart-control-device-java/iot-hub-sent-message-change-rate.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste volali přímou metodu na zařízení z back-endové aplikace a odpověděli na volání přímé metody v aplikaci simulované zařízení.

Informace o tom, jak směrovat zprávy typu zařízení-cloud do různých cílů v cloudu, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Kurz: Směrování telemetrických dat do různých koncových bodů za účelem zpracování](tutorial-routing.md)
