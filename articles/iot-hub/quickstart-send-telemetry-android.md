---
title: Odesílání telemetrie k rychlému startu pro Azure IoT Hub (Android) | Dokumentace Microsoftu
description: V tomto rychlém startu spustíte ukázku s Androidem aplikace odesílá Simulovaná telemetrická data do služby IoT hub a čtení telemetrických dat ze služby IoT hub pro zpracování v cloudu.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/15/2019
ms.author: wesmc
ms.openlocfilehash: b8cf0891bd2a11a4ea46cc9fb8bad266862b6971
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/04/2019
ms.locfileid: "59005373"
---
# <a name="quickstart-send-iot-telemetry-from-an-android-device"></a>Rychlý start: Odesílat IoT telemetrická data ze zařízení s Androidem

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub je služba Azure, která umožňuje ingestovat velké objemy telemetrických dat ze zařízení IoT do cloudu pro účely uložení nebo zpracování. V tomto rychlém startu můžete odesílat telemetrická data do služby IoT Hub z aplikace pro Android běží na fyzických nebo simulovaných zařízení.

Rychlý start používá předem napsaná aplikace systému Android odesílat telemetrická data. Telemetrická data budou číst ze služby IoT Hub pomocí Azure Cloud Shell. Před spuštěním aplikace vytvoříte službu IoT hub a registraci zařízení do centra.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Android studio z https://developer.android.com/studio/. Další informace týkající se instalace sady Android Studio najdete v tématu [android instalace](https://developer.android.com/studio/install). 

* 27. Android SDK se používá v rámci ukázky v tomto článku.

* Spusťte následující příkaz pro přidání rozšíření Microsoft Azure IoT pro Azure CLI do instance služby Cloud Shell. Rozšíření IOT přidá služby IoT Hub, IoT Edge a IoT zařízení zřizování služby (DPS) konkrétní příkazy rozhraní příkazového řádku Azure.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

* [Ukázkové aplikace pro Android](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample) spustíte v tomto rychlém startu je součástí sady azure-iot-samples-java úložišti na Githubu. Stáhněte nebo naklonujte [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java) úložiště.

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrování zařízení

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell.

1. Spusťte následující příkaz v Azure Cloud Shell vytvořte identitu zařízení.

   **YourIoTHubName**: Nahraďte tento zástupný text pod názvem, který jste vybrali pro službu IoT hub.

   **MyAndroidDevice**: MyAndroidDevice je název pro registrovaná zařízení. Použijte MyAndroidDevice, jak je znázorněno. Pokud si zvolíte jiný název zařízení, budete ho muset používat v průběhu celého článku a aktualizovat název zařízení v ukázkových aplikacích, než je spustíte.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidDevice
    ```

2. Spuštěním následujícího příkazu ve službě Azure Cloud Shell získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:

    **YourIoTHubName**: Nahraďte tento zástupný text pod názvem, který jste vybrali pro službu IoT hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidDevice --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá nějak takto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v tomto rychlém startu k odesílání telemetrie.

## <a name="send-telemetry"></a>Odeslání telemetrie

1. Otevřete ukázkový projekt Android Githubu v nástroji Android Studio. Projekt se nachází v následujícím adresáři klonované nebo stažených kopie [azure-iot ukázka java](https://github.com/Azure-Samples/azure-iot-samples-java) úložiště.

        \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample

2. V nástroji Android Studio, otevřete *gradle.properties* pro ukázkového projektu a nahraďte **Device_Connection_String** zástupného symbolu připojovacím řetězcem zařízení jste si předtím poznamenali.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. V nástroji Android Studio, klikněte na tlačítko **souboru** > **synchronizovat projekt se soubory Gradle**. Ověřte, že dokončení sestavení.

   > [!NOTE]
   > Pokud dojde k selhání synchronizace projektu, může být pro jednu z následujících důvodů:
   >
   > * Verze modulu plug-in Android Gradle a Gradle z projektu odkazováno jsou zastaralé pro vaši verzi systému Android Studio. Postupujte podle [tyto pokyny](https://developer.android.com/studio/releases/gradle-plugin) odkaz a nainstalujte správná verze modulu plug-in a Gradle pro vaši instalaci.
   > * Licenční smlouvy pro sadu Android SDK není podepsaný. Postupujte podle pokynů ve výstupu sestavení k podepisování licenční smlouvy a stáhnout sadu SDK.

4. Po dokončení sestavení kliknutím na **spustit** > **spuštění "aplikace"**. Konfigurace aplikace pro spuštění na fyzické zařízení s Androidem nebo emulátoru Androidu. Další informace o spouštění aplikace pro Android na fyzické zařízení nebo emulátoru najdete v tématu [spuštění aplikace](https://developer.android.com/training/basics/firstapp/running-app).

5. Jakmile aplikace načte, klikněte na tlačítko **Start** tlačítko Zahájit odesílání telemetrických dat do služby IoT Hub:

    ![Aplikace](media/quickstart-send-telemetry-android/sample-screenshot.png)


## <a name="read-the-telemetry-from-your-hub"></a>Čtení telemetrických dat z centra

V této části budete používat Azure Cloud Shell s [rozšíření IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) k monitorování zařízení zprávy, které jsou odesílány zařízení s Androidem.

1. Pomocí služby Azure Cloud Shell spusťte následující příkaz, který provede připojení a čtení zpráv z centra IoT:

   **YourIoTHubName**: Nahraďte tento zástupný text pod názvem, který jste vybrali pro službu IoT hub.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```
    Na následujícím snímku obrazovky se zobrazí výstup jako IoT hub přijímá telemetrická data odesílaná zařízení s Androidem:

      ![Čtení zpráv zařízení pomocí Azure CLI](media/quickstart-send-telemetry-android/read-data.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste nastavení IoT hub, zaregistrované zařízení, odeslali Simulovaná telemetrická data do centra pomocí aplikace pro Android a čtení telemetrických dat z centra pomocí Azure Cloud Shell.

Informace o tom, jak řídit simulované zařízení z back-endové aplikace, najdete v dalším rychlém startu.

> [!div class="nextstepaction"]
> [Rychlý start: Řízení zařízení připojeném do služby IoT hub](quickstart-control-device-android.md)

