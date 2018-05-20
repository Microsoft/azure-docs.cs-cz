---
title: Rychlý start k řízení zařízení ze služby Azure IoT Hub (Java) | Microsoft Docs
description: V tomto rychlém startu spustíte dvě ukázkové aplikace Java. První aplikace je back-endová aplikace, která může vzdáleně řídit zařízení připojená k vašemu centru. Druhá aplikace simuluje zařízení připojené k vašemu centru, které je možné řídit vzdáleně.
services: iot-hub
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: ns
ms.date: 04/30/2018
ms.author: dobett
ms.openlocfilehash: 6dcbf954fdfd6f5b6f65b54edf33e9da234c7d0f
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-java"></a>Rychlý start: Řízení zařízení připojeného k centru IoT (Java)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub je služba Azure, která umožňuje ingestovat velké objemy telemetrických dat ze zařízení IoT do cloudu a spravovat zařízení z cloudu. V tomto rychlém startu použijete *přímou metodu* k řízení simulovaného zařízení připojeného k centru IoT. Přímé metody můžete použít k provádění vzdálených změn chování zařízení připojeného k centru IoT.

Rychlý start používá dvě předem napsané aplikace Java:

* Aplikaci simulovaného zařízení, která odpovídá na přímé metody volané z back-endové aplikace. Aby bylo možné přijímat volání přímé metody, připojí se tato aplikace ke koncovému bodu centra IoT pro konkrétní zařízení.
* Back-endovou aplikaci, která na simulovaném zařízení volá přímé metody. Aby na zařízení bylo možné volat přímou metodu, připojí se tato aplikace ke koncovému bodu vašeho centra IoT na straně služby.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Dvě ukázkové aplikace, které spustíte v tomto rychlém startu, jsou napsány pomocí Javy. Na vývojovém počítači musíte mít Java SE 8 nebo novější.

Javu pro různé platformy si můžete stáhnout z webu [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html).

Aktuální verzi Javy na vývojovém počítači můžete ověřit pomocí následujícího příkazu:

```cmd/sh
java --version
```

K sestavení ukázek potřebujete nainstalovat Maven 3. Maven pro různé platformy si můžete stáhnout z webu [Apache Maven](https://maven.apache.org/download.cgi).

Aktuální verzi Mavenu na vývojovém počítači můžete ověřit pomocí následujícího příkazu:

```cmd/sh
mvn --version
```

Pokud jste to ještě neudělali, stáhněte si ukázkový projekt Java z webu https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip a extrahujte archiv ZIP.

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Pokud jste dokončili předchozí [Rychlý start: Odesílání telemetrických dat ze zařízení do centra IoT](quickstart-send-telemetry-java.md), můžete tento krok přeskočit.

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>Registrování zařízení

Pokud jste dokončili předchozí [Rychlý start: Odesílání telemetrických dat ze zařízení do centra IoT](quickstart-send-telemetry-java.md), můžete tento krok přeskočit.

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu pomocí Azure CLI zaregistrujete simulované zařízení.

1. Přidejte rozšíření rozhraní příkazového řádku IoT Hub a vytvořte identitu zařízení. `{YourIoTHubName}` nahraďte názvem, který jste vybrali pro centrum IoT:

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyJavaDevice
    ```

    Pokud si zvolíte jiný název zařízení, změňte ho také v ukázkových aplikacích, než je spustíte.

1. Spuštěním následujícího příkazu získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyJavaDevice --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá nějak takto: `Hostname=...=`. Tuto hodnotu použijete později v tomto rychlém startu.

## <a name="retrieve-the-service-connection-string"></a>Načtení připojovacího řetězce služby

Potřebujete také _připojovací řetězec služby_ IoT Hub, který back-endové aplikaci umožní připojení k vašemu centru a načtení zpráv. Následující příkaz načte připojovací řetězec služby pro vaše centrum IoT:

```azurecli-interactive
az iot hub show-connection-string --hub-name {YourIoTHubName} --output table
```

Poznamenejte si připojovací řetězec služby, který vypadá nějak takto: `Hostname=...=`. Tuto hodnotu použijete později v tomto rychlém startu.

## <a name="listen-for-direct-method-calls"></a>Naslouchání voláním přímé metody

Aplikace simulovaného zařízení se připojí ke koncovému bodu v centru IoT pro konkrétní zařízení, odešle simulovaná telemetrická data a z vašeho centra naslouchá voláním přímé metody. Volání přímé metody z centra v tomto rychlém startu nařídí zařízení, aby změnilo interval, ve kterém se odesílají telemetrická data. Simulované zařízení odešle po spuštění přímé metody zpět do centra potvrzení.

1. V okně terminálu přejděte do kořenové složky ukázkového projektu Java. Pak přejděte do složky **Quickstarts\simulated-device-2**.

1. V libovolném textovém editoru otevřete soubor **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java**.

    Hodnotu proměnné `connString` nahraďte připojovacím řetězcem zařízení, který jste si předtím poznamenali. Změny pak uložte do souboru **SimulatedDevice.java**.

1. V okně terminálu spusťte následující příkazy pro instalaci požadovaných knihoven a sestavení aplikace simulovaného zařízení:

    ```cmd/sh
    mvn clean package
    ```

1. V okně terminálu spusťte následující příkazy pro spuštění aplikace simulovaného zařízení:

    ```cmd/sh
    java -jar target/simulated-device-2-1.0.0-with-deps.jar
    ```

    Následující snímek obrazovky ukazuje výstup, zatímco aplikace simulovaného zařízení odesílá telemetrická data do vašeho centra IoT:

    ![Spuštění simulovaného zařízení](media/quickstart-control-device-java/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>Volání přímé metody

Back-endová aplikace se připojí ke koncovému bodu vašeho centra IoT na straně služby. Aplikace provádí volání přímé metody na zařízení prostřednictvím centra IoT a čeká na potvrzení. Back-endová aplikace služby IoT Hub se obvykle spouští v cloudu.

1. V jiném okně terminálu přejděte do kořenové složky ukázkového projektu Java. Pak přejděte do složky **Quickstarts\back-end-application**.

1. V libovolném textovém editoru otevřete soubor **src/main/java/com/microsoft/docs/iothub/samples/ReadDeviceToCloudMessages.java**.

    Hodnotu proměnné `iotHubConnectionString` nahraďte připojovacím řetězcem služby, který jste si předtím poznamenali. Změny pak uložte do souboru **BackEndApplication.java**.

1. V okně terminálu spusťte následující příkazy pro instalaci požadovaných knihoven a sestavení back-endové aplikace:

    ```cmd/sh
    mvn clean package
    ```

1. V okně terminálu spusťte následující příkazy pro spuštění back-endové aplikace:

    ```cmd/sh
    java -jar target/back-end-application-1.0.0-with-deps.jar
    ```

    Následující snímek obrazovky ukazuje výstup, zatímco aplikace provádí volání přímé metody na zařízení a obdrží potvrzení:

    ![Spuštění back-endové aplikace](media/quickstart-control-device-java/BackEndApplication.png)

    Po spuštění back-endové aplikace se v okně konzoly se simulovaným zařízením zobrazí zpráva a rychlost odesílání zpráv se změní:

    ![Změna simulovaného klienta](media/quickstart-control-device-java/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud se budete chtít přesunout ke kurzům, zachovejte skupinu prostředků a centrum IoT pro pozdější použití.

Pokud už centrum IoT nepotřebujete, odstraňte ho společně se skupinou prostředků na portálu. Provedete to výběrem skupiny prostředků, která obsahuje vaše centrum IoT, a kliknutím na **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste volali přímou metodu na zařízení z back-endové aplikace a odpovídali na volání přímé metody v aplikaci simulovaného zařízení.

Informace o tom, jak směrovat zprávy typu zařízení-cloud do různých cílů v cloudu, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Kurz: Směrování telemetrických dat do různých koncových bodů za účelem zpracování](iot-hub-java-java-process-d2c.md)