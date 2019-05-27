---
title: Komunikace na zařízení aplikaci v jazyce C prostřednictvím datových proudů zařízení Azure IoT Hub (preview) | Dokumentace Microsoftu
description: V tomto rychlém startu budete spouštět aplikace na straně služby jazyka C, který komunikuje s zařízení IoT prostřednictvím datového proudu zařízení.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: f5e6128c1ecceda181f92b2d81e9ac06effbfce2
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65834063"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Rychlý start: Komunikace na zařízení aplikaci v jazyce C prostřednictvím datových proudů zařízení služby IoT Hub (preview)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Microsoft Azure IoT Hub v současné době podporuje datové proudy zařízení jako [funkce ve verzi preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Datové proudy zařízení služby IoT Hub](iot-hub-device-streams-overview.md) povolit zařízení a služeb aplikacím komunikovat způsobem, firewallem procházející a zabezpečené. Ve verzi public preview je v SDK pro jazyk C podporuje pouze datové proudy zařízení na straně zařízení. Tento rychlý start v důsledku toho pokrývá jenom pokyny ke spuštění aplikace straně zařízení. By měl spustit doprovodné aplikace straně služby, která je k dispozici v následujících úvodních příručkách:
 
   * [Komunikaci s aplikací pro zařízení v C# prostřednictvím datových proudů zařízení služby IoT Hub](./quickstart-device-streams-echo-csharp.md)

   * [Komunikaci s aplikací pro zařízení v prostředí Nodejs prostřednictvím datových proudů zařízení služby IoT Hub](./quickstart-device-streams-echo-nodejs.md).

Aplikace C straně zařízení v rámci tohoto rychlého startu má následující funkce:

* Vytvořte datový proud zařízení na zařízení IoT.

* Získávat data z na straně služby a odezvu ji zpět.

Zahájení procesu zařízení datového proudu, jakož i jak ji používat k odesílání a příjmu dat vám ukáže, kód.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Ve verzi preview streamovaných zařízení je momentálně podporována pouze pro vytvoření centra IoT hub v těchto oblastech:

  * **USA (střed)**

  * **Střed USA – EUAP**

* Nainstalujte [Visual Studio 2017](https://www.visualstudio.com/vs/) s povolenou sadou funkcí [Vývoj desktopových aplikací pomocí C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/).

* Nainstalujte nejnovější verzi [Git](https://git-scm.com/download/).

* Spusťte následující příkaz pro přidání rozšíření Microsoft Azure IoT pro Azure CLI do instance služby Cloud Shell. Rozšíření IOT přidá služby IoT Hub, IoT Edge a IoT zařízení zřizování služby (DPS) konkrétní příkazy rozhraní příkazového řádku Azure.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

Pro účely tohoto rychlého startu budete používat [sadu SDK pro zařízení Azure IoT pro jazyk C](iot-hub-device-sdk-c-intro.md). Připravte vývojové prostředí klonovat a vytvářet [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) z Githubu. Ukázkový kód, který se používá v tomto rychlém startu, je součástí sady SDK na GitHubu.

1. Stáhněte si [sestavovací systém CMake](https://cmake.org/download/).

    Je důležité, aby požadavky na sadu Visual Studio (Visual Studio a sada funkcí Vývoj desktopových aplikací pomocí C++) byly na vašem počítači nainstalované ještě **před** zahájením instalace `CMake`. Jakmile jsou požadované součásti k dispozici a stažený soubor je ověřený, nainstalujte sestavovací systém CMake.

2. Otevřete prostředí příkazového řádku nebo Git Bash. Spusťte následující příkaz pro naklonování úložiště GitHub sady [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c):

    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

    Očekávejte, aby tato operace trvat několik minut.

3. V kořenovém adresáři úložiště Git vytvořte podadresář `cmake` a přejděte do této složky.

    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Spusťte následující příkazy z `cmake` adresář pro sestavení verze sady SDK, které jsou specifické pro vaše klientská platforma pro vývoj.

   * V systému Linux:

      ```bash
      cmake ..
      make -j
      ```

   * Ve Windows spusťte následující příkazy v Developer Command Prompt pro sadu Visual Studio 2015 nebo 2017. V adresáři `cmake` se vygeneruje řešení Visual Studia pro simulované zařízení.

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registrování zařízení

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V této části zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell s [rozšířením IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest).

1. Spusťte následující příkaz v Azure Cloud Shell vytvořte identitu zařízení.

   **YourIoTHubName**: Nahraďte tento zástupný text pod názvem, který jste vybrali pro službu IoT hub.

   **Mojezařízení**: Toto je název pro registrovaná zařízení. Použijte Mojezařízení, jak je znázorněno. Pokud si zvolíte jiný název zařízení, budete ho muset používat v průběhu celého článku a aktualizovat název zařízení v ukázkových aplikacích, než je spustíte.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Spuštěním následujícího příkazu ve službě Azure Cloud Shell získejte *připojovací řetězec zařízení* pro zařízení, které jste právě zaregistrovali:

   **YourIoTHubName**: Nahraďte tento zástupný text pod názvem, který jste vybrali pro službu IoT hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, která vypadá jako v následujícím příkladu:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v tomto rychlém startu.

## <a name="communicate-between-device-and-service-via-device-streams"></a>Komunikaci mezi zařízením a službou prostřednictvím datových proudů zařízení

V této části spuštění aplikace straně zařízení a aplikace straně služby a komunikaci mezi nimi.

### <a name="run-the-device-side-application"></a>Spuštění aplikace straně zařízení

Ke spuštění aplikace straně zařízení, je třeba provést následující kroky:

1. Zadejte svoje přihlašovací údaje zařízení tak, že upravíte soubor zdrojového `iothub_client_c2d_streaming_sample.c` ve složce `iothub_client/samples/iothub_client_c2d_streaming_sample` a připojovací řetězec zařízení.

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "[device connection string]";
   ```

2. Kompilujte kód následujícím způsobem:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to the cmake folder at the root of repo
   cmake --build . -- /m /p:Configuration=Release
   ```

3. Spusťte zkompilovaný program:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   ./iothub_client_c2d_streaming_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_sample\Release
   iothub_client_c2d_streaming_sample.exe
   ```

### <a name="run-the-service-side-application"></a>Spuštění aplikace straně služby

Jak už bylo zmíněno dříve, IoT Hub C SDK podporuje datové proudy zařízení pouze na straně zařízení. K sestavení a spuštění aplikace straně služby, postupujte podle kroků uvedených v jednom z následujících šablon rychlý start:

* [Pro aplikace pro zařízení v komunikaci C# prostřednictvím datových proudů zařízení služby IoT Hub](./quickstart-device-streams-echo-csharp.md)

* [Komunikace na zařízení aplikaci v Node.js prostřednictvím datových proudů zařízení služby IoT Hub](./quickstart-device-streams-echo-nodejs.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste nastavení služby IoT hub, zaregistrovali zařízení, navázat datovém proudu zařízení mezi C aplikací na zařízení a jiná aplikace na straně služby a používají datového proudu k odesílání dat vpřed a zpět mezi těmito aplikacemi.

Další informace o datových proudů zařízení pomocí odkazů níže:

> [!div class="nextstepaction"]
> [Přehled datových proudů zařízení](./iot-hub-device-streams-overview.md)