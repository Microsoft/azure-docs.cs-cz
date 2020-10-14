---
title: Principy digitálních dvojčat IoT Plug and Play
description: Vysvětlení způsobu, jakým technologie Plug and Play IoT používá digitální vlákna
author: prashmo
ms.author: prashmo
ms.date: 07/17/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ea523045875e0abc9e14924c7bb388ea2cfcc2db
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92046464"
---
# <a name="understand-iot-plug-and-play-digital-twins"></a>Principy digitálních dvojčat IoT Plug and Play

Zařízení IoT technologie Plug and Play implementuje model, který je popsaný v rámci schématu [DTDL (Digital vlákna Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) . Model popisuje sadu komponent, vlastností, příkazů a zpráv telemetrie, které může mít konkrétní zařízení. Když se zařízení IoT technologie Plug and Play připojí ke službě IoT Hub, inicializuje se nevlákenná a digitální vlákna.

IoT technologie Plug and Play používá DTDL verze 2. Další informace o této verzi najdete v článku specifikace [Digital DTDLs Definition Language () – verze 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) na GitHubu.

DTDL není výhradně pro IoT technologie Plug and Play. Další služby IoT, jako jsou třeba [digitální vlákna Azure](../digital-twins/overview.md), ji používají k vyjádření celého prostředí, jako jsou budovy a energetické sítě. Další informace najdete v tématu [pochopení zdvojených modelů v digitálních Prozdvojeních Azure](../digital-twins/concepts-models.md).

Tento článek popisuje, jak jsou komponenty a vlastnosti reprezentovány v *požadovaných* a *nahlášených* částech v zařízení. Popisuje také, jak se tyto koncepty mapují na odpovídající digitální dvojče.

Zařízení Plug and Play IoT v tomto článku, které implementuje [model řadiče teploty](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) se součástí [termostatu](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) .

## <a name="device-twins-and-digital-twins"></a>Vlákna a digitální vlákna zařízení

Vlákna zařízení jsou dokumenty JSON, které ukládají informace o stavu zařízení včetně metadat, konfigurací a podmínek. Další informace najdete v tématu [pochopení a používání nevláken zařízení v IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md). Zařízení i tvůrci řešení mohou i nadále používat stejnou sadu dvojitých rozhraní API a sad SDK pro zařízení k implementaci zařízení a řešení pomocí konvencí technologie Plug and Play IoT.

Digitální vlákna rozhraní API pracují s konstrukcemi na vysoké úrovni v rámci digitálního DTDL (Digital vlákna Definition Language), jako jsou komponenty, vlastnosti a příkazy. Digitální vlákna rozhraní API usnadňují tvůrcům řešení vytváření řešení IoT technologie Plug and Play.

V zařízení je stav vlastnosti s možností zápisu rozdělen mezi požadované a nahlášené oddíly. Všechny vlastnosti jen pro čtení jsou k dispozici v části nahlášené.

V digitálním vlákna je k dispozici jednotný pohled na aktuální a požadovaný stav vlastnosti. Stav synchronizace pro danou vlastnost je uložen v příslušné části výchozí součásti `$metadata` .

### <a name="digital-twin-json-format"></a>Formát digitálního vlákna JSON

Při reprezentaci jako objektu JSON obsahuje digitální vlákna následující pole:

| Název pole | Popis |
| --- | --- |
| `$dtId` | Uživatelem zadaný řetězec představující ID digitálního vlákna zařízení |
| `{propertyName}` | Hodnota vlastnosti ve formátu JSON |
| `$metadata.$model` | Volitelné ID rozhraní modelu, které charakterizuje tuto digitální vlákna |
| `$metadata.{propertyName}.desiredValue` | [Pouze pro zapisovatelné vlastnosti] Požadovaná hodnota zadané vlastnosti |
| `$metadata.{propertyName}.desiredVersion` | [Pouze pro zapisovatelné vlastnosti] Verze požadované hodnoty udržovaná IoT Hub|
| `$metadata.{propertyName}.ackVersion` | [Povinné, pouze pro zapisovatelné vlastnosti] Verze potvrzená zařízením, které implementuje digitální vlákna, musí být větší nebo rovna požadované verzi. |
| `$metadata.{propertyName}.ackCode` | [Povinné, pouze pro zapisovatelné vlastnosti] `ack` Kód vrácený aplikací pro zařízení implementující digitální vlákna |
| `$metadata.{propertyName}.ackDescription` | [Volitelné, pouze pro zapisovatelné vlastnosti] `ack` Popis vrácený aplikací pro zařízení implementující digitální dvojitou vlákenou |
| `$metadata.{propertyName}.lastUpdateTime` | IoT Hub udržuje časové razítko poslední aktualizace vlastnosti zařízením. Časová razítka jsou v UTC a kódovaná ve formátu ISO8601 YYYY-MM-DDTHH: MM: SS. mmmZ |
| `{componentName}` | Objekt JSON, který obsahuje hodnoty vlastností a metadat komponenty. |
| `{componentName}.{propertyName}` | Hodnota vlastnosti komponenty ve formátu JSON |
| `{componentName}.$metadata` | Informace metadat pro komponentu. |

#### <a name="device-twin-sample"></a>Ukázka vlákna zařízení

Následující fragment kódu ukazuje technologie Plug and Play jako objekt JSON, který je ve formátu.

```json
{
    "deviceId": "sample-device",
    "modelId": "dtmi:com:example:TemperatureController;1",
    "version": 15,
    "properties": {
        "desired": {
            "thermostat1": {
                "__t": "c",
                "targetTemperature": 21.8
            },
            "$metadata": {...},
            "$version": 4
        },
        "reported": {
            "serialNumber": "alwinexlepaho8329",
            "thermostat1": {
                "maxTempSinceLastReboot": 25.3,
                "__t": "c",
                "targetTemperature": {
                    "value": 21.8,
                    "ac": 200,
                    "ad": "Successfully executed patch",
                }
            },
            "$metadata": {...},
            "$version": 11
        }
    }
}
```

#### <a name="digital-twin-sample"></a>Ukázka digitálního vlákna

Následující fragment kódu ukazuje, že digitální dvojitě formátovaná jako objekt JSON:

```json
{
    "$dtId": "sample-device",
    "serialNumber": "alwinexlepaho8329",
    "thermostat1": {
        "maxTempSinceLastReboot": 25.3,
        "targetTemperature": 21.8,
        "$metadata": {
            "targetTemperature": {
                "desiredValue": 21.8,
                "desiredVersion": 4,
                "ackVersion": 4,
                "ackCode": 200,
                "ackDescription": "Successfully executed patch",
                "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
            },
            "maxTempSinceLastReboot": {
                "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
            }
        }
    },
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

### <a name="properties"></a>Vlastnosti

Vlastnosti jsou datová pole, která představují stav entity (jako jsou vlastnosti v mnoha objektově orientovaných programovacích jazycích).

#### <a name="read-only-property"></a>Vlastnost jen pro čtení

XSD

```json
{
    "@type": "Property",
    "name": "serialNumber",
    "displayName": "Serial Number",
    "description": "Serial number of the device.",
    "schema": "string"
}
```

V tomto příkladu `alwinexlepaho8329` je aktuální hodnotou `serialNumber` vlastnosti jen pro čtení hlášené zařízením.
Následující fragmenty kódu ukazují souběžnou reprezentaci JSON `serialNumber` vlastnosti:

:::row:::
   :::column span="":::
      **Dvojče zařízení**

```json
"properties": {
    "reported": {
        "serialNumber": "alwinexlepaho8329"
    }
}
```

   :::column-end:::
   :::column span="":::
      **Digitální vlákna**

```json
"serialNumber": "alwinexlepaho8329"
```

   :::column-end:::
:::row-end:::

#### <a name="writable-property"></a>Vlastnost s možností zápisu

Řekněme, že zařízení má také následující vlastnost s možností zápisu ve výchozí komponentě:

```json
{
    "@type": "Property",
    "name": "fanSpeed",
    "displayName": "Fan Speed",
    "writable": true,
    "schema": "double"
}
```

:::row:::
   :::column span="":::
      **Dvojče zařízení**

```json
{
    "properties": {
        "desired": {
            "fanSpeed": 2.0,
        },
        "reported": {
            "fanSpeed": {
                "value": 3.0,
                "ac": 200,
                "av": 1,
                "ad": "Successfully executed patch version 1"
            }
        }
    },
}
```

   :::column-end:::
   :::column span="":::
      **Digitální vlákna**

```json
{
    "fanSpeed": 3.0,
    "$metadata": {
        "fanSpeed": {
            "desiredValue": 2.0,
            "desiredVersion": 2,
            "ackVersion": 1,
            "ackCode": 200,
            "ackDescription": "Successfully executed patch version 1",
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

   :::column-end:::
:::row-end:::

V tomto příkladu `3.0` je aktuální hodnota `fanSpeed` vlastnosti hlášené zařízením. `2.0` je požadovaná hodnota nastavená řešením. Požadovaná hodnota a stav synchronizace vlastnosti root-Level je nastavena v rámci kořenové úrovně `$metadata` pro digitální vlákna. Když zařízení přejde do režimu online, může tuto aktualizaci použít a nahlásit zpět aktualizovanou hodnotu.

### <a name="components"></a>Komponenty

Komponenty umožňují sestavovat rozhraní modelu jako sestavení jiných rozhraní.
Zvažte použití [termostatu](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) rozhraní, které je definováno jako model.
Toto rozhraní se teď dá začlenit jako součást thermostat1 (a další součást thermostat2) při definování [modelu teplotního adaptéru](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json).

V zařízení je komponenta označena `{ "__t": "c"}` značkou. V digitálním vlákna `$metadata` označuje přítomnost součást.

V tomto příkladu `thermostat1` je komponentou se dvěma vlastnostmi:

- `maxTempSinceLastReboot` je vlastnost jen pro čtení.
- `targetTemperature` je vlastnost s možností zápisu, která byla úspěšně synchronizována zařízením. Požadovaná hodnota a stav synchronizace těchto vlastností je součástí `$metadata` .

Následující fragmenty kódu znázorňují souběžnou reprezentaci `thermostat1` komponenty:

:::row:::
   :::column span="":::
      **Dvojče zařízení**

```json
"properties": {
    "desired": {
        "thermostat1": {
            "__t": "c",
            "targetTemperature": 21.8
        },
        "$metadata": {
        },
        "$version": 4
    },
    "reported": {
        "thermostat1": {
            "maxTempSinceLastReboot": 25.3,
            "__t": "c",
            "targetTemperature": {
                "value": 21.8,
                "ac": 200,
                "ad": "Successfully executed patch",
                "av": 4
            }
        },
        "$metadata": {
        },
        "$version": 11
    }
}
```

   :::column-end:::
   :::column span="":::
      **Digitální vlákna**

```json
"thermostat1": {
    "maxTempSinceLastReboot": 25.3,
    "targetTemperature": 21.8,
    "$metadata": {
        "targetTemperature": {
            "desiredValue": 21.8,
            "desiredVersion": 4,
            "ackVersion": 4,
            "ackCode": 200,
            "ackDescription": "Successfully executed patch",
            "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
        },
        "maxTempSinceLastReboot": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

   :::column-end:::
:::row-end:::

## <a name="digital-twin-apis"></a>Digitální dvojitá rozhraní API

Digitální vlákna Azure jsou vybavená pomocí příkazu **získat digitální**vlákna, **aktualizovat digitální vlákna**, **vyvolat příkaz komponenty** a **vyvolat příkaz** pro správu digitálního vlákna zařízení. [Rozhraní REST API](/rest/api/iothub/service/digitaltwin) můžete buď použít přímo, nebo prostřednictvím [sady SDK služby](../iot-pnp/libraries-sdks.md).

## <a name="digital-twin-change-events"></a>Události změn digitálního dvojčete

Pokud jsou povolené události změn digitálního dvojčete, při každé změně aktuální nebo požadované hodnoty nějaké komponenty nebo vlastnosti se aktivuje událost. Události změny digitálního vlákna jsou generovány ve formátu [opravy JSON](http://jsonpatch.com/) . V případě, že jsou povolené události změny, jsou ve formátu vlákna zařízení vygenerovány odpovídající události.

Informace o tom, jak povolit směrování pro zařízení a digitální události, najdete v tématu [použití IoT Hub směrování zpráv pro posílání zpráv ze zařízení do cloudu do různých koncových bodů](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events). Chcete-li porozumět formátu zprávy, přečtěte si téma [Vytvoření a čtení zpráv IoT Hub](../iot-hub/iot-hub-devguide-messages-construct.md).

Například následující událost digitálního události změny se aktivuje při `targetTemperature` nastavení řešení:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/17/2020 6:11:04 AM
iothub-message-source:digitalTwinChangeEvents
correlation-id:275d463fa034
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature",
    "value": {
      "desiredValue": 21.8,
      "desiredVersion": 4
    }
  }
]
```

Následující událost změny digitálního vlákna se aktivuje, když zařízení hlásí, že se použila výše požadovaná změna:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/17/2020 6:11:05 AM
iothub-message-source:digitalTwinChangeEvents
correlation-id:275d464a2c80
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackCode",
    "value": 200
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackDescription",
    "value": "Successfully executed patch"
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackVersion",
    "value": 4
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/lastUpdateTime",
    "value": "2020-07-17T06:11:04.9309159Z"
  },
  {
    "op": "add",
    "path": "/thermostat1/targetTemperature",
    "value": 21.8
  }
]
```

> [!NOTE]
> Pokud jsou v zařízení i v oznámení o změně digitálního vlákna, jsou zdvojené zprávy s oznámením o změně zapnuté.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili o digitálních prostředcích, tady je několik dalších prostředků:

- [Jak používat technologie Plug and Play digitálních vláken rozhraní IoT](howto-manage-digital-twin.md)
- [Interakce se zařízením z vašeho řešení](quickstart-service-node.md)
- [Digitální zdvojené REST API IoT](/rest/api/iothub/service/digitaltwin)
- [Průzkumník Azure IoT](howto-use-iot-explorer.md)