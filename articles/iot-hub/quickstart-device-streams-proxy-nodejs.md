---
title: Pro zařízení Azure IoT Hub streamuje rychlý úvod k Node.js pro SSH a protokolu RDP (preview) | Dokumentace Microsoftu
description: V tomto rychlém startu spustíte ukázkovou aplikaci Node.js, která funguje jako proxy server, aby se povolily scénáře SSH a protokolu RDP přes službu IoT Hub zařízení datové proudy.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 83339273d9161c3947df191d10e788980db39b28
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445980"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-nodejs-proxy-application-preview"></a>Rychlý start: Povolit SSH a protokolu RDP přes proud zařízení služby IoT Hub pomocí proxy aplikace v Node.js (preview)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub v současné době podporuje datové proudy zařízení jako [funkce ve verzi preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Datové proudy zařízení služby IoT Hub](./iot-hub-device-streams-overview.md) povolit zařízení a služeb aplikacím komunikovat způsobem, firewallem procházející a zabezpečené. 

Tento rychlý start popisuje provádění aplikace Node.js proxy serveru, na kterém běží na straně služby Secure Shell (SSH) a provoz protokolu RDP (Remote Desktop) se neodesílají do zařízení v rámci datového proudu zařízení. Přehled nastavení, naleznete v tématu [místní ukázka Proxy](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp). 

Ve verzi public preview sady Node.js SDK podporuje datové proudy zařízení na straně služby. V důsledku toho tento rychlý start popisuje pokyny ke spuštění pouze místní služby proxy aplikace. Spuštění aplikace proxy pro místní zařízení, najdete v tématech:  

   * [Povolit SSH a protokolu RDP přes datové proudy zařízení služby IoT Hub pomocí proxy aplikace v jazyce C](./quickstart-device-streams-proxy-c.md)
   * [Povolit SSH a RDP datové proudy zařízení služby IoT Hub s využitím C# proxy aplikací](./quickstart-device-streams-proxy-csharp.md)

Tento článek popisuje nastavení pro SSH (s použitím port 22) a pak popisuje, jak upravit nastavení pro protokol RDP, (ta používá port 3389). Protože zařízení datové proudy nezávisejí na aplikaci a protokol-, můžete upravit stejného vzorku tak, aby vyhovovaly jiné typy provozu aplikaci klient server, obvykle tak, že upravíte komunikační port.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Ve verzi preview streamovaných zařízení je momentálně podporována pouze pro IoT hub, které jsou vytvořeny v těchto oblastech:

  * Střední USA
  * Střed USA – EUAP

* Ke spuštění aplikace místní služby v rámci tohoto rychlého startu, potřebujete Node.js v10.x.x nebo později na vývojovém počítači.
  * Stáhněte si [Node.js](https://nodejs.org) pro různé platformy.
  * Zkontrolujte aktuální verzi Node.js na vývojovém počítači s použitím následujícího příkazu:

   ```
   node --version
   ```

* Přidáte rozšíření Azure IoT pro Azure CLI do instance služby Cloud Shell spuštěním následujícího příkazu. Přidá rozšíření IOT služby IoT Hub, IoT Edge a IoT zařízení zřizování služby (DPS)-konkrétní příkazy rozhraní příkazového řádku Azure.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

* Pokud jste tak již neučinili, [stáhněte si ukázkový projekt Node.js](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip) a extrahujte archiv ZIP.

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Pokud jste dokončili předchozí [rychlý start: Odesílání telemetrických dat ze zařízení do služby IoT hub](quickstart-send-telemetry-node.md), můžete tento krok přeskočit.

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registrování zařízení

Pokud jste dokončili [rychlý start: Odesílání telemetrických dat ze zařízení do služby IoT hub](quickstart-send-telemetry-node.md), můžete tento krok přeskočit.

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V této části použít Azure Cloud Shell zaregistrovat simulované zařízení.

1. K vytvoření identity zařízení, spusťte následující příkaz ve službě Cloud Shell:

   > [!NOTE]
   > * Nahradit *YourIoTHubName* zástupný symbol název, který zvolíte pro službu IoT hub.
   > * Použití *Mojezařízení*, jak je znázorněno. Jedná se o název pro registrovaná zařízení. Pokud zvolíte jiný název pro vaše zařízení, použijte tento název v rámci tohoto článku a aktualizujte název zařízení v ukázkové aplikace před spuštěním je.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

1. Chcete-li povolit back endové aplikace k připojení ke službě IoT hub a načítání zpráv, budete potřebovat *připojovací řetězec služby*. Následující příkaz načte řetězec služby IoT hub:

   > [!NOTE]
   > Nahradit *YourIoTHubName* zástupný symbol název, který zvolíte pro službu IoT hub.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Všimněte si, vrácená hodnota pro pozdější použití v rámci tohoto rychlého startu. Soubor bude vypadat jako v následujícím příkladu:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>Přístup k zařízení prostřednictvím datových proudů zařízení přes SSH

V této části vytvoříte začátku do konce datového proudu k tunelování provoz SSH.

### <a name="run-the-device-local-proxy-application"></a>Spuštění aplikace proxy pro místní zařízení

Jak už bylo zmíněno dříve, SDK IoT Hub pro Node.js podporuje datové proudy zařízení na straně služby. Pro místní zařízení aplikaci pomocí proxy aplikace zařízení, která je k dispozici v jednom z následujících šablon rychlý start:

   * [Povolit SSH a protokolu RDP přes datové proudy zařízení služby IoT Hub pomocí proxy aplikace v jazyce C](./quickstart-device-streams-proxy-c.md)
   * [Povolit SSH a RDP datové proudy zařízení služby IoT Hub s využitím C# proxy aplikací](./quickstart-device-streams-proxy-csharp.md) 

Než budete pokračovat k dalšímu kroku, ujistěte se, že je zařízení místní proxy aplikace spuštěna.

### <a name="run-the-service-local-proxy-application"></a>Spuštění místní služby proxy aplikace

S místní zařízení proxy aplikace spuštěná spusťte místní služby proxy aplikace, která je napsána v Node.js pomocí následujícího:

1. Pro proměnné prostředí zadejte svoje přihlašovací údaje služby, ID cílové zařízení, ve kterém se spustí proces démon programu SSH a číslo portu pro proxy server, na kterém běží na zařízení.

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

   Změňte předchozí hodnoty tak, aby odpovídaly zařízení ID a připojovací řetězec.

1. Přejděte *šablon rychlý start – služby/zařízení – datové proudy* ve složce rozzipovaný projekt a spuštění místní služby proxy aplikace.

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   # Run the service-local proxy application
   node proxy.js
   ```

### <a name="ssh-to-your-device-via-device-streams"></a>SSH do vašeho zařízení prostřednictvím datových proudů zařízení

V systému Linux, spusťte SSH pomocí `ssh $USER@localhost -p 2222` na terminálu. Ve Windows pomocí svého oblíbeného klienta SSH (například PuTTY).

Výstup konzoly na místní službu po navázání relace SSH (místní služby proxy aplikace naslouchá na portu 2222):

![Výstup terminálu SSH](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

Klientské aplikace SSH výstupu konzoly (klient SSH komunikuje se proces démon programu SSH propojením na port 22, který naslouchá místní služby proxy aplikace):

![Výstup klienta SSH](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>Připojení RDP k zařízení prostřednictvím datových proudů zařízení

Nyní pomocí RDP klientské aplikace a připojení k proxy serveru služby na port 2222, libovolného portu, který jste zvolili dříve.

> [!NOTE]
> Ujistěte se, že vaše zařízení proxy je správně nakonfigurovaný pro protokol RDP a nakonfigurovanou RDP port 3389.

![Připojení klienta protokolu RDP do místní služby proxy aplikace](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste nastavení služby IoT hub, zaregistrovali zařízení a nasadit proxy aplikace služby k povolení protokolu RDP a SSH na zařízení IoT. Provoz protokolu RDP a SSH se tunelové propojení prostřednictvím datového proudu zařízení prostřednictvím služby IoT hub. Tento proces eliminuje nutnost přímého připojení k zařízení.

Další informace o zařízení datových proudů, naleznete v tématu:

> [!div class="nextstepaction"]
> [Přehled datových proudů zařízení](./iot-hub-device-streams-overview.md)
