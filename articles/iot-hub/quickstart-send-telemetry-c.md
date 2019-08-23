---
title: Rychlý start k odesílání telemetrických dat do služby Azure IoT Hub (C) | Microsoft Docs
description: V tomto rychlém startu spustíte dvě ukázkové aplikace C, které odesílají simulovaná telemetrická data do centra IoT a čtou telemetrická data z centra IoT, aby se mohla zpracovávat v cloudu.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/10/2019
ms.author: wesmc
ms.openlocfilehash: 842efca1b40827f63ab23581aeac7e5226d04349
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900287"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-c"></a>Rychlý start: Odeslání telemetrie ze zařízení do služby IoT Hub a jejich čtení pomocí back-endové aplikace (C)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub je služba Azure, která umožňuje ingestovat velké objemy telemetrických dat ze zařízení IoT do cloudu pro účely uložení nebo zpracování. V tomto rychlém startu odešlete telemetrická data z aplikace simulovaného zařízení prostřednictvím služby IoT Hub do back-endové aplikace za účelem zpracování.

V rychlém startu se používá k odesílání telemetrických dat do centra IoT ukázková aplikace C ze [sady SDK pro zařízení Azure IoT pro jazyk C](iot-hub-device-sdk-c-intro.md). Sady SDK pro zařízení Azure IoT jsou kvůli přenositelnosti a široké kompatibilitě platforem napsané v [ANSI C (C99)](https://wikipedia.org/wiki/C99). Před spuštěním ukázkového kódu vytvoříte centrum IoT a zaregistrujete simulované zařízení v tomto centru.

Tento článek je napsaný pro Windows, ale tento rychlý start se dá použít i pro Linux.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Nainstalujte [Visual Studio 2019](https://www.visualstudio.com/vs/) s povoleným pracovním vytížením pro [desktopovou vývoj C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) .
* Nainstalujte nejnovější verzi [Git](https://git-scm.com/download/).
* Spuštěním následujícího příkazu přidejte do instance služby Cloud Shell Microsoft Azure rozšíření IoT pro rozhraní příkazového řádku Azure. Rozšíření IOT přidá do Azure CLI příkazy určené pro služby IoT Hub, IoT Edge a IoT Device Provisioning Service (DPS).

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

V tomto rychlém startu budete používat [sadu SDK pro zařízení Azure IoT pro jazyk C](iot-hub-device-sdk-c-intro.md). 

Tuto sadu SDK můžete používat, když nainstalujete tyto balíčky a knihovny pro následující prostředí:

* **Linux**: apt-get Packages jsou k dispozici pro Ubuntu 16,04 a 18,04 pomocí následujících architektur procesoru: AMD64, arm64, armhf a i386. Další informace najdete v tématu o [vytvoření projektu klienta zařízení jazyka C na Ubuntu pomocí apt-get](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/ubuntu_apt-get_sample_setup.md).

* **mbed**: Pro vývojáře, kteří vytvářejí aplikace zařízení na platformě mbed, jsme publikovali knihovnu a ukázky, které vám pomůžou začít používat Azure IoT Hub v řádu minut. Další informace najdete v tématu o [použití knihovny mbed](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#mbed).

* **Arduino**: Pokud vyvíjíte na Arduino, můžete využít knihovnu Azure IoT dostupnou ve Správci knihovny IDE Arduino. Další informace najdete v tématu o [knihovně Azure IoT Hub pro Arduino](https://github.com/azure/azure-iot-arduino).

* **iOS**: Sada IoT Hub SDK pro zařízení je k dispozici jako CocoaPods pro vývoj zařízení s Mac a iOS. Další informace najdete v [ukázkách pro iOS pro Microsoft Azure IoT](https://cocoapods.org/pods/AzureIoTHubClient).

V tomto rychlém startu ale připravíte vývojové prostředí, které se používá k klonování a sestavení [sady Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) z GitHubu. Ukázkový kód, který se používá v tomto rychlém startu, je součástí sady SDK na GitHubu.

1. Stáhněte si [sestavovací systém cmake](https://cmake.org/download/).

    **Před** zahájením `CMake` instalace je důležité, abyste na svém počítači nainstalovali požadavky sady Visual Studio ( C++Visual Studio a Desktop Development with). Jakmile jsou požadované součásti k dispozici a stažený soubor je ověřený, nainstalujte sestavovací systém CMake.

2. Otevřete příkazový řádek nebo prostředí Git bash a přejděte do pracovního adresáře, do kterého chcete klonovat sadu Azure IoT C SDK. Spusťte následující příkaz pro naklonování úložiště GitHub sady [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c):

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.

3. V kořenovém adresáři úložiště Git vytvořte podadresář `cmake` a přejděte do této složky. Do pracovního adresáře zadejte následující příkazy:

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Spusťte následující příkaz, který sestaví verzi sady SDK specifickou pro vaši vývojovou platformu vašeho klienta. V adresáři `cmake` se vygeneruje řešení Visual Studia pro simulované zařízení.

    ```cmd
    cmake ..
    ```

    Pokud `cmake` nenajde váš C++ kompilátor, může při spuštění výše uvedeného příkazu dojít k chybám sestavení. Pokud k tomu dojde, zkuste tento příkaz spustit v [příkazovém řádku sady Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Po úspěšném sestavení by posledních pár řádků výstupu mělo vypadat přibližně takto:

    ```cmd/sh
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrování zařízení

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V této části použijete Azure Cloud Shell s [rozšířením IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) k registraci simulovaného zařízení.

1. Spuštěním následujícího příkazu v Azure Cloud Shell vytvořte identitu zařízení.

   **YourIoTHubName**: Nahraďte tento zástupný symbol níže názvem, který zvolíte pro Centrum IoT.

   **MyCDevice**: Toto je název zadaný pro registrované zařízení. Použijte uvedený název MyCDevice. Pokud pro vaše zařízení zvolíte jiný název, budete ho muset použít i v celém rámci tohoto článku a před jeho spuštěním aktualizovat název zařízení v ukázkových aplikacích.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyCDevice
    ```

2. Spuštěním následujících příkazů v Azure Cloud Shell Získejte _připojovací řetězec zařízení_ pro zařízení, které jste zaregistrovali:

   **YourIoTHubName**: Nahraďte tento zástupný symbol níže názvem, který zvolíte pro Centrum IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyCDevice --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá nějak takto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v tomto rychlém startu.

## <a name="send-simulated-telemetry"></a>Odesílání simulovaných telemetrických dat

Aplikace simulovaného zařízení se připojí ke koncovému bodu vašeho centra IoT pro konkrétní zařízení a odesílá simulovaná telemetrická data v podobě řetězce.

1. Pomocí textového editoru otevřete zdrojový soubor iothub_convenience_sample.c a projděte si ukázkový kód pro odesílání telemetrických dat. Soubor se nachází v následujícím umístění v pracovním adresáři, ve kterém jste naklonováni sadu Azure IoT C SDK:

    ```
    azure-iot-sdk-c\iothub_client\samples\iothub_convenience_sample\iothub_convenience_sample.c
    ```

2. Vyhledejte deklaraci konstanty `connectionString`:

    ```C
    /* Paste in your device connection string  */
    static const char* connectionString = "[device connection string]";
    ```

    Hodnotu konstanty `connectionString` nahraďte připojovacím řetězcem zařízení, který jste si předtím poznamenali. Pak uložte změny do souboru **iothub_convenience_sample.c**.

3. V okně místního terminálu přejděte v adresáři CMake do adresáře projektu *iothub_convenience_sample*, který jste vytvořili v sadě Azure IoT C SDK. Do pracovního adresáře zadejte tento příkaz:

    ```cmd/sh
    cd azure-iot-sdk-c/cmake/iothub_client/samples/iothub_convenience_sample
    ```

4. V okně místního terminálu spusťte nástroj CMake a sestavte ukázku s použitím aktualizované hodnoty `connectionString`:

    ```cmd/sh
    cmake --build . --target iothub_convenience_sample --config Debug
    ```

5. V místním okně terminálu spusťte následující příkaz, který spustí aplikaci simulovaného zařízení:

    ```cmd/sh
    Debug\iothub_convenience_sample.exe
    ```

    Následující snímek obrazovky ukazuje výstup, zatímco aplikace simulovaného zařízení odesílá telemetrická data do centra IoT:

    ![Spuštění simulovaného zařízení](media/quickstart-send-telemetry-c/simulated-device-app.png)

## <a name="read-the-telemetry-from-your-hub"></a>Čtení telemetrických dat z centra

V této části použijete Azure Cloud Shell s [rozšířením IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) , abyste mohli monitorovat zprávy zařízení odesílané simulovaným zařízením.

1. Pomocí služby Azure Cloud Shell spusťte následující příkaz, který provede připojení a čtení zpráv z centra IoT:

   **YourIoTHubName**: Nahraďte tento zástupný symbol níže názvem, který zvolíte pro Centrum IoT.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

    ![Čtení zpráv zařízení pomocí Azure CLI](media/quickstart-send-telemetry-c/read-device-to-cloud-messages-app.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste nastavili centrum IoT, zaregistrovali zařízení, odeslali simulovaná telemetrická data do centra pomocí aplikace C a přečetli telemetrická data z centra pomocí služby Azure Cloud Shell.

Další informace o vývoji pomocí sady Azure IoT Hub C SDK najdete v následujícím průvodci:

> [!div class="nextstepaction"]
> [Vývoj pomocí sady Azure IoT Hub C SDK](iot-hub-devguide-develop-for-constrained-devices.md)