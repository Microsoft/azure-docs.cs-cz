---
title: Oznámení událostí
titleSuffix: Azure Digital Twins
description: Podívejte se, jak interpretovat různé typy událostí a jejich různé zprávy s oznámením.
author: baanders
ms.author: baanders
ms.date: 4/8/2021
ms.topic: conceptual
ms.service: digital-twins
ms.custom: contperf-fy21q4
ms.openlocfilehash: 42842b00120b7e918ca5b790cce92a74ab1b99d5
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259954"
---
# <a name="event-notifications"></a>Oznámení událostí

Různé události v digitálních událostech Azure vytváří **oznámení**, což umožňuje, aby back-end řešení v průběhu různých akcí probíhají. Ty se pak směrují do různých umístění uvnitř i mimo digitální vlákna Azure, která můžou tyto informace [použít k provedení](concepts-route-events.md) akce.

Je možné vygenerovat několik typů oznámení a zprávy s oznámením mohou vypadat jinak, v závislosti na tom, jaký typ události byly vygenerovány. Tento článek obsahuje podrobné informace o různých typech zpráv a o tom, co můžou vypadat jako.

Tento graf znázorňuje různé typy oznámení:

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

## <a name="notification-structure"></a>Struktura oznámení

Obecně platí, že oznámení se skládají ze dvou částí: hlavičky a textu. 

### <a name="event-notification-headers"></a>Hlavičky oznámení událostí

Hlavičky zpráv s oznámením jsou reprezentovány s páry klíč-hodnota. V závislosti na použitém protokolu (MQTT, AMQP nebo HTTP) budou záhlaví zpráv serializována jinak. Tato část popisuje obecné informace v hlavičce pro oznamovací zprávy bez ohledu na zvolené konkrétní protokoly a serializace.

Některá oznámení odpovídají standardu [CloudEvents](https://cloudevents.io/) . Shoda CloudEvents je následující.
* Oznámení vysílaná ze zařízení budou dál dodržovat stávající specifikace oznámení.
* Oznámení zpracovaná a generovaná pomocí IoT Hub nadále postupovat podle stávajících specifikací pro oznámení, s výjimkou případů, kdy IoT Hub zvolit podporu CloudEvents, jako je například Event Grid
* Oznámení vysílaná z [digitálních vláken](concepts-twins-graph.md) s [modelem](concepts-models.md) vyhovujícím CloudEvents
* Oznámení zpracovaná a generovaná pomocí digitálních vláken Azure v souladu s CloudEvents

Služby musí přidat pořadové číslo pro všechna oznámení, aby označovaly jejich pořadí, nebo aby si zachovali vlastní řazení jiným způsobem. 

Oznámení vygenerovaná pomocí digitálních vláken Azure do Event Grid se automaticky naformátují buď na schéma CloudEvents, nebo na schéma EventGridEvent, a to v závislosti na typu schématu definovaném v tématu Event Grid. 

Atributy rozšíření u hlaviček budou přidány jako vlastnosti Event Grid schématu v datové části. 

### <a name="event-notification-bodies"></a>Tělo oznámení události

Texty zpráv oznámení jsou popsány zde ve formátu JSON. Tělo zprávy může být serializováno jinak, v závislosti na serializaci požadované pro tělo zprávy (například s JSON, CBOR, Protobuf atd.).

Sada polí, které tělo obsahuje, se liší podle různých typů oznámení.

V následujících částech najdete další podrobnosti o různých typech oznámení vysílaných IoT Hub a digitálních Vlákenách Azure (nebo jiných službách Azure IoT). Přečtěte si informace o akcích, které aktivují jednotlivé typy oznámení, a o sadě polí zahrnutých do každého typu textu oznámení.

## <a name="digital-twin-change-notifications"></a>Oznámení o změně digitálního vlákna

Při aktualizaci digitálního vlákna se aktivují **oznámení o změně digitálního vlákna** , třeba:
* Když se změní hodnoty vlastnosti nebo metadata.
* Při změně digitálního vlákna nebo metadat komponenty. Příkladem tohoto scénáře je změna modelu digitálního vlákna.

### <a name="properties"></a>Vlastnosti

Tady jsou pole v těle oznámení o změně digitálního vlákna.

| Name    | Hodnota |
| --- | --- |
| `id` | Identifikátor oznámení, jako je například identifikátor UUID nebo čítač, který služba spravuje. `source` + `id` je jedinečný pro každou událost DISTINCT |
| `source` | Název instance služby IoT Hub nebo instance digitálního vlákna Azure, jako je například *myhub.Azure-Devices.NET* nebo *mydigitaltwins.westus2.azuredigitaltwins.NET* |
| `data` | Dokument opravy JSON popisující aktualizaci provedenou na vlákna. Podrobnosti najdete níže v části [Podrobnosti o těle](#body-details) . |
| `specversion` | *1.0*<br>Zpráva odpovídá této verzi [specifikace CloudEvents](https://github.com/cloudevents/spec). |
| `type` | `Microsoft.DigitalTwins.Twin.Update` |
| `datacontenttype` | `application/json` |
| `subject` | ID digitálního vlákna |
| `time` | Časové razítko při výskytu operace u digitálního vlákna |
| `traceparent` | Kontext trasování W3C pro událost |

### <a name="body-details"></a>Podrobnosti těla

V rámci zprávy `data` obsahuje pole dokument opravy JSON obsahující aktualizaci digitálního vlákna.

Řekněme například, že digitální vlákna bylo aktualizováno pomocí následující opravy.

:::code language="json" source="~/digital-twins-docs-samples/models/patch-component-2.json":::

Data v odpovídajících oznámeních (Pokud synchronně prováděná službou, jako je například digitální vlákna Azure s aktualizací digitálního vlákna), by měla tělo jako:

```json
{
    "modelId": "dtmi:example:com:floor4;2",
    "patch": [
      {
        "value": 40,
        "path": "/Temperature",
        "op": "replace"
      },
      {
        "value": 30,
        "path": "/comp1/prop1",
        "op": "add"
      }
    ]
  }
```

Toto jsou informace, které se nacházejí v `data` poli zprávy s oznámením o životním cyklu.

## <a name="digital-twin-lifecycle-notifications"></a>Oznámení o digitálních cyklech digitálního vlákna

Všechny [digitální vlákna](concepts-twins-graph.md) vydávají oznámení, bez ohledu na to, jestli představují [IoT Hub zařízení v digitálních](how-to-ingest-iot-hub-data.md) prostředcích Azure nebo ne. Důvodem je oznámení o **životním cyklu**, která se týkají digitálního vlákna.

Oznámení o životním cyklu se aktivují v těchto případech:
* Vytvoří se digitální dvojitá vlákna.
* Digitální zdvojení se odstraní.

### <a name="properties"></a>Vlastnosti

Tady jsou pole v těle oznámení o životním cyklu.

| Name | Hodnota |
| --- | --- |
| `id` | Identifikátor oznámení, jako je například identifikátor UUID nebo čítač, který služba spravuje. `source` + `id` je jedinečný pro každou událost DISTINCT. |
| `source` | Název instance služby IoT Hub nebo instance digitálního vlákna Azure, jako je například *myhub.Azure-Devices.NET* nebo *mydigitaltwins.westus2.azuredigitaltwins.NET* |
| `data` | Data vlákna, u kterých došlo k události životního cyklu. Podrobnosti najdete níže v části [Podrobnosti o těle](#body-details-1) . |
| `specversion` | *1.0*<br>Zpráva odpovídá této verzi [specifikace CloudEvents](https://github.com/cloudevents/spec). |
| `type` | `Microsoft.DigitalTwins.Twin.Create`<br>`Microsoft.DigitalTwins.Twin.Delete` |
| `datacontenttype` | `application/json` |
| `subject` | ID digitálního vlákna |
| `time` | Časové razítko, kdy došlo k operaci na vlákna |
| `traceparent` | Kontext trasování W3C pro událost |

### <a name="body-details"></a>Podrobnosti těla

Tady je příklad zprávy s oznámením o životním cyklu: 

```json
{
  "specversion": "1.0",
  "id": "d047e992-dddc-4a5a-b0af-fa79832235f8",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net",
  "data": {
    "$dtId": "floor1",
    "$etag": "W/\"e398dbf4-8214-4483-9d52-880b61e491ec\"",
    "$metadata": {
      "$model": "dtmi:example:Floor;1"
    }
  },
  "subject": "floor1",
  "time": "2020-06-23T19:03:48.9700792Z",
  "datacontenttype": "application/json",
  "traceparent": "00-18f4e34b3e4a784aadf5913917537e7d-691a71e0a220d642-01"
}
```

Uvnitř zprávy `data` pole obsahuje data ovlivněného digitálního vlákna reprezentovaného ve formátu JSON. Toto schéma je *zdrojem digitálního vlákna 7,1*.

Pro události vytvoření `data` datová část odráží stav vlákna po vytvoření prostředku, takže by měl obsahovat všechny vygenerované systémové prvky stejně jako `GET` volání.

Tady je příklad dat pro zařízení [IoT technologie Plug and Play (PnP)](../iot-pnp/overview-iot-plug-and-play.md) , komponenty a žádné vlastnosti nejvyšší úrovně. Vlastnosti, které nedávají smysl pro zařízení (například hlášené vlastnosti), by měly být vynechány. Toto jsou informace, které se nacházejí v `data` poli zprávy s oznámením o životním cyklu.

```json
{
  "$dtId": "device-digitaltwin-01",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "thermostat": {
    "temperature": 80,
    "humidity": 45,
    "$metadata": {
      "$model": "dtmi:com:contoso:Thermostat;1",
      "temperature": {
        "desiredValue": 85,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      },
      "humidity": {
        "desiredValue": 40,
        "desiredVersion": 1,
        "ackVersion": 1,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Thermostat_X500;1",
  }
}
```

Tady je další příklad digitálních dvojitých dat. Tento model je založen na [modelu](concepts-models.md)a nepodporuje komponenty:

```json
{
  "$dtId": "logical-digitaltwin-01",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "avgTemperature": 70,
  "comfortIndex": 85,
  "$metadata": {
    "$model": "dtmi:com:contoso:Building;1",
    "avgTemperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "comfortIndex": {
      "desiredValue": 90,
      "desiredVersion": 1,
      "ackVersion": 3,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

## <a name="digital-twin-relationship-change-notifications"></a>Oznámení o změně vztahu digitálního vlákna

**Oznámení o změně vztahu** se aktivují, když se vytvoří, aktualizuje nebo odstraní jakákoli relace digitálního vlákna. 

### <a name="properties"></a>Vlastnosti

Tady jsou pole v těle oznámení o změně vztahu.

| Name    | Hodnota |
| --- | --- |
| `id` | Identifikátor oznámení, jako je například identifikátor UUID nebo čítač, který služba spravuje. `source` + `id` je jedinečný pro každou událost DISTINCT |
| `source` | Název instance digitálního vlákna Azure, jako je *mydigitaltwins.westus2.azuredigitaltwins.NET* |
| `data` | Datová část relace, která byla změněna. Podrobnosti najdete níže v části [Podrobnosti o těle](#body-details-2) . |
| `specversion` | *1.0*<br>Zpráva odpovídá této verzi [specifikace CloudEvents](https://github.com/cloudevents/spec). |
| `type` | `Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br>`Microsoft.DigitalTwins.Relationship.Delete` |
| `datacontenttype` | `application/json` |
| `subject` | ID vztahu, např. `<twinID>/relationships/<relationshipID>` |
| `time` | Časové razítko při výskytu operace u vztahu |
| `traceparent` | Kontext trasování W3C pro událost |

### <a name="body-details"></a>Podrobnosti těla

Uvnitř zprávy `data` obsahuje pole datovou část relace ve formátu JSON. Používá stejný formát jako `GET` požadavek pro relaci prostřednictvím [rozhraní DigitalTwins API](/rest/api/digital-twins/dataplane/twins). 

Tady je příklad dat pro oznámení o relaci aktualizace. "Aktualizace vztahu" znamená, že se změnily vlastnosti relace, takže data zobrazí aktualizovanou vlastnost a její novou hodnotu. Toto jsou informace, které se budou nacházet v `data` poli zprávy s oznámením o digitálním zdvojeném vztahu.

```json
{
    "modelId": "dtmi:example:Floor;1",
    "patch": [
      {
        "value": "user3",
        "path": "/ownershipUser",
        "op": "replace"
      }
    ]
  }
```

Tady je příklad dat pro upozornění na vytvoření nebo odstranění vztahu. `Relationship.Delete`V případě je text totožný s `GET` požadavkem a získá nejnovější stav před odstraněním.

```json
{
    "$relationshipId": "device_to_device",
    "$etag": "W/\"72479873-0083-41a8-83e2-caedb932d881\"",
    "$relationshipName": "Connected",
    "$targetId": "device2",
    "connectionType": "WIFI"
}
```

## <a name="digital-twin-telemetry-messages"></a>Zprávy s digitálním dvojitým telemetriem

**Zprávy telemetrie** jsou přijímány do digitálních vláken Azure z připojených zařízení, která shromažďují a odesílají měření.

### <a name="properties"></a>Vlastnosti

Tady jsou pole v těle zprávy telemetrie.

| Name    | Hodnota |
| --- | --- |
| `id` | Identifikátor oznámení, který poskytuje zákazník při volání rozhraní API telemetrie |
| `source` | Plně kvalifikovaný název vlákna, na které byla událost telemetrie odeslána Používá následující formát: `<yourDigitalTwinInstance>.api.<yourRegion>.digitaltwins.azure.net/<twinId>` . |
| `specversion` | *1.0*<br>Zpráva odpovídá této verzi [specifikace CloudEvents](https://github.com/cloudevents/spec). |
| `type` | `microsoft.iot.telemetry` |
| `data` | Zpráva telemetrie, která byla odeslána na vlákna. Datová část není upravena a nemusí se zarovnávat se schématem nevláken, která byla odeslána telemetrie. |
| `dataschema` | Schéma dat je ID modelu vlákna nebo komponenty, která generuje telemetrii. Například, `dtmi:example:com:floor4;2`. |
| `datacontenttype` | `application/json` |
| `traceparent` | Kontext trasování W3C pro událost. |

### <a name="body-details"></a>Podrobnosti těla

Tělo obsahuje měření telemetrie spolu s některými kontextové informace o zařízení.

Tady je příklad těla zprávy telemetrie: 

```json
{
  "specversion": "1.0",
  "id": "df5a5992-817b-4e8a-b12c-e0b18d4bf8fb",
  "type": "microsoft.iot.telemetry",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net/digitaltwins/room1",
  "data": {
    "Temperature": 10
  },
  "dataschema": "dtmi:example:com:floor4;2",
  "datacontenttype": "application/json",
  "traceparent": "00-7e3081c6d3edfb4eaf7d3244b2036baa-23d762f4d9f81741-01"
}
```

## <a name="next-steps"></a>Další kroky

Přečtěte si o doručování událostí do různých umístění pomocí koncových bodů a tras:
* [*Koncepty: trasy událostí*](concepts-route-events.md)
