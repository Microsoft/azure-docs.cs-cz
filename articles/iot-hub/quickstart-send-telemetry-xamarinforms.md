---
title: Rychlý start k odesílání telemetrických dat do služby Azure IoT Hub | Microsoft Docs
description: V tomto rychlém startu spustíte ukázkovou aplikaci Xamarin Forms k odeslání simulované telemetrie do služby IoT Hub a ke čtení telemetrie ze služby IoT Hub ke zpracování v cloudu.
author: cmaneu
manager: philmea
ms.author: chmaneu
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
ms.date: 05/20/2020
ms.openlocfilehash: d2292a65ef25f6450d6810b50366daa622b92bc8
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2020
ms.locfileid: "85802703"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-xamarin-forms"></a>Rychlý Start: odeslání telemetrie ze zařízení do služby IoT Hub (formuláře Xamarin)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub je služba Azure, která umožňuje ingestovat velké objemy telemetrických dat ze zařízení IoT do cloudu pro účely uložení nebo zpracování. V tomto článku budete do služby IoT Hub odesílat telemetrická data z aplikace simulovaného zařízení. Pak můžete data zobrazit v back-endové aplikaci.

Tento článek používá předem napsanou aplikaci Xamarin Forms k odeslání telemetrie a nástroje CLI ke čtení telemetrie z IoT Hub.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.


## <a name="prerequisites"></a>Požadavky

- Stažení vzorového kódu z [ukázek Azure](https://github.com/Azure-Samples/azure-iot-samples-xamarin/archive/master.zip).

- Nejnovější verzi sady [Visual Studio 2019](https://visualstudio.microsoft.com/) nebo [Visual Studio pro Mac](https://visualstudio.microsoft.com/) s nainstalovanou sadou nástrojů Xamarin Forms. Tento rychlý Start byl testován pomocí sady Visual Studio 16.6.0.

- Ujistěte se, že je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto rychlém startu používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokovaný v některých podnikových a vzdělávacích prostředích sítě. Další informace a způsoby, jak tento problém obejít, najdete v tématu [připojení k IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

- Spuštěním následujícího příkazu přidejte do instance služby Cloud Shell Microsoft Azure rozšíření IoT pro rozhraní příkazového řádku Azure. Rozšíření IoT přidá do Azure CLI příkazy určené pro služby IoT Hub, IoT Edge a IoT Device Provisioning Service (DPS).

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrování zařízení

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell.

1. Spuštěním následujícího příkazu v Azure Cloud Shell vytvořte identitu zařízení.

   **YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který jste zvolili pro Centrum IoT.

   **myXamarinDevice**: Toto je název zařízení, které registrujete. Doporučuje se používat **myXamarinDevice** , jak je znázorněno na obrázku. Pokud pro vaše zařízení zvolíte jiný název, budete ho muset použít i v celém rámci tohoto článku a před jeho spuštěním aktualizovat název zařízení v ukázkových aplikacích.

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {YourIoTHubName} --device-id myXamarinDevice
   ```

1. Spuštěním následujícího příkazu v Azure Cloud Shell Získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:

   **YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který jste zvolili pro Centrum IoT.

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id myXamarinDevice --output table
   ```

   Poznamenejte si připojovací řetězec zařízení, který vypadá nějak takto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=myXamarinDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v rychlém startu.

## <a name="send-simulated-telemetry"></a>Odesílání simulovaných telemetrických dat

Ukázková aplikace běží ve Windows – přes aplikaci UWP – zařízení s iOS nebo simulátor, zařízení nebo simulátor Androidu, které se připojí ke koncovému bodu specifickému pro zařízení ve službě IoT Hub a odesílá simulaci simulované teploty a vlhkosti. 

1. Otevřete Vzorový pracovní prostor v aplikaci Visual Studio nebo Visual Studio pro Mac.
2. Rozbalte projekt **SimulatedDevice** .  
3. Otevřete **IoTHubService.cs** pro úpravy v aplikaci Visual Studio. 
4. Vyhledejte **_iotHubConnectionString** proměnnou a aktualizujte hodnotu pomocí připojovacího řetězce zařízení, který jste si poznamenali dříve.
5. Uložte provedené změny. 
6. Spusťte projekt v emulátoru zařízení nebo reálné zařízení s tlačítkem **Sestavit a spustit** nebo klávesovou zkratkou **F5** ve Windows nebo v **příkazu + r** na Macu. 

   ![Spuštění projektu](media/quickstart-send-telemetry-xamarinforms/run-sample.png)

7. Jakmile se otevře emulátor, vyberte v ukázkové aplikaci **Start** (Spustit).

Následující snímek obrazovky ukazuje příklad výstupu, protože aplikace odesílá simulovanou telemetrii do služby IoT Hub: ![ spuštění simulovaného zařízení](media/quickstart-send-telemetry-xamarinforms/view-d2c.png)

## <a name="read-the-telemetry-from-your-hub"></a>Čtení telemetrických dat z centra

Ukázková aplikace, kterou jste spustili v emulátoru XCode, ukazuje data o zprávách odeslaných ze zařízení. Můžete zobrazit také přijatá data procházející přes vaše centrum IoT. Rozšíření IoT Hub CLI se může ve vaší službě IoT Hub připojit ke koncovému bodu **Události** na straně služby. Toto rozšíření přijímá zprávy ze zařízení do cloudu odesílané z vašeho simulovaného zařízení. Back-endová aplikace služby IoT Hub se obvykle spouští v cloudu, aby mohla přijímat a zpracovávat zprávy typu zařízení-cloud.

Ve službě Azure Cloud Shell spusťte následující příkazy a položku `YourIoTHubName` nahraďte názvem centra IoT:

```azurecli-interactive
az iot hub monitor-events --device-id myXamarinDevice --hub-name {YourIoTHubName}
```

Následující snímek obrazovky ukazuje výstup, když rozšíření přijímá telemetrická data odesílaná simulovaným zařízením do centra:

Následující snímek obrazovky ukazuje typ telemetrie, který se zobrazí v místním okně terminálu: ![ Zobrazit telemetrii](media/quickstart-send-telemetry-ios/view-telemetry.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu nastavíte centrum IoT, zaregistrovali zařízení, odeslali simulovanou telemetrii do centra z aplikace Xamarin Forms a přečetli telemetrii z centra. 

Informace o tom, jak řídit simulované zařízení z back-endové aplikace, najdete v dalším rychlém startu.

> [!div class="nextstepaction"]
> [Rychlý start: Řízení zařízení připojeného k centru IoT](quickstart-control-device-node.md)
