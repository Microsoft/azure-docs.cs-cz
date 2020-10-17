---
title: Řízení zařízení v rychlém startu Azure IoT Hub (Android) | Microsoft Docs
description: V tomto rychlém startu spustíte dvě ukázkové aplikace Java. Jedna aplikace je aplikace služby, která může vzdáleně řídit zařízení připojená k rozbočovači. Druhá aplikace běží na fyzickém nebo simulovaném zařízení připojeném k vašemu rozbočovači, který se dá řídit vzdáleně.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- devx-track-java
ms.date: 06/21/2019
ms.author: wesmc
ms.openlocfilehash: bd23483997b94f16e926c2849e0879b41316fba3
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148904"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-android"></a>Rychlý Start: řízení zařízení připojeného ke centru IoT (Android)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

V tomto rychlém startu použijete přímou metodu k řízení simulovaného zařízení připojeného k Azure IoT Hub. IoT Hub je služba Azure, která umožňuje spravovat zařízení IoT z cloudu a ingestovat velké objemy telemetrie zařízení do cloudu za účelem uložení nebo zpracování. Přímé metody můžete použít k provádění vzdálených změn chování zařízení připojeného k centru IoT. V tomto rychlém startu se používají dvě aplikace: simulovaná aplikace zařízení, která reaguje na přímé metody volané z back-endové aplikace služby a aplikace služby, která volá metodu Direct na zařízení s Androidem.

## <a name="prerequisites"></a>Předpoklady

* Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Android Studio s Android SDK 27](https://developer.android.com/studio/). Další informace najdete v tématu [instalace Android Studio](https://developer.android.com/studio/install).

* [Git](https://git-scm.com/download/).

* [Sada SDK pro zařízení – ukázková aplikace pro Android](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample), která je součástí [ukázek Azure IoT (Java)](https://github.com/Azure-Samples/azure-iot-samples-java)

* [Ukázková aplikace pro Android Service SDK](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/service/AndroidSample), která je součástí ukázek Azure IoT (Java).

* Port 8883 otevřete v bráně firewall. Ukázka zařízení v tomto rychlém startu používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokovaný v některých podnikových a vzdělávacích prostředích sítě. Další informace a způsoby, jak tento problém obejít, najdete v tématu [připojení k IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Přidat rozšíření Azure IoT

Spuštěním následujícího příkazu přidejte do instance služby Cloud Shell Microsoft Azure rozšíření IoT pro rozhraní příkazového řádku Azure. Rozšíření IoT přidá do Azure CLI příkazy specifické pro IoT Hub, IoT Edge a IoT Device Provisioning Service (DPS).

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Pokud jste dokončili předchozí [rychlý Start: odeslání telemetrie ze zařízení do služby IoT Hub](quickstart-send-telemetry-android.md), můžete tento krok přeskočit a použít již vytvořeného centra IoT.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrace zařízení

Pokud jste dokončili předchozí [rychlý Start: odeslání telemetrie ze zařízení do služby IoT Hub](quickstart-send-telemetry-android.md), můžete tento krok přeskočit a použít stejné zařízení, které je zaregistrované v předchozím rychlém startu.

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell.

1. Spuštěním následujícího příkazu v Azure Cloud Shell vytvořte identitu zařízení.

   **YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který jste zvolili pro Centrum IoT.

   **MyAndroidDevice**: Toto je název zařízení, které registrujete. Doporučuje se používat **MyAndroidDevice** , jak je znázorněno na obrázku. Pokud pro vaše zařízení zvolíte jiný název, budete ho muset použít i v celém rámci tohoto článku a před jeho spuštěním aktualizovat název zařízení v ukázkových aplikacích.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Spuštěním následujícího příkazu ve službě Azure Cloud Shell získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:

   **YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který zvolíte pro Centrum IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name {YourIoTHubName} \
      --device-id MyAndroidDevice \
      --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá takto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v tomto rychlém startu.

## <a name="retrieve-the-service-connection-string"></a>Načtení připojovacího řetězce služby

Také potřebujete _připojovací řetězec služby_ , který umožňuje, aby se aplikace back-end připojovaly ke službě IoT Hub, aby bylo možné spouštět metody a získávat zprávy. Následující příkaz načte připojovací řetězec služby pro vaše centrum IoT:

**YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který jste zvolili pro Centrum IoT.

```azurecli-interactive
az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
```

Poznamenejte si připojovací řetězec služby, který vypadá nějak takto:

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Tuto hodnotu použijete později v tomto rychlém startu. Tento připojovací řetězec služby se liší od připojovacího řetězce zařízení, který jste si poznamenali v předchozím kroku.

## <a name="listen-for-direct-method-calls"></a>Naslouchání voláním přímé metody

Obě ukázky pro tento rychlý Start jsou součástí úložiště Azure-IoT-Samples-Java na GitHubu. Stáhněte nebo naklonujte úložiště [Azure-IoT-Samples-Java](https://github.com/Azure-Samples/azure-iot-samples-java) .

Ukázkovou aplikaci sady SDK pro zařízení můžete spustit na fyzickém zařízení s Androidem nebo v emulátoru Androidu. Ukázka se připojí ke koncovému bodu specifickému pro zařízení ve službě IoT Hub, pošle simulovanou telemetrii a naslouchá volání přímých metod z vašeho centra. Volání přímé metody z centra v tomto rychlém startu nařídí zařízení, aby změnilo interval, ve kterém se odesílají telemetrická data. Simulované zařízení po provedení přímé metody pošle potvrzení zpátky do vašeho centra.

1. V Android Studio otevřete projekt GitHub Sample Android. Projekt je umístěný v následujícím adresáři naklonované nebo stažené kopie úložiště [Azure-IoT-Sample-Java](https://github.com/Azure-Samples/azure-iot-samples-java) : *\azure-IoT-Samples-java\iot-hub\Samples\device\AndroidSample*.

2. V Android Studio otevřete *Gradle. Properties* pro ukázkový projekt a nahraďte zástupný text **Device_Connection_String** pomocí připojovacího řetězce zařízení, který jste si poznamenali dříve.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. V Android Studio klikněte na **File**  >  **projekt synchronizace souborů se soubory Gradle**. Ověřte, že se sestavení dokončilo.

   > [!NOTE]
   > Pokud synchronizace projektu neproběhne úspěšně, může to být z některého z následujících důvodů:
   >
   > * Verze modulu plug-in pro Android Gradle a Gradle, na které se odkazuje v projektu, jsou zastaralé pro vaši verzi Android Studio. Podle [těchto pokynů](https://developer.android.com/studio/releases/gradle-plugin) můžete odkazovat a instalovat správné verze modulu plug-in a Gradle pro vaši instalaci.
   > * Licenční smlouva pro Android SDK nebyla podepsána. Podle pokynů ve výstupu sestavení podepište licenční smlouvu a Stáhněte si sadu SDK.

4. Po dokončení sestavení klikněte na **Spustit**  >  **Spustit aplikaci**. Nakonfigurujte aplikaci tak, aby běžela na fyzickém zařízení s Androidem nebo v emulátoru Androidu. Další informace o spuštění aplikace pro Android na fyzickém zařízení nebo emulátoru najdete v tématu [spuštění aplikace](https://developer.android.com/training/basics/firstapp/running-app).

5. Po načtení aplikace kliknutím na tlačítko **Start** spustíte odesílání telemetrie do IoT Hub:

    ![Ukázka snímku obrazovky aplikace pro Android klientského zařízení](media/quickstart-control-device-android/sample-screenshot.png)

Tato aplikace musí být při spuštění ukázky sady SDK v provozu na fyzickém zařízení nebo emulátoru, aby se interval telemetrie aktualizoval během doby běhu.

## <a name="read-the-telemetry-from-your-hub"></a>Čtení telemetrie z centra

V této části použijete Azure Cloud Shell s [rozšířením IoT](/cli/azure/ext/azure-iot/iot?view=azure-cli-latest) k monitorování zpráv odesílaných zařízením s Androidem.

1. Pomocí služby Azure Cloud Shell spusťte následující příkaz, který provede připojení a čtení zpráv z centra IoT:

   **YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který zvolíte pro Centrum IoT.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    Následující snímek obrazovky ukazuje výstup, ve kterém služba IoT Hub přijímá telemetrii odeslanou zařízením s Androidem:

      ![Čtení zpráv zařízení pomocí Azure CLI](media/quickstart-control-device-android/read-data.png)

Ve výchozím nastavení aplikace telemetrie odesílá telemetrii ze zařízení s Androidem každých pět sekund. V další části použijete přímé volání metody k aktualizaci intervalu telemetrie pro zařízení s Androidem IoT.

## <a name="call-the-direct-method"></a>Volání přímé metody

Aplikace služby se v IoT Hub připojí ke koncovému bodu na straně služby. Aplikace umožňuje přímé volání metod do zařízení prostřednictvím služby IoT Hub a naslouchá potvrzením.

Tuto aplikaci spusťte na samostatném fyzickém zařízení s Androidem nebo emulátoru Androidu.

Aplikace služby back-end IoT Hub obvykle běží v cloudu, kde je snazší zmírnit rizika spojená s citlivým připojovacím řetězcem, který řídí všechna zařízení v IoT Hub. V tomto příkladu ho používáme jako aplikaci pro Android jenom pro demonstrační účely. Jiné jazykové verze tohoto rychlého startu poskytují příklady, které úzce přizpůsobují typickou aplikaci back-end služby.

1. V Android Studio otevřete vzorový projekt pro Android Service. Projekt je umístěný v následujícím adresáři naklonované nebo stažené kopie úložiště [Azure-IoT-Sample-Java](https://github.com/Azure-Samples/azure-iot-samples-java) : *\azure-IoT-Samples-java\iot-hub\Samples\service\AndroidSample*.

2. V Android Studio otevřete *Gradle. Properties* pro ukázkový projekt. Aktualizujte hodnoty vlastností **ConnectionString** a **DeviceID** pomocí připojovacího řetězce služby, který jste si poznamenali dříve, a ID zařízení s Androidem, které jste si zaregistrovali.

    ```
    ConnectionString=HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}
    DeviceId=MyAndroidDevice
    ```

3. V Android Studio klikněte na **File**  >  **projekt synchronizace souborů se soubory Gradle**. Ověřte, že se sestavení dokončilo.

   > [!NOTE]
   > Pokud synchronizace projektu neproběhne úspěšně, může to být z některého z následujících důvodů:
   >
   > * Verze modulu plug-in pro Android Gradle a Gradle, na které se odkazuje v projektu, jsou zastaralé pro vaši verzi Android Studio. Podle [těchto pokynů](https://developer.android.com/studio/releases/gradle-plugin) můžete odkazovat a instalovat správné verze modulu plug-in a Gradle pro vaši instalaci.
   > * Licenční smlouva pro Android SDK nebyla podepsána. Podle pokynů ve výstupu sestavení podepište licenční smlouvu a Stáhněte si sadu SDK.

4. Po dokončení sestavení klikněte na **Spustit**  >  **Spustit aplikaci**. Nakonfigurujte aplikaci tak, aby běžela na samostatném fyzickém zařízení s Androidem nebo v emulátoru Androidu. Další informace o spuštění aplikace pro Android na fyzickém zařízení nebo emulátoru najdete v tématu [spuštění aplikace](https://developer.android.com/training/basics/firstapp/running-app).

5. Po načtení aplikace aktualizujte hodnotu **nastavit interval zasílání zpráv** na **1000** a klikněte na **vyvolat**.

    Interval zasílání zpráv o telemetrie je v milisekundách. Výchozí interval telemetrie pro zařízení je nastavený na 5 sekund. Tato změna bude aktualizovat zařízení s Androidem IoT, aby se tato telemetrie poslala každou sekundu.

    ![Zadejte interval telemetrie.](media/quickstart-control-device-android/enter-telemetry-interval.png)

6. Aplikace dostane potvrzení, že se metoda úspěšně spustila, nebo ne.

    ![Potvrzení přímé metody](media/quickstart-control-device-android/direct-method-ack.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste volali přímou metodu na zařízení z back-endové aplikace a odpověděli na přímé volání metody v aplikaci simulovaného zařízení.

Informace o tom, jak směrovat zprávy typu zařízení-cloud do různých cílů v cloudu, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Kurz: Směrování telemetrických dat do různých koncových bodů za účelem zpracování](tutorial-routing.md)