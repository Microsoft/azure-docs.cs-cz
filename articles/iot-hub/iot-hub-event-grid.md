---
title: Azure IoT Hub a Event Grid | Dokumentace Microsoftu
description: Pomocí Azure Event Grid ke spouštění procesů na základě akcí, ke kterým dochází ve službě IoT Hub.
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/14/2018
ms.author: kgremban
ms.openlocfilehash: 14bdbb5d629cb5a3fccd6f874e30ded0648e0124
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249464"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Reakce na události služby IoT Hub s využitím služby Event Grid pro aktivaci akcí

Azure IoT Hub se integruje s Azure Event Grid tak, aby mohla odesílat oznámení událostí k jiným službám a aktivovat podřízené procesy. Konfigurovat podnikové aplikace tak, aby naslouchala událostem IoT Hubu tak, aby mohly reagovat na kritické události spolehlivé, škálovatelné a zabezpečeným způsobem. Například sestavte aplikaci umožňuje provádět více akcí, jako je aktualizace databáze, vytvářet lístek a doručování e-mailové oznámení pokaždé, když se nový IoT zařízení je zaregistrované do služby IoT hub. 

[Azure Event Grid](../event-grid/overview.md) je plně spravovaná služba Směrování událostí, který používá publikování-odběru modelu. Event Grid obsahuje integrovanou podporu pro služby Azure, jako je [Azure Functions](../azure-functions/functions-overview.md) a [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md)a může poskytovat výstrahy na události na jiné než Azure services pomocí webhooků. Úplný seznam obslužných rutin událostí, které podporuje Služba Event Grid najdete v tématu [Úvod do služby Azure Event Grid](../event-grid/overview.md). 

![Architektura služby Azure Event Grid](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Regionální dostupnost

Integrace služby Event Grid je k dispozici pro IoT hub umístěných v oblastech, kde je podporován služby Event Grid. Nejnovější seznam oblastí najdete v tématu [Úvod do služby Azure Event Grid](../event-grid/overview.md). 

## <a name="event-types"></a>Typy událostí

IoT Hub zveřejňuje následující typy událostí: 

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Publikuje, když je zařízení zaregistrované do služby IoT hub. |
| Microsoft.Devices.DeviceDeleted | Publikuje, když zařízení se odstraní ze služby IoT hub. |
| Microsoft.Devices.DeviceConnected | Publikuje, když je zařízení připojené do služby IoT hub. |
| Microsoft.Devices.DeviceDisconnected | Publikuje, když zařízení je odpojen od služby IoT hub. |

Nastavit, které události chcete-li publikovat z každé služby IoT hub pomocí webu Azure portal nebo rozhraní příkazového řádku Azure. Příklad, projděte si kurz [odeslat e-mailová oznámení o událostech služby Azure IoT Hub pomocí Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md).

## <a name="event-schema"></a>Schéma událostí

Události služby IoT Hub obsahovat všechny informace, které je potřeba reagovat na změny v životní cyklus vašeho zařízení. Identifikujete událost služby IoT Hub tak, že zkontrolujete vlastnost typ eventType začínající hodnotou **Microsoft.Devices**. Další informace o tom, jak použít vlastnosti událostí služby Event Grid najdete v tématu [schéma událostí služby Event Grid](../event-grid/event-schema.md).

### <a name="device-connected-schema"></a>Schéma připojené zařízení

Následující příklad ukazuje schématu je zařízení připojené události: 

```json
[{  
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8", 
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceConnected", 
  "eventTime": "2018-06-02T19:17:44.4383997Z", 
  "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID",
  }, 
  "dataVersion": "1", 
  "metadataVersion": "1" 
}]
```

### <a name="device-created-schema"></a>Zařízení se vytvořilo schématu

Následující příklad ukazuje schématu zařízení se vytvořilo události: 

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag":"null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

Podrobný popis každé vlastnosti najdete v tématu [schématu událostí služby Azure Event Grid pro službu IoT Hub](../event-grid/event-schema-iot-hub.md)

## <a name="filter-events"></a>Umožňuje filtrovat události

Odběry událostí služby IoT Hub můžete filtrovat události podle typu a zařízení název události. Na základě předmětu filtry ve službě Event Grid pracovní **začíná** (předpona) a **skončí s** odpovídá (přípona). Tento filtr používá `AND` operátoru, takže doručí události s předmětem, které odpovídají předpony a přípony do odběratele. 

Předmět události IoT používá formát:

```json
devices/{deviceId}
```
## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Omezení pro zařízení připojené a zařízení odpojí události

Zobrazí zařízení, připojení a odpojení zařízení události, je nutné otevřít odkaz D2C nebo C2D odkaz pro vaše zařízení. Pokud vaše zařízení používá protokol MQTT, Centrum IoT udržovat C2D otevřete odkaz. AMQP můžete otevřít odkaz C2D voláním [přijímat asynchronní rozhraní API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet). 

Odkaz D2C je otevřený, když odesíláte telemetrii. Pokud je blikání připojení zařízení, například zařízení, připojení a odpojení často, nebudeme vám posílat každý jedno připojení stavu, ale budete publikovat stav připojení, která je zachycená každou minutu. I v případě výpadku služby IoT Hub publikujeme stav připojení zařízení ihned poté, co je výpadek. Pokud zařízení odpojí během tohoto výpadku, události odpojených zařízení bude publikován do 10 minut.

## <a name="tips-for-consuming-events"></a>Tipy pro používání událostí

Aplikace, které zpracovávají události služby IoT Hub postupujte podle těchto doporučené postupy:

* Několik předplatných lze nakonfigurovat pro směrování událostí na stejný ovladač událostí, takže je důležité, abyste se předpokládá, že jsou události z určitého zdroje. Vždy zkontrolujte zpráv tématu k zajištění, že pocházejí ze služby IoT hub, které jste očekávali. 

* Nepředpokládejte, že jsou všechny události, které se zobrazí typy, které jste očekávali. Vždy zkontrolujte typ události před zpracováním zprávy.

* Můžete doručování zpráv mimo pořadí nebo po prodlevě. Značka etag pole použijte k pochopení, pokud je aktuální informace o objektech.

## <a name="next-steps"></a>Další postup

* [Projděte si kurz událostí služby IoT Hub](../event-grid/publish-iot-hub-events-to-logic-apps.md)
* [Informace o uspořádání událostí připojení a odpojení zařízení](iot-hub-how-to-order-connection-state-events.md)
* [Další informace o službě Event Grid](../event-grid/overview.md)
* [Srovnání rozdílů mezi směrování událostí služby IoT Hub a zpráv](iot-hub-event-grid-routing-comparison.md)
