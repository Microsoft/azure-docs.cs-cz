---
title: IoT Hub a Event Grid pro Azure | Microsoft Docs
description: Pomocí Azure Event Grid můžete spouštět procesy na základě akcí, ke kterým dochází v IoT Hub.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: a357e403aba64a5d05e359bf1186b01f73146758
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934104"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Reakce na události IoT Hub pomocí Event Grid k aktivaci akcí

Služba Azure IoT Hub se integruje s Azure Event Grid, takže můžete posílat oznámení o událostech ostatním službám a aktivovat navazující procesy. Nakonfigurujte obchodní aplikace tak, aby naslouchaly událostem IoT Hub, abyste mohli reagovat na kritické události spolehlivým, škálovatelným a zabezpečeným způsobem. Například sestavíte aplikaci, která aktualizuje databázi, vytvoří pracovní lístek a doručí e-mailové oznámení pokaždé, když se do služby IoT Hub zaregistruje nové zařízení IoT.

[Azure Event Grid](../event-grid/overview.md) je plně spravovaná služba Směrování událostí, která používá model publikování a odběru. Event Grid má integrovanou podporu pro služby Azure, jako je [Azure Functions](../azure-functions/functions-overview.md) a [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md), a může doručovat výstrahy událostí na služby mimo Azure pomocí webhooků. Úplný seznam obslužných rutin událostí, které Event Grid podporuje, najdete v [úvodu k Azure Event Grid](../event-grid/overview.md).

![Architektura Azure Event Grid](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Regionální dostupnost

Integrace Event Grid je dostupná pro centra IoT umístěná v oblastech, kde je podpora Event Grid podporovaná. Všechny události zařízení s výjimkou událostí telemetrie zařízení jsou všeobecně dostupné. Událost telemetrie zařízení je ve verzi Public Preview a je dostupná ve všech oblastech kromě Východní USA, Západní USA, Západní Evropa, [Azure Government](/azure/azure-government/documentation-government-welcome), [Azure Čína 21Vianet](/azure/china)a [Azure Německo](https://azure.microsoft.com/global-infrastructure/germany/). Nejnovější seznam oblastí najdete v [úvodu k Azure Event Grid](../event-grid/overview.md).

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

Zpráva telemetrie zařízení musí být v platném formátu JSON se sadou contentType nastavenou na JSON a contentEncoding nastavenou na UTF-8 ve [vlastnostech systému](iot-hub-devguide-routing-query-syntax.md#system-properties)zpráv. Pokud tato akce není nastavena, IoT Hub zapíše zprávy ve formátu kódování Base 64.

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

Event Grid umožňuje [filtrování](../event-grid/event-filtering.md) typů událostí, předmětů a obsahu dat. Při vytváření předplatného Event Grid můžete zvolit přihlášení k odběru vybraných událostí IoT. Filtry předmětu v Event Grid fungují na základě **začátku** (prefix) a **končí** na (přípona) shod. Filtr používá `AND` operátor, takže události s subjektem, které odpovídají předponě a příponám, budou doručeny do odběratele.

Předmět událostí IoT používá formát:

```json
devices/{deviceId}
```

Event Grid také umožňuje filtrování atributů každé události, včetně obsahu dat. To vám umožní vybrat, které události budou doručeny do obsahu na základě zprávy telemetrie. Příklady zobrazení najdete v tématu [Rozšířené filtrování](../event-grid/event-filtering.md#advanced-filtering) . Pro filtrování těla zprávy telemetrie musíte nastavit contentType na JSON a contentEncoding na UTF-8 ve [vlastnostech systému](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)zpráv.

Pro události netelemetrie, jako jsou DeviceConnected, DeviceDisconnected, DeviceCreated a DeviceDeleted, je možné při vytváření předplatného použít filtrování Event Grid. Pro události telemetrie se kromě filtrování v Event Grid můžou uživatelé také filtrovat na základě vlákna zařízení, vlastností a textu zprávy pomocí dotazu směrování zpráv. Vytvoříme výchozí [trasu](iot-hub-devguide-messages-d2c.md) v IoT Hub na základě vašeho Event Grid předplatného pro telemetrii zařízení. Tato jediná trasa dokáže zvládnout všechna Vaše předplatná Event Grid. Chcete-li filtrovat zprávy před odesláním dat telemetrie, můžete aktualizovat [dotaz směrování](iot-hub-devguide-routing-query-syntax.md). Všimněte si, že dotaz směrování lze použít na tělo zprávy pouze v případě, že tělo je JSON. Ve [vlastnostech systému](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)zpráv musíte také nastavit CONTENTTYPE na JSON a CONTENTENCODING na UTF-8.

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Omezení pro události připojené k zařízení a odpojené zařízení

Pokud chcete přijímat události připojené k zařízení a odpojené zařízení, musíte pro své zařízení otevřít odkaz D2C nebo C2D. Pokud zařízení používá protokol MQTT, IoT Hub zůstane otevřený odkaz C2D. Pro AMQP můžete otevřít odkaz C2D voláním metody [Receive ASYNC API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet).

Odkaz D2C je otevřený, Pokud posíláte telemetrii. Pokud je připojení zařízení blikáním, což znamená, že zařízení se často připojuje a odpojuje, nebudeme odesílat každý stav jednoho připojení, ale bude publikovat stav připojení, pro který se snímek vybere každou minutu. V případě výpadku IoT Hub zveřejníme stav připojení zařízení, jakmile se výpadek zastaví. Pokud se zařízení během tohoto výpadku odpojí, bude událost odpojená zařízení publikována během 10 minut.

## <a name="tips-for-consuming-events"></a>Tipy pro náročné události

Aplikace, které zpracovávají IoT Hub události, by měly postupovat podle těchto doporučených postupů:

* Více předplatných lze nakonfigurovat pro směrování událostí do stejné obslužné rutiny události, proto Nepředpokládáme, že události jsou z konkrétního zdroje. Vždy zkontrolujte téma zprávy a ujistěte se, že pochází ze služby IoT Hub, kterou očekáváte.

* Nepředpokládáme, že všechny události, které obdržíte, jsou typy, které očekáváte. Před zpracováním zprávy vždy kontrolujte typ eventType.

* Zprávy mohou být doručeny mimo pořadí nebo po zpoždění. Pomocí pole ETag můžete zjistit, jestli jsou informace o objektech pro události vytvořené nebo odstraněné zařízení aktuální.

## <a name="next-steps"></a>Další postup

* [Vyzkoušejte kurz IoT Hub události](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [Informace o uspořádání událostí připojení a odpojení zařízení](iot-hub-how-to-order-connection-state-events.md)

* [Další informace o Event Grid](../event-grid/overview.md)

* [Porovnání rozdílů mezi událostmi a zprávami IoT Hub směrování](iot-hub-event-grid-routing-comparison.md)
