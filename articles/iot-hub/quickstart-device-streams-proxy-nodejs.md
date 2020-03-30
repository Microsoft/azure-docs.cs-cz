---
title: Azure IoT Hub zařízení datových proudů Node.js rychlý start pro SSH a RDP
description: V tomto rychlém startu spustíte ukázkovou aplikaci Node.js, která funguje jako proxy server pro povolení scénářů SSH a RDP přes datové proudy zařízení služby IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: c372a0a09fd3143f570aa4b316c9191e617c69e2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675462"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-nodejs-proxy-application-preview"></a>Úvodní příručka: Povolení SSH a RDP přes datový proud zařízení služby IoT Hub pomocí proxy aplikace Node.js (preview)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

V tomto rychlém startu povolíte přenosy zabezpečeného prostředí (SSH) a protokolu RDP (RDP), které mají být odeslány do zařízení prostředpou datového proudu zařízení. Datové proudy zařízení Azure IoT Hub umožňují aplikacím služeb a zařízení komunikovat zabezpečeným způsobem a způsobem šetrným k bráně firewall. Tento rychlý start popisuje spuštění proxy aplikace Node.js, která je spuštěna na straně služby. Během veřejné verze Preview podporuje sada SDK node.js streamy zařízení pouze na straně služby. V důsledku toho tento rychlý start popisuje pokyny ke spuštění pouze aplikace proxy místní služby.

## <a name="prerequisites"></a>Požadavky

* Dokončení [povolit SSH a RDP přes datové proudy zařízení Služby IoT Hub pomocí aplikace proxy C](./quickstart-device-streams-proxy-c.md) nebo [povolit ssh a RDP přes datové proudy zařízení IoT Hub pomocí aplikace proxy Jazyka C#](./quickstart-device-streams-proxy-csharp.md).

* Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Node.js 10+](https://nodejs.org).

* [Ukázkový projekt Node.js](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip).

Aktuální verzi souboru Node.js můžete ověřit ve vývojovém počítači pomocí následujícího příkazu:

```cmd/sh
node --version
```

Microsoft Azure IoT Hub aktuálně podporuje datové proudy zařízení jako [funkci náhledu](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!IMPORTANT]
> Náhled datových proudů zařízení je momentálně podporovaný jenom pro Centra IoT Hub vytvořených v následujících oblastech:
>
> * USA – střed
> * Centrální US EUAP
> * Severní Evropa
> * Jihovýchodní Asie
  
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Přidání rozšíření Azure IoT

Přidejte rozšíření Azure IoT pro Azure CLI do instance Cloud Shell spuštěním následujícího příkazu. Rozšíření IoT přidá do rozhraní příkazového příkazu Azure CLI služby IoT Hub, IoT Edge a Služby zřizování zařízení IoT (DPS).

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Pokud jste dokončili předchozí [Rychlý start: Odesílání telemetrických dat ze zařízení do centra IoT](quickstart-send-telemetry-node.md), můžete tento krok přeskočit.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrování zařízení

Pokud jste dokončili [úvodní příručku: Odesílat telemetrii ze zařízení do služby IoT hub](quickstart-send-telemetry-node.md), můžete tento krok přeskočit.

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V této části použijete Azure Cloud Shell k registraci simulované zařízení.

1. Chcete-li vytvořit identitu zařízení, spusťte v prostředí Cloud Shell následující příkaz:

   > [!NOTE]
   > * Nahraďte zástupný symbol *YourIoTHubName* názvem, který jste zvolili pro centrum IoT.
   > * Pro název zařízení, které registrujete, je doporučeno použít *MyDevice,* jak je znázorněno. Pokud pro své zařízení zvolíte jiný název, použijte tento název v celém tomto článku a aktualizujte název zařízení v ukázkových aplikacích před jejich spuštěním.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Chcete-li povolit back-endové aplikaci pro připojení k centru IoT hub a načíst zprávy, potřebujete také *připojovací řetězec služby*. Následující příkaz načte řetězec pro vaše centrum IoT:

   > [!NOTE]
   > Nahraďte zástupný symbol *YourIoTHubName* názvem, který jste zvolili pro centrum IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

   Všimněte si vrácené služby připojovací řetězec pro pozdější použití v tomto rychlém startu. Soubor bude vypadat jako v následujícím příkladu:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH do zařízení prostřednictvím datových proudů zařízení

V této části vytvoříte end-to-end datový proud pro tunelové propojení s provozem SSH.

### <a name="run-the-device-local-proxy-application"></a>Spuštění aplikace proxy místního zařízení

Jak již bylo zmíněno dříve, sada SDK služby IoT Hub Node.js podporuje datové proudy zařízení pouze na straně služby. Pro místní aplikaci zařízení použijte aplikaci proxy zařízení, která je k dispozici v jednom z následujících rychlých startů:

   * [Povolení datových proudů zařízení SSH a RDP přes službu IoT Hub pomocí aplikace proxy C](./quickstart-device-streams-proxy-c.md)
   * [Povolení datových proudů zařízení SSH a RDP přes službu IoT Hub pomocí aplikace proxy jazyka C#](./quickstart-device-streams-proxy-csharp.md) 

Než přejdete k dalšímu kroku, ujistěte se, že je spuštěna místní proxy aplikace zařízení. Přehled nastavení naleznete v tématu [Místní ukázka proxy .](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp)

### <a name="run-the-service-local-proxy-application"></a>Spuštění aplikace proxy místní služby

Tento článek popisuje nastavení pro SSH (pomocí portu 22) a pak popisuje, jak upravit nastavení pro RDP (který používá port 3389). Vzhledem k tomu, že datové proudy zařízení jsou nezávislá na aplikacích a protokolech, můžete upravit stejnou ukázku tak, aby vyhovovala jiným typům přenosů aplikací klient-server, obvykle úpravou komunikačního portu.

Při spuštění aplikace proxy místní ho zařízení spusťte aplikaci proxy proxy pro místní službu, která je zapsána v souboru Node.js, následujícím způsobem v místním okně terminálu:

1. Pro proměnné prostředí zadejte pověření služby, ID cílového zařízení, kde je spuštěn daemon SSH a číslo portu proxy serveru, který běží na zařízení.

   ```
   # In Linux
   export IOTHUB_CONNECTION_STRING="{ServiceConnectionString}"
   export STREAMING_TARGET_DEVICE="MyDevice"
   export PROXY_PORT=2222

   # In Windows
   SET IOTHUB_CONNECTION_STRING={ServiceConnectionString}
   SET STREAMING_TARGET_DEVICE=MyDevice
   SET PROXY_PORT=2222
   ```

   Změňte zástupný symbol ServiceConnectionString tak, aby odpovídal vašemu připojovacímu řetězci služby, a **MyDevice** tak, aby odpovídalo ID vašeho zařízení, pokud jste dali svému jinému názvu.

1. Přejděte `Quickstarts/device-streams-service` do adresáře ve složce rozvrácený projekt. Ke spuštění aplikace proxy pro místní službu použijte následující kód:

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   # Run the service-local proxy application
   node proxy.js
   ```

### <a name="ssh-to-your-device-via-device-streams"></a>SSH do vašeho zařízení prostřednictvím datových proudů zařízení

V Linuxu spusťte `ssh $USER@localhost -p 2222` SSH pomocí na terminálu. V systému Windows použijte svého oblíbeného klienta SSH (například PuTTY).

Výstup konzoly na místní služby po nastavení relace SSH (aplikace proxy místní služby naslouchá na portu 2222):

![Výstup terminálu SSH](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

Konzolový výstup klientské aplikace SSH (klient SSH komunikuje s daemonem SSH připojením k portu 22, kde naslouchá aplikace proxy místní služby):

![Výstup klienta SSH](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>RDP do vašeho zařízení prostřednictvím datových proudů zařízení

Nyní použijte klientskou aplikaci RDP a připojte se k proxy služby na portu 2222, což je libovolný port, který jste zvolili dříve.

> [!NOTE]
> Ujistěte se, že je proxy zařízení správně nakonfigurovánpro rdp a nakonfigurován s portem RDP 3389.

![Klient RDP se připojuje k místní aplikaci proxy služby](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu nastavíte službu IoT hub, zaregistrujete zařízení a nasadíte aplikaci proxy služby, která umožňuje RDP a SSH na zařízení IoT. Provoz RDP a SSH bude tunelován prostřednictvím datového proudu zařízení přes centrum IoT. Tento proces eliminuje potřebu přímého připojení k zařízení.

Další informace o streamech zařízení najdete v tématu:

> [!div class="nextstepaction"]
> [Přehled datových proudů zařízení](./iot-hub-device-streams-overview.md)
