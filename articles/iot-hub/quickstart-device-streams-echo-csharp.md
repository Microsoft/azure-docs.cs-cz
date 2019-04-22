---
title: Azure IoT Hub zařízení streamy C# rychlý start (preview) | Dokumentace Microsoftu
description: V tomto rychlém startu spustíte dvě ukázkové C# aplikace, které komunikují prostřednictvím datového proudu zařízení navázané prostřednictvím služby IoT Hub.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 2853bd5539a40e3b38927f619756fe37a4cec984
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59006856"
---
# <a name="quickstart-communicate-to-device-applications-in-c-via-iot-hub-device-streams-preview"></a>Rychlý start: Komunikace zařízení pro aplikace v C# prostřednictvím datových proudů zařízení služby IoT Hub (preview)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Microsoft Azure IoT Hub v současné době podporuje datové proudy zařízení jako [funkce ve verzi preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Datové proudy zařízení služby IoT Hub](./iot-hub-device-streams-overview.md) povolit zařízení a služeb aplikacím komunikovat způsobem, firewallem procházející a zabezpečené. Tento rychlý start zahrnuje dvě C# programy, které využívají datové proudy zařízení k odesílání dat vpřed a zpět (echo).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Ve verzi preview streamovaných zařízení je momentálně podporována pouze pro vytvoření centra IoT hub v těchto oblastech:

  - **USA (střed)**
  - **Střed USA – EUAP**

Dvě ukázkové aplikace, které spustíte v tomto rychlém startu, jsou napsány pomocí C#. Na počítači používaném pro vývoj musíte mít .NET Core SDK 2.1.0 nebo vyšší.

Sadu .NET Core SDK pro různé platformy si můžete stáhnout z webu [.NET](https://www.microsoft.com/net/download/all).

Aktuální verzi C# na počítači používaném pro vývoj můžete ověřit pomocí následujícího příkazu:

```
dotnet --version
```

Spusťte následující příkaz pro přidání rozšíření Microsoft Azure IoT pro Azure CLI do instance služby Cloud Shell. Rozšíření IOT přidá služby IoT Hub, IoT Edge a IoT zařízení zřizování služby (DPS) konkrétní příkazy rozhraní příkazového řádku Azure.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Stáhněte si ukázkový projekt C# z https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip a extrahujte archiv ZIP. Je třeba na straně zařízení a služeb.

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registrování zařízení

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell.

1. Spusťte následující příkaz v Azure Cloud Shell vytvořte identitu zařízení.

   **YourIoTHubName**: Nahraďte tento zástupný text pod názvem, který jste vybrali pro službu IoT hub.

   **Mojezařízení**: Toto je název pro registrovaná zařízení. Použijte Mojezařízení, jak je znázorněno. Pokud si zvolíte jiný název zařízení, budete ho muset používat v průběhu celého článku a aktualizovat název zařízení v ukázkových aplikacích, než je spustíte.

    ```azurecli-interactive
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

3. Musíte také _připojovací řetězec služby_ ze služby IoT hub umožňuje aplikace na straně služby pro připojení ke službě IoT hub a vytvořit datový proud zařízení. Následující příkaz načte tuto hodnotu pro službu IoT hub:

   **YourIoTHubName**: Nahraďte tento zástupný text pod názvem, který jste vybrali pro službu IoT hub.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Poznamenejte si vrácené hodnoty, který vypadá takto:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Komunikaci mezi zařízením a službou prostřednictvím datových proudů zařízení

### <a name="run-the-service-side-application"></a>Spuštění aplikace straně služby

Přejděte na `iot-hub/Quickstarts/device-streams-echo/service` ve složce rozzipovaný projekt. Budete potřebovat následující informace užitečné:

| Název parametru | Hodnota parametru |
|----------------|-----------------|
| `ServiceConnectionString` | Zadejte připojovací řetězec služby IoT hub. |
| `DeviceId` | Zadejte ID zařízení, které jste vytvořili dříve, například Mojezařízení. |

Kompilace a spuštění kódu následujícím způsobem:

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run "<ServiceConnectionString>" "<MyDevice>"

# In Windows
dotnet run <ServiceConnectionString> <MyDevice>
```

> [!NOTE]
> Vypršení časového limitu dojde, pokud aplikace na straně zařízení nereaguje v čase.

### <a name="run-the-device-side-application"></a>Spuštění aplikace straně zařízení

Přejděte na `iot-hub/Quickstarts/device-streams-echo/device` ve složce rozzipovaný projekt. Budete potřebovat následující informace užitečné:

| Název parametru | Hodnota parametru |
|----------------|-----------------|
| `DeviceConnectionString` | Zadejte připojovací řetězec zařízení služby IoT Hub. |

Kompilace a spuštění kódu následujícím způsobem:

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run "<DeviceConnectionString>"

# In Windows
dotnet run <DeviceConnectionString>
```

Na konci posledního kroku program straně služeb opraví, zahájí se stream do vašeho zařízení a po vytvoření se odeslat vyrovnávací paměti řetězce ke službě prostřednictvím datového proudu. V této ukázce straně služby program jednoduše vrátí zpět stejná data na zařízení, ukázka úspěšné obousměrnou komunikaci mezi těmito dvěma aplikacemi. Viz následující obrázek.

Na straně zařízení výstupu konzoly: ![alternativní text](./media/quickstart-device-streams-echo-csharp/device-console-output.png "výstupu na straně zařízení konzoly")

Na straně služby výstupu konzoly: ![alternativní text](./media/quickstart-device-streams-echo-csharp/service-console-output.png "výstupu na straně služby konzoly")

Přenosy budou odesílat prostřednictvím datového proudu se tunelové propojení prostřednictvím služby IoT Hub spíše než odesílají přímo. To poskytuje [tyto výhody](./iot-hub-device-streams-overview.md#benefits).

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste nastavení služby IoT hub, zaregistrovali zařízení, vytvořit datový proud zařízení mezi C# aplikace na straně zařízení a služeb a používají datového proudu k odesílání dat vpřed a zpět mezi těmito aplikacemi.

Další informace o datových proudů zařízení pomocí odkazů níže:

> [!div class="nextstepaction"]
> [Přehled datových proudů zařízení](./iot-hub-device-streams-overview.md)
