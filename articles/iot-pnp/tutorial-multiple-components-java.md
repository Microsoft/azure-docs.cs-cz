---
title: Připojte si ukázkový kód zařízení s komponentou IoT technologie Plug and Play Preview k IoT Hub | Microsoft Docs
description: Sestavte a spouštějte ukázkový kód zařízení Java ve verzi Preview technologie Plug and Play, který používá více komponent a připojuje se ke centru IoT. K zobrazení informací odesílaných zařízením do centra použijte nástroj Azure IoT Explorer.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 1d16d8c54939c4f659b6a1530e2d360b957a09ad
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352763"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-preview-multiple-component-device-application-to-iot-hub-java"></a>Kurz: připojení ukázkové IoT technologie Plug and Play ve verzi Preview aplikace zařízení s více komponentami pro IoT Hub (Java)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

V tomto kurzu se dozvíte, jak vytvořit ukázkovou aplikaci IoT technologie Plug and Play zařízení s více komponentami, jak ji připojit ke službě IoT Hub a použít Azure CLI k zobrazení telemetrie, kterou posílá. Ukázková aplikace je napsaná v jazyce Java a je obsažená v sadě SDK pro zařízení Azure IoT pro jazyk Java. Tvůrce řešení může pomocí Azure CLI porozumět možnostem zařízení technologie Plug and Play IoT, aniž by bylo nutné zobrazovat kód zařízení.

V tomto kurzu se dozvíte, jak vytvořit ukázkovou aplikaci IoT technologie Plug and Play pro zařízení pomocí komponent a kořenového rozhraní, připojit ho ke službě IoT Hub a pomocí nástroje Azure IoT Explorer zobrazit informace, které posílá do centra. Ukázková aplikace je napsaná v jazyce Java a je obsažená v sadě SDK pro zařízení Azure IoT pro jazyk Java. Tvůrce řešení může pomocí nástroje Azure IoT Explorer pochopit možnosti zařízení technologie Plug and Play IoT, aniž by bylo nutné zobrazovat kód zařízení.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Předpoklady

Pro dokončení tohoto kurzu ve Windows nainstalujte do svého místního prostředí Windows následující software:

* Java SE Development Kit 8. V [dlouhodobé podpoře jazyka Java pro Azure a Azure Stack](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)v části **Dlouhodobá podpora**vyberte **Java 8**.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

### <a name="azure-iot-explorer"></a>Průzkumník Azure IoT

Pokud chcete s ukázkovým zařízením pracovat v druhé části tohoto rychlého startu, použijte nástroj **Azure IoT Explorer** . [Stáhněte a nainstalujte si nejnovější verzi Azure IoT Exploreru](./howto-use-iot-explorer.md) pro váš operační systém.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Spuštěním následujícího příkazu Získejte _připojovací řetězec služby IoT Hub_ pro vaše centrum. Poznamenejte si tento připojovací řetězec, budete ho používat později v tomto rychlém startu:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Připojovací řetězec služby IoT Hub můžete najít také pomocí nástroje Azure IoT Explorer.

Spuštěním následujícího příkazu Získejte _připojovací řetězec zařízení_ pro zařízení, které jste přidali do centra. Poznamenejte si tento připojovací řetězec, budete ho používat později v tomto rychlém startu:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>Stáhněte si kód

V tomto kurzu připravíte vývojové prostředí, které můžete použít k klonování a sestavení sady SDK pro zařízení s IoT Hub v jazyce Java.

Otevřete příkazový řádek v adresáři dle vašeho výběru. Spusťte následující příkaz, který naklonuje úložiště GitHubu pro [Azure IoT Java SDK a knihovny](https://github.com/Azure/azure-iot-sdk-java) do tohoto umístění:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

Očekává se, že dokončení této operace trvá několik minut.

## <a name="build-the-code"></a>Sestavení kódu

Ve Windows přejděte do kořenové složky naklonovaného úložiště sady Java SDK. Pak přejděte do složky *\device\iot-Device-samples\pnp-Device-sample\temerature-Controller-Device-Sample* .

Spusťte následující příkaz, který sestaví ukázkovou aplikaci:

```java
mvn clean package
```

## <a name="run-the-device-sample"></a>Spuštění ukázky zařízení

Vytvořte proměnnou prostředí s názvem **IOTHUB_DEVICE_CONNECTION_STRING** pro uložení připojovacího řetězce zařízení, který jste si poznamenali dříve.

Chcete-li spustit ukázkovou aplikaci, spusťte následující příkaz:

```java
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.TemperatureController"
```

Zařízení je teď připravené přijmout příkazy a aktualizace vlastností a zahájilo posílání dat telemetrie do centra. Ponechte ukázku spuštěnou při dokončení dalších kroků.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Použití Azure IoT Exploreru k ověření kódu

Po spuštění ukázky klienta zařízení použijte nástroj Azure IoT Explorer a ověřte, jestli funguje.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Kontrola kódu

Tato ukázka implementuje zařízení adaptéru technologie Plug and Play pro řízení teploty pro IoT. Model, který tato ukázka implementuje, používá [více komponent](concepts-components.md). [Soubor modelu DTDL (Digital Real Definition Language) pro zařízení s teplotou](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definuje telemetrii, vlastnosti a příkazy, které zařízení implementuje.

Kód zařízení používá standardní `DeviceClient` třídu pro připojení ke službě IoT Hub. Zařízení odešle ID modelu DTDL modelu, který implementuje v žádosti o připojení. Zařízení, které odesílá ID modelu, je zařízení IoT technologie Plug and Play:

```java
private static void initializeDeviceClient() throws URISyntaxException, IOException {
    ClientOptions options = new ClientOptions();
    options.setModelId(MODEL_ID);
    deviceClient = new DeviceClient(deviceConnectionString, protocol, options);

    deviceClient.registerConnectionStatusChangeCallback((status, statusChangeReason, throwable, callbackContext) -> {
        log.debug("Connection status change registered: status={}, reason={}", status, statusChangeReason);

        if (throwable != null) {
            log.debug("The connection status change was caused by the following Throwable: {}", throwable.getMessage());
            throwable.printStackTrace();
        }
    }, deviceClient);

    deviceClient.open();
}
```

ID modelu je uloženo v kódu, jak je znázorněno v následujícím fragmentu kódu:

```java
private static final String MODEL_ID = "dtmi:com:example:Thermostat;1";
```

Po připojení zařízení ke službě IoT Hub kód zaregistruje obslužné rutiny příkazů.

```java
deviceClient.subscribeToDeviceMethod(new MethodCallback(), null, new MethodIotHubEventCallback(), null);
```

Existují samostatné obslužné rutiny pro požadované aktualizace vlastností na dvou součástech termostatu:

```java
deviceClient.startDeviceTwin(new TwinIotHubEventCallback(), null, new GenericPropertyUpdateCallback(), null);
Map<Property, Pair<TwinPropertyCallBack, Object>> desiredPropertyUpdateCallback = Stream.of(
        new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
                new Property(THERMOSTAT_1, null),
                new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_1)),
        new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
                new Property(THERMOSTAT_2, null),
                new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_2))
).collect(Collectors.toMap(AbstractMap.SimpleEntry::getKey, AbstractMap.SimpleEntry::getValue));

deviceClient.subscribeToTwinDesiredProperties(desiredPropertyUpdateCallback);
```

Vzorový kód odesílá telemetrii z každé komponenty termostatu:

```java
sendTemperatureReading(THERMOSTAT_1);
sendTemperatureReading(THERMOSTAT_2);
```

`sendTemperatureReading`Metoda používá `PnpHhelper` třídu k vytváření zpráv pro každou komponentu:

```java
Message message = PnpHelper.createIotHubMessageUtf8(telemetryName, currentTemperature, componentName);
```

`PnpHelper`Třída obsahuje další ukázkové metody, které lze použít s více modelem komponent.

K zobrazení telemetrie a vlastností ze dvou součástí termostatu použijte nástroj Azure IoT Explorer:

:::image type="content" source="media/tutorial-multiple-components-java/multiple-component.png" alt-text="Více zařízení komponent v Azure IoT Exploreru":::

Pomocí nástroje Azure IoT Explorer můžete také volat příkazy v některé ze dvou součástí termostatu nebo v kořenovém rozhraní.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili připojit zařízení IoT technologie Plug and Play k komponentám do služby IoT Hub. Další informace o modelech zařízení IoT technologie Plug and Play najdete v tématech:

> [!div class="nextstepaction"]
> [Příručka pro vývojáře pro modelování IoT technologie Plug and Play Preview](concepts-developer-guide.md)
