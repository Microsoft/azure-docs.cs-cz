---
title: Komunikace na zařízení aplikaci v Node.js prostřednictvím datových proudů zařízení služby IoT Hub (preview) | Dokumentace Microsoftu
description: V tomto rychlém startu spustíte aplikací Node.js straně služby, které komunikuje s zařízení IoT prostřednictvím datového proudu zařízení.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: d9d083569d648b2bd22438f295a640b4e8d03b77
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2019
ms.locfileid: "66733245"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>Rychlý start: Komunikace na zařízení aplikaci v Node.js prostřednictvím datových proudů zařízení služby IoT Hub (preview)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Microsoft Azure IoT Hub v současné době podporuje datové proudy zařízení jako [funkce ve verzi preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Datové proudy zařízení služby IoT Hub](./iot-hub-device-streams-overview.md) povolit zařízení a služeb aplikacím komunikovat způsobem, firewallem procházející a zabezpečené. Ve verzi public preview je v sady Node.js SDK podporuje pouze datové proudy zařízení na straně služby. Tento rychlý start v důsledku toho pokrývá jenom pokyny ke spuštění aplikace straně služby. Doprovodné aplikace na straně zařízení by měl spustit z jednoho z následujících šablon rychlý start:

* [Komunikaci s aplikací pro zařízení v jazyce C prostřednictvím datových proudů zařízení služby IoT Hub](./quickstart-device-streams-echo-c.md)

* [Komunikaci s aplikací pro zařízení v C# prostřednictvím datových proudů zařízení služby IoT Hub](./quickstart-device-streams-echo-csharp.md).

Aplikace Node.js straně služby v tomto rychlém startu má následující funkce:

* Vytvoří datový proud zařízení na zařízení IoT.

* Načte vstup z příkazového řádku a odesílá je do aplikace zařízení, která bude echo zpět.

Zahájení procesu zařízení datového proudu, jakož i jak ji používat k odesílání a příjmu dat vám ukáže, kód.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Ve verzi preview streamovaných zařízení je momentálně podporována pouze pro vytvoření centra IoT hub v těchto oblastech:

*  **USA (střed)**

*  **Střed USA – EUAP**

Ke spuštění aplikace straně služby v rámci tohoto rychlého startu budete potřebovat Node.js v10.x.x nebo novějším na vývojovém počítači.

Node.js můžete stáhnout pro různé platformy z [Nodejs.org](https://nodejs.org).

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

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registrování zařízení

Pokud jste dokončili předchozí [rychlý start: Odesílání telemetrických dat ze zařízení do služby IoT hub](quickstart-send-telemetry-node.md), můžete tento krok přeskočit.

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell.

1. Spusťte následující příkaz v Azure Cloud Shell vytvořte identitu zařízení.

   **YourIoTHubName**: Nahraďte tento zástupný text pod názvem, který jste zvolili pro službu IoT hub.

   **Mojezařízení**: Toto je název pro registrovaná zařízení. Použijte Mojezařízení, jak je znázorněno. Pokud si zvolíte jiný název zařízení, budete ho muset používat v průběhu celého článku a aktualizovat název zařízení v ukázkových aplikacích, než je spustíte.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Potřebujete také *připojovací řetězec služby*, který back-endové aplikaci umožní připojení k vašemu centru IoT a načtení zpráv. Následující příkaz načte připojovací řetězec služby pro vaše centrum IoT:

    **YourIoTHubName**: Nahraďte tento zástupný text pod názvem, který jste zvolili pro službu IoT hub.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Poznamenejte si vrácené hodnoty, který vypadá takto:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Komunikaci mezi zařízením a službou prostřednictvím datových proudů zařízení

V této části spuštění aplikace straně zařízení a aplikace straně služby a komunikaci mezi nimi.

### <a name="run-the-device-side-application"></a>Spuštění aplikace straně zařízení

Jak už bylo zmíněno dříve, SDK IoT Hub pro Node.js na straně služby podporuje pouze datové proudy zařízení. Pro aplikace na straně zařízení použijte doprovodných zařízení programu k dispozici v jednom z těchto rychlých startů:

   * [Komunikaci s aplikací pro zařízení v jazyce C prostřednictvím datových proudů zařízení služby IoT Hub](./quickstart-device-streams-echo-c.md)

   * [Komunikaci s aplikací pro zařízení v C# prostřednictvím datových proudů zařízení služby IoT Hub](./quickstart-device-streams-echo-csharp.md)

Zajistěte, aby že aplikace na straně zařízení se systémem než budete pokračovat k dalšímu kroku.

### <a name="run-the-service-side-application"></a>Spuštění aplikace straně služby

Za předpokladu, že aplikace na straně zařízení se systémem, postupujte podle pokynů ke spuštění aplikace straně služby v Node.js:

* Zadejte své přihlašovací údaje služby a ID zařízení jako proměnné prostředí.
 
   ```
   # In Linux
   export IOTHUB_CONNECTION_STRING="<provide_your_service_connection_string>"
   export STREAMING_TARGET_DEVICE="MyDevice"

   # In Windows
   SET IOTHUB_CONNECTION_STRING=<provide_your_service_connection_string>
   SET STREAMING_TARGET_DEVICE=MyDevice
   ```
  
   Změna `MyDevice` ID zařízení jste zvolili pro vaše zařízení.

* Přejděte na `Quickstarts/device-streams-service` ve vašich rozzipovaný projekt složky a spusťte ukázku pomocí uzlu.

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
    
   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   node echo.js
   ```

Na konci posledního kroku program straně služeb opraví, zahájí se stream do vašeho zařízení a po vytvoření se odeslat vyrovnávací paměti řetězce ke službě prostřednictvím datového proudu. V této ukázce straně služby program jednoduše načte `stdin` na terminálu a odesílá je do zařízení, což bude potom vracení ho. Tento příklad ukazuje úspěšné obousměrnou komunikaci mezi těmito dvěma aplikacemi.

![Výstup na konzole na straně služby](./media/quickstart-device-streams-echo-nodejs/service-console-output.png)

Program lze pak ukončit stisknutím kombinace kláves zadejte znovu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu mít nastavení služby IoT hub, zaregistrovali zařízení, vytvořit datový proud zařízení mezi aplikacemi na straně zařízení a služeb a používají datového proudu k odesílání dat vpřed a zpět mezi těmito aplikacemi.

Další informace o datových proudů zařízení pomocí odkazů níže:

> [!div class="nextstepaction"]
> [Přehled datových proudů zařízení](./iot-hub-device-streams-overview.md) 