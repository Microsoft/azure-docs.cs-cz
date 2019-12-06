---
title: IoT Hub a Event Grid pro Azure | Microsoft Docs
description: Pomocí Azure Event Grid můžete spouštět procesy na základě akcí, ke kterým dochází v IoT Hub.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.openlocfilehash: a1fd99ee595c4ae91ccd06aa41fa421ca8fcc074
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851696"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Reakce na události IoT Hub pomocí Event Grid k aktivaci akcí

Služba Azure IoT Hub se integruje s Azure Event Grid, takže můžete posílat oznámení o událostech ostatním službám a aktivovat navazující procesy. Nakonfigurujte obchodní aplikace tak, aby naslouchaly událostem IoT Hub, abyste mohli reagovat na kritické události spolehlivým, škálovatelným a zabezpečeným způsobem. Například sestavíte aplikaci, která aktualizuje databázi, vytvoří pracovní lístek a doručí e-mailové oznámení pokaždé, když se do služby IoT Hub zaregistruje nové zařízení IoT.

[Azure Event Grid](../event-grid/overview.md) je plně spravovaná služba Směrování událostí, která používá model publikování a odběru. Event Grid má integrovanou podporu pro služby Azure, jako je [Azure Functions](../azure-functions/functions-overview.md) a [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md), a může doručovat výstrahy událostí na služby mimo Azure pomocí webhooků. Úplný seznam obslužných rutin událostí, které Event Grid podporuje, najdete v [úvodu k Azure Event Grid](../event-grid/overview.md).

![Architektura Azure Event Grid](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Dostupnost podle oblastí

Integrace Event Grid je dostupná pro centra IoT umístěná v oblastech, kde je podpora Event Grid podporovaná. Nejnovější seznam oblastí najdete v [úvodu k Azure Event Grid](../event-grid/overview.md).

## <a name="event-types"></a>Typy událostí

IoT Hub zveřejňují následující typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Publikováno, když je zařízení zaregistrované do služby IoT Hub. |
| Microsoft.Devices.DeviceDeleted | Publikováno při odstranění zařízení ze služby IoT Hub. |
| Microsoft.Devices.DeviceConnected | Publikováno, když je zařízení připojené ke IoT Hub. |
| Microsoft. Devices. DeviceDisconnected | Publikováno, když se zařízení odpojí od služby IoT Hub. |
| Microsoft.Devices.DeviceTelemetry | Publikováno po odeslání zprávy telemetrie zařízení do služby IoT Hub |

Pomocí Azure Portal nebo rozhraní příkazového řádku Azure můžete nakonfigurovat, které události se mají publikovat z každého centra IoT Hub. Například zkuste kurz [Odeslat e-mailová oznámení o událostech Azure IoT Hub pomocí Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md).

## <a name="event-schema"></a>Schéma událostí

IoT Hub události obsahují všechny informace, které potřebujete k reakci na změny v životním cyklu zařízení. Událost IoT Hub můžete identifikovat tak, že zkontrolujete, že vlastnost eventType začíná na **Microsoft. Devices**. Další informace o tom, jak používat Event Grid vlastností události, najdete v tématu [Event Grid schématu událostí](../event-grid/event-schema.md).

### <a name="device-connected-schema"></a>Schéma připojené k zařízení

Následující příklad ukazuje schéma události připojené k zařízení:

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

### <a name="device-telemetry-schema"></a>Schéma telemetrie zařízení

Zpráva telemetrie zařízení musí být v platném formátu JSON se sadou contentType nastavenou na **Application/JSON** a contentEncoding nastavenou na **UTF-8** ve [vlastnostech systému](iot-hub-devguide-routing-query-syntax.md#system-properties)zpráv. U obou těchto vlastností se nerozlišují malá a velká písmena. Pokud není kódování obsahu nastaveno, IoT Hub zapíše zprávy ve formátu kódování Base 64.

Události telemetrie zařízení můžete rozšířit předtím, než se publikují do Event Grid tím, že vyberete koncový bod jako Event Grid. Další informace najdete v tématu [Přehled rozšíření zpráv](iot-hub-message-enrichments-overview.md).

Následující příklad ukazuje schéma události telemetrie zařízení:

```json
[{  
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {
      "body": {
          "Weather": {
              "Temperature": 900
            },
            "Location": "USA"
        },
        "properties": {
            "Status": "Active"
        },
        "systemProperties": {
          "iothub-content-type": "application/json",
          "iothub-content-encoding": "utf-8",
          "iothub-connection-device-id": "d1",
          "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
          "iothub-connection-auth-generation-id": "123455432199234570",
          "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
          "iothub-message-source": "Telemetry"
        }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="device-created-schema"></a>Schéma se vytvořilo v zařízení.

Následující příklad ukazuje schéma události vytvoření zařízení:

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

Podrobný popis jednotlivých vlastností najdete v tématu [Azure Event Grid schéma událostí pro IoT Hub](../event-grid/event-schema-iot-hub.md).

## <a name="filter-events"></a>Události filtru

Odběry událostí IoT Hub mohou filtrovat události na základě typu události, datového obsahu a předmětu, což je název zařízení.

Event Grid umožňuje [filtrování](../event-grid/event-filtering.md) typů událostí, předmětů a obsahu dat. Při vytváření předplatného Event Grid můžete zvolit přihlášení k odběru vybraných událostí IoT. Filtry předmětu v Event Grid fungují na základě **začátku** (prefix) a **končí** na (přípona) shod. Filtr používá operátor `AND`, takže události s subjektem, které odpovídají předponě a příponám, budou doručeny do odběratele.

Předmět událostí IoT používá formát:

```json
devices/{deviceId}
```

Event Grid také umožňuje filtrování atributů každé události, včetně obsahu dat. To vám umožní vybrat, které události budou doručeny do obsahu na základě zprávy telemetrie. Příklady zobrazení najdete v tématu [Rozšířené filtrování](../event-grid/event-filtering.md#advanced-filtering) . Pro filtrování těla zprávy telemetrie musíte nastavit contentType na **Application/JSON** a ContentEncoding na **UTF-8** ve [vlastnostech systému](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)zpráv. U obou těchto vlastností se nerozlišují malá a velká písmena.

Pro události netelemetrie, jako jsou DeviceConnected, DeviceDisconnected, DeviceCreated a DeviceDeleted, je možné při vytváření předplatného použít filtrování Event Grid. Pro události telemetrie se kromě filtrování v Event Grid můžou uživatelé také filtrovat na základě vlákna zařízení, vlastností a textu zprávy pomocí dotazu směrování zpráv. 

Když se přihlásíte k odběru událostí telemetrie prostřednictvím Event Grid, IoT Hub vytvoří výchozí trasu zpráv pro odesílání zpráv typu zařízení ze zdroje dat do Event Grid. Další informace o směrování zpráv najdete v tématu [IoT Hub směrování zpráv](iot-hub-devguide-messages-d2c.md). Tato trasa se zobrazí na portálu v části IoT Hub > směrování zpráv. Vytvoří se jenom jedna trasa pro Event Grid bez ohledu na počet takových předplatných vytvořených pro události telemetrie. Takže pokud potřebujete několik předplatných s různými filtry, můžete použít operátor OR v těchto dotazech na stejné trase. Vytvoření a odstranění trasy je řízeno prostřednictvím předplatného událostí telemetrie prostřednictvím Event Grid. Nemůžete vytvořit ani odstranit trasu pro Event Grid pomocí směrování zpráv IoT Hub.

Chcete-li filtrovat zprávy před odesláním dat telemetrie, můžete aktualizovat [dotaz směrování](iot-hub-devguide-routing-query-syntax.md). Všimněte si, že dotaz směrování lze použít na tělo zprávy pouze v případě, že tělo je JSON. Ve [vlastnostech systému](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)zpráv musíte také nastavit ContentType na **Application/JSON** a contentEncoding na **UTF-8** .

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Omezení pro události připojené k zařízení a odpojené zařízení

Aby bylo možné přijímat události stavu připojení zařízení, musí zařízení dělat v rámci IoT Hub buď operaci "d 2C Send telemetrie" nebo "C2D Receive Message". Upozorňujeme však, že pokud zařízení používá protokol AMQP pro připojení ke službě IoT Hub, doporučujeme, aby provedou operaci C2D Receive Message, jinak se oznámení o stavu připojení můžou zpozdit o několik minut. Pokud zařízení používá protokol MQTT, IoT Hub zůstane otevřený odkaz C2D. V případě AMQP můžete otevřít odkaz C2D voláním metody [Receive ASYNC API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet), pro IoT Hub C# SDK nebo [klienta zařízení pro AMQP](iot-hub-amqp-support.md#device-client).

Odkaz D2C je otevřený, Pokud posíláte telemetrii. 

Pokud je připojení zařízení blikáním, což znamená, že zařízení se často připojuje a odpojuje, nebudeme odesílat každý stav jednoho připojení, ale aktuální stav připojení se v pravidelném snímku publikuje, dokud se blikání nepokračuje. Když se přijme stejná událost stavu připojení s různými čísly sekvence nebo s různými událostmi stavu připojení, znamená to, že došlo ke změně stavu připojení zařízení.

## <a name="tips-for-consuming-events"></a>Tipy pro náročné události

Aplikace, které zpracovávají IoT Hub události, by měly postupovat podle těchto doporučených postupů:

* Více předplatných lze nakonfigurovat pro směrování událostí do stejné obslužné rutiny události, proto Nepředpokládáme, že události jsou z konkrétního zdroje. Vždy zkontrolujte téma zprávy a ujistěte se, že pochází ze služby IoT Hub, kterou očekáváte.

* Nepředpokládáme, že všechny události, které obdržíte, jsou typy, které očekáváte. Před zpracováním zprávy vždy kontrolujte typ eventType.

* Zprávy mohou být doručeny mimo pořadí nebo po zpoždění. Pomocí pole ETag můžete zjistit, jestli jsou informace o objektech pro události vytvořené nebo odstraněné zařízení aktuální.

## <a name="next-steps"></a>Další kroky

* [Vyzkoušejte kurz IoT Hub události](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [Informace o uspořádání událostí připojení a odpojení zařízení](iot-hub-how-to-order-connection-state-events.md)

* [Další informace o Event Grid](../event-grid/overview.md)

* [Porovnání rozdílů mezi událostmi a zprávami IoT Hub směrování](iot-hub-event-grid-routing-comparison.md)

* [Naučte se používat události telemetrie IoT k implementaci prostorové analýzy IoT pomocí Azure Maps](../azure-maps/tutorial-iot-hub-maps.md#create-an-azure-function-and-add-an-event-grid-subscription)
