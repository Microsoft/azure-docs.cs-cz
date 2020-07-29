---
title: Implementace zjišťování modelu IoT technologie Plug and Play Preview | Microsoft Docs
description: Jako tvůrce řešení se dozvíte, jak implementovat zjišťování modelu IoT technologie Plug and Play ve vašem řešení.
author: prashmo
ms.author: prashmo
ms.date: 07/23/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 364b85a8ead09858b97d5d7e6ca8c130b9960b2c
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337377"
---
# <a name="implement-iot-plug-and-play-preview-model-discovery-in-an-iot-solution"></a>Implementace zjišťování modelu IoT technologie Plug and Play Preview v řešení IoT

Tento článek popisuje, jak jako tvůrce řešení můžete implementovat zjišťování modelu IoT technologie Plug and Play Preview v řešení IoT. Zjišťování modelů popisuje, jak:

- Zařízení IoT technologie Plug and Play registrují ID modelu.
- Řešení IoT načte rozhraní implementovaná zařízením.

Existují dvě široké kategorie řešení IoT:

- *Účelově vytvořené řešení IoT* funguje se známou sadou technologie Plug and Playch modelů zařízení IoT.

- *Řešení IoT řízené modelem* může pracovat s jakýmkoli zařízením IoT technologie Plug and Play. Sestavování řešení založeného na modelu je složitější, ale výhoda spočívá v tom, že vaše řešení funguje se všemi zařízeními přidanými v budoucnu.

    Chcete-li vytvořit modelem řízené řešení IoT, je nutné vytvořit logiku proti primitivním rozhraním technologie Plug and Play IoT: telemetrie, vlastnosti a příkazy. Logika vašeho řešení představuje zařízení s kombinací více možností telemetrie, vlastností a příkazů.

Tento článek popisuje, jak implementovat zjišťování modelu v obou typech řešení.

## <a name="model-discovery"></a>Zjišťování modelů

Chcete-li zjistit model, který zařízení implementuje, řešení může získat ID modelu pomocí zjišťování založeného na událostech nebo vyhledávání na základě vlákna:

### <a name="event-based-discovery"></a>Zjišťování založené na událostech

Když se zařízení IoT technologie Plug and Play připojuje k IoT Hub, zaregistruje model, který implementuje. Výsledkem této registrace je oznámení [události změny digitálního vlákna](concepts-digital-twin.md#digital-twin-change-events) . Informace o tom, jak povolit směrování pro digitální události s dvojitou událostmi, najdete v tématu [použití IoT Hub směrování zpráv pro posílání zpráv ze zařízení do cloudu do různých koncových bodů](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Řešení může použít událost uvedenou v následujícím fragmentu kódu k získání informací o IoT technologie Plug and Play zařízení, které se připojuje a získání jeho ID modelu:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/22/2020 8:02:27 PM
iothub-message-source:digitalTwinChangeEvents
correlation-id:100f322dc2c5
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:com:example:TemperatureController;1"
  }
]
```

Tato událost se aktivuje při přidání nebo aktualizaci ID modelu zařízení.

### <a name="twin-based-discovery"></a>Zjišťování na základě vlákna

Pokud řešení potřebuje znát informace o možnostech daného zařízení, může k načtení informací použít rozhraní API [získat digitální](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin/getdigitaltwin) práci.

V následujícím digitálním fragmentu vlákna `$metadata.$model` obsahuje ID modelu zařízení technologie Plug and Play IoT:

```json
{
    "$dtId": "sample-device",
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

Řešení lze také použít k načtení ID modelu **z vlákna zařízení** , jak je znázorněno v následujícím fragmentu kódu:

```json
{
    "deviceId": "sample-device",
    "etag": "AAAAAAAAAAc=",
    "deviceEtag": "NTk0ODUyODgx",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "2020-07-17T06:12:26.8402249Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "modelId": "dtmi:com:example:TemperatureController;1",
    "version": 15,
    "properties": {...}
    }
}
```

## <a name="model-resolution"></a>Rozlišení modelu

Řešení používá rozlišení modelu pro získání přístupu k rozhraním, která tvoří model z ID modelu. 

- Řešení se můžou rozhodnout ukládat tato rozhraní jako soubory do místní složky. 
- Řešení můžou používat [úložiště modelu](concepts-model-repository.md).

## <a name="next-steps"></a>Další kroky

Teď, když jste se dozvěděli o zjišťování modelů v řešení IoT, se dozvíte víc o [platformě Azure IoT](overview-iot-plug-and-play.md) , která pro vaše řešení používá další funkce.

- [Interakce se zařízením z vašeho řešení](quickstart-service-node.md)
- [Digitální zdvojené REST API IoT](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin)
- [Průzkumník Azure IoT](howto-use-iot-explorer.md)
