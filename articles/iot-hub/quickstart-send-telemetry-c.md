---
title: Rychlý start k odesílání telemetrických dat do služby Azure IoT Hub (C) | Microsoft Docs
description: V tomto rychlém startu spustíte dvě ukázkové aplikace C, které odesílají simulovaná telemetrická data do centra IoT a čtou telemetrická data z centra IoT, aby se mohla zpracovávat v cloudu.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
ms.date: 04/10/2019
ms.author: wesmc
ms.openlocfilehash: 46dc5298337b2ee0d16d22ec6ab24cb9d751fc38
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769182"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-c"></a>Úvodní příručka: Odeslání telemetrie ze zařízení do centra IoT hubu a její čtení pomocí back-endové aplikace (C)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub je služba Azure, která umožňuje ingestovat velké objemy telemetrických dat ze zařízení IoT do cloudu pro účely uložení nebo zpracování. V tomto rychlém startu odešlete telemetrická data z aplikace simulovaného zařízení prostřednictvím služby IoT Hub do back-endové aplikace za účelem zpracování.

V rychlém startu se používá k odesílání telemetrických dat do centra IoT ukázková aplikace C ze [sady SDK pro zařízení Azure IoT pro jazyk C](iot-hub-device-sdk-c-intro.md). Sady SDK pro zařízení Azure IoT jsou kvůli přenositelnosti a široké kompatibilitě platforem napsané v [ANSI C (C99)](https://wikipedia.org/wiki/C99). Před spuštěním ukázkového kódu vytvoříte centrum IoT a zaregistrujete simulované zařízení v tomto centru.

Tento článek je napsán pro Windows, ale můžete dokončit tento rychlý start na Linuxu také.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

* Nainstalujte [Visual Studio 2019](https://www.visualstudio.com/vs/) s povoleným [zatížením "Vývoj plochy s C++".](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/)

* Nainstalujte nejnovější verzi [Git](https://git-scm.com/download/).

* Zkontrolujte, zda je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto rychlém startu používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokován v některých prostředích podnikové a vzdělávací sítě. Další informace a způsoby, jak tento problém vyřešit, najdete [v tématu připojení k centru IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).


* Spusťte následující příkaz a přidejte rozšíření Microsoft Azure IoT extension pro Azure CLI do instance Cloud Shellu. Rozšíření IoT přidá do rozhraní příkazového příkazu Azure CLI specifické pro služby IoT Hub, IoT Edge a Služby zřizování zařízení IoT (DPS).

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

Pro tento rychlý start budete používat [Azure IoT zařízení SDK pro C](iot-hub-device-sdk-c-intro.md). 

V následujících prostředích můžete sdk použít instalací těchto balíčků a knihoven:

* **Linux**: apt-get balíčky jsou k dispozici pro Ubuntu 16.04 a 18.04 pomocí následujících architektur CPU: amd64, arm64, armhf a i386. Další informace najdete v tématu o [vytvoření projektu klienta zařízení jazyka C na Ubuntu pomocí apt-get](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/ubuntu_apt-get_sample_setup.md).

* **mbed**: Pro vývojáře, kteří vytvářejí aplikace zařízení na platformě mbed, jsme publikovali knihovnu a ukázky, které vám pomohou začít během několika minut witH Azure IoT Hub. Další informace najdete v tématu o [použití knihovny mbed](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#mbed).

* **Arduino**: Pokud vyvíjíte na Arduino, můžete využít knihovnu Azure IoT k dispozici ve správci knihovny Arduino IDE. Další informace najdete v tématu o [knihovně Azure IoT Hub pro Arduino](https://github.com/azure/azure-iot-arduino).

* **iOS**: Sada SDK pro zařízení IoT Hub je k dispozici jako CocoaPods pro vývoj pro zařízení se systémy Mac a iOS. Další informace najdete v [ukázkách pro iOS pro Microsoft Azure IoT](https://cocoapods.org/pods/AzureIoTHubClient).

V tomto rychlém startu však připravíte vývojové prostředí, které se používá ke klonování a vytváření [sady Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) z GitHubu. Ukázkový kód, který se používá v tomto rychlém startu, je součástí sady SDK na GitHubu.

1. Stáhněte si [systém sestavení CMake](https://cmake.org/download/).

    Je důležité, aby požadavky na sadu Visual Studio (Visual Studio a sada funkcí Vývoj desktopových aplikací pomocí C++) byly na vašem počítači nainstalované ještě **před** zahájením instalace `CMake`. Jakmile jsou požadované součásti k dispozici a stažený soubor je ověřený, nainstalujte sestavovací systém CMake.

2. Vyhledejte název značky pro [nejnovější verzi](https://github.com/Azure/azure-iot-sdk-c/releases/latest) sady SDK.

3. Otevřete prostředí příkazového řádku nebo Git Bash. Spusťte následující příkazy a naklonovat nejnovější verzi úložiště [GitHub Azure IoT C SDK.](https://github.com/Azure/azure-iot-sdk-c) Jako hodnotu `-b` parametru použijte značku, kterou jste našli v předchozím kroku:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.

4. V kořenovém adresáři úložiště Git vytvořte podadresář `cmake` a přejděte do této složky. Spusťte z adresáře následující příkazy: `azure-iot-sdk-c`

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Spusťte následující příkaz a vytvořte verzi sady SDK specifickou pro vývojovou klientskou platformu. V adresáři `cmake` se vygeneruje řešení Visual Studia pro simulované zařízení.

    ```cmd
    cmake ..
    ```

    Pokud `cmake` nenajde kompilátor Jazyka C++, může dojít k chybám sestavení při spuštění výše uvedeného příkazu. Pokud k tomu dojde, zkuste tento příkaz spustit v [příkazovém řádku sady Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

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

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V této části použijete Azure Cloud Shell s [rozšířením IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) k registraci simulované zařízení.

1. Spusťte následující příkaz v Azure Cloud Shell a vytvořte identitu zařízení.

   **YourIoTHubName**: Nahraďte tento zástupný symbol níže názvem, který jste vybrali pro svůj centr IoT.

   **MyCDevice**: Toto je název zařízení, které registrujete. Doporučuje se používat **MyCDevice,** jak je znázorněno. Pokud pro své zařízení zvolíte jiný název, budete muset tento název použít v celém tomto článku a aktualizovat název zařízení v ukázkových aplikacích před jejich spuštěním.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyCDevice
    ```

2. Spusťte následující příkaz v Prostředí Azure Cloud Shell a získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:

   **YourIoTHubName**: Nahraďte tento zástupný symbol níže názvem, který jste vybrali pro svůj centr IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyCDevice --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá nějak takto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyCDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v rychlém startu.

## <a name="send-simulated-telemetry"></a>Odesílání simulovaných telemetrických dat

Aplikace simulovaného zařízení se připojí ke koncovému bodu vašeho centra IoT pro konkrétní zařízení a odesílá simulovaná telemetrická data v podobě řetězce.

1. Pomocí textového editoru otevřete zdrojový soubor iothub_convenience_sample.c a projděte si ukázkový kód pro odesílání telemetrických dat. Soubor se nachází v následujícím umístění pod pracovním adresářem, kde jste naklonovali sadu Azure IoT C SDK:

    ```
    azure-iot-sdk-c\iothub_client\samples\iothub_convenience_sample\iothub_convenience_sample.c
    ```

2. Vyhledejte deklaraci konstanty `connectionString`:

    ```C
    /* Paste in your device connection string  */
    static const char* connectionString = "[device connection string]";
    ```

    Nahraďte hodnotu `connectionString` konstanty připojovacím řetězcem zařízení, který jste si dříve poznamenali. Pak uložte změny do souboru **iothub_convenience_sample.c**.

3. V okně místního terminálu přejděte v adresáři CMake do adresáře projektu *iothub_convenience_sample*, který jste vytvořili v sadě Azure IoT C SDK. Zadejte z pracovního adresáře následující příkaz:

    ```cmd/sh
    cd azure-iot-sdk-c/cmake/iothub_client/samples/iothub_convenience_sample
    ```

4. V okně místního terminálu spusťte nástroj CMake a sestavte ukázku s použitím aktualizované hodnoty `connectionString`:

    ```cmd/sh
    cmake --build . --target iothub_convenience_sample --config Debug
    ```

5. V místním okně terminálu spusťte následující příkaz a spusťte aplikaci simulované zařízení:

    ```cmd/sh
    Debug\iothub_convenience_sample.exe
    ```

    Následující snímek obrazovky ukazuje výstup, zatímco aplikace simulovaného zařízení odesílá telemetrická data do centra IoT:

    ![Spuštění simulovaného zařízení](media/quickstart-send-telemetry-c/simulated-device-app.png)

## <a name="read-the-telemetry-from-your-hub"></a>Čtení telemetrických dat z centra

V této části budete používat Azure Cloud Shell s [rozšířením IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) ke sledování zpráv zařízení, které jsou odesílány simulovaným zařízením.

1. Pomocí služby Azure Cloud Shell spusťte následující příkaz, který provede připojení a čtení zpráv z centra IoT:

   **YourIoTHubName**: Nahraďte tento zástupný symbol níže názvem, který zvolíte pro svůj iot hub.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    ![Čtení zpráv zařízení pomocí Azure CLI](media/quickstart-send-telemetry-c/read-device-to-cloud-messages-app.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu nastavíte službu IoT hub, zaregistrujete zařízení, odešlete simulovanou telemetrii do centra pomocí aplikace C a načtete telemetrickou metu z centra pomocí prostředí Azure Cloud Shell.

Další informace o vývoji pomocí sady Azure IoT Hub C SDK najdete v následujícím průvodci:

> [!div class="nextstepaction"]
> [Vývoj pomocí sady Azure IoT Hub C SDK](iot-hub-devguide-develop-for-constrained-devices.md)
