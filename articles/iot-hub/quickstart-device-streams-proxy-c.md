---
title: Datové proudy zařízení Azure IoT Hub c rychlý start pro SSH a RDP
description: V tomto rychlém startu spustíte ukázkovou aplikaci C, která funguje jako proxy server pro povolení scénářů SSH a RDP přes datové proudy zařízení služby IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: af303928490d2570fa9430bd4b9d30c4a49df304
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675493"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Úvodní příručka: Povolení SSH a RDP přes datový proud zařízení služby IoT Hub pomocí aplikace proxy C (preview)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Azure IoT Hub aktuálně podporuje datové proudy zařízení jako [funkci náhledu](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Datové proudy zařízení služby IoT Hub umožňují aplikacím služby](./iot-hub-device-streams-overview.md) a zařízení komunikovat bezpečným způsobem a způsobem, který je vhodný pro bránu firewall. Přehled nastavení naleznete na [stránce Ukázka místního proxy serveru](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

Tento rychlý start popisuje nastavení pro tunelové propojení zabezpečeného prostředí (SSH) přenosy (pomocí portu 22) prostřednictvím datových proudů zařízení. Nastavení provozu protokolu RDP (RDP) je podobné a vyžaduje jednoduchou změnu konfigurace. Vzhledem k tomu, že datové proudy zařízení jsou nezávislá na aplikacích a protokolech, můžete tento rychlý start upravit tak, aby vyhovoval jiným typům provozu aplikací.

## <a name="how-it-works"></a>Jak to funguje

Následující obrázek znázorňuje, jak místní proxy programy zařízení a služby umožňují koncové připojení mezi klientem SSH a procesy demonu SSH. Během veřejné verze Preview podporuje sada C SDK datové proudy zařízení pouze na straně zařízení. V důsledku toho tento rychlý start popisuje pokyny ke spuštění pouze místní proxy aplikace zařízení. Chcete-li vytvořit a spustit doprovodnou aplikaci na straně služby, postupujte podle pokynů v jednom z následujících rychlých startů:

* [Datové proudy zařízení SSH/RDP přes službu IoT Hub pomocí proxy serveru C#](./quickstart-device-streams-proxy-csharp.md)
* [Datové proudy zařízení SSH/RDP přes službu IoT Hub pomocí proxy serveru NodeJS](./quickstart-device-streams-proxy-nodejs.md).

![Nastavení místního proxy serveru](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg)

1. Místní proxy služba se připojí k centru IoT a spustí datový proud zařízení do cílového zařízení.

2. Místní proxy server zařízení dokončí handshake zahájení datového proudu a vytvoří tunel streamování začátku do konce prostřednictvím koncového bodu streamování služby IoT hub na straně služby.

3. Místní proxy zařízení se připojí k daemonu SSH, který poslouchá na portu 22 v zařízení. Toto nastavení je konfigurovatelné, jak je popsáno v části Spuštění aplikace proxy místní zařízení.

4. Místní proxy služba čeká na nové připojení SSH od uživatele nasloucháním na určeném portu, který je v tomto případě port 2222. Toto nastavení je konfigurovatelné, jak je popsáno v části Spuštění aplikace proxy místní zařízení. Když se uživatel připojí přes klienta SSH, tunelové propojení umožňuje přenos aplikací SSH, který má být přenášen mezi klientem SSH a serverovými programy.

> [!NOTE]
> Provoz SSH, který je odeslán prostřednictvím datového proudu zařízení, je tunelován přes koncový bod streamování služby IoT hub, nikoli odesílány přímo mezi službou a zařízením. Další informace najdete v [tématu výhody používání datových proudů zařízení služby Iot Hub](iot-hub-device-streams-overview.md#benefits). Kromě toho obrázek znázorňuje daemon SSH, který běží na stejném zařízení (nebo počítači) jako proxy server místnízařízení. V tomto rychlém startu poskytuje SSH daemon IP adresu umožňuje proxy zařízení a daemon spustit na různých počítačích stejně.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

* Náhled datových proudů zařízení je aktuálně podporován pouze pro centra IoT, které jsou vytvořeny v následujících oblastech:

  * USA – střed
  * Centrální US EUAP
  * Severní Evropa
  * Jihovýchodní Asie

* Nainstalujte [Visual Studio 2019](https://www.visualstudio.com/vs/) s vývojem plochy s povoleným zatížením [C++.](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/)
* Nainstalujte nejnovější verzi [Git](https://git-scm.com/download/).

* Spusťte následující příkaz a přidejte rozšíření Azure IoT pro Azure CLI do instance Cloud Shellu. Rozšíření IOT přidá do rozhraní příkazového příkazu Azure CLI služby IoT Hub, IoT Edge a Služby zřizování zařízení IoT (DPS).

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

Pro tento rychlý start použijete [sdk zařízení Azure IoT pro C](iot-hub-device-sdk-c-intro.md). Připravíte vývojové prostředí, které se používá ke klonování a vytváření [sady Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) z GitHubu. Sada SDK na GitHubu obsahuje ukázkový kód, který se používá v tomto rychlém startu.

1. Stáhněte si [systém sestavení CMake](https://cmake.org/download/).

    Je důležité, aby požadavky sady Visual Studio (Visual Studio a vývoj plochy s úlohami *jazyka C++)* byly nainstalovány v počítači *před* spuštěním instalace CMake. Po předpoklady jsou na místě a stahování je ověřeno, můžete nainstalovat cmake sestavení systému.

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

   * V systému Windows spusťte následující příkazy v příkazovém řádku pro vývojáře pro Visual Studio 2015 nebo 2017. Řešení sady Visual Studio pro simulované zařízení bude generováno v adresáři *cmake.*

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

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V této části použijete Azure Cloud Shell s [rozšířením IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) k registraci simulované zařízení.

1. Chcete-li vytvořit identitu zařízení, spusťte v prostředí Cloud Shell následující příkaz:

   > [!NOTE]
   > * Nahraďte zástupný symbol *YourIoTHubName* názvem, který jste zvolili pro centrum IoT.
   > * Pro název zařízení, které registrujete, je doporučeno použít *MyDevice,* jak je znázorněno. Pokud pro své zařízení zvolíte jiný název, použijte tento název v celém tomto článku a aktualizujte název zařízení v ukázkových aplikacích před jejich spuštěním.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Chcete-li získat *připojovací řetězec zařízení* pro zařízení, které jste právě zaregistrovali, spusťte v prostředí Cloud Shell následující příkazy:

   > [!NOTE]
   > Nahraďte zástupný symbol *YourIoTHubName* názvem, který jste zvolili pro centrum IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Všimněte si vrácené zařízení připojovací řetězec pro pozdější použití v tomto rychlém startu. Soubor bude vypadat jako v následujícím příkladu:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH do zařízení prostřednictvím datových proudů zařízení

V této části vytvoříte end-to-end datový proud pro tunelové propojení s provozem SSH.

### <a name="run-the-device-local-proxy-application"></a>Spuštění aplikace proxy místního zařízení

1. Upravte zdrojový soubor **iothub_client_c2d_streaming_proxy_sample.c** ve složce `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample`a zadejte připojovací řetězec zařízení, název IP/hostcílového zařízení a port SSH 22:

   ```C
   /* Paste in your device connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   static const char* localHost = "{IP/Host of your target machine}"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

1. Zkompilujte ukázku:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to cmake at root of repository
   cmake --build . -- /m /p:Configuration=Release
   ```

1. Spusťte zkompilovaný program na zařízení:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   ./iothub_client_c2d_streaming_proxy_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_proxy_sample\Release
   iothub_client_c2d_streaming_proxy_sample.exe
   ```

### <a name="run-the-service-local-proxy-application"></a>Spuštění aplikace proxy místní služby

Jak je popsáno v části "Jak to funguje", vytvoření koncového datového proudu pro tunelové přenosy SSH vyžaduje místní proxy server na každém konci (na straně služby i zařízení). Ve verzi Public Preview podporuje sada SDK centra IoT Hub C streamy pouze na straně zařízení. Chcete-li vytvořit a spustit proxy server místní služby, postupujte podle pokynů v jednom z následujících rychlých startů:

   * [Streamy zařízení SSH/RDP přes Službu IoT Hub pomocí proxy aplikací Jazyka C#](./quickstart-device-streams-proxy-csharp.md)
   * [Datové proudy zařízení SSH/RDP přes službu IoT Hub pomocí proxy aplikací Node.js](./quickstart-device-streams-proxy-nodejs.md)

### <a name="establish-an-ssh-session"></a>Navázání relace SSH

Po spuštění proxy servery místní zařízení a služby použijte klientský program SSH a připojte se k serveru proxy pro místní službu na portu 2222 (namísto daemonu SSH přímo).

```cmd/sh
ssh {username}@localhost -p 2222
```

V tomto okamžiku okno přihlášení SSH vyzve k zadání pověření.

Následující obrázek znázorňuje výstup konzoly na místním proxy zařízení, který se `IP_address:22`připojuje k daemonu SSH na adrese :

![Výstup proxy místního zařízení](./media/quickstart-device-streams-proxy-c/device-console-output.png)

Následující obrázek znázorňuje výstup konzoly klientského programu SSH. Klient SSH komunikuje s daemonem SSH připojením k portu 22, na kterém naslouchá místní proxy služba:

![Výstup klienta SSH](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu nastavíte službu IoT hub, zaregistrujete zařízení, nasadíte proxy program místního zařízení a služby k vytvoření datového proudu zařízení prostřednictvím služby IoT Hub a použijete servery proxy k tunelování provozu SSH.

Další informace o streamech zařízení najdete v tématu:

> [!div class="nextstepaction"]
> [Přehled datových proudů zařízení](./iot-hub-device-streams-overview.md)
