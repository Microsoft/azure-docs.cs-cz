---
title: Rychlý Start – streamy zařízení Azure IoT Hub Node.js rychlý Start pro SSH a RDP
description: V tomto rychlém startu spustíte ukázkovou Node.js aplikaci, která funguje jako proxy a povoluje scénáře SSH a RDP přes IoT Hub streamy zařízení.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: references_regions
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: ef45a6277adeff09a34fe22b7abeb21d3e603167
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624350"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-nodejs-proxy-application-preview"></a>Rychlý Start: povolení SSH a RDP přes datový proud IoT Hub zařízení pomocí proxy aplikace Node.js (Preview)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

V tomto rychlém startu povolíte odesílání Secure Shell (SSH) a protokol RDP (Remote Desktop Protocol) (RDP) do zařízení přes datový proud zařízení. Datové proudy zařízení v Azure IoT Hub umožňují aplikacím služeb a zařízením komunikovat zabezpečeným způsobem a bránou firewall. Tento rychlý Start popisuje spuštění aplikace Node.js proxy serveru, která běží na straně služby. Ve verzi Public Preview podporuje Node.js SDK pouze datové proudy zařízení na straně služby. V důsledku toho tento rychlý Start popisuje pokyny ke spuštění pouze místní proxy aplikace služby.

## <a name="prerequisites"></a>Požadavky

* Dokončení [Povolení protokolu SSH a RDP přes IoT Hub datových proudů zařízení pomocí aplikace proxy](./quickstart-device-streams-proxy-c.md) v jazyce C nebo [Povolení protokolu SSH a RDP přes IoT Hub datových proudů zařízení pomocí proxy aplikace jazyka C#](./quickstart-device-streams-proxy-csharp.md).

* Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Node.js 10 +](https://nodejs.org).

    Aktuální verzi Node.js můžete na svém vývojovém počítači ověřit pomocí následujícího příkazu:

    ```cmd/sh
    node --version
    ```

* [Vzorový Node.js projekt](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Microsoft Azure IoT Hub aktuálně podporuje streamy zařízení jako [funkci ve verzi Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!IMPORTANT]
> Verze Preview datových proudů zařízení je momentálně podporovaná jenom pro centra IoT vytvořená v následujících oblastech:
>
> * USA – střed
> * Střed USA EUAP
> * Severní Evropa
> * Jihovýchodní Asie

### <a name="add-azure-iot-extension"></a>Přidat rozšíření Azure IoT

Spuštěním následujícího příkazu přidejte rozšíření Azure IoT pro Azure CLI do instance Cloud Shell. Rozšíření IoT přidá do Azure CLI příkazy, které jsou specifické pro služby IoT Hub, IoT Edge a IoT Device Provisioning (DPS).

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Pokud jste dokončili předchozí [Rychlý start: Odesílání telemetrických dat ze zařízení do centra IoT](quickstart-send-telemetry-node.md), můžete tento krok přeskočit.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrace zařízení

Pokud jste dokončili [rychlý Start: poslat telemetrii ze zařízení do služby IoT Hub](quickstart-send-telemetry-node.md), můžete tento krok přeskočit.

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V této části použijete Azure Cloud Shell k registraci simulovaného zařízení.

1. Chcete-li vytvořit identitu zařízení, spusťte následující příkaz v Cloud Shell:

   > [!NOTE]
   > * Zástupný text *YourIoTHubName* nahraďte názvem, který jste zvolili pro Centrum IoT.
   > * Pro název zařízení, které zaregistrujete, se doporučuje používat *mojezařízení* , jak je znázorněno na obrázku. Pokud pro své zařízení zvolíte jiný název, použijte tento název v celém rámci tohoto článku a aktualizujte název zařízení v ukázkových aplikacích ještě předtím, než je spustíte.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Pokud chcete aplikaci pro back-end povolit připojení ke službě IoT Hub a načíst zprávy, budete potřebovat také *připojovací řetězec služby*. Následující příkaz načte řetězec pro Centrum IoT:

   > [!NOTE]
   > Zástupný text *YourIoTHubName* nahraďte názvem, který jste zvolili pro Centrum IoT.

    ```azurecli-interactive
    az iot hub connection-string show --policy-name service --hub-name {YourIoTHubName} --output table
    ```

   Všimněte si vráceného připojovacího řetězce služby pro pozdější použití v tomto rychlém startu. Soubor bude vypadat jako v následujícím příkladu:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH pro zařízení prostřednictvím datových proudů zařízení

V této části vytvoříte ucelený Stream pro tunelování provozu SSH.

### <a name="run-the-device-local-proxy-application"></a>Spuštění aplikace místní proxy zařízení

Jak bylo zmíněno dříve, sada IoT Hub Node.js SDK podporuje pouze datové proudy zařízení na straně služby. V případě aplikace pro místní zařízení použijte proxy aplikace zařízení, která je k dispozici v jednom z následujících rychlých startů:

   * [Povolení SSH a RDP přes IoT Hub datových proudů zařízení pomocí aplikace proxy v jazyce C](./quickstart-device-streams-proxy-c.md)
   * [Povolení SSH a RDP přes IoT Hub datových proudů zařízení pomocí proxy aplikace v jazyce C#](./quickstart-device-streams-proxy-csharp.md) 

Než přejdete k dalšímu kroku, ujistěte se, že je spuštěná aplikace proxy místního zařízení. Přehled nastavení najdete v tématu [Ukázka místního proxy serveru](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

### <a name="run-the-service-local-proxy-application"></a>Spuštění aplikace místní proxy serveru služby

Tento článek popisuje nastavení pro SSH (pomocí portu 22) a pak popisuje, jak upravit nastavení protokolu RDP (který používá port 3389). Vzhledem k tomu, že datové proudy zařízení jsou aplikace a protokol nezávislá, můžete stejnou ukázku upravit tak, aby vyhovovala jiným typům provozu klientských a serverových aplikací, a to obvykle změnou komunikačního portu.

V případě spuštěné aplikace proxy místního zařízení spusťte místní proxy aplikaci služby, která je napsaná v Node.js pomocí následujícího postupu v místním okně terminálu:

1. Pro proměnné prostředí zadejte přihlašovací údaje služby, ID cílového zařízení, kde se démon SSH spustí, a číslo portu proxy serveru, který je na zařízení spuštěný.

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

   Změňte zástupný symbol ServiceConnectionString tak, aby odpovídal vašemu připojovacímu řetězci služby, a **mojezařízení** tak, aby odpovídal vašemu ID zařízení, pokud jste zadali jiný název.

1. Přejděte do `Quickstarts/device-streams-service` adresáře ve složce projektu unzip. Pro spuštění aplikace proxy serveru místní služby použijte následující kód:

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   # Run the service-local proxy application
   node proxy.js
   ```

### <a name="ssh-to-your-device-via-device-streams"></a>SSH k zařízení prostřednictvím datových proudů zařízení

V systému Linux spusťte SSH pomocí nástroje `ssh $USER@localhost -p 2222` v terminálu. V systému Windows použijte svůj oblíbený klient SSH (například k disukázce).

Výstup konzoly v místní službě po vytvoření relace SSH (aplikace proxy serveru místní služby naslouchá na portu 2222):

![Výstup terminálu SSH](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

Výstup konzoly klientské aplikace SSH (klient SSH komunikuje s démonem SSH připojením k portu 22, kde aplikace proxy místní služby naslouchá):

![Výstup klienta SSH](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>Protokol RDP do zařízení prostřednictvím datových proudů zařízení

Teď použijte klientskou aplikaci RDP a připojte se k proxy služby na portu 2222, což je libovolný port, který jste si zvolili dříve.

> [!NOTE]
> Ujistěte se, že je váš proxy zařízení správně nakonfigurovaný pro RDP a nakonfigurovaný pomocí portu RDP 3389.

![Klient RDP se připojí k aplikaci proxy místní služby.](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu nastavíte službu IoT Hub, zaregistrovali zařízení a nasadili jste proxy aplikaci služby, která povoluje protokol RDP a SSH na zařízení IoT. Přenosy RDP a SSH se budou tunelovat prostřednictvím datového proudu zařízení prostřednictvím centra IoT. Tento proces eliminuje nutnost přímého připojení k zařízení.

Další informace o datových proudech zařízení najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Přehled streamů zařízení](./iot-hub-device-streams-overview.md)
