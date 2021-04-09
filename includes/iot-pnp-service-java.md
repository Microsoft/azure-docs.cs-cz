---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 8d3f35a733a0f78fabc33df857d911ba3cd222f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "102244454"
---
IoT technologie Plug and Play zjednodušuje IoT tím, že vám umožní pracovat s funkcemi zařízení bez znalosti základní implementace zařízení. V tomto rychlém startu se dozvíte, jak pomocí jazyka Java připojit a řídit zařízení IoT technologie Plug and Play, které je připojené k vašemu řešení.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

K dokončení tohoto rychlého startu ve Windows nainstalujte do svého místního prostředí Windows následující software:

* Java SE Development Kit 8. V [dlouhodobé podpoře jazyka Java pro Azure a Azure Stack](/java/azure/jdk/)v části **Dlouhodobá podpora** vyberte **Java 8**.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Naklonujte úložiště sady SDK pomocí ukázkového kódu.

Pokud jste dokončili [rychlý Start: Připojte ukázkovou aplikaci IoT technologie Plug and Play zařízení běžící v systému Windows k IoT Hub (Java)](../articles/iot-pnp/quickstart-connect-device.md), již jste naklonoval úložiště.

Otevřete příkazový řádek v adresáři dle vašeho výběru. Spusťte následující příkaz, který naklonuje [Microsoft Azure sady SDK IoT pro](https://github.com/Azure/azure-iot-sdk-java) úložiště GitHub Java do tohoto umístění:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

## <a name="build-and-run-the-sample-device"></a>Sestavení a spuštění ukázkového zařízení

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Další informace o ukázkové konfiguraci najdete v [ukázkovém souboru Readme](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md).

V tomto rychlém startu použijete vzorový termostat zařízení, které je napsané v jazyce Java jako zařízení IoT technologie Plug and Play. Spuštění ukázkového zařízení:

1. Otevřete okno terminálu a přejděte do místní složky, která obsahuje sadu Microsoft Azure IoT SDK pro úložiště v jazyce Java, kterou jste naklonoval z GitHubu.

1. Toto okno terminálu slouží jako terminál **zařízení** . Přejít do kořenové složky naklonovaného úložiště. Všechny závislosti Nainstalujte spuštěním následujícího příkazu:

1. Spusťte následující příkaz, který sestaví ukázkovou aplikaci zařízení:

    ```cmd
    mvn install -T 2C -DskipTests
    ```

1. Pokud chcete spustit ukázkovou aplikaci zařízení, přejděte do složky *device\iot-Device-samples\pnp-Device-sample\thermostat-Device-Sample* a spusťte následující příkaz:

    ```cmd
    mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
    ```

Zařízení je teď připravené přijmout příkazy a aktualizace vlastností a zahájilo posílání dat telemetrie do centra. Po dokončení dalších kroků nechejte ukázkové zařízení spuštěné.

## <a name="run-the-sample-solution"></a>Spuštění ukázkového řešení

V [části nastavení prostředí pro iot technologie Plug and Play rychlé starty a kurzy](../articles/iot-pnp/set-up-environment.md) , které jste vytvořili dvě proměnné prostředí pro konfiguraci ukázky pro připojení ke službě IoT Hub a zařízení:

* **IOTHUB_CONNECTION_STRING**: připojovací řetězec ke službě IoT Hub jste si poznamenali dříve.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"` .

V tomto rychlém startu použijete ukázkové řešení IoT napsané v jazyce Java k interakci s ukázkovým zařízením, které jste právě nastavili.

> [!NOTE]
> Tato ukázka používá obor názvů **com. Microsoft. Azure. SDK. IoT. Service** z **klienta služby IoT Hub**. Další informace o rozhraních API, včetně rozhraní Digital props API, najdete v [příručce pro vývojáře služby](../articles/iot-pnp/concepts-developer-guide-service.md).

1. Otevřete další okno terminálu pro použití jako terminálu **služby** .

1. V naklonovaném úložišti Java SDK přejděte do složky *service\iot-Service-samples\pnp-Service-sample\thermostat-Service-Sample* .

1. Chcete-li spustit ukázkovou aplikaci služby, spusťte následující příkaz:

    ```cmd
    mvm exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.service.Thermostat"
    ```

### <a name="get-device-twin"></a>Získat dvojitou dvojici zařízení

Následující fragment kódu ukazuje, jak načíst ve službě vlákna ve službě:

```java
 // Get the twin and retrieve model Id set by Device client.
DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);
System.out.println("Model Id of this Twin is: " + twin.getModelId());
```

### <a name="update-a-device-twin"></a>Aktualizace vlákna v zařízení

Následující fragment kódu ukazuje, jak použít *opravu* k aktualizaci vlastností prostřednictvím vlákna zařízení:

```java
String propertyName = "targetTemperature";
double propertyValue = 60.2;
twin.setDesiredProperties(Collections.singleton(new Pair(propertyName, propertyValue)));
twinClient.updateTwin(twin);
```

Výstup zařízení ukazuje, jak zařízení reaguje na tuto aktualizaci vlastností.

### <a name="invoke-a-command"></a>Vyvolání příkazu

Následující fragment kódu ukazuje volání příkazu na zařízení:

```java
// The method to invoke for a device without components should be "methodName" as defined in the DTDL.
String methodToInvoke = "getMaxMinReport";
System.out.println("Invoking method: " + methodToInvoke);

Long responseTimeout = TimeUnit.SECONDS.toSeconds(200);
Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);

// Invoke the command.
String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);
MethodResult result = methodClient.invoke(deviceId, methodToInvoke, responseTimeout, connectTimeout, commandInput);
if(result == null)
{
    throw new IOException("Method result is null");
}

System.out.println("Method result status is: " + result.getStatus());
```

Výstup zařízení ukazuje, jak zařízení reaguje na tento příkaz.
