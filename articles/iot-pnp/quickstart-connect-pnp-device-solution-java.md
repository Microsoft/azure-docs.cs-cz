---
title: Interakce se zařízením IoT Plug and Play Preview připojeným k vašemu řešení Azure IoT | Dokumenty společnosti Microsoft
description: Pomocí Javy se můžete připojit k zařízení IoT Plug and Play Preview, které je připojené k vašemu řešení Azure IoT, a pracovat s ním.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 53812e68fe397b81f29869565e0e4a0640a9ef23
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76964630"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-java"></a>Úvodní příručka: Interakce se zařízením IoT Plug and Play Preview, které je připojené k vašemu řešení (Java)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT Plug and Play Preview zjednodušuje IoT tím, že umožňuje interakci s funkcemi zařízení bez znalosti základní implementace zařízení. Tento rychlý start ukazuje, jak používat Javu k připojení a ovládání zařízení IoT Plug and Play, které je připojené k vašemu řešení.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu potřebujete java SE 8 na vývojovém počítači. Musíte také nainstalovat Maven 3.

Podrobnosti o tom, jak nastavit s těmito, najdete [v tématu Příprava vývojového prostředí](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md) v Microsoft Azure IoT zařízení SDK pro Java.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Spusťte následující příkaz, abyste získali _připojovací řetězec centra IoT hub_ pro vaše centrum (poznámka pro pozdější použití):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Spuštění ukázkového zařízení

V tomto rychlém startu použijete ukázkový senzor prostředí, který je napsán v Javě jako zařízení IoT Plug and Play. Následující pokyny ukazují, jak nainstalovat a spustit zařízení:

1. Otevřete okno terminálu v adresáři, který si vyberete. Spusťte následující příkaz pro klonování [ukázek Azure IoT pro úložiště GitHub v Jazyce Java](https://github.com/Azure-Samples/azure-iot-samples-java) do tohoto umístění:

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-java
    ```

1. Toto okno terminálu bude nyní použito jako terminál _vašeho zařízení._ Přejděte do složky vašeho klonovaného úložiště a přejděte do složky **/azure-iot-samples-java/digital-twin/Samples/device/JdkSample.** Nainstalujte požadované knihovny a vytvořte aplikaci simulovaných zařízení spuštěním následujícího příkazu:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Konfigurace _připojovacího řetězce zařízení_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Spuštěním ukázky ze složky zařízení spusťte následující příkaz.

    ```cmd/sh
    java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
    ```

1. Zobrazí se zprávy, že zařízení je připojeno, provádění různých kroků instalace a čekání na aktualizace služby, následované protokoly telemetrie. To znamená, že zařízení je nyní připraven o příjem příkazů a aktualizace vlastností a začal oodesílání telemetrických dat do rozbočovače. Po nechat vzorek běží při dokončení dalších kroků. Nezavírejte tento terminál, budete ho později potřebovat k potvrzení, že vzorky služeb také fungovaly.

## <a name="run-the-sample-solution"></a>Spuštění ukázkového roztoku

V tomto rychlém startu použijete ukázkové řešení IoT v Javě k interakci s ukázkovým zařízením.

1. Otevřete další okno terminálu (toto bude váš _servisní_ terminál). Přejděte do složky klonovaného úložiště a přejděte do složky **/azure-iot-samples-java\digital-twin\Samples\service\JdkSample.**

1. Nainstalujte požadované knihovny a vytvořte ukázku služby spuštěním následujícího příkazu:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Nakonfigurujte _připojovací řetězec a_ _ID centra_ IoT, abyste službě umožnili připojení k oběma těmto:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Čtení vlastnosti

1. Když jste _zařízení_ připojili k jeho terminálu, jednou z výstupních zpráv byla následující zpráva označující jeho stav online. Vlastnost, `state` která slouží k označení, zda je zařízení online, je _true_:

    ```cmd/sh
    State of environmental sensor was set to true
    ```

1. Přejděte na _servisní_ terminál a pomocí následujícího příkazu spusťte ukázku služby pro čtení informací o zařízení:

    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```

1. Ve výstupu _terminálu služby_ přejděte na komponentu. `environmentalSensor` Vidíte, že `state` vlastnost byla hlášena jako _true_:
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

### <a name="update-a-writable-property"></a>Aktualizace zapisovatelné vlastnosti

1. Přejděte na _servisní_ terminál a nastavte následující proměnné, které definují, kterou vlastnost chcete aktualizovat:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set PROPERTY_NAME=brightness
    set PROPERTY_VALUE=42
    ```

1. Ke spuštění ukázky služby pro aktualizaci vlastnosti použijte následující příkaz:

    ```cmd/sh
    java -jar update-digital-twin/target/update-digital-twin-with-deps.jar
    ```

1. Výstup _terminálu služby_ zobrazuje aktualizované informace o zařízení. Přejděte `environmentalSensor` na komponentu a zobcete novou hodnotu jasu 42.

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

1. Přejděte na terminál _zařízení,_ uvidíte, že zařízení obdrželo aktualizaci:

    ```cmd/sh
    OnPropertyUpdate called: propertyName=brightness, reportedValue=null, desiredVersion=2, desiredValue={"value":"42"}
    Report property: propertyName=brightness, reportedValue={"value":"42"}, desiredVersion=2 was DIGITALTWIN_CLIENT_OK
    ```
2. Vraťte se do _svého servisního_ terminálu a spusťte níže uvedený příkaz, abyste znovu získali informace o zařízení, abyste potvrdili, že vlastnost byla aktualizována.
    
    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```
3. Ve výstupu _terminálu služby,_ pod komponentou, `environmentalSensor` uvidíte, že byla hlášena aktualizovaná hodnota jasu. Poznámka: Dokončení aktualizace může chvíli trvat, než zařízení dokončí. Tento krok můžete opakovat, dokud zařízení skutečně nezpracuje aktualizaci vlastnosti.
    
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

1. Přejděte na _servisní_ terminál a nastavte následující proměnné, které definují, který příkaz chcete vyvolat:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    set PAYLOAD=10
    ```

1. Ke spuštění ukázky služby pro vyvolání příkazu použijte následující příkaz:

    ```cmd/sh
    java -jar invoke-digital-twin-command/target/invoke-digital-twin-command-with-deps.jar
    ```

1. Výstup v _servisním_ terminálu by měl být potvrzen následujícím potvrzením:

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned PAYLOAD was
    ```

1. Přejděte na terminál _zařízení,_ uvidíte, že příkaz byl potvrzen:

    ```cmd/sh
    OnCommandReceived called: commandName=blink, requestId=<some ID value>, commandPayload="10"
    EnvironmentalSensor is blinking every 10 seconds.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak připojit zařízení IoT Plug and Play k řešení IoT. Další informace o tom, jak vytvořit řešení, které spolupracuje s vašimi zařízeními IoT Plug and Play, najdete v tématu:

> [!div class="nextstepaction"]
> [Postup: Připojení k zařízení a interakce se zařízením](howto-develop-solution.md)
