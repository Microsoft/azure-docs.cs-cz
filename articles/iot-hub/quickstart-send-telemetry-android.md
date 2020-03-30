---
title: Odeslání telemetrie do rychlého startu služby Azure IoT Hub (Android) | Dokumenty společnosti Microsoft
description: V tomto rychlém startu spustíte ukázkovou aplikaci pro Android, která odesílá simulovanou telemetrii do centra IoT hubu a čte telemetrická data z centra IoT hub pro zpracování v cloudu.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/15/2019
ms.author: wesmc
ms.openlocfilehash: e239f8c6a7738500176b84589f2032636eeb71e5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675444"
---
# <a name="quickstart-send-iot-telemetry-from-an-android-device"></a>Úvodní příručka: Odeslání telemetrie IoT ze zařízení se systémem Android

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

V tomto rychlém startu odesíláte telemetrická data do služby Azure IoT Hub z aplikace pro Android spuštěné na fyzickém nebo simulovaném zařízení. IoT Hub je služba Azure, která umožňuje ingestovat velké objemy telemetrických dat ze zařízení IoT do cloudu pro účely uložení nebo zpracování. Tento rychlý start používá předem napsanou aplikaci pro Android k odeslání telemetrie. Telemetrie se bude číst z ioT hubu pomocí Azure Cloud Shell. Před spuštěním aplikace vytvoříte službu IoT hub a zaregistrujete zařízení s centrem.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Android Studio s Android SDK 27](https://developer.android.com/studio/). Další informace naleznete v tématu [android-installation](https://developer.android.com/studio/install). Android SDK 27 se používá v ukázce v tomto článku.

* [Ukázka aplikace pro Android](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample). Tato ukázka je součástí úložiště [azure-iot-samples-java.](https://github.com/Azure-Samples/azure-iot-samples-java)

* Port 8883 otevřít ve vašem firewallu. Ukázka zařízení v tomto rychlém startu používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokován v některých prostředích podnikové a vzdělávací sítě. Další informace a způsoby, jak tento problém vyřešit, najdete [v tématu připojení k centru IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Přidání rozšíření Azure IoT

Spusťte následující příkaz a přidejte rozšíření Microsoft Azure IoT extension pro Azure CLI do instance Cloud Shellu. Rozšíření IOT přidá do rozhraní příkazového příkazu Azure CLI specifické pro služby IoT Hub, IoT Edge a Služby zřizování zařízení IoT (DPS).

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrování zařízení

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell.

1. Spusťte následující příkaz v Azure Cloud Shell a vytvořte identitu zařízení.

   **YourIoTHubName**: Nahraďte tento zástupný symbol níže názvem, který jste vybrali pro svůj centr IoT.

   **MyAndroidDevice**: Toto je název zařízení, které registrujete. Doporučuje se používat **MyAndroidDevice,** jak je znázorněno. Pokud pro své zařízení zvolíte jiný název, budete muset tento název použít v celém tomto článku a aktualizovat název zařízení v ukázkových aplikacích před jejich spuštěním.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Spusťte následující příkaz v Prostředí Azure Cloud Shell a získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:

    **YourIoTHubName**: Nahraďte tento zástupný symbol níže názvem, který jste vybrali pro svůj centr IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyAndroidDevice --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá nějak takto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v tomto rychlém startu k odeslání telemetrie.

## <a name="send-simulated-telemetry"></a>Odesílání simulovaných telemetrických dat

1. Otevřete ukázkový projekt GitHubu pro Android ve Studiu androida. Projekt je umístěn v následujícím adresáři klonované nebo stažené kopie [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java) úložiště.

        \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample

2. V Android Studio *otevřete gradle.properties* pro ukázkový projekt a nahraďte zástupný symbol **Device_Connection_String** připojovacím řetězcem zařízení, který jste si dříve poznamenali.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. Ve Studiu pro Android klikněte na Project pro synchronizaci **souborů** > **s gradle soubory**. Ověřte dokončení sestavení.

   > [!NOTE]
   > Pokud se synchronizace projektu nezdaří, může to být z jednoho z následujících důvodů:
   >
   > * Verze pluginu Android Gradle a Gradle odkazované v projektu jsou zastaralé pro vaši verzi Android Studia. Postupujte [podle těchto pokynů](https://developer.android.com/studio/releases/gradle-plugin) odkazovat a nainstalovat správné verze pluginu a Gradle pro vaši instalaci.
   > * Licenční smlouva pro android sdk nebyla podepsána. Podle pokynů ve výstupu sestavení podepište licenční smlouvu a stáhněte si sadu SDK.

4. Po dokončení sestavení klepněte na tlačítko **Spustit** > **spustit 'aplikaci'**. Nakonfigurujte aplikaci tak, aby běžela na fyzickém zařízení Android nebo emulátoru Androidu. Další informace o spuštění aplikace pro Android na fyzickém zařízení nebo emulátoru najdete v tématu [Spuštění aplikace](https://developer.android.com/training/basics/firstapp/running-app).

5. Po načtení aplikace klikněte na tlačítko **Start** a začněte odesílat telemetrii do služby IoT Hub:

    ![Aplikace](media/quickstart-send-telemetry-android/sample-screenshot.png)


## <a name="read-the-telemetry-from-your-hub"></a>Čtení telemetrických dat z centra

V této části použijete Azure Cloud Shell s [rozšířením IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) ke sledování zpráv zařízení, které jsou odesílány zařízením Android.

1. Pomocí služby Azure Cloud Shell spusťte následující příkaz, který provede připojení a čtení zpráv z centra IoT:

   **YourIoTHubName**: Nahraďte tento zástupný symbol níže názvem, který jste vybrali pro svůj centr IoT.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    Následující snímek obrazovky ukazuje výstup jako služba IoT hub přijímá telemetrii odeslanou zařízením Android:

      ![Čtení zpráv zařízení pomocí Azure CLI](media/quickstart-send-telemetry-android/read-data.png)
## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu nastavíte službu IoT hub, zaregistrujete zařízení, odešlete simulovanou telemetrii do centra pomocí aplikace pro Android a načtete telemetrickou metu z centra pomocí prostředí Azure Cloud Shell.

Informace o tom, jak řídit simulované zařízení z back-endové aplikace, najdete v dalším rychlém startu.

> [!div class="nextstepaction"]
> [Rychlý start: Řízení zařízení připojeného k centru IoT](quickstart-control-device-android.md)