---
title: Řízení zařízení z rychlého startu služby Azure IoT Hub (Android) | Dokumenty společnosti Microsoft
description: V tomto rychlém startu spustíte dvě ukázkové aplikace Java. Jedna aplikace je aplikace služby, která může vzdáleně ovládat zařízení připojená k rozbočovači. Druhá aplikace běží na fyzickém nebo simulovaném zařízení připojeném k rozbočovači, které lze ovládat vzdáleně.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/21/2019
ms.author: wesmc
ms.openlocfilehash: 5a912199869cb2cce690a3502345ec6f9ca43d22
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675678"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-android"></a>Úvodní příručka: Ovládání zařízení připojeného k centru IoT hub (Android)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

V tomto rychlém startu použijete přímou metodu k řízení simulované zařízení připojené k Azure IoT Hub. IoT Hub je služba Azure, která vám umožní spravovat vaše zařízení IoT z cloudu a ingestovat velké objemy telemetrie zařízení do cloudu pro ukládání nebo zpracování. Přímé metody můžete použít k provádění vzdálených změn chování zařízení připojeného k centru IoT. Tento rychlý start používá dvě aplikace: simulované zařízení aplikace, která reaguje na přímé metody volané z aplikace back-end služby a aplikace služby, která volá přímou metodu na zařízení Se systémem Android.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Android Studio s Android SDK 27](https://developer.android.com/studio/). Další informace naleznete v [tématu Instalace sady Android Studio](https://developer.android.com/studio/install).

* [Git](https://git-scm.com/download/).

* [Ukázka sady Device SDK pro Android](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample)zahrnuté v [ukázkách Azure IoT (Java).](https://github.com/Azure-Samples/azure-iot-samples-java)

* [Ukázka sady Service SDK pro Android](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/service/AndroidSample), která je součástí ukázky Azure IoT (Java).

* Port 8883 otevřít ve vašem firewallu. Ukázka zařízení v tomto rychlém startu používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokován v některých prostředích podnikové a vzdělávací sítě. Další informace a způsoby, jak tento problém vyřešit, najdete [v tématu připojení k centru IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Přidání rozšíření Azure IoT

Spusťte následující příkaz a přidejte rozšíření Microsoft Azure IoT extension pro Azure CLI do instance Cloud Shellu. Rozšíření IoT přidá do rozhraní příkazového příkazu Azure CLI specifické pro služby IoT Hub, IoT Edge a Služby zřizování zařízení IoT (DPS).

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Pokud jste dokončili předchozí [úvodní příručku: Odeslat telemetrii ze zařízení do služby IoT hub](quickstart-send-telemetry-android.md), můžete tento krok přeskočit a použít službu IoT hub, kterou jste už vytvořili.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrování zařízení

Pokud jste dokončili předchozí [úvodní příručku: Odeslat telemetrii ze zařízení do služby IoT hub](quickstart-send-telemetry-android.md), můžete tento krok přeskočit a použít stejné zařízení registrované v předchozím rychlém startu.

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell.

1. Spusťte následující příkaz v Azure Cloud Shell a vytvořte identitu zařízení.

   **YourIoTHubName**: Nahraďte tento zástupný symbol níže názvem, který jste vybrali pro svůj centr IoT.

   **MyAndroidDevice**: Toto je název zařízení, které registrujete. Doporučuje se používat **MyAndroidDevice,** jak je znázorněno. Pokud pro své zařízení zvolíte jiný název, musíte tento název použít v celém tomto článku a aktualizovat název zařízení v ukázkových aplikacích před jejich spuštěním.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Spuštěním následujícího příkazu ve službě Azure Cloud Shell získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:

   **YourIoTHubName**: Nahraďte tento zástupný symbol níže názvem, který zvolíte pro svůj iot hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name {YourIoTHubName} \
      --device-id MyAndroidDevice \
      --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá nějak takto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v tomto rychlém startu.

## <a name="retrieve-the-service-connection-string"></a>Načtení připojovacího řetězce služby

Potřebujete také _připojovací řetězec služby,_ který umožní aplikacím back-endové služby připojit se k centru IoT hub za účelem spuštění metod a načtení zpráv. Následující příkaz načte připojovací řetězec služby pro vaše centrum IoT:

**YourIoTHubName**: Nahraďte tento zástupný symbol níže názvem, který jste vybrali pro svůj centr IoT.

```azurecli-interactive
az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
```

Poznamenejte si připojovací řetězec služby, který vypadá nějak takto:

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Tuto hodnotu použijete později v tomto rychlém startu. Tento připojovací řetězec služby se liší od připojovacího řetězce zařízení, který jste zaznamenali v předchozím kroku.

## <a name="listen-for-direct-method-calls"></a>Naslouchání voláním přímé metody

Obě ukázky pro tento rychlý start jsou součástí úložiště azure-iot-samples-java na GitHubu. Stáhněte nebo naklonujte úložiště [azure-iot-samples-java.](https://github.com/Azure-Samples/azure-iot-samples-java)

Ukázkovou aplikaci sady SDK zařízení lze spustit na fyzickém zařízení Android nebo emulátoru Android. Ukázka se připojí ke koncovému bodu specifickému pro zařízení ve vašem centru IoT hub, odešle simulovanou telemetrii a naslouchá volání přímé metody z vašeho centra. Volání přímé metody z centra v tomto rychlém startu nařídí zařízení, aby změnilo interval, ve kterém se odesílají telemetrická data. Simulované zařízení odešle potvrzení zpět do rozbočovače po spuštění přímé metody.

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

    ![Ukázkový snímek obrazovky aplikace android klientského zařízení](media/quickstart-control-device-android/sample-screenshot.png)

Tato aplikace musí být ponechána spuštěná na fyzickém zařízení nebo emulátoru při spuštění ukázky sady SDK služby k aktualizaci intervalu telemetrie za běhu.

## <a name="read-the-telemetry-from-your-hub"></a>Čtení telemetrických dat z centra

V této části použijete Azure Cloud Shell s [rozšířením IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) ke sledování zpráv, které jsou odesílány zařízením Android.

1. Pomocí služby Azure Cloud Shell spusťte následující příkaz, který provede připojení a čtení zpráv z centra IoT:

   **YourIoTHubName**: Nahraďte tento zástupný symbol níže názvem, který zvolíte pro svůj iot hub.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    Následující snímek obrazovky ukazuje výstup jako služba IoT hub přijímá telemetrii odeslanou zařízením Android:

      ![Čtení zpráv zařízení pomocí Azure CLI](media/quickstart-control-device-android/read-data.png)

Ve výchozím nastavení telemetrická aplikace odesílá telemetrická data ze zařízení Android každých pět sekund. V další části použijete volání přímé metody k aktualizaci intervalu telemetrie pro zařízení IoT android.

## <a name="call-the-direct-method"></a>Volání přímé metody

Aplikace služby se připojuje ke koncovému bodu na straně služby ve službě IoT Hub. Aplikace provádí volání přímé metody zařízení prostřednictvím služby IoT hub a naslouchá potvrzení.

Spusťte tuto aplikaci na samostatném fyzickém zařízení Android nebo emulátoru Android.

Back-endová aplikace služby IoT Hub se obvykle spouští v cloudu, kde je snazší zmírnit rizika spojená s citlivým připojovacím řetězcem, který řídí všechna zařízení v centru IoT Hub. V tomto příkladu jej spouštějíme jako aplikaci pro Android pouze pro demonstrační účely. Ostatní jazykové verze tohoto rychlého startu poskytují příklady, které lépe zarovnávají typické aplikace back-end služby.

1. Otevřete ukázkový projekt služby GitHub androida v Android Studiu. Projekt je umístěn v následujícím adresáři klonované nebo stažené kopie [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java) úložiště.

        \azure-iot-samples-java\iot-hub\Samples\service\AndroidSample

2. V Android Studio otevřete *gradle.properties* pro ukázkový projekt. Aktualizujte hodnoty vlastností **ConnectionString** a **DeviceId** pomocí dříve uvedeného připojovacího řetězce služby a ID zařízení Android, které jste zaregistrovali.

    ```
    ConnectionString=HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}
    DeviceId=MyAndroidDevice
    ```

3. Ve Studiu pro Android klikněte na Project pro synchronizaci **souborů** > **s gradle soubory**. Ověřte dokončení sestavení.

   > [!NOTE]
   > Pokud se synchronizace projektu nezdaří, může to být z jednoho z následujících důvodů:
   >
   > * Verze pluginu Android Gradle a Gradle odkazované v projektu jsou zastaralé pro vaši verzi Android Studia. Postupujte [podle těchto pokynů](https://developer.android.com/studio/releases/gradle-plugin) odkazovat a nainstalovat správné verze pluginu a Gradle pro vaši instalaci.
   > * Licenční smlouva pro android sdk nebyla podepsána. Podle pokynů ve výstupu sestavení podepište licenční smlouvu a stáhněte si sadu SDK.

4. Po dokončení sestavení klepněte na tlačítko **Spustit** > **spustit 'aplikaci'**. Nakonfigurujte aplikaci tak, aby běžela na samostatném fyzickém zařízení Android nebo emulátoru Androidu. Další informace o spuštění aplikace pro Android na fyzickém zařízení nebo emulátoru najdete v tématu [Spuštění aplikace](https://developer.android.com/training/basics/firstapp/running-app).

5. Po načtení aplikace aktualizujte hodnotu **Nastavit interval zasílání zpráv** na **1000** a klepněte na **tlačítko Vyvolat**.

    Interval zasílání telemetrických zpráv je v milisekundách. Výchozí interval telemetrie vzorku zařízení je nastaven na 5 sekund. Tato změna aktualizuje zařízení IoT Android tak, aby telemetrie je odeslána každou sekundu.

    ![Zadat interval telemetrie](media/quickstart-control-device-android/enter-telemetry-interval.png)

6. Aplikace obdrží potvrzení označující, zda metoda byla úspěšně provedena nebo ne.

    ![Potvrzení přímé metody](media/quickstart-control-device-android/direct-method-ack.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste volali přímou metodu na zařízení z back-endové aplikace a odpověděli na volání přímé metody v aplikaci simulované zařízení.

Informace o tom, jak směrovat zprávy typu zařízení-cloud do různých cílů v cloudu, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Kurz: Směrování telemetrických dat do různých koncových bodů za účelem zpracování](tutorial-routing.md)
