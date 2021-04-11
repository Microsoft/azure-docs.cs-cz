---
title: Rychlý Start – řízení zařízení z Azure IoT Hub rychlý Start (.NET) | Microsoft Docs
description: V tomto rychlém startu spustíte dvě ukázkové aplikace C#. Jedna aplikace je aplikace služby, která může vzdáleně řídit zařízení připojená k rozbočovači. Druhá aplikace simuluje zařízení připojené k vašemu centru, které je možné řídit vzdáleně.
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
ms.date: 03/04/2020
ms.openlocfilehash: b89cc9dfb0a98f61b74ecf42471d08129661fb22
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059853"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-net"></a>Rychlý start: Řízení zařízení připojeného k centru IoT (.NET)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub je služba Azure, která umožňuje spravovat zařízení IoT z cloudu a ingestovat velké objemy telemetrie zařízení do cloudu za účelem uložení nebo zpracování. V tomto rychlém startu použijete *přímou metodu* k řízení simulovaného zařízení připojeného k centru IoT. Přímé metody můžete použít k provádění vzdálených změn chování zařízení připojeného k centru IoT.

Rychlý start používá dvě předem vytvořené aplikace .NET:

* Aplikace simulovaného zařízení, která reaguje na přímé metody volané z aplikace služby. Aby bylo možné přijímat volání přímé metody, připojí se tato aplikace ke koncovému bodu centra IoT pro konkrétní zařízení.

* Aplikace služby, která volá přímé metody na simulovaném zařízení. Aby na zařízení bylo možné volat přímou metodu, připojí se tato aplikace ke koncovému bodu na straně služby ve vašem centru IoT.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Dvě ukázkové aplikace, které spustíte v tomto rychlém startu, jsou napsány pomocí C#. Ve vývojovém počítači potřebujete .NET Core SDK 3,1 nebo vyšší.

    Sadu .NET Core SDK pro různé platformy si můžete stáhnout z webu [.NET](https://www.microsoft.com/net/download/all).

    Aktuální verzi C# na počítači používaném pro vývoj můžete ověřit pomocí následujícího příkazu:

    ```cmd/sh
    dotnet --version
    ```
* Pokud jste to ještě neudělali, Stáhněte si ukázky pro Azure IoT C# z webu https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip a Extrahujte archiv zip.

* Ujistěte se, že je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto rychlém startu používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokovaný v některých podnikových a vzdělávacích prostředích sítě. Další informace a způsoby, jak tento problém obejít, najdete v tématu [připojení k IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Pokud jste dokončili předchozí [Rychlý start: Odesílání telemetrických dat ze zařízení do centra IoT](quickstart-send-telemetry-dotnet.md), můžete tento krok přeskočit.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrace zařízení

Pokud jste dokončili předchozí [Rychlý start: Odesílání telemetrických dat ze zařízení do centra IoT](quickstart-send-telemetry-dotnet.md), můžete tento krok přeskočit.

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell.

1. Spuštěním následujícího příkazu v Azure Cloud Shell vytvořte identitu zařízení.

   **YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který jste zvolili pro Centrum IoT.

   **MyDotnetDevice**: Toto je název zařízení, které registrujete. Doporučuje se používat **MyDotnetDevice** , jak je znázorněno na obrázku. Pokud pro vaše zařízení zvolíte jiný název, budete ho muset použít i v celém rámci tohoto článku a před jeho spuštěním aktualizovat název zařízení v ukázkových aplikacích.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

2. Spuštěním následujícího příkazu ve službě Azure Cloud Shell získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:

   **YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který jste zvolili pro Centrum IoT.

    ```azurecli-interactive
    az iot hub device-identity connection-string show \
      --hub-name {YourIoTHubName} \
      --device-id MyDotnetDevice \
      --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá takto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v tomto rychlém startu.

## <a name="retrieve-the-service-connection-string"></a>Načtení připojovacího řetězce služby

K povolení aplikace služby pro připojení k centru a k načtení zpráv potřebujete také _připojovací řetězec služby_ IoT Hub. Následující příkaz načte připojovací řetězec služby pro vaše centrum IoT:

```azurecli-interactive
az iot hub connection-string show --policy-name service --name {YourIoTHubName} --output table
```

Poznamenejte si připojovací řetězec služby, který vypadá nějak takto:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Tuto hodnotu použijete později v tomto rychlém startu. Tento připojovací řetězec služby se liší od připojovacího řetězce zařízení, který jste si poznamenali v předchozím kroku.

## <a name="listen-for-direct-method-calls"></a>Naslouchání voláním přímé metody

Aplikace simulovaného zařízení se připojí ke koncovému bodu v centru IoT pro konkrétní zařízení, odešle simulovaná telemetrická data a z vašeho centra naslouchá voláním přímé metody. Volání přímé metody z centra v tomto rychlém startu nařídí zařízení, aby změnilo interval, ve kterém se odesílají telemetrická data. Simulované zařízení po provedení přímé metody pošle potvrzení zpátky do vašeho centra.

1. V okně místního terminálu přejděte do kořenové složky ukázkového projektu jazyka C#. Pak přejděte do složky **IoT-hub\Quickstarts\SimulatedDeviceWithCommand** .

2. V okně místního terminálu pomocí následujících příkazů nainstalujte požadované balíčky pro aplikaci simulovaného zařízení:

    ```cmd/sh
    dotnet restore
    ```

3. V okně místního terminálu spusťte následující příkaz, který sestaví a spustí aplikaci simulovaného zařízení s náhradou `{DeviceConnectionString}` připojovacího řetězce zařízení, který jste si dříve poznamenali:

    ```cmd/sh
    dotnet run -- {DeviceConnectionString}
    ```

    Následující snímek obrazovky ukazuje výstup, zatímco aplikace simulovaného zařízení odesílá telemetrická data do vašeho centra IoT:

    ![Spuštění simulovaného zařízení](./media/quickstart-control-device-dotnet/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>Volání přímé metody

Aplikace služby se v IoT Hub připojí ke koncovému bodu na straně služby. Aplikace umožňuje přímé volání metod do zařízení prostřednictvím služby IoT Hub a naslouchá potvrzením. Aplikace služby IoT Hub obvykle běží v cloudu.

1. V jiném okně místního terminálu přejděte do kořenové složky ukázkového projektu jazyka C#. Pak přejděte do složky **IoT-hub\Quickstarts\InvokeDeviceMethod** .

2. V okně místního terminálu spuštěním následujících příkazů nainstalujte požadované knihovny pro aplikaci služby:

    ```cmd/sh
    dotnet restore
    ```

3. V okně místního terminálu spuštěním následujících příkazů Sestavte a spusťte aplikaci služby a nahraďte `{ServiceConnectionString}` připojovací řetězec služby, který jste si dříve poznamenali:

    ```cmd/sh
    dotnet run -- {ServiceConnectionString}
    ```

    Následující snímek obrazovky ukazuje výstup, ve kterém aplikace představuje přímé volání metody do zařízení a přijímá potvrzení:

    ![Spuštění aplikace služby](./media/quickstart-control-device-dotnet/BackEndApplication.png)

    Po spuštění aplikace služby se zobrazí zpráva v okně konzoly, ve kterém je spuštěno simulované zařízení, a rychlost, s jakou odesílá zprávy změny:

    ![Změna simulovaného klienta](./media/quickstart-control-device-dotnet/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste volali přímou metodu na zařízení z aplikace služby a odpověděli na přímé volání metody v aplikaci simulovaného zařízení.

Informace o tom, jak směrovat zprávy typu zařízení-cloud do různých cílů v cloudu, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Kurz: Směrování telemetrických dat do různých koncových bodů za účelem zpracování](tutorial-routing.md)
