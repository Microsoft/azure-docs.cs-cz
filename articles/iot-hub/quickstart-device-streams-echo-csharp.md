---
title: Komunikujte s aplikací zařízení v C# pomocí datových proudů zařízení Azure IoT Hub
description: V tomto rychlém startu spustíte dvě ukázkové aplikace jazyka C#, které komunikují prostřednictvím datového proudu zařízení vytvořeného prostřednictvím služby IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 64af62cb6c2c56ca8c7e67e2f1467d4a7e8335a0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675523"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Úvodní příručka: Komunikace s aplikací zařízení v C# prostřednictvím datových proudů zařízení služby IoT Hub (náhled)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Azure IoT Hub aktuálně podporuje datové proudy zařízení jako [funkci náhledu](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Datové proudy zařízení služby IoT Hub umožňují aplikacím služby](./iot-hub-device-streams-overview.md) a zařízení komunikovat bezpečným způsobem a způsobem, který je vhodný pro bránu firewall. Tento rychlý start zahrnuje dvě aplikace jazyka C#, které využívají datové proudy zařízení k odesílání dat tam a zpět (echo).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

* Náhled datových proudů zařízení je aktuálně podporován pouze pro centra IoT, které jsou vytvořeny v následujících oblastech:
  * USA – střed
  * Centrální US EUAP
  * Severní Evropa
  * Jihovýchodní Asie

* Dvě ukázkové aplikace, které spustíte v tomto rychlém startu jsou zapsány v C#. Potřebujete .NET Core SDK 2.1.0 nebo novější ve vývojovém počítači.
  * Stáhněte si [sdk jádra .NET pro více platforem z rozhraní .NET](https://www.microsoft.com/net/download/all).
  * Ověřte aktuální verzi jazyka C# ve vývojovém počítači pomocí následujícího příkazu:

   ```
   dotnet --version
   ```

* Přidejte rozšíření Azure IoT pro Azure CLI do instance Cloud Shell spuštěním následujícího příkazu. Rozšíření IOT přidá do rozhraní příkazového příkazu Azure CLI služby IoT Hub, IoT Edge a Služby zřizování zařízení IoT (DPS).

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

* [Stáhněte si ukázky Azure IoT C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) a extrahujte archiv ZIP. Potřebujete ji jak na straně zařízení, tak na straně služby.

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrování zařízení

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V této části použijete Azure Cloud Shell k registraci simulované zařízení.

1. Chcete-li vytvořit identitu zařízení, spusťte v prostředí Cloud Shell následující příkaz:

   > [!NOTE]
   > * Nahraďte zástupný symbol *YourIoTHubName* názvem, který jste zvolili pro centrum IoT.
   > * Pro název zařízení, které registrujete, je doporučeno použít *MyDevice,* jak je znázorněno. Pokud pro své zařízení zvolíte jiný název, použijte tento název v celém tomto článku a aktualizujte název zařízení v ukázkových aplikacích před jejich spuštěním.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Chcete-li získat *připojovací řetězec zařízení* pro zařízení, které jste právě zaregistrovali, spusťte v prostředí Cloud Shell následující příkaz:

   > [!NOTE]
   > Nahraďte zástupný symbol *YourIoTHubName* názvem, který jste zvolili pro centrum IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Všimněte si vrácené zařízení připojovací řetězec pro pozdější použití v tomto rychlém startu. Soubor bude vypadat jako v následujícím příkladu:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

3. Potřebujete také *připojovací řetězec služby* z vašeho centra IoT, abyste povolili aplikaci na straně služby připojení k centru IoT hub a vytvořili datový proud zařízení. Následující příkaz načte tuto hodnotu pro vaše služby IoT hub:

   > [!NOTE]
   > Nahraďte zástupný symbol *YourIoTHubName* názvem, který jste zvolili pro centrum IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Všimněte si vrácené služby připojovací řetězec pro pozdější použití v tomto rychlém startu. Soubor bude vypadat jako v následujícím příkladu:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Komunikace mezi zařízením a službou prostřednictvím datových proudů zařízení

V této části spustíte aplikaci na straně zařízení i aplikaci na straně služby a komunikujete mezi nimi.

### <a name="run-the-service-side-application"></a>Spuštění aplikace na straně služby

V místním okně terminálu `iot-hub/Quickstarts/device-streams-echo/service` přejděte do adresáře ve složce projektu, který je rozepnut. Mějte po ruce následující informace:

| Název parametru | Hodnota parametru |
|----------------|-----------------|
| `ServiceConnectionString` | Připojovací řetězec služby služby služby služby IoT hub. |
| `MyDevice` | Identifikátor zařízení, které jste vytvořili dříve. |

Zkompilujte a spusťte kód pomocí následujících příkazů:

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{ServiceConnectionString}" "MyDevice"

# In Windows
dotnet run {ServiceConnectionString} MyDevice
```
Aplikace bude čekat na aplikaci zařízení k dispozici.

> [!NOTE]
> K časovému výtce dochází, pokud aplikace na straně zařízení nereaguje včas.

### <a name="run-the-device-side-application"></a>Spuštění aplikace na straně zařízení

V jiném okně místního `iot-hub/Quickstarts/device-streams-echo/device` terminálu přejděte do adresáře ve složce projektu, který je rozepnut. Mějte po ruce následující informace:

| Název parametru | Hodnota parametru |
|----------------|-----------------|
| `DeviceConnectionString` | Připojovací řetězec zařízení vašeho centra IoT Hub. |

Zkompilujte a spusťte kód pomocí následujících příkazů:

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{DeviceConnectionString}"

# In Windows
dotnet run {DeviceConnectionString}
```

Na konci posledního kroku aplikace na straně služby zahájí datový proud do vašeho zařízení. Po vytvoření datového proudu aplikace odešle vyrovnávací paměť řetězce do služby přes datový proud. V této ukázce aplikace na straně služby jednoduše ozvěny zpět stejná data do zařízení, což demonstruje úspěšnou obousměrnou komunikaci mezi dvěma aplikacemi.

Výstup konzoly na straně zařízení:

![Výstup konzoly na straně zařízení](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

Výstup konzoly na straně služby:

![Výstup konzoly na straně služby](./media/quickstart-device-streams-echo-csharp/service-console-output.png)

Přenosy odesílané přes datový proud jsou tunelovány přes centrum IoT, nikoli odesílané přímo. Výhody poskytované jsou podrobně popsány v [výhody zařízení proudy](./iot-hub-device-streams-overview.md#benefits).

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu nastavíte službu IoT hub, zaregistrujete zařízení, nastavíte datový proud zařízení mezi aplikacemi jazyka C# na straně zařízení a služby a pomocí datového proudu odesíláte data tam a zpět mezi aplikacemi.

Další informace o streamech zařízení najdete v tématu:

> [!div class="nextstepaction"]
> [Přehled datových proudů zařízení](./iot-hub-device-streams-overview.md)
