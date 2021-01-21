---
title: Rychlý Start – komunikace s aplikací zařízení v C s datovými proudy zařízení v Azure IoT Hub
description: V tomto rychlém startu spustíte aplikaci na straně zařízení v jazyce C, která komunikuje se zařízením IoT přes datový proud zařízení.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: 403fe82c46c90c5f8efb48ce0a394a2f63488fed
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624621"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Rychlý Start: komunikace s aplikací zařízení v C prostřednictvím datových proudů zařízení IoT Hub (Preview)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Azure IoT Hub aktuálně podporuje streamy zařízení jako [funkci ve verzi Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[IoT Hub datové proudy zařízení](iot-hub-device-streams-overview.md) umožňují aplikacím služeb a zařízením komunikovat zabezpečeným způsobem a bránou firewall. V rámci verze Public Preview podporuje sada C SDK streamy zařízení jenom na straně zařízení. V důsledku toho tento rychlý Start popisuje pokyny pro spuštění pouze aplikace na straně zařízení. Chcete-li spustit odpovídající aplikaci na straně služby, přečtěte si tyto články:

* [Komunikace s aplikacemi zařízení v jazyce C# prostřednictvím datových proudů zařízení IoT Hub](./quickstart-device-streams-echo-csharp.md)

* [Komunikace s aplikacemi zařízení v Node.js prostřednictvím datových proudů IoT Hub zařízení](./quickstart-device-streams-echo-nodejs.md)

Aplikace C na straně zařízení v tomto rychlém startu má následující funkce:

* Vytvořte datový proud zařízení pro zařízení IoT.

* Přijímá data odeslaná z aplikace na straně služby a vrací je zpět.

Kód ukazuje proces iniciace datového proudu zařízení a také způsob, jak ho použít k posílání a přijímání dat.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Potřebujete následující požadavky:

* Nainstalujte [Visual Studio 2019](https://www.visualstudio.com/vs/) s povoleným úlohou **vývoj desktopových aplikací v jazyce C++** .

* Nainstalujte nejnovější verzi [Git](https://git-scm.com/download/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

Verze Preview datových proudů zařízení je momentálně podporovaná jenom pro centra IoT, která jsou vytvořená v následujících oblastech:

  * USA – střed
  * Střed USA EUAP
  * Severní Evropa
  * Jihovýchodní Asie

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

V tomto rychlém startu použijete [sadu SDK pro zařízení Azure IoT pro jazyk C](iot-hub-device-sdk-c-intro.md). Připravíte vývojové prostředí, které se používá k klonování a sestavení [sady Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) z GitHubu. Sada SDK na GitHubu obsahuje vzorový kód, který se používá v tomto rychlém startu.

   > [!NOTE]
   > Než začnete s tímto postupem, ujistěte se, že je aplikace Visual Studio nainstalovaná s úlohou **vývoj desktopových aplikací C++** .

1. Nainstalujte [systém sestavení cmake](https://cmake.org/download/) , jak je popsáno na stránce pro stažení.

1. Otevřete prostředí příkazového řádku nebo Git Bash. Spuštěním následujících příkazů naklonujte úložiště GitHub pro [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) :

    ```cmd/sh
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Tato operace by měla trvat několik minut.

1. Vytvořte podadresář *cmake* v kořenovém adresáři úložiště Git a přejděte do této složky. Z adresáře *Azure-IoT-SDK-c* spusťte následující příkazy:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

1. Spuštěním následujících příkazů z adresáře *cmake* sestavíte verzi sady SDK, která je specifická pro vaši platformu vašeho vývojového klienta.

   * V systému Linux:

      ```bash
      cmake ..
      make -j
      ```

   * V systému Windows otevřete [Developer Command Prompt pro Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs). Spusťte příkaz pro vaši verzi sady Visual Studio. V tomto rychlém startu se používá Visual Studio 2019. Tyto příkazy vytvoří řešení sady Visual Studio pro simulované zařízení v adresáři *cmake* .

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Or for VS2019
      cmake .. -G "Visual Studio 16 2019"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrace zařízení

Abyste se mohli připojit, musíte zařízení zaregistrovat ve službě IoT Hub. V této části použijete Azure Cloud Shell s [rozšířením IoT](/cli/azure/ext/azure-iot/iot?view=azure-cli-latest&preserve-view=true) k registraci simulovaného zařízení.

1. Chcete-li vytvořit identitu zařízení, spusťte následující příkaz v Cloud Shell:

   > [!NOTE]
   > * Zástupný text *YourIoTHubName* nahraďte názvem, který jste zvolili pro Centrum IoT.
   > * Pro název zařízení, které zaregistrujete, se doporučuje používat *mojezařízení* , jak je znázorněno na obrázku. Pokud pro své zařízení zvolíte jiný název, použijte tento název v celém rámci tohoto článku a aktualizujte název zařízení v ukázkových aplikacích ještě předtím, než je spustíte.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Pokud chcete získat *připojovací řetězec zařízení* pro zařízení, které jste právě zaregistrovali, spusťte v Cloud Shell následující příkaz:

   > [!NOTE]
   > Zástupný text *YourIoTHubName* nahraďte názvem, který jste zvolili pro Centrum IoT.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Poznamenejte si vrácený připojovací řetězec zařízení pro pozdější použití v tomto rychlém startu. Soubor bude vypadat jako v následujícím příkladu:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Komunikace mezi zařízením a službou prostřednictvím datových proudů zařízení

V této části spustíte jak aplikaci na straně zařízení, tak aplikaci na straně služby a komunikujete mezi nimi.

### <a name="run-the-device-side-application"></a>Spuštění aplikace na straně zařízení

Chcete-li spustit aplikaci na straně zařízení, postupujte podle následujících kroků:

1. Zadejte přihlašovací údaje k zařízení úpravou zdrojového souboru **iothub_client_c2d_streaming_sample. c** ve `iothub_client/samples/iothub_client_c2d_streaming_sample` složce a přidáním připojovacího řetězce zařízení.

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   ```

1. Zkompilujte kód pomocí následujících příkazů:

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

1. Spusťte kompilovaný program:

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

### <a name="run-the-service-side-application"></a>Spuštění aplikace na straně služby

Jak již bylo uvedeno výše, sada IoT Hub C SDK podporuje pouze datové proudy zařízení na straně zařízení. Pokud chcete sestavit a spustit doprovodnou aplikaci na straně služby, postupujte podle pokynů v některém z následujících rychlých startů:

* [Komunikace s aplikací zařízení v jazyce C# prostřednictvím IoT Hub datových proudů zařízení](./quickstart-device-streams-echo-csharp.md)

* [Komunikace s aplikací zařízení v Node.js prostřednictvím datových proudů IoT Hub zařízení](./quickstart-device-streams-echo-nodejs.md)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu nastavíte službu IoT Hub, zaregistrovali jste zařízení, navázali datový proud zařízení mezi aplikací jazyka C na zařízení a další aplikací na straně služby a pomocí datového proudu odesíláte data mezi aplikacemi.

Další informace o datových proudech zařízení najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Přehled streamů zařízení](./iot-hub-device-streams-overview.md)
