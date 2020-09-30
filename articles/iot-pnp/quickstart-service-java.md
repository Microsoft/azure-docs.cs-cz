---
title: Interakce se zařízením IoT technologie Plug and Play připojeným k řešení Azure IoT (Java) | Microsoft Docs
description: Pomocí Java se můžete připojit k zařízení IoT technologie Plug and Play, které je připojené k řešení Azure IoT, a pracovat s nimi.
author: ericmitt
ms.author: ericmitt
ms.date: 9/17/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: cd618cf5f2f82b9c87981e961ed401f3409ec9d4
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91583553"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-device-thats-connected-to-your-solution-java"></a>Rychlý Start: interakce se zařízením IoT technologie Plug and Play připojeným k vašemu řešení (Java)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

IoT technologie Plug and Play zjednodušuje IoT tím, že vám umožní pracovat s funkcemi zařízení bez znalosti základní implementace zařízení. V tomto rychlém startu se dozvíte, jak pomocí jazyka Java připojit a řídit zařízení IoT technologie Plug and Play, které je připojené k vašemu řešení.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

K dokončení tohoto rychlého startu ve Windows nainstalujte do svého místního prostředí Windows následující software:

* Java SE Development Kit 8. V [dlouhodobé podpoře jazyka Java pro Azure a Azure Stack](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true)v části **Dlouhodobá podpora**vyberte **Java 8**.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Naklonujte úložiště sady SDK pomocí ukázkového kódu.

Pokud jste dokončili [rychlý Start: Připojte ukázkovou aplikaci IoT technologie Plug and Play zařízení běžící v systému Windows k IoT Hub (Java)](quickstart-connect-device-java.md), již jste naklonoval úložiště.

Otevřete příkazový řádek v adresáři dle vašeho výběru. Spusťte následující příkaz, který naklonuje [Microsoft Azure sady SDK IoT pro](https://github.com/Azure/azure-iot-sdk-java) úložiště GitHub Java do tohoto umístění:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

## <a name="build-and-run-the-sample-device"></a>Sestavení a spuštění ukázkového zařízení

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

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

V [části nastavení prostředí pro iot technologie Plug and Play rychlé starty a kurzy](set-up-environment.md) , které jste vytvořili dvě proměnné prostředí pro konfiguraci ukázky pro připojení ke službě IoT Hub a zařízení:

* **IOTHUB_CONNECTION_STRING**: připojovací řetězec ke službě IoT Hub jste si poznamenali dříve.
* **DEVICE_ID**: `"my-pnp-device"` .

V tomto rychlém startu použijete ukázkové řešení IoT napsané v jazyce Java k interakci s ukázkovým zařízením, které jste právě nastavili.

> [!NOTE]
> Tato ukázka používá obor názvů **com. Microsoft. Azure. SDK. IoT. Service. *;** z **klienta služby IoT Hub**. Další informace o tom, jak načíst ID modelu, najdete v [příručce pro vývojáře](concepts-developer-guide-device-csharp.md).

1. Otevřete další okno terminálu pro použití jako terminálu **služby** .

1. V naklonovaném úložišti Java SDK přejděte do složky *service\iot-Service-samples\pnp-Service-sample\thermostat-Service-Sample* .

1. Chcete-li spustit ukázkovou aplikaci služby, spusťte následující příkaz:

    ```cmd
    mvm exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.service.Thermostat"
    ```

### <a name="get-digital-twin"></a>Získání digitálního vlákna

Následující fragment kódu ukazuje, jak načíst ve službě vlákna ve službě:

```java
 // Get the twin and retrieve model Id set by Device client.
DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);
System.out.println("Model Id of this Twin is: " + twin.getModelId());
```

### <a name="update-a-digital-twin"></a>Aktualizace digitálního vlákna

Následující fragment kódu ukazuje, jak použít *opravu* k aktualizaci vlastností prostřednictvím digitálního vlákna:

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

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak připojit zařízení IoT technologie Plug and Play k řešení IoT. Další informace o modelech zařízení IoT technologie Plug and Play najdete v tématech:

> [!div class="nextstepaction"]
> [Příručka pro vývojáře IoT technologie Plug and Play Modeling](concepts-developer-guide-device-csharp.md)
