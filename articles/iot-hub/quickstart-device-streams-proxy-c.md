---
title: Rychlé zprovoznění zařízení v Azure IoT Hub v datových proudech v jazyce C pro SSH a RDP
description: V tomto rychlém startu spustíte ukázkovou aplikaci jazyka C, která funguje jako proxy a povoluje scénáře SSH a RDP nad IoT Hubmi datovými proudy zařízení.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 9e2182ee67683c4dfb963aeb320984a82d78b26c
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148855"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Rychlý Start: povolení SSH a RDP přes datový proud IoT Hub zařízení pomocí aplikace proxy v jazyce C (Preview)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Azure IoT Hub aktuálně podporuje streamy zařízení jako [funkci ve verzi Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[IoT Hub datové proudy zařízení](./iot-hub-device-streams-overview.md) umožňují aplikacím služeb a zařízením komunikovat zabezpečeným způsobem a bránou firewall. Přehled nastavení najdete [na ukázkové stránce místní proxy](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

Tento rychlý Start popisuje nastavení provozu Secure Shellho tunelu (SSH) (pomocí portu 22) prostřednictvím datových proudů zařízení. Instalace pro provoz protokol RDP (Remote Desktop Protocol) (RDP) je podobná a vyžaduje jednoduchou změnu konfigurace. Vzhledem k tomu, že proudy zařízení jsou aplikace a protokol nezávislá, můžete tento rychlý Start upravit tak, aby vyhovoval jiným typům provozu aplikací.

## <a name="how-it-works"></a>Jak to funguje

Následující obrázek ukazuje, jak se v místních a koncových procesech proxy zařízení a služeb pro službu a procesy démona SSH zapnuly koncová připojení. V rámci verze Public Preview podporuje sada C SDK streamy zařízení jenom na straně zařízení. V důsledku toho se v tomto rychlém startu dozvíte, jak spustit pouze aplikaci proxy místní zařízení. Pokud chcete sestavit a spustit doprovodnou aplikaci na straně služby, postupujte podle pokynů v některém z následujících rychlých startů:

* [SSH/RDP přes IoT Hub streamy zařízení pomocí proxy jazyka C#](./quickstart-device-streams-proxy-csharp.md)
* [SSH/RDP po IoT Hub datových proudů zařízení pomocí proxy serveru NodeJS](./quickstart-device-streams-proxy-nodejs.md)

![Nastavení místního proxy serveru](./media/quickstart-device-streams-proxy-c/device-stream-proxy-diagram.png)

1. Místní proxy server služby se připojí ke službě IoT Hub a spustí na cílovém zařízení datový proud zařízení.

2. Místní proxy zařízení dokončí modul handshake iniciace streamování a vytvoří komplexní tunelové propojení prostřednictvím koncového bodu streamování služby IoT Hub až po stranu služby.

3. Místní proxy zařízení se připojí k procesu démon SSH, který naslouchá na portu 22 na zařízení. Toto nastavení je možné konfigurovat, jak je popsáno v části spuštění aplikace pro místní proxy zařízení.

4. Místní proxy server služby čeká na nové připojení SSH od uživatele tím, že naslouchá na určeném portu, což je v tomto případě port 2222. Toto nastavení je možné konfigurovat, jak je popsáno v části spuštění aplikace pro místní proxy zařízení. Když se uživatel připojí přes klienta SSH, tunel umožňuje přenos aplikace SSH mezi klientskými a serverovými programy SSH.

> [!NOTE]
> Provoz SSH, který se odesílá přes datový proud zařízení, se prochází tunelovým propojením přes koncový bod streamování IoT Hub, nikoli přímo mezi službou a zařízením. Další informace najdete v tématu [výhody použití datových proudů zařízení ve službě IoT Hub](iot-hub-device-streams-overview.md#benefits). Obrázek navíc znázorňuje démona SSH, který běží na stejném zařízení (nebo počítači) jako místní proxy server zařízení. V tomto rychlém startu zadáním IP adresy démona SSH umožníte, aby místní proxy zařízení a démon běžely i na různých počítačích.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

## <a name="prerequisites"></a>Předpoklady

* Verze Preview datových proudů zařízení je momentálně podporovaná jenom pro centra IoT, která jsou vytvořená v následujících oblastech:

  * Střední USA
  * Střed USA EUAP
  * Severní Evropa
  * Jihovýchodní Asie

* Nainstalujte [Visual Studio 2019](https://www.visualstudio.com/vs/) s povoleným úlohou [vývoj desktopových aplikací v jazyce C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) .
* Nainstalujte nejnovější verzi [Git](https://git-scm.com/download/).

* Spuštěním následujícího příkazu přidejte rozšíření Azure IoT pro Azure CLI do instance Cloud Shell. Rozšíření IOT přidá do Azure CLI příkazy, které jsou specifické pro služby IoT Hub, IoT Edge a IoT Device Provisioning (DPS).

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

V tomto rychlém startu použijete [sadu SDK pro zařízení Azure IoT pro jazyk C](iot-hub-device-sdk-c-intro.md). Připravíte vývojové prostředí, které se používá k klonování a sestavení [sady Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) z GitHubu. Sada SDK na GitHubu obsahuje vzorový kód, který se používá v tomto rychlém startu.

1. Stáhněte si [sestavovací systém cmake](https://cmake.org/download/).

    *Před* zahájením instalace cmake je důležité, abyste na svém počítači nainstalovali požadavky sady Visual Studio (Visual Studio a *vývoj desktopových aplikací s C++* ). Po dokončení požadavků a ověření, že je možné soubory stáhnout, můžete nainstalovat sestavovací systém CMake.

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

   * V systému Windows spusťte následující příkazy v Developer Command Prompt pro Visual Studio 2015 nebo 2017. Řešení sady Visual Studio pro simulované zařízení se vygeneruje v adresáři *cmake* .

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

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V této části použijete Azure Cloud Shell s [rozšířením IoT](/cli/azure/ext/azure-iot/iot?view=azure-cli-latest) k registraci simulovaného zařízení.

1. Chcete-li vytvořit identitu zařízení, spusťte následující příkaz v Cloud Shell:

   > [!NOTE]
   > * Zástupný text *YourIoTHubName* nahraďte názvem, který jste zvolili pro Centrum IoT.
   > * Pro název zařízení, které zaregistrujete, se doporučuje používat *mojezařízení* , jak je znázorněno na obrázku. Pokud pro své zařízení zvolíte jiný název, použijte tento název v celém rámci tohoto článku a aktualizujte název zařízení v ukázkových aplikacích ještě předtím, než je spustíte.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Pokud chcete získat *připojovací řetězec zařízení* pro zařízení, které jste právě zaregistrovali, spusťte v Cloud Shell následující příkazy:

   > [!NOTE]
   > Zástupný text *YourIoTHubName* nahraďte názvem, který jste zvolili pro Centrum IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Poznamenejte si vrácený připojovací řetězec zařízení pro pozdější použití v tomto rychlém startu. Soubor bude vypadat jako v následujícím příkladu:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH pro zařízení prostřednictvím datových proudů zařízení

V této části vytvoříte ucelený Stream pro tunelování provozu SSH.

### <a name="run-the-device-local-proxy-application"></a>Spuštění aplikace místní proxy zařízení

1. Upravte zdrojový soubor **iothub_client_c2d_streaming_proxy_sample. c** ve složce `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample` a zadejte připojovací řetězec zařízení, IP adresu cílového zařízení nebo název hostitele a port SSH 22:

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

### <a name="run-the-service-local-proxy-application"></a>Spuštění aplikace místní proxy serveru služby

Jak je popsáno v části "jak to funguje", vytvoření koncového datového proudu pro tunelový přenos SSH vyžaduje místní proxy server na každém konci (na stranách služby i na straně zařízení). V rámci verze Public Preview podporuje IoT Hub C SDK pouze datové proudy zařízení na straně zařízení. Pokud chcete sestavit a spustit místní proxy server služby, postupujte podle pokynů v některém z těchto rychlých startů:

   * [SSH/RDP přes IoT Hub streamy zařízení pomocí proxy aplikací C#](./quickstart-device-streams-proxy-csharp.md)
   * [SSH/RDP přes IoT Hub streamy zařízení pomocí proxy aplikací Node.js](./quickstart-device-streams-proxy-nodejs.md)

### <a name="establish-an-ssh-session"></a>Vytvoření relace SSH

Po spuštění místních proxy serverů zařízení i služby použijte klientský program SSH a připojte se k místnímu proxy serveru služby na portu 2222 (místo démona SSH přímo).

```cmd/sh
ssh {username}@localhost -p 2222
```

V tomto okamžiku se zobrazí okno přihlášení SSH s výzvou k zadání přihlašovacích údajů.

Následující obrázek ukazuje výstup konzoly na místní proxy zařízení, které se připojí k procesu démon SSH na adrese `IP_address:22` :

![Výstup místního proxy serveru v zařízení](./media/quickstart-device-streams-proxy-c/device-console-output.png)

Následující obrázek ukazuje výstup konzoly klientského programu SSH. Klient SSH komunikuje s démonem SSH připojením k portu 22, na kterém místní proxy služby naslouchá:

![Výstup klienta SSH](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu nastavíte službu IoT Hub, zaregistrovali jste zařízení, nasadili zařízení a místní proxy server služby, aby se navázal datový proud zařízení pomocí IoT Hub a použil proxy servery k tunelování provozu SSH.

Další informace o datových proudech zařízení najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Přehled streamů zařízení](./iot-hub-device-streams-overview.md)