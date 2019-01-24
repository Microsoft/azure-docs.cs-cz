---
title: Pro zařízení Azure IoT Hub streamuje rychlý úvod k Node.js pro SSH nebo RDP (preview) | Dokumentace Microsoftu
description: V tomto rychlém startu spustíte ukázkovou aplikaci Node.js, která funguje jako proxy server, aby se povolily scénáře SSH nebo RDP přes datové proudy zařízení služby IoT Hub.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/15/2019
ms.author: rezas
ms.openlocfilehash: 012fdfa4faf10cacaf85819517f358c1af1ab39d
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54830212"
---
# <a name="quickstart-sshrdp-over-iot-hub-device-streams-using-nodejs-proxy-application-preview"></a>Rychlý start: SSH nebo RDP over datové proudy zařízení služby IoT Hub pomocí Node.js proxy aplikace (preview)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

[Datové proudy zařízení služby IoT Hub](./iot-hub-device-streams-overview.md) povolit zařízení a služeb aplikacím komunikovat způsobem, firewallem procházející a zabezpečené. Tento rychlý start popisuje provádění aplikace Node.js proxy server běžící na straně služby umožňují provoz SSH a RDP k odeslání do zařízení v rámci datového proudu zařízení. Zobrazit [na této stránce](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp) přehledné informace o nastavení. Ve verzi public preview je v sady Node.js SDK podporuje pouze datové proudy zařízení na straně služby. Tento rychlý start v důsledku toho pokrývá jenom pokyny ke spuštění straně služby proxy serveru. Měli byste spustit doprovodné straně zařízení proxy serveru, který je k dispozici v [rychlý start C](./quickstart-device-streams-proxy-c.md) nebo [ C# rychlý Start](./quickstart-device-streams-proxy-csharp.md) vodítka.

Nejprve popisujeme nastavení pro SSH (pomocí portu `22`). Pak zjistíte, jak upravit nastavení pro protokol RDP, (ta používá port 3389). Protože zařízení datové proudy jsou aplikace a bez ohledu na protokol, lze upravit stejného vzorku (obvykle změnou portů pro komunikaci) tak, aby vyhovovaly jiné typy provozu aplikací.

Kód vám ukáže, inicializace a použití datového proudu zařízení a je možné použít opakovaně pro provoz aplikací také (jiné než RDP a SSH).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.


## <a name="prerequisites"></a>Požadavky

Ke spuštění aplikace straně služby v rámci tohoto rychlého startu budete potřebovat Node.js verze 4.x.x nebo novější na vývojovém počítači.

Node.js pro různé platformy si můžete stáhnout z webu [nodejs.org](https://nodejs.org).

Aktuální verzi Node.js na počítači používaném pro vývoj můžete ověřit pomocí následujícího příkazu:

```
node --version
```

Pokud jste to ještě neudělali, stáhněte si ukázkový projekt Node.js z webu https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip a extrahujte archiv ZIP.


## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Pokud jste dokončili předchozí [rychlý start: Odesílání telemetrických dat ze zařízení do služby IoT hub](quickstart-send-telemetry-node.md), můžete tento krok přeskočit.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]


## <a name="register-a-device"></a>Registrování zařízení

Pokud jste dokončili předchozí [rychlý start: Odesílání telemetrických dat ze zařízení do služby IoT hub](quickstart-send-telemetry-node.md), můžete tento krok přeskočit.

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell.

1. Ve službě Azure Cloud Shell spusťte následující příkazy pro přidání rozšíření rozhraní příkazového řádku IoT Hub a vytvoření identity zařízení. 

   **YourIoTHubName**: Nahraďte tento zástupný text pod názvem, který jste zvolili pro službu IoT hub.

   **Mojezařízení**: Toto je název pro registrovaná zařízení. Použijte Mojezařízení, jak je znázorněno. Pokud si zvolíte jiný název zařízení, budete ho muset používat v průběhu celého článku a aktualizovat název zařízení v ukázkových aplikacích, než je spustíte.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Potřebujete také _připojovací řetězec služby_, který back-endové aplikaci umožní připojení k vašemu centru IoT a načtení zpráv. Následující příkaz načte připojovací řetězec služby pro vaše centrum IoT:

    **YourIoTHubName**: Nahraďte tento zástupný text pod názvem, který jste zvolili pro službu IoT hub.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --hub-name YourIoTHubName
    ```

    Poznamenejte si vrácené hodnoty, který vypadá takto:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`


## <a name="ssh-to-a-device-via-device-streams"></a>Přístup k zařízení prostřednictvím datových proudů zařízení přes SSH

### <a name="run-the-device-side-proxy"></a>Spustit straně zařízení proxy serveru

Jak už bylo zmíněno dříve, SDK IoT Hub pro Node.js na straně služby podporuje pouze datové proudy zařízení. Aplikace na straně zařízení, pomocí doprovodných zařízení proxy serveru sady k dispozici v [rychlý start C](./quickstart-device-streams-proxy-c.md) nebo [ C# rychlý Start](./quickstart-device-streams-proxy-csharp.md) vodítka. Ujistěte se, že je spuštěný proxy server straně zařízení než budete pokračovat k dalšímu kroku.


### <a name="run-the-service-side-proxy"></a>Spusťte službu na straně serveru proxy

Za předpokladu, že je spuštěný proxy server straně zařízení, postupujte podle pokynů pro spuštění straně služby proxy napsané v Node.js:

- Zadejte svoje přihlašovací údaje služby, ID cílové zařízení, ve kterém se spustí proces démon programu SSH a číslo portu proxy serveru spuštěného v příslušném zařízení jako proměnné prostředí.
```
  # In Linux
  export IOTHUB_CONNECTION_STRING="<provide_your_service_connection_string>"
  export STREAMING_TARGET_DEVICE="MyDevice"
  export PROXY_PORT=2222

  # In Windows
  SET IOTHUB_CONNECTION_STRING=<provide_your_service_connection_string>
  SET STREAMING_TARGET_DEVICE=MyDevice
  SET PROXY_PORT=2222
```
Změna `MyDevice` ID zařízení jste zvolili pro vaše zařízení.

- Přejděte do `Quickstarts/device-streams-service` rozzipovaný projekt složky a spuštění místní služby proxy serveru.
```
  cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

  # Install the preview service SDK, and other dependencies
  npm install azure-iothub@streams-preview
  npm install

  # Run the service-local proxy application
  node proxy.js
```

### <a name="ssh-to-your-device-via-device-streams"></a>SSH do vašeho zařízení prostřednictvím datových proudů zařízení
V systému Linux, spusťte pomocí SSH `ssh $USER@localhost -p 2222` na terminálu. Ve Windows, pomocí svého oblíbeného klienta SSH (například PuTTY).

Výstup konzoly na straně služby po navázání relace SSH (služba místní proxy server naslouchá na portu 2222): ![Alternativní text](./media/quickstart-device-streams-proxy-nodejs/service-console-output.PNG "terminálu výstup SSH")


Výstup programu klienta SSH konzole (klient SSH komunikuje se proces démon programu SSH pomocí připojení k portu <code>22</code> kde service místní proxy server naslouchá na): ![Alternativní text](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.PNG "výstup klienta SSH")


### <a name="rdp-to-your-device-via-device-streams"></a>Připojení RDP k zařízení prostřednictvím datových proudů zařízení

Teď pomocí programu klienta protokolu RDP a připojení k proxy serveru služby na port 2222 (bylo k dispozici libovolného portu, který jste zvolili dříve).

> [!NOTE]
> Ujistěte se, že vaše zařízení proxy je správně nakonfigurovaný pro protokol RDP a nakonfigurovanou RDP port 3389.

![Alternativní text](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.PNG "klienta protokolu RDP se připojí k proxy serveru místní služby.")


## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste nastavení služby IoT hub, zaregistrovali zařízení a nasadili programu proxy server služby pro povolení protokolu RDP a SSH do zařízení IoT. Provoz protokolu RDP a SSH se tunelové propojení prostřednictvím datového proudu zařízení prostřednictvím služby IoT Hub. Tím se eliminuje nutnost přímého připojení k zařízení.

Další informace o datových proudů zařízení pomocí odkazů níže:

> [!div class="nextstepaction"]
> [Přehled datových proudů zařízení](./iot-hub-device-streams-overview.md)
