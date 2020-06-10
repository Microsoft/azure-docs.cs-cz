---
title: Interpretace dat událostí
titleSuffix: Azure Digital Twins
description: Podívejte se, jak interpretovat různé typy událostí a jejich různé zprávy s oznámením.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 4c95e686de23654688d0d7c3182c6565a907b750
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84612960"
---
# <a name="understand-event-data"></a>Pochopení dat událostí

Různé události v digitálních událostech Azure vytváří **oznámení**, což umožňuje, aby back-end řešení v průběhu různých akcí probíhají. Ty se pak směrují do různých umístění uvnitř i mimo digitální vlákna Azure, která můžou tyto informace [použít k provedení](concepts-route-events.md) akce.

Je možné vygenerovat několik typů oznámení a zprávy s oznámením mohou vypadat jinak, v závislosti na tom, jaký typ události byly vygenerovány. Tento článek obsahuje podrobné informace o různých typech zpráv a o tom, co můžou vypadat jako.

Tento graf znázorňuje různé typy oznámení:

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

Obecně platí, že oznámení se skládají ze dvou částí: hlavičky a textu. 

### <a name="event-notification-headers"></a>Hlavičky oznámení událostí

Hlavičky zpráv s oznámením jsou reprezentovány s páry klíč-hodnota. V závislosti na použitém protokolu (MQTT, AMQP nebo HTTP) budou záhlaví zpráv serializována jinak. Tato část popisuje obecné informace v hlavičce pro oznamovací zprávy bez ohledu na zvolené konkrétní protokoly a serializace.

Některá oznámení odpovídají standardu CloudEvents. Shoda CloudEvents je následující.
* Oznámení vysílaná ze zařízení budou dál dodržovat stávající specifikace oznámení.
* Oznámení zpracovaná a generovaná pomocí IoT Hub nadále postupovat podle stávajících specifikací pro oznámení, s výjimkou případů, kdy IoT Hub zvolit podporu CloudEvents, jako je například Event Grid
* Oznámení vysílaná z [digitálních vláken](concepts-twins-graph.md) s [modelem](concepts-models.md) vyhovujícím CloudEvents
* Oznámení zpracovaná a generovaná pomocí digitálních vláken Azure v souladu s CloudEvents

Služby musí přidat pořadové číslo pro všechna oznámení, aby označovaly jejich pořadí, nebo aby si zachovali vlastní řazení jiným způsobem. Oznámení vysílaná pomocí digitálních vláken Azure do Event Grid se naformátují do schématu Event Grid, dokud Event Grid pro vstup podporuje CloudEvents. Atributy rozšíření u hlaviček budou přidány jako vlastnosti Event Grid schématu v datové části. 

### <a name="event-notification-bodies"></a>Tělo oznámení události

Texty zpráv oznámení jsou popsány zde ve formátu JSON. Tělo zprávy může být serializováno jinak, v závislosti na serializaci požadované pro tělo zprávy (například s JSON, CBOR, Protobuf atd.).

Sada polí, které tělo obsahuje, se liší podle různých typů oznámení. Tady jsou dva vzorové tělo zprávy, které vám pomůžou získat představu o tom, co obvykle vypadá a co může zahrnovat.

Zpráva telemetrie:

```json
{ 
    "specversion": "1.0", 
    "type": "microsoft.iot.telemetry", 
    "source": "myhub.westus2.azuredigitaltwins.net", 
    "subject": "thermostat.vav-123", 
    "id": "c1b53246-19f2-40c6-bc9e-4666fa590d1a",
    "dataschema": "dtmi:com:contoso:DigitalTwins:VAV;1",
    "time": "2018-04-05T17:31:00Z", 
    "datacontenttype" : "application/json", 
    "data":  
      {
          "temp": 70,
          "humidity": 40 
      }
}
```

Zpráva s oznámením o životním cyklu:

```json
{ 
    "specversion": "1.0", 
    "type": "microsoft.digitaltwins.twin.create", 
    "source": "mydigitaltwins.westus2.azuredigitaltwins.net", 
    "subject": "device-123", 
    "id": "c1b53246-19f2-40c6-bc9e-4666fa590d1a", 
    "time": "2018-04-05T17:31:00Z", 
    "datacontenttype" : "application/json", 
    "dataschema": "dtmi:com:contoso:DigitalTwins:Device;1",           
    "data":  
      { 
        "$dtId": "room-123", 
        "property": "value",
        "$metadata": { 
                //...
        } 
      } 
}
```

## <a name="message-format-detail-for-different-event-types"></a>Podrobnosti formátu zprávy pro různé typy událostí

V této části najdete podrobnější informace o různých typech oznámení vysílaných IoT Hub a digitálních Vlákenách Azure (nebo jiných službách Azure IoT). Přečtěte si informace o akcích, které aktivují jednotlivé typy oznámení, a o sadě polí zahrnutých do každého typu textu oznámení.

### <a name="digital-twin-life-cycle-notifications"></a>Oznámení digitálního vlákna životního cyklu

Všechny [digitální vlákna](concepts-twins-graph.md) vydávají oznámení, bez ohledu na to, jestli představují [IoT Hub zařízení v digitálních](how-to-ingest-iot-hub-data.md) prostředcích Azure nebo ne. Důvodem je oznámení o **životním cyklu**, která se týkají digitálního vlákna.

Oznámení životního cyklu se aktivují v těchto případech:
* Vytvoří se digitální dvojitá vlákna.
* Digitální zdvojení se odstraní.

#### <a name="properties"></a>Vlastnosti

Tady jsou pole v těle oznámení o životním cyklu.

| Name | Hodnota |
| --- | --- |
| `id` | Identifikátor oznámení, jako je například identifikátor UUID nebo čítač, který služba spravuje. `source` + `id`je jedinečný pro každou událost DISTINCT. |
| `source` | Název instance služby IoT Hub nebo instance digitálního vlákna Azure, jako je například *myhub.Azure-Devices.NET* nebo *mydigitaltwins.westus2.azuredigitaltwins.NET* |
| `specversion` | 1.0 |
| `type` | `Microsoft.DigitalTwins.Twin.Create`<br>`Microsoft.DigitalTwins.Twin.Delete`<br>`Microsoft.DigitalTwins.TwinProxy.Create`<br>`Microsoft.DigitalTwins.TwinProxy.Delete`<br>`Microsoft.DigitalTwins.TwinProxy.Attach`<br>`Microsoft.DigitalTwins.TwinProxy.Detach` |
| `datacontenttype` | application/json |
| `subject` | ID digitálního vlákna |
| `time` | Časové razítko, kdy došlo k operaci na vlákna |
| `sequence` | Hodnota vyjadřující pozici události ve větší seřazené posloupnosti událostí. Služby musí přidat pořadové číslo pro všechna oznámení, aby označovaly jejich pořadí, nebo aby si zachovali vlastní řazení jiným způsobem. Pořadové číslo se zvýší s každou zprávou. Nastaví se na 1, pokud se objekt odstraní a znovu vytvoří se stejným ID. |
| `sequencetype` | Další podrobnosti o použití pole Sequence. Tato vlastnost například může určovat, že hodnota musí být podepsané 32 celé číslo, které začíná na 1 a v každém okamžiku se zvýší o 1. |

#### <a name="body-details"></a>Podrobnosti těla

Tělo je ovlivněné digitální, reprezentované ve formátu JSON. Toto schéma je *zdrojem digitálního vlákna 7,1*.

Pro události vytvoření datová část odráží stav vlákna po vytvoření prostředku, takže by měl obsahovat všechny vygenerované systémové prvky stejně jako `GET` volání.

Tady je příklad těla pro zařízení [IoT technologie Plug and Play (PnP)](../iot-pnp/overview-iot-plug-and-play.md) , komponenty a žádné vlastnosti nejvyšší úrovně. Vlastnosti, které nedávají smysl pro zařízení (například hlášené vlastnosti), by měly být vynechány.

```json
{
  "$dtId": "device-digitaltwin-01",
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

Tady je další příklad digitálního vlákna. Tento model je založen na [modelu](concepts-models.md)a nepodporuje komponenty:

```json
{
  "$dtId": "logical-digitaltwin-01",
  "avgTemperature": 70,
  "comfortIndex": 85,
  "$metadata": {
    "$model": "dtmi:com:contoso:Building;1",
    "$kind": "DigitalTwin",
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

### <a name="digital-twin-relationship-change-notifications"></a>Oznámení o změně vztahu digitálního vlákna

**Oznámení o změně vztahu** se aktivují, když se vytvoří, aktualizuje nebo odstraní jakákoli relace digitálního vlákna. 

#### <a name="properties"></a>Vlastnosti

Tady jsou pole v těle oznámení o změně hrany.

| Name    | Hodnota |
| --- | --- |
| `id` | Identifikátor oznámení, jako je například identifikátor UUID nebo čítač, který služba spravuje. `source` + `id`je jedinečný pro každou událost DISTINCT |
| `source` | Název instance digitálního vlákna Azure, jako je *mydigitaltwins.westus2.azuredigitaltwins.NET* |
| `specversion` | 1.0 |
| `type` | `Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br>`Microsoft.DigitalTwins.Relationship.Delete`<br>`datacontenttype    application/json for Relationship.Create`<br>`application/json-patch+json for Relationship.Update` |
| `subject` | ID vztahu, např.`<twinID>/relationships/<relationshipName>/<edgeID>` |
| `time` | Časové razítko při výskytu operace u vztahu |
| `sequence` | Hodnota vyjadřující pozici události ve větší seřazené posloupnosti událostí. Služby musí přidat pořadové číslo pro všechna oznámení, aby označovaly jejich pořadí, nebo aby si zachovali vlastní řazení jiným způsobem. Pořadové číslo se zvýší s každou zprávou. Nastaví se na 1, pokud se objekt odstraní a znovu vytvoří se stejným ID. |
| `sequencetype` | Další podrobnosti o použití pole Sequence. Tato vlastnost například může určovat, že hodnota musí být podepsané 32 celé číslo, které začíná na 1 a v každém okamžiku se zvýší o 1. |

#### <a name="body-details"></a>Podrobnosti těla

Tělo je datová část relace, také ve formátu JSON. Používá stejný formát jako `GET` požadavek pro relaci prostřednictvím [rozhraní DigitalTwins API](how-to-use-apis-sdks.md). 

"Aktualizace vztahu" znamená, že došlo ke změně vlastností vztahu. 

Tady je příklad oznámení relace aktualizace pro aktualizaci vlastnosti:

```json
[
  {
    "op": "replace",
    "path": "ownershipUser",
    "value": "user3"
  }
]
```

`Relationship.Delete`V případě je text totožný s `GET` požadavkem a získá nejnovější stav před odstraněním.

Tady je příklad upozornění na vytvoření nebo odstranění vztahu:

```json
{
    "$relationshipId": "EdgeId1",
    "$sourceId": "building11",
    "$relationshipName": "Contains",
    "$targetId": "floor11",
    "ownershipUser": "user1",
    "ownershipDepartment": "Operations"
}
```

### <a name="digital-twin-model-change-notifications"></a>Upozornění na změnu digitálního typu s dvojitým modelem

**Oznámení o změně modelu** se aktivují, když se nahraje, znovu načte, opraví, vyřadí nebo odstraní [model](concepts-models.md) DTDL (Digital Definition Language).

#### <a name="properties"></a>Vlastnosti

Tady jsou pole v těle oznámení o změně modelu.

| Name    | Hodnota |
| --- | --- |
| `id` | Identifikátor oznámení, jako je například identifikátor UUID nebo čítač, který služba spravuje. `source` + `id`je jedinečný pro každou událost DISTINCT |
| `source` | Název instance služby IoT Hub nebo instance digitálního vlákna Azure, jako je například *myhub.Azure-Devices.NET* nebo *mydigitaltwins.westus2.azuredigitaltwins.NET* |
| `specversion` | 1.0 |
| `type` | `Microsoft.DigitalTwins.Model.Upload`<br>`Microsoft.DigitalTwins.Model.Reload`(Specifické pro centra)<br>`Microsoft.DigitalTwins.Model.Patch`(Specifické pro centra)<br>`Microsoft.DigitalTwins.Model.Decom`<br>`Microsoft.DigitalTwins.Model.Delete` |
| `datacontenttype` | application/json |
| `subject` | ID modelu ve formuláři`dtmi:<domain>:<unique model identifier>;<model version number>` |
| `time` | Časové razítko při výskytu operace v modelu |
| `sequence` | Hodnota vyjadřující pozici události ve větší seřazené posloupnosti událostí. Služby musí přidat pořadové číslo pro všechna oznámení, aby označovaly jejich pořadí, nebo aby si zachovali vlastní řazení jiným způsobem. Pořadové číslo se zvýší s každou zprávou. Nastaví se na 1, pokud se objekt odstraní a znovu vytvoří se stejným ID. |
| `sequencetype` | Další podrobnosti o použití pole Sequence. Tato vlastnost například může určovat, že hodnota musí být podepsané 32 celé číslo, které začíná na 1 a v každém okamžiku se zvýší o 1. |
| `modelstatus` | Stav řešení pro řešení modelu. Možné hodnoty: úspěch/NotFound/neúspěch (jenom IoT Hub) | 
| `updatereason` | Aktualizuje důvod modelu ve schématu. Možné hodnoty: vytvořit/obnovit/přepsat (pouze IoT Hub) | 

#### <a name="body-details"></a>Podrobnosti těla

Pro akce nahrávání, opětovného načtení a opravy modelů není k dispozici žádný text zprávy. Uživatel musí provést `GET` volání pro získání obsahu modelu. 

V případě a bude `Model.Decom` tělo opravy ve formátu JSON patch, stejně jako všechna ostatní rozhraní patch API na povrchu rozhraní API digitálních vláken Azure. Pokud tedy chcete vyřadit z provozu model z provozu, použijte:

```json
[
  {
    "op": "replace",
    "path": "/decommissionedState",
    "value": true
  }
]
```

V případě je `Model.Delete` Text žádosti stejný jako `GET` požadavek a získá nejnovější stav před odstraněním.

### <a name="digital-twin-change-notifications"></a>Oznámení o změně digitálního vlákna

Při aktualizaci digitálního vlákna se aktivují **oznámení o změně digitálního vlákna** , třeba:
* Když se změní hodnoty vlastnosti nebo metadata.
* Při změně digitálního vlákna nebo metadat komponenty. Příkladem tohoto scénáře je změna modelu digitálního vlákna.

#### <a name="properties"></a>Vlastnosti

Tady jsou pole v těle oznámení o změně digitálního vlákna.

| Name    | Hodnota |
| --- | --- |
| `id` | Identifikátor oznámení, jako je například identifikátor UUID nebo čítač, který služba spravuje. `source` + `id`je jedinečný pro každou událost DISTINCT |
| `source` | Název instance služby IoT Hub nebo instance digitálního vlákna Azure, jako je například *myhub.Azure-Devices.NET* nebo *mydigitaltwins.westus2.azuredigitaltwins.NET*
| `specversion` | 1.0 |
| `type` | `Microsoft.DigitalTwins.Twin.Update` |
| `datacontenttype` | Application/JSON – patch + JSON |
| `subject` | ID digitálního vlákna |
| `time` | Časové razítko při výskytu operace u digitálního vlákna |
| `sequence` | Hodnota vyjadřující pozici události ve větší seřazené posloupnosti událostí. Služby musí přidat pořadové číslo pro všechna oznámení, aby označovaly jejich pořadí, nebo aby si zachovali vlastní řazení jiným způsobem. Pořadové číslo se zvýší s každou zprávou. Nastaví se na 1, pokud se objekt odstraní a znovu vytvoří se stejným ID. |
| `sequencetype` | Další podrobnosti o použití pole Sequence. Tato vlastnost například může určovat, že hodnota musí být podepsané 32 celé číslo, které začíná na 1 a v každém okamžiku se zvýší o 1. |

#### <a name="body-details"></a>Podrobnosti těla

Tělo `Twin.Update` oznámení je dokument opravy JSON obsahující aktualizaci digitálního vlákna.

Řekněme například, že digitální vlákna bylo aktualizováno pomocí následující opravy.

```json
[
  {
    "op": "replace",
    "path": "/mycomp/prop1",
    "value": {"a":3}
  }
]
```

Příslušné oznámení (Pokud synchronně vykonává služba, například digitální vlákna Azure, které aktualizují digitální vlákna) by mělo tělo, jako je:

```json
[
    { "op": "replace", "path": "/myComp/prop1", "value": {"a": 3}},
    { "op": "replace", "path": "/myComp/$metadata/prop1",
        "value": {
            "desiredValue": { "a": 3 },
            "desiredVersion": 2,
                "ackCode": 200,
            "ackVersion": 2 
        }
    }
]
```

## <a name="next-steps"></a>Další kroky

Přečtěte si téma Vytvoření koncových bodů a tras pro doručování událostí:
* [Postupy: Správa koncových bodů a tras](how-to-manage-routes.md)

Nebo si přečtěte další informace o rozhraních API a možnostech sady SDK pro digitální vlákna Azure:
* [Postupy: použití rozhraní API a sad SDK pro digitální vlákna Azure](how-to-use-apis-sdks.md)