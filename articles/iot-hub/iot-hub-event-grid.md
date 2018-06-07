---
title: Azure IoT Hub a mřížky událostí | Microsoft Docs
description: K aktivaci procesy založené na akce prováděné ve IoT Hub pomocí Azure událostí mřížky.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/14/2018
ms.author: kgremban
ms.openlocfilehash: f187aa81ca519f2597657f01c2d7a630740b5348
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634307"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions---preview"></a>Reagování na události služby IoT Hub pomocí mřížky události pro aktivační událost akce - Preview

Azure IoT Hub se integruje s mřížky událostí Azure tak, aby mohla odesílat oznámení událostí k jiným službám a aktivovat podřízené procesy. Konfigurovat podnikové aplikace tak, aby naslouchala událostem IoT Hub, aby mohly reagovat na důležité události spolehlivé, škálovatelné a zabezpečené způsobem. Například vytvořit aplikaci provádět několik akcí, jako je aktualizace databáze, vytváření lístek a doručování e-mailové oznámení pokaždé, když je nové zařízení IoT registrováno do služby IoT hub. 

[Azure mřížky událostí] [ lnk-eg-overview] je plně spravovaná událostí směrování služba, která používá publikování-přihlášení k odběru modelu. Událost mřížky má integrovanou podporu pro Azure služby, jako je [Azure Functions](../azure-functions/functions-overview.md) a [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md)a lze výstrah událostí doručit mimo platformu Azure services pomocí webhooků. Úplný seznam obslužných rutin událostí, které podporuje mřížky událostí najdete v tématu [Úvod do Azure událostí mřížky][lnk-eg-overview]. 

![Architektura Azure událostí mřížky](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Regionální dostupnost

Integrace mřížky událostí je k dispozici pro centra IoT, které jsou umístěné v oblastech, kde je podporováno Grid událostí. Nejnovější seznam oblastí naleznete v tématu [Úvod do Azure událostí mřížky][lnk-eg-overview]. 

## <a name="event-types"></a>Typy událostí

IoT Hub publikuje následující typy událostí: 

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Publikuje, když je zařízení registrováno do služby IoT hub. |
| Microsoft.Devices.DeviceDeleted | Publikuje, když zařízení se odstraní ze služby IoT hub. | 

Nakonfigurovat události, které pro publikování z každé služby IoT hub pomocí portálu Azure nebo rozhraní příkazového řádku Azure. Příklad, vyzkoušejte kurzu [odesílání e-mailové oznámení o událostech Azure IoT Hub pomocí Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md). 

## <a name="event-schema"></a>Schéma událostí

IoT Hub události obsahují všechny informace, které je potřeba reagovat na změny v průběhu životního cyklu zařízení. Identifikujete událost IoT Hub kontrolou, který začíná vlastnost Typ události **Microsoft.Devices**. Další informace o tom, jak pomocí vlastností události událostí mřížky, najdete v článku [schématu události událostí mřížky](../event-grid/event-schema.md).

### <a name="device-created-schema"></a>Schéma zařízení vytvořit

Následující příklad ukazuje schéma zařízení vytvořit událost: 

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
    "deviceId": "LogicAppTestDevice",
    "operationTimestamp": "2018-01-02T19:17:44.4383997Z",
    "opType": "DeviceCreated"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Podrobný popis každé vlastnosti, najdete v části [schématu události mřížky událostí Azure pro IoT Hub](../event-grid/event-schema-iot-hub.md)

## <a name="filter-events"></a>Filtrování událostí

Odběry událostí služby IoT Hub můžete filtrovat události podle typu zařízení a název události. Na základě předmět filtry v mřížce událostí pracovní **předponu** a **příponu** odpovídá. Filtr používá `AND` operátor, takže doručí události se subjektem, které odpovídají předponu i příponu do odběratele. 

Předmět události IoT používá formát:

```json
devices/{deviceId}
```

### <a name="tips-for-consuming-events"></a>Tipy pro použití události

Aplikace, které zpracování událostí služby IoT Hub postupujte podle těchto navrhované postupy:

* Více předplatných může být nakonfigurován pro trasy události stejné obslužné rutiny události, proto je důležité, abyste předpokládají, že události jsou z určitého zdroje. Zpráva tématu, které chcete zajistit, že pocházejí ze služby IoT hub, které očekáváte, že si vždycky prohlédněte. 
* Nepředpokládejte, že jsou všechny události, které obdržíte typy, které očekáváte. Typ události si vždycky prohlédněte před zpracováním zprávy.
* Mimo provoz nebo po prodlevě můžete doručování zpráv. Pole značky etag slouží k pochopení, pokud je vaše informace o objektech aktuální.



## <a name="next-steps"></a>Další postup

* [Vyzkoušejte si kurz událostí služby IoT Hub](../event-grid/publish-iot-hub-events-to-logic-apps.md)
* [Další informace o události mřížky][lnk-eg-overview]
* [Porovnání rozdíly mezi směrování událostí služby IoT Hub a zprávy][lnk-eg-compare]

<!-- Links -->
[lnk-eg-overview]: ../event-grid/overview.md
[lnk-eg-compare]: iot-hub-event-grid-routing-comparison.md