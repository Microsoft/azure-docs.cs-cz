---
title: Rychlý Start – komunikace s aplikací zařízení v jazyce C# s datovými proudy zařízení v Azure IoT Hub
description: V tomto rychlém startu spustíte dvě ukázkové aplikace v C#, které komunikují přes Stream zařízení vytvořený prostřednictvím IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 3eb65db27e5b96f4b12973154bc860a2ab3df020
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624604"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Rychlý Start: komunikace s aplikací zařízení v jazyce C# prostřednictvím datových proudů zařízení IoT Hub (Preview)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Azure IoT Hub aktuálně podporuje streamy zařízení jako [funkci ve verzi Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[IoT Hub datové proudy zařízení](./iot-hub-device-streams-overview.md) umožňují aplikacím služeb a zařízením komunikovat zabezpečeným způsobem a bránou firewall. Tento rychlý Start zahrnuje dvě aplikace v jazyce C#, které využívají datové proudy zařízení k posílání a vracení dat (ECHO).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Verze Preview datových proudů zařízení je momentálně podporovaná jenom pro centra IoT, která jsou vytvořená v následujících oblastech:
  * USA – střed
  * Střed USA EUAP
  * Severní Evropa
  * Jihovýchodní Asie

* Dvě ukázkové aplikace, které spouštíte v rámci tohoto rychlého startu, jsou napsány v jazyce C#. Ve vývojovém počítači potřebujete .NET Core SDK 2.1.0 nebo novější.

    Stáhněte si [.NET Core SDK pro více platforem od .NET](https://www.microsoft.com/net/download/all).

    Ověřte aktuální verzi C# na vývojovém počítači pomocí následujícího příkazu:

    ```
    dotnet --version
    ```

* [Stáhněte si ukázky Azure IoT C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) a Extrahujte archiv zip. Budete ho potřebovat na straně zařízení i na straně služby.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrace zařízení

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V této části použijete Azure Cloud Shell k registraci simulovaného zařízení.

1. Chcete-li vytvořit identitu zařízení, spusťte následující příkaz v Cloud Shell:

   > [!NOTE]
   > * Zástupný text *YourIoTHubName* nahraďte názvem, který jste zvolili pro Centrum IoT.
   > * Pro název zařízení, které zaregistrujete, se doporučuje používat *mojezařízení* , jak je znázorněno na obrázku. Pokud pro své zařízení zvolíte jiný název, použijte tento název v celém rámci tohoto článku a aktualizujte název zařízení v ukázkových aplikacích ještě předtím, než je spustíte.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Pokud chcete získat *připojovací řetězec zařízení* pro zařízení, které jste právě zaregistrovali, spusťte v Cloud Shell následující příkaz:

   > [!NOTE]
   > Zástupný text *YourIoTHubName* nahraďte názvem, který jste zvolili pro Centrum IoT.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Poznamenejte si vrácený připojovací řetězec zařízení pro pozdější použití v tomto rychlém startu. Soubor bude vypadat jako v následujícím příkladu:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

3. *Připojovací řetězec služby* je také potřeba z vašeho centra IoT, aby se aplikace na straně služby mohla připojit ke službě IoT Hub a vytvořit datový proud zařízení. Následující příkaz načte tuto hodnotu služby IoT Hub:

   > [!NOTE]
   > Zástupný text *YourIoTHubName* nahraďte názvem, který jste zvolili pro Centrum IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Všimněte si vráceného připojovacího řetězce služby pro pozdější použití v tomto rychlém startu. Soubor bude vypadat jako v následujícím příkladu:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Komunikace mezi zařízením a službou prostřednictvím datových proudů zařízení

V této části spustíte jak aplikaci na straně zařízení, tak aplikaci na straně služby a komunikujete mezi nimi.

### <a name="run-the-service-side-application"></a>Spuštění aplikace na straně služby

V místním okně terminálu přejděte do `iot-hub/Quickstarts/device-streams-echo/service` adresáře ve složce getproject. Mějte na ruce následující informace:

| Název parametru | Hodnota parametru |
|----------------|-----------------|
| `ServiceConnectionString` | Připojovací řetězec služby centra IoT Hub. |
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
Aplikace bude čekat na zpřístupnění aplikace zařízení.

> [!NOTE]
> K vypršení časového limitu dojde, pokud aplikace na straně zařízení nereaguje v čase.

### <a name="run-the-device-side-application"></a>Spuštění aplikace na straně zařízení

V jiném místním okně terminálu přejděte do `iot-hub/Quickstarts/device-streams-echo/device` adresáře ve složce getproject. Mějte na ruce následující informace:

| Název parametru | Hodnota parametru |
|----------------|-----------------|
| `DeviceConnectionString` | Připojovací řetězec zařízení vašeho IoT Hub. |

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

Na konci posledního kroku aplikace na straně služby inicializuje datový proud na vaše zařízení. Po navázání datového proudu aplikace odešle službě přes datový proud vyrovnávací paměť řetězců. V této ukázce aplikace na straně služby jednoduše vrátí zpět stejná data do zařízení, které demonstruje úspěšnou obousměrnou komunikaci mezi těmito dvěma aplikacemi.

Výstup na konzole na straně zařízení:

![Výstup na konzole na straně zařízení](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

Výstup na konzole na straně služby:

![Výstup na konzole na straně služby](./media/quickstart-device-streams-echo-csharp/service-console-output.png)

Přenos přenášený přes datový proud se místo přímého posílání prostřednictvím tunelového přenosu prostřednictvím centra IoT. Uvedené výhody jsou popsány v části [výhody streamování zařízení](./iot-hub-device-streams-overview.md#benefits).

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu nastavíte službu IoT Hub, zaregistrovali jste zařízení a navázali jste datový proud zařízení mezi aplikacemi v jazyce C# na stranách zařízení a služeb a použili jste datový proud k posílání dat mezi aplikacemi a zpátky mezi nimi.

Další informace o datových proudech zařízení najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Přehled streamů zařízení](./iot-hub-device-streams-overview.md)
