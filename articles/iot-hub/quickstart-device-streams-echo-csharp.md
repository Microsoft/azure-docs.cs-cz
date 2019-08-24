---
title: Pro aplikace pro zařízení v komunikaci C# prostřednictvím datových proudů zařízení Azure IoT Hub (preview) | Dokumentace Microsoftu
description: V tomto rychlém startu spustíte dvě ukázkové C# aplikace, které komunikují prostřednictvím datového proudu zařízení navázané prostřednictvím služby IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: de581362371e28523c99f961dfdb5c2009901343
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446111"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Rychlý start: Na zařízení aplikaci v komunikaci C# prostřednictvím datových proudů zařízení služby IoT Hub (preview)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Datové proudy zařízení jako v současné době podporuje služba Azure IoT Hub [funkce ve verzi preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Datové proudy zařízení služby IoT Hub](./iot-hub-device-streams-overview.md) povolit zařízení a služeb aplikacím komunikovat způsobem, firewallem procházející a zabezpečené. Tento rychlý start zahrnuje dvě C# aplikací, které budou využívat datové proudy zařízení k odesílání dat vpřed a zpět (echo).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Ve verzi preview streamovaných zařízení je momentálně podporována pouze pro IoT hub, které jsou vytvořeny v těchto oblastech:
  * Střední USA
  * Střed USA – EUAP

* Dvě ukázkové aplikace, které běží v rámci tohoto rychlého startu jsou zapsány pomocí C#. Budete potřebovat sadu .NET Core SDK 2.1.0 nebo novějším na vývojovém počítači.
  * Stáhněte si [.NET Core SDK pro různé platformy z .NET](https://www.microsoft.com/net/download/all).
  * Zkontrolujte aktuální verzi C# na vývojovém počítači s použitím následujícího příkazu:

   ```
   dotnet --version
   ```

* Přidáte rozšíření Azure IoT pro Azure CLI do instance služby Cloud Shell spuštěním následujícího příkazu. Přidá rozšíření IOT služby IoT Hub, IoT Edge a IoT zařízení zřizování služby (DPS)-konkrétní příkazy rozhraní příkazového řádku Azure.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

* [Stáhněte si ukázku C# projektu](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) a extrahujte archiv ZIP. Když je potřebujete na straně zařízení a na straně služby.

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registrování zařízení

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V této části použít Azure Cloud Shell zaregistrovat simulované zařízení.

1. K vytvoření identity zařízení, spusťte následující příkaz ve službě Cloud Shell:

   > [!NOTE]
   > * Nahradit *YourIoTHubName* zástupný symbol název, který zvolíte pro službu IoT hub.
   > * Použití *Mojezařízení*, jak je znázorněno. Jedná se o název pro registrovaná zařízení. Pokud zvolíte jiný název pro vaše zařízení, použijte tento název v rámci tohoto článku a aktualizujte název zařízení v ukázkové aplikace před spuštěním je.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

1. Chcete-li získat *připojovací řetězec zařízení* pro zařízení, které jste právě zaregistrovali, spuštěním následujícího příkazu ve službě Cloud Shell:

   > [!NOTE]
   > Nahradit *YourIoTHubName* zástupný symbol název, který zvolíte pro službu IoT hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Poznamenejte si připojovací řetězec zařízení pro pozdější použití v rámci tohoto rychlého startu. Soubor bude vypadat jako v následujícím příkladu:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

3. Musíte také *připojovací řetězec služby* ze služby IoT hub umožňuje aplikace na straně služby pro připojení ke službě IoT hub a vytvořit datový proud zařízení. Následující příkaz načte tuto hodnotu pro službu IoT hub:

   > [!NOTE]
   > Nahradit *YourIoTHubName* zástupný symbol název, který zvolíte pro službu IoT hub.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Všimněte si, vrácená hodnota pro pozdější použití v rámci tohoto rychlého startu. Soubor bude vypadat jako v následujícím příkladu:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Komunikaci mezi zařízením a služby prostřednictvím datových proudů zařízení

V této části spuštění aplikace straně zařízení a aplikace straně služby a komunikaci mezi nimi.

### <a name="run-the-service-side-application"></a>Spuštění aplikace straně služby

Přejděte *– / šablon rychlý start/zařízení – datové proudy echo/služby Azure iot hub* ve složce rozzipovaný projekt. Zachovat po ruce následující informace:

| Název parametru | Hodnota parametru |
|----------------|-----------------|
| `ServiceConnectionString` | Zadejte připojovací řetězec služby IoT hub. |
| `DeviceId` | Zadat ID zařízení, které jste vytvořili dříve (například *Mojezařízení*). |

Kompilace a spuštění kódu následujícím způsobem:

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "<ServiceConnectionString>" "<MyDevice>"

# In Windows
dotnet run <ServiceConnectionString> <MyDevice>
```

> [!NOTE]
> Vypršení časového limitu dojde, pokud aplikace na straně zařízení nereaguje v čase.

### <a name="run-the-device-side-application"></a>Spuštění aplikace straně zařízení

Přejděte *-hub/šablon rychlý start/zařízení – datové proudy echo/zařízení iot* ve složce rozzipovaný projekt. Zachovat po ruce následující informace:

| Název parametru | Hodnota parametru |
|----------------|-----------------|
| `DeviceConnectionString` | Zadejte připojovací řetězec zařízení služby IoT Hub. |

Kompilace a spuštění kódu následujícím způsobem:

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "<DeviceConnectionString>"

# In Windows
dotnet run <DeviceConnectionString>
```

Na konci posledního kroku zahájí aplikace straně služby stream do vašeho zařízení. Po vytvoření datového proudu aplikace odesílá vyrovnávací paměti řetězce ke službě prostřednictvím datového proudu. V této ukázce aplikace na straně služby jednoduše vrátí zpět stejná data na zařízení, která ukazuje úspěšné obousměrnou komunikaci mezi těmito dvěma aplikacemi.

Výstup na straně zařízení konzoly:

![Výstup na konzole na straně zařízení](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

Výstup na straně služby konzoly:

![Výstup na konzole na straně služby](./media/quickstart-device-streams-echo-csharp/service-console-output.png)

Přenosy budou odesílat prostřednictvím datového proudu je tunelové propojení prostřednictvím služby IoT hub spíše než odesílají přímo. Výhody poskytované jsou podrobně popsány v [zařízení datové proudy výhody](./iot-hub-device-streams-overview.md#benefits).

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste nastavení služby IoT hub, zaregistrovali zařízení, vytvořit datový proud zařízení mezi C# aplikací na zařízení a služby strany a používají datového proudu k odesílání dat vpřed a zpět mezi těmito aplikacemi.

Další informace o zařízení datových proudů, naleznete v tématu:

> [!div class="nextstepaction"]
> [Přehled datových proudů zařízení](./iot-hub-device-streams-overview.md)
