---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 7ab17f0d34ba7682778120e11aab562e106b5df7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95510575"
---
IoT technologie Plug and Play zjednodušuje IoT tím, že vám umožní pracovat s funkcemi zařízení bez znalosti základní implementace zařízení. V tomto rychlém startu se dozvíte, jak pomocí Node.js připojit a řídit zařízení IoT technologie Plug and Play, které je připojené k vašemu řešení.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

K dokončení tohoto rychlého startu potřebujete Node.js na svém vývojovém počítači. Nejnovější doporučenou verzi si můžete stáhnout z [NodeJS.org](https://nodejs.org)na víc platforem.

Aktuální verzi Node.js na počítači používaném pro vývoj můžete ověřit pomocí následujícího příkazu:

```cmd/sh
node --version
```

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Naklonujte úložiště sady SDK pomocí ukázkového kódu.

Naklonujte ukázky z [úložiště sady SDK pro uzly](https://github.com/Azure/azure-iot-sdk-node). Otevřete okno terminálu ve složce podle vašeho výběru. Spusťte následující příkaz, který naklonuje [sadu Microsoft Azure IoT SDK pro Node.js](https://github.com/Azure/azure-iot-sdk-node) úložiště GitHub:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="run-the-sample-device"></a>Spuštění ukázkového zařízení

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Další informace o ukázkové konfiguraci najdete v [ukázkovém souboru Readme](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md).

V tomto rychlém startu použijete vzorový termostat zařízení, které je napsané v Node.js jako zařízení technologie Plug and Play IoT. Spuštění ukázkového zařízení:

1. Otevřete okno terminálu a přejděte do místní složky, která obsahuje sadu Microsoft Azure IoT SDK pro Node.js úložiště, které jste naklonoval z GitHubu.

1. Toto okno terminálu slouží jako terminál **zařízení** . Přejděte do složky naklonovaného úložiště a přejděte do složky */Azure-IoT-SDK-Node/Device/Samples/PNP* . Všechny závislosti Nainstalujte spuštěním následujícího příkazu:

    ```cmd/sh
    npm install
    ```

1. Spusťte vzorový termostat zařízení pomocí následujícího příkazu:

    ```cmd/sh
    node simple_thermostat.js
    ```

1. Zobrazí se zpráva oznamující, že zařízení odeslalo nějaké informace a nahlásilo je jako online. Tyto zprávy signalizují, že zařízení začalo posílat data telemetrie do centra a teď je připravené přijímat příkazy a aktualizace vlastností. Nezavírejte Tento terminál, budete ho potřebovat k potvrzení, že ukázka služby funguje.

## <a name="run-the-sample-solution"></a>Spuštění ukázkového řešení

V [části nastavení prostředí pro iot technologie Plug and Play rychlé starty a kurzy](../articles/iot-pnp/set-up-environment.md) , které jste vytvořili dvě proměnné prostředí pro konfiguraci ukázky pro připojení ke službě IoT Hub a zařízení:

* **IOTHUB_CONNECTION_STRING**: připojovací řetězec ke službě IoT Hub jste si poznamenali dříve.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"` .

V tomto rychlém startu použijete ukázkové řešení IoT v Node.js k interakci s ukázkovým zařízením, které jste právě nastavili.

1. Otevřete další okno terminálu pro použití jako terminálu **služby** .

1. V úložišti naklonovaného uzlu sady SDK přejděte do složky */Azure-IoT-SDK-Node/Service/Samples/JavaScript* . Všechny závislosti Nainstalujte spuštěním následujícího příkazu:

    ```cmd/sh
    npm install
    ```

### <a name="read-a-property"></a>Číst vlastnost

1. Po spuštění ukázkového zařízení v terminálu **zařízení** jste viděli následující zprávy, které indikují stav online:

    ```cmd/sh
    properties have been reported for component
    sending telemetry message 0...
    ```

1. Přejděte do terminálu **služby** a pomocí následujícího příkazu spusťte ukázku pro čtení informací o zařízení:

    ```cmd/sh
    node twin.js
    ```

1. Ve výstupu terminálu **služby** si všimněte odezvy zařízení. Zobrazí se ID modelu a přidružené vlastnosti zařízení, které se nahlásily:

    ```json
    Model Id: dtmi:com:example:Thermostat;1
    {
      "deviceId": "my-pnp-device",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "Njc3MDMxNDcy",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00Z",
      "connectionState": "Connected",
      "lastActivityTime": "0001-01-01T00:00:00Z",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "modelId": "dtmi:com:example:Thermostat;1",
      "version": 4,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2020-10-05T11:35:19.4574755Z"
          },
          "$version": 1
        },
        "reported": {
          "maxTempSinceLastReboot": 31.343640523762232,
          "serialNumber": "123abc",
          "$metadata": {
            "$lastUpdated": "2020-10-05T11:35:23.7339042Z",
            "maxTempSinceLastReboot": {
              "$lastUpdated": "2020-10-05T11:35:23.7339042Z"
            },
            "serialNumber": {
              "$lastUpdated": "2020-10-05T11:35:23.7339042Z"
            }
          },
          "$version": 3
        }
      },
      "capabilities": {
        "iotEdge": false
      },
      "tags": {}
    }
    ```

1. Následující fragment kódu ukazuje kód v *twin.js* , který načte ID modelu vlákna zařízení:

    ```javascript
    var registry = Registry.fromConnectionString(connectionString);
    registry.getTwin(deviceId, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log('Model Id: ' + twin.modelId);
        //...
      }
      //...
    }
    ```

V tomto scénáři výstup IT `Model Id: dtmi:com:example:Thermostat;1` .

> [!NOTE]
> Tyto ukázky služeb používají třídu **registru** z **klienta služby IoT Hub**. Další informace o rozhraních API, včetně rozhraní Digital props API, najdete v [příručce pro vývojáře služby](../articles/iot-pnp/concepts-developer-guide-service.md).

### <a name="update-a-writable-property"></a>Aktualizovat vlastnost s možností zápisu

1. Otevřete soubor *twin.js* v editoru kódu.

1. Přečtěte si vzorový kód, který ukazuje dva způsoby, jak se zařízení dodrží. Chcete-li použít první způsob, upravte `twinPatch` proměnnou následujícím způsobem:

    ```javascript
    var twinPatch = {
      tags: {
        city: "Redmond"
      },
      properties: {
        desired: {
          targetTemperature: 42
        }
      }
    };
    ```

    `targetTemperature`Vlastnost je definována jako vlastnost s možností zápisu v modelu zařízení termostatu.

1. V terminálu **služby** použijte následující příkaz ke spuštění ukázky pro aktualizaci vlastnosti:

    ```cmd/sh
    node twin.js
    ```

1. V terminálu **zařízení** vidíte, že zařízení obdrželo aktualizaci:

    ```cmd/sh
    The following properties will be updated for the default component:
    {
      targetTemperature: {
        value: 42,
        ac: 200,
        ad: 'Successfully executed patch for targetTemperature',
        av: 2
      }
    }
    updated the property
    ```

1. V terminálu **služby** spusťte následující příkaz, kterým potvrdíte aktualizaci této vlastnosti:

    ```cmd/sh
    node twin.js
    ```

1. Ve výstupu terminálu **služby** se v `reported` části Vlastnosti zobrazí aktualizovaná cílová teplota. Může chvíli trvat, než zařízení dokončí aktualizaci. Opakujte tento krok, dokud zařízení nezpracuje aktualizaci vlastnosti:

    ```json
    "reported": {
      //...
      "targetTemperature": {
        "value": 42,
        "ac": 200,
        "ad": "Successfully executed patch for targetTemperature",
        "av": 4
      },
      //...
    }
    ```

### <a name="invoke-a-command"></a>Vyvolání příkazu

1. Otevřete soubor *device_method.js* a zkontrolujte kód.

1. Přejděte do terminálu **služby** . Pomocí následujícího příkazu spusťte ukázku pro vyvolání příkazu:

    ```cmd/sh
    set IOTHUB_METHOD_NAME=getMaxMinReport
    set IOTHUB_METHOD_PAYLOAD=commandpayload
    node device_method.js
    ```

1. Výstup v terminálu **služby** zobrazuje následující potvrzení:

    ```cmd/sh
    getMaxMinReport on my-pnp-device:
    {
      "status": 200,
      "payload": {
        "maxTemp": 23.460596940801928,
        "minTemp": 23.460596940801928,
        "avgTemp": 23.460596940801928,
        "endTime": "2020-10-05T12:48:08.562Z",
        "startTime": "2020-10-05T12:47:54.450Z"
      }
    }
    ```

1. V terminálu **zařízení** se zobrazí potvrzení příkazu:

    ```cmd/sh
    MaxMinReport commandpayload
    Response to method 'getMaxMinReport' sent successfully.
    ```
