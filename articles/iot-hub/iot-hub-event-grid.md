---
title: Azure IoT Hub a Event Grid | Dokumentace Microsoftu
description: Pomocí Azure Event Grid ke spouštění procesů na základě akcí, ke kterým dochází ve službě IoT Hub.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: f08845dbf4168627d0198e81d2092a1fe56c6c89
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2019
ms.locfileid: "66733875"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Reakce na události služby IoT Hub s využitím služby Event Grid pro aktivaci akcí

Azure IoT Hub se integruje s Azure Event Grid tak, aby mohla odesílat oznámení událostí k jiným službám a aktivovat podřízené procesy. Konfigurovat podnikové aplikace tak, aby naslouchala událostem IoT Hubu tak, aby mohly reagovat na kritické události spolehlivé, škálovatelné a zabezpečeným způsobem. Například sestavte aplikaci, která aktualizuje databázi, vytvoří lístek práce a poskytuje e-mailové oznámení pokaždé, když se nový IoT zařízení je zaregistrované do služby IoT hub.

[Azure Event Grid](../event-grid/overview.md) je plně spravovaná služba Směrování událostí, který používá publikování-odběru modelu. Event Grid obsahuje integrovanou podporu pro služby Azure, jako je [Azure Functions](../azure-functions/functions-overview.md) a [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md)a může poskytovat výstrahy na události na jiné než Azure services pomocí webhooků. Úplný seznam obslužných rutin událostí, které podporuje Služba Event Grid najdete v tématu [Úvod do služby Azure Event Grid](../event-grid/overview.md).

![Architektura služby Azure Event Grid](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Regionální dostupnost

Integrace služby Event Grid je k dispozici pro IoT hub umístěných v oblastech, kde je podporován služby Event Grid. Všechny události zařízení s výjimkou zařízení telemetrické události jsou obecně dostupné. Telemetrické události zařízení je ve verzi public preview a je k dispozici ve všech oblastech kromě USA – východ, USA – Západ, západní Evropa, [Azure Government](/azure/azure-government/documentation-government-welcome), [Azure China 21Vianet](/azure/china/china-welcome), a [Azure Germany](https://azure.microsoft.com/global-infrastructure/germany/). Nejnovější seznam oblastí najdete v tématu [Úvod do služby Azure Event Grid](../event-grid/overview.md).

## <a name="event-types"></a>Typy událostí

IoT Hub zveřejňuje následující typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Publikuje, když je zařízení zaregistrované do služby IoT hub. |
| Microsoft.Devices.DeviceDeleted | Publikuje, když zařízení se odstraní ze služby IoT hub. |
| Microsoft.Devices.DeviceConnected | Publikuje, když je zařízení připojené do služby IoT hub. |
| Microsoft.Devices.DeviceDisconnected | Publikuje, když zařízení je odpojen od služby IoT hub. |
| Microsoft.Devices.DeviceTelemetry | Publikování do služby IoT hub je odeslání telemetrické zprávy zařízení |

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

### <a name="device-telemetry-schema"></a>Schéma Telemetrie zařízení

Zařízení telemetrické zprávy musí být v platném formátu JSON s contentType nastavit do formátu JSON a nastavit na UTF-8 ve zprávě contentEncoding [vlastnosti systému](iot-hub-devguide-routing-query-syntax.md#system-properties). Pokud to není nastavena, pak služby IoT Hub bude zapisovat zprávy v zakódovaném formátu base 64.

Můžete obohatit zařízení telemetrických událostí před jejich publikováním do služby Event Grid tak, že vyberete koncový bod jako služby Event Grid. Další informace najdete v tématu [přehled obohacení zpráv](iot-hub-message-enrichments-overview.md).

Následující příklad ukazuje schématu událostí telemetrie zařízení:

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

Podrobný popis každé vlastnosti najdete v tématu [schématu událostí služby Azure Event Grid pro službu IoT Hub](../event-grid/event-schema-iot-hub.md).

## <a name="filter-events"></a>Události filtru

Odběry událostí služby IoT Hub můžete filtrovat události podle typu události, datového obsahu a předmětu, což je název zařízení.

Event Grid umožňuje [filtrování](../event-grid/event-filtering.md) na obsah typy, témata a data události. Při vytváření odběru Event gridu, můžete přihlásit k odběru vybraných událostí IoT. Na základě předmětu filtry ve službě Event Grid pracovní **začíná** (předpona) a **skončí s** odpovídá (přípona). Tento filtr používá `AND` operátoru, takže doručí události s předmětem, které odpovídají předpony a přípony do odběratele.

Předmět události IoT používá formát:

```json
devices/{deviceId}
```

Event Grid také umožňuje filtrování podle atributů událostí, včetně dat obsahu. To umožňuje zvolit, jaké události se doručují na základě obsahu telemetrické zprávy. Podrobnosti najdete na [rozšířené filtrování](../event-grid/event-filtering.md#advanced-filtering) Chcete-li zobrazit příklady.

Bez telemetrie událostí jako DeviceConnected DeviceDisconnected, DeviceCreated a DeviceDeleted služby Event Grid filtrování je možné při vytváření odběru. Telemetrické události, kromě filtrování ve službě Event Grid uživatelé mohou filtrovat také na dvojčata zařízení, vlastnosti zprávy a text pomocí dotazu směrování zpráv. Můžeme vytvořit výchozí [trasy](iot-hub-devguide-messages-d2c.md) ve službě IoT Hub podle předplatného služby Event Grid k telemetrii zařízení. Tato trasa jeden dokáže uchovat všechna vaše předplatná Event gridu. Pokud chcete filtrovat zprávy před odesláním telemetrická data, můžete aktualizovat vaše [směrování dotazů](iot-hub-devguide-routing-query-syntax.md). Mějte na paměti, že směrování dotazu lze použít u zprávy pouze v případě, že je text JSON.

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Omezení pro zařízení připojené a zařízení odpojí události

Zobrazí zařízení, připojení a odpojení zařízení události, je nutné otevřít odkaz D2C nebo C2D odkaz pro vaše zařízení. Pokud vaše zařízení používá protokol MQTT, Centrum IoT udržovat C2D otevřete odkaz. AMQP, můžete otevřít odkaz C2D voláním [přijímat asynchronní rozhraní API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet).

Odkaz D2C je otevřený, když odesíláte telemetrii. Pokud je blikání připojení zařízení, což znamená, že zařízení připojení a odpojení často, jsme nepošle každý stav jednoho připojení, ale zveřejní stav připojení, které se pořídí snímek každou minutu. I v případě výpadku služby IoT Hub publikujeme stav připojení zařízení ihned poté, co je výpadek. Pokud zařízení odpojí během tohoto výpadku, události odpojených zařízení bude publikován do 10 minut.

## <a name="tips-for-consuming-events"></a>Tipy pro používání událostí

Aplikace, které zpracovávají události služby IoT Hub postupujte podle těchto doporučené postupy:

* Několik předplatných lze nakonfigurovat pro směrování událostí na stejný ovladač událostí, takže Nepředpokládejte, že jsou události z určitého zdroje. Vždy zkontrolujte zpráv tématu k zajištění, že pocházejí ze služby IoT hub, které jste očekávali.

* Nepředpokládejte, že jsou všechny události, které se zobrazí typy, které jste očekávali. Vždy zkontrolujte typ události před zpracováním zprávy.

* Můžete doručování zpráv mimo pořadí nebo po prodlevě. Značka etag pole použijte k pochopení, pokud je vaše informace o objektech aktuální pro zařízení se vytvořilo nebo zařízení odstranit události.

## <a name="next-steps"></a>Další postup

* [Projděte si kurz událostí služby IoT Hub](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [Informace o uspořádání událostí připojení a odpojení zařízení](iot-hub-how-to-order-connection-state-events.md)

* [Další informace o službě Event Grid](../event-grid/overview.md)

* [Srovnání rozdílů mezi směrování událostí služby IoT Hub a zpráv](iot-hub-event-grid-routing-comparison.md)
