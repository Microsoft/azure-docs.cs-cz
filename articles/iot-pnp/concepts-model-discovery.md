---
title: Použití modelů IoT technologie Plug and Play v řešení | Microsoft Docs
description: Jako tvůrce řešení se dozvíte, jak můžete ve vašem řešení IoT používat modely IoT technologie Plug and Play.
author: arunmannengal
ms.author: arunmann
ms.date: 07/23/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: dda0e3d6739e94df4ce5910938c164e3db336bce
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445570"
---
# <a name="use-iot-plug-and-play-models-in-an-iot-solution"></a>Použití modelů IoT technologie Plug and Play v řešení IoT

Tento článek popisuje, jak v řešení IoT můžete identifikovat ID modelu technologie Plug and Play zařízení IoT a pak načíst jeho definici modelu.

Existují dvě široké kategorie řešení IoT:

- *Účelové řešení* funguje se známou sadou modelů pro zařízení IoT technologie Plug and Play, která se budou k řešení připojovat. Tyto modely použijete při vývoji řešení.

- *Modelem řízené* řešení může pracovat s modelem libovolného zařízení technologie Plug and Play IoT. Sestavení modelem řízeného řešení je složitější, ale výhoda je, že vaše řešení funguje se všemi zařízeními, která se můžou v budoucnu přidat. Modelem řízené řešení IoT načte model a použije ho k určení telemetrie, vlastností a příkazů, které zařízení implementuje.

Použití modelu IoT technologie Plug and Play, řešení IoT:

1. Určuje ID modelu implementovaného modulem IoT technologie Plug and Play zařízení, modulem nebo IoT Edge připojeným k řešení.

1. Pomocí ID modelu načte definici modelu připojeného zařízení z úložiště modelu nebo vlastního úložiště.

## <a name="identify-model-id"></a>Identifikace ID modelu

Když se zařízení IoT technologie Plug and Play připojuje k IoT Hub, zaregistruje ID modelu modelu, který implementuje IoT Hub.

IoT Hub upozorní řešení s ID modelu zařízení jako součást toku připojení zařízení.

Řešení může získat ID modelu zařízení IoT technologie Plug and Play pomocí jedné z následujících tří metod:

### <a name="get-device-twin-api"></a>Získat rozhraní API pro vyzdvojené zařízení

Řešení může použít rozhraní API pro [získání](/java/api/com.microsoft.azure.sdk.iot.device.deviceclient.getdevicetwin?preserve-view=true&view=azure-java-stable) nepoužité k načtení ID modelu zařízení IoT technologie Plug and Play.

> [!TIP]
> Pro moduly a moduly IoT Edge použijte [ModuleClient. getvláken](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient.gettwin?preserve-view=true&view=azure-java-stable).

V následujícím fragmentu dvojitá reakce zařízení `modelId` obsahuje ID modelu technologie Plug and Play zařízení IoT:

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

### <a name="get-digital-twin-api"></a>Získat digitální dvojitou rozhraní API

Řešení může pomocí rozhraní [Get Digital vláken](/rest/api/iothub/service/digitaltwin/getdigitaltwin) API načíst ID modelu implementovaného zařízením IoT technologie Plug and Play.

V následujícím fragmentu digitálního obrazu s dvojitou odezvou `$metadata.$model` obsahuje ID modelu technologie Plug and Play zařízení IoT:

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

### <a name="digital-twin-change-event-notification"></a>Oznámení události změny digitálního vlákna

Připojení zařízení má za následek oznámení [události změny digitálního vlákna](concepts-digital-twin.md#digital-twin-change-events) . Řešení se musí přihlásit k odběru tohoto oznámení události. Informace o tom, jak povolit směrování pro digitální události s dvojitou událostmi, najdete v tématu [použití IoT Hub směrování zpráv pro posílání zpráv ze zařízení do cloudu do různých koncových bodů](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Řešení může použít událost uvedenou v následujícím fragmentu kódu k získání informací o zařízení IoT technologie Plug and Play, které se připojuje a získání jeho ID modelu:

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

## <a name="retrieve-a-model-definition"></a>Načtení definice modelu

Řešení používá ID modelu uvedené výše k načtení odpovídající definice modelu.

Řešení může získat definici modelu pomocí jedné z následujících možností:

### <a name="model-repository"></a>Úložiště modelů

Řešení mohou pomocí [úložiště modelu](concepts-model-repository.md) načíst modely. Buď musí tvůrci zařízení nebo tvůrci řešení nahrávat své modely do úložiště předem, aby je řešení mohli načíst.

Po identifikaci ID modelu pro nové připojení zařízení použijte následující postup:

1. Načtěte definici modelu pomocí ID modelu z úložiště modelu. Další informace najdete v tématu [úložiště modelů zařízení](concepts-model-repository.md).

1. Pomocí definice modelu připojeného zařízení můžete vytvořit výčet možností zařízení.

1. Pomocí možností ve výčtu zařízení můžete uživatelům umožnit [interakci se zařízením](quickstart-service-node.md).

### <a name="custom-store"></a>Vlastní úložiště

Řešení mohou ukládat tyto definice modelů v místním systému souborů, ve veřejném úložišti souborů nebo použít vlastní implementaci.

Po identifikaci ID modelu pro nové připojení zařízení použijte následující postup:

1. Načtěte definici modelu pomocí ID modelu z vlastního úložiště.

1. Pomocí definice modelu připojeného zařízení můžete vytvořit výčet možností zařízení. 

1. Pomocí možností ve výčtu zařízení můžete uživatelům umožnit [interakci se zařízením](quickstart-service-node.md).  

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak integrovat technologie Plug and Play modelech IoT do řešení IoT, některé z navrhovaných dalších kroků:

- [Interakce se zařízením z vašeho řešení](quickstart-service-node.md)
- [Digitální zdvojené REST API IoT](/rest/api/iothub/service/digitaltwin)
- [Průzkumník Azure IoT](howto-use-iot-explorer.md)
