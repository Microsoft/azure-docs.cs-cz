---
title: Rychlý Start – odeslání telemetrie do služby Azure IoT Hub Starter (C) | Microsoft Docs
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
- 'Role: Cloud Development'
- devx-track-azurecli
ms.date: 04/10/2019
ms.author: wesmc
ms.openlocfilehash: aad28c704ba8c46f4c2d0111e545dc4d2381250d
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201936"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-c"></a>Rychlý Start: odeslání telemetrie ze zařízení do služby IoT Hub a její čtení pomocí back-endové aplikace (C)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub je služba Azure, která umožňuje ingestovat velké objemy telemetrických dat ze zařízení IoT do cloudu pro účely uložení nebo zpracování. V tomto rychlém startu odešlete telemetrická data z aplikace simulovaného zařízení prostřednictvím služby IoT Hub do back-endové aplikace za účelem zpracování.

V rychlém startu se používá k odesílání telemetrických dat do centra IoT ukázková aplikace C ze [sady SDK pro zařízení Azure IoT pro jazyk C](iot-hub-device-sdk-c-intro.md). Sady SDK pro zařízení Azure IoT jsou kvůli přenositelnosti a široké kompatibilitě platforem napsané v [ANSI C (C99)](https://wikipedia.org/wiki/C99). Před spuštěním ukázkového kódu vytvoříte centrum IoT a zaregistrujete simulované zařízení v tomto centru.

Tento článek je napsán pro systém Windows, ale tento rychlý Start můžete dokončit i v systému Linux.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Nainstalujte [Visual Studio 2019](https://www.visualstudio.com/vs/) se zapnutou úlohou [vývoj desktopových aplikací pomocí C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) .

* Nainstalujte nejnovější verzi [Git](https://git-scm.com/download/).

* Ujistěte se, že je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto rychlém startu používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokovaný v některých podnikových a vzdělávacích prostředích sítě. Další informace a způsoby, jak tento problém obejít, najdete v tématu [připojení k IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

V tomto rychlém startu budete používat [sadu SDK pro zařízení Azure IoT pro jazyk C](iot-hub-device-sdk-c-intro.md). 

V následujících prostředích můžete sadu SDK použít tak, že nainstalujete tyto balíčky a knihovny:

* **Linux**: apt-get Packages jsou k dispozici pro Ubuntu 16,04 a 18,04 pomocí následujících architektur procesoru: AMD64, arm64, armhf a i386. Další informace najdete v tématu o [vytvoření projektu klienta zařízení jazyka C na Ubuntu pomocí apt-get](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/ubuntu_apt-get_sample_setup.md).

* **mbed**: pro vývojáře, kteří vytvářejí aplikace zařízení na platformě mbed, jsme publikovali knihovnu a ukázky, které vám pomůžou začít používat Azure IoT Hub v řádu minut. Další informace najdete v tématu o [použití knihovny mbed](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#mbed).

* **Arduino**: Pokud vyvíjíte na Arduino, můžete využít knihovnu Azure IoT dostupnou ve Správci knihovny IDE Arduino. Další informace najdete v tématu o [knihovně Azure IoT Hub pro Arduino](https://github.com/azure/azure-iot-arduino).

* **iOS**: Sada SDK pro zařízení IoT Hub je k dispozici jako CocoaPods pro vývoj pro zařízení se systémy Mac a iOS. Další informace najdete v [ukázkách pro iOS pro Microsoft Azure IoT](https://cocoapods.org/pods/AzureIoTHubClient).

V tomto rychlém startu ale připravíte vývojové prostředí, které se používá k klonování a sestavení [sady Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) z GitHubu. Ukázkový kód, který se používá v tomto rychlém startu, je součástí sady SDK na GitHubu.

1. Stáhněte si [sestavovací systém cmake](https://cmake.org/download/).

    Je důležité, aby požadavky na sadu Visual Studio (Visual Studio a sada funkcí Vývoj desktopových aplikací pomocí C++) byly na vašem počítači nainstalované ještě **před** zahájením instalace `CMake`. Jakmile jsou požadované součásti k dispozici a stažený soubor je ověřený, nainstalujte sestavovací systém CMake.

2. Vyhledejte název značky pro [nejnovější verzi](https://github.com/Azure/azure-iot-sdk-c/releases/latest) sady SDK.

3. Otevřete prostředí příkazového řádku nebo Git Bash. Spuštěním následujících příkazů naklonujte nejnovější verzi úložiště GitHub pro [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) . Použijte značku, kterou jste našli v předchozím kroku, jako hodnotu `-b` parametru:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.

4. V kořenovém adresáři úložiště Git vytvořte podadresář `cmake` a přejděte do této složky. Z adresáře spusťte následující příkazy `azure-iot-sdk-c` :

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Spusťte následující příkaz, který sestaví verzi sady SDK specifickou pro vaši vývojovou platformu vašeho klienta. V adresáři `cmake` se vygeneruje řešení Visual Studia pro simulované zařízení.

    ```cmd
    cmake ..
    ```

    Pokud nenajde `cmake` kompilátor jazyka C++, může při spuštění výše uvedeného příkazu dojít k chybám sestavení. Pokud k tomu dojde, zkuste tento příkaz spustit v [příkazovém řádku sady Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs). 

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

## <a name="register-a-device"></a>Registrace zařízení

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V této části použijete Azure Cloud Shell s [rozšířením IoT](/cli/azure/ext/azure-iot/iot) k registraci simulovaného zařízení.

1. Spuštěním následujícího příkazu v Azure Cloud Shell vytvořte identitu zařízení.

   **YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který jste zvolili pro Centrum IoT.

   **MyCDevice**: Toto je název zařízení, které registrujete. Doporučuje se používat **MyCDevice** , jak je znázorněno na obrázku. Pokud pro vaše zařízení zvolíte jiný název, budete ho muset použít i v celém rámci tohoto článku a před jeho spuštěním aktualizovat název zařízení v ukázkových aplikacích.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyCDevice
    ```

2. Spuštěním následujícího příkazu v Azure Cloud Shell Získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:

   **YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který jste zvolili pro Centrum IoT.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyCDevice --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá takto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyCDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v rychlém startu.

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

    Hodnotu konstanty nahraďte `connectionString` připojovacím řetězcem zařízení, který jste si poznamenali dříve. Pak uložte změny do souboru **iothub_convenience_sample.c**.

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

## <a name="read-the-telemetry-from-your-hub"></a>Čtení telemetrie z centra

V této části použijete Azure Cloud Shell s [rozšířením IoT](/cli/azure/ext/azure-iot/iot) , abyste mohli monitorovat zprávy zařízení odesílané simulovaným zařízením.

1. Pomocí služby Azure Cloud Shell spusťte následující příkaz, který provede připojení a čtení zpráv z centra IoT:

   **YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který zvolíte pro Centrum IoT.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    ![Čtení zpráv zařízení pomocí Azure CLI](media/quickstart-send-telemetry-c/read-device-to-cloud-messages-app.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu nastavíte centrum IoT, zaregistrovali zařízení, poslali simulovanou telemetrii do centra pomocí aplikace v jazyce C a načetli telemetrii z centra pomocí Azure Cloud Shell.

Další informace o vývoji pomocí sady Azure IoT Hub C SDK najdete v následujícím průvodci:

> [!div class="nextstepaction"]
> [Vývoj pomocí sady Azure IoT Hub C SDK](iot-hub-devguide-develop-for-constrained-devices.md)
