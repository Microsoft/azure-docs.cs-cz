---
title: Komunikujte s aplikací pro zařízení v C pomocí datových proudů zařízení Azure IoT Hub
description: V tomto rychlém startu spustíte aplikaci na straně zařízení C, která komunikuje se zařízením IoT prostřednictvím datového proudu zařízení.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: 52f9e6529329c5bb1abb176082294dc26e64baa3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675532"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Úvodní příručka: Komunikace s aplikací zařízení v C prostřednictvím datových proudů zařízení služby IoT Hub (náhled)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Azure IoT Hub aktuálně podporuje datové proudy zařízení jako [funkci náhledu](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Datové proudy zařízení služby IoT Hub umožňují aplikacím služby](iot-hub-device-streams-overview.md) a zařízení komunikovat bezpečným způsobem a způsobem, který je vhodný pro bránu firewall. Během veřejné verze Preview podporuje sada C SDK datové proudy zařízení pouze na straně zařízení. V důsledku toho tento rychlý start obsahuje pokyny ke spuštění pouze aplikace na straně zařízení. Chcete-li spustit odpovídající aplikaci na straně služby, naleznete v těchto článcích:

* [Komunikace s aplikacemi zařízení v C# prostřednictvím datových proudů zařízení služby IoT Hub](./quickstart-device-streams-echo-csharp.md)

* [Komunikace s aplikacemi zařízení v node.js prostřednictvím datových proudů zařízení služby IoT Hub](./quickstart-device-streams-echo-nodejs.md)

Aplikace C na straně zařízení v tomto rychlém startu má následující funkce:

* Vytvořte datový proud zařízení do zařízení IoT.

* Přijímat data odeslaná z aplikace na straně služby a echo je zpět.

Kód ukazuje proces zahájení datového proudu zařízení a také jak jej použít k odesílání a přijímání dat.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Potřebujete následující předpoklady:

* Nainstalujte [Visual Studio 2019](https://www.visualstudio.com/vs/) s vývojem plochy s povoleným zatížením **C++.**

* Nainstalujte nejnovější verzi [Git](https://git-scm.com/download/).

* Spusťte následující příkaz a přidejte rozšíření Azure IoT pro Azure CLI do instance Cloud Shellu. Rozšíření IOT přidá do rozhraní příkazového příkazu Azure CLI služby IoT Hub, IoT Edge a Služby zřizování zařízení IoT (DPS).

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

Náhled datových proudů zařízení je aktuálně podporován pouze pro centra IoT, které jsou vytvořeny v následujících oblastech:

  * USA – střed
  * Centrální US EUAP
  * Severní Evropa
  * Jihovýchodní Asie

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

Pro tento rychlý start použijete [sdk zařízení Azure IoT pro C](iot-hub-device-sdk-c-intro.md). Připravíte vývojové prostředí, které se používá ke klonování a vytváření [sady Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) z GitHubu. Sada SDK na GitHubu obsahuje ukázkový kód, který se používá v tomto rychlém startu.

   > [!NOTE]
   > Než začnete tento postup, ujistěte se, že Visual Studio je nainstalován s **desktop vývoj s zatížením C++.**

1. Nainstalujte [systém sestavení CMake,](https://cmake.org/download/) jak je popsáno na stránce pro stažení.

1. Otevřete prostředí příkazového řádku nebo Git Bash. Spusťte následující příkazy pro klonování úložiště [GitHub Azure IoT C SDK:](https://github.com/Azure/azure-iot-sdk-c)

    ```cmd/sh
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Tato operace by měla trvat několik minut.

1. Vytvořte podadresář *cmake* v kořenovém adresáři úložiště git a přejděte do této složky. Spusťte následující příkazy z adresáře *azure-iot-sdk-c:*

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

1. Spusťte následující příkazy z adresáře *cmake* a vytvořte verzi sady SDK, která je specifická pro vývojovou klientskou platformu.

   * V Linuxu:

      ```bash
      cmake ..
      make -j
      ```

   * V systému Windows otevřete [příkazový řádek pro vývojáře pro visual studio](/dotnet/framework/tools/developer-command-prompt-for-vs). Spusťte příkaz pro vaši verzi sady Visual Studio. Tento rychlý start používá Visual Studio 2019. Tyto příkazy vytvořit řešení sady Visual Studio pro simulované zařízení v adresáři *cmake.*

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

## <a name="register-a-device"></a>Registrování zařízení

Před připojením je nutné zaregistrovat zařízení ve službě IoT hub. V této části použijete Azure Cloud Shell s [rozšířením IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) k registraci simulované zařízení.

1. Chcete-li vytvořit identitu zařízení, spusťte v prostředí Cloud Shell následující příkaz:

   > [!NOTE]
   > * Nahraďte zástupný symbol *YourIoTHubName* názvem, který jste zvolili pro centrum IoT.
   > * Pro název zařízení, které registrujete, je doporučeno použít *MyDevice,* jak je znázorněno. Pokud pro své zařízení zvolíte jiný název, použijte tento název v celém tomto článku a aktualizujte název zařízení v ukázkových aplikacích před jejich spuštěním.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Chcete-li získat *připojovací řetězec zařízení* pro zařízení, které jste právě zaregistrovali, spusťte v prostředí Cloud Shell následující příkaz:

   > [!NOTE]
   > Nahraďte zástupný symbol *YourIoTHubName* názvem, který jste zvolili pro centrum IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Všimněte si vrácené zařízení připojovací řetězec pro pozdější použití v tomto rychlém startu. Soubor bude vypadat jako v následujícím příkladu:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Komunikace mezi zařízením a službou prostřednictvím datových proudů zařízení

V této části spustíte aplikaci na straně zařízení i aplikaci na straně služby a komunikujete mezi nimi.

### <a name="run-the-device-side-application"></a>Spuštění aplikace na straně zařízení

Chcete-li spustit aplikaci na straně zařízení, postupujte takto:

1. Zadejte pověření zařízení úpravou zdrojového souboru `iothub_client/samples/iothub_client_c2d_streaming_sample` **iothub_client_c2d_streaming_sample.c** ve složce a přidáním připojovacího řetězce zařízení.

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

1. Spusťte zkompilovaný program:

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

Jak již bylo zmíněno dříve, sada IoT Hub C SDK podporuje datové proudy zařízení pouze na straně zařízení. Chcete-li vytvořit a spustit doprovodnou aplikaci na straně služby, postupujte podle pokynů v jednom z následujících rychlých startů:

* [Komunikace s aplikací zařízení v C# prostřednictvím datových proudů zařízení služby IoT Hub](./quickstart-device-streams-echo-csharp.md)

* [Komunikace s aplikací zařízení v node.js prostřednictvím datových proudů zařízení služby IoT Hub](./quickstart-device-streams-echo-nodejs.md)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu nastavíte službu IoT hub, zaregistrujete zařízení, nastavíte datový proud zařízení mezi aplikací C na zařízení a jinou aplikací na straně služby a pomocí datového proudu odesíláte data tam a zpět mezi aplikacemi.

Další informace o streamech zařízení najdete v tématu:

> [!div class="nextstepaction"]
> [Přehled datových proudů zařízení](./iot-hub-device-streams-overview.md)
