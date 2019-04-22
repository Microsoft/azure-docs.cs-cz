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
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: a459473e04f9cbf3b11b75f3b9dbea2732455084
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59005432"
---
# <a name="quickstart-sshrdp-over-iot-hub-device-streams-using-nodejs-proxy-application-preview"></a>Rychlý start: SSH nebo RDP over datové proudy zařízení služby IoT Hub pomocí Node.js proxy aplikace (preview)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub v současné době podporuje datové proudy zařízení jako [funkce ve verzi preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Datové proudy zařízení služby IoT Hub](./iot-hub-device-streams-overview.md) povolit zařízení a služeb aplikacím komunikovat způsobem, firewallem procházející a zabezpečené. Tato příručka rychlý start popisuje provádění aplikace Node.js proxy server běžící na straně služby umožňují provoz SSH a RDP k odeslání do zařízení v rámci datového proudu zařízení. Zobrazit [tady](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp) přehledné informace o nastavení. Ve verzi public preview je v sady Node.js SDK podporuje pouze datové proudy zařízení na straně služby. V důsledku toho této úvodní příručky pokrývá jenom pokyny ke spuštění místní služby proxy serveru. Měli byste spustit doprovodných zařízení místní proxy server, který je k dispozici v [rychlý start C](./quickstart-device-streams-proxy-c.md) nebo [ C# rychlý Start](./quickstart-device-streams-proxy-csharp.md) vodítka.

Nejprve popisujeme nastavení pro SSH (s použitím port 22). Pak zjistíte, jak upravit nastavení pro protokol RDP, (ta používá port 3389). Protože zařízení datové proudy jsou aplikace a bez ohledu na protokol, se stejným vzorkem lze upravit tak, aby vyhovovaly jiné typy provozu aplikace klient/server (obvykle úpravou komunikační port).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Ve verzi preview streamovaných zařízení je momentálně podporována pouze pro vytvoření centra IoT hub v těchto oblastech:

  - **USA (střed)**
  - **Střed USA – EUAP**

Ke spuštění aplikace místní služby v rámci tohoto rychlého startu budete potřebovat Node.js verze 4.x.x nebo novější na vývojovém počítači.

Node.js pro různé platformy si můžete stáhnout z webu [nodejs.org](https://nodejs.org).

Aktuální verzi Node.js na počítači používaném pro vývoj můžete ověřit pomocí následujícího příkazu:

```
node --version
```

Spusťte následující příkaz pro přidání rozšíření Microsoft Azure IoT pro Azure CLI do instance služby Cloud Shell. Rozšíření IOT přidá služby IoT Hub, IoT Edge a IoT zařízení zřizování služby (DPS) konkrétní příkazy rozhraní příkazového řádku Azure.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Pokud jste to ještě neudělali, stáhněte si ukázkový projekt Node.js z webu https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip a extrahujte archiv ZIP.

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Pokud jste dokončili předchozí [rychlý start: Odesílání telemetrických dat ze zařízení do služby IoT hub](quickstart-send-telemetry-node.md), můžete tento krok přeskočit.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registrování zařízení

Pokud jste dokončili předchozí [rychlý start: Odesílání telemetrických dat ze zařízení do služby IoT hub](quickstart-send-telemetry-node.md), můžete tento krok přeskočit.

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell.

1. Spusťte následující příkaz v Azure Cloud Shell vytvořte identitu zařízení.

   **YourIoTHubName**: Nahraďte tento zástupný text pod názvem, který jste zvolili pro službu IoT hub.

   **Mojezařízení**: Toto je název pro registrovaná zařízení. Použijte Mojezařízení, jak je znázorněno. Pokud si zvolíte jiný název zařízení, budete ho muset používat v průběhu celého článku a aktualizovat název zařízení v ukázkových aplikacích, než je spustíte.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Potřebujete také _připojovací řetězec služby_, který back-endové aplikaci umožní připojení k vašemu centru IoT a načtení zpráv. Následující příkaz načte připojovací řetězec služby pro vaše centrum IoT:

    **YourIoTHubName**: Nahraďte tento zástupný text pod názvem, který jste zvolili pro službu IoT hub.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Poznamenejte si vrácené hodnoty, který vypadá takto:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>Přístup k zařízení prostřednictvím datových proudů zařízení přes SSH

### <a name="run-the-device-local-proxy"></a>Spusťte místní zařízení proxy serveru

Jak už bylo zmíněno dříve, SDK IoT Hub pro Node.js na straně služby podporuje pouze datové proudy zařízení. Pro místní zařízení aplikaci pomocí doprovodných zařízení proxy serveru sady k dispozici v [rychlý start C](./quickstart-device-streams-proxy-c.md) nebo [ C# rychlý Start](./quickstart-device-streams-proxy-csharp.md) vodítka. Ujistěte se, že je spuštěný proxy server místní zařízení než budete pokračovat k dalšímu kroku.

### <a name="run-the-service-local-proxy"></a>Spuštění místní služby proxy

Za předpokladu, že [zařízení místní proxy](#run-the-device-local-proxy) je spuštěný, postupujte podle kroků níže spusťte místní službu proxy napsané v Node.js.

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
  Změňte tak, aby odpovídaly zařízení ID a připojovací řetězec výše uvedených hodnot.

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

Výstup konzoly na místní službu po navázání relace SSH (služba místní proxy server naslouchá na portu 2222): ![Alternativní text](./media/quickstart-device-streams-proxy-nodejs/service-console-output.PNG "terminálu výstup SSH")

Výstup programu klienta SSH konzole (klient SSH komunikuje se proces démon programu SSH na port 22, kde služba místní proxy server naslouchá připojením): ![Alternativní text](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.PNG "výstup klienta SSH")

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
