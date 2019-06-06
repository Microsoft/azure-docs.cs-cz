---
title: Azure IoT Hub zařízení datové proudy C pro rychlý start pro SSH a protokolu RDP (preview) | Dokumentace Microsoftu
description: V tomto rychlém startu spustíte ukázkovou C aplikaci, která funguje jako proxy server, aby se povolily scénáře SSH a protokolu RDP přes službu IoT Hub zařízení datové proudy.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: e21d7381831553f8d82b00d9ed3be0b03f13bca9
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735125"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Rychlý start: Povolit SSH a protokolu RDP přes proud zařízení služby IoT Hub pomocí proxy aplikace jazyka C (preview)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Datové proudy zařízení jako v současné době podporuje služba Azure IoT Hub [funkce ve verzi preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Datové proudy zařízení služby IoT Hub](./iot-hub-device-streams-overview.md) povolit zařízení a služeb aplikacím komunikovat způsobem, firewallem procházející a zabezpečené. Přehled nastavení, naleznete v tématu [stránka s ukázkou místní proxy server](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

Tento rychlý start popisuje nastavení tunelu Secure Shell (SSH) provozu (pomocí portu 22) prostřednictvím datových proudů zařízení. Instalační program pro provoz protokolu RDP (Remote Desktop) se podobá a vyžaduje o jednoduchou změnu konfigurace. Protože zařízení datové proudy nezávisejí na aplikaci a protokol-, můžete upravit v tomto rychlém startu tak, aby vyhovovaly jiné typy provozu aplikací.

## <a name="how-it-works"></a>Jak to funguje

Následující obrázek ukazuje, jak povolit proxy aplikací místních zařízení a služby začátku do konce připojení mezi klienta SSH a procesy démon procesu SSH. Ve verzi public preview SDK pro jazyk C podporuje datové proudy zařízení na straně zařízení. V důsledku toho tento rychlý start popisuje pokyny ke spuštění pouze místní zařízení proxy aplikací. Když spustíte jeden z následujících šablon rychlý start straně služby:

* [SSH nebo RDP over zařízení služby IoT Hub toků pomocí C# proxy](./quickstart-device-streams-proxy-csharp.md)
* [SSH nebo RDP over datových proudů zařízení služby IoT Hub pomocí Node.js proxy](./quickstart-device-streams-proxy-nodejs.md).

![Instalace místní proxy server](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg)

1. Místní služba proxy připojí ke službě IoT hub a spustí datový proud zařízení na cílové zařízení.

2. Proxy server pro místní zařízení dokončí handshake zahájení datového proudu a vytvoří tunel začátku do konce datového proudu přes koncový bod streamování služby IoT hub na straně služby.

3. Místní zařízení proxy serveru se připojí k proces démon programu SSH, který naslouchá na portu 22 na zařízení. Toto nastavení je možné konfigurovat, jak je popsáno v části "Spuštění zařízení místní proxy aplikace".

4. Proxy server místní služby čeká na nová připojení SSH od uživatele prostřednictvím naslouchání na určeném portu, který v tomto případě je port 2222. Toto nastavení je možné konfigurovat, jak je popsáno v části "Spuštění zařízení místní proxy aplikace". Když se uživatel připojí pomocí klienta SSH, tunelové propojení umožňuje provoz SSH aplikace přenos mezi aplikacemi klientem a serverem SSH.

> [!NOTE]
> Provoz SSH, který je odeslán přes zařízení datový proud je tunelovým propojením přes koncový bod streamování služby IoT hub spíše než odeslána přímo mezi službou a zařízení. Další informace najdete v tématu [výhody použití datových proudů zařízení služby Iot Hub](iot-hub-device-streams-overview.md#benefits). Kromě toho obrázek znázorňuje proces démon programu SSH, který běží na stejném zařízení (nebo počítače) jako proxy pro místní zařízení. V tomto rychlém startu poskytnou IP adresu démon procesu SSH umožňuje proxy pro místní zařízení a proces daemon spouštět na různých počítačích.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Ve verzi preview streamovaných zařízení je momentálně podporována pouze pro IoT hub, které jsou vytvořeny v těchto oblastech:

  * USA – střed
  * Střed USA – EUAP

* Nainstalujte [Visual Studio 2019](https://www.visualstudio.com/vs/) s [vývoj desktopových aplikací pomocí C++ ](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) povolenou sadu funkcí.
* Nainstalujte nejnovější verzi [Git](https://git-scm.com/download/).

* Spusťte následující příkaz pro přidání rozšíření Azure IoT pro Azure CLI do instance služby Cloud Shell. Přidá rozšíření IOT služby IoT Hub, IoT Edge a IoT zařízení zřizování služby (DPS)-konkrétní příkazy rozhraní příkazového řádku Azure.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

Pro účely tohoto rychlého startu použijete [zařízení Azure IoT SDK pro jazyk C](iot-hub-device-sdk-c-intro.md). Příprava prostředí pro vývoj klonovat a vytvářet [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) z Githubu. Sada SDK na Githubu zahrnuje ukázkový kód, který se používá v rámci tohoto rychlého startu.

1. Stáhněte si [sestavovací systém CMake](https://cmake.org/download/).

    Je důležité, který požadavky sady Visual Studio (Visual Studio a *vývoj desktopových aplikací pomocí C++*  úloh) jsou na vašem počítači nainstalovaný *před* zahájením instalace CMake. Po požadavky jsou na místě a ověření stahování, můžete nainstalovat sestavovací systém CMake.

1. Otevřete prostředí příkazového řádku nebo Git Bash. Spusťte následující příkaz pro naklonování úložiště GitHub sady [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c):

    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

    Očekávejte, aby tato operace trvat několik minut.

1. Vytvoření *cmake* podadresáře v kořenovém adresáři úložiště Git, jak je znázorněno v následujícím příkazu a přejděte do této složky.

    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Spusťte následující příkazy z *cmake* adresář pro sestavení verze sady SDK, které jsou specifické pro vaše klientská platforma pro vývoj.

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

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V této části můžete použít Azure Cloud Shell s [rozšíření IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) zaregistrovat simulované zařízení.

1. K vytvoření identity zařízení, spusťte následující příkaz ve službě Cloud Shell:

   > [!NOTE]
   > * Nahradit *YourIoTHubName* zástupný symbol název, který zvolíte pro službu IoT hub.
   > * Použití *Mojezařízení*, jak je znázorněno. Jedná se o název pro registrovaná zařízení. Pokud zvolíte jiný název pro vaše zařízení, použijte tento název v rámci tohoto článku a aktualizujte název zařízení v ukázkové aplikace před spuštěním je.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

1. Chcete-li získat *připojovací řetězec zařízení* pro zařízení, které jste právě zaregistrovali, spusťte následující příkazy ve službě Cloud Shell:

   > [!NOTE]
   > Nahradit *YourIoTHubName* zástupný symbol název, který zvolíte pro službu IoT hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Poznamenejte si připojovací řetězec zařízení pro pozdější použití v rámci tohoto rychlého startu. Soubor bude vypadat jako v následujícím příkladu:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="ssh-to-a-device-via-device-streams"></a>Přístup k zařízení prostřednictvím datových proudů zařízení přes SSH

V této části vytvoříte začátku do konce datového proudu k tunelování provoz SSH.

### <a name="run-the-device-local-proxy-application"></a>Spuštění aplikace proxy pro místní zařízení

1. Upravte zdrojový soubor *iothub_client_c2d_streaming_sample.c* ve složce *iothub_client/samples/iothub_client_c2d_streaming_sample*a zadejte připojovací řetězec zařízení, nastavte cílovou zařízení IP nebo název hostitele a port 22 pro SSH:

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "[Connection string of IoT Hub]";
   static const char* localHost = "[IP/Host of your target machine]"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

1. Kompilace vzorku:

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

1. Zkompilovaný program spouštět v zařízení:

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

### <a name="run-the-service-local-proxy-application"></a>Spuštění místní služby proxy aplikace

Jak je popsáno v části "Jak to funguje" Vytvoření tunelu SSH provoz začátku do konce datového proudu vyžaduje místní proxy server na každém konci (na službu i zařízení strany). Ve verzi public preview IoT Hub C SDK podporuje datové proudy zařízení na straně zařízení. Sestavte a spusťte místní službu proxy serveru, postupujte podle pokynů v jednom z následujících šablon rychlý start:

   * [SSH nebo RDP over zařízení služby IoT Hub toků pomocí C# proxy aplikací](./quickstart-device-streams-proxy-csharp.md)
   * [SSH nebo RDP over datových proudů zařízení služby IoT Hub pomocí Node.js proxy aplikací](./quickstart-device-streams-proxy-nodejs.md)

### <a name="establish-an-ssh-session"></a>Relaci SSH

Po spuštění proxy serverů s místním zařízení a služba používat váš klientský program SSH a připojení k proxy serveru místní služby na port 2222 (namísto démon procesu SSH přímo).

```cmd/sh
ssh <username>@localhost -p 2222
```

V tomto okamžiku okno přihlášení SSH vás vyzve k zadání přihlašovacích údajů.

Následující obrázek ukazuje na místní zařízení proxy serveru, který se připojuje k proces démon programu SSH na výstup konzoly `IP_address:22`:

![Výstup zařízení místní proxy server](./media/quickstart-device-streams-proxy-c/device-console-output.png)

Následující obrázek ukazuje výstup konzoly z programu klienta SSH. Klient SSH komunikuje se démon procesu SSH propojením na port 22, který služba místní proxy server naslouchá na:

![Výstup klienta SSH](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste nastavení služby IoT hub, zaregistrovali zařízení, nasazení zařízení – a místní službu proxy program vytvořit datový proud zařízení prostřednictvím služby IoT Hub a použít proxy serverů k tunelování provoz SSH.

Další informace o zařízení datových proudů, naleznete v tématu:

> [!div class="nextstepaction"]
> [Přehled datových proudů zařízení](./iot-hub-device-streams-overview.md)
