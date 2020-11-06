---
title: Kurz – použití MQTT k vytvoření klienta zařízení Azure IoT technologie Plug and Play | Microsoft Docs
description: Kurz – použití protokolu MQTT přímo k vytvoření klienta zařízení IoT technologie Plug and Play bez použití sad SDK pro zařízení Azure IoT
author: ericmitt
ms.author: ericmitt
ms.date: 05/13/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 6852b0532b23e46c7b986926b21cd0b7e9f9736d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421375"
---
# <a name="tutorial---use-mqtt-to-develop-an-iot-plug-and-play-device-client"></a>Kurz – použití MQTT ke zřízení klienta zařízení IoT technologie Plug and Play

Pokud je to možné, měli byste použít jednu ze sad SDK pro zařízení Azure IoT k vytváření technologie Plug and Play klientů zařízení IoT. Ve scénářích, jako je například použití paměťově omezeného zařízení, možná budete muset ke komunikaci se službou IoT Hub použít knihovnu MQTT.

Ukázka v tomto kurzu používá [Mosquitto](http://mosquitto.org/) Library MQTT a Visual Studio. Kroky v tomto kurzu předpokládají, že používáte Windows na vašem vývojovém počítači.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Pro dokončení tohoto kurzu ve Windows nainstalujte do svého místního prostředí Windows následující software:

* [Visual Studio (komunita, Professional nebo Enterprise)](https://visualstudio.microsoft.com/downloads/) – nezapomeňte při [instalaci](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) sady Visual Studio zahrnout **vývoj desktopových aplikací pomocí C++** .
* [Git](https://git-scm.com/download/)
* [CMake](https://cmake.org/download/)

Pomocí nástroje *Azure IoT Explorer* přidejte do svého IoT Hub nové zařízení. Službu IoT Hub a nástroj Azure IoT Explorer jste nakonfigurovali po dokončení [nastavení prostředí pro technologie Plug and Play rychlý Start a kurzy pro IoT](set-up-environment.md):

1. Spusťte nástroj **Azure IoT Explorer** .
1. Na stránce **centra IoT** vyberte **Zobrazit zařízení v tomto centru**.
1. Na stránce **zařízení** vyberte **+ Nový**.
1. Vytvořte zařízení s názvem *My-MQTT-Device* , které používá automaticky generovaný symetrický klíč.
1. Na stránce **Identita zařízení** rozbalte **připojovací řetězec s tokenem SAS**.
1. Zvolte **primární klíč** , který chcete použít jako **symetrický klíč** , nastavte čas vypršení platnosti na 60 minut a vyberte **Generovat**.
1. Zkopírujte vygenerovaný **připojovací řetězec tokenu SAS** , použijte tuto hodnotu později v tomto kurzu.

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

V **Průzkumník řešení** klikněte pravým tlačítkem na projekt **TelemetryMQTTWin32** a vyberte **nastavit jako spouštěný projekt**.

V projektu **TelemetryMQTTWin32** otevřete zdrojový soubor **MQTT_Mosquitto. cpp** . Aktualizujte definice informací o připojení s podrobnostmi o zařízení, které jste si poznamenali dříve. Nahraďte zástupné symboly řetězce tokenu pro:

* `IOTHUBNAME` identifikátor s názvem vašeho centra IoT Hub.
* `DEVICEID` identifikátor s `my-mqtt-device` .
* `PWD` identifikátor se správnou částí připojovacího řetězce tokenu SAS, který jste vygenerovali pro zařízení. Použijte část připojovacího řetězce od do `SharedAccessSignature sr=` konce.

Ověřte, že kód správně funguje, spuštěním Azure IoT Exploreru spusťte naslouchání telemetrie.

Spuštění aplikace (CTRL + F5) po několika sekundách vidíte výstup, který vypadá takto:

:::image type="content" source="media/tutorial-use-mqtt/mqtt-sample-output.png" alt-text="Výstup z ukázkové aplikace MQTT":::

V Azure IoT Exploreru vidíte, že zařízení není technologie Plug and Play zařízení IoT:

:::image type="content" source="media/tutorial-use-mqtt/non-pnp-iot-explorer.png" alt-text="Zařízení bez technologie Plug and Play IoT v Azure IoT Exploreru":::

### <a name="make-the-device-an-iot-plug-and-play-device"></a>Nastavení zařízení jako technologie Plug and Play IoT

Zařízení IoT technologie Plug and Play musí splňovat sadu jednoduchých konvencí. Když zařízení pošle ID modelu při připojení, bude se jednat o zařízení IoT technologie Plug and Play.

V této ukázce přidáte ID modelu do paketu připojení MQTT. ID modelu předáte jako parametr QueryString v `USERNAME` a změňte na `api-version` `2020-09-30` :

```c
// computed Host Username and Topic
//#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2018-06-30"
#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2020-09-30&model-id=dtmi:com:example:Thermostat;1"
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
>Klient používá `IoTHubRootCA_Baltimore.pem` soubor kořenového certifikátu k ověření identity centra IoT, ke kterému se připojuje.

### <a name="mqtt-topics"></a>Témata MQTT

Následující definice jsou pro témata MQTT, která zařízení používá k odesílání informací do služby IoT Hub. Další informace najdete v tématu [komunikace se službou IoT Hub pomocí protokolu MQTT](../iot-hub/iot-hub-mqtt-support.md):

* `DEVICE_CAPABILITIES_MESSAGE`Definuje téma, které zařízení používá k tomu, aby nahlásilo rozhraní, které implementuje.
* `DEVICETWIN_PATCH_MESSAGE`Definuje téma, které zařízení používá k nahlášení aktualizací vlastností do centra IoT.
* `DEVICE_TELEMETRY_MESSAGE`Definuje téma, které zařízení používá k posílání telemetrie do služby IoT Hub.

Další informace o MQTT najdete v části [ukázky MQTT pro úložiště GitHub Azure IoT](https://github.com/Azure-Samples/IoTMQTTSample/) .
  
## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak upravit klienta zařízení MQTT a postupovat podle konvencí technologie Plug and Play IoT. Další informace o službě IoT technologie Plug and Play najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Architektura](concepts-architecture.md)

Další informace o podpoře IoT Hub pro protokol MQTT najdete v tématech:

> [!div class="nextstepaction"]
> [Komunikace se službou IoT Hub pomocí protokolu MQTT](../iot-hub/iot-hub-mqtt-support.md)