---
title: Řízení zařízení z rychlého startu Azure IoT Hub (Android) | Dokumentace Microsoftu
description: V tomto rychlém startu spustíte dvě ukázkové aplikace Java. Jedna aplikace je aplikace služby, která můžete vzdáleně ovládat zařízení připojené k vašemu centru. Jiné aplikace bude spuštěna na fyzické nebo simulované zařízení připojeném k centru dá řídit vzdáleně.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/19/2018
ms.author: wesmc
ms.openlocfilehash: 28884b9b7d29a3c8da1fee0f0b54269bdaadf926
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52427895"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-android"></a>Rychlý start: Řízení zařízení připojená ke službě IoT hub (Android)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub je služba Azure, která umožňuje ingestovat velké objemy telemetrických dat ze zařízení IoT do cloudu a spravovat zařízení z cloudu. V tomto rychlém startu použijete *přímou metodu* k řízení simulovaného zařízení připojeného k centru IoT. Přímé metody můžete použít k provádění vzdálených změn chování zařízení připojeného k centru IoT.

Rychlý start používá dvě předem napsané aplikace Java:

* Aplikace simulovaného zařízení, která bude reagovat na přímé metody volat z aplikace back-end služby. Aby bylo možné přijímat volání přímé metody, připojí se tato aplikace ke koncovému bodu centra IoT pro konkrétní zařízení.

* Aplikace služby, která volá metodu s přímým přístupem na zařízení s Androidem. Aby na zařízení bylo možné volat přímou metodu, připojí se tato aplikace ke koncovému bodu vašeho centra IoT na straně služby.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky


* Android studio z https://developer.android.com/studio/. Další informace týkající se instalace sady Android Studio najdete v tématu [android instalace](https://developer.android.com/studio/install). 

* 27. Android SDK se používá v rámci ukázky v tomto článku. 

* Podle tohoto rychlého startu jsou nutné dvě ukázkové aplikace: [sady SDK pro zařízení ukázkové aplikace pro Android](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample) a [sady SDK služby ukázkové aplikace pro Android](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/service/AndroidSample). Obě tyto vzorků, které jsou součástí sady azure-iot-samples-java úložišti na Githubu. Stáhněte nebo naklonujte [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java) úložiště.


## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Pokud jste dokončili předchozí [rychlý start: odesílání telemetrických dat ze zařízení do služby IoT hub](quickstart-send-telemetry-android.md), můžete tento krok přeskočit a pomocí služby IoT hub jste už vytvořili.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrování zařízení

Pokud jste dokončili předchozí [rychlý start: odesílání telemetrických dat ze zařízení do služby IoT hub](quickstart-send-telemetry-android.md), můžete tento krok přeskočit a použít stejné zařízení registrovaná v předchozím rychlém startu.

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell.

1. Ve službě Azure Cloud Shell spusťte následující příkazy pro přidání rozšíření rozhraní příkazového řádku IoT Hub a vytvoření identity zařízení. 

   **YourIoTHubName**: Nahraďte tento zástupný text pod názvem jste zvolili pro službu IoT hub.

   **MyAndroidDevice**: Tato hodnota je název pro registrovaná zařízení. Použijte MyAndroidDevice, jak je znázorněno. Pokud zvolíte jiný název pro vaše zařízení, budete také muset použít tento název v rámci tohoto článku a aktualizujte název zařízení v ukázkové aplikace před spuštěním je.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create \
      --hub-name YourIoTHubName --device-id MyAndroidDevice
    ```

2. Spuštěním následujícího příkazu ve službě Azure Cloud Shell získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:

   **YourIoTHubName**: Tento zástupný text pod nahraďte název, který zvolíte pro službu IoT hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name YourIoTHubName \
      --device-id MyAndroidDevice \
      --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá nějak takto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v tomto rychlém startu.

## <a name="retrieve-the-service-connection-string"></a>Načtení připojovacího řetězce služby

Budete také potřebovat _připojovací řetězec služby_ umožnit aplikacím back endové služby pro připojení ke službě IoT hub, aby bylo možné spouštět metody a načítání zpráv. Následující příkaz načte připojovací řetězec služby pro vaše centrum IoT:
   
**YourIoTHubName**: Nahraďte tento zástupný text pod názvem jste zvolili pro službu IoT hub.

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIoTHubName --output table
```

Poznamenejte si připojovací řetězec služby, který vypadá nějak takto:

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={YourSharedAccessKey}`

Tuto hodnotu použijete později v tomto rychlém startu. Připojovací řetězec služby se liší od připojovacího řetězce zařízení.

## <a name="listen-for-direct-method-calls"></a>Naslouchání voláním přímé metody

Zařízení SDK ukázkovou aplikaci můžete spustit na fyzické zařízení s Androidem nebo emulátoru Androidu. Ukázka připojit ke koncový bod specifický pro zařízení ve službě IoT hub, odesílá Simulovaná telemetrická data a čeká na přímé metody volání rozbočovače. Volání přímé metody z centra v tomto rychlém startu nařídí zařízení, aby změnilo interval, ve kterém se odesílají telemetrická data. Simulované zařízení odešle po spuštění přímé metody zpět do centra potvrzení.

1. Otevřete ukázkový projekt Android githubu v nástroji Android Studio. Projekt se nachází v následujícím adresáři klonované nebo stažených kopie [azure-iot ukázka java](https://github.com/Azure-Samples/azure-iot-samples-java) úložiště.

        \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample

2. V nástroji Android Studio, otevřete *gradle.properties* pro ukázkového projektu a nahraďte **Device_Connection_String** zástupného symbolu připojovacím řetězcem zařízení jste si předtím poznamenali.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. V nástroji Android Studio, klikněte na tlačítko **souboru** > **synchronizovat projekt se soubory Gradle**. Ověřte, že dokončení sestavení.

4. Po dokončení sestavení kliknutím na **spustit** > **spuštění "aplikace"**. Konfigurace aplikace pro spuštění na fyzické zařízení s Androidem nebo emulátoru Androidu. Další informace o spouštění aplikace pro Android na fyzické zařízení nebo emulátoru najdete v tématu [spuštění aplikace](https://developer.android.com/training/basics/firstapp/running-app).

5. Jakmile aplikace načte, klikněte na tlačítko **Start** tlačítko Zahájit odesílání telemetrických dat do služby IoT Hub:

    ![Aplikace](media/quickstart-send-telemetry-android/sample-screenshot.png)

Tato aplikace musí zůstat spuštěné na phycial zařízení nebo emulátoru při spuštění služby SDK ukázku aktualizovat interval telemetrie za běhu.


## <a name="read-the-telemetry-from-your-hub"></a>Čtení telemetrických dat z centra

V této části budete používat Azure Cloud Shell s [rozšíření IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) k monitorování zařízení zprávy, které jsou odesílány zařízení s Androidem.

1. Pomocí služby Azure Cloud Shell spusťte následující příkaz, který provede připojení a čtení zpráv z centra IoT:

   **YourIoTHubName**: Tento zástupný text pod nahraďte název, který zvolíte pro službu IoT hub.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```
    Na následujícím snímku obrazovky se zobrazí výstup jako IoT hub přijímá telemetrická data odesílaná zařízení s Androidem:

      ![Čtení zpráv zařízení pomocí Azure CLI](media/quickstart-send-telemetry-android/read-data.png)

Ve výchozím nastavení telemetrie aplikace odesílá telemetrická data ze zařízení s Androidem každých 5 sekund. V další části použijete volání přímé metody aktualizovat interval telemetrie pro zařízení s Androidem IoT.


## <a name="call-the-direct-method"></a>Volání přímé metody

Aplikace služby se připojí ke koncovému bodu straně služby ve službě IoT Hub. Aplikace provádí volání přímé metody na zařízení prostřednictvím centra IoT a čeká na potvrzení. 

Spuštění této aplikace na samostatné fyzické zařízení s Androidem nebo emulátoru Androidu.

Aplikace back-end služby IoT Hub se obvykle běží v cloudu, kde se snadněji zmírnit rizika spojená s citlivé připojovací řetězec, který řídí všechna zařízení ve službě IoT Hub. V tomto příkladu jsme spustili ji aplikace pro Android pouze pro demonstrační účely. Jiné jazykové verze v tomto rychlém startu poskytují další příklady, které odpovídají lépe aplikace back-end služby. 

1. Otevřete github služby ukázkový projekt pro Android v nástroji Android Studio. Projekt se nachází v následujícím adresáři klonované nebo stažených kopie [azure-iot ukázka java](https://github.com/Azure-Samples/azure-iot-samples-java) úložiště.

        \azure-iot-samples-java\iot-hub\Samples\service\AndroidSample

2. V nástroji Android Studio, otevřete *gradle.properties* pro ukázku projekt a aktualizujte hodnotu **ConnectionString** a **DeviceId** vlastnosti s vaším připojením služby řetězec, který jste si předtím poznamenali a zaregistrujete zařízení s Androidem ID.

    ```
    ConnectionString=HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={YourSharedAccessKey}
    DeviceId=MyAndroidDevice    
    ```

3. V nástroji Android Studio, klikněte na tlačítko **souboru** > **synchronizovat projekt se soubory Gradle**. Ověřte, že dokončení sestavení.

4. Po dokončení sestavení kliknutím na **spustit** > **spuštění "aplikace"**. Konfigurace aplikace pro spuštění na samostatné fyzické zařízení s Androidem nebo emulátoru Androidu. Další informace o spouštění aplikace pro Android na fyzické zařízení nebo emulátoru najdete v tématu [spuštění aplikace](https://developer.android.com/training/basics/firstapp/running-app).

5. Jakmile aplikace načte, aktualizujte **nastavit Interval pro zasílání zpráv** hodnota, která se **1000** a klikněte na tlačítko **Invoke**. 

    Zasílání zpráv interval telemetrie TH je uvedena v milisekundách. Výchozí interval telemetrie zařízení vzorku nastaven po dobu 5 sekund. Tato změna bude aktualizovat zařízení s Androidem IoT tak, aby se odesílají telemetrická data za sekundu.

    ![Zadejte interval telemetrie](media/quickstart-control-device-android/enter-telemetry-interval.png)

6. Aplikace se zobrazí potvrzení určující, zda metoda proveden úspěšně, nebo ne.

    ![Potvrzení přímé metody](media/quickstart-control-device-android/direct-method-ack.png)



## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu volat přímé metody v zařízení z aplikace back-end a přidal odpověď na volání přímé metody v aplikaci simulovaného zařízení.

Informace o tom, jak směrovat zprávy typu zařízení-cloud do různých cílů v cloudu, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Kurz: Směrování telemetrických dat do různých koncových bodů za účelem zpracování](tutorial-routing.md)