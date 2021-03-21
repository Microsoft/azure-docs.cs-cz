---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 3bf5ac4e01bca3bfc3cc8720a068bc53830b4747
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104612587"
---
V tomto kurzu se dozvíte, jak vytvořit ukázkovou aplikaci IoT technologie Plug and Play zařízení s více komponentami, jak ji připojit ke službě IoT Hub a použít Azure CLI k zobrazení telemetrie, kterou posílá. Ukázková aplikace je napsaná v jazyce Java a je obsažená v sadě SDK pro zařízení Azure IoT pro jazyk Java. Tvůrce řešení může pomocí Azure CLI porozumět možnostem zařízení technologie Plug and Play IoT, aniž by bylo nutné zobrazovat kód zařízení.

V tomto kurzu se dozvíte, jak vytvořit ukázkovou aplikaci IoT technologie Plug and Play zařízení pomocí komponent, připojit ji k centru IoT a pomocí nástroje Azure IoT Explorer zobrazit informace, které posílá do centra. Ukázková aplikace je napsaná v jazyce Java a je obsažená v sadě SDK pro zařízení Azure IoT pro jazyk Java. Tvůrce řešení může pomocí nástroje Azure IoT Explorer pochopit možnosti zařízení technologie Plug and Play IoT, aniž by bylo nutné zobrazovat kód zařízení.

V tomto kurzu jste:

> [!div class="checklist"]
> * Stáhněte si vzorový kód.
> * Sestavte vzorový kód.
> * Spusťte ukázkovou aplikaci zařízení a ověřte, že se připojí ke službě IoT Hub.
> * Zkontrolujte zdrojový kód.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Pro dokončení tohoto kurzu ve Windows nainstalujte do svého místního prostředí Windows následující software:

* Java SE Development Kit 8. V [dlouhodobé podpoře jazyka Java pro Azure a Azure Stack](/java/azure/jdk/)v části **Dlouhodobá podpora** vyberte **Java 8**.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

## <a name="download-the-code"></a>Stáhněte si kód

Pokud jste dokončili [rychlý Start: Připojte ukázkovou aplikaci IoT technologie Plug and Play zařízení běžící v systému Windows k IoT Hub (Java)](../articles/iot-pnp/quickstart-connect-device.md), již jste naklonoval úložiště.

Otevřete příkazový řádek v adresáři dle vašeho výběru. Spusťte následující příkaz, který naklonuje úložiště GitHubu pro [Azure IoT Java SDK a knihovny](https://github.com/Azure/azure-iot-sdk-java) do tohoto umístění:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

Očekává se, že dokončení této operace trvá několik minut.

## <a name="build-the-code"></a>Sestavení kódu

Ve Windows přejděte do kořenové složky naklonovaného úložiště sady Java SDK. Spuštěním následujícího příkazu Sestavte závislosti:

```cmd/sh
mvn install -T 2C -DskipTests
```

## <a name="run-the-device-sample"></a>Spuštění ukázky zařízení

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Chcete-li spustit ukázkovou aplikaci, přejděte do složky *\device\iot-Device-samples\pnp-Device-sample\temperature-Controller-Device-Sample* a spusťte následující příkaz:

```cmd/sh
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.TemperatureController"
```

Zařízení je teď připravené přijmout příkazy a aktualizace vlastností a zahájilo posílání dat telemetrie do centra. Ponechte ukázku spuštěnou při dokončení dalších kroků.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Použití Azure IoT Exploreru k ověření kódu

Po spuštění ukázky klienta zařízení použijte nástroj Azure IoT Explorer a ověřte, jestli funguje.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Kontrola kódu

Tato ukázka implementuje zařízení adaptéru technologie Plug and Play pro řízení teploty pro IoT. Model, který tato ukázka implementuje, používá [více komponent](../articles/iot-pnp/concepts-modeling-guide.md). [Soubor modelu DTDL (Digital Real Definition Language) pro zařízení s teplotou](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definuje telemetrii, vlastnosti a příkazy, které zařízení implementuje.

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

:::image type="content" source="media/iot-pnp-multiple-components-java/multiple-component.png" alt-text="Více zařízení komponent v Azure IoT Exploreru":::

Pomocí nástroje Azure IoT Explorer můžete také volat příkazy v obou dvou přísoučástcích termostatu nebo ve výchozí součásti.
