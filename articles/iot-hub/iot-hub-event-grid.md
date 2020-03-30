---
title: Azure IoT Hub a grid událostí | Dokumenty společnosti Microsoft
description: Azure Event Grid slouží k aktivaci procesů na základě akcí, ke kterým dochází v centru IoT Hub.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.openlocfilehash: a1fd99ee595c4ae91ccd06aa41fa421ca8fcc074
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284548"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Reakce na události služby IoT Hub pomocí služby Event Grid ke spuštění akcí

Azure IoT Hub se integruje s Azure Event Grid, takže můžete odesílat oznámení událostí do jiných služeb a aktivovat navazující procesy. Nakonfigurujte své obchodní aplikace tak, aby naslouchaly událostem služby IoT Hub, abyste mohli spolehlivě, škálovatelně a bezpečně reagovat na kritické události.Například vytvořte aplikaci, která aktualizuje databázi, vytvoří pracovní lístek a doručuje e-mailové oznámení pokaždé, když je nové zařízení IoT zaregistrováno do vašeho centra IoT.

[Azure Event Grid](../event-grid/overview.md) je plně spravovaná služba směrování událostí, která používá model publikování a odběru. Event Grid má integrovanou podporu pro služby Azure, jako jsou [Azure Functions](../azure-functions/functions-overview.md) a Azure [Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md), a může doručovat upozornění na události pro služby, které nejsou Azure, pomocí webhooků. Úplný seznam obslužných rutin událostí, které podporuje Event Grid, najdete [v tématu Úvod do Azure Event Grid](../event-grid/overview.md).

![Architektura Azure Event Grid](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Regionální dostupnost

Integrace event gridu je dostupná pro centra IoT, která se nacházejí v oblastech, kde je podporována event grid. Nejnovější seznam oblastí najdete [v tématu Úvod do Azure Event Grid](../event-grid/overview.md).

## <a name="event-types"></a>Typy událostí

IoT Hub publikuje následující typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.Devices.DeviceVytvořeno | Publikováno, když je zařízení registrované do centra IoT. |
| Microsoft.Devices.DeviceOdstraněno | Publikováno při odstranění zařízení z centra IoT. |
| Microsoft.Devices.DevicePřipojeno | Publikováno, když je zařízení připojené k centru IoT hub. |
| Microsoft.Devices.DeviceOdpojeno | Publikováno, když je zařízení odpojeno od centra IoT hub. |
| Telemetrie Microsoft.Devices.DeviceTelemetrie | Publikováno při odeslání telemetrické zprávy zařízení do služby IoT hub |

Pomocí portálu Azure nebo rozhraní příkazového příkazového příkazu Azure nakonfigurujte, které události se mají publikovat z každého centra IoT. Například zkuste kurz [Odesílání e-mailových oznámení o událostech služby Azure IoT Hub pomocí logic apps](../event-grid/publish-iot-hub-events-to-logic-apps.md).

## <a name="event-schema"></a>Schéma událostí

Události ioT hubu obsahují všechny informace, které potřebujete k reakci na změny životního cyklu zařízení. Událost služby IoT Hub můžete identifikovat tak, že zkontrolujete, zda vlastnost eventType začíná na **Microsoft.Devices**. Další informace o použití vlastností události Event Grid naleznete ve [schématu událostí Event Grid](../event-grid/event-schema.md).

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

Zpráva o telemetrii zařízení musí být v platném formátu JSON s contentType nastaveným na **application/json** a contentEncoding nastaveným na **UTF-8** ve [vlastnostech systému](iot-hub-devguide-routing-query-syntax.md#system-properties)zpráv . Obě tyto vlastnosti jsou malá a velká písmena. Pokud kódování obsahu není nastaveno, ioT Hub zapíše zprávy v základním formátu 64 kódované.

Telemetrické události zařízení můžete obohatit před jejich publikováním do mřížky událostí výběrem koncového bodu jako mřížky událostí. Další informace naleznete v [tématu Přehled obohacení zpráv](iot-hub-message-enrichments-overview.md).

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

### <a name="device-created-schema"></a>Schéma vytvořené zařízením

Následující příklad ukazuje schéma události vytvořené zařízením:

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

Podrobný popis jednotlivých vlastností najdete v tématu [schéma událostí Služby Event Grid Azure pro službu IoT Hub](../event-grid/event-schema-iot-hub.md).

## <a name="filter-events"></a>Události filtru

Odběry událostí služby IoT Hub můžou filtrovat události na základě typu události, datového obsahu a předmětu, což je název zařízení.

Event Grid umožňuje [filtrování](../event-grid/event-filtering.md) typů událostí, předmětů a datového obsahu. Při vytváření předplatného Event Grid se můžete přihlásit k odběru vybraných událostí IoT. Filtry předmětu v mřížce událostí pracují na základě shod (předpona) a **Ends With** (přípona). **Begins With** Filtr používá `AND` operátor, takže události s předmětem, které odpovídají předponě a příponě jsou dodávány odběrateli.

Předmět událostí IoT používá formát:

```json
devices/{deviceId}
```

Event Grid také umožňuje filtrování atributů každé události, včetně datového obsahu. To umožňuje zvolit, jaké události jsou dodávány na základě obsahu zprávy telemetrie. Příklady naleznete [v rozšířeném filtrování.](../event-grid/event-filtering.md#advanced-filtering) Pro filtrování na tělo zprávy telemetrie, je nutné nastavit contentType **na application/json** a contentEncoding na **UTF-8** ve [vlastnostech systému](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)zpráv . Obě tyto vlastnosti jsou malá a velká písmena.

Pro události bez telemetrie, jako je DeviceConnected, DeviceDisconnected, DeviceCreated a DeviceDeleted, filtrování mřížky událostí lze použít při vytváření předplatného. Pro telemetrické události, kromě filtrování v Event Grid, uživatelé mohou také filtrovat na dvojčata zařízení, vlastnosti zprávy a text prostřednictvím dotazu směrování zpráv. 

Když se přihlásíte k odběru událostí telemetrie prostřednictvím služby Event Grid, služba IoT Hub vytvoří výchozí trasu zprávy pro odesílání zpráv zařízení typu zdroje dat do služby Event Grid. Další informace o směrování zpráv naleznete v tématu [Směrování zpráv služby IoT Hub](iot-hub-devguide-messages-d2c.md). Tato trasa bude viditelná na portálu v části > směrování zpráv služby IoT Hub. Pouze jedna trasa do Event Grid je vytvořen bez ohledu na počet odběrů EG vytvořené pro telemetrické události. Takže pokud potřebujete několik předplatných s různými filtry, můžete použít operátor OR v těchto dotazech na stejném postupu. Vytvoření a odstranění trasy je řízeno prostřednictvím předplatného událostí telemetrie prostřednictvím event gridu. Trasu do mřížky událostí nelze vytvořit ani odstranit pomocí směrování zpráv služby IoT Hub.

Chcete-li filtrovat zprávy před odesláním telemetrických dat, můžete aktualizovat [směrovací dotaz](iot-hub-devguide-routing-query-syntax.md). Všimněte si, že směrovací dotaz lze použít na text zprávy pouze v případě, že tělo je JSON. Je také nutné nastavit contentType na **application/json** a contentEncoding na **UTF-8** ve [vlastnostech systému](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)zpráv .

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Omezení událostí připojení a odpojení zařízení

Chcete-li přijímat události stavu připojení zařízení, musí zařízení provést buď "D2C Send Telemetry" nebo "C2D receive Message" operace s Iot Hub. Všimněte si však, že pokud zařízení používá protokol AMQP pro připojení k službu Iot Hub, doporučuje se, aby provést operaci "C2D receive Message" jinak jejich oznámení o stavu připojení může být zpožděno o několik minut. Pokud vaše zařízení používá protokol MQTT, služba IoT Hub ponechá připojení C2D otevřené. Pro AMQP můžete otevřít odkaz C2D voláním [receive asynchronní rozhraní API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet), pro IoT Hub C# SDK nebo [klienta zařízení pro AMQP](iot-hub-amqp-support.md#device-client).

Spojení D2C je otevřené, pokud odesíláte telemetrii. 

Pokud připojení zařízení bliká, což znamená, že se zařízení často připojuje a odpojuje, neodešleme každý stav připojení, ale zveřejníme aktuální stav připojení pořízený na pravidelném snímku, dokud blikání nebude pokračovat. Příjem buď stejné události stavu připojení s různými pořadovými čísly nebo různé události stavu připojení, což znamená, že došlo ke změně stavu připojení zařízení.

## <a name="tips-for-consuming-events"></a>Tipy pro náročné akce

Aplikace, které zpracovávají události centra IoT Hub, by se měly řídit následujícími doporučenými postupy:

* Více odběrů lze nakonfigurovat pro směrování událostí na stejnou obslužnou rutinu události, takže nepředpokládejte, že události pocházejí z určitého zdroje. Vždy zkontrolujte téma zprávy a ujistěte se, že pochází z centra IoT, které očekáváte.

* Nepředpokládejte, že všechny události, které obdržíte, jsou typy, které očekáváte. Před zpracováním zprávy vždy zkontrolujte eventType.

* Zprávy mohou být doručeny mimo pořadí nebo po zpoždění. Pomocí pole etag zjistěte, zda jsou informace o objektech aktuální pro události vytvořené zařízením nebo odstraněné zařízení.

## <a name="next-steps"></a>Další kroky

* [Vyzkoušejte kurz událostí ioT hubu](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [Informace o uspořádání událostí připojení a odpojení zařízení](iot-hub-how-to-order-connection-state-events.md)

* [Další informace o Mřížce událostí](../event-grid/overview.md)

* [Porovnání rozdílů mezi událostmi směrování služby IoT Hub a zprávami](iot-hub-event-grid-routing-comparison.md)

* [Zjistěte, jak pomocí událostí telemetrie IoT implementovat prostorovou analýzu IoT pomocí Azure Maps](../azure-maps/tutorial-iot-hub-maps.md#create-an-azure-function-and-add-an-event-grid-subscription)
