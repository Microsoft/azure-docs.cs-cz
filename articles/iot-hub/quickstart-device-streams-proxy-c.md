---
title: Azure IoT Hub zařízení datové proudy C pro rychlý start pro SSH nebo RDP (preview) | Dokumentace Microsoftu
description: V tomto rychlém startu spustíte ukázkovou aplikaci jazyka C, který funguje jako proxy server, aby se povolily scénáře SSH nebo RDP přes datové proudy zařízení služby IoT Hub.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 539357c9dcfaaffa551b4be08427a51d9e92475f
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484765"
---
# <a name="quickstart-sshrdp-over-iot-hub-device-streams-using-c-proxy-application-preview"></a>Rychlý start: SSH nebo RDP over datové proudy zařízení služby IoT Hub pomocí jazyka C proxy aplikace (preview)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub v současné době podporuje datové proudy zařízení jako [funkce ve verzi preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Datové proudy zařízení služby IoT Hub](./iot-hub-device-streams-overview.md) povolit zařízení a služeb aplikacím komunikovat způsobem, firewallem procházející a zabezpečené. Zobrazit [na této stránce](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp) přehledné informace o nastavení.

Tento dokument popisuje nastavení pro tunelové propojení provoz SSH (s použitím port 22) prostřednictvím datových proudů zařízení. Instalační program pro provoz protokolu RDP se podobá a vyžaduje o jednoduchou změnu konfigurace. Protože zařízení datové proudy jsou aplikace a bez ohledu na protokol, tohoto rychlého startu je upravit (tak, že změníte portů pro komunikaci) tak, aby vyhovovaly jiné typy provozu aplikací.

## <a name="how-it-works"></a>Jak to funguje?
Na následujícím obrázku vidíte nastavení jak proxy aplikací místních zařízení a služby vám umožní připojení k začátku do konce mezi klienta SSH a procesy démon procesu SSH. Ve verzi public preview je v SDK pro jazyk C podporuje pouze datové proudy zařízení na straně zařízení. Tento rychlý start v důsledku toho pokrývá jenom pokyny ke spuštění aplikace proxy pro místní zařízení. Byste měli spustit doprovodné místní službu proxy aplikací, která je k dispozici v [ C# rychlý Start](./quickstart-device-streams-proxy-csharp.md) nebo [rychlý úvod k Node.js](./quickstart-device-streams-proxy-nodejs.md) vodítka.

![Alternativní text](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg "nastavení místní proxy server")

1. Místní služba proxy připojí k centru IoT a zahájí datovém proudu zařízení na cílové zařízení.

2. Místní zařízení proxy dokončení inicializace handshake datového proudu a vytvoří tunel začátku do konce datového proudu přes koncový bod streamování služby IoT Hub na straně služby.

3. Místní zařízení proxy serveru se připojí k démon procesu SSH (SSHD) naslouchá na portu 22 na zařízení (jde konfigurovat, jak je popsáno [níže](#run-the device-local-proxy-application)).

4. Místní služba proxy čeká pro nová připojení SSH od uživatele prostřednictvím naslouchání na určeném portu, který v tomto případě je port 2222 (Toto se taky dají konfigurovat, jak je popsáno [níže](#run-the-device-local-proxy-application)). Když se uživatel připojí pomocí klienta SSH, tunelové propojení umožňuje provoz SSH aplikace přenos mezi aplikacemi klientem a serverem SSH.

> [!NOTE]
> Provoz SSH odesílaných v rámci datového proudu zařízení bude tunelovým propojením přes koncový bod streamování služby IoT Hub spíše než odesílaných přímo mezi službou a zařízení. To poskytuje [tyto výhody](./iot-hub-device-streams-overview.md#benefits). Kromě toho na obrázku vidíte démon procesu SSH běží na stejném zařízení (nebo počítače) jako proxy pro místní zařízení. V tomto rychlém startu poskytnou IP adresu démon procesu SSH umožňuje zařízení místní proxy server a démon běžet na různých počítačích i.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Ve verzi preview streamovaných zařízení je momentálně podporována pouze pro vytvoření centra IoT hub v těchto oblastech:

  * **USA (střed)**
  * **Střed USA – EUAP**

* Nainstalujte [Visual Studio 2017](https://www.visualstudio.com/vs/) s povolenou sadou funkcí [Vývoj desktopových aplikací pomocí C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/).
* Nainstalujte nejnovější verzi [Git](https://git-scm.com/download/).

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

Pro účely tohoto rychlého startu budete používat [sadu SDK pro zařízení Azure IoT pro jazyk C](iot-hub-device-sdk-c-intro.md). Připravte vývojové prostředí klonovat a vytvářet [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) z Githubu. Ukázkový kód, který se používá v tomto rychlém startu, je součástí sady SDK na GitHubu. 

1. Stáhněte si [sestavovací systém CMake](https://cmake.org/download/). Ověření staženého binární soubor pomocí hodnoty kryptografické hodnoty hash, která odpovídá verzi, kterou stáhnete. Kryptografická hodnota hash hodnoty jsou také umístěny z odkazu CMake již k dispozici.

    Následující příklad používá prostředí Windows PowerShell k ověření kryptografické hodnoty hash verze 3.13.4 x64 distribuce MSI:

    ```powershell
    PS C:\Downloads> $hash = get-filehash .\cmake-3.13.4-win64-x64.msi
    PS C:\Downloads> $hash.Hash -eq "64AC7DD5411B48C2717E15738B83EA0D4347CD51B940487DFF7F99A870656C09"
    True
    ```

    Následující hodnoty hash pro verzi 3.13.4 byly uvedeny na webu CMake v době psaní tohoto textu:

    ```
    563a39e0a7c7368f81bfa1c3aff8b590a0617cdfe51177ddc808f66cc0866c76  cmake-3.13.4-Linux-x86_64.tar.gz
    7c37235ece6ce85aab2ce169106e0e729504ad64707d56e4dbfc982cb4263847  cmake-3.13.4-win32-x86.msi
    64ac7dd5411b48c2717e15738b83ea0d4347cd51b940487dff7f99a870656c09  cmake-3.13.4-win64-x64.msi
    ```

    Je důležité, aby požadavky na sadu Visual Studio (Visual Studio a sada funkcí Vývoj desktopových aplikací pomocí C++) byly na vašem počítači nainstalované ještě **před** zahájením instalace `CMake`. Jakmile jsou požadované součásti k dispozici a stažený soubor je ověřený, nainstalujte sestavovací systém CMake.

2. Otevřete prostředí příkazového řádku nebo Git Bash. Spusťte následující příkaz pro naklonování úložiště GitHub sady [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c):
    
    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```
    Velikost tohoto úložiště je aktuálně přibližně 220 MB. Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.

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

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registrování zařízení

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V této části zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell s [rozšířením IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest).

1. Ve službě Azure Cloud Shell spusťte následující příkazy pro přidání rozšíření rozhraní příkazového řádku IoT Hub a vytvoření identity zařízení. 

   **YourIoTHubName**: Nahraďte tento zástupný text pod názvem, který jste vybrali pro službu IoT hub.

   **Mojezařízení**: Toto je název pro registrovaná zařízení. Použijte Mojezařízení, jak je znázorněno. Pokud si zvolíte jiný název zařízení, budete ho muset používat v průběhu celého článku a aktualizovat název zařízení v ukázkových aplikacích, než je spustíte.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Spuštěním následujícího příkazu ve službě Azure Cloud Shell získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:

   **YourIoTHubName**: Nahraďte tento zástupný text pod názvem, který jste vybrali pro službu IoT hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, která vypadá jako v následujícím příkladu:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v tomto rychlém startu.

## <a name="ssh-to-a-device-via-device-streams"></a>Přístup k zařízení prostřednictvím datových proudů zařízení přes SSH

### <a name="run-the-device-local-proxy-application"></a>Spuštění aplikace proxy pro místní zařízení

1. Upravte zdrojový soubor `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample/iothub_client_c2d_streaming_proxy_sample.c` a poté zadejte připojovací řetězec zařízení, cílové zařízení IP nebo název hostitele a port 22 pro SSH:

   ```C
   /* Paste in the your iothub connection string  */
   static const char* connectionString = "[Connection string of IoT Hub]";
   static const char* localHost = "[IP/Host of your target machine]"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

2. Kompilace vzorku:

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

3. Zkompilovaný program spouštět v zařízení:

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

Jak je popsáno [dříve](#how-it-works), zřízení začátku do konce datového proudu k tunelování SSH provoz vyžaduje místní proxy server na každém konci (i ve službě a zařízení). Ve verzi public preview je v C SDK pro IoT Hub podporuje pouze datové proudy zařízení na straně zařízení. Sestavte a spusťte místní službu proxy serveru, postupujte podle kroků uvedených v [ C# rychlý Start](./quickstart-device-streams-proxy-csharp.md) nebo [rychlý úvod k Node.js](./quickstart-device-streams-proxy-nodejs.md).

### <a name="establish-an-ssh-session"></a>Relaci SSH

Po spuštění proxy serverů s místním zařízení a služba používat váš klientský program SSH a připojení k proxy serveru místní služby na port 2222 (namísto démon procesu SSH přímo).

```cmd/sh
ssh <username>@localhost -p 2222
```

V tomto okamžiku zobrazí se výzva k přihlášení SSH k zadání přihlašovacích údajů.

Výstup na místní zařízení proxy serveru, který se připojuje k proces démon programu SSH v konzole `IP_address:22`: ![Alternativní text](./media/quickstart-device-streams-proxy-c/device-console-output.PNG "výstupní zařízení místní proxy server")

Výstup programu klienta SSH konzole (klient SSH komunikuje se proces démon programu SSH propojením na port 22, který služba místní proxy server naslouchá na): ![Alternativní text](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png "výstup klienta SSH")

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste nastavení služby IoT hub, zaregistrovali zařízení, nasazení zařízení – a místní službu proxy program vytvořit datový proud zařízení prostřednictvím služby IoT Hub a použít proxy serverů k tunelování provoz SSH.

Další informace o datových proudů zařízení pomocí odkazů níže:

> [!div class="nextstepaction"]
> [Přehled datových proudů zařízení](./iot-hub-device-streams-overview.md)
