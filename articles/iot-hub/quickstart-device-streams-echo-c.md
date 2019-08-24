---
title: Komunikace na zařízení aplikaci v jazyce C prostřednictvím datových proudů zařízení Azure IoT Hub (preview) | Dokumentace Microsoftu
description: V tomto rychlém startu spustíte aplikace na straně zařízení jazyka C, který komunikuje s zařízení IoT prostřednictvím datového proudu zařízení.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 4b6f987c68f9fe3ef95c82017b7d8be1d83083ea
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446132"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Rychlý start: Komunikace na zařízení aplikaci v jazyce C prostřednictvím datových proudů zařízení služby IoT Hub (preview)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Datové proudy zařízení jako v současné době podporuje služba Azure IoT Hub [funkce ve verzi preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Datové proudy zařízení služby IoT Hub](iot-hub-device-streams-overview.md) povolit zařízení a služeb aplikacím komunikovat způsobem, firewallem procházející a zabezpečené. Ve verzi public preview SDK pro jazyk C podporuje datové proudy zařízení na straně zařízení. V důsledku toho tento rychlý start popisuje pokyny ke spuštění jenom aplikace na straně zařízení. Ke spuštění doprovodné aplikace na straně služby, naleznete v tématu:
 
   * [Komunikaci s aplikací pro zařízení v C# prostřednictvím datových proudů zařízení služby IoT Hub](./quickstart-device-streams-echo-csharp.md)
   * [Komunikaci s aplikací pro zařízení v Node.js prostřednictvím datových proudů zařízení služby IoT Hub](./quickstart-device-streams-echo-nodejs.md)

Aplikace C straně zařízení v rámci tohoto rychlého startu má následující funkce:

* Vytvořte datový proud zařízení na zařízení IoT.
* Příjem dat, které se odesílá z aplikace na straně služby a odezvu ji zpět.

Kód ukazuje proces zahájení zařízení datového proudu, jakož i jak používat odesílat a přijímat data.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Ve verzi preview streamovaných zařízení je momentálně podporována pouze pro IoT hub, které jsou vytvořeny v těchto oblastech:

  * Střední USA
  * Střed USA – EUAP

* Nainstalujte [Visual Studio 2017](https://www.visualstudio.com/vs/) s [vývoj desktopových aplikací pomocí C++ ](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) povolenou sadu funkcí.

* Nainstalujte nejnovější verzi [Git](https://git-scm.com/download/).

* Spusťte následující příkaz pro přidání rozšíření Azure IoT pro Azure CLI do instance služby Cloud Shell. Přidá rozšíření IOT služby IoT Hub, IoT Edge a IoT zařízení zřizování služby (DPS)-konkrétní příkazy rozhraní příkazového řádku Azure.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

Pro účely tohoto rychlého startu použijete [zařízení Azure IoT SDK pro jazyk C](iot-hub-device-sdk-c-intro.md). Příprava prostředí pro vývoj klonovat a vytvářet [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) z Githubu. Sada SDK na Githubu zahrnuje ukázkový kód, který se používá v rámci tohoto rychlého startu.

1. Stáhněte si [sestavovací systém CMake](https://cmake.org/download/).

    Před zahájením instalace CMake, je důležité, který požadavky sady Visual Studio (Visual Studio a *vývoj desktopových aplikací pomocí C++*  úloh) jsou na vašem počítači nainstalovaný. Poté, co jsou požadavky na místě a ověříte, stahování, můžete nainstalovat sestavovací systém CMake.

2. Otevřete prostředí příkazového řádku nebo Git Bash. Spusťte následující příkaz pro naklonování úložiště GitHub sady [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c):

    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

    Tato operace by měla trvat několik minut.

3. Vytvoření *cmake* podadresáře v kořenovém adresáři úložiště Git, jak je vidět v následujícím příkazu, pak přejděte do této složky.

    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Spusťte následující příkazy z *cmake* adresář pro sestavení verze sady SDK, které jsou specifické pro vaše klientská platforma pro vývoj.

   * V systému Linux:

      ```bash
      cmake ..
      make -j
      ```

   * Ve Windows spusťte následující příkazy v Developer Command Prompt pro sadu Visual Studio 2015 nebo 2017. Řešení sady Visual Studio pro simulované zařízení bude vygenerován ve *cmake* adresáře.

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

Předtím, než se může připojit, zaregistrujte zařízení pomocí služby IoT hub. V této části můžete použít Azure Cloud Shell s [rozšíření IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) zaregistrovat simulované zařízení.

1. K vytvoření identity zařízení, spusťte následující příkaz ve službě Cloud Shell:

   > [!NOTE]
   > * Nahradit *YourIoTHubName* zástupný symbol název, který zvolíte pro službu IoT hub.
   > * Použití *Mojezařízení*, jak je znázorněno. Jedná se o název pro registrovaná zařízení. Pokud zvolíte jiný název pro vaše zařízení, použijte tento název v rámci tohoto článku a aktualizujte název zařízení v ukázkové aplikace před spuštěním je.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Chcete-li získat *připojovací řetězec zařízení* pro zařízení, které jste právě zaregistrovali, spusťte následující příkazy ve službě Cloud Shell:

   > [!NOTE]
   > Nahradit *YourIoTHubName* zástupný symbol název, který zvolíte pro službu IoT hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Poznamenejte si připojovací řetězec zařízení pro pozdější použití v rámci tohoto rychlého startu. Soubor bude vypadat jako v následujícím příkladu:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Komunikaci mezi zařízením a služby prostřednictvím datových proudů zařízení

V této části spuštění aplikace straně zařízení a aplikace straně služby a komunikaci mezi nimi.

### <a name="run-the-device-side-application"></a>Spuštění aplikace straně zařízení

Ke spuštění aplikace straně zařízení, postupujte takto:

1. Zadejte svoje přihlašovací údaje zařízení tak, že upravíte *iothub_client_c2d_streaming_sample.c* zdrojový soubor v *iothub_client/samples/iothub_client_c2d_streaming_sample* složku a pak zadají připojovací řetězec zařízení.

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

Jak už bylo zmíněno dříve, sady SDK IoT Hub C podporuje datové proudy zařízení na straně zařízení. Sestavení a spuštění aplikace straně služby, postupujte podle pokynů v jednom z následujících šablon rychlý start:

* [Pro aplikace pro zařízení v komunikaci C# prostřednictvím datových proudů zařízení služby IoT Hub](./quickstart-device-streams-echo-csharp.md)
* [Komunikace na zařízení aplikaci v Node.js prostřednictvím datových proudů zařízení služby IoT Hub](./quickstart-device-streams-echo-nodejs.md)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste nastavení služby IoT hub, zaregistrovali zařízení, vytvořit datový proud zařízení mezi C aplikací na zařízení a jiná aplikace na straně služby a používají datového proudu k odesílání dat vpřed a zpět mezi těmito aplikacemi.

Další informace o zařízení datových proudů, naleznete v tématu:

> [!div class="nextstepaction"]
> [Přehled datových proudů zařízení](./iot-hub-device-streams-overview.md)
