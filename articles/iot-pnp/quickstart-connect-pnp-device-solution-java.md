---
title: Interakce se zařízením IoT technologie Plug and Play Preview připojeným k vašemu řešení – Java | Microsoft Docs
description: Pomocí Java se můžete připojit k zařízení IoT technologie Plug and Play Preview, které je připojené k řešení Azure IoT, a pracovat s nimi.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 43136e1a09cc771f39b92a83ca4c452fc1276091
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044031"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-java"></a>Rychlý Start: interakce se zařízením IoT technologie Plug and Play ve verzi Preview, které je připojené k vašemu řešení (Java)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT technologie Plug and Play Preview zjednodušuje IoT tím, že vám umožní pracovat s funkcemi zařízení bez znalosti základní implementace zařízení. V tomto rychlém startu se dozvíte, jak pomocí jazyka Java připojit a řídit zařízení IoT technologie Plug and Play, které je připojené k vašemu řešení.

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto rychlého startu potřebujete Java SE 8 na svém vývojovém počítači. Také je potřeba nainstalovat Maven 3.

Podrobnosti o tom, jak s nimi nastavovat, najdete v tématu [Příprava vývojového prostředí](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md) v sadě SDK pro zařízení Microsoft Azure IoT pro jazyk Java.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Spuštěním následujícího příkazu Získejte _připojovací řetězec služby IoT Hub_ pro vaše centrum (Poznámka pro pozdější použití):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Spuštění ukázkového zařízení

V tomto rychlém startu použijete vzorový senzor prostředí, který je napsán v jazyce Java jako zařízení IoT technologie Plug and Play. Následující pokyny ukazují, jak nainstalovat a spustit zařízení:

1. Otevřete okno terminálu v adresáři dle vašeho výběru. Spuštěním následujícího příkazu naklonujte [ukázky Azure IoT pro](https://github.com/Azure-Samples/azure-iot-samples-java) úložiště GitHub Java do tohoto umístění:

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-java
    ```

1. Toto okno terminálu se teď použije jako terminál _zařízení_ . Přejděte do složky naklonovaného úložiště a přejděte do složky **/Azure-IoT-Samples-Java/Digital-Twin/Samples/Device/JdkSample** . Nainstalujte požadované knihovny a sestavte aplikaci simulovaného zařízení spuštěním následujícího příkazu:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Konfigurace _připojovacího řetězce zařízení_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Spuštěním následujícího příkazu spusťte ukázku ze složky zařízení.

    ```cmd/sh
    java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
    ```

1. Zobrazí se zpráva oznamující, že je zařízení připojené, provádění různých kroků instalace a čekání na aktualizace služby, a potom i protokoly telemetrie. To znamená, že zařízení je nyní připraveno přijímat příkazy a aktualizace vlastností a zahájilo posílání dat telemetrie do centra. Ponechte ukázku spuštěnou při dokončení dalších kroků. Nezavírejte tohoto terminálu, budete ho potřebovat později, abyste ověřili, že se ukázky služby také osvědčily.

## <a name="run-the-sample-solution"></a>Spuštění ukázkového řešení

V tomto rychlém startu použijete k interakci s ukázkovým zařízením ukázkové řešení IoT v jazyce Java.

1. Otevřete další okno terminálu (bude to váš terminál _služby_ ). Přejděte do složky naklonovaného úložiště a přejděte do složky **/Azure-IoT-Samples-java\digital-twin\Samples\service\JdkSample** .

1. Nainstalujte požadované knihovny a sestavte ukázku služby spuštěním následujícího příkazu:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Nakonfigurujte _připojovací řetězec služby IoT Hub_ a _ID zařízení_ , aby se služba mohla připojit k oběma těmto akcím:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Číst vlastnost

1. Když jste _zařízení_ připojili v terminálu, jedna z výstupních zpráv byla následující zprávou, která indikuje stav online. `state`Vlastnost, která určuje, jestli je zařízení online, má _hodnotu true_:

    ```cmd/sh
    State of environmental sensor was set to true
    ```

1. Přejděte do terminálu _služby_ a pomocí následujícího příkazu spusťte ukázku služby pro čtení informací o zařízení:

    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```

1. Ve výstupu terminálu _služby_ se posuňte k `environmentalSensor` součásti. Vidíte, že `state` vlastnost byla hlášena jako _true_:
    ```JSON
    "environmentalSensor" : {
      "name" : "environmentalSensor",
      "properties" : {
        "state" : {
          "reported" : {
            "value" : true,
            "desiredState" : null
          },
          "desired" : null
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>Aktualizovat vlastnost s možností zápisu

1. Přejděte do terminálu _služby_ a nastavte následující proměnné pro definování, kterou vlastnost chcete aktualizovat:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set PROPERTY_NAME=brightness
    set PROPERTY_VALUE=42
    ```

1. Pomocí následujícího příkazu spusťte ukázku služby pro aktualizaci vlastnosti:

    ```cmd/sh
    java -jar update-digital-twin/target/update-digital-twin-with-deps.jar
    ```

1. Výstup terminálu _služby_ zobrazuje aktualizované informace o zařízení. Posuňte se na `environmentalSensor` součást, abyste viděli novou hodnotu jasu 42.

    ```json
    "environmentalSensor": {
        "name": "environmentalSensor",
        "properties": {
            "brightness": {
                "reported": null,
                "desired": {
                    "value": "42"
                }
            },
    ```

1. Přejděte do terminálu _zařízení_ , vidíte, že zařízení obdrželo aktualizaci:

    ```cmd/sh
    OnPropertyUpdate called: propertyName=brightness, reportedValue=null, desiredVersion=2, desiredValue={"value":"42"}
    Report property: propertyName=brightness, reportedValue={"value":"42"}, desiredVersion=2 was DIGITALTWIN_CLIENT_OK
    ```
2. Vraťte se do terminálu _služby_ a spuštěním následujícího příkazu znovu Získejte informace o zařízení, aby se ověřilo, že se vlastnost aktualizovala.
    
    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```
3. V výstupu terminálu _služby_ se pod `environmentalSensor` komponentou zobrazí aktualizovaná hodnota jasu. Poznámka: může chvíli trvat, než zařízení dokončí aktualizaci. Tento krok můžete opakovat, dokud zařízení nezpracovává aktualizaci vlastností.
    
    ```json
    "environmentalSensor" : {
      "name" : "environmentalSensor",
      "properties" : {
        "brightness" : {
          "reported" : {
            "value" : {
              "value" : "42"
            },
            "desiredState" : {
              "code" : 200,
              "version" : 2,
              "description" : "OK"
            }
          },
          "desired" : {
            "value" : "42"
          }
        },
        "state" : {
          "reported" : {
            "value" : true,
            "desiredState" : null
          },
          "desired" : null
        }
      }
    },       
    ```

### <a name="invoke-a-command"></a>Vyvolání příkazu

1. Přejděte do terminálu _služby_ a nastavte následující proměnné pro definování příkazu, který se má vyvolat:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    set PAYLOAD=10
    ```

1. Pomocí následujícího příkazu spusťte ukázku služby pro vyvolání příkazu:

    ```cmd/sh
    java -jar invoke-digital-twin-command/target/invoke-digital-twin-command-with-deps.jar
    ```

1. Výstup v terminálu _služby_ by měl zobrazovat následující potvrzení:

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned PAYLOAD was
    ```

1. Přejděte do terminálu _zařízení_ , uvidíte, že byl příkaz potvrzen:

    ```cmd/sh
    OnCommandReceived called: commandName=blink, requestId=<some ID value>, commandPayload="10"
    EnvironmentalSensor is blinking every 10 seconds.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak připojit zařízení IoT technologie Plug and Play k řešení IoT. Další informace o tom, jak vytvořit řešení, které komunikuje s technologie Plug and Play vašich zařízení IoT, najdete tady:

> [!div class="nextstepaction"]
> [Postupy: připojení k zařízení a práce s nimi](howto-develop-solution.md)
