---
title: Rychlý start k odesílání telemetrických dat do služby Azure IoT Hub (C#) | Microsoft Docs
description: V tomto rychlém startu spustíte dvě ukázkové aplikace C#, které odesílají simulovaná telemetrická data do centra IoT a čtou telemetrická data z centra IoT, aby se mohla zpracovávat v cloudu.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
ms.date: 06/01/2020
ms.openlocfilehash: 98b50649b5a788270fa2b4cd8b62ca5598daa25f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "87320472"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-net"></a>Rychlý Start: odeslání telemetrie ze zařízení do služby IoT Hub a jejich čtení pomocí back-endové aplikace (.NET)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub je služba Azure, která umožňuje ingestovat velké objemy telemetrických dat ze zařízení IoT do cloudu pro účely uložení nebo zpracování. V tomto rychlém startu odešlete telemetrická data z aplikace simulovaného zařízení prostřednictvím služby IoT Hub do back-endové aplikace za účelem zpracování.

V tomto rychlém startu se používají dvě předem napsané aplikace C#, jedna k odesílání telemetrických dat a jedna ke čtení telemetrických dat z centra. Před spuštěním těchto dvou aplikací vytvoříte centrum IoT a zaregistrujete v tomto centru zařízení.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Předpoklady

Dvě ukázkové aplikace, které spustíte v tomto rychlém startu, jsou napsány pomocí C#. Ve vývojovém počítači potřebujete .NET Core SDK 3,0 nebo vyšší.

Sadu .NET Core SDK pro různé platformy si můžete stáhnout z webu [.NET](https://www.microsoft.com/net/download/all).

Aktuální verzi C# na počítači používaném pro vývoj můžete ověřit pomocí následujícího příkazu:

```cmd/sh
dotnet --version
```

> [!NOTE]
> Pro kompilaci kódu služby Event Hubs používaného pro čtení telemetrie v tomto rychlém startu se doporučuje .NET Core SDK 3,0 nebo vyšší. .NET Core SDK 2,1 můžete použít, pokud nastavíte jazykovou verzi pro kód služby na náhled, jak je uvedeno v části [přečtení telemetrie z vašeho centra](#read-the-telemetry-from-your-hub) .

Spuštěním následujícího příkazu přidejte do instance služby Cloud Shell Microsoft Azure rozšíření IoT pro rozhraní příkazového řádku Azure. Rozšíření IOT přidá do Azure CLI příkazy určené pro služby IoT Hub, IoT Edge a IoT Device Provisioning Service (DPS).

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

Stáhněte si ukázky pro Azure IoT C# z webu [https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) a Extrahujte archiv zip.

Ujistěte se, že je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto rychlém startu používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokovaný v některých podnikových a vzdělávacích prostředích sítě. Další informace a způsoby, jak tento problém obejít, najdete v tématu [připojení k IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT (neboli IoT Hubu)

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrace zařízení

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell.

1. Spuštěním následujícího příkazu v Azure Cloud Shell vytvořte identitu zařízení.

   **YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který jste zvolili pro Centrum IoT.

   **MyDotnetDevice**: Toto je název zařízení, které registrujete. Doporučuje se používat **MyDotnetDevice** , jak je znázorněno na obrázku. Pokud pro vaše zařízení zvolíte jiný název, budete ho muset použít i v celém rámci tohoto článku a před jeho spuštěním aktualizovat název zařízení v ukázkových aplikacích.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

2. Spuštěním následujícího příkazu v Azure Cloud Shell Získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:

   **YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který jste zvolili pro Centrum IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDotnetDevice --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá takto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDotnetDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v rychlém startu.

3. K povolení back-endové aplikace pro připojení ke službě IoT Hub a k načtení zpráv budete potřebovat taky _koncový bod kompatibilní s Event Hubs_, _cestu kompatibilní s Event Hubs_a _primární klíč služby_ z vašeho centra IoT. Následující příkazy načtou tyto hodnoty pro centrum IoT:

   **YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který zvolíte pro Centrum IoT.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Poznamenejte si tyto tři hodnoty, které použijete později v rychlém startu.

## <a name="send-simulated-telemetry"></a>Odesílání simulovaných telemetrických dat

Aplikace simulovaného zařízení se připojuje ke koncovému bodu vašeho centra IoT pro konkrétní zařízení a odesílá simulovaná telemetrická data o teplotě a vlhkosti vzduchu.

1. V okně místního terminálu přejděte do kořenové složky ukázkového projektu jazyka C#. Pak přejděte do složky **iot-hub\Quickstarts\simulated-device**.

2. V libovolném textovém editoru otevřete soubor **SimulatedDevice.cs**.

    Nahraďte hodnotu `s_connectionString` proměnné připojovacím řetězcem zařízení, který jste si poznamenali dříve. Pak změny uložte do **SimulatedDevice.cs**.

3. V okně místního terminálu pomocí následujících příkazů nainstalujte požadované balíčky pro aplikaci simulovaného zařízení:

    ```cmd/sh
    dotnet restore
    ```

4. Spuštěním následujícího příkazu v okně místního terminálu sestavte a spusťte aplikaci simulovaného zařízení:

    ```cmd/sh
    dotnet run
    ```

    Následující snímek obrazovky ukazuje výstup, zatímco aplikace simulovaného zařízení odesílá telemetrická data do vašeho centra IoT:

    ![Spuštění simulovaného zařízení](media/quickstart-send-telemetry-dotnet/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Čtení telemetrie z centra

Back-endová aplikace se připojí ke koncovému bodu **Events** na straně služby ve vašem centru IoT. Aplikace přijímá zprávy typu zařízení-cloud odeslané ze simulovaného zařízení. Back-endová aplikace služby IoT Hub se obvykle spouští v cloudu, aby mohla přijímat a zpracovávat zprávy typu zařízení-cloud.

1. V jiném okně místního terminálu přejděte do kořenové složky ukázkového projektu jazyka C#. Potom přejděte ke složce **iot-hub\Quickstarts\read-d2c-messages**.

2. V libovolném textovém editoru otevřete soubor **ReadDeviceToCloudMessages.cs**. Aktualizujte následující proměnné a uložte provedené změny souboru.

    | Proměnná | Hodnota |
    | -------- | ----------- |
    | `EventHubsCompatibleEndpoint` | Nahraďte hodnotu proměnné pomocí koncového bodu kompatibilního s Event Hubs, který jste si poznamenali dříve. |
    | `EventHubName`                | Nahraďte hodnotu proměnné cestou kompatibilní s Event Hubs, kterou jste si poznamenali dříve. |
    | `IotHubSasKey`                | Nahraďte hodnotu proměnné primárním klíčem služby, který jste si poznamenali dříve. |

    > [!NOTE]
    > Pokud používáte .NET Core SDK 2,1, musíte nastavit jazykovou verzi na náhled pro zkompilování kódu. Provedete to tak, že otevřete soubor **Read-D2C-Messages. csproj** a nastavíte hodnotu `<LangVersion>` elementu na `preview` .

3. V okně místního terminálu pomocí následujících příkazů nainstalujte požadované knihovny pro back-endovou aplikaci:

    ```cmd/sh
    dotnet restore
    ```

4. Spuštěním následujících příkazů v okně místního terminálu sestavte a spusťte back-endovou aplikaci:

    ```cmd/sh
    dotnet run
    ```

    Následující snímek obrazovky ukazuje výstup, zatímco back-endová aplikace přijímá telemetrická data odeslaná simulovaným zařízením do centra:

    ![Spuštění back-endové aplikace](media/quickstart-send-telemetry-dotnet/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu nastavíte centrum IoT, zaregistrovali zařízení, poslali simulovanou telemetrii do centra pomocí aplikace v jazyce C# a načetli telemetrii z centra pomocí jednoduché back-endové aplikace.

Informace o tom, jak řídit simulované zařízení z back-endové aplikace, najdete v dalším rychlém startu.

> [!div class="nextstepaction"]
> [Rychlý start: Řízení zařízení připojeného k centru IoT](quickstart-control-device-dotnet.md)