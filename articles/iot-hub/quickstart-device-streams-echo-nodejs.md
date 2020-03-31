---
title: Komunikace s aplikací na zařízení v node.js pomocí datových proudů zařízení Azure IoT Hub
description: V tomto rychlém startu spustíte aplikaci na straně služby Node.js, která komunikuje se zařízením IoT prostřednictvím datového proudu zařízení.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 0757c5eb8639e4a864b049adc92c97a7cf69adba
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675509"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>Úvodní příručka: Komunikace s aplikací zařízení v node.js prostřednictvím datových proudů zařízení služby IoT Hub (náhled)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

V tomto rychlém startu spustíte aplikaci na straně služby a nastavíte komunikaci mezi zařízením a službou pomocí datových proudů zařízení. Datové proudy zařízení Azure IoT Hub umožňují aplikacím služeb a zařízení komunikovat zabezpečeným způsobem a způsobem šetrným k bráně firewall. Během veřejné verze Preview podporuje sada SDK Node.js pouze datové proudy zařízení na straně služby. V důsledku toho tento rychlý start zahrnuje pouze pokyny ke spuštění aplikace na straně služby.

## <a name="prerequisites"></a>Požadavky

* Dokončení [komunikovat do aplikací zařízení v C prostřednictvím datových proudů zařízení služby IoT Hub](./quickstart-device-streams-echo-c.md) nebo [komunikovat s aplikacemi zařízení v C# prostřednictvím datových proudů zařízení služby IoT Hub](./quickstart-device-streams-echo-csharp.md).

* Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Node.js 10+](https://nodejs.org).

* [Ukázkový projekt Node.js](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip).

Aktuální verzi Node.js na počítači používaném pro vývoj můžete ověřit pomocí následujícího příkazu:

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

Spusťte následující příkaz a přidejte rozšíření Microsoft Azure IoT extension pro Azure CLI do instance Cloud Shellu. Rozšíření IoT přidá příkazy Služby IoT Hub, IoT Edge a Služby dps (IoT Device Provisioning) do rozhraní příkazového příkazu Azure CLI.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Pokud jste dokončili předchozí [Rychlý start: Odesílání telemetrických dat ze zařízení do centra IoT](quickstart-send-telemetry-node.md), můžete tento krok přeskočit.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrování zařízení

Pokud jste dokončili předchozí [Rychlý start: Odesílání telemetrických dat ze zařízení do centra IoT](quickstart-send-telemetry-node.md), můžete tento krok přeskočit.

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell.

1. Spusťte následující příkaz v Azure Cloud Shell a vytvořte identitu zařízení.

   **YourIoTHubName**: Nahraďte tento zástupný symbol níže názvem, který jste vybrali pro svůj centr IoT.

   **MyDevice**: Toto je název zařízení, které registrujete. Doporučujese používat **MyDevice,** jak je znázorněno. Pokud pro své zařízení zvolíte jiný název, musíte tento název použít v celém tomto článku a aktualizovat název zařízení v ukázkových aplikacích před jejich spuštěním.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

2. Potřebujete také *připojovací řetězec služby*, který back-endové aplikaci umožní připojení k vašemu centru IoT a načtení zpráv. Následující příkaz načte připojovací řetězec služby pro vaše centrum IoT:

    **YourIoTHubName**: Nahraďte tento zástupný symbol níže názvem, který jste vybrali pro svůj centr IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Všimněte si vrácené služby připojovací řetězec pro pozdější použití v tomto rychlém startu. Soubor bude vypadat jako v následujícím příkladu:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Komunikace mezi zařízením a službou prostřednictvím datových proudů zařízení

V této části spustíte aplikaci na straně zařízení i aplikaci na straně služby a komunikujete mezi nimi.

### <a name="run-the-device-side-application"></a>Spuštění aplikace na straně zařízení

Jak již bylo zmíněno dříve, sada SDK node.js služby IoT Hub podporuje pouze datové proudy zařízení na straně služby. Pro aplikaci na straně zařízení použijte jeden z doprovodných programů zařízení, které jsou k dispozici v těchto rychlých startech:

* [Komunikace s aplikacemi zařízení v C prostřednictvím datových proudů zařízení služby IoT Hub](./quickstart-device-streams-echo-c.md)

* [Komunikace s aplikacemi zařízení v C# prostřednictvím datových proudů zařízení služby IoT Hub](./quickstart-device-streams-echo-csharp.md)

Ujistěte se, že aplikace na straně zařízení běží před pokračováním k dalšímu kroku.

### <a name="run-the-service-side-application"></a>Spuštění aplikace na straně služby

Aplikace Node.js na straně služby v tomto rychlém startu má následující funkce:

* Vytvoří datový proud zařízení do zařízení IoT.
* Přečte vstup z příkazového řádku a odešle jej do aplikace zařízení, která jej bude opakovat zpět.

Kód bude demonstrovat proces zahájení datového proudu zařízení, jakož i jak jej použít k odesílání a přijímání dat.

Za předpokladu, že je spuštěna aplikace na straně zařízení, postupujte podle následujících kroků v okně místního terminálu a spusťte aplikaci na straně služby v souboru Node.js:

* Zadejte pověření služby a ID zařízení jako proměnné prostředí.
 
   ```cmd/sh
   # In Linux
   export IOTHUB_CONNECTION_STRING="{ServiceConnectionString}"
   export STREAMING_TARGET_DEVICE="MyDevice"

   # In Windows
   SET IOTHUB_CONNECTION_STRING={ServiceConnectionString}
   SET STREAMING_TARGET_DEVICE=MyDevice
   ```
  
   Změňte zástupný symbol ServiceConnectionString tak, aby odpovídal vašemu připojovacímu řetězci služby, a **MyDevice** tak, aby odpovídalo ID vašeho zařízení, pokud jste dali svému jinému názvu.

* Přejděte `Quickstarts/device-streams-service` do rozepnuté složky projektu a spusťte ukázku pomocí uzlu.

   ```cmd/sh
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
    
   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   node echo.js
   ```

Na konci posledního kroku program na straně služby zahájí datový proud do vašeho zařízení a po vytvoření odešle vyrovnávací paměť řetězce do služby přes datový proud. V této ukázce program na `stdin` straně služby jednoduše přečte na terminálu a odešle jej do zařízení, které jej pak bude echo zpět. To ukazuje úspěšnou obousměrnou komunikaci mezi těmito dvěma aplikacemi.

![Výstup konzoly na straně služby](./media/quickstart-device-streams-echo-nodejs/service-console-output.png)

Poté můžete program ukončit opětovným stisknutím klávesy Enter.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu nastavíte službu IoT hub, zaregistrujete zařízení, nastavíte datový proud zařízení mezi aplikacemi na straně zařízení a služby a pomocí datového proudu odesíláte data tam a zpět mezi aplikacemi.

Další informace o streamech zařízení najdete v následujících odkazech:

> [!div class="nextstepaction"]
> [Přehled datových proudů zařízení](./iot-hub-device-streams-overview.md) 