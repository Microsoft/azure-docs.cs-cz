---
title: Rychlý start k řízení zařízení ze služby Azure IoT Hub (.NET) | Microsoft Docs
description: V tomto rychlém startu spustíte dvě ukázkové aplikace C#. První aplikace je back-endová aplikace, která může vzdáleně řídit zařízení připojená k vašemu centru. Druhá aplikace simuluje zařízení připojené k vašemu centru, které je možné řídit vzdáleně.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/21/2019
ms.openlocfilehash: 740cb3a046514ffee9b2151315133220465878cf
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78673457"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-net"></a>Rychlý start: Řízení zařízení připojeného k centru IoT (.NET)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub je služba Azure, která vám umožní spravovat vaše zařízení IoT z cloudu a ingestovat velké objemy telemetrie zařízení do cloudu pro ukládání nebo zpracování. V tomto rychlém startu použijete *přímou metodu* k řízení simulovaného zařízení připojeného k centru IoT. Přímé metody můžete použít k provádění vzdálených změn chování zařízení připojeného k centru IoT.

Rychlý start používá dvě předem vytvořené aplikace .NET:

* Aplikaci simulovaného zařízení, která odpovídá na přímé metody volané z back-endové aplikace. Aby bylo možné přijímat volání přímé metody, připojí se tato aplikace ke koncovému bodu centra IoT pro konkrétní zařízení.

* Back-endovou aplikaci, která na simulovaném zařízení volá přímé metody. Aby na zařízení bylo možné volat přímou metodu, připojí se tato aplikace ke koncovému bodu na straně služby ve vašem centru IoT.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Dvě ukázkové aplikace, které spustíte v tomto rychlém startu, jsou napsány pomocí C#. Na počítači používaném pro vývoj musíte mít .NET Core SDK 2.1.0 nebo vyšší.

Sadu .NET Core SDK pro různé platformy si můžete stáhnout z webu [.NET](https://www.microsoft.com/net/download/all).

Aktuální verzi C# na počítači používaném pro vývoj můžete ověřit pomocí následujícího příkazu:

```cmd/sh
dotnet --version
```

Spusťte následující příkaz a přidejte rozšíření Microsoft Azure IoT extension pro Azure CLI do instance Cloud Shellu. Rozšíření IOT přidá do rozhraní příkazového příkazu Azure CLI specifické pro služby IoT Hub, IoT Edge a Služby zřizování zařízení IoT (DPS).

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

Pokud jste tak ještě neučinili, stáhněte ukázky https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip Azure IoT C# z archivu ZIP a extrahujte archiv ZIP.

Zkontrolujte, zda je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto rychlém startu používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokován v některých prostředích podnikové a vzdělávací sítě. Další informace a způsoby, jak tento problém vyřešit, najdete [v tématu připojení k centru IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Pokud jste dokončili předchozí [Rychlý start: Odesílání telemetrických dat ze zařízení do centra IoT](quickstart-send-telemetry-dotnet.md), můžete tento krok přeskočit.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrování zařízení

Pokud jste dokončili předchozí [Rychlý start: Odesílání telemetrických dat ze zařízení do centra IoT](quickstart-send-telemetry-dotnet.md), můžete tento krok přeskočit.

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell.

1. Spusťte následující příkaz v Azure Cloud Shell a vytvořte identitu zařízení.

   **YourIoTHubName**: Nahraďte tento zástupný symbol níže názvem, který jste vybrali pro svůj centr IoT.

   **MyDotnetDevice**: Toto je název zařízení, které registrujete. Doporučuje se používat **MyDotnetDevice,** jak je znázorněno. Pokud pro své zařízení zvolíte jiný název, musíte tento název použít v celém tomto článku a aktualizovat název zařízení v ukázkových aplikacích před jejich spuštěním.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

2. Spuštěním následujícího příkazu ve službě Azure Cloud Shell získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:

   **YourIoTHubName**: Nahraďte tento zástupný symbol níže názvem, který jste vybrali pro svůj centr IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name {YourIoTHubName} \
      --device-id MyDotnetDevice \
      --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá nějak takto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v tomto rychlém startu.

## <a name="retrieve-the-service-connection-string"></a>Načtení připojovacího řetězce služby

Potřebujete také _připojovací řetězec služby_ IoT Hub, který back-endové aplikaci umožní připojení k vašemu centru a načtení zpráv. Následující příkaz načte připojovací řetězec služby pro vaše centrum IoT:

```azurecli-interactive
az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
```

Poznamenejte si připojovací řetězec služby, který vypadá nějak takto:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Tuto hodnotu použijete později v tomto rychlém startu. Tento připojovací řetězec služby se liší od připojovacího řetězce zařízení, který jste zaznamenali v předchozím kroku.

## <a name="listen-for-direct-method-calls"></a>Naslouchání voláním přímé metody

Aplikace simulovaného zařízení se připojí ke koncovému bodu v centru IoT pro konkrétní zařízení, odešle simulovaná telemetrická data a z vašeho centra naslouchá voláním přímé metody. Volání přímé metody z centra v tomto rychlém startu nařídí zařízení, aby změnilo interval, ve kterém se odesílají telemetrická data. Simulované zařízení odešle potvrzení zpět do rozbočovače po spuštění přímé metody.

1. V okně místního terminálu přejděte do kořenové složky ukázkového projektu jazyka C#. Potom přejděte do složky **iot-hub\Quickstarts\simulated-device-2**.

2. V libovolném textovém editoru otevřete soubor **SimulatedDevice.cs**.

    Nahraďte hodnotu `s_connectionString` proměnné připojovacím řetězcem zařízení, který jste si dříve poznamenali. Potom změny uložte do **SimulatedDevice.cs**.

3. V okně místního terminálu pomocí následujících příkazů nainstalujte požadované balíčky pro aplikaci simulovaného zařízení:

    ```cmd/sh
    dotnet restore
    ```

4. Spuštěním následujícího příkazu v okně místního terminálu sestavte a spusťte aplikaci simulovaného zařízení:

    ```cmd/sh
    dotnet run
    ```

    Následující snímek obrazovky ukazuje výstup, zatímco aplikace simulovaného zařízení odesílá telemetrická data do vašeho centra IoT:

    ![Spuštění simulovaného zařízení](./media/quickstart-control-device-dotnet/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>Volání přímé metody

Back-endová aplikace se připojí ke koncovému bodu vašeho centra IoT na straně služby. Aplikace provádí volání přímé metody zařízení prostřednictvím služby IoT hub a naslouchá potvrzení. Back-endová aplikace služby IoT Hub se obvykle spouští v cloudu.

1. V jiném okně místního terminálu přejděte do kořenové složky ukázkového projektu jazyka C#. Potom přejděte do složky **iot-hub\Quickstarts\back-end-application**.

2. V libovolném textovém editoru otevřete soubor **BackEndApplication.cs**.

    Nahraďte hodnotu `s_connectionString` proměnné připojovacím řetězcem služby, který jste si dříve poznamenali. Potom změny uložte do **BackEndApplication.cs**.

3. V okně místního terminálu pomocí následujících příkazů nainstalujte požadované knihovny pro back-endovou aplikaci:

    ```cmd/sh
    dotnet restore
    ```

4. Spuštěním následujících příkazů v okně místního terminálu sestavte a spusťte back-endovou aplikaci:

    ```cmd/sh
    dotnet run
    ```

    Následující snímek obrazovky ukazuje výstup, protože aplikace provádí přímé volání metody zařízení a přijímá potvrzení:

    ![Spuštění back-endové aplikace](./media/quickstart-control-device-dotnet/BackEndApplication.png)

    Po spuštění back-endové aplikace se v okně konzoly se simulovaným zařízením zobrazí zpráva a rychlost odesílání zpráv se změní:

    ![Změna simulovaného klienta](./media/quickstart-control-device-dotnet/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste volali přímou metodu na zařízení z back-endové aplikace a odpověděli na volání přímé metody v aplikaci simulované zařízení.

Informace o tom, jak směrovat zprávy typu zařízení-cloud do různých cílů v cloudu, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Kurz: Směrování telemetrických dat do různých koncových bodů za účelem zpracování](tutorial-routing.md)
