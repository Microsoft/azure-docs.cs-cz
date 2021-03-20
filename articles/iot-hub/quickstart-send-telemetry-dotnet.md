---
title: Rychlý Start – odeslání telemetrie do služby Azure IoT Hub Starter (C#) | Microsoft Docs
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
- devx-track-azurecli
ms.date: 06/01/2020
ms.openlocfilehash: 914df5b80dee7da041b268a3aaf25ac493d0cf5b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98624435"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-service-application-net"></a>Rychlý Start: odeslání telemetrie ze zařízení do služby IoT Hub a její čtení pomocí aplikace služby (.NET)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub je služba Azure, která umožňuje ingestovat velké objemy telemetrických dat ze zařízení IoT do cloudu pro účely uložení nebo zpracování. V tomto rychlém startu odešlete telemetrii z aplikace simulovaného zařízení prostřednictvím IoT Hub do aplikace služby ke zpracování.

V tomto rychlém startu se používají dvě předem napsané aplikace C#, jedna k odesílání telemetrických dat a jedna ke čtení telemetrických dat z centra. Před spuštěním těchto dvou aplikací vytvoříte centrum IoT a zaregistrujete v tomto centru zařízení.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

* Dvě ukázkové aplikace, které spustíte v tomto rychlém startu, jsou napsány pomocí C#. Ve vývojovém počítači potřebujete .NET Core SDK 3,1 nebo vyšší.

    Sadu .NET Core SDK pro různé platformy si můžete stáhnout z webu [.NET](https://www.microsoft.com/net/download/all).

    Aktuální verzi C# na počítači používaném pro vývoj můžete ověřit pomocí následujícího příkazu:

    ```cmd/sh
    dotnet --version
    ```

    > [!NOTE]
    > Pro kompilaci kódu služby Event Hubs používaného pro čtení telemetrie v tomto rychlém startu se doporučuje .NET Core SDK 3,1 nebo vyšší.


* Stáhněte si ukázky pro Azure IoT C# z webu [https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) a Extrahujte archiv zip.

* Ujistěte se, že je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto rychlém startu používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokovaný v některých podnikových a vzdělávacích prostředích sítě. Další informace a způsoby, jak tento problém obejít, najdete v tématu [připojení k IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

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
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDotnetDevice --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá takto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDotnetDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v rychlém startu.

3. K tomu, aby se mohla aplikace služby připojit ke službě IoT Hub a jak se budou tyto zprávy načítat, potřebujete také _koncový bod kompatibilní s Event Hubs_, _cestu kompatibilní s Event Hubs_ a _primární klíč služby_ z vašeho centra IoT. Následující příkazy načtou tyto hodnoty pro centrum IoT:

   **YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který zvolíte pro Centrum IoT.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Poznamenejte si tyto tři hodnoty, které použijete později v rychlém startu.

## <a name="send-simulated-telemetry"></a>Odesílání simulovaných telemetrických dat

Aplikace simulovaného zařízení se připojuje ke koncovému bodu vašeho centra IoT pro konkrétní zařízení a odesílá simulovaná telemetrická data o teplotě a vlhkosti vzduchu.

1. V okně místního terminálu přejděte do kořenové složky ukázkového projektu jazyka C#. Pak přejděte do složky **IoT-hub\Quickstarts\SimulatedDevice** .

2. V okně místního terminálu pomocí následujících příkazů nainstalujte požadované balíčky pro aplikaci simulovaného zařízení:

    ```cmd/sh
    dotnet restore
    ```

3. V okně místního terminálu spusťte následující příkaz, který sestaví a spustí aplikaci simulovaného zařízení s připojovacím řetězcem zařízení, který jste si poznamenali v předchozím kroku:

    ```cmd/sh
    dotnet run -- {DeviceConnectionString}
    ```

    Následující snímek obrazovky ukazuje výstup, zatímco aplikace simulovaného zařízení odesílá telemetrická data do vašeho centra IoT:

    ![Spuštění simulovaného zařízení](media/quickstart-send-telemetry-dotnet/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Čtení telemetrie z centra

Aplikace služby se připojí ke koncovému bodu **událostí** na straně služby na vašem IoT Hub. Aplikace přijímá zprávy typu zařízení-cloud odeslané ze simulovaného zařízení. Aplikace služby IoT Hub obvykle běží v cloudu pro příjem a zpracování zpráv ze zařízení do cloudu.

1. V jiném okně místního terminálu přejděte do kořenové složky ukázkového projektu jazyka C#. Pak přejděte do složky **IoT-hub\Quickstarts\ReadD2cMessages** .

2. V okně místního terminálu spusťte následující příkaz, který nainstaluje požadované knihovny pro aplikaci:

    ```cmd/sh
    dotnet restore
    ```

3. V okně místního terminálu spusťte následující příkaz, který zobrazí možnosti parametrů.

    ```cmd/sh
    dotnet run
    ```

4. V okně místního terminálu spusťte jeden z následujících příkazů, který sestaví a spustí aplikaci s:

    ```cmd/sh
    dotnet run -- -c {EventHubConnectionString}
    ```

    nebo

    ```cmd/sh
    dotnet run -- -e {EventHubCompatibleEndpoint} -n {EventHubName} -s {SharedAccessKey}
    ```

    Následující snímek obrazovky ukazuje výstup, protože aplikace služby obdrží telemetrii odeslanou simulovaným zařízením do centra:

    ![Spuštění aplikace služby](media/quickstart-send-telemetry-dotnet/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu nastavíte centrum IoT, zaregistrovali zařízení, poslali simulovanou telemetrii do centra pomocí aplikace v jazyce C# a načetli telemetrii z centra pomocí jednoduché aplikace služby.

Další informace o tom, jak ovládat simulované zařízení z aplikace služby, můžete pokračovat v dalším rychlém startu.

> [!div class="nextstepaction"]
> [Rychlý start: Řízení zařízení připojeného k centru IoT](quickstart-control-device-dotnet.md)
