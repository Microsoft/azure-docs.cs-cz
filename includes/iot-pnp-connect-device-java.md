---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 7961dae0ef227a7e321992e0b239f7a3e6e66b0a
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2020
ms.locfileid: "95510583"
---
V tomto rychlém startu se dozvíte, jak vytvořit ukázkovou aplikaci IoT technologie Plug and Play zařízení, jak ji připojit k centru IoT a použít nástroj Azure IoT Explorer k zobrazení telemetrie, kterou posílá. Ukázková aplikace je napsaná v jazyce Java a je obsažená v sadě SDK pro zařízení Azure IoT pro jazyk Java. Tvůrce řešení může pomocí nástroje Azure IoT Explorer pochopit možnosti zařízení technologie Plug and Play IoT, aniž by bylo nutné zobrazovat kód zařízení.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

K dokončení tohoto rychlého startu ve Windows nainstalujte do svého místního prostředí Windows následující software:

* Java SE Development Kit 8. V [dlouhodobé podpoře jazyka Java pro Azure a Azure Stack](/java/azure/jdk/?preserve-view=true&view=azure-java-stable)v části **Dlouhodobá podpora** vyberte **Java 8**.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

## <a name="download-the-code"></a>Stáhněte si kód

V tomto rychlém startu připravíte vývojové prostředí, které můžete použít k klonování a sestavení sady SDK pro zařízení s IoT Hub v jazyce Java.

Otevřete příkazový řádek v adresáři dle vašeho výběru. Spusťte následující příkaz, který naklonuje úložiště GitHubu pro [Azure IoT Java SDK a knihovny](https://github.com/Azure/azure-iot-sdk-java) do tohoto umístění:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

## <a name="build-the-code"></a>Sestavení kódu

Ve Windows přejděte do kořenové složky naklonovaného úložiště sady Java SDK.

Spusťte následující příkaz, který sestaví ukázkovou aplikaci:

```cmd
mvn install -T 2C -DskipTests
```

## <a name="run-the-device-sample"></a>Spuštění ukázky zařízení

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Další informace o ukázkové konfiguraci najdete v [ukázkovém souboru Readme](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/pnp-device-sample/readme.md).

Přejděte do složky *\device\iot-Device-samples\pnp-Device-sample\thermostat-Device-Sample* .

Chcete-li spustit ukázkovou aplikaci, spusťte následující příkaz:

```cmd
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
```

Zařízení je teď připravené přijmout příkazy a aktualizace vlastností a zahájilo posílání dat telemetrie do centra. Ponechte ukázku spuštěnou při dokončení dalších kroků.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Použití Azure IoT Exploreru k ověření kódu

Po spuštění ukázky klienta zařízení použijte nástroj Azure IoT Explorer a ověřte, jestli funguje.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Kontrola kódu

Tato ukázka implementuje jednoduché zařízení IoT technologie Plug and Play termostat. Model, který tento ukázkový implementuje, nepoužívá [komponenty](../articles/iot-pnp/concepts-components.md)IoT technologie Plug and Play. [Soubor modelu DTDL pro zařízení termostata](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) definuje telemetrii, vlastnosti a příkazy, které zařízení implementuje.

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

Kód, který aktualizuje vlastnosti, zpracovává příkazy a odesílá telemetrii, je stejný jako kód pro zařízení, které nepoužívá konvence technologie Plug and Play IoT.

Ukázka používá knihovnu JSON k analýze objektů JSON v datových vytíženích odeslaných ze služby IoT Hub:

```java
import com.google.gson.Gson;

...

Date since = new Gson().fromJson(jsonRequest, Date.class);
```
