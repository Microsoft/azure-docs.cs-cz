---
title: Rychlý start k odesílání telemetrických dat do služby Azure IoT Hub (C) | Microsoft Docs
description: V tomto rychlém startu spustíte dvě ukázkové aplikace C, které odesílají simulovaná telemetrická data do centra IoT a čtou telemetrická data z centra IoT, aby se mohla zpracovávat v cloudu.
author: wesmc7777
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/27/2018
ms.author: wesmc
ms.openlocfilehash: 77b76ac5b30c4f5f647c532dbc5db68b396b3d20
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2018
ms.locfileid: "45636137"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-the-telemetry-from-the-hub-with-a-back-end-application-c"></a>Rychlý start: Odesílání telemetrických dat ze zařízení do centra IoT a čtení telemetrických dat z centra pomocí back-endové aplikace (C)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub je služba Azure, která umožňuje ingestovat velké objemy telemetrických dat ze zařízení IoT do cloudu pro účely uložení nebo zpracování. V tomto rychlém startu odešlete telemetrická data z aplikace simulovaného zařízení prostřednictvím služby IoT Hub do back-endové aplikace za účelem zpracování.

V rychlém startu se používá k odesílání telemetrických dat do centra IoT ukázková aplikace C ze [sady SDK pro zařízení Azure IoT pro jazyk C](iot-hub-device-sdk-c-intro.md). Sady SDK pro zařízení Azure IoT jsou kvůli přenositelnosti a široké kompatibilitě platforem napsané v [ANSI C (C99)](https://wikipedia.org/wiki/C99). Před spuštěním ukázkového kódu vytvoříte centrum IoT a zaregistrujete simulované zařízení v tomto centru.

Tento článek je napsaný pro Windows, ale tento rychlý start se dá použít i pro Linux.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Nainstalujte [Visual Studio 2017](https://www.visualstudio.com/vs/) s povolenou sadou funkcí [Vývoj desktopových aplikací pomocí C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/).
* Nainstalujte nejnovější verzi [Git](https://git-scm.com/download/).

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

Pro účely tohoto rychlého startu budete používat [sadu SDK pro zařízení Azure IoT pro jazyk C](iot-hub-device-sdk-c-intro.md). 

Tuto sadu SDK můžete používat, když nainstalujete tyto balíčky a knihovny pro následující prostředí:

* **Linux**: Balíčky apt-get jsou k dispozici pro Ubuntu 16.04 a 18.04 pro tyto architektury procesoru: amd64, arm64, armhf a i386. Další informace najdete v tématu o [vytvoření projektu klienta zařízení jazyka C na Ubuntu pomocí apt-get](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/ubuntu_apt-get_sample_setup.md).

* **mbed**: Pro vývojáře vytvářející aplikace pro zařízení na platformě mbed jsme publikovali knihovnu a ukázky, které jim umožní začít používat Azure IoT Hub během několika minut. Další informace najdete v tématu o [použití knihovny mbed](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#mbed).

* **Arduino**: Při vývoji pro Arduino můžete využít knihovnu Azure IoT, která je k dispozici ve správci knihovny pro rozhraní Arduino IDE. Další informace najdete v tématu o [knihovně Azure IoT Hub pro Arduino](https://github.com/azure/azure-iot-arduino).

* **iOS**: Sada SDK pro zařízení IoT Hub je k dispozici jako CocoaPods pro vývoj pro zařízení se systémy Mac a iOS. Další informace najdete v [ukázkách pro iOS pro Microsoft Azure IoT](https://cocoapods.org/pods/AzureIoTHubClient).

V tomto rychlém startu však připravíte vývojové prostředí používané ke klonování a sestavení sady [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) z GitHubu. Ukázkový kód, který se používá v tomto rychlém startu, je součástí sady SDK na GitHubu. 


1. Stáhněte si verzi 3.11.4 [sestavovacího systému CMake](https://cmake.org/download/). Stažený binární soubor ověřte pomocí odpovídající kryptografické hodnoty hash. Následující příklad používá Windows PowerShell k ověření kryptografické hodnoty hash pro verzi 3.11.4 distribuce x64 MSI:

    ```PowerShell
    PS C:\Downloads> $hash = get-filehash .\cmake-3.11.4-win64-x64.msi
    PS C:\Downloads> $hash.Hash -eq "56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869"
    True
    ```
    
    V době psaní tohoto textu byly na webu CMake uvedené tyto hodnoty hash pro verzi 3.11.4:

    ```
    6dab016a6b82082b8bcd0f4d1e53418d6372015dd983d29367b9153f1a376435  cmake-3.11.4-Linux-x86_64.tar.gz
    72b3b82b6d2c2f3a375c0d2799c01819df8669dc55694c8b8daaf6232e873725  cmake-3.11.4-win32-x86.msi
    56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869  cmake-3.11.4-win64-x64.msi
    ```

    Je důležité, aby požadavky na sadu Visual Studio (Visual Studio a sada funkcí Vývoj desktopových aplikací pomocí C++) byly na vašem počítači nainstalované ještě **před** zahájením instalace `CMake`. Jakmile jsou požadované součásti k dispozici a stažený soubor je ověřený, nainstalujte sestavovací systém CMake.

2. Otevřete prostředí příkazového řádku nebo Git Bash. Spusťte následující příkaz pro naklonování úložiště GitHub sady [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c):
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    Velikost tohoto úložiště je aktuálně přibližně 220 MB. Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.


3. V kořenovém adresáři úložiště Git vytvořte podadresář `cmake` a přejděte do této složky. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Spuštěním následujícího příkazu sestavte verzi sady SDK určenou pro platformu vašeho vývojového klienta. V adresáři `cmake` se vygeneruje řešení Visual Studia pro simulované zařízení. 

    ```cmd
    cmake ..
    ```
    
    Pokud `cmake` nenajde váš kompilátor C++, můžou se při spuštění výše uvedeného příkazu zobrazit chyby sestavení. Pokud k tomu dojde, zkuste tento příkaz spustit v [příkazovém řádku sady Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

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

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>Registrování zařízení

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V této části zaregistrujete simulované zařízení pomocí Azure CLI s [rozšířením IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest).

1. Přidejte rozšíření rozhraní příkazového řádku IoT Hub a vytvořte identitu zařízení. `{YourIoTHubName}` nahraďte názvem, který jste vybrali pro centrum IoT:

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyCDevice
    ```

    Pokud si zvolíte jiný název zařízení, změňte ho také v ukázkových aplikacích, než je spustíte.

2. Spuštěním následujícího příkazu získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyCDevice --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá nějak takto: `Hostname=...=`. Tuto hodnotu použijete později v tomto rychlém startu.


## <a name="send-simulated-telemetry"></a>Odesílání simulovaných telemetrických dat

Aplikace simulovaného zařízení se připojí ke koncovému bodu vašeho centra IoT pro konkrétní zařízení a odesílá simulovaná telemetrická data v podobě řetězce.

1. Pomocí textového editoru otevřete zdrojový soubor iothub_convenience_sample.c a projděte si ukázkový kód pro odesílání telemetrických dat. Soubor se nachází v následujícím umístění:

    ```
    \azure-iot-sdk-c\iothub_client\samples\iothub_convenience_sample\iothub_convenience_sample.c
    ```

2. Vyhledejte deklaraci konstanty `connectionString`:

    ```C
    /* Paste in your device connection string  */
    static const char* connectionString = "[device connection string]";
    ```
    Hodnotu konstanty `connectionString` nahraďte připojovacím řetězcem zařízení, který jste si předtím poznamenali. Pak uložte změny do souboru **iothub_convenience_sample.c**.

3. V okně terminálu přejděte v adresáři CMake do adresáře projektu *iothub_convenience_sample*, který jste vytvořili v sadě Azure IoT C SDK.

    ```
    cd /azure-iot-sdk-c/cmake/iothub_client/samples/iothub_convenience_sample
    ```

4. Spusťte CMake pomocí následujícího příkazového řádku, aby se sestavila ukázka s aktualizovanou hodnotou `connectionString`:

    ```cmd/sh
    cmake --build . --target iothub_convenience_sample --config Debug
    ```

5. Spuštěním následujícího příkazu v příkazovém řádku spusťte aplikaci simulovaného zařízení:

    ```cmd/sh
    Debug\iothub_convenience_sample.exe
    ```

    Následující snímek obrazovky ukazuje výstup, zatímco aplikace simulovaného zařízení odesílá telemetrická data do centra IoT:

    ![Spuštění simulovaného zařízení](media/quickstart-send-telemetry-c/simulated-device-app.png)

## <a name="read-the-telemetry-from-your-hub"></a>Čtení telemetrických dat z centra


V této části budete pomocí Azure CLI s [rozšířením IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) monitorovat zprávy zařízení, které odesílá simulované zařízení.

1. Pomocí Azure CLI spusťte následující příkaz, který provede připojení a čtení zpráv z centra IoT:

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    ![Čtení zpráv zařízení pomocí Azure CLI](media/quickstart-send-telemetry-c/read-device-to-cloud-messages-app.png)

    

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nastavili centrum IoT, zaregistrovali zařízení, odeslali simulovaná telemetrická data do centra pomocí aplikace C a přečetli telemetrická data z centra pomocí Azure CLI.

Další informace o vývoji pomocí sady Azure IoT Hub C SDK najdete v následujícím průvodci:

> [!div class="nextstepaction"]
> [Vývoj pomocí sady Azure IoT Hub C SDK](iot-hub-devguide-develop-for-constrained-devices.md)