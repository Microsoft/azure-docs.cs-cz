---
title: Rychlý Start – komunikace s aplikací zařízení v Node.js s využitím streamování zařízení Azure IoT Hub
description: V tomto rychlém startu spustíte Node.js aplikaci na straně služby, která komunikuje se zařízením IoT přes datový proud zařízení.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 335014f032162866e4780bf1294ddcd108b4fd03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624384"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>Rychlý Start: komunikace s aplikací zařízení v Node.js prostřednictvím datových proudů zařízení IoT Hub (Preview)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

V tomto rychlém startu spustíte aplikaci na straně služby a nastavíte komunikaci mezi zařízením a službou pomocí datových proudů zařízení. Datové proudy zařízení v Azure IoT Hub umožňují aplikacím služeb a zařízením komunikovat zabezpečeným způsobem a bránou firewall. Během veřejné verze Preview podporuje Node.js SDK na straně služby pouze datové proudy zařízení. V důsledku toho se v tomto rychlém startu vztahují jenom na pokyny ke spuštění aplikace na straně služby.

## <a name="prerequisites"></a>Požadavky

* Dokončíní [komunikace s aplikacemi zařízení v C prostřednictvím IoT Hub datových proudů zařízení](./quickstart-device-streams-echo-c.md) nebo [komunikace s aplikacemi zařízení v jazyce C# prostřednictvím IoT Hub datových proudů zařízení](./quickstart-device-streams-echo-csharp.md).

* Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Node.js 10 +](https://nodejs.org).

    Aktuální verzi Node.js na počítači používaném pro vývoj můžete ověřit pomocí následujícího příkazu:

    ```cmd/sh
    node --version
    ```

* [Vzorový Node.js projekt](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

Microsoft Azure IoT Hub aktuálně podporuje streamy zařízení jako [funkci ve verzi Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!IMPORTANT]
> Verze Preview datových proudů zařízení je momentálně podporovaná jenom pro centra IoT vytvořená v následujících oblastech:
>
> * USA – střed
> * Střed USA EUAP
> * Severní Evropa
> * Jihovýchodní Asie

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Pokud jste dokončili předchozí [Rychlý start: Odesílání telemetrických dat ze zařízení do centra IoT](quickstart-send-telemetry-node.md), můžete tento krok přeskočit.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrace zařízení

Pokud jste dokončili předchozí [Rychlý start: Odesílání telemetrických dat ze zařízení do centra IoT](quickstart-send-telemetry-node.md), můžete tento krok přeskočit.

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell.

1. Spuštěním následujícího příkazu v Azure Cloud Shell vytvořte identitu zařízení.

   **YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který jste zvolili pro Centrum IoT.

   **Mojezařízení**: Jedná se o název zařízení, které zaregistrujete. Doporučuje se používat **mojezařízení** , jak je znázorněno na obrázku. Pokud pro vaše zařízení zvolíte jiný název, budete ho muset použít i v celém rámci tohoto článku a před jeho spuštěním aktualizovat název zařízení v ukázkových aplikacích.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

2. Potřebujete také *připojovací řetězec služby*, který back-endové aplikaci umožní připojení k vašemu centru IoT a načtení zpráv. Následující příkaz načte připojovací řetězec služby pro vaše centrum IoT:

    **YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který jste zvolili pro Centrum IoT.

    ```azurecli-interactive
    az iot hub connection-string show --policy-name service --name {YourIoTHubName} --output table
    ```

    Všimněte si vráceného připojovacího řetězce služby pro pozdější použití v tomto rychlém startu. Soubor bude vypadat jako v následujícím příkladu:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Komunikace mezi zařízením a službou prostřednictvím datových proudů zařízení

V této části spustíte jak aplikaci na straně zařízení, tak aplikaci na straně služby a komunikujete mezi nimi.

### <a name="run-the-device-side-application"></a>Spuštění aplikace na straně zařízení

Jak bylo zmíněno dříve, IoT Hub Node.js SDK podporuje pouze datové proudy zařízení na straně služby. V případě aplikace na straně zařízení použijte jeden z doprovodných programů zařízení, které jsou dostupné v těchto rychlých startech:

* [Komunikace s aplikacemi pro zařízení v C prostřednictvím datových proudů zařízení IoT Hub](./quickstart-device-streams-echo-c.md)

* [Komunikace s aplikacemi zařízení v jazyce C# prostřednictvím datových proudů zařízení IoT Hub](./quickstart-device-streams-echo-csharp.md)

Než budete pokračovat k dalšímu kroku, ujistěte se, že je spuštěná aplikace na straně zařízení.

### <a name="run-the-service-side-application"></a>Spuštění aplikace na straně služby

Aplikace Node.js na straně služby v tomto rychlém startu má následující funkce:

* Vytvoří datový proud zařízení pro zařízení IoT.
* Přečte vstup z příkazového řádku a pošle ho do aplikace zařízení, která ho vrátí zpátky.

Kód ukáže proces iniciace datového proudu zařízení a také způsob, jak ho použít k posílání a přijímání dat.

Za předpokladu, že je aplikace na straně zařízení spuštěná, postupujte podle následujících kroků v místním okně terminálu a spusťte aplikaci na straně služby v Node.js:

* Zadejte své přihlašovací údaje služby a ID zařízení jako proměnné prostředí.
 
   ```cmd/sh
   # In Linux
   export IOTHUB_CONNECTION_STRING="{ServiceConnectionString}"
   export STREAMING_TARGET_DEVICE="MyDevice"

   # In Windows
   SET IOTHUB_CONNECTION_STRING={ServiceConnectionString}
   SET STREAMING_TARGET_DEVICE=MyDevice
   ```
  
   Změňte zástupný symbol ServiceConnectionString tak, aby odpovídal vašemu připojovacímu řetězci služby, a **mojezařízení** tak, aby odpovídal vašemu ID zařízení, pokud jste zadali jiný název.

* Přejděte do `Quickstarts/device-streams-service` složky projektu getzip a spusťte ukázku pomocí Node.

   ```cmd/sh
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
    
   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   node echo.js
   ```

Na konci posledního kroku program na straně služby inicializuje Stream do vašeho zařízení a jakmile ho naváže, odešle službě přes datový proud vyrovnávací paměť řetězců. V této ukázce program na straně služby jednoduše přečte `stdin` v terminálu a odešle ho do zařízení, které se pak vrátí zpátky. Tím se ukazuje úspěšná obousměrná komunikace mezi těmito dvěma aplikacemi.

![Výstup konzoly na straně služby](./media/quickstart-device-streams-echo-nodejs/service-console-output.png)

Program pak můžete ukončit stisknutím klávesy ENTER.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu nastavíte centrum IoT, zaregistrované zařízení, navázali jste datový proud zařízení mezi aplikacemi na straně zařízení a služeb a pomocí tohoto datového proudu odesíláte data mezi aplikacemi.

Další informace o datových proudech zařízení získáte pomocí odkazů níže:

> [!div class="nextstepaction"]
> [Přehled streamů zařízení](./iot-hub-device-streams-overview.md)
