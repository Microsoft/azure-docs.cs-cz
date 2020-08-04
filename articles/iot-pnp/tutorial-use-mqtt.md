---
title: Použití MQTT k vytvoření klienta zařízení služby IoT technologie Plug and Play Preview | Microsoft Docs
description: Použití protokolu MQTT přímo k vytvoření klienta zařízení IoT technologie Plug and Play Preview bez použití sad SDK pro zařízení Azure IoT
author: ericmitt
ms.author: ericmitt
ms.date: 05/13/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 56463b03fe633959585e14271050bcdaacb25663
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2020
ms.locfileid: "87535672"
---
# <a name="use-mqtt-to-develop-an-iot-plug-and-play-preview-device-client"></a>Použití MQTT k vývoji klienta zařízení IoT technologie Plug and Play Preview

Pokud je to možné, měli byste použít jednu ze sad SDK pro zařízení Azure IoT k vytváření technologie Plug and Play klientů zařízení IoT. Ve scénářích, jako je například použití paměťově omezeného zařízení, možná budete muset ke komunikaci se službou IoT Hub použít knihovnu MQTT.

Ukázka v tomto kurzu používá [Mosquitto](http://mosquitto.org/) Library MQTT a Visual Studio. Kroky v tomto kurzu předpokládají, že používáte Windows na vašem vývojovém počítači.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Požadavky

Pro dokončení tohoto kurzu ve Windows nainstalujte do svého místního prostředí Windows následující software:

* [Visual Studio (komunita, Professional nebo Enterprise)](https://visualstudio.microsoft.com/downloads/) – nezapomeňte při [instalaci](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019) sady Visual Studio zahrnout **vývoj desktopových aplikací pomocí C++** .
* [Git](https://git-scm.com/download/)
* [CMake](https://cmake.org/download/)
* [Průzkumník Azure IoT](howto-install-iot-explorer.md)

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Spuštěním následujícího příkazu Získejte sdílený přístupový podpis, který se zařízení připojí k vašemu rozbočovači. Poznamenejte si tento řetězec, použijete ho později v tomto kurzu:

```azurecli-interactive
az iot hub generate-sas-token -d <YourDeviceID> -n <YourIoTHubName>
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

Použijte připojovací řetězec služby IoT Hub ke konfiguraci nástroje **Azure IoT Explorer** :

1. Spusťte nástroj **Azure IoT Explorer** .
1. Na stránce **Nastavení** vložte připojovací řetězec ke službě IoT Hub do nastavení **Konfigurace aplikace** .
1. Vyberte **Uložit a připojit**.
1. Zařízení, které jste přidali dříve, je v seznamu zařízení na hlavní stránce.

## <a name="clone-sample-repo"></a>Klonovat ukázkové úložiště

Pomocí následujícího příkazu naklonujte ukázkové úložiště do vhodného umístění v místním počítači:

```cmd
git clone https://github.com/Azure-Samples/IoTMQTTSample.git
```

## <a name="install-mqtt-library"></a>Nainstalovat knihovnu MQTT

Pomocí tohoto `vcpkg` nástroje si stáhněte a sestavte knihovnu Mosquitto pro zatmění.

Pomocí následujícího příkazu naklonujte úložiště **Vcpkg** do vhodného umístění v místním počítači:

```cmd
git clone https://github.com/Microsoft/vcpkg.git
```

K přípravě prostředí použijte následující příkazy `vcpkg` . Když spustíte příkaz, budete potřebovat příkazový řádek se zvýšenými oprávněními `vcpkg integrate install` :

```cmd
cd vcpkg
.\bootstrap-vcpkg.bat
.\vcpkg integrate install
```

Pomocí následujícího příkazu Stáhněte a sestavte knihovnu Mosquitto pro zatmění:

```cmd
.\vcpkg install mosquitto:x64-windows
```

## <a name="migrate-the-sample-to-iot-plug-and-play"></a>Migrace ukázky do IoT technologie Plug and Play

### <a name="review-the-non-iot-plug-and-play-sample-code"></a>Přečtěte si vzorový kód jiný než IoT technologie Plug and Play

Než sestavíte a spustíte, aktualizujte kód s podrobnostmi služby IoT Hub a zařízení.

Chcete-li zobrazit ukázkový kód v aplikaci Visual Studio, otevřete soubor řešení *MQTTWin32. sln* ve složce *IoTMQTTSample\src\Windows* .

V **Průzkumník řešení**klikněte pravým tlačítkem na projekt **TelemetryMQTTWin32** a vyberte **nastavit jako spouštěný projekt**.

V projektu **TelemetryMQTTWin32** otevřete zdrojový soubor **MQTT_Mosquitto. cpp** . Aktualizujte definice informací o připojení s podrobnostmi o zařízení, které jste si poznamenali dříve. Nahraďte zástupný symbol řetězce tokenu pro:

* `IOTHUBNAME`identifikátor s názvem centra IoT, které jste vytvořili.
* `DEVICEID`identifikátor s názvem zařízení, které jste vytvořili.
* `PWD`identifikátor s hodnotou sdíleného přístupového podpisu, kterou jste vygenerovali pro dané zařízení.

Ověřte, že kód správně funguje, spuštěním Azure IoT Exploreru spusťte naslouchání telemetrie.

Spuštění aplikace (CTRL + F5) po několika sekundách vidíte výstup, který vypadá takto:

:::image type="content" source="media/tutorial-use-mqtt/mqtt-sample-output.png" alt-text="Výstup z ukázkové aplikace MQTT":::

V Azure IoT Exploreru vidíte, že zařízení není technologie Plug and Play zařízení IoT:

:::image type="content" source="media/tutorial-use-mqtt/non-pnp-iot-explorer.png" alt-text="Zařízení bez technologie Plug and Play IoT v Azure IoT Exploreru":::

### <a name="make-the-device-an-iot-plug-and-play-device"></a>Nastavení zařízení jako technologie Plug and Play IoT

Zařízení IoT technologie Plug and Play musí splňovat sadu jednoduchých konvencí. Když zařízení pošle ID modelu při připojení, bude se jednat o zařízení IoT technologie Plug and Play.

V této ukázce přidáte ID modelu * * do paketu připojení MQTT. ID modelu předáte jako parametr QueryString v `USERNAME` a změňte na `api-version` `2020-05-31-preview` :

```c
// computed Host Username and Topic
//#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2018-06-30"
#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2020-05-31-preview&model-id=dtmi:com:example:Thermostat;1"
#define PORT 8883
#define HOST IOTHUBNAME //".azure-devices.net"
#define TOPIC "devices/" DEVICEID "/messages/events/"
```

Znovu sestavte a spusťte ukázku.

V zařízení se teď bude jednat o ID modelu:

:::image type="content" source="media/tutorial-use-mqtt/model-id-iot-explorer.png" alt-text="Zobrazení ID modelu v Azure IoT Exploreru":::

Teď můžete přejít na součást technologie Plug and Play IoT:

:::image type="content" source="media/tutorial-use-mqtt/components-iot-explorer.png" alt-text="Zobrazit součásti v Azure IoT Exploreru":::

Teď můžete změnit kód zařízení, abyste implementovali telemetrii, vlastnosti a příkazy definované v modelu. Pokud chcete zobrazit ukázkovou implementaci zařízení termostata pomocí knihovny Mosquitto, přečtěte si téma [použití MQTT PNP s Azure IoTHub bez sady IoT SDK ve Windows](https://github.com/Azure-Samples/IoTMQTTSample/tree/master/src/Windows/PnPMQTTWin32) na GitHubu.

> [!NOTE]
> Ve výchozím nastavení je sdílený přístupový podpis platný jenom po 60 minutách.

> [!NOTE]
>Klient používá `IoTHubRootCA_Baltimore.pem` soubor kořenového certifikátu k ověření identity centra IoT, ke kterému se připojuje.

### <a name="mqtt-topics"></a>Témata MQTT

Následující definice jsou pro témata MQTT, která zařízení používá k odesílání informací do služby IoT Hub. Další informace najdete v tématu [komunikace se službou IoT Hub pomocí protokolu MQTT](../iot-hub/iot-hub-mqtt-support.md):

* `DEVICE_CAPABILITIES_MESSAGE`Definuje téma, které zařízení používá k tomu, aby nahlásilo rozhraní, které implementuje.
* `DEVICETWIN_PATCH_MESSAGE`Definuje téma, které zařízení používá k nahlášení aktualizací vlastností do centra IoT.
* `DEVICE_TELEMETRY_MESSAGE`Definuje téma, které zařízení používá k posílání telemetrie do služby IoT Hub.

Další informace o MQTT najdete v části [ukázky MQTT pro úložiště GitHub Azure IoT](https://github.com/Azure-Samples/IoTMQTTSample/) .

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak upravit klienta zařízení MQTT a postupovat podle konvencí technologie Plug and Play IoT. Další informace o službě IoT technologie Plug and Play najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Architektura](concepts-architecture.md)

Další informace o podpoře IoT Hub pro protokol MQTT najdete v tématech:

> [!div class="nextstepaction"]
> [Komunikace se službou IoT Hub pomocí protokolu MQTT](../iot-hub/iot-hub-mqtt-support.md)
